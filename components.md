# Компонентная архитектура
<!-- Состав и взаимосвязи компонентов системы между собой и внешними системами с указанием протоколов, ключевые технологии, используемые для реализации компонентов.
Диаграмма контейнеров C4 и текстовое описание. 
-->
## Компонентная диаграмма

```plantuml
@startuml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

AddElementTag("microService", $shape=EightSidedShape(), $bgColor="CornflowerBlue", $fontColor="white", $legendText="microservice")
AddElementTag("storage", $shape=RoundedBoxShape(), $bgColor="lightSkyBlue", $fontColor="white")

Person(admin, "Администратор")
Person(security, "Безопасник")
Person(user, "Пользователь")

System_Ext(temp,"Датчик температуры")
System_Ext(door,"Датчик входа")

System_Boundary(smart_house, "Умный дом") {
   Container(app, "Клиентское веб-приложение", "html, JavaScript, Angular", "Портал умного дома")
   Container(client_service, "Сервис авторизации", "C++", "Сервис управления пользователями", $tags = "microService")    
   Container(door_service, "Сервис входов", "C++", "Сервис управления информацией о входе/выходе", $tags = "microService") 
   Container(temp_service, "Сервис погоды", "C++", "Сервис управления информацией о погоде", $tags = "microService")       
   ContainerDb(db, "Данные умного дома", "MySQL", "Хранение данных конференции", $tags = "storage")
   
}

Rel_D(admin, app, "Добавление/просмотр информации о пользователях")
Rel_D(security, app,"Просмотр информации о прозодах")
Rel_D(user, app,"Просмотр информации о температуре")

Rel(app,client_service,"Создание пользователя","http://localhost/person ")
Rel(client_service,db,,"INTSERT/SELECT/UPDATE","SQL")

Rel(app,door_service,"Работа с проходами","http://localhost/door ")
Rel_L(door_service,door,"Запрос информации о проходах")
Rel(door_service,db,,"INTSERT/SELECT/UPDATE","SQL")

Rel(app,temp_service,"Работа с температурой","http://localhost/еуьз ")
Rel_R(temp_service,temp,"Запрос текущей температуры")
Rel(temp_service,db,"INTSERT/SELECT/UPDATE","SQL")


@enduml
```
## Список компонентов
### Сервис авторизации


**API**:
-	Создание нового пользователя
      - входыне параметры: login, Имя, Фамилия, email, обращение (гн/гжа)
      - выходные параметры, отсутствуют
-	Поиск пользователя по логину
     - входные параметры:  login
     - выходные параметры: Имя, Фамилия, email, обращение (гн/гжа)
-	Поиск пользователя по маске имя и фамилии
     - входные параметры: маска фамилии, маска имени
     - выходные параметры: login, Имя, Фамилия, email, обращение (гн/гжа)

### Сервис входов
**API**:
- Сохранение информации о проходе
  - Входные параметры: Дата прохода, Направление прохода (вход/выход)
  - Выходыне параметры: отсутствуют
- Получение информации о совершенных проходах
  - Входные параметры: диапазон дат
  - Выходные параметры: массив пар время - направление прохода


### Сервис температуры
**API**:
- Сохранение информации о измерениях температуре
  - Входные параметры:  время замера, температура в цельсиях
  - Выходные параметры: отсутствуют
- Получение информации о измерениях температуры
  - Входные параметры: диапазон дат
  - Выходные параметры: массив пар время - температура
