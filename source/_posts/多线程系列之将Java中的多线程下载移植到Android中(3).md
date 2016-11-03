---
title: 多线程系列之将Java中的多线程下载移植到Android中(3)
date: 2016-04-21 02:37:23
categories: 技术博客
tags: 多线程下载
---
在这一节中，我们就来讲多线程下载以及断点续传在android中怎么使用，前两节是为本节做准备的，没有看前两节的同学，最好看完前面的两篇文章再来看这篇。其实在android端的应用和java基本上是差不多的，只不过在android端我建议对于断点续传的记录的保存放在android的sqlite3的数据库中，这样是最好的，当然保存在sd卡中也行，我为了方便起见，我没有建立数据库，而是直接保存到了sd卡中。先看一下我在android中运行的效果图，如下：
![](http://7xsgef.com1.z0.glb.clouddn.com/20140104183044093.jpg)

我在这里的代码加上了进度条的显示和下载进度百分比的显示，为了让进度条和百分比不混乱，我为下载线程中计算下载进度的代码加上了锁。如下：
![](http://7xsgef.com1.z0.glb.clouddn.com/20140104182023625.jpg)
<!--more-->
还有就是下边的一段代码我把记录下载进度的文件保存到了SD卡中，我建议最好用数据库。这段代码如下：
![](http://7xsgef.com1.z0.glb.clouddn.com/20140104182226937.jpg)

其它的我倒是没有什么好解释的了，跟上一篇文章的内容差不多，而且我在代码中已经写得很详细了，那么就请大家看代码吧！

MainActivity中的代码如下：
```java
package net.loonggg.android.downloader;

import java.io.File;
import java.io.FileInputStream;
import java.io.InputStream;
import java.io.RandomAccessFile;
import java.net.HttpURLConnection;
import java.net.URL;

import android.annotation.SuppressLint;
import android.app.Activity;
import android.os.Bundle;
import android.os.Handler;
import android.os.Message;
import android.view.View;
import android.view.Window;
import android.widget.Button;
import android.widget.EditText;
import android.widget.ProgressBar;
import android.widget.TextView;
import android.widget.Toast;

@SuppressLint("HandlerLeak")
public class MainActivity extends Activity {
	public int currentProcess = 0;// 下载文件的当前进度
	// 开启的线程的个数
	public static final int THREAD_COUNT = 3;
	public static int runningThread = 3;// 记录正在运行的下载文件的线程数
	private EditText et;
	private Button btn;
	private TextView tv;
	private ProgressBar pb;// 下载进度条

	private Handler handler = new Handler() {
		public void handleMessage(android.os.Message msg) {
			switch (msg.arg1) {
			case 0:
				Toast.makeText(getApplicationContext(), "下载失败！",
						Toast.LENGTH_SHORT).show();
				break;
			case 1:
				Toast.makeText(getApplicationContext(), "下载完成！",
						Toast.LENGTH_SHORT).show();
				break;
			case 2:
				tv.setText("下载进度:" + pb.getProgress() * 100 / pb.getMax() + "%");
				break;
			default:
				break;
			}
		};
	};

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		requestWindowFeature(Window.FEATURE_NO_TITLE);
		setContentView(R.layout.activity_main);
		et = (EditText) findViewById(R.id.et);
		pb = (ProgressBar) findViewById(R.id.pb);
		btn = (Button) findViewById(R.id.btn);
		tv = (TextView) findViewById(R.id.tv_process);
		btn.setOnClickListener(new View.OnClickListener() {

			@Override
			public void onClick(View v) {
				downLoad();
			}
		});
	}

	/**
	 * 下载文件的方法
	 */
	private void downLoad() {
		final String path = et.getText().toString();
		new Thread() {
			public void run() {
				try {
					// 1、连接服务器，获取一个文件，获取文件的长度，在本地创建一个大小跟服务器文件大小一样的临时文件
					URL url = new URL(path);
					HttpURLConnection conn = (HttpURLConnection) url
							.openConnection();
					conn.setConnectTimeout(5000);
					conn.setRequestMethod("GET");
					int code = conn.getResponseCode();
					if (code == 200) {
						// 服务器返回的数据的长度，实际就是文件的长度
						int length = conn.getContentLength();
						pb.setMax(length);// 为进度条设置最大值
						System.out.println("----文件总长度----" + length);
						// 在客户端本地创建出来一个大小跟服务器端文件一样大小的临时文件
						RandomAccessFile raf = new RandomAccessFile(
								"/sdcard/temp.apk", "rwd");
						// 指定创建的这个文件的长度
						raf.setLength(length);
						// 关闭raf
						raf.close();
						// 假设是3个线程去下载资源
						// 平均每一个线程下载的文件的大小
						int blockSize = length / THREAD_COUNT;
						for (int threadId = 1; threadId <= THREAD_COUNT; threadId++) {
							// 第一个线程开始下载的位置
							int startIndex = (threadId - 1) * blockSize;
							int endIndex = threadId * blockSize - 1;
							if (threadId == THREAD_COUNT) {
								endIndex = length;
							}
							System.out.println("----threadId---"
									+ "--startIndex--" + startIndex
									+ "--endIndex--" + endIndex);
							new DownloadThread(path, threadId, startIndex,
									endIndex).start();
						}
					}
				} catch (Exception e) {
					e.printStackTrace();
					Message msg = new Message();
					msg.arg1 = 0;
					handler.sendMessage(msg);
				}
			};
		}.start();
	}

	/**
	 * 下载文件的子线程，每一个线程下载对应位置的文件
	 * 
	 * @author loonggg
	 * 
	 */
	public class DownloadThread extends Thread {
		private int threadId;
		private int startIndex;
		private int endIndex;
		private String path;

		/**
		 * @param path
		 *            下载文件在服务器上的路径
		 * @param threadId
		 *            线程id
		 * @param startIndex
		 *            线程下载的开始位置
		 * @param endIndex
		 *            线程下载的结束位置
		 */
		public DownloadThread(String path, int threadId, int startIndex,
				int endIndex) {
			this.path = path;
			this.threadId = threadId;
			this.startIndex = startIndex;
			this.endIndex = endIndex;
		}

		@Override
		public void run() {
			try {
				// 检查是否存在记录下载长度的文件，如果存在读取这个文件的数据
				// -------------------------替换成数据库----------------------------
				File tempFile = new File("/sdcard/" + threadId + ".txt");
				if (tempFile.exists() && tempFile.length() > 0) {
					FileInputStream fis = new FileInputStream(tempFile);
					byte[] temp = new byte[1024 * 10];
					int leng = fis.read(temp);
					// 已经下载的长度
					String downloadLen = new String(temp, 0, leng);
					int downloadInt = Integer.parseInt(downloadLen);
					// ------------------这两行代码是关于断点续传时，设置进度条的起点时的关键代码-------------------
					int alreadyDownloadInt = downloadInt - startIndex;
					currentProcess += alreadyDownloadInt;// 计算每个线程上次断点已经下载的文件的长度
					// ---------------------------------------------------------------------------------
					startIndex = downloadInt;
					fis.close();
				}
				// ---------------------------------------------------------------

				URL url = new URL(path);
				HttpURLConnection conn = (HttpURLConnection) url
						.openConnection();
				conn.setRequestMethod("GET");
				// 重要：请求服务器下载部分的文件 指定文件的位置
				conn.setRequestProperty("Range", "bytes=" + startIndex + "-"
						+ endIndex);
				conn.setConnectTimeout(5000);
				// 从服务器请求全部资源的状态码200 ok 如果从服务器请求部分资源的状态码206 ok
				int code = conn.getResponseCode();
				System.out.println("---code---" + code);
				InputStream is = conn.getInputStream();// 已经设置了请求的位置，返回的是当前位置对应的文件的输入流
				RandomAccessFile raf = new RandomAccessFile("/sdcard/temp.apk",
						"rwd");
				// 随机写文件的时候从哪个位置开始写
				raf.seek(startIndex);// 定位文件
				int len = 0;
				byte[] buffer = new byte[1024];
				int total = 0;// 记录已经下载的数据的长度
				while ((len = is.read(buffer)) != -1) {
					RandomAccessFile recordFile = new RandomAccessFile(
							"/sdcard/" + threadId + ".txt", "rwd");// 记录每个线程的下载进度，为断点续传做标记
					raf.write(buffer, 0, len);
					total += len;
					recordFile.write(String.valueOf(startIndex + total)
							.getBytes());
					recordFile.close();
					// 同步加锁，防止混乱
					synchronized (MainActivity.this) {
						currentProcess += len;// 获取当前的总进度
						// 特殊情况，ProgressBarH和ProgressDialog进度条和对话框可以在子线程里面更新UI，系统内部代码特殊处理
						pb.setProgress(currentProcess);// 更改界面上进度条的进度
						Message msg = Message.obtain();
						msg.arg1 = 2;
						// 发送更新消息，更新进度的百分比
						handler.sendMessage(msg);
					}
				}
				is.close();
				raf.close();
				System.out.println("线程：" + threadId + "下载完毕了！");
			} catch (Exception e) {
				e.printStackTrace();
				Message msg = handler.obtainMessage();
				msg.arg1 = 0;
				handler.sendMessage(msg);
			} finally {
				threadFinish();
			}
		}

		/**
		 * 我个人认为不锁定也可以，但是锁定可能更安全，如果谁有好的建议，到底用不用锁定，请给我留言
		 */
		private synchronized void threadFinish() {
			runningThread--;
			if (runningThread == 0) {// 所有的线程已经执行完毕
				for (int i = 1; i <= THREAD_COUNT; i++) {// 删除记录下载进度的文件
					File file = new File("/sdcard/" + i + ".txt");
					file.delete();
					Message msg = handler.obtainMessage();
					msg.arg1 = 1;
					handler.sendMessage(msg);
				}
			}
		}
	}

}
```

还有就是对应的Activity_main.xml的代码如下：
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >

    <EditText
        android:id="@+id/et"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="http://dl1.byme001.com/phone_android.apk" />

    <ProgressBar
        android:id="@+id/pb"
        style="?android:attr/progressBarStyleHorizontal"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" />

    <TextView
        android:id="@+id/tv_process"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="下载进度"
        android:textSize="30sp" />

    <Button
        android:id="@+id/btn"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="下载" />

</LinearLayout>
```
当然大家别忘了在清单文件中设置网络权限和读写SD卡的权限： 

```xml
 <uses-permission android:name="android.permission.INTERNET" />
 <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```
到这里就完了！大家如果有什么不明白的，可以在下面留言！ 

