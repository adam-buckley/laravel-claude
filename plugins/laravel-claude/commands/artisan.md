---
name: artisan
description: "Run common Laravel Artisan commands quickly. Use /artisan make:model, /artisan migrate, etc."
---

# Artisan Command Helper

You help users run Laravel Artisan commands efficiently.

## Common Commands

When the user provides an artisan command, execute it. If they just say `/artisan` without arguments, show them the most common commands:

### Make Commands
```bash
php artisan make:model ModelName -mfc    # Model with migration, factory, controller
php artisan make:controller NameController --resource
php artisan make:request StoreNameRequest
php artisan make:middleware MiddlewareName
php artisan make:policy NamePolicy --model=Name
php artisan make:event NameEvent
php artisan make:listener NameListener --event=NameEvent
php artisan make:job NameJob
php artisan make:mail NameMail --markdown=emails.name
php artisan make:notification NameNotification
php artisan make:rule NameRule
php artisan make:command NameCommand
php artisan make:test NameTest          # Feature test
php artisan make:test NameTest --unit   # Unit test
```

### Database Commands
```bash
php artisan migrate
php artisan migrate:fresh --seed
php artisan migrate:rollback
php artisan migrate:status
php artisan db:seed
php artisan db:seed --class=SpecificSeeder
```

### Cache Commands
```bash
php artisan cache:clear
php artisan config:clear
php artisan config:cache
php artisan route:clear
php artisan route:cache
php artisan view:clear
php artisan view:cache
php artisan optimize:clear   # Clear all caches
```

### Queue Commands
```bash
php artisan queue:work
php artisan queue:listen
php artisan queue:restart
php artisan queue:failed
php artisan queue:retry all
```

### Other Useful Commands
```bash
php artisan route:list
php artisan tinker
php artisan serve
php artisan storage:link
php artisan schedule:run
php artisan schedule:list
```

## Execution

When the user provides a command like `/artisan migrate`, run:
```bash
php artisan migrate
```

Always show the output of the command to the user.
