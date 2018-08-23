---
layout: post
title: "TabbleView的Cell中有Textfield、TextView时防止键盘遮挡"
date: 2018-08-23 09:36:34 +0800
comments: true
categories: 
---



TableView的Cell中有TextField、TextView时防止键盘遮挡小技巧

<!--more-->
直接上代码后面再解释



	- (UITableView *)tableView {	     if (!_tableView) {            UITableViewController* tvc=[[UITableViewController alloc] initWithStyle:UITableViewStylePlain];            [self addChildViewController:tvc];            [tvc.view setFrame:self.view.frame];            _tableView=tvc.tableView;            _tableView.delegate = self;            _tableView.dataSource = self;            _tableView.separatorStyle = UITableViewCellSeparatorStyleSingleLine;      }        return _tableView;
    } 


直接在初始化TableView的时候用系统的UITableViewController替换掉，因为UITableViewController中的tableview已经做了可以自适应键盘高度来改变tableview的额外高度。

