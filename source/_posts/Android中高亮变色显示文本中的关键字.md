---
title: Android中高亮变色显示文本中的关键字
date: 2015-11-16 12:39:04
tags:
- 高亮变色
- 关键字
categories: 
- 技术博客
- 编程技巧
---
应该是好久没有写有关技术类的文章了，前天还有人在群里问我，说群主很长时间没有分享干货了，今天分享一篇Android中TextView在大段的文字内容中如何让关键字高亮变色的文章 ，希望对大家有所帮助，我终于在歪路上回归正途了。这个篇文章在平时应该还算比较常用吧，如果你会了，就不用看了，如果还不会，可以看一眼，非常简单。
<!-- more-->
今天分享的文章大概内容是在TextView中如何使大段的文字内容中关键字变色高亮显示的，分为一个关键字高亮变色显示和多个关键字一起高亮变色显示。我已经封装成了KeywordUtil工具类，可以直接调用，效果图如下：
![效果图](http://7xsgef.com1.z0.glb.clouddn.com/640.jpeg)

具体代码如下：
```java
package net.loonggg.test;

import java.util.regex.Matcher;
import java.util.regex.Pattern;

import android.text.SpannableString;
import android.text.Spanned;
import android.text.style.ForegroundColorSpan;

public class KeywordUtil {

	/**
	 * 关键字高亮变色
	 * 
	 * @param color
	 *            变化的色值
	 * @param text
	 *            文字
	 * @param keyword
	 *            文字中的关键字
	 * @return
	 */
	public static SpannableString matcherSearchTitle(int color, String text,
			String keyword) {
		SpannableString s = new SpannableString(text);
		Pattern p = Pattern.compile(keyword);
		Matcher m = p.matcher(s);
		while (m.find()) {
			int start = m.start();
			int end = m.end();
			s.setSpan(new ForegroundColorSpan(color), start, end,
					Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
		}
		return s;
	}

	/**
	 * 多个关键字高亮变色
	 * 
	 * @param color
	 *            变化的色值
	 * @param text
	 *            文字
	 * @param keyword
	 *            文字中的关键字数组
	 * @return
	 */
	public static SpannableString matcherSearchTitle(int color, String text,
			String[] keyword) {
		SpannableString s = new SpannableString(text);
		for (int i = 0; i < keyword.length; i++) {
			Pattern p = Pattern.compile(keyword[i]);
			Matcher m = p.matcher(s);
			while (m.find()) {
				int start = m.start();
				int end = m.end();
				s.setSpan(new ForegroundColorSpan(color), start, end,
						Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
			}
		}
		return s;
	}
}
```
其实看完代码就知道了，非常简单。大家可以试一试哦！获取demo的方法跟以前一样，只需在公众号**非著名程序员**里回复关键字“5”即可获得。

