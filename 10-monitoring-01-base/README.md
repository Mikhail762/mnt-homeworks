# Домашнее задание к занятию "10.01. Зачем и что нужно мониторить"

## Обязательные задания

`1. Вас пригласили настроить мониторинг на проект. На онбординге вам рассказали, что проект представляет из себя 
платформу для вычислений с выдачей текстовых отчетов, которые сохраняются на диск. Взаимодействие с платформой 
осуществляется по протоколу http. Также вам отметили, что вычисления загружают ЦПУ. Какой минимальный набор метрик вы
выведите в мониторинг и почему?`  
- доступность сервиса по коду http;
Для возможности непрерывной записи отчетов:  
- процент свободного места на диске;  
- процент свободных inode;  
- IOwait. Рост значений указывает на недостаточную скорость диска или ошибки при записи.  
- Процент загрузки CPU.

`2. Менеджер продукта посмотрев на ваши метрики сказал, что ему непонятно что такое RAM/inodes/CPUla. Также он сказал, 
что хочет понимать, насколько мы выполняем свои обязанности перед клиентами и какое качество обслуживания. Что вы 
можете ему предложить?`  

Для наглядности систему мониторинга нужно дополнить системой визуализации с понятными графиками. С клиентами нужно
обсудить необходимые показатели уровня обслуживания, разработать схему расчета этих показателей, и отслеживать
соответствие снимаемой метрики расчетным показателям.

`3. Вашей DevOps команде в этом году не выделили финансирование на построение системы сбора логов. Разработчики в свою 
очередь хотят видеть все ошибки, которые выдают их приложения. Какое решение вы можете предпринять в этой ситуации, 
чтобы разработчики получали ошибки приложения?`  

Архивировать логи и использовать бесплатную версию Graylog.

`4. Вы, как опытный SRE, сделали мониторинг, куда вывели отображения выполнения SLA=99% по http кодам ответов. 
Вычисляете этот параметр по следующей формуле: summ_2xx_requests/summ_all_requests. Данный параметр не поднимается выше 
70%, но при этом в вашей системе нет кодов ответа 5xx и 4xx. Где у вас ошибка?`  

Не учтены ответы 3хх. Формула должна выглядеть так:  
`SLI = (summ_2xx_requests + summ_3xx_requests)/summ_all_requests`

