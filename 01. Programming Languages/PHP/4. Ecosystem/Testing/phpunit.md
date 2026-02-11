# Coverage
Создает отчет о покрытии тестами в формате html. Создаст папку coverage-report с html файлами которые можно открыть локально.
Должен быть установлен xdebug.mode=coverage

```bash
php bin/phpunit --coverage-html coverage-report
XDEBUG_MODE=coverage php bin/phpunit --coverage-html coverage-report
```



```sh
php bin/phpunit --filter testIsValid src/Common/tests/Domain/MFA/Authenticator/EmailAuthenticatorTest.php

php bin/phpunit src/Common/tests/Domain/MFA/Authenticator/EmailAuthenticatorTest.php


php bin/phpunit src/Component/Profile/tests/Domain/Account/Reader/ValidateSubscriptionReaderTest.php


XDEBUG_TRIGGER=1 php bin/phpunit --filter test fail with invalid key account information tests/ABTasty/CoreBundle/Filter/AccountInformationFilterTest.php


XDEBUG_TRIGGER=1 php bin/phpunit --filter test_value tests/ABTasty/AudienceBundle/Validator/Constraints/DatalayerValueValidatorTest.php

```

```php
        $this->authenticator = $this->getMockBuilder(TotpAuthenticator::class)
            ->setConstructorArgs([$this->cache])
            ->onlyMethods(['isValid'])
            ->getMock()
        ;
```