### Настройка панели навигации с разграничением прав

#### 1. Изменение меню навигации

Откройте файл `views/layouts/main.php` (или другой ваш основной layout-файл). Найдите секцию с меню измените:

```php
NavBar::begin([
        'brandLabel' => 'Мой Не Сам',
        'brandUrl' => Yii::$app->homeUrl,
        'options' => ['class' => 'navbar-expand-md navbar-dark bg-dark fixed-top']
    ]);
    echo Nav::widget([
        'options' => ['class' => 'navbar-nav'],
        'items' => [
            // Проверка на обычного пользователя
            Yii::$app->user->identity->role_id === 1 ? ['label' => 'Мои заявки', 'url' => ['/request/index']] : '',
            //Проверка на админа
            Yii::$app->user->identity->role_id === 2 ? ['label' => 'Админка', 'url' => ['/admin/index']] : '',
            Yii::$app->user->isGuest
                ? ['label' => 'Войти', 'url' => ['/site/login']]
                : '<li class="nav-item">'
                    . Html::beginForm(['/site/logout'])
                    . Html::submitButton(
                        'Выйти (' . Yii::$app->user->identity->username . ')',
                        ['class' => 'nav-link btn btn-link logout']
                    )
                    . Html::endForm()
                    . '</li>'
        ]
    ]);
    NavBar::end();
```
![image](https://github.com/user-attachments/assets/93e40286-b4f5-4c7b-a1b7-82bc0362cba3)
*Обычный пользователь*


![image](https://github.com/user-attachments/assets/77c5ed8b-3afd-4e55-b8c4-9a7ee297eb8a)
*Администратор*


#### 2. Фильтрация для пользователя

Модифицируем `models/RequestSearch.php`:

```php
public function search($params)
{
    $query = Request::find();

    //Если обычный пользователь выводим только его заявки
     if (Yii::$app->user->identity->role_id === 1) {
            $query->andWhere(['user_id' => Yii::$app->user->id]);
      }

    // ... остальной код ...
}
```

### Итог

Теперь система имеет:
1. Разные меню навигации для пользователей и администраторов
2. Фильтрацию заявок для обычных пользователей
3. Полноценную админ-панель

