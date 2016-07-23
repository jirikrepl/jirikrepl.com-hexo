---
title: Enable Xdebug on Windows
date: 2016-07-23 15:05:23
tags:
---

In order to install and configure Xdebug on Windows machine:

- In terminal create file with phpInfo() output: 

		$ php -i > phpOutput

- Copy the contents of phpOutput to form [on this page](http://xdebug.org/wizard.php) and press *Analyze* button

- Follow instructions on page and download generated Xdebug ddl

- Edit `php.ini` file. Set proper path to `ddl` file. Uncoment those lines below. 

		[XDebug]
		zend_extension = C:\xampp\php\ext\php_xdebug-2.3.1-5.6-vc11.dll
		xdebug.remote_enable = 1	
		xdebug.remote_handler = "dbgp"	
		xdebug.remote_host = "127.0.0.1"