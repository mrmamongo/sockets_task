# Ремарочки ремарочки

Что было сделано правильно:

- В целом задача была решена, ООП не требовалось, так что даже не буду ничего по этому поводу говорить

Что можно сделать, чтобы ООП работало как надо:

1. !! Вспомнить, что такое ООП !! Эта методология постулирует манипуляцию объектам и контрактами между объектами
2. Определить, какие в программе будут объекты, и какие между ними будут контракты. Объекты - классы\структуры, контракты между объектами - интерфейсы классов
3. Реализовать сначала интерфейсы, а потом только логику! Ты пишешь как бы "библиотеку". Надо сделать так, чтобы конечный пользователь (a.k.a глупый программист Вася) смог твою библиотеку скачать и понять по коду\интерфейсу, что и как работает
4. Логику при этом по максимуму инкапсулировать внутрь объектов\контрактов. Таким образом ты избавишь конечного ~~или конченного~~ Васю от лишних ошибок и читания твоего кода

Как это сделать? Приведу пример клиента:

Первое - основной объект, который будет всё связывать:

```c++

class client {
    private:
        // ...
    public:
        void send_message(...); // + разные перегрузки: отправка просто строки, либо сериализуемого объекта
        void receive_message(...); // здесь например таймаут или разные шаблонные параметры для десериализации сразу в структуру
        void connect(...); // таймаут, эндпоинт для подключения
        void disconnect(...);
};
```

Далее - объект контракта, tcp_client

```c++

class tcp_client {
    private:
    public:
        void send(); // более низкоуровневый слой, тут работаем только с char* или uint8*
        char* /* uint8* */ recv();
};
```

Он может хранить в себе условный сокет,
по которому будет общаться с сервером, флаги и прочие.
Основная идея - разграничить слои ответственности между `client` и
`tcp_client` так, чтобы каждый из них занимался только теми делами,
которые предусмотрены на его уроне абстракции. 
Так у нас получается, что `tcp_client` представляет слой **транспортный**,
а `client` - **приложения**
~~[ни разу не намёк](https://ru.wikipedia.org/wiki/%D0%A1%D0%B5%D1%82%D0%B5%D0%B2%D0%B0%D1%8F_%D0%BC%D0%BE%D0%B4%D0%B5%D0%BB%D1%8C_OSI)~~

Идём дальше!

Это ведь мы дошли только до уровня **приложения** 

И поэтому, например, в конечном приложении и в сервере, и в клиенте будет использоваться вот этот класс `client`
Мозг взорвался, да? А ведь, если немного вдуматься, то идея уже не кажется такой странной. И сервер, и клиент работают с объектом клиента, который предоставляет абсолютно идентичный интерфейс. Уже не такой взрыв мозга, да?

На данном этапе пока что всё. Дальше давай думай, предполагай сама. Буду ждать мыслей