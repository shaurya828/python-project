import mysql.connector
mycon=mysql.connector.connect(host="localhost",user="root",password="mypassword",database="test")
cursor=mycon.cursor()
stocklist="PRODUCT ID | CATAGORY        | ARTICLE NO | BRAND           | PRICE   | QUANTITY  | GST "
saleslist="SALE NUMBER | PRODUCT CODE | QUANTITY | CUSTOMER ID | DATE OF SALE | MODE OF PAYMENT | TOTAL SALE AMOUNT"
customerlist="CUSTOMER ID | CUSTOMER NAME   | CUSTOMER MOBILE NO | DATE OF REGISTRATION"

def stock1():
    try:
        cursor.execute("SELECT MAX(PID),COUNT(*) FROM STOCK")
        data=cursor.fetchone()
        if data[1]==0:
            pid=1001
        else:
            pid=data[0]+1
        ctg=input("Enter the catagory of product :-")
        arn=input("Enter the 3 digit article no :-")
        brd=input("Enter the brand of product :-")
        pr=input("Enter the price of product :-")
        qty=input("Enter the quantity of product :-")
        if int(pr)<1000:
            cursor.execute("INSERT INTO STOCK VALUES('"+str(pid)+"','"+ctg+"','"+arn+"','"+brd+"','"+pr+"','"+qty+"','"+str((int(pr))/20)+"')")
        else:
            cursor.execute("INSERT INTO STOCK VALUES('"+str(pid)+"','"+ctg+"','"+arn+"','"+brd+"','"+pr+"','"+qty+"','"+str((int(pr))*0.18)+"')")
        print("New product has been registered")
        print("This product's product id is" , pid)
        mycon.commit()
    except:
        print("something went wrong please try again , so for the inconvenience.\n")

def stock2(a21):
    try:
        cursor.execute("SELECT PID FROM STOCK")
        info=cursor.fetchall()
        if (a21,) not in info:
            print("You entered wrong product id which does not exist.\n")
        else:
            cursor.execute("DELETE FROM STOCK WHERE PID='"+str(a21)+"'")
            print(a21,"product id  product is deleted succesfully")
        mycon.commit()
    except:
        print("something went wrong please try again , so for the inconvenience.\n")

def stock3(a31):
    try:
        cursor.execute("SELECT PID FROM STOCK")
        info=cursor.fetchall()
        if (a31,) not in info:
            print("You entered wrong product id which does not exist.\n")
        else:
            ctg=input("Enter the catagory of product :-")
            arn=input("Enter the 3 digit article no :-")
            brd=input("Enter the brand of product :-")
            pr=int(input("Enter the price of product :-"))
            qty=input("Enter the quantity of product :-")
            if pr<1000:
                cursor.execute("UPDATE STOCK SET CTG='"+ctg+"', ARN='"+arn+"', BRD='"+brd+"', PR='"+str(pr)+"', QTY='"+qty+"',GST='"+str(int(pr/20))+"' WHERE PID='"+str(a31)+"'")
            else:
                cursor.execute("UPDATE STOCK SET CTG='"+ctg+"', ARN='"+arn+"', BRD='"+brd+"', PR='"+str(pr)+"', QTY='"+qty+"',GST='"+str(int(pr*0.18))+"'WHERE PID='"+str(a31)+"'")
            print(a31,"product id  product is modified succesfully")
        mycon.commit()
    except:
        print("something went wrong please try again , so for the inconvenience.\n")

def stock4(a41):
    try:
        cursor.execute("SELECT PID FROM STOCK")
        info=cursor.fetchall()
        if (a41,) not in info:
            print("You entered wrong product id which does not exist.\n")
        else:
            cursor.execute("SELECT * FROM STOCK WHERE PID='"+str(a41)+"'")
            data=cursor.fetchall()
            print(a41,"product id  product's data is")
            print(stocklist)
            for i in data:
                if i[4]<1000:
                    print(i[0],"       | ",i[1]," "*(15-len(i[1]))," | ",i[2],"        | ",i[3]," "*(15-len(i[3]))," | ",i[4],"   | ",i[5],"       | ",i[6],sep="")
                else:
                    print(i[0],"       | ",i[1]," "*(15-len(i[1]))," | ",i[2],"        | ",i[3]," "*(15-len(i[3]))," | ",i[4],"  | ",i[5],"       | ",i[6],sep="")
        mycon.commit()
    except:
        print("something went wrong please try again , so for the inconvenience.\n")

