---
layout: post
title: "GitLab 学习笔记"
tagline: ""
description: "GitLab 折腾记录"
category: 学习笔记
tags: [GitLab, Git, ]
last_updated: 
---

Here is some config file paths and some commands I used to deal with GitLab. This is a learning note, not an instruction of installing GitLab on your server. For the detail of installation, please check the official site and [playlist](https://www.youtube.com/playlist?list=PLKHfgb7QQO0h0KajNxedrSGoTZz7WR7dB) I create on Youtube.

![gitlab icon](https://lh3.googleusercontent.com/-l4q7YxpSrak/Vf54ohUJuCI/AAAAAAAA15Q/BMtzh9XgSJY/s512-Ic42/gitlab_icon.png)

##config
gitlab主要配置目录 `/etc/gitlab/gitlab.rb`
 
##command

- gitlab-ctl reconfigure
	always run this command after modifying the config file `/etc/gitlab/gitlab.rb`. All the config files are generated by this command and located under `opt/gitlab/embedded/service/gitlab-rails/config`.

- gitlab-ctl start|stop|restart
	start stop and restart gitlab service

- gitlab-ctl status
	输出类似下面的格式：

		run: logrotate: (pid 744) 197s; run: log: (pid 737) 197s
		run: nginx: (pid 743) 197s; run: log: (pid 731) 197s
		run: postgresql: (pid 748) 197s; run: log: (pid 738) 197s
		run: redis: (pid 746) 197s; run: log: (pid 734) 197s
		run: sidekiq: (pid 747) 197s; run: log: (pid 736) 197s
		run: unicorn: (pid 745) 197s; run: log: (pid 735) 197s

- gitlab-ctl tail
	trace the log, and check error

- gitlab-ctl tail postgresql
	单独查看 postgresql 日志

##file path
- /opt/gitlab
	gitlab和依赖应用的代码

- /var/opt/gitlab/bin
	gitlab-ctl reconfigure命令写入的地方，存放应用数据和配置文件 /etc/gitlab omnibus 版本 gitlab的配置文件，平常只需要修改这个配置文件就够了

- /var/opt/gitlab/git-data/repositories
	all the repo are stored here.

- /var/log/gitlab
	包含 omnibus 版本 gitlab 所有组件的日志数据

- /var/opt/gitlab/nginx/conf/
	nginx.conf and gitlab-http.conf

## GitLab SMTP setting
official document is [here](https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/doc/settings/smtp.md)

Following is my setting to use 163 NetEase's SMTP service. But after I test for a while, most of GitLab emails were blocked by the service. So I change my setting to use **mailgun**.

	# If your SMTP server does not like the default 'From: gitlab@localhost' you 
	# can change the 'From' with this setting.
	# 网易服务器smtp机器要求身份验证帐号和发信帐号必须一致
	# 如果用户在发送邮件时，身份验证帐号和发件人帐号是不同的，因此拒绝发送。
	gitlab_rails['gitlab_email_from'] = 'username@163.com'
	gitlab_rails['gitlab_email_reply_to'] = 'username@163.com'

	#send gitlab notification via SMTP by 163.com
	gitlab_rails['smtp_enable'] = true
	gitlab_rails['smtp_address'] = "smtp.163.com"
	gitlab_rails['smtp_port'] = 25
	gitlab_rails['smtp_user_name'] = "username@163.com"
	gitlab_rails['smtp_password'] = "smtp password"
	gitlab_rails['smtp_domain'] = "163.com"
	gitlab_rails['smtp_authentication'] = "login"
	gitlab_rails['smtp_enable_starttls_auto'] = true

This is my setting for mailgun

	# Sending email via SMTP using mailgun, following is the config
	gitlab_rails['smtp_enable'] = true
	gitlab_rails['smtp_address'] = "smtp.mailgun.org"
	gitlab_rails['smtp_port'] = 587
	gitlab_rails['smtp_user_name'] = "gitlab@domain.com created in the console of mailgun"
	gitlab_rails['smtp_password'] = "password created in the console of mailgun"
	gitlab_rails['smtp_domain'] = "your domian.com same as the @domain.com"
	gitlab_rails['smtp_authentication'] = "login"
	gitlab_rails['smtp_enable_starttls_auto'] = true

##Ref

<http://doc.gitlab.com/omnibus/>
