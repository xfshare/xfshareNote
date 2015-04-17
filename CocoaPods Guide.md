#[CocoaPods](https://cocoapods.org) Guide

## ruby 配置源

		ruby -v 查看版本
		gem sources --remove https://rubygems.org/ 移除源
		gem sources -a http://ruby.taobao.org/ 添加源
		gem sources -l //查看源

## Install
		
	>Setting up CocoaPods master repo,一直处在这个状态,下载所需文件,文件目录 ~/.cocoapods 使用du -sh * 查看文件夹大小,大约会在120多MB
	
		sudo gem install cocoapods
		
## Use
1. Terminal进入项目根目录.[^1]

2. Edit Podfile

		vim Podfile
		
		格式如下:
		platform :ios, '7.0' 
		pod "AFNetworking", "~> 2.0" 
		
		//保存,退出
		ESC->:wq

3. Install

		pod install
		
		如果没有反应,使用
		pod install --verbose --no-repo-update 
		或者 
		pod update --verbose --no-repo-update 

4. Finish
	
		.xcworkspace Run Proj
		

## Search Other SDK

		
		pod search 'name'

## Help
		
		pod --help
