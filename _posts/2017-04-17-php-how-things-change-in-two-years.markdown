---
layout: post
title:  "PHP: how things change in two years"
img: posts/scalar-type-hinting-return-types-no-docs.png
categories: php, programming
---

More than two years ago I started my own [PHP Design Pattern](https://github.com/Hyunk3l/php-design-patterns) repository with code examples.
Yes, yet another design patterns repository!
In spare time, I was trying to write down each one of original [Gang of Four design patterns](https://en.wikipedia.org/wiki/Design_Patterns), in PHP, with some customizations.
Sadly I had no time to code all the 23 classic patterns and I abandoned the repo with just 8 examples completed.

## Early 2015
PHP stable release was 5.6.6 and PHP 7 was still in development, PHPUnit latest release was 4.4.
PHP community was talking about [PHP-NG](https://wiki.php.net/phpng), that was the base of PHP 7 and was a refactor of the Zend Engine and promised a 100% increase in performance.
One of the largest frameworks for PHP, Symfony, was on the [2.6 version](http://symfony.com/blog/symfony-2-6-3-released).
Meanwhile, great things like [Hexagonal Architecture](http://alistair.cockburn.us/Hexagonal+architecture) and [Domain-Driven Design](https://en.wikipedia.org/wiki/Domain-driven_design) were starting to be a trend and developers were starting to adopt them.
I was working in local environment using [Vagrant](https://www.vagrantup.com/) and I was starting using [Ansible for machine provisioning](https://www.ansible.com/provisioning).
About my repo, it had composer, PSR-4, unit tests, [PSR-1](http://www.php-fig.org/psr/psr-1/), code sniffer, Travis for CI etc etc.

## Early 2017
After two years, there are a lot of changes in PHP, that I'm not going to list here, but I'll delegate to other articles:
* [WHAT'S NEW IN PHP 7?](https://mediatemple.net/community/products/dv/207889153/what%27s-new-in-php-7)
* [Upcoming changes in PHP 7.1](https://m.dotdev.co/upcoming-changes-in-php-7-1-76ebea53b820)

Actually, PHP 7.1.4 version is out, 7.2 is on the go and all versions previous 7.0 are discontinued.
[PHPUnit](https://phpunit.de/) is on version 6.1 and is supported on PHP 7.0 and PHP 7.1.
Symfony is on [3.2 version](https://symfony.com/blog/symfony-3-2-7-released), 3.3 is on the go and Fabien Potencier is thinking [about version 4](https://medium.com/@fabpot/fabien-potencier-4574622d6a7e).
Meanwhile, for local environment (not only) a lot of people are using Docker, thanks to Docker for [Mac](https://docs.docker.com/docker-for-mac/) and [Windows](https://docs.docker.com/docker-for-windows/) too, that adds "native" support instead of using Docker Machine.

### PSR-4 Adaptation
First of all I needed to modernize composer.json:
![PSR-4 in composer.json](posts/psr-4-directories-structure.png)
Among other changes, I changed the namespace:
![namespace adaptation](posts/namespace-adaptation.png)

### Scalar type hinting, return type, no more doc required
Big changes in PHP 7.0 are the introduction of scalar type hinting and return type.
![scalar type hinting and return type in PHP 7](posts/scalar-type-hinting-return-types-no-docs.png)
As you can see, I removed documentation, since method params types are specified natively!
Furthermore, imho, method/class descriptions are useless too if the code is clean enough and names are meaningfully. So I removed them. 

### Unit Tests
Big changes in PHPUnit too and since my code is compatible with >= 7.0 only, I started using 6.0 version.
![PHPUnit 6 adaptation](posts/phpunit-6-adaptation.png)
and more syntax changes.
Another change I did in my unit tests is related with the way I code.
Test cases should reflect use cases, that's why I changed test methods names:
![Test cases should reflect use cases](posts/new-way-of-uts.png)
from `testExecuteCommand` to `shouldExecuteACommand`. This is just a silly example.
Furthermore, as you can see, I removed a totally useless method description `Testing execute.` (what was I thinking for?!) and add a `@test` annotation
that avoids me to put a silly `test` prefix in test method.

### Travis: no more PHP < 7.0
A smaller change in my repo:
![travis ci no more php smaller than seven](posts/travis-no-more-php-smaller-than-seven.png)
but a big one in PHP community!
A lot of libraries/tools/frameworks are leaving behind the past and supporting only PHP >= 7.0

## Conclusion
Time flies, two years passed really fast!
Software Engineering changed a lot: new technologies, new architectures, new frameworks, new tools, but PHP is still there.
Sometimes we must stop for a moment and look back to the past to see changes we made and see if we've done them in the right way.