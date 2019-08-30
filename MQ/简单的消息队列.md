Mq 为Message Queue 消息队列是应用程序和应用程序的通信方法

RabbitMq是一个开源的，



Rabbit的六种模式
1.一对一
编写一个Uitl
public class mqConnectionUitl {
    public static Connection getConnection() throws Exception{
        //定义工厂
        ConnectionFactory factory=new ConnectionFactory();
        //设置信息
        factory.setHost("localhost");
        factory.setPort(5672);
        factory.setVirtualHost("/fa");
        factory.setUsername("admin");
        factory.setPassword("admin");

        return  factory.newConnection();
    }
}


