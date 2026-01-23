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

## ECS

```
      # - name: Trigger ECS deployment
      #   run: |
      #     echo "Triggering new deployment for Laravel service..."
      #     aws ecs update-service \
      #       --cluster ${{ env.ECS_CLUSTER }} \
      #       --service ${{ env.LARAVEL_ECS_SERVICE }} \
      #       --force-new-deployment \
      #       --region ${{ env.AWS_REGION }} \
      #       --no-cli-pager

      # - name: Wait for deployment completion
      #   run: |
      #     echo "Waiting for service to reach steady state..."
      #     echo "This may take a few minutes as ECS:"
      #     echo "  1. Starts new tasks with updated images"
      #     echo "  2. Waits for ALB health checks to pass"
      #     echo "  3. Drains connections from old tasks"
      #     echo "  4. Terminates old tasks"
      #     echo ""

      #     aws ecs wait services-stable \
      #       --cluster ${{ env.ECS_CLUSTER }} \
      #       --services ${{ env.LARAVEL_ECS_SERVICE }} \
      #       --region ${{ env.AWS_REGION }}

      #     echo "Laravel service deployment completed successfully!"


      # - name: Trigger ECS deployment
      #   run: |
      #     echo "Triggering new deployment for MODX service..."
      #     aws ecs update-service \
      #       --cluster ${{ env.ECS_CLUSTER }} \
      #       --service ${{ env.MODX_ECS_SERVICE }} \
      #       --force-new-deployment \
      #       --region ${{ env.AWS_REGION }} \
      #       --no-cli-pager

      # - name: Wait for deployment completion
      #   run: |
      #     echo "Waiting for service to reach steady state..."
      #     echo "This may take a few minutes as ECS:"
      #     echo "  1. Starts new tasks with updated images"
      #     echo "  2. Waits for ALB health checks to pass"
      #     echo "  3. Drains connections from old tasks"
      #     echo "  4. Terminates old tasks"
      #     echo ""

      #     aws ecs wait services-stable \
      #       --cluster ${{ env.ECS_CLUSTER }} \
      #       --services ${{ env.MODX_ECS_SERVICE }} \
      #       --region ${{ env.AWS_REGION }}

      #     echo "MODX service deployment completed successfully!"

```
