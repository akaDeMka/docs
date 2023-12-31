# POSIX signals

POSIX определяет 28 сигналов, которые можно классифицировать следующим образом:

| Название | Код | Действие по умолчанию | Описание | Тип |
|----------|----------|----------|----------|----------|
| SIGABRT | 6 | Завершение с дампом памяти | Сигнал, посылаемый функцией abort() | Управление |
| SIGALRM | 14 | Завершение | Сигнал истечения времени, заданного alarm() | Уведомление |
| SIGBUS | 10 | Завершение с дампом памяти | Неправильное обращение в физическую память | Исключение |
| SIGCHLD | 18 | Игнорируется | Дочерний процесс завершен или остановлен | Уведомление |
| SIGCONT | 25 | Продолжить выполнение | Продолжить выполнение ранее остановленного процесса | Управление |
| SIGFPE | 8 | Завершение с дампом памяти | Ошибочная арифметическая операция | Исключение |
| SIGHUP | 1 | Завершение | Закрытие терминала | Уведомление |
| SIGILL | 4 | Завершение с дампом памяти | Недопустимая инструкция процессора | Исключение |
| SIGINT | 2 | Завершение | Сигнал прерывания (Ctrl-C) с терминала | Управление |
| SIGKILL | 9 | Завершение | Безусловное завершение | Управление |
| SIGPIPE | 13 | Завершение | Запись в разорванное соединение (пайп, сокет) | Уведомление |
| SIGQUIT | 3 | Завершение с дампом памяти | Сигнал «Quit» с терминала (Ctrl-\\) | Управление |
| SIGSEGV | 11 | Завершение с дампом памяти | Нарушение при обращении в память | Исключение |
| SIGSTOP | 23 | Остановка процесса | Остановка выполнения процесса | Управление |
| SIGTERM | 15 | Завершение | Сигнал завершения (сигнал по умолчанию для утилиты kill) | Управление |
| SIGTSTP | 20 | Остановка процесса | Сигнал остановки с терминала (Ctrl-Z) | Управление |
| SIGTTIN | 26 | Остановка процесса | Попытка чтения с терминала фоновым процессом | Управление |
| SIGTTOU | 27 | Остановка процесса | Попытка записи на терминал фоновым процессом | Управление |
| SIGUSR1 | 16 | Завершение | Пользовательский сигнал № 2 | Пользовательский |
| SIGUSR2 | 17 | Завершение | Неправильное обращение в физическую память | Исключение |
| SIGPOLL | 22 | Завершение | Событие, отслеживаемое poll() | Уведомление |
| SIGPROF | 29 | Завершение | Истечение таймера профилирования | Отладка |
| SIGSYS | 12 | Завершение с дампом памяти | Неправильный системный вызов | Исключение |
| SIGTRAP | 5 | Завершение с дампом памяти | Ловушка трассировки или брейкпоинт | Отладка |
| SIGURG | 21 | Игнорируется | На сокете получены срочные данные | Уведомление |
| SIGVTALRM | 28 | Завершение | Истечение «виртуального таймера» | Уведомление |
| SIGXCPU | 30 | Завершение с дампом памяти | Процесс превысил лимит процессорного времени | Исключение |
| SIGXFSZ | 31 | Завершение с дампом памяти | Процесс превысил допустимый размер файла | Исключение |
| SIGBUS | 10 | Завершение с дампом памяти | Неправильное обращение в физическую память | Исключение |