def stock5():
    try:
        cursor.execute("SELECT * FROM STOCK")
        data=cursor.fetchall()
        print("All details of stock is :-")
        print(stocklist)
        for i in data:
            if i[4]<1000:
                print(i[0],"       | ",i[1]," "*(15-len(i[1]))," | ",i[2],"        | ",i[3]," "*(15-len(i[3]))," | ",i[4],"   | ",i[5],"       | ",i[6],sep="")
            else:
                print(i[0],"       | ",i[1]," "*(15-len(i[1]))," | ",i[2],"        | ",i[3]," "*(15-len(i[3]))," | ",i[4],"  | ",i[5],"       | ",i[6],sep="")
        mycon.commit()
    except:
        print("something went wrong please try again , so for the inconvenience.\n")

def sales1():
    try:
        cursor.execute("SELECT MAX(SNO),COUNT(*) FROM SALES")
        data=cursor.fetchone()
        if data[1]==0:
            sno=1001
        else:
            sno=data[0]+1
        pid=int(input("Enter the product id of sale :-"))
        cursor.execute("SELECT PID FROM STOCK")
        info=cursor.fetchall()
        if (pid,) in info:
            qty=int(input("Enter the quantity of product saled :-"))
            cursor.execute("SELECT PR , GST , QTY FROM STOCK WHERE PID='"+str(pid)+"'")
            data=cursor.fetchall()
            data2=data[0]
            am=(int(data2[0])+int(data2[1]))*(int(qty))
            if data2[2]>=qty:
                cid=int(input("Enter the customer id of customer :-"))
                cursor.execute("SELECT CID FROM CUSTOMER")
                info=cursor.fetchall()
                if (cid,) in info:
                    date=input("Enter the date of sale in (YYYY-MM-DD) :-")
                    mode=input("Enter the mode of payment :-")
                    cursor.execute("UPDATE STOCK SET QTY=QTY-'"+str(qty)+"' WHERE PID='"+str(pid)+"' ")
                    cursor.execute("INSERT INTO SALES VALUES('"+str(sno)+"','"+str(pid)+"','"+str(qty)+"','"+str(cid)+"','"+date+"','"+mode+"','"+str(int(am))+"')")
                    print("This sale , sale no is",sno)
                    print("New sale has been registered")
                else:
                    print("You entered wrong customer id which does not exist.\n")
            else:
                print("product has not much quantity to register the sale")
        else:
            print("You entered a sales on non existing product id , so please first correct the product id this customer to record this sale")
        mycon.commit()
    except:
        print("something went wrong please try again , so for the inconvenience.\n")

def sales2(b21):
    try:
        cursor.execute("SELECT COUNT(*) FROM SALES WHERE DAT='"+b21+"'")
        info=cursor.fetchone()
        if info[0]==0:
            print("You entered wrong DATE which does not exist.\n")
        else:
            cursor.execute("SELECT * FROM SALES WHERE DAT='"+b21+"'")
            data=cursor.fetchall()
            print("All sales on date",b21,":-")
            print(saleslist)
            for i in data:
                print(i[0],"        | ",i[1],"         | ",i[2],"        | ",i[3],"        | ",i[4],"   | ",i[5]," "*(15-len(i[5]))," | ",i[6],sep="")
        mycon.commit()
    except:
        print("something went wrong please try again , so for the inconvenience.\n")

def sales3(b31):
    try:
        cursor.execute("SELECT CID FROM CUSTOMER")
        info=cursor.fetchall()
        if (b31,) not in info:
            print("You entered wrong customer id which does not exist.\n")
        else:
            cursor.execute("SELECT COUNT(*) FROM SALES WHERE CID='"+str(b31)+"'")
            data=cursor.fetchone()
            if data[0]>0:
                cursor.execute("SELECT * FROM SALES WHERE CID=('"+str(b31)+"')")
                data=cursor.fetchall()
                print("All sales made by a customer",b31,":-")
                print(saleslist)
                for i in data:
                    print(i[0],"        | ",i[1],"         | ",i[2],"        | ",i[3],"        | ",i[4],"   | ",i[5]," "*(15-len(i[5]))," | ",i[6],sep="")
            else:
                print("This customer has not purchased any item.\n")
        mycon.commit()
    except:
        print("something went wrong please try again , so for the inconvenience.\n")

