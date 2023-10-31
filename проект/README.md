# Создание приложения для учёта сотрудников: добавление, редактирование, удаление.
## _Цель_

Целью моего проекта было создание приложения с выводом сотрудников, а также с функциями добавления, 
редактирования и удаления сотрудников.

Для этого я:
- Импортировала все нужные библиотеки
- Создала класс главного окна Main  
- Создала класс окна для обновления, наследуемый от класса дочернего окна
- Создала класс поиска записи
- Создала класс базы данных
- Прописала главное окно (размер, заголовок, ограничение изменения размеров окна)

## _Импортирование всех нужных библиотек_

- Импортировала модуль tkinter и дает ему псевдоним tk. Модуль tkinter предоставляет функциональность для создания 
графического интерфейса пользователя (GUI) в Python.

          import tkinter as tk

- Импортировала классы и функции из модуля tkinter для создания расширенных элементов управления в графическом интерфейсе.
ttk (Themed Tkinter) предоставляет более современные и стилизованные виджеты, такие как кнопки, текстовые поля и другие элементы.

          from tkinter import ttk

- Импортировала модуль sqlite3, который предоставляет функциональность для работы с базами данных SQLite в Python.
Библиотека sqlite3 позволяет создавать, подключаться и выполнять операции с базами данных SQLite.

          import sqlite3

## _Создание класса главного окна Main_

1) Функция-конструктор класса Main вызывается при создании объекта этого класса.
Он принимает аргумент root, который является родительским окном для класса Main.


Далее вызывается конструктор родительского класса tk.Frame,
чтобы инициализировать графический интерфейс.

        class Main(tk.Frame):
            def __init__(self, root):
                super().__init__(root)
                self.init_main()
                self.db = db
                self.view_records()



self.db = db - Эта строка устанавливает атрибут db для объекта класса Main. Предполагается, что db является объектом базы
данных, который будет использоваться в приложении.

self.view_records() - Этот метод вызывает функцию view_records(), которая отображает записи из базы данных в графическом
интерфейсе приложения.


2) Функция self.init_main() отвечает за хранение и инициализацию объектов GUI

           def init_main(self):

- Cоздаем панель инструментов (тулбар)


bg - фон


bd - границы

          toolbar = tk.Frame(bg='#d7d8e0', bd=2)
          

 - Упаковка
 - side закрепляет вверху окна
 - fill растягивает по X (горизонтали)

          toolbar.pack(side=tk.TOP, fill=tk.X)
          
          

 - Перенос картинки

           self.add_img = tk.PhotoImage(file='img/add.png')

- Создание кнопки добавления


command - функция по нажатию


compound - ориентация текста (tk.CENTER , tk.LEFT , tk.RIGHT , tk.TOP или tk.BOTTOM.)


image - иконка кнопки
        
      btn_open_dialog = tk.Button(toolbar, bg='#d7d8e0', bd=0,
          image=self.add_img, command=self.open_dialog)

 - Создание кнопки изменения данных
        
        self.update_img = tk.PhotoImage(file='img/update.png')
        btn_edit_dialog = tk.Button(toolbar, bg='#d7d8e0', bd=0,
                                    image=self.update_img, command=self.open_update_dialog)
        btn_edit_dialog.pack(side=tk.LEFT)

 - Создание кнопки удаления записи
        
        self.delete_img = tk.PhotoImage(file='img/delete.png')
        btn_delete = tk.Button(toolbar, bg='#d7d8e0', bd=0,
                               image=self.delete_img, command=self.delete_records)
        btn_delete.pack(side=tk.LEFT)

 - Создание кнопки поиска
        
        self.search_img = tk.PhotoImage(file='img/search.png')
        btn_search = tk.Button(toolbar, bg='#d7d8e0', bd=0,
                               image=self.search_img, command=self.open_search_dialog)
        btn_search.pack(side=tk.LEFT)

 - Создание кнопки обновления
        
        self.refresh_img = tk.PhotoImage(file='img/refresh.png')
        btn_refresh = tk.Button(toolbar, bg='#d7d8e0', bd=0,
                                image=self.refresh_img, command=self.view_records)
        btn_refresh.pack(side=tk.LEFT)



 - Добавляем Treeview


columns - столбцы


height - высота таблицы


show='headings' скрываем нулевую (пустую) колонку таблицы
        
         self.tree = ttk.Treeview(self, columns=('ID', 'name', 'tel', 'email', 'payment'),
                                 height=45, show='headings')


 - Добавляем параметры колонкам


width - ширина


anchor - выравнивание текста в ячейке


        self.tree.column("ID", width=30, anchor=tk.CENTER)
        self.tree.column("name", width=150, anchor=tk.CENTER)
        self.tree.column("tel", width=150, anchor=tk.CENTER)
        self.tree.column("email", width=150, anchor=tk.CENTER)
        self.tree.column("payment", width=150, anchor=tk.CENTER)

