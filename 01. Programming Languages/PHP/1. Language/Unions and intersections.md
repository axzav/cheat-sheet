
```php
use Symfony\Component\Serializer\Normalizer\DenormalizerInterface;
use Symfony\Component\Serializer\Normalizer\NormalizerInterface;
use Symfony\Component\Serializer\SerializerInterface;

class DataFormatter
{
    public function __construct(
        private (NormalizerInterface&DenormalizerInterface)|SerializerInterface $transformer,
    ) {
        // ...
    }

    // ...
}
```