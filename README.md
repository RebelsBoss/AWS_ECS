**Початкове знайомство з AWS ECS через веб-інтерфейс. Також окремим yaml файлом створення цієї інфраструктури у AWS CloudFormation.**

# 1. **Створити VPC, subnet, igw, route table та security groups.**

Вибираємо необхідний нам регіон. Заходимо до [VPC](https://eu-central-1.console.aws.amazon.com/vpcconsole/home?region=eu-central-1#Home:). Та створюємо мережу, підмережі, доступ до інтернету та все прописуємо у маршрутах.  
Вибираємо **“VPC only”** друга опція має переваги, але вона коштує додаткових грошей. Також пишемо наш **CIDR** для мережі.

![Screenshot 2024-09-17 163322](https://github.com/user-attachments/assets/4e598219-3e5d-4962-b2c3-54959ad7cd30)

Обираємо мережу, та [додаємо підмережі](https://eu-central-1.console.aws.amazon.com/vpcconsole/home?region=eu-central-1#subnets:). Прописуємо **CIDR** для кожної.

![Screenshot 2024-09-17 163336](https://github.com/user-attachments/assets/059d9120-f18b-4ce6-8407-71dc0a6c4ead)
![Screenshot 2024-09-17 163739](https://github.com/user-attachments/assets/cab44203-acf9-48ea-bedc-029110b577bd)

Надаємо доступ до інтернету з наших підмереж створивши [**igw**](https://eu-central-1.console.aws.amazon.com/vpcconsole/home?region=eu-central-1#igws:).

![Screenshot 2024-09-17 163348](https://github.com/user-attachments/assets/44fb6ef2-cf7f-4769-9e9f-1caa6f243543)

Тепер треба написати маршрути у [**route table**](https://eu-central-1.console.aws.amazon.com/vpcconsole/home?region=eu-central-1#RouteTables:) для **igw,** щоб наші ресурси мали доступ до інтернету та був доступ до них через інтернет.

![Screenshot 2024-09-17 171650](https://github.com/user-attachments/assets/c5b29e10-3baf-48a3-a2b4-33ae6a4e2b31)


Також для того, щоб зробити публічною **endpoint** нашої **RDS** маємо поставити галочку **Enable DNS hostnames**. 

![Screenshot 2024-09-17 171937](https://github.com/user-attachments/assets/7b22f853-af51-4d86-8c57-8718efe0004c)
![Screenshot 2024-09-17 171948](https://github.com/user-attachments/assets/a4d16a53-974d-48b2-92ea-4365a506107b)


Створюємо [**security groups**](https://eu-central-1.console.aws.amazon.com/vpcconsole/home?region=eu-central-1#SecurityGroups:) та відкриваємо необхідні порти.

![Screenshot 2024-09-17 172641](https://github.com/user-attachments/assets/ecb19a79-e965-43a8-b5ea-a3f2ecc69dbc)

# 2. **Створити RDS**

Заходимо до [**RDS**](https://eu-central-1.console.aws.amazon.com/rds/home?region=eu-central-1) та створюємо нову базу.  
Вибираємо **“Standard create”,** друга опція легша але не буде повний перелік всіх можливостей. Вибираємо необхідну нам базу, версію та середу(прод, дев, тест).

![Screenshot 2024-09-18 083340](https://github.com/user-attachments/assets/388b4dc7-a7c8-4417-8d04-0a0ece08ceb7)
![Screenshot 2024-09-18 083400](https://github.com/user-attachments/assets/fe22719f-d9ac-4321-b3ee-ea8c60d4dc0e)

Прописуємо назву нашого інстансу. Маємо дві опції для логіну або через **Secrets Manager**, що є бест практиками. Або **Self managed** де ми самі пишемо юзера та пароль під якими будемо заходити до нашої бази.  
Після чого вибираємо тип нашого інстансу з необхідними нам ресурсами.  

![Screenshot 2024-09-18 083415](https://github.com/user-attachments/assets/cb426617-95a5-497a-86b5-badf0d94533c)
![Screenshot 2024-09-18 083433](https://github.com/user-attachments/assets/3243fa17-cd35-4dfd-a4be-653241fbbf7f)

Обираємо розмір сховища, його тип (**ssd/hdd**), **iops**, встановлюємо поріг для скейлингу.

![Screenshot 2024-09-18 083446](https://github.com/user-attachments/assets/dc773da4-b904-406f-958f-2c22d3a6a4a0)

По проекту ми не створюємо другу **read replica**. По бест практикам рекомендуємо мати такий дубль інстансу.

![Screenshot 2024-09-18 083459](https://github.com/user-attachments/assets/9f781f60-9827-4dc3-92e6-454efd6e3d3d)

Обираємо нашу створену **VPC**, тип підключення та надаємо публічний доступ до бази. По бест практикам рекомендуємо таки базу не робити публічною та створювати для неї окремі приватні підмережі.

![Screenshot 2024-09-18 083526](https://github.com/user-attachments/assets/8df9bb93-e7fd-4fcc-be37-7ba002b1a2c5)

Далі знаходимо нашу **security group**, яку створювали на початку. Рекомендуємо також для бази робити окрему. Налаштовуємо теги, та варіанти аутентифікації. Ми обирали **Password authentication**.  
Рекомендуємо вмикати моніторинг ти налаштовувати бекапування для відстежування логів майже у реальному часі і для відмовостійкості і надійності збереження даних робити бекапи.

![Screenshot 2024-09-18 083541](https://github.com/user-attachments/assets/bb5347fe-b348-4719-a242-e9f1353f2291)
![Screenshot 2024-09-18 083558](https://github.com/user-attachments/assets/367a7f01-8d54-438a-be91-57a4ca094542)
![Screenshot 2024-09-18 083650](https://github.com/user-attachments/assets/048a6916-51ea-45e2-87f8-4f8baaf5b05d)

# 3. **Створити AWS ECS cluster**

Заходимо до [AWS ECS](https://eu-central-1.console.aws.amazon.com/ecs/v2/clusters?region=eu-central-1) та створюємо кластер.  
Пишемо назву для нового кластеру та обираємо інфраструктуру на якій будуть працювати наші контейнери.

![Screenshot 2024-09-18 092029](https://github.com/user-attachments/assets/f052aedc-18b8-475b-8510-3b1d3d233be5)

По дефолту стоїть **АМІ Amazon Linux 2 (kernel 5.10)**, рекомендуємо обирати **Amazon Linux 2**. Був момент у підключені для серверу через веб, на дефолтному **АМІ** це не вдається.

![Screenshot 2024-09-18 092213](https://github.com/user-attachments/assets/5e06ebc9-c4ad-4171-a1dc-ad474e98c877)

Вибираємо нашу **VPC** та підмережі у яких будуть знаходитись інстанси, нашу створену **security group** та по проекту додавали публічну ІР адресу. Рекомендуємо вмикати моніторинг.

![Screenshot 2024-09-18 092425](https://github.com/user-attachments/assets/be33e1fd-fe3a-4067-ab68-081c0a2b707b)

# 4.  **Створити [task definitions](https://eu-central-1.console.aws.amazon.com/ecs/v2/task-definitions?region=eu-central-1)**.

Обираємо інфраструктуру на якій будуть хоститься контейнери, у нашому випадку це **ЕС2**. Також дуже важливо обрати мережевий мод для таску, у нашому варіанті можемо залишити дефолтний мод.

![Screenshot 2024-09-18 093841](https://github.com/user-attachments/assets/ce7c7789-59b8-45b9-813f-b90a4090cbe9)

Далі пишемо ім’я контейнера та шлях до іміджу. Якщо **ECS** не знайде імідж локально тоді буде шукати у **dockerhub**. Робимо порт форвардінг, по дефолту на хост порті буде 0, це рандомно надасть порт.

![Screenshot 2024-09-18 093904](https://github.com/user-attachments/assets/659d3737-891f-41b9-9dfc-94bc8fe5ec69)

Таски мають своє версіонування, з кожної версії можна створити нову.

![Screenshot 2024-09-18 102328](https://github.com/user-attachments/assets/cfb5ea36-a532-4d2f-b09a-27afb2c217b8)

# 5. **Створити [target group](https://eu-central-1.console.aws.amazon.com/ec2/home?region=eu-central-1#TargetGroups:)**

Тепер створюємо таргет групу у яку будемо додавати сервіс **ECS**. Для сервісу на **ЕС2** вибираємо інстанс. Якщо буде **Fargate** тоді ІР адресу.

![Screenshot 2024-09-18 142307](https://github.com/user-attachments/assets/346fd427-9583-420e-9a01-58989fed43a0)

Дуже важливо для health check вказати саме шлях до нашої першої сторінки.

![Screenshot 2024-09-18 151952](https://github.com/user-attachments/assets/29170286-37ec-40b0-8cba-3b9df8474f9f)

# 6. **Створити [Load Balancer](https://eu-central-1.console.aws.amazon.com/ec2/home?region=eu-central-1#LoadBalancers:)**

Тепер створюємо **Load Balancer** для нашого додатку. На першій сторінці маємо декілька варіантів, ми рекомендуємо саме **Application Load Balancer**.

![Screenshot 2024-09-18 142507](https://github.com/user-attachments/assets/6854c758-1a11-4a62-b23f-2c7b557e3726)

Пишемо ім’я, та робимо балансер публічним.

![Screenshot 2024-09-18 142540](https://github.com/user-attachments/assets/f7b7b272-6f8e-406f-8937-6b45f01100e9)

Обираємо мережу та зони які **Load Balancer** буде обслуговувати, **security group** яку створили раніше, а також прописуємо **listeners** які буде слухати наш балансир.

![Screenshot 2024-09-18 142641](https://github.com/user-attachments/assets/23d2fd3c-3028-4b63-a953-30eed8f5cefb)
![Screenshot 2024-09-18 142649](https://github.com/user-attachments/assets/c1ea671f-cbe0-4e70-8577-15452ee08c0d)
![Screenshot 2024-09-18 142713](https://github.com/user-attachments/assets/f74111b7-8849-4593-868e-f8a42edbabcd)

# 7. **Створити AWS [ECS](https://eu-central-1.console.aws.amazon.com/ecs/v2/clusters?region=eu-central-1) service з доданим Load Balancer**

Бест практики розгортання контейнерів у кластері ECS, це розгортання саме на сервісах. Сервіси це сутність яка керує нашими тасками і контейнерами всередині. У сервісах безліч опцій та можливостей, наприклад автоскейлинг, лоад балансер, балансування контейнерів між серверами та багато іншого.  
Вибираємо сервіс, таск яким він буде керувати, бажану кількість реплік.

![Screenshot 2024-09-18 103100](https://github.com/user-attachments/assets/faf1589d-13a9-44aa-b96d-3a266e61a025)

Відкриваємо вкладку балансира та вибираємо створений нами раніше балансир. Обираємо **listeners**, **target groups** та обов’язково треба вказати шлях до нашого додатку **health check path**.

![Screenshot 2024-09-18 154324](https://github.com/user-attachments/assets/efe08143-abda-438c-ad26-9b760eb61787)
![Screenshot 2024-09-18 154338](https://github.com/user-attachments/assets/ad05965a-0561-4bff-8126-b00310331969)
