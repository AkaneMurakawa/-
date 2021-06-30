# 5 XML

## 1.1 Dom解析XML文档

```text
import java.io.File;
import java.util.ArrayList;
import java.util.List;

import org.dom4j.Attribute;
import org.dom4j.Document;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;

/**
 * 使用Dom解析XML文档
 *
 */
public class ParseXMLDemo {
    public static void main(String[] args) {
        try {
            /*
             * 解析XML的大致步骤：
             * 1.创建SAXReader
             * 2.读取要解析的XML文档并生成Document对象
             * 这一步就是Dom解析耗时耗资源的地方了，因为需要将XML文档都读取完毕并载入内存
             * 3.通过Document对象获取根元素
             * 4.按照XML文档结构从根元素开始逐级获取子元素已达到遍历XML文档数据的目的
             */

            //1
            File file = new File("./src/xml/emp.xml");
            if(file.exists()){
                System.out.println("文件已存在！");
            }else{
                System.out.println("文件创建成功！");
            }

            SAXReader reader = new SAXReader();
            //2
            Document document = reader.read(new File("./src/xml/emp.xml"));
            //reader.read(new FileInputStream("emplist.xml"));

            /*
             * 3
             * Document提供了获取根元素的方法
             * Element getRootElement()
             * 返回的Element实例表示XML文档的根元素
             * 
             * Element的每一个实例用于表示XML文档中的一个元素（一对标签）
             * 
             * Element提供了用于获取其表示的标签的信息的相关方法：
             * String getName()获取当前标签的名字
             * Element element(String name)获取指定标签中指定名字的子标签
             * List elements()获取当前标签中所有同名子标签
             * Attribute attribute(String name)获取当前标签中指定名字的属性
             * attribute：属性; 标志, 象征; 特质, 特性; 定语
             * String getText()获取当前标签中间的文本数据
             */
            Element root = document.getRootElement();
            /*
             * 创建一个集合用于保存所有从XML文档中解析的信息
             */
            List<Emp> empList = new ArrayList<Emp>();
            List<Element> list = root.elements();
            for (Element empEle : list) {
                Element nameEle = empEle.element("name");
                String name = nameEle.getText();
                System.out.println(name);

                Element ageEle = empEle.element("age");
                int age = Integer.parseInt(ageEle.getText());
                System.out.println(age);

                Attribute attr = empEle.attribute("id");
                int id = Integer.parseInt(attr.getText());
                Emp emp = new Emp(name, age);
                empList.add(emp);
            }
            for (Emp emp : empList) {
                System.out.println(emp);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

## 1.2 Dom写出XML文档

```text
import java.io.FileOutputStream;
import java.util.ArrayList;
import java.util.List;

import org.dom4j.Document;
import org.dom4j.DocumentHelper;
import org.dom4j.Element;
import org.dom4j.io.OutputFormat;
import org.dom4j.io.XMLWriter;

/**
 * 使用DOM写出XML文档
 * 
 * SAX解析
 * 优点：解析可以立即开始，速度快，没有内存压力。
 * 缺点：不能对节点做修改
 * 
 * DOM解析(W3C推荐处理XML的一种方式)
 * 优点：把xml文件在内存中构造树形结构，可以遍历和修改节点
 * 缺点：如果文件比较大，内存有压力，解析的时间会比较长
 *
 */
public class WrtiteXMLDemo {
    public static void main(String[] args) {
        try {
            /*
             * 写出XML文档的大致步骤：
             * 1.创建Document对象表示一个空白文档
             * 2.向Document中添加根元素
             * 3.按照XML设计的结构向根元素中逐级添加子元素及数据
             * 4.创建XMLWriter
             * 5.使用XMLWriter将Document对象写出生成XML文档
             * 
             */
            List<Emp> empList = new ArrayList<Emp>();
            empList.add(new Emp("xws",20));
            empList.add(new Emp("xust",20));
            empList.add(new Emp("zyw",20));
            empList.add(new Emp("zl",20));
            //1
            Document document = DocumentHelper.createDocument();
            //2
            Element root = document.addElement("list");
            //3
            for (Emp emp : empList) {
                Element empEle = root.addElement("emp");

                Element nameEle = empEle.addElement("name");
                nameEle.addText(emp.getName());
                Element ageEle = empEle.addElement("age");
                ageEle.addText(String.valueOf(emp.getAge()));

                empEle.addAttribute("id", emp.getAge()+"");
            }
            XMLWriter writer = new XMLWriter(new FileOutputStream("test.xml"),OutputFormat.createPrettyPrint());
            writer.write(document);
            System.out.println("写出完毕");
            writer.close();
        } catch (Exception e) {
            // TODO: handle exception
        }
    }
}
```

## 1.3 使用xpath获取xml文档数据

```text
import java.io.File;
import java.util.List;
import org.dom4j.Document;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;

/**
 * 使用Xpath获取xml文档数据
 *
 */
public class XpathDemo {
    public static void main(String[] args) {
        try {
            SAXReader reader = new SAXReader();
            Document doc = reader.read(new File("myemp.xml"));
            String path = "/list/emp/name";

            /*
             * 使用xpath获取选取的内容
             */
            List<Element> list = doc.selectNodes(path);
            for (Element e : list) {
                System.out.println(e.getText());
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

