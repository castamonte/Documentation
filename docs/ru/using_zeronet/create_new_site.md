# Создание нового ZeroNet сайта

## Простой путь: Используя веб-интерфейс

 * Кликните на пункт меню **⋮** > **"Create new, empty site"** на сайте [ZeroHello](http://127.0.0.1:43110/1HeLLo4uzjaLetFx6NH3PMwFP3qbRbTf3D).
 * Вы будете **перенаправлены** на совершенно новый сайт, изменять который можете только Вы!
 * Найти и изменять контент Вашего сайта Вы можете в каталоге **data/[АдресВашегоСайта]**
 * После изменений откройте Ваша сайт, потяните влево кнопку "0" справа вверху, потом нажмите кнопку **Sign and publish** внизу

## Вручную: Используя командную строку

### 1. Создайте структуру сайта

* Остановите ZeroNet если он запущен
* Перейдите в каталог, где установлен ZeroNet и выполните команды:

```bash
$ zeronet.py siteCreate
...
- Site private key: 23DKQpzxhbVBrAtvLEc2uvk7DZweh4qL3fn3jpM3LgHDczMK2TtYUq
- Site address: 13DNDkMUExRf9Xa9ogwPKqp7zyHFEqbhC2
...
- Site created!
$ zeronet.py
...
```

- Это создаст первоначальные файлы Вашего сайта в каталоге ```data/13DNDkMUExRf9Xa9ogwPKqp7zyHFEqbhC2```.

> __Кстати:__
> Пользователям Windows, использующие версию bundle, нужно перейти в каталог ZeroBundle/ZeroNet и выполнить `"../Python/python.exe" zeronet.py siteCreate`

### 2. Написание/Изменение сайта

* Редактируйте фалы сайта, расположенные в ```data/[АдресВашегоСайта]``` (eg: 13DNDkMUExRf9Xa9ogwPKqp7zyHFEqbhC2).
* Когда Ваш сайт будет готов, выполните:

```bash
$ zeronet.py siteSign 13DNDkMUExRf9Xa9ogwPKqp7zyHFEqbhC2
- Signing site: 13DNDkMUExRf9Xa9ogwPKqp7zyHFEqbhC2...
Private key (input hidden):
```

* Введите приватный ключ, который Вы получили при создании сайта. Это подпишет все файлы и пиры смогут удостовериться, что изменения внёс именно владелец сайта.

### 3. Опубликуйте изменения сайта

* Чтобы известить пиров об изменении сайта Вам надо выполнить:

```bash
$ zeronet.py sitePublish 13DNDkMUExRf9Xa9ogwPKqp7zyHFEqbhC2
...
Site:13DNDk..bhC2 Publishing to 3/10 peers...
Site:13DNDk..bhC2 Successfuly published to 3 peers
- Serving files....
```

* Это всё! Вы полностью подписали и опубликовали свои изменения.
* Ваш сайт теперь доступен по адресу: ```http://localhost:43110/13DNDkMUExRf9Xa9ogwPKqp7zyHFEqbhC2```


**Дальнейшие шаги:** [ZeroNet Developer Documentation](../../site_development/getting_started/)
