---
layout: post
title: "ios键盘之数字键盘"
date: 2018-09-03 13:41:05 +0800
comments: true
categories: 
---

经常要用到金额输入带小数点的键盘，都并不需要自定义，系统有提供带点的数字键盘。
<!--more-->
###配置
field.keyboardType = UIKeyboardTypeNumberPad; 
这个只是纯数字键盘，无小数点。

field.keyboardType = UIKeyboardTypeDecimalPad 
这个只是纯数字键盘，有小数点。



#### <a name="fenced-code-block">金额输入限制两位小数</a>
代码

	- (BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString *)string;
	{
    	//string就是此时输入的那个字符textField就是此时正在输入的那个输入框返回YES就是可以改变输入框的值NO相反
   	 	NSString * toBeString = [textField.text     stringByReplacingCharactersInRange:range withString:string];
   		 // 判断是否输入内容，或者用户点击的是键盘的删除按钮
   		 if (![string isEqualToString:@""]) {
        		if ([textField isEqual:self.textField]) {
            // 小数点在字符串中的位置 第一个数字从0位置开始
            		NSInteger dotLocation = [textField.text rangeOfString:@"."].location;
            		if (dotLocation == NSNotFound && range.location != 0) {
                //没有小数点,最大数值
                		if (range.location >= 9){
                    	NSLog(@"单笔金额不能超过亿位");
                    	if ([string isEqualToString:@"."] && range.location == 9) {
                        	return YES;
                    	}
                    	return NO;
               	 	}
            	}
            //判断输入多个小数点,禁止输入多个小数点
            	if (dotLocation != NSNotFound){
                if ([string isEqualToString:@"."])return NO;
            }
            //判断小数点后最多两位
            if (dotLocation != NSNotFound && range.location > dotLocation + 2) { return NO; }
            //判断总长度
            	if (textField.text.length > 11) {
                	return NO;
            	}
        	}
    	}
    	return YES;
	}