- Подписываем колонки

        self.tree.heading("ID", text='ID')
        self.tree.heading("name", text='ФИО')
        self.tree.heading("tel", text='Телефон')
        self.tree.heading("email", text='E-mail')
        self.tree.heading("payment", text='Зарплата')

- Упаковка

        self.tree.pack(side=tk.LEFT)

- Создаём полосу прокрутки

        scroll = tk.Scrollbar(self, command=self.tree.yview)

- Полоса прокрутки размещается на главном окне приложения с выравниванием слева
и заполняет доступное вертикальное пространство 

        scroll.pack(side=tk.LEFT, fill=tk.Y)

-  Настраивается виджет tree для использования полосы прокрутки. Опция yscrollcommand
указывает, какой метод полосы прокрутки будет вызываться при прокрутке виджета tree.
В данном случае, используется метод set полосы прокрутки

        self.tree.configure(yscrollcommand=scroll.set)


3) Функция records отвечает за добавнение данных

        def records(self, name, tel, email, payment):
            self.db.insert_data(name, tel, email, payment)
            self.view_records()

4) Функция update_record отвечает за обновление (изменение) данных
    
        def update_record(self, name, tel, email, payment):
            self.db.c.execute('''UPDATE db SET name=?, tel=?, email=?, payment=? WHERE ID=?''',
                          (name, tel, email, payment, self.tree.set(self.tree.selection()[0], '#1')))
            self.db.conn.commit()
            self.view_records()

5) Функция view_records отвечает за вывод данных в виджет таблицы
    
  - выбираем информацию из БД

        self.db.c.execute('''SELECT * FROM db''')

  - удаляем все из виджета таблицы

        [self.tree.delete(i) for i in self.tree.get_children()]
  - добавляем в виджет таблицы всю информацию из БД

        [self.tree.insert('', 'end', values=row)
         for row in self.db.c.fetchall()]
        
6) Функция delete_records отвечает за удаление записей
    
  - цикл по выделенным записям

        for selection_item in self.tree.selection():

  - удаление из БД

            self.db.c.execute('''DELETE FROM db WHERE id=?''',
                              (self.tree.set(selection_item, '#1'),))

  - сохранение изменений в БД

        self.db.conn.commit()

  - обновление виджета таблицы

        self.view_records()

7) Функция search_records отвечает за поиск записи
  

8) Функция open_dialog отвечает за вызов дочернего окна

       def open_dialog(self):
           Child()

9) Функция  open_update_dialog отвечает за вызов окна для изменения данных

        def open_update_dialog(self):
            Update()

10) Функция open_search_dialog отвечает за вызов окна для поиска

        def open_search_dialog(self):
            Search()











## _Создание класса дочерних окон_

Класс Child - класс дочерних окон

Toplevel - окно верхнего уровня

    class Child(tk.Toplevel):

1) Функция__init__ является конструктором 

    
        def __init__(self):
            super().__init__(root)
            self.init_child()
            self.view = app

2) Функция init_child регулирует параметры окна (загаловок, размер окна, ограничение изменения 
размеров окна)
    
        def init_child(self):
            self.title('Добавить')
            self.geometry('400x220')
            self.resizable(False, False)

   - Перехватываем все события происходящие в приложении

            self.grab_set()
   
   - Захватываем фокус
   
           self.focus_set()

   - Подписываем колонки
   
           label_name = tk.Label(self, text='ФИО:')
           label_name.place(x=50, y=50)
           label_select = tk.Label(self, text='Телефон')
           label_select.place(x=50, y=80)
           label_sum = tk.Label(self, text='E-mail')
           label_sum.place(x=50, y=110)
            label_pay = tk.Label(self, text='Зарплата')
            label_pay.place(x=50, y=140)

   - Добавляем строку ввода для наименования
   
           self.entry_name = ttk.Entry(self)
   
   - Меняем координаты объекта
   
           self.entry_name.place(x=200, y=50)

   - Добавляем строку ввода для email
   
           self.entry_email = ttk.Entry(self)
           self.entry_email.place(x=200, y=80)

   - Добавляем строку ввода для телефона
   
           self.entry_tel = ttk.Entry(self)
           self.entry_tel.place(x=200, y=110)
   
   - Добавляем кнопку ввода зарплаты
   
           self.entry_payment = ttk.Entry(self)
           self.entry_payment.place(x=200, y=140)

   - Кнопка закрытия дочернего окна
   
           self.btn_cancel = ttk.Button(
               self, text='Закрыть', command=self.destroy)
           self.btn_cancel.place(x=300, y=170)

   - Кнопка добавления
   
           self.btn_ok = ttk.Button(self, text='Добавить')
           self.btn_ok.place(x=220, y=170)
   
       Срабатывание по ЛКМ (переключение между разными языковыми раскладками)
       При нажатии кнопки вызывается метод records, которому передаюся значения из строк ввода
   
           self.btn_ok.bind('<Button-1>', lambda event: self.view.records(self.entry_name.get(),
                                                                          self.entry_email.get(),
                                                                          self.entry_tel.get()
                                                                          self.entry_payment.get()))))
    

