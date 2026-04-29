# Лабораторная работа №1

**University:** [ITMO University](https://itmo.ru/ru/)  
**Faculty:** [FICT](https://fict.itmo.ru)  
**Course:** [Cloud platforms as the basis of technology entrepreneurship](https://)  
**Year:** 2025/2026  
**Group:** U4125  
**Author:** Исайкин Евгений Дмитриевич  
**Lab:** Lab1  
**Date of create:** 28.04.2026  
**Date of finished:**  

---

## Цель работы

Ознакомиться с основными возможностями и преимуществами облачной платформы Google Cloud, а также на практике изучить работу с IAM, service account, Compute Engine и Cloud Storage.

## Ход работы

### 1. Подготовка проекта Google Cloud

Для выполнения лабораторной работы был использован учебный проект Google Cloud `cloud-platforms-as-the-basis`. После получения доступа к проекту была открыта Google Cloud Console.

<img width="1147" height="50" alt="image" src="https://github.com/user-attachments/assets/a30cf3c7-a014-499d-906f-5f2db3487f43" />

### 2. Создание service account

В разделе **IAM & Admin → Service Accounts** был создан service account для выполнения лабораторной работы. В соответствии с требованием по неймингу использовалось имя `eisaikin-sa-lab1`, где `e` — первая буква имени, `isaikin` — фамилия, а `sa-lab1` — указание на service account для первой лабораторной работы.

<img width="607" height="588" alt="image" src="https://github.com/user-attachments/assets/94e9eb4f-5c21-43da-b94a-7b642bacd8a8" />

После создания service account ему была назначена роль **Storage Admin**. Эта роль предоставляет права на управление ресурсами Cloud Storage и была необходима для первичного доступа к bucket и копирования файлов.

<img width="745" height="198" alt="image" src="https://github.com/user-attachments/assets/a72451a5-45eb-4a58-a4e7-5cda93c169d4" />

### 3. Создание виртуальной машины Compute Engine

Далее была создана виртуальная машина в сервисе **Compute Engine**. Для нее было задано имя `eisaikin-vm-lab1` согласно требуемому шаблону нейминга. В качестве типа машины был выбран **e2-micro**, а режим предоставления ресурсов был установлен как **Spot**.

<img width="762" height="239" alt="image" src="https://github.com/user-attachments/assets/327c8d90-c940-4e3d-9381-5f438c0b5d2c" />

<img width="748" height="120" alt="image" src="https://github.com/user-attachments/assets/c41b777a-64f7-4675-a647-be05e2062212" />

<img width="807" height="52" alt="image" src="https://github.com/user-attachments/assets/c24f98aa-7852-4c63-8a1b-c0755e3ae0f4" />

На первом этапе VM была создана со стандартным Compute Engine service account. При проверке внутри SSH-сессии было видно, что активной учетной записью является default service account вида `...compute@developer.gserviceaccount.com`. Это означало, что изменение прав у `eisaikin-sa-lab1` не влияло бы на поведение VM, поэтому конфигурация была исправлена.

<img width="1035" height="221" alt="image" src="https://github.com/user-attachments/assets/336f26ec-0c8a-4b87-a35c-557eca9c334f" />

VM была пересоздана так, чтобы в настройках **Identity and API access** использовался service account `eisaikin-sa-lab1`. 

<img width="883" height="332" alt="image" src="https://github.com/user-attachments/assets/230001e8-b4b2-4c65-8bb3-50d658b2c8bd" />

После исправления VM работала от имени `eisaikin-sa-lab1@cloud-platforms-as-the-basis.iam.gserviceaccount.com`. Это важно, так как дальнейшая проверка прав доступа должна выполняться именно от имени созданного service account.

### 4. Поиск bucket и копирование файлов на VM

С помощью утилиты `gcloud` был выполнен просмотр содержимого bucket `lab1-bucket-itmo`:

```bash
gcloud storage ls gs://lab1-bucket-itmo
```

В результате были найдены три файла: `pic1.jpg`, `pic2.jpg` и `pic3.jpeg`.

<img width="406" height="101" alt="image" src="https://github.com/user-attachments/assets/8c550886-bcfb-4139-a96e-a95904583798" />

Для хранения файлов на виртуальной машине была создана локальная директория:

```bash
mkdir -p ~/lab1-files
```

<img width="606" height="83" alt="image" src="https://github.com/user-attachments/assets/06bd830b-cc47-4014-9c85-a06b34821888" />

Затем три файла были скопированы из Cloud Storage в локальную папку на VM:

```bash
gcloud storage cp gs://lab1-bucket-itmo/pic1.jpg ~/lab1-files/
gcloud storage cp gs://lab1-bucket-itmo/pic2.jpg ~/lab1-files/
gcloud storage cp gs://lab1-bucket-itmo/pic3.jpeg ~/lab1-files/
```

<img width="895" height="425" alt="image" src="https://github.com/user-attachments/assets/2227c0e8-7c2c-4013-90f0-678dea411a20" />

Для проверки наличия файлов на виртуальной машине была использована команда:

```bash
ls -lah ~/lab1-files
```

Команда показала, что все три файла находятся в локальной директории VM.

<img width="588" height="477" alt="image" src="https://github.com/user-attachments/assets/28e4822a-4dea-4820-9467-bb637cd20b30" />

### 5. Изменение прав service account

После успешного копирования файлов права service account были изменены: роль **Storage Admin** была удалена, а вместо нее была назначена роль **Compute Viewer**.

<img width="821" height="53" alt="image" src="https://github.com/user-attachments/assets/e01268ef-7b0a-456f-887c-ec6836b0f611" />

<img width="879" height="189" alt="image" src="https://github.com/user-attachments/assets/b64e6803-5597-4978-a68d-1164a99af698" />

Роль **Compute Viewer** предназначена для просмотра ресурсов Compute Engine. Она не должна предоставлять административные права на Cloud Storage и не включает управление IAM-policy bucket.

### 6. Повторная проверка доступа к bucket

После изменения роли была выполнена повторная попытка обращения к bucket и копирования объекта. Ожидалось, что доступ к объектам Cloud Storage будет ограничен, так как у service account больше нет роли **Storage Admin**.

<img width="725" height="214" alt="image" src="https://github.com/user-attachments/assets/d423c1c8-4a25-4df5-8aed-74185dafa758" />

Однако повторное копирование файла продолжило выполняться успешно. Для анализа причины была проверена политика доступа bucket через интерфейс Google Cloud. Было обнаружено, что для bucket `lab1-bucket-itmo` настроено отдельное разрешение `allUsers: Storage Object Viewer`.

<img width="1178" height="50" alt="image" src="https://github.com/user-attachments/assets/988f228a-4cbe-4ad6-ad24-18c7dcbc9278" />
<img width="1152" height="57" alt="image" src="https://github.com/user-attachments/assets/913ff6ee-d616-4ae5-9433-de0e77390cc4" />
Это означает, что объекты bucket доступны публично для чтения. Поэтому операция копирования файлов могла выполняться даже после удаления роли **Storage Admin** у моего service account.

Для дополнительной проверки была выполнена попытка получить IAM-policy bucket командой:

```bash
gcloud storage buckets get-iam-policy gs://lab1-bucket-itmo
```
<img width="1448" height="78" alt="image" src="https://github.com/user-attachments/assets/d6abd9b6-7ea8-4a45-9296-00e01e62f057" />
Команда завершилась ошибкой доступа. Это подтверждает, что после смены роли на **Compute Viewer** service account потерял административные права на Cloud Storage и не может просматривать IAM-policy bucket через `gcloud`.

<img width="1204" height="591" alt="image" src="https://github.com/user-attachments/assets/7d343950-0b90-4228-ba09-4ccd3925fd83" />

Также была выполнена попытка получить IAM-policy проекта. Эта операция также завершилась ошибкой доступа, так как у service account отсутствовали необходимые разрешения для просмотра политики проекта.

### 7. Удаление созданных ресурсов

После завершения проверки созданная виртуальная машина была остановлена и удалена. Также был удален service account, созданный для выполнения лабораторной работы.

## Выводы

В ходе лабораторной работы были изучены базовые сервисы Google Cloud: IAM, Service Accounts, Compute Engine и Cloud Storage. Был создан service account `eisaikin-sa-lab1` с ролью **Storage Admin**, а также виртуальная машина `eisaikin-vm-lab1` с типом **e2-micro** в режиме **Spot**.

Отдельно была выявлена и исправлена ошибка конфигурации: первая версия VM использовала стандартный Compute Engine default service account. После проверки через metadata server VM была настроена на использование созданного service account `eisaikin-sa-lab1`. Это позволило корректно проверить влияние изменения IAM-ролей на доступ к ресурсам.

С помощью утилиты `gcloud` был найден bucket `lab1-bucket-itmo`, после чего три файла были скопированы из Cloud Storage в локальную директорию на виртуальной машине. Наличие файлов было подтверждено командой `ls -lah`.

После смены роли service account с **Storage Admin** на **Compute Viewer** повторное копирование файлов продолжило выполняться успешно. Причина заключалась в том, что на уровне bucket было настроено отдельное публичное разрешение `allUsers: Storage Object Viewer`. Это разрешение давало возможность читать объекты bucket независимо от роли, назначенной конкретному service account на уровне проекта.

При этом попытки получить IAM-policy bucket и IAM-policy проекта завершились ошибкой доступа. Это подтвердило, что административные права на Cloud Storage после смены роли были утрачены. Таким образом, лабораторная работа показала, что фактический доступ к ресурсам Google Cloud определяется совокупностью IAM-политик на разных уровнях: проекта, bucket и отдельных объектов.
