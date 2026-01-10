# Random commands

```
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 858409149719.dkr.ecr.us-east-1.amazonaws.com
```

## Installing Dependencies in PHP application

* Running compose validate in PHP application, only after running composer install
*   The standard is to have:<br>

    ```php
    COPY composer.json composer.lock* ./
    RUN composer install \
      && composer validate
    ```