## _Создание класса окна для обновления, наследуемого от класса дочернего окна_

Класс Update в качестве атребута принимает класс Child

    class Update(Child):


1) Функция__init__ является конструктором

        def __init__(self):
            super().__init__()
            self.init_edit()
            self.view = app
            self.db = db
            self.default_data()

2) Функция init_edit создаёт заголовок дочернего окна и кнопку с возможностью редактирования данных 

       def init_edit(self):
           self.title('Редактировать позицию')
           btn_edit = ttk.Button(self, text='Редактировать')
           btn_edit.place(x=205, y=170)
           btn_edit.bind('<Button-1>', lambda event: self.view.update_record(self.entry_name.get(),
                                                                             self.entry_email.get(),
                                                                             self.entry_tel.get()))

- Закрываем окно редактирования
- add='+' позваляет на одну кнопку вешать более одного события

           btn_edit.bind('<Button-1>', lambda event: self.destroy(), add='+')
           self.btn_ok.destroy()

3) Функция default_data выполняет SQL-запрос к базе данных, получает одну строку данных
из результата выполнения SQL-запроса и сохраняет ее в переменной row.

           def default_data(self):
              self.db.c.execute('''SELECT * FROM db WHERE id=?''',
                          (self.view.tree.set(self.view.tree.selection()[0], '#1'),))
              row = self.db.c.fetchone()
              self.entry_name.insert(0, row[1])
              self.entry_email.insert(0, row[2])
              self.entry_tel.insert(0, row[3])
              self.entry_payment.insert(0, row[4])



## _Создание класс поиска записи_

Класс Search принимает атрибут tk.Toplevel (окно верхнего уровня)

    class Search(tk.Toplevel):
        def __init__(self):
            super().__init__()
            self.init_search()
            self.view = app

1) Функция init_search оформляет окно поиска (заголовок, размер, ограничения изменения размера окна)

        def init_search(self):
            self.title('Поиск')
            self.geometry('300x100')
            self.resizable(False, False)

- Следующие строки кода создают метку, поле ввода и две кнопки на главном окне приложения, а также привязывают
события клика к соответствующим методам.

add='+' позваляет на одну кнопку вешать более одного события

            label_search = tk.Label(self, text='Поиск')
            label_search.place(x=50, y=20)

            self.entry_search = ttk.Entry(self)
            self.entry_search.place(x=105, y=20, width=150)

            btn_cancel = ttk.Button(self, text='Закрыть', command=self.destroy)
            btn_cancel.place(x=185, y=50)

            btn_search = ttk.Button(self, text='Поиск')
            btn_search.place(x=105, y=50)
            btn_search.bind('<Button-1>', 
                        lambda event: self.view.search_records(self.entry_search.get()))
            btn_search.bind('<Button-1>', 
                        lambda event: self.destroy(), add='+')





## _Создание класс базы данных_

Класс DB отвечает за создание базы данных

    class DB:

1) Функция__init__ является конструктором и в ней создаётся таблица, привязанная к модулю sqlite3
        
    
    def __init__(self):
        self.conn = sqlite3.connect('db.db') # создаем соединение с БД

- Создание объекта класса cursor, используемый для взаимодействия с БД

        self.c = self.conn.cursor()

- Выполнение запроса к БД

        self.c.execute(
            '''CREATE TABLE IF NOT EXISTS db (id integer primary key, name text, tel text, email text)''')
       
- Сохранение изменений БД

        self.conn.commit()

2) Функция insert_data отвечает за добавления в БД


    def insert_data(self, name, tel, email):
        self.c.execute('''INSERT INTO db (name, tel, email) VALUES (?, ?, ?)''',
                       (name, tel, email))
        self.conn.commit()





## _Прописала главное окно (размер, заголовок, ограничение изменения размеров окна)_

- Эта строка проверяет, запущен ли данный файл напрямую, а не импортирован как модуль.
Это обычно используется для выполнения определенного кода только при запуске файла
напрямую.

        if __name__ == '__main__':

- Создается экземпляр класса Tk() из модуля tkinter

        root = tk.Tk()

- Создается экземпляр класса DB()

        db = DB()
        app = Main(root)
        app.pack()

- Устанавливается заголовок окна на "Телефонная книга"

        root.title('Телефонная книга')

- Устанавливается размер окна
    
        root.geometry('665x450')

- Ограничивается возможность изменения размеров окна по ширине и высоте.

        root.resizable(False, False)

- Запускается главный цикл обработки событий, который ожидает и обрабатывает события
в приложении, пока окно не будет закрыто.

        root.mainloop()




## _Вывод_
Я создала приложение, которое позволяет отслеживать наличие сотрудников и их данные, такие как:
ФИО, электронная почта, номер телефона и заработная плата.

Это приложение при небольшой доработке может использоваться в компаниях и различных организациях
для отслеживания кадров.




Автор: Казакова Александра Сергеевна (alexkazakova)