def sales4(b41):
    try:
        cursor.execute("SELECT PID FROM STOCK")
        info=cursor.fetchall()
        if (b41,) not in info:
            print("Thier is no product with this product id")
        else:
            cursor.execute("SELECT COUNT(*) FROM SALES WHERE PIDS='"+str(b41)+"'")
            data=cursor.fetchone()
            if data[0]>0:
                cursor.execute("SELECT * FROM SALES WHERE PIDS='"+str(b41)+"'")
                data=cursor.fetchall()
                print("All sales of product",b41,":-")
                print(saleslist)
                for i in data:
                    print(i[0],"        | ",i[1],"         | ",i[2],"        | ",i[3],"        | ",i[4],"   | ",i[5]," "*(15-len(i[5]))," | ",i[6],sep="")
            else:
                print("Currently no sale is registered of this product.\n")
        mycon.commit()
    except:
        print("something went wrong please try again , so for the inconvenience.\n")

def sales5(b51,b52):
    try:
        cursor.execute("SELECT SUM(S.GST*R.QTY) from STOCK S ,SALES R WHERE DAT BETWEEN '"+b51+"' and '"+b52+"' and S.PID=R.PIDS")
        data=cursor.fetchone()
        print("Total gst collection between",b51,"and",b52,":-",data[0])
        mycon.commit()
    except:
        print("something went wrong please try again , so for the inconvenience.\n")

def customer1():
    try:
        cursor.execute("SELECT MAX(CID),COUNT(*) FROM CUSTOMER")
        data=cursor.fetchone()
        if data[1]==0:
            cid=1001
        else:
            cid=data[0]+1
        cname=input("Enter the name of customer :-")
        cmno=input("Enter the mobile no :-")
        dor=input("Enter the date of registration in (YYYY-MM-DD):-")
        cursor.execute("INSERT INTO CUSTOMER VALUES('"+str(cid)+"','"+cname+"','"+cmno+"','"+dor+"')")
        print("New customer has been registered its customer id is ",cid,"\n",sep="")
        mycon.commit()
    except:
        print("something went wrong please try again , so for the inconvenience.\n")

def customer2(c21):
    try:
        cursor.execute("SELECT CID FROM CUSTOMER")
        info=cursor.fetchall()
        if (c21,) not in info:
            print("You entered wrong customer id which does not exist.\n")
        else:
            cursor.execute("DELETE FROM CUSTOMER WHERE CID='"+str(c21)+"'")
            print(c21,"customer id  product is deleted succesfully")
        mycon.commit()
    except:
        print("something went wrong please try again , so for the inconvenience.\n")

def customer3(c31):
    try:
        cursor.execute("SELECT CID FROM CUSTOMER")
        info=cursor.fetchall()
        if (c31,) not in info:
            print("You entered wrong customer id which does not exist.\n")
        else:
            cname=input("Enter the name of customer :-")
            cmno=input("Enter the mobile no :-")
            dor=input("Enter the date of registration :-")
            cursor.execute("UPDATE CUSTOMER SET CNAME='"+cname+"', CMNO='"+cmno+"', DOR='"+dor+"' WHERE CID='"+str(c31)+"'")
            print(c31,"product id  product is modified succesfully")
        mycon.commit()
    except:
        print("something went wrong please try again , so for the inconvenience.\n")

def customer4(c41):
    try:
        cursor.execute("SELECT CID FROM CUSTOMER")
        info=cursor.fetchall()
        if (c41,) not in info:
            print("You entered wrong customer id which does not exist.\n")
        else:
            cursor.execute("SELECT COUNT(*) FROM SALES WHERE CID='"+str(c41)+"'")
            data=cursor.fetchone()
            if data[0]>0:
                cursor.execute("SELECT * FROM SALES WHERE CID=('"+str(c41)+"')")
                data=cursor.fetchall()
                print("All sales made by a customer",c41,":-")
                print(saleslist)
                for i in data:
                    print(i[0],"        | ",i[1],"         | ",i[2],"        | ",i[3],"        | ",i[4],"   | ",i[5]," "*(15-len(i[5]))," | ",i[6],sep="")
            else:
                print("This customer has not purchased any item.\n")

        mycon.commit()
    except:
        print("something went wrong please try again , so for the inconvenience.\n")

def customer5(c51):
    try:
        cursor.execute("SELECT CID FROM CUSTOMER")
        info=cursor.fetchall()
        if (c51,) not in info:
            print("You entered wrong customer id which does not exist.\n")
        else:
            cursor.execute("SELECT * FROM CUSTOMER WHERE CID='"+str(c51)+"'")
            i=cursor.fetchone()
            print("Details of customer with customer id:-",c51)
            print(customerlist)
            print(i[0],"        | ",i[1]," "*(15-len(i[1]))," | ",i[2],"         | ",i[3],sep="")
        mycon.commit()
    except:
        print("something went wrong please try again , so for the inconvenience.\n")

