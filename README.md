# ToDo
Create a versatile Todo app using (Item/Model) based containers

Our Application interface
-
![WhatsApp Image 2022-01-23 at 16 29 13](https://user-images.githubusercontent.com/93820154/150686449-1540d657-8b0f-4068-b912-57d34569b0cb.jpeg)

We created 3 menus:

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
***
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
***
