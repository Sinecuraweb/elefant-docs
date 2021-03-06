# PHP Framework Benchmarks

This page shows benchmarks comparing the following PHP frameworks:

* CakePHP 1.3.4
* CodeIgniter 2.1.0
* Elefant 1.3.0
* FatFree 2.0.9
* FuelPHP 1.1
* Kohana 3.2
* Laravel 3.1.3
* Silex (latest as of 2012-03-31)
* Symfony 1.4.8
* Symfony 2
* Yii 1.1.10
* Zend 1.11.11

> **Note:** CakePHP 2.1.1 is still in the works, but having issues with caching. I will update the results once those are sorted out.

The results are measured in requests/second.

The test is a simple ""Hello World"". While this doesn't test many aspects of a framework (I plan on adding additional tests for the database layer and template rendering in the future), it does test the overhead the framework itself adds with its front controller, bootstrapping, request routing, and how well each framework makes use of APC. This makes it a decent measure for evaluating what cost each framework adds to the core of a project.

## System info

The test machine is a MacBook Pro with the following specs:

	Mac OS X 10.7.3
	2.53 GHz Core i5 w/ 8 GB RAM
	Apache 2.2.21
	PHP 5.3.8

## Benchmarking tool

I forked the [phpmark](http://code.google.com/p/phpmark) benchmarking tool that adds the additional frameworks. The forked code is here:

* [https://github.com/jbroadway/phpmark](https://github.com/jbroadway/phpmark)
* [https://github.com/jbroadway/phpmark-elefant](https://github.com/jbroadway/phpmark-elefant)

For Symfony2, I used the Symfony Project's own version [optimized for Hello World style tests](https://github.com/symfony/symfony-hello-world).

Additionally, paths were modified in the `benchmark.sh` script to adjust the paths for Mac OS X, specifically:

	# Change the following settings if needed
	BENCH_INIT=""/usr/sbin/ab -n 5"";
	BENCH_EXEC=""/usr/sbin/ab -t 30 -c 10""
	CURL_EXEC=""/usr/bin/curl""
	HTTP_START='sudo /usr/sbin/apachectl start'
	HTTP_STOP='sudo /usr/sbin/apachectl stop'

## The results

Here is a chart of the results of the test:

![PHP web framework benchmark results](/files/phpmark-results.png)

> Note: Higher numbers are better.

And here is a table of the actual numbers:

|  | **Req/s - APC off** | **Req/s - APC on** | Winners |
| - | -: | -: | -: |
| **CakePHP 1.3.4** | 51.66 | 78.57 |  |
| **CodeIgniter 2.1.0** | 214.90 | 463.33 | 3rd |
| **Elefant 1.3.0** | 365.12 | 535.49 | **1st** |
| **FatFree 2.0.9** | 350.90 | 518.23 | 2nd |
| **FuelPHP 1.1** | 147.70 | 211.37 |  |
| **Kohana 3.2** | 167.10 | 258.60 |  |
| **Laravel 3.1.3** | 186.43 | 262.87 |  |
| **Silex (2012-03-31)** | 90.79 | 102.83 |  |
| **Symfony 1.4.8** | 59.27 | 99.20 |  |
| **Symfony 2** | 104.77 | 188.60 |  |
| **Yii 1.1.4** | 166.91 | 417.33 |  |
| **Zend 1.10.8** | 108.47 | 163.50 |  |


Some notes about the tests:

1. I'm still working on updating CakePHP to 2.1.1, but I'm running into issues with its caching. I will update the test results for it as soon as those are sorted out.

2. `ab` runs into socket limits on Mac OS X leading to to the following error message:

	apr_poll: The timeout specified has expired (70007)

Turning KeepAlive off doesn't fix it, but this only happens for the baseline tests, which we aren't as concerned about here.

## Conclusions

Elefant comes out on top with a small lead over FatFree. CodeIgniter comes in third with a large boost with APC enabled.

Yii and Laravel are on the upper end of the middle, with Yii almost matching CodeIgniter with APC enabled, while Laravel doesn't seem to benefit as much from APC caching. FuelPHP and Kohana round out the middle.

Symfony 2 improves over 1.4, but still lags substantially behind, along with CakePHP, Silex, and Zend. The surprising one to me was Silex, which I expected to be faster. I suppose it's the cost of parsing the .phar file format. APC didn't provide much improvement for it either.

As you can see, there is a substantial variance in performance between the different frameworks, with the winner being almost 7 times faster than the slowest framework. I hope this information is useful in helping each framework's developers to consider ways they might improve their performance in future releases.

For more benchmarks, see:

* [Template benchmarks comparing Elefant, Twig, and Smarty 3](https://github.com/jbroadway/template-bench)
* [Database abstraction layer benchmarks comparing Elefant, Doctrine, Idiorm/Paris, Propel, RedBean, and Zend_Db](https://github.com/jbroadway/php-dbal-bench)
