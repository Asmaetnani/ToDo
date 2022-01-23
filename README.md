# ToDo Application
The goal of the project is to create an application to manage our tasks. It should have all the features of main application such as menues, actions and toolbar. The application must store an archive of all the pending and finished tasks.

## Our Application interface   

We have used QTDesigner to obtain the following overview

![WhatsApp Image 2022-01-23 at 16 29 13](https://user-images.githubusercontent.com/93820154/150686449-1540d657-8b0f-4068-b912-57d34569b0cb.jpeg)

The View of the main widget is split in three areas:The first (en persistent) area shows the list of today tasks
-The second one is reserved for pending task (tasks for the future)
-Finally, the third one shows the set of finished tasks.

We created a menuBar that is composed of 3 menus:

1-File menu:

![image](https://user-images.githubusercontent.com/93820154/150686525-abd74314-8a3f-4811-bc81-4c7574ca1b6f.png)

2-Option menu:

![image](https://user-images.githubusercontent.com/93820154/150686549-05a990b6-19c6-49c2-8db7-2c8437a8152a.png)

3-Help menu:

![image](https://user-images.githubusercontent.com/93820154/150686564-0018e59a-005d-4402-80e4-dff721ec936b.png)

Adding a task:
-
A Task is defined by the following attributes:   


   - A description: stating the text and goal for the task like (Buying the milk).  


   - A finished boolean indicating if the task is Finished or due.   

   - A Tag category to show the class of the task .    



Finally, a task should have a DueDate which stores the Date planned for the date.    
  
  
First we need to create a dialog to extract those attributes and here is the code for it:    
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
![image](https://user-images.githubusercontent.com/93820154/150688803-dff854fe-e075-40b5-b7b2-0a278305e86f.png)

Now we move to the implementation of our function :   
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
Hide/Show the pending and finished views
-
The user could either hide/show the pending and finished views    

This is the implementation of on_actionUpcoming_task_toggled slot:
```cpp
void ToDo::on_actionUpcoming_task_toggled(bool arg1)
{
    if(arg1)
            ui->pendingtasks->setVisible(true);
        else
            ui->pendingtasks->setVisible(false);
}
```
This is the implementation of on_actionFinished_Tasks_toggled slot:

```cpp
void ToDo::on_actionFinished_Tasks_toggled(bool arg1)
{
    if(arg1)
            ui->finishedtasks->setVisible(true);
        else
            ui->finishedtasks->setVisible(false);
}
```
 Save Files
 -
 We need to add a function that will save our tasks in a file.
 ```cpp
 void ToDo::saveTasks(QString filename){
QFile file(filename);
if(file.open(QIODevice::WriteOnly|QIODevice::Text)){

QTextStream stream (&file);
 stream<<"List of Today's tasks :"<<Qt::endl;
for(int i=0;i<ui->taskslist->count();i++){
QListWidgetItem*item=ui->taskslist->item(i);
stream<<item->text()<<Qt::endl;
}
stream<<"List of pending tasks :"<<Qt::endl;
for(int i=0;i<ui->pendingtasks->count();i++){
QListWidgetItem*item=ui->pendingtasks->item(i);
stream<<item->text()<<Qt::endl;
}
stream<<"List of finished tasks :"<<Qt::endl;
for(int i=0;i<ui->finishedtasks->count();i++){
QListWidgetItem*item=ui->finishedtasks->item(i);
stream<<item->text()<<Qt::endl;
}


file.close();
}
 ```
 And this is the implementation of the slot :   
  ```cpp
  void ToDo::on_actionSave_triggered()
{
    if(currentFile=="")
    {

    QFileDialog d;
    auto filename=d.getSaveFileName();
    currentFile=filename;
    setWindowTitle(currentFile);

    }

    saveTasks(currentFile);

    }
}
  
  ```
  Open a file
  -
  After saving afile we can open it like following:
  ```cpp
  void ToDo::openTasks(QString filename){

QFile file(filename);
if(file.open(QIODevice::ReadOnly|QIODevice::Text)){

QTextStream open (&file);
QVector<QString>tasks;
while(!open.atEnd()){
QString line=open.readLine();
tasks.append(line);

}
int a =tasks.indexOf("List of pending tasks :");
int b=tasks.indexOf("List of finished tasks :");
for(auto i=1;i<a;i++)
ui->taskslist->addItem(tasks[i]);
for(auto i=a+1;i<b;i++)
ui->pendingtasks->addItem(tasks[i]);
for(auto i=b+1;i<tasks.size();i++)
ui->finishedtasks->addItem(tasks[i]);


file.close();
}
}
  ```
And this is the implementation of the slot :
```cpp
void ToDo::on_actionOpen_triggered()
{
    QFileDialog d;
    auto filename=d.getOpenFileName();
    currentFile=filename;
    setWindowTitle(currentFile);

    openTasks(currentFile);
}
```
Close the application
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
Exemple
-

 MVC Model
 -
 No we are going to make another version of the application using models:
  - First we are going to create the form using QtDesigner but this time using QListView instead of QListWidget     
 Then we have to create models using QStringModel
 We are going to add the following in the header   
 ```cpp
    QStringListModel *model1;
    QStringListModel *model2;
    QStringListModel *model3;

    QStringList newtasks;
    QStringList Pendingtasks;
    QStringList Finishedtasks;
 ```
 And in the .cpp file we will add the implementation:
 ```cpp
 model1 = new QStringListModel();
 model2 = new QStringListModel();
 model3 = new QStringListModel();

model1->setStringList(newtasks);
ui->newlist->setModel(model1);

model2->setStringList(Pendingtasks);
ui->pendinglist->setModel(model2);

model3->setStringList(Finishedtasks);
ui->finishedlisk->setModel(model3);
```
Add task function
-
```cpp
void ToDo::on_actionAdd_Task_triggered()
{
    addTask dialog;
    auto reply= dialog.exec();
    if(reply == addTask::Accepted)
    {
        QString text= dialog.getTask();
        if(dialog.getDate()==QDate::currentDate()){
            newtasks.append(text);
        }else if(dialog.getDate()!=QDate::currentDate()){
            Pendingtasks.append(text);
        }else if(dialog.isChecked()){
            Finishedtasks.append(text);
        }
    }

    model1->setStringList(newtasks);
    ui->newlist->setModel(model1);

    model2->setStringList(Pendingtasks);
    ui->pendinglist->setModel(model2);


    model3->setStringList(Finishedtasks);
    ui->finishedlisk->setModel(model3);
}
```
Save function
-
```cpp
if(file.open(QIODevice::ReadWrite | QIODevice::Text)){
     QTextStream out(&file);
     for(int i=0;i<Todaytasks.size();i++)
     {
         out << "1"<< Todaytasks.at(i)<< Qt::endl;
     }
     for(int i=0;i<Pendingtasks.size();i++)
     {
         out << "2"<< Pendingtasks.at(i) << Qt::endl;
     }
     for(int i=0;i<Finishedtasks.size();i++)
     {
         out << "3"<< Finishedtasks.at(i) << Qt::endl;
     }
     file.close();
 }

```
Open function
```cpp
if (!file.open(QIODevice::ReadOnly | QIODevice::Text))
         return;

   while (!file.atEnd()) {
       QString line = file.readLine();
       if(line.at(0)=="1"){
             Todaytasks.append(line.mid(1,line.size()));
       }else if(line.at(0)=="2"){
           Pendingtasks.append(line.mid(1,line.size()));
       }else if(line.at(0)=="3"){
           Finishedtasks.append(line.mid(1,line.size()));
       }
   }
   
   model1->setStringList(Todaytasks);
   ui->lw1->setModel(model1);

   model2->setStringList(Pendingtasks);
   ui->lw2->setModel(model2);

   model3->setStringList(Finishedtasks);
   ui->lw3->setModel(model3);
```
 Conclusion
 -
 Doing this homework was the best way to better understand how to work with (item/model) and the MVC model as well and developp an application using both methods .
 
 
 
