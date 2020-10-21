# IntelliJ IDEA / PhpStorm PHPStan / Psalm / Generics

**Use Replacements:**

 - https://github.com/JetBrains/phpstorm-psalm-plugin
 - https://github.com/JetBrains/phpstorm-phpstan-plugin


[![Build Status](https://travis-ci.org/Haehnchen/idea-php-generics-plugin.svg?branch=master)](https://travis-ci.org/Haehnchen/idea-php-generics-plugin)
[![Version](http://phpstorm.espend.de/badge/12754/version)](https://plugins.jetbrains.com/plugin/12754)
[![Downloads](http://phpstorm.espend.de/badge/12754/downloads)](https://plugins.jetbrains.com/plugin/12754)
[![Downloads last month](http://phpstorm.espend.de/badge/12754/last-month)](https://plugins.jetbrains.com/plugin/12754)
[![Donate to this project using Paypal](https://img.shields.io/badge/paypal-donate-yellow.svg)](https://www.paypal.me/DanielEspendiller)


Key         | Value
----------- | -----------
Plugin url  | https://plugins.jetbrains.com/plugin/12754-php-generics
Id          | de.espend.idea.php.generics
Changelog   | [CHANGELOG](CHANGELOG.md)


!!! Work in progress !!!

## Supported

### types

```php
/**
 * @[psalm-|phpstan-]template T
 * @[psalm-|phpstan-]param class-string<T> $class
 * @[psalm-|phpstan-]return T
 */
function instantiator(string $class) {
    return new $class();
}

class Foo {}

$a = instantiator(Foo::class); // Psalm knows the result is an object of type Foo
```

### class-string

 * Inspections for not given wrong parameter

```php
    /**
     * @[psalm-|phpstan-]template T as Exception
     * @[psalm-|phpstan-]param T::class $type
     * @return T
     */
    function a(string $type): Exception
    {
        return new $type;
    }
```

### templates

```php
    $collection = new FooCollection();
    
    // its now a type of "Foobar"
    $foobar = $collection->getValue();
    $foobar->get<caret>Foobar(); // method is clickable and autocompletes
```

```php
    /**
     * @[psalm-|phpstan-]template T
     */
    class Collection
    {
        /**
         * @[psalm-|phpstan-]return T
         */
        public function getValue() {}
    }

    /**
     * @[psalm-|phpstan-]extends Collection<Foobar>
     */
    class FooCollection extends Collection {}

    class Foobar
    {
        public function getFoobar() {}
    }
```

### Object-like arrays

https://psalm.dev/docs/annotating_code/type_syntax/array_types/

```php
    a(['<caret>' => ''])

```

```php
    /**
     * @[psalm-|phpstan-]param array{foo: string, bar: int} $type
     */
    function a(array $type): Exception
    {
    }
```


### psalm-immutable and psalm-readonly

Inspection to show disallowed write access

```
class PsalmReadOnly {
    /**
     * @psalm-readonly
     */
    public string $readOnly;
}

/**
 * @psalm-immutable
 */
class PsalmImmutable {
    public string $readOnly;
}
```

Follows into errors hints

```
(new PsalmReadOnly())->readOnly = 'test';
(new PsalmImmutable())->readOnly = 'test';
```

## Quality Tools

Provides support for quality tools inspection via directly call PHPStan or Psalm reporting via `codestyle` format

## Limitation / Issues

 * Inconsistently PhpStorm docblock parser: https://youtrack.jetbrains.com/issue/WI-47644
 
 ## Screenshots

 ![class-string](https://plugins.jetbrains.com/files/12754/screenshot_20052.png)
 ![Object-like arrays](https://plugins.jetbrains.com/files/12754/screenshot_21124.png)
 ![Psalm Immutable](https://plugins.jetbrains.com/files/12754/screenshot_21166.png)
 ![Quality Tool](https://plugins.jetbrains.com/files/12754/screenshot_21656.png)
 ![Template Types](https://plugins.jetbrains.com/files/12754/screenshot_21688.png)

## TODO

https://youtrack.jetbrains.com/issue/WI-47158

```php
/**
 * @template T
 */
class Map
{
    /**
     * @param array<string, T>
     */
    public function __construct(array $data = []) {}
    /**
     * @return T
     */
    public function get(string $key) {}
    /**
     * @param T $value
     */
    public function set(string $key, $value): void {}
}
// Automatically inferred as Map<string>
$map = new Map([0 => 'Foo', 1 => 'Bar']);
$map->set(2, true); // Expected string
```


https://youtrack.jetbrains.com/issue/WI-45248


```php
    class Assert
    {
        /**
         * @psalm-template ExpectedType of object
         * @psalm-param class-string<ExpectedType> $class
         * @psalm-assert ExpectedType $value
         */
        public static function isInstanceOf($value, $class, $message = '')
        {
        }
    }
```


https://github.com/phpstan/phpdoc-parser/pull/30

```php

/**
* @param array{'foo': int, "bar": string} $a
* @param array{0: int, 1?: int} $a
* @param array{int, int} $a
* @param array{foo: int, bar: string} $a
* @param array{foo:string, bar:?int} $a
*/
```

Others

```php
 /** @var array<int, string> */
 ```
