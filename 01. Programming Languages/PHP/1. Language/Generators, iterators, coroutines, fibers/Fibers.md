# Fiber

Файбер — волокно. Тред - нить. Нить состоит из волокон.

Файбер это специализированный класс для создания корутин, который дает более удобный синтаксис и содержит собственный стек вызовов


```php
$fiber = new Fiber(function (): void {
  $value = Fiber::suspend('fiber');
  echo "Value used to resume fiber: ", $value, "\n";
});

$value = $fiber->start();

echo "Value from fiber suspending: ", $value, "\n";

$fiber->resume('test');
```

```bash
Value from fiber suspending: fiber
Value used to resume fiber: test
```

## Links
- https://www.slideshare.net/chtalbert/from-generator-to-fiber-the-road-to-coroutine-in-php - слайд по корутинам, генераторам и файберам
- https://wiki.php.net/rfc/fibers
- https://journal.stuffwithstuff.com/2015/02/01/what-color-is-your-function/

