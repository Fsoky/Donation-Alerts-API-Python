# Donation Alerts API Python
__Модуль для Python, который позволит легко работать с Donation Alerts API__

__pip install donationalerts-api -U__

[Создать свое приложение Donation Alerts](https://www.donationalerts.com/application/clients) \
[Официальная документация Donation Alerts API](https://www.donationalerts.com/apidoc)

![DA API](https://github.com/Fsoky/Donation-Alerts-API-Python/blob/main/images/logo-api.jpg)

## 🔥 Простой пример работы
В этом примере мы логинимся в нашем приложении с определенными правами, получаем _access_token_, после в пременной `user` мы получаем JSON-объект, в котором содержится информация, в переменной `donations` тоже хранится информация, только уже другая. И теперь возвращаем нашу переменную `user`

`DonationAlertsApi` - основной класс для работы с DA API, на вход принимает _client_id_, _client_secret_, _redirect_uri_, _scopes_ \
`Scopes` - позволит вам передать ряд прав в удобном формате, все права можете посмотреть в [оф. документации](https://www.donationalerts.com/apidoc#authorization__scopes), также имеет атрибут _ALL_SCOPES_ для передачи всех прав сразу (Scopes.ALL_SCOPES)

```py
from flask import Flask, redirect, request

from donationalerts_api import DonationAlertsApi
from donationalerts_api.modules import Scopes

app = Flask(__name__)
api = DonationAlertsApi("client id", "client secret", "http://127.0.0.1:5000/login", [Scopes.USER_SHOW, Scopes.DONATION_INDEX])


@app.route("/", methods=["get"])
def index():
	return redirect(api.login()) # Log in your application


@app.route("/login", methods=["get"])
def login():
	code = request.args.get("code") # Получить нужный код для access token
	access_token = api.get_access_token(code)

	user = api.user(access_token)
	donations = api.donations_list(access_token) # Получить список донатов

	return user.objects # Возвращает JSON object

if __name__ == "__main__":
	app.run(debug=True)
```

## 💖 Получение донатов в реальном времени без Oauth2
Здесь мы легко можем получить донат в реальном времени, для этого всего лишь нужно токен и пару строчек кода. Токен вы можете скопировать в [Основных настройках](https://www.donationalerts.com/dashboard/general). Теперь при новом донате, вы получите JSON-объект, который можете использовать уже сами.

![ТОКЕН](https://github.com/Fsoky/Donation-Alerts-API-Python/blob/main/images/example_alert_2.png)

```py
from donationalerts_api import Alert

alert = Alert("token")

@alert.event()
def new_donation(event):
    """ Пример обращения
    event.username - получает никнейм донатера
    event.objects - вернуть JSON object
    """
	print(event) # Выведет все доступные атрибуты, к которым можно обратиться
```

Все примеры вы можете посмотреть в папке [Examples](https://github.com/Fsoky/Donation-Alerts-API-Python/tree/main/examples) \
__[Donation Alerts API Python - небольшой обзор](https://www.youtube.com/watch?v=ZJVVDRNR9Vw)__ - в ролике, автор рассказывает о первой версии, возможно кому-то будет интересно \
__[Donation Alerts API Python - получение событий в реальном времени](https://www.youtube.com/watch?v=pAdPuScKSNs)__ - небольшая история обновлений и демонстрация новых возможностей
