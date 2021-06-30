# 6 Spring Transaction

文章：[https://github.com/seaswalker/spring-analysis/blob/master/note/spring-transaction.md](https://github.com/seaswalker/spring-analysis/blob/master/note/spring-transaction.md)

[spring源码阅读--@Transactional实现原理](https://blog.csdn.net/qq_20597727/article/details/84868035)

在了解了Spring AOP后，再来看看Spring的事务。Spring的声明式事务利用的就是AOP，@Transactional就像是我们的切入点@Around，而@Before和@After就像是准备事务，提交事务。如有异常则进行回滚。在本章中，也会研究一下Spring中的事务隔离和事务传播，更多事务的内容会另起一章详细分析，例如分布式事务、MySQL中的加锁机制，不同隔离级别在事务中出现的问题以及解决方案。

## 编程式事务管理和声明式事务管理

文章： [Spring的编程式事务和声明式事务](https://www.cnblogs.com/nnngu/p/8627662.html)

Spring支持编程式事务管理和声明式事务管理两种方式

* **编程式事务**：使用TransactionTemplate或者直接使用底层的PlatformTransactionManager。对于编程式事务管理，spring推荐使用TransactionTemplate。
* **声明式事务**：是建立在AOP之上的。其本质是对方法前后进行拦截，然后在目标方法开始之前创建或者加入一个事务，在执行完目标方法之后根据执行情况提交或者回滚事务。声明式事务最大的优点就是不需要通过编程的方式管理事务，这样就不需要在业务逻辑代码中掺杂事务管理的代码，只需在配置文件中做相关的事务规则声明\(或通过**基于@Transactional注解的方式**\)，便可以将事务规则应用到业务逻辑中。

目前，在实际开发中，SpringBoot项目用到的大多数是声明式事务，通过注解的方式方便并且对代码侵入性低。在这里只是简单了解一下这两个概念，可能很多人用了很久都不知道什么叫编程式事务和声明式事务。

## 几个概念

文章：

[DataSource数据源简单理解](https://blog.csdn.net/qq_40910541/article/details/80771607)

[数据源\(DataSource\)是什么以及SpringBoot中数据源配置](https://blog.csdn.net/weixin_33935777/article/details/91445354?utm_medium=distribute.pc_relevant.none-task-blog-title-5&spm=1001.2101.3001.4242)

* PlatformTransactionManager：事务管理器 
* DataSource：数据源。（数据源是对数据库以及对数据库交互操作的抽象，它封装了目标源的位置信息，验证信息和建立与关闭连接的操作。数据源大致分为2种：**不提供连接池**和**提供连接池管理**）
* Connection：客户端和数据库的连接
* TransactionStatus：事务状态，用于手动设置回滚，查看事务状态，事务保存点savepoint

## TransactionInterceptor–最终事务管理者

从Spring AOP一章我们可以知道，当Spring回调方法时，是通过设置一个InvocationHandler处理\(拦截\)所有的方法。而Spring tx中的是TransactionInterceptor

invoke回调方法

```java
@Override
@Nullable
public Object invoke(MethodInvocation invocation) throws Throwable {
   // Work out the target class: may be {@code null}.
   // The TransactionAttributeSource should be passed the target class
   // as well as the method, which may be from an interface.
   Class<?> targetClass = (invocation.getThis() != null ? AopUtils.getTargetClass(invocation.getThis()) : null);

   // Adapt to TransactionAspectSupport's invokeWithinTransaction...
   return invokeWithinTransaction(invocation.getMethod(), targetClass, invocation::proceed);
}
```

对around-advice-based通用的委托处理，能够处理CallbackPreferringPlatformTransactionManager以及实现PlatformTransactionManager的接口

```java
/**
 * General delegate for around-advice-based subclasses, delegating to several other template
 * methods on this class. Able to handle {@link CallbackPreferringPlatformTransactionManager}
 * as well as regular {@link PlatformTransactionManager} implementations.
 * @param method the Method being invoked
 * @param targetClass the target class that we're invoking the method on
 * @param invocation the callback to use for proceeding with the target invocation
 * @return the return value of the method, if any
 * @throws Throwable propagated from the target invocation
 */
@Nullable
protected Object invokeWithinTransaction(Method method, @Nullable Class<?> targetClass,
      final InvocationCallback invocation) throws Throwable {

   // If the transaction attribute is null, the method is non-transactional.
   TransactionAttributeSource tas = getTransactionAttributeSource();
   final TransactionAttribute txAttr = (tas != null ? tas.getTransactionAttribute(method, targetClass) : null);
   // 获取事务管理 TransactionManager.class
   final TransactionManager tm = determineTransactionManager(txAttr);

   // reactive风格的事务管理器，忽略
   if (this.reactiveAdapterRegistry != null && tm instanceof ReactiveTransactionManager) {
      ReactiveTransactionSupport txSupport = this.transactionSupportCache.computeIfAbsent(method, key -> {
         if (KotlinDetector.isKotlinType(method.getDeclaringClass()) && KotlinDelegate.isSuspend(method)) {
            throw new TransactionUsageException(
                  "Unsupported annotated transaction on suspending function detected: " + method +
                  ". Use TransactionalOperator.transactional extensions instead.");
         }
         ReactiveAdapter adapter = this.reactiveAdapterRegistry.getAdapter(method.getReturnType());
         if (adapter == null) {
            throw new IllegalStateException("Cannot apply reactive transaction to non-reactive return type: " +
                  method.getReturnType());
         }
         return new ReactiveTransactionSupport(adapter);
      });
      return txSupport.invokeWithinTransaction(
            method, targetClass, invocation, txAttr, (ReactiveTransactionManager) tm);
   }

   // 重点看接下来的部分
   // 校验，是否是PlatformTransactionManager类型，否会抛出异常
   PlatformTransactionManager ptm = asPlatformTransactionManager(tm);
   final String joinpointIdentification = methodIdentification(method, targetClass, txAttr);

   if (txAttr == null || !(ptm instanceof CallbackPreferringPlatformTransactionManager)) {
      // Standard transaction demarcation with getTransaction and commit/rollback calls.
      //  开启事务
      TransactionInfo txInfo = createTransactionIfNecessary(ptm, txAttr, joinpointIdentification);

      Object retVal;
      try {
         // This is an around advice: Invoke the next interceptor in the chain.
         // This will normally result in a target object being invoked.
         // 方法调用
         retVal = invocation.proceedWithInvocation();
      }
      catch (Throwable ex) {
         // target invocation exception
         // 回滚事务
         completeTransactionAfterThrowing(txInfo, ex);
         throw ex;
      }
      finally {
         cleanupTransactionInfo(txInfo);
      }

      if (vavrPresent && VavrDelegate.isVavrTry(retVal)) {
         // Set rollback-only in case of Vavr failure matching our rollback rules...
         TransactionStatus status = txInfo.getTransactionStatus();
         if (status != null && txAttr != null) {
            retVal = VavrDelegate.evaluateTryFailure(retVal, txAttr, status);
         }
      }
      // 提交事务
      commitTransactionAfterReturning(txInfo);
      return retVal;
   }

   else {
      final ThrowableHolder throwableHolder = new ThrowableHolder();

      // It's a CallbackPreferringPlatformTransactionManager: pass a TransactionCallback in.
      try {
         Object result = ((CallbackPreferringPlatformTransactionManager) ptm).execute(txAttr, status -> {
            TransactionInfo txInfo = prepareTransactionInfo(ptm, txAttr, joinpointIdentification, status);
            try {
               Object retVal = invocation.proceedWithInvocation();
               if (vavrPresent && VavrDelegate.isVavrTry(retVal)) {
                  // Set rollback-only in case of Vavr failure matching our rollback rules...
                  retVal = VavrDelegate.evaluateTryFailure(retVal, txAttr, status);
               }
               return retVal;
            }
            catch (Throwable ex) {
               if (txAttr.rollbackOn(ex)) {
                  // A RuntimeException: will lead to a rollback.
                  if (ex instanceof RuntimeException) {
                     throw (RuntimeException) ex;
                  }
                  else {
                     throw new ThrowableHolderException(ex);
                  }
               }
               else {
                  // A normal return value: will lead to a commit.
                  throwableHolder.throwable = ex;
                  return null;
               }
            }
            finally {
               cleanupTransactionInfo(txInfo);
            }
         });

         // Check result state: It might indicate a Throwable to rethrow.
         if (throwableHolder.throwable != null) {
            throw throwableHolder.throwable;
         }
         return result;
      }
      catch (ThrowableHolderException ex) {
         throw ex.getCause();
      }
      catch (TransactionSystemException ex2) {
         if (throwableHolder.throwable != null) {
            logger.error("Application exception overridden by commit exception", throwableHolder.throwable);
            ex2.initApplicationException(throwableHolder.throwable);
         }
         throw ex2;
      }
      catch (Throwable ex2) {
         if (throwableHolder.throwable != null) {
            logger.error("Application exception overridden by commit exception", throwableHolder.throwable);
         }
         throw ex2;
      }
   }
}
```

### 事务创建

**Spring事务的开启实际上是将数据库的自动提交设为false**。

createTransactionIfNecessary

——&gt; tm.getTransaction\(txAttr\);

——&gt;startTransaction\(def, transaction, debugEnabled, suspendedResources\);

——&gt;doBegin

```java
@Override
protected void doBegin(Object transaction, TransactionDefinition definition) {
    // 此时，txObject不为null，只是其核心的ConnectHolder属性为null
    DataSourceTransactionObject txObject = (DataSourceTransactionObject) transaction;
    Connection con = null;
    if (txObject.getConnectionHolder() == null ||
            txObject.getConnectionHolder().isSynchronizedWithTransaction()) {
        Connection newCon = this.dataSource.getConnection();
        // 获得连接，可以看出ConnectionHolder是对Connection的包装
        txObject.setConnectionHolder(new ConnectionHolder(newCon), true);
    }
    txObject.getConnectionHolder().setSynchronizedWithTransaction(true);
    con = txObject.getConnectionHolder().getConnection();
    // 设置是否只读和隔离级别
    Integer previousIsolationLevel = DataSourceUtils.prepareConnectionForTransaction(con, definition);
    txObject.setPreviousIsolationLevel(previousIsolationLevel);
    // Switch to manual commit if necessary. This is very expensive in some JDBC drivers,
    // so we don't want to do it unnecessarily (for example if we've explicitly
    // configured the connection pool to set it already).
    if (con.getAutoCommit()) {
        txObject.setMustRestoreAutoCommit(true);
        con.setAutoCommit(false);
    }
    txObject.getConnectionHolder().setTransactionActive(true);
    int timeout = determineTimeout(definition);
    if (timeout != TransactionDefinition.TIMEOUT_DEFAULT) {
        txObject.getConnectionHolder().setTimeoutInSeconds(timeout);
    }
    // Bind the session holder to the thread.
    if (txObject.isNewConnectionHolder()) {
        TransactionSynchronizationManager.bindResource(getDataSource(), txObject.getConnectionHolder());
    }
}
```

### 隔离级别分析

这里设置隔离级别，实际上调用的就是jdbc

```java
/**
 * Prepare the given Connection with the given transaction semantics.
 * @param con the Connection to prepare
 * @param definition the transaction definition to apply
 * @return the previous isolation level, if any
 * @throws SQLException if thrown by JDBC methods
 * @see #resetConnectionAfterTransaction
 * @see Connection#setTransactionIsolation
 * @see Connection#setReadOnly
 */
@Nullable
public static Integer prepareConnectionForTransaction(Connection con, @Nullable TransactionDefinition definition)
      throws SQLException {

   Assert.notNull(con, "No Connection specified");

   boolean debugEnabled = logger.isDebugEnabled();
   // Set read-only flag.
   if (definition != null && definition.isReadOnly()) {
      try {
         if (debugEnabled) {
            logger.debug("Setting JDBC Connection [" + con + "] read-only");
         }
         con.setReadOnly(true);
      }
      catch (SQLException | RuntimeException ex) {
         Throwable exToCheck = ex;
         while (exToCheck != null) {
            if (exToCheck.getClass().getSimpleName().contains("Timeout")) {
               // Assume it's a connection timeout that would otherwise get lost: e.g. from JDBC 4.0
               throw ex;
            }
            exToCheck = exToCheck.getCause();
         }
         // "read-only not supported" SQLException -> ignore, it's just a hint anyway
         logger.debug("Could not set JDBC Connection read-only", ex);
      }
   }

   // Apply specific isolation level, if any.
   Integer previousIsolationLevel = null;
   if (definition != null && definition.getIsolationLevel() != TransactionDefinition.ISOLATION_DEFAULT) {
      if (debugEnabled) {
         logger.debug("Changing isolation level of JDBC Connection [" + con + "] to " +
               definition.getIsolationLevel());
      }
      int currentIsolation = con.getTransactionIsolation();
      if (currentIsolation != definition.getIsolationLevel()) {
         previousIsolationLevel = currentIsolation;
         // 设置隔离级别
         con.setTransactionIsolation(definition.getIsolationLevel());
      }
   }

   return previousIsolationLevel;
}

// ------------------------------------------------------------------------------------------------------------------
    /**
     * Attempts to change the transaction isolation level for this
     * <code>Connection</code> object to the one given.
     * The constants defined in the interface <code>Connection</code>
     * are the possible transaction isolation levels.
     * <P>
     * <B>Note:</B> If this method is called during a transaction, the result
     * is implementation-defined.
     *
     * @param level one of the following <code>Connection</code> constants:
     *        <code>Connection.TRANSACTION_READ_UNCOMMITTED</code>,
     *        <code>Connection.TRANSACTION_READ_COMMITTED</code>,
     *        <code>Connection.TRANSACTION_REPEATABLE_READ</code>, or
     *        <code>Connection.TRANSACTION_SERIALIZABLE</code>.
     *        (Note that <code>Connection.TRANSACTION_NONE</code> cannot be used
     *        because it specifies that transactions are not supported.)
     * @exception SQLException if a database access error occurs, this
     * method is called on a closed connection
     *            or the given parameter is not one of the <code>Connection</code>
     *            constants
     * @see DatabaseMetaData#supportsTransactionIsolationLevel
     * @see #getTransactionIsolation
     */
    void setTransactionIsolation(int level) throws SQLException;
```

### 事务传播分析

> 事务传播：指的是，如果在开始当前事务之前，一个事务上下文已经存在，此时可以指定一个事务性方法的执行行为。

需要注意的是，事务的隔离级别底层调用的是jdbc，但事务传播却不是jdbc特有的，而是Spring提供的一种特性。

事务的传播发生在事务的创建

org.springframework.transaction.interceptor.TransactionAspectSupport\#createTransactionIfNecessary

——&gt;tm.getTransaction\(txAttr\);

——&gt;handleExistingTransaction\(def, transaction, debugEnabled\);

如果事务已经存在

```java
if (isExistingTransaction(transaction)) {
   // Existing transaction found -> check propagation behavior to find out how to behave.
   return handleExistingTransaction(def, transaction, debugEnabled);
}
```

如果事务已经存在，则进行处理

```java
/**
 * Create a TransactionStatus for an existing transaction.
 */
private TransactionStatus handleExistingTransaction(
      TransactionDefinition definition, Object transaction, boolean debugEnabled)
      throws TransactionException {

   if (definition.getPropagationBehavior() == TransactionDefinition.PROPAGATION_NEVER) {
      throw new IllegalTransactionStateException(
            "Existing transaction found for transaction marked with propagation 'never'");
   }

   if (definition.getPropagationBehavior() == TransactionDefinition.PROPAGATION_NOT_SUPPORTED) {
      if (debugEnabled) {
         logger.debug("Suspending current transaction");
      }
      Object suspendedResources = suspend(transaction);
      boolean newSynchronization = (getTransactionSynchronization() == SYNCHRONIZATION_ALWAYS);
      return prepareTransactionStatus(
            definition, null, false, newSynchronization, debugEnabled, suspendedResources);
   }

   if (definition.getPropagationBehavior() == TransactionDefinition.PROPAGATION_REQUIRES_NEW) {
      if (debugEnabled) {
         logger.debug("Suspending current transaction, creating new transaction with name [" +
               definition.getName() + "]");
      }
      SuspendedResourcesHolder suspendedResources = suspend(transaction);
      try {
         return startTransaction(definition, transaction, debugEnabled, suspendedResources);
      }
      catch (RuntimeException | Error beginEx) {
         resumeAfterBeginException(transaction, suspendedResources, beginEx);
         throw beginEx;
      }
   }

   if (definition.getPropagationBehavior() == TransactionDefinition.PROPAGATION_NESTED) {
      if (!isNestedTransactionAllowed()) {
         throw new NestedTransactionNotSupportedException(
               "Transaction manager does not allow nested transactions by default - " +
               "specify 'nestedTransactionAllowed' property with value 'true'");
      }
      if (debugEnabled) {
         logger.debug("Creating nested transaction with name [" + definition.getName() + "]");
      }
      if (useSavepointForNestedTransaction()) {
         // Create savepoint within existing Spring-managed transaction,
         // through the SavepointManager API implemented by TransactionStatus.
         // Usually uses JDBC 3.0 savepoints. Never activates Spring synchronization.
         DefaultTransactionStatus status =
               prepareTransactionStatus(definition, transaction, false, false, debugEnabled, null);
         status.createAndHoldSavepoint();
         return status;
      }
      else {
         // Nested transaction through nested begin and commit/rollback calls.
         // Usually only for JTA: Spring synchronization might get activated here
         // in case of a pre-existing JTA transaction.
         return startTransaction(definition, transaction, debugEnabled, null);
      }
   }

   // Assumably PROPAGATION_SUPPORTS or PROPAGATION_REQUIRED.
   if (debugEnabled) {
      logger.debug("Participating in existing transaction");
   }
   if (isValidateExistingTransaction()) {
      if (definition.getIsolationLevel() != TransactionDefinition.ISOLATION_DEFAULT) {
         Integer currentIsolationLevel = TransactionSynchronizationManager.getCurrentTransactionIsolationLevel();
         if (currentIsolationLevel == null || currentIsolationLevel != definition.getIsolationLevel()) {
            Constants isoConstants = DefaultTransactionDefinition.constants;
            throw new IllegalTransactionStateException("Participating transaction with definition [" +
                  definition + "] specifies isolation level which is incompatible with existing transaction: " +
                  (currentIsolationLevel != null ?
                        isoConstants.toCode(currentIsolationLevel, DefaultTransactionDefinition.PREFIX_ISOLATION) :
                        "(unknown)"));
         }
      }
      if (!definition.isReadOnly()) {
         if (TransactionSynchronizationManager.isCurrentTransactionReadOnly()) {
            throw new IllegalTransactionStateException("Participating transaction with definition [" +
                  definition + "] is not marked as read-only but existing transaction is");
         }
      }
   }
   boolean newSynchronization = (getTransactionSynchronization() != SYNCHRONIZATION_NEVER);
   return prepareTransactionStatus(definition, transaction, false, newSynchronization, debugEnabled, null);
}
```

#### 事务传播属性Propagation

具体的处理在这里不去深究，从上面代码可以看到，事务的传播通过TransactionDefinition里的属性来指定

* REQUIRED：如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务
* SUPPORTS：如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行
* MANDATORY：如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常
* REQUIRES\_NEW：创建一个新的事务，如果当前存在事务，**则把当前事务挂起**
* NOT\_SUPPORTED：以非事务方式运行，如果当前存在事务，则把当前事务挂起
* NEVER：以非事务方式运行，如果当前存在事务，则抛出异常
* NESTED：如果当前存在事务，当前的方法就应该在这个事务的**嵌套事务**内运行；如果当前没有事务，则该取值等价于REQUIRED（如果当前没有事务，则创建一个新的事务）

在这里，比较不好理解的是REQUIRED和REQUIRES\_NEW、NESTED，后面会来具体分析。

#### **那事务的传播是通过如何实现的呢？**

从下面代码可以看到，通过**ThreadLocal**为当前线程绑定了一个dataSource，表示存在处于活动状态的事务

org.springframework.jdbc.datasource.DataSourceTransactionManager\#doGetTransaction

```java
@Override
protected Object doGetTransaction() {
   DataSourceTransactionObject txObject = new DataSourceTransactionObject();
   txObject.setSavepointAllowed(isNestedTransactionAllowed());
   ConnectionHolder conHolder =
         (ConnectionHolder) TransactionSynchronizationManager.getResource(obtainDataSource());
   txObject.setConnectionHolder(conHolder, false);
   return txObject;
}

// --------------------------------------------------------------------------------------------------------------------
    /**
     * Retrieve a resource for the given key that is bound to the current thread.
     * @param key the key to check (usually the resource factory)
     * @return a value bound to the current thread (usually the active
     * resource object), or {@code null} if none
     * @see ResourceTransactionManager#getResourceFactory()
     */
    @Nullable
    public static Object getResource(Object key) {
        Object actualKey = TransactionSynchronizationUtils.unwrapResourceIfNecessary(key);
        Object value = doGetResource(actualKey);
        if (value != null && logger.isTraceEnabled()) {
            logger.trace("Retrieved value [" + value + "] for key [" + actualKey + "] bound to thread [" +
                    Thread.currentThread().getName() + "]");
        }
        return value;
    }

// --------------------------------------------------------------------------------------------------------------------    
    /**
     * Actually check the value of the resource that is bound for the given key.
     */
    @Nullable
    private static Object doGetResource(Object actualKey) {
        // private static final ThreadLocal<Map<Object, Object>> resources = new NamedThreadLocal<>("Transactional resources");
        Map<Object, Object> map = resources.get();
        if (map == null) {
            return null;
        }
        Object value = map.get(actualKey);
        // Transparently remove ResourceHolder that was marked as void...
        if (value instanceof ResourceHolder && ((ResourceHolder) value).isVoid()) {
            map.remove(actualKey);
            // Remove entire ThreadLocal if empty...
            if (map.isEmpty()) {
                resources.remove();
            }
            value = null;
        }
        return value;
    }
```

### 事务挂起

> 例如：
>
> 方法A支持事务，方法B不支持事务。 方法A调用方法B。 在方法A开始运行时，系统为它建立Transaction，方法A中对于数据库的处理操作，会在该Transaction的控制之下。 这时，方法A调用方法B,方法A打开的 Transaction将挂起，方法B中任何数据库操作，都不在该Transaction的管理之下。 当方法B返回，方法A继续运行，之前的Transaction恢复，后面的数据库操作继续在该Transaction的控制之下 提交或回滚。

简单来说，就是AB两个方法，B不应该在这个A事务里执行。

#### 事务挂起是如何实现？

org.springframework.transaction.support.AbstractPlatformTransactionManager\#suspend

——&gt;org.springframework.jdbc.datasource.DataSourceTransactionManager\#doSuspend

——&gt;org.springframework.transaction.support.TransactionSynchronizationManager\#unbindResource

```java
/**
 * Unbind a resource for the given key from the current thread.
 * @param key the key to unbind (usually the resource factory)
 * @return the previously bound value (usually the active resource object)
 * @throws IllegalStateException if there is no value bound to the thread
 * @see ResourceTransactionManager#getResourceFactory()
 */
public static Object unbindResource(Object key) throws IllegalStateException {
   Object actualKey = TransactionSynchronizationUtils.unwrapResourceIfNecessary(key);
   Object value = doUnbindResource(actualKey);
   if (value == null) {
      throw new IllegalStateException(
            "No value for key [" + actualKey + "] bound to thread [" + Thread.currentThread().getName() + "]");
   }
   return value;
}
```

可以看出，**所谓的事务挂起其实就是一个移除当前线程、数据源的过程**。

```java
/**
 * Actually remove the value of the resource that is bound for the given key.
 */
@Nullable
private static Object doUnbindResource(Object actualKey) {
   Map<Object, Object> map = resources.get();
   if (map == null) {
      return null;
   }
   Object value = map.remove(actualKey);
   // Remove entire ThreadLocal if empty...
   if (map.isEmpty()) {
      resources.remove();
   }
   // Transparently suppress a ResourceHolder that was marked as void...
   if (value instanceof ResourceHolder && ((ResourceHolder) value).isVoid()) {
      value = null;
   }
   if (value != null && logger.isTraceEnabled()) {
      logger.trace("Removed value [" + value + "] for key [" + actualKey + "] from thread [" +
            Thread.currentThread().getName() + "]");
   }
   return value;
}
```

#### 那么挂起这个操作到底是如何实现\(起作用\)的呢?

DataSourceTransactionManager.doSuspend:

```text
@Override
protected Object doSuspend(Object transaction) {
    DataSourceTransactionObject txObject = (DataSourceTransactionObject) transaction;
    txObject.setConnectionHolder(null);
    ConnectionHolder conHolder = (ConnectionHolder)
            TransactionSynchronizationManager.unbindResource(this.dataSource);
    return conHolder;
}
```

其实玄机就在于将ConnectionHolder设为null这一行，因为**一个ConnectionHolder对象就代表了一个数据库连接，将ConnectionHolder设为null就意味着我们下次要使用连接时，将重新从连接池获取，而新的连接的自动提交是为true的**。

### NESTED

> ROPAGATION\_NESTED 开始一个 "嵌套的" 事务, 它是已经存在事务的一个真正的子事务. 嵌套事务开始执行时, 它将取得一个 savepoint. 如果这个嵌套事务失败, 我们将回滚到此 savepoint. 嵌套事务是外部事务的一部分, 只有外部事务结束后它才会被提交.

org.springframework.transaction.support.AbstractTransactionStatus\#createAndHoldSavepoint

其实利用的就是jdbc里的savepoint

#### 关于网上对于ROPAGATION\_NESTED不合理的描述

需要注意的是**，网上有些描述是**

* REQUIRED：如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务
* NESTED：如果当前存在事务，则**创建一个事务**作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于REQUIRED（如果当前没有事务，则创建一个新的事务）

**注意**：这里的描述其实有些问题，对于NESTED，本质上还是**同一个事务的不同保存点（savepoint）**，如果涉及到外层事务回滚，**则内层的也将会被回滚;**

### REQUIRES\_NEW

文章：[Spring事务传播机制Propagation.REQUIRES\_NEW详解及测试](https://blog.csdn.net/liujiancheng521/article/details/83542388)

REQUIRES\_NEW：创建一个新的事务，如果当前存在事务，**则把当前事务挂起**

* REQUIRES\_NEW会开启新的事务，外层事务不会影响内部事务的提交/回滚
* REQUIRES\_NEW的**内部事务的异常，会影响外部事务的回滚**

### NESTED与REQUIRED、REQUIRED\_NEW对比

* NESTED ：本质上是设置一个保存点，实际还是同一个事务。外层回滚，内层也会回滚
* REQUIRED\_NEW：实际上是开启一个**新的事务**，拿到的是新的资源，所以外层事务回滚时，不影响内层事务。内层回滚影响外层回滚，但是外层回滚不影响内层回滚
* REQUIRED\_NEW和REQUIRED区别在于，如果存在事务，则REQUIRED会共用一个事务，**没有内层和外层的说法**。而REQUIRED\_NEW肯定会开启一个新的事务。

## @Transactional分析

在这里说几个常用的属性

#### Propagation.REQUIRED

默认事务传播为REQUIRED

#### Isolation.DEFAULT

默认事务隔离级别为数据库所使用的隔离级别

```java
/**
 * Use the default isolation level of the underlying datastore.
 * All other levels correspond to the JDBC isolation levels.
 * @see java.sql.Connection
 */
DEFAULT(TransactionDefinition.ISOLATION_DEFAULT),
```

#### timeout

默认数据库所使用，如果数据库未设置，就是none。（如果超过一定时间事务还未执行完毕，就自动回滚，单位：s）

#### rollbackFor

遇到什么异常的时候需要回滚，默认是RuntimeException 和 Error及子类，但检查异常（checked exceptions）不会回滚\(如业务异常\)，因此通常设置为rollbackFor = Exception.class。当然，也可以通过noRollbackFor指定何种异常不回滚。

```java
/**
 * Defines zero (0) or more exception {@link Class classes}, which must be
 * subclasses of {@link Throwable}, indicating which exception types must cause
 * a transaction rollback.
 * <p>By default, a transaction will be rolling back on {@link RuntimeException}
 * and {@link Error} but not on checked exceptions (business exceptions). See
 * {@link org.springframework.transaction.interceptor.DefaultTransactionAttribute#rollbackOn(Throwable)}
 * for a detailed explanation.
 * <p>This is the preferred way to construct a rollback rule (in contrast to
 * {@link #rollbackForClassName}), matching the exception class and its subclasses.
 * <p>Similar to {@link org.springframework.transaction.interceptor.RollbackRuleAttribute#RollbackRuleAttribute(Class clazz)}.
 * @see #rollbackForClassName
 * @see org.springframework.transaction.interceptor.DefaultTransactionAttribute#rollbackOn(Throwable)
 */
Class<? extends Throwable>[] rollbackFor() default {};
```

#### setRollbackOnly

还可以编程式的通过setRollbackOnly\(\)方法来指示一个事务**手动回滚**。

使用场景：因为rollbackFor，只有在抛出Throwable时才会进行回滚。而当我们插入多条数据时候，一次次插入有可能并不会插入成功，此时并没抛异常，那么我们这里就需要自己手动判断插入的结果，然后手动回滚。

```java
TransactionAspectSupport.currentTransactionStatus().setRollbackOnly(); // 手动设置回滚
TransactionAspectSupport.currentTransactionStatus().isRollbackOnly();


// 示例
@Resource
private PlatformTransactionManager transManager;
@Resource
private DefaultTransactionDefinition transDefinition;
if (transManager.getTransaction(transDefinition).isRollbackOnly()) {
    return;
}
```

## @Transactional常见问题

* @Transactional注解标注方法修饰符为**非public**时，不会创建代理对象，因此@Transactional注解将会不起作用。org.springframework.transaction.interceptor.AbstractFallbackTransactionAttributeSource\#computeTransactionAttribute
* 在类内部调用调用类内部@Transactional标注的方法。这种情况下也会导致事务不开启。
* 事务方法内部捕捉了异常，没有抛出新的异常，导致事务操作不会进行回滚。

## @Transactional源码

```java
/**
 * Describes a transaction attribute on an individual method or on a class.
 *
 * <p>At the class level, this annotation applies as a default to all methods of
 * the declaring class and its subclasses. Note that it does not apply to ancestor
 * classes up the class hierarchy; methods need to be locally redeclared in order
 * to participate in a subclass-level annotation.
 *
 * <p>This annotation type is generally directly comparable to Spring's
 * {@link org.springframework.transaction.interceptor.RuleBasedTransactionAttribute}
 * class, and in fact {@link AnnotationTransactionAttributeSource} will directly
 * convert the data to the latter class, so that Spring's transaction support code
 * does not have to know about annotations. If no rules are relevant to the exception,
 * it will be treated like
 * {@link org.springframework.transaction.interceptor.DefaultTransactionAttribute}
 * (rolling back on {@link RuntimeException} and {@link Error} but not on checked
 * exceptions).
 *
 * <p>For specific information about the semantics of this annotation's attributes,
 * consult the {@link org.springframework.transaction.TransactionDefinition} and
 * {@link org.springframework.transaction.interceptor.TransactionAttribute} javadocs.
 *
 * @author Colin Sampaleanu
 * @author Juergen Hoeller
 * @author Sam Brannen
 * @since 1.2
 * @see org.springframework.transaction.interceptor.TransactionAttribute
 * @see org.springframework.transaction.interceptor.DefaultTransactionAttribute
 * @see org.springframework.transaction.interceptor.RuleBasedTransactionAttribute
 */
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface Transactional {

   /**
    * Alias for {@link #transactionManager}.
    * @see #transactionManager
    */
   @AliasFor("transactionManager")
   String value() default "";

   /**
    * A <em>qualifier</em> value for the specified transaction.
    * <p>May be used to determine the target transaction manager,
    * matching the qualifier value (or the bean name) of a specific
    * {@link org.springframework.transaction.PlatformTransactionManager}
    * bean definition.
    * @since 4.2
    * @see #value
    */
   @AliasFor("value")
   String transactionManager() default "";

   /**
    * The transaction propagation type.
    * <p>Defaults to {@link Propagation#REQUIRED}.
    * @see org.springframework.transaction.interceptor.TransactionAttribute#getPropagationBehavior()
    */
   Propagation propagation() default Propagation.REQUIRED;

   /**
    * The transaction isolation level.
    * <p>Defaults to {@link Isolation#DEFAULT}.
    * <p>Exclusively designed for use with {@link Propagation#REQUIRED} or
    * {@link Propagation#REQUIRES_NEW} since it only applies to newly started
    * transactions. Consider switching the "validateExistingTransactions" flag to
    * "true" on your transaction manager if you'd like isolation level declarations
    * to get rejected when participating in an existing transaction with a different
    * isolation level.
    * @see org.springframework.transaction.interceptor.TransactionAttribute#getIsolationLevel()
    * @see org.springframework.transaction.support.AbstractPlatformTransactionManager#setValidateExistingTransaction
    */
   Isolation isolation() default Isolation.DEFAULT;

   /**
    * The timeout for this transaction (in seconds).
    * <p>Defaults to the default timeout of the underlying transaction system.
    * <p>Exclusively designed for use with {@link Propagation#REQUIRED} or
    * {@link Propagation#REQUIRES_NEW} since it only applies to newly started
    * transactions.
    * @see org.springframework.transaction.interceptor.TransactionAttribute#getTimeout()
    */
   int timeout() default TransactionDefinition.TIMEOUT_DEFAULT;

   /**
    * A boolean flag that can be set to {@code true} if the transaction is
    * effectively read-only, allowing for corresponding optimizations at runtime.
    * <p>Defaults to {@code false}.
    * <p>This just serves as a hint for the actual transaction subsystem;
    * it will <i>not necessarily</i> cause failure of write access attempts.
    * A transaction manager which cannot interpret the read-only hint will
    * <i>not</i> throw an exception when asked for a read-only transaction
    * but rather silently ignore the hint.
    * @see org.springframework.transaction.interceptor.TransactionAttribute#isReadOnly()
    * @see org.springframework.transaction.support.TransactionSynchronizationManager#isCurrentTransactionReadOnly()
    */
   boolean readOnly() default false;

   /**
    * Defines zero (0) or more exception {@link Class classes}, which must be
    * subclasses of {@link Throwable}, indicating which exception types must cause
    * a transaction rollback.
    * <p>By default, a transaction will be rolling back on {@link RuntimeException}
    * and {@link Error} but not on checked exceptions (business exceptions). See
    * {@link org.springframework.transaction.interceptor.DefaultTransactionAttribute#rollbackOn(Throwable)}
    * for a detailed explanation.
    * <p>This is the preferred way to construct a rollback rule (in contrast to
    * {@link #rollbackForClassName}), matching the exception class and its subclasses.
    * <p>Similar to {@link org.springframework.transaction.interceptor.RollbackRuleAttribute#RollbackRuleAttribute(Class clazz)}.
    * @see #rollbackForClassName
    * @see org.springframework.transaction.interceptor.DefaultTransactionAttribute#rollbackOn(Throwable)
    */
   Class<? extends Throwable>[] rollbackFor() default {};

   /**
    * Defines zero (0) or more exception names (for exceptions which must be a
    * subclass of {@link Throwable}), indicating which exception types must cause
    * a transaction rollback.
    * <p>This can be a substring of a fully qualified class name, with no wildcard
    * support at present. For example, a value of {@code "ServletException"} would
    * match {@code javax.servlet.ServletException} and its subclasses.
    * <p><b>NB:</b> Consider carefully how specific the pattern is and whether
    * to include package information (which isn't mandatory). For example,
    * {@code "Exception"} will match nearly anything and will probably hide other
    * rules. {@code "java.lang.Exception"} would be correct if {@code "Exception"}
    * were meant to define a rule for all checked exceptions. With more unusual
    * {@link Exception} names such as {@code "BaseBusinessException"} there is no
    * need to use a FQN.
    * <p>Similar to {@link org.springframework.transaction.interceptor.RollbackRuleAttribute#RollbackRuleAttribute(String exceptionName)}.
    * @see #rollbackFor
    * @see org.springframework.transaction.interceptor.DefaultTransactionAttribute#rollbackOn(Throwable)
    */
   String[] rollbackForClassName() default {};

   /**
    * Defines zero (0) or more exception {@link Class Classes}, which must be
    * subclasses of {@link Throwable}, indicating which exception types must
    * <b>not</b> cause a transaction rollback.
    * <p>This is the preferred way to construct a rollback rule (in contrast
    * to {@link #noRollbackForClassName}), matching the exception class and
    * its subclasses.
    * <p>Similar to {@link org.springframework.transaction.interceptor.NoRollbackRuleAttribute#NoRollbackRuleAttribute(Class clazz)}.
    * @see #noRollbackForClassName
    * @see org.springframework.transaction.interceptor.DefaultTransactionAttribute#rollbackOn(Throwable)
    */
   Class<? extends Throwable>[] noRollbackFor() default {};

   /**
    * Defines zero (0) or more exception names (for exceptions which must be a
    * subclass of {@link Throwable}) indicating which exception types must <b>not</b>
    * cause a transaction rollback.
    * <p>See the description of {@link #rollbackForClassName} for further
    * information on how the specified names are treated.
    * <p>Similar to {@link org.springframework.transaction.interceptor.NoRollbackRuleAttribute#NoRollbackRuleAttribute(String exceptionName)}.
    * @see #noRollbackFor
    * @see org.springframework.transaction.interceptor.DefaultTransactionAttribute#rollbackOn(Throwable)
    */
   String[] noRollbackForClassName() default {};

}
```

## 隔离级别属性ISOLATION

* READ\_UNCOMMITTED：该隔离级别表示一个事务可以**读取另一个事务修改但还没有提交**的数据。该级别不能防止脏读，不可重复读和幻读，因此很少使用该隔离级别。比如PostgreSQL实际上并没有此级别。
* READ\_COMMITTED：该隔离级别表示一个事务**只能读取另一个事务已经提交**的数据。该级别可以**防止脏读**，这也是大多数情况下的推荐值。
* REPEATABLE\_READ：该隔离级别表示一个事务在整个过程中可以多次重复执行某个查询，并且每次返回的记录都相同。**该级别可以防止脏读和不可重复读。**
* SERIALIZABLE：所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，**该级别可以防止脏读、不可重复读以及幻读**。但是这将严重影响程序的性能。通常情况下也不会用到该级别。

从上面来看，能供我们选择的也只有READ\_COMMITTED和REPEATABLE\_READ，俗称的RC和RR级别。对于脏读、不可重复读以及幻读，以及隔离级别，分布式事务，数据库的锁，会在**MySQL/3 事务和锁** 再详细分析。

READ\_COMMITTED：禁止脏读，但允许不可重复读和幻读

REPEATABLE\_READ：禁止脏读和不可重复读，但运行幻读

## 总结

本章分析了Spring事务的实现原理，处理的流程。对事务的传播实现进行了分析，另外对@Transactional注解常用功能进行总结。

