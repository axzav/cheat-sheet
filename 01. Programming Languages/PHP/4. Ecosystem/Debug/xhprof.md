# Xhprof

```php
xhprof_enable(XHPROF_FLAGS_CPU | XHPROF_FLAGS_MEMORY);

//code to profile

$xhprof_data = xhprof_disable();

//send profile to buggregator
$requestData = [
    'profile' => $xhprof_data,
    'tags' => '',
    'app_name' => 'Test app',
    'hostname' => \gethostname(),
    'date' => (new \DateTime())->getTimestamp(),
];

$ch = curl_init('http://profiler@buggregator:8000');
$payload = json_encode($requestData);
curl_setopt( $ch, CURLOPT_POSTFIELDS, $payload );
curl_setopt( $ch, CURLOPT_HTTPHEADER, array('Content-Type:application/json'));
curl_setopt( $ch, CURLOPT_RETURNTRANSFER, true );
$result = curl_exec($ch);
curl_close($ch);
```


https://pecl.php.net/package/xhprof
https://github.com/longxinH/xhprof


https://github.com/perftools/xhgui - gui