# Інтеграція Jenkins для CI/CD Java-проєкту

Мета роботи

1. Ознайомитися з основами Jenkins

2. Налаштувати Freestyle Job

3. Створити декларативний Jenkins Pipeline

4. Додати Telegram-нотифікації про статус білда


## Виконання

Форкнув репозиторій gs-spring-boot у свій GitHub:

https://github.com/YuriiStashkiv/gs-spring-boot

Jenkins автоматично клонує його під час запуску джобів. 

Успішно запустив .yml файл.

![](/DZ1/screenshots/Docker_Desktop_1.png)

Підготував Jenkins до роботи

У Tools додав maven-інсталятор, для його коректної роботи. 

![](/DZ1/screenshots/Maven_tools.png)

Також встановив додатковий Plugin для Telegram сповіщень і настроїв його відповідно до інструкції, яку надав розробник.

https://github.com/jenkinsci/telegram-notifications-plugin

![](/DZ1/screenshots/Bot_in_Jenkins.png)

Створив два Jobs у Jenkins

![](/DZ1/screenshots/Jobs_in_jenkins.png)

### Simple Freestyle Job

Підключив Git репозиторій до Job, через відповідну вкладку. Вставив туди посилання і замінив гілку на main.

![](/DZ1/screenshots/Git.png)

У Build-Steps спершу використав maven, який я попередньо налаштував у Tools, а у додаткових налаштування вибрав розташування POM, бо у форкнутого репозиторія їх є 2. Я обрав Complete

![](/DZ1/screenshots/Maven_with_POM.png)

Post-build Actions додав дію Archive the artifacts. І вказав шлях.

Результат:

![](/DZ1/screenshots/Freestyle_res.png)

### Simple Pipeline Job

У цьому Job все виконувалось у коді.

      pipeline{
          agent any
          tools{
              maven 'Maven-3.9.11' # підключаємо maven, назва така, яку вказували при створенні
          }
          stages{
              
              stage('Checkout'){
                  steps{
                      git branch: 'main', url:'https://github.com/YuriiStashkiv/gs-spring-boot' # підключаємо до Git репозиторію, який я форкнув
                  }
              }
              
              stage('Build'){
                  steps{
                      dir ('complete'){ # вказуємо у якій папці буде виконуватися білд
                          sh 'mvn clean install' 
                      }
                  }
              }
              
              stage('Archive'){
                  steps{ 
                      archiveArtifacts artifacts: 'complete/target/*.jar' # архівуємо наш результат
                  }
              }
          }
          post{
              success {
                  telegramSend(message: 'Yay', chatId: -5047667306) # підключаємось до телеграм бота
              }
          }
      }

Результат:

![](/DZ1/screenshots/Pipeline_res.png)

Сповіщення у телеграмі:

![](/DZ1/screenshots/Bot_in_telegtam.png)


