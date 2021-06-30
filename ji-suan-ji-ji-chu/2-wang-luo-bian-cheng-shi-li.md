# 网络编程

> 编写一个网络应用程序，有客户端和服务器，双方能进行通信。分别使用TCP和UDP方式实现

## 1.1 基于TCP的网络编程

### Client

```text
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.io.OutputStreamWriter;
import java.io.PrintWriter;
import java.net.Socket;
import java.net.UnknownHostException;
import java.util.Scanner;

/**
 * 聊天工具，仅供学习
 * @since JDK1.8
 */
public class Client {
    /**
     * Socket封装了TCP通讯协议
     */
    private Socket socket;  

    /**
     * 初始化客户端
     */
    public Client() throws UnknownHostException, IOException{
        System.out.println("正在连接服务端...");
        socket = new Socket("localhost",8008);
        System.out.println("已经连接到服务端！");
    }

    /**
     * 客户端开始工作
     */
    public void start(){
        try {
            ServerHandler handler = new ServerHandler();
            Thread t = new Thread(handler);
            t.start();

            Scanner scan = new Scanner(System.in);

            OutputStream os = socket.getOutputStream();
            OutputStreamWriter osw = new OutputStreamWriter(os,"UTF-8");
            PrintWriter pw = new PrintWriter(osw);

            String msg = null;
            long last = System.currentTimeMillis();
            do{
                msg = scan.nextLine();
                if(System.currentTimeMillis()-last >= 1000){
                    pw.println(msg);
                }else{
                    System.out.println("说话速度过快。");
                }
            }while(!"exit".equals(msg));

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 该线程用来循环读取服务端发送过来的每一条消息
     * 并输出到控制台
     */
    private class ServerHandler implements Runnable{
        public void run(){
            try {
                InputStream in = socket.getInputStream();
                InputStreamReader isr = new InputStreamReader(in,"UTF-8");
                BufferedReader br = new BufferedReader(isr);

                String msg = null;
                while((msg = br.readLine()) != null){
                    System.out.println(msg);
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }

    public static void main(String[] args) {
        try {
            Client client = new Client();
            client.start();
        } catch (Exception e) {
            System.out.println("客户端启动失败");
            e.printStackTrace();
        }
    }
}
```

### Server

```text
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.io.OutputStreamWriter;
import java.io.PrintWriter;
import java.net.InetAddress;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.ArrayList;
import java.util.List;

/**
 * 聊天工具，仅供学习
 * @since JDK1.8
 */
public class Server {
    /**
     * 1.申请服务端口
     * 2.监听申请的服务端口，一旦一个客户端通过该端口与服务端连接，
     * ServerSocket就会主动创建一个Socket与该客户端通讯。
     */
    private ServerSocket server;

    /**
     * 存放所有客户端的输出流用于广播
     */
    List<PrintWriter> allOut;

    /**
     * 初始化服务端，申请端口，若该端口已经被其他程序占用，会抛出异常。
     * @throws IOException
     */
    public Server() throws IOException{
        System.out.println("正在启动服务端...");
        server = new ServerSocket(8008);
        System.out.println("成功启动，等待连接！");
        allOut = new ArrayList<PrintWriter>();
    }

    public void start(){
        try {
            while(true){
                System.out.println("等待客户端连接...");
                Socket socket =server.accept();
                System.out.println("一个客户端已连接！");
                ClientHandler handler = new ClientHandler(socket);
                Thread t = new Thread(handler);
                t.start();
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private class ClientHandler implements Runnable{
        /**
         * 通过这个socket与指定客户端交互
         */
        private Socket socket;
        private String hostName;
        public ClientHandler(Socket socket){
            this.socket = socket;
            InetAddress address = socket.getInetAddress();
            hostName = address.getHostName();
        }
        public void run(){
            PrintWriter pw = null;
            try {
                System.out.println(hostName+"上线了！");
                InputStream is = socket.getInputStream(); 
                InputStreamReader isr = new InputStreamReader(is,"UTF-8");
                BufferedReader br = new BufferedReader(isr);

                OutputStream os = socket.getOutputStream();
                OutputStreamWriter osw = new OutputStreamWriter(os,"UTF-8");
                pw = new PrintWriter(osw, true);

                synchronized(allOut){
                    allOut.add(pw);
                }

                String msg = null;
                while((msg = br.readLine())!=null){
                    synchronized(allOut){
                        for (PrintWriter o : allOut) {
                            o.println(hostName+":"+msg);
                        }
                    }
                }
            } catch (Exception e) {
                e.printStackTrace();
            }finally{
                /*
                 * 断开后将该客户端的输出流从共享集合中删除
                 */
                synchronized(allOut){
                    allOut.remove(pw);
                }
                try {
                    socket.close();
                } catch (Exception e2) {
                    e2.printStackTrace();
                }
                System.out.println(hostName+"下线了！");
            }
        }
    }
    public static void main(String[] args) {
        Server server;
        try {
            server = new Server();
            server.start();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## 1.2 基于UDP的网络编程

### Client

```text
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.InetAddress;
/**
 * 1.DatagramSocket
 * 2.DatagramPacket
 * 3.socket.send()
 *
 */
public class UDP_Client {
    public static void main(String[] args) {
        DatagramSocket socket = null;
        try {
            socket = new DatagramSocket();//创建Socket
            byte[] data = "hello，Server！".getBytes();
            DatagramPacket packet = new DatagramPacket(data, data.length,InetAddress.getByName("localhost"),8088);
            socket.send(packet);
        } catch (Exception e) {
            e.printStackTrace();
        }finally{
            if(socket != null){
                socket.close();
            }
        }
    }
}
```

### server

```text
import java.net.DatagramPacket;
import java.net.DatagramSocket;
/**
 * 1.DatagramSocket
 * 2.Datagrampacket
 * 3.socket.receive()
 * @author HanaeYuuma
 *
 */
public class UDP_Server {
    public static void main(String[] args) {
        DatagramSocket socket = null;
        try {
            socket = new DatagramSocket(8088);//申请端口
            byte[] data = new byte[1024];
            DatagramPacket packet = new DatagramPacket(data, data.length);//创建接收包
            socket.receive(packet);//会产生阻塞，读取发送过来的数据
            String str = new String(packet.getData(), 0,packet.getLength());
            System.out.println(str);
        } catch (Exception e) {
            e.printStackTrace();
        }finally{
            if(socket != null){
                socket.close();
            }
        }
    }
}
```

