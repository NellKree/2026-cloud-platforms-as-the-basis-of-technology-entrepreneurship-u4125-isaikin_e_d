# Лабораторная работа №3

**University:** [ITMO University](https://itmo.ru/ru/)  
**Faculty:** [FICT](https://fict.itmo.ru)  
**Course:** [Cloud platforms as the basis of technology entrepreneurship](https://)  
**Year:** 2025/2026  
**Group:** U4125  
**Author:** Исайкин Евгений Дмитриевич  
**Lab:** Lab3  
**Date of create:**  
**Date of finished:**  

---

## Цель работы

Ознакомиться с основными принципами работы Google Cloud Storage: созданием bucket, загрузкой объектов, организацией файлов внутри bucket, настройкой публичного доступа и получением ссылок на файлы.

## Ход работы

### 1. Выбор проекта и переход в Cloud Storage

Для выполнения лабораторной работы был выбран существующий проект Google Cloud, в котором были предоставлены необходимые права доступа. После выбора проекта был открыт раздел **Cloud Storage**, предназначенный для работы с объектным хранилищем.

<img width="919" height="491" alt="image" src="https://github.com/user-attachments/assets/429f59f5-a8c6-4f2d-9a8f-49b3875d0cfd" />

### 2. Создание Cloud Storage bucket

В разделе **Cloud Storage → Buckets** был создан новый bucket. Для лабораторной работы использовалось имя:

```text
lab3-eisaikin-bucket
```

Остальные параметры были оставлены близкими к стандартным настройкам, так как в рамках работы требовалось проверить базовый сценарий использования Cloud Storage.

<img width="926" height="281" alt="image" src="https://github.com/user-attachments/assets/337a437b-ebb1-4539-91e9-1c77fe451cc9" />

После подтверждения настроек bucket был успешно создан и появился в списке хранилищ проекта.

<img width="1188" height="779" alt="image" src="https://github.com/user-attachments/assets/ce79a78c-bc02-4a66-aee6-e085326af2f2" />

### 3. Загрузка изображений в bucket

В созданный bucket были загружены четыре изображения. После загрузки файлы отобразились в интерфейсе Cloud Storage как отдельные объекты.

<img width="499" height="235" alt="image" src="https://github.com/user-attachments/assets/1f9c1a98-232e-45e9-8b26-bf5da193c9f1" />

### 4. Создание папки и перемещение файлов

Для организации объектов внутри bucket была создана папка:

```text
lab3-images
```

После этого загруженные изображения были перемещены в созданную папку. Важно учитывать, что Cloud Storage является объектным хранилищем: папки в нем являются не классическими директориями файловой системы, а логическими префиксами в именах объектов.

<img width="537" height="324" alt="image" src="https://github.com/user-attachments/assets/258782f1-9c2b-48e3-8756-8f795553dfc7" />

<img width="832" height="299" alt="image" src="https://github.com/user-attachments/assets/84b61831-05c9-4880-9e9e-6f2671db8142" />

После перемещения изображения стали доступны внутри папки `lab3-images`.

### 5. Настройка публичного доступа

Следующим шагом была выполнена настройка публичного доступа к объектам. Для этого в настройках доступа bucket была предпринята попытка добавить principal:

```text
allUsers
```

с ролью:

```text
Storage Object Viewer
```

На первом этапе возникла ошибка обновления IAM policy. Причина заключалась в том, что для bucket был включен механизм **Public Access Prevention**, запрещающий добавление публичного доступа через `allUsers` или `allAuthenticatedUsers`.

<img width="598" height="793" alt="image" src="https://github.com/user-attachments/assets/60ee5b35-1900-4d7e-9544-31a8514cec27" />

После этого была изменена настройка ограничения публичного доступа. Ограничение было отключено для данного bucket, что позволило продолжить настройку прав доступа.

<img width="466" height="237" alt="image" src="https://github.com/user-attachments/assets/739a3d87-dfd0-45f6-a90a-c729ccc8f90d" />

После отключения ограничения публичного доступа был добавлен principal `allUsers` с ролью `Storage Object Viewer`. Эта роль разрешает публичное чтение объектов, но не дает прав на управление bucket.

<img width="542" height="506" alt="image" src="https://github.com/user-attachments/assets/e311e5d0-cb31-40e6-ba78-a2b47582b25e" />

В результате файлы, размещенные в bucket, стали доступны по публичным ссылкам.

https://storage.googleapis.com/lab3-eisaikin-bucket/lab3-images/image1.jpg

### 6. Получение ссылок на изображения

Для файлов были получены публичные ссылки через контекстное меню объекта. Полученные ссылки имеют следующий вид:

```text
https://storage.googleapis.com/lab3-eisaikin-bucket/lab3-images/image1.jpg
https://storage.googleapis.com/lab3-eisaikin-bucket/lab3-images/image2.jpg
https://storage.googleapis.com/lab3-eisaikin-bucket/lab3-images/image3.jpg
https://storage.googleapis.com/lab3-eisaikin-bucket/lab3-images/image4.jpg
```

Для проверки публичного доступа ссылки были открыты в браузере. Изображения успешно загружались без дополнительной авторизации в Google Cloud.

### 7. Удаление ресурсов

После завершения проверки созданные ресурсы были удалены. Удаление bucket и объектов необходимо для того, чтобы не оставлять лишние ресурсы в облачной среде и не создавать потенциальные риски, связанные с публичным доступом.

## Вывод

В ходе лабораторной работы были изучены базовые возможности Google Cloud Storage. Был создан bucket, загружены изображения, создана логическая папка для организации объектов, а файлы были перемещены внутрь этой папки.

Также была изучена настройка публичного доступа. При первой попытке добавить `allUsers` возникла ошибка из-за включенного механизма Public Access Prevention. После отключения ограничения для bucket удалось выдать роль `Storage Object Viewer` для `allUsers`, благодаря чему изображения стали доступны по публичным ссылкам.

В результате работы было подтверждено, что Cloud Storage является объектным хранилищем, где доступ к объектам управляется через IAM-политики и настройки публичного доступа. Дополнительно был закреплен практический навык удаления созданных ресурсов после завершения работы.
<img width="537" height="324" alt="image" src="https://github.com/user-attachments/assets/258782f1-9c2b-48e3-8756-8f795553dfc7" />
<img width="832" height="299" alt="image" src="https://github.com/user-attachments/assets/84b61831-05c9-4880-9e9e-6f2671db8142" />
<img width="454" height="283" alt="image" src="https://github.com/user-attachments/assets/aa52f4f1-b2c7-416a-be33-984b9105b785" />
<img width="598" height="793" alt="image" src="https://github.com/user-attachments/assets/60ee5b35-1900-4d7e-9544-31a8514cec27" />
<img width="466" height="237" alt="image" src="https://github.com/user-attachments/assets/739a3d87-dfd0-45f6-a90a-c729ccc8f90d" />
<img width="542" height="506" alt="image" src="https://github.com/user-attachments/assets/e311e5d0-cb31-40e6-ba78-a2b47582b25e" />
https://storage.googleapis.com/lab3-eisaikin-bucket/lab3-images/image1.jpg
<img width="715" height="849" alt="image" src="https://github.com/user-attachments/assets/df20baae-8f65-4a78-8fd6-22ba432e087d" />
https://storage.googleapis.com/lab3-eisaikin-bucket/lab3-images/image2.jpg
<img width="724" height="856" alt="image" src="https://github.com/user-attachments/assets/a5075dec-4808-4592-adf2-f6fd8fe76ce4" />
