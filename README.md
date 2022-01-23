# ToDo
Create a versatile Todo app using (Item/Model) based containers

Our Application interface
-
![WhatsApp Image 2022-01-23 at 16 29 13](https://user-images.githubusercontent.com/93820154/150686449-1540d657-8b0f-4068-b912-57d34569b0cb.jpeg)

The View of the main widget is split in three areas:The first (en persistent) area shows the list of today tasks
-The second one is reserved for pending task (tasks for the future)
-Finally, the third one shows the set of finished tasks.

We also created 3 menus:

1-File menu:

![image](https://user-images.githubusercontent.com/93820154/150686525-abd74314-8a3f-4811-bc81-4c7574ca1b6f.png)

2-Option menu:

![image](https://user-images.githubusercontent.com/93820154/150686549-05a990b6-19c6-49c2-8db7-2c8437a8152a.png)

3-Help menu:

![image](https://user-images.githubusercontent.com/93820154/150686564-0018e59a-005d-4402-80e4-dff721ec936b.png)

Use Cases:
-
Here is a list of cases that the user could perform with our app:

close the application
-
```cpp
void ToDo::on_action_Quit_triggered()
{
    QFile file("CurrentFile.txt");
    if(file.open(QIODevice::ReadWrite|QIODevice::Text)){
    QTextStream stream(&file);
    stream<<currentFile;
    file.close();
    }
    
    this->close();
}
```

Creating new tasks
-
The essential components of a task : description, finished and a Tag.
To add a task we first need a dialog
```cpp
#include "addtask.h"
#include "ui_addtask.h"

addtask::addtask(QWidget *parent) :
    QDialog(parent),
    ui(new Ui::addtask)
{
    ui->setupUi(this);
}

addtask::~addtask()
{
    delete ui;
}
QString addtask::gettask() {
   return ui->lineEdit->text()+ " Due: "+ui->dateEdit->text() + " Tag: " +ui->comboBox->currentText();}

    QDate addtask::getdate(){
       return ui->dateEdit->date();
    }
    bool addtask::get_finished_task(){
       return ui->checkBox->isChecked();
    }
    QString addtask::getTag(){
       return ui->comboBox->currentText();
}
```
The add slot:
```cpp
void ToDo::on_actionAdd_task_triggered()
{
    addtask D;
     auto reply = D.exec();
     if (reply==addtask::Accepted)
     {
         if (D.get_finished_task())
         {
            ui->finishedtasks->addItem(D.gettask());
         }
      else if (QDate::currentDate() == D.getdate())
          ui->taskslist->addItem(D.gettask());
      else
          ui->pendingtasks->addItem(D.gettask());
}}
```
![image](https://user-images.githubusercontent.com/93820154/150688803-dff854fe-e075-40b5-b7b2-0a278305e86f.png)
 
 
Hide/Show the pending
-
```cpp
void ToDo::on_actionUpcoming_task_toggled(bool arg1)
{
    if(arg1)
            ui->pendingtasks->setVisible(true);
        else
            ui->pendingtasks->setVisible(false);
}
```

Finish views
-
```cpp
void ToDo::on_actionFinished_Tasks_toggled(bool arg1)
{
    if(arg1)
            ui->finishedtasks->setVisible(true);
        else
            ui->finishedtasks->setVisible(false);
}
```
 The tasks entered to the application must remains in the app in future use
 -
 Meaning, If I create a task and I close the application, next time I opened the application, I should find my tasks and not start from scratch.
 
 ```cpp

```

 
 
 
