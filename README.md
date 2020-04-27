# rabbitmq-util
mq快速使用


````
package com.xnx3.demo.delay.simple;

import java.io.IOException;
import java.util.Date;
import com.xnx3.rabbitmq.DelayUtil;
import com.xnx3.rabbitmq.interfaces.DelayReceiveInterface;

/**
 * 消费者，也就是消息接受者。这里是最简单的使用方式。
 * 这里将消息生产者（发送）、消费者（接收）一块进行的演示。发送、接收 都在这个类中
 * @author 管雷鸣 www.guanleiming.com
 */
public class Demo {
	static DelayUtil delayUtil;
	static{
		/*
		 * delayUtil 的初始化、以及 调用 delayUtil.receive(...) 接收消息处理消息，只需一次即可。
		 */
		try {
			delayUtil = new DelayUtil();
			delayUtil.receive(new DelayReceiveInterface() {
				public boolean dispose(String msg) {
					//接收到消息，消息在这里进行处理。
					System.out.println("消息内容为:"+msg+",time:"+new Date().getTime());
					/*
					 * 如果处理成功，则返回true，证明已经消费成功了。
					 * 如果没处理成功，返回false，那么证明没消费成功，会触发下面的 failure(msg) 接口 
					 */
					return false;
				}
				public void failure(String msg) {
					System.out.println("当消息消费失败后，执行此处。这里更多的多用是，当消息执行失败后，通过这里记录日志，或者发邮件、短信通知开发者。这里接收到的信息为 :"+msg);
				}
			});
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
	
	public static void main(String[] args) throws IOException {
		//这里发送延迟消息时，直接通过 delayUtil.send(...) 即可发送延迟消息，延迟多少秒后，自动由 delayUtil.receive(...) 接收
		delayUtil.send("哈哈，我延迟1秒", 1);
		delayUtil.send("哈哈，我延迟2秒", 2);
		delayUtil.send("哈哈，我延迟3秒", 3);
		delayUtil.send("哈哈，我就比较厉害了，我延迟10秒", 10);
	}
}

````