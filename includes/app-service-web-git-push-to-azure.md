## <a name="push-to-azure-from-git"></a>Публикация в Azure из Git

Добавьте удаленную службу приложений Azure в локальный репозиторий Git.

```bash
git remote add azure <URI from previous step>
```

Отправьте код в удаленную службу приложений Azure, чтобы развернуть приложение. После этого введите пароль, указанный ранее при создании пользователя развертывания. Убедитесь, что вы ввели пароль, созданный в разделе о [настройке пользователя развертывания](#configure-a-deployment-user), а не тот, что использовался для входа на портал Azure.

```bash
git push azure master
```

Предыдущая команда выводит сведения, подобные следующим:
