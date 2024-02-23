
# Тестовое задание PHP Laravel разработчика


**Назначение:** Встраиваемая форма для сайта
**Формат API:** JSON-RPC v2.0 [[Json RPC]]
**Описание:** Необходимо реализовать систему состоящую из двух частей: первая часть - сайт
data на котором есть редактор форм (как в гугл формах, можно
добавлять/редактировать/удалять поля).
Набор типов полей формы ограничить до, input, textarea, select.

Вторая часть - отдельный сайт site на котором нужно вывести созданную форму через API с первого сайта (data) также, на втором сайте должна присутствовать страница на которой отображаются сохраненные данные формы через API с первого сайта.

Далее немного подробнее:
* Структура сервиса data (api):
Сервис data (это редактор форм) – это отдельный сайт, он состоит из страницы редактора форм на котором можно задать типы поля, название и описание поля для формы, а также
задать название самой формы. После сохранения форма имеет свой уникальный form_uid присвоенный редактором при сохранении, и дату создания формы. Также есть отдельная страница на которой отображаются данные переданные в форму на другом сайте через api.
* Структура основного сайта (site):
Сайт – это отдельный проект, должен содержать class для работы с API сервиса (data) по протоколу json-rpc. В классе вам необходимо реализовать три метода:
- Получение полей формы по его form_uid и отображении его на странице сайта.
- Отправка заполненных данных формы через api на сервис data для сохранения этих данных там в базу .
- Получение сохраненных данных формы с сервиса data по form_uid, для дальнейшего
отображения на отдельной странице сайта.
Class должен быть независимым и переносимым на другие ресурсы.
Для демонстрации работы задания необходимо развернуть 2 сайта, на одном сайте будет сервис data, на другом страница с формой и страница с данными формы.
Требований по оформлению нет, все как душе угодно.
Для проверки и оценки результата необходимо предоставить:
- Ссылки на рабочую версию data
- Ссылки на рабочую версию site
- Ссылку на GIT репозиторий для проверки кода
- Описание структурной схемы проекта (xmind)
- Описание работы и методов подключения
- Dump базы данных. 

**Уделяйте внимание качеству кода, который вы пишите, а также стилистическому
оформлению. Удачи!**