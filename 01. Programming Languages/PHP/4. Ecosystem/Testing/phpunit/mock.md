```php
        $oauth2Mock = $this->getMockBuilder(OAuth2::class)
            ->setConstructorArgs([
                $this->tokenStorage,
                $userRepositoryLocator,
                $this->googleClient,
                $clientRepositoryLocator,
                '',
                '',
            ])
            ->onlyMethods(['getRequiredScopes', 'validateRedirectUri'])
            ->getMock()
        ;

```