def customer6():
    try:
        cursor.execute("SELECT * FROM CUSTOMER")
        data= cursor.fetchall()
        print(customerlist)
        for i in data:
            print(i[0],"        | ",i[1]," "*(15-len(i[1]))," | ",i[2],"         | ",i[3],sep="")
    except:
        print("something went wrong please try again , so for the inconvenience.\n")

def stock():
    a=1
    while a==1:
        print("\n------STOCK MENU-----")
        print("1-To add a product")
        print("2-To delete a product")
        print("3-Modify a product")
        print("4-To check the stock status of a product")
        print("5-To check the stock status of all product.\n")
        cha=int(input("Enter your choice from stock menu:-"))
        if cha==1:
            print("You choose to add a product option.\n")
            stock1()
        elif cha==2:
            print("You choose to delete a product.\n")
            a2=int(input("Enter the product id to delete product:-"))
            stock2(a2)
        elif cha==3:
            print("You choose to modify a product.\n")
            a3=int(input("Enter the product id to modify a product:-"))
            stock3(a3)
        elif cha==4:
            print("You choose to check the stock status of a product.\n")
            a4=int(input("Enter the product id to check stock status product:-"))
            stock4(a4)
        elif cha==5:
            print("You choose to check the stock status of all product.\n")
            stock5()
        else:
            print("You enter a wrong choice.\n")
        a=int(input("Enter 1 if you want to continue the stock menu:-"))
    print("Thanks for using stock menu.\n")

def sales():
    b=1
    while b==1:
        print("\n------SALES MENU-----")
        print("1-To register a new sale.")
        print("2-To check sale by date.")
        print("3-To check sale by customer id.")
        print("4-To check sale by product id.")
        print("5-To show total gst collection.\n")
        chb=int(input("Enter your choice from sales menu:-"))
        if chb==1:
            print("You choose to register a new sale.\n")
            sales1()
        elif chb==2:
            print("You choose to check sale by date.\n")
            b2=input("Enter the date in (YYYY-MM-DD):-")
            sales2(b2)
        elif chb==3:
            print("You choose to check sale by customer id.\n")
            b3=int(input("Enter the customer id:-"))
            sales3(b3)
        elif chb==4:
            print("You choose to check sale by product id.\n")
            b4=int(input("Enter the product id:-"))
            sales4(b4)
        elif chb==5:
            print("You choose to show total gst collection.\n")
            b51=input("Enter the starting date in (YYYY-MM-DD):-")
            b52=input("Enter the ending date in (YYYY-MM-DD):-")
            sales5(b51,b52)
        else:
            print("You enter a wrong choice.\n")
        b=int(input("Enter 1 if you want to continue the sales menu:-"))
    print("Thanks for using sales menu.\n")

def customer():
    c=1
    while c==1:
        print("\n------CUSTOMER MENU-----")
        print("1-To add a new customer")
        print("2-To delete a customer")
        print("3-Modify the details of a customer")
        print("4-To see customer all purchase details")
        print("5-To see details of customer")
        print("6-To see details of all customer.\n")
        chc=int(input("Enter your choice from customer menu:-"))
        if chc==1:
            print("You choose to add a new customer.\n")
            customer1()
        elif chc==2:
            print("You choose to delete a customer.\n")
            c2=int(input("Enter the customer id:-"))
            customer2(c2)
        elif chc==3:
            print("You choose to modify details of a customer.\n")
            c3=int(input("Enter the customer id:-"))
            customer3(c3)
        elif chc==4:
            print("You choose to see customer all purchase details.\n")
            c4=int(input("Enter the customer id:-"))
            customer4(c4)
        elif chc==5:
            print("You choose to see details of customer.\n")
            c5=int(input("Enter the customer id:-"))
            customer5(c5)
        elif chc==6:
            print("You choose to see details of all customer")
            customer6()
        else:
            print("You enter a wrong choice.\n")
        c=int(input("Enter 1 if you want to continue the customer menu:-"))
    print("Thanks for using customer menu.\n")


d=1
ch=0
while d==1:
    print("\n-----WELCOME TO SALES MANAGER-----")
    print("-----MENU-----")
    print("1-To use the stock menu")
    print("2-To use the customer menu")
    print("3-To use the sales menu.\n")
    ch=int(input("Enter your choice from menu:-"))
    if ch==1:
        print("You choose to go stock menu.\n")
        stock()
    elif ch==3:
        print("You choose to go sales menu.\n")
        sales()
    elif ch==2:
        print("You choose to go customer menu.\n")
        customer()
    else:
         print("You enter a wrong choice.\n")
    d=int(input("Enter 1 if you want to continue the menu for sales manager:-"))
print("Thanks for using the menu for sales manager")

mycon.commit()




