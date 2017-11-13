#############################################################################################
# Group Members: Peyton Sidders, Darrell D., Jordan Morris                                 ##
# Date: 10/20/2017                                                                         ##
# Description: Group Project 1                                                             ##
#############################################################################################
from tkinter import*
import sys 
from time import sleep
from random import randint
from random import shuffle
import RPi.GPIO as GPIO
import pygame

class Project(Frame):
    #setting each directory to be global
    global Puzzle1
    global Puzzle2
    global Puzzle3
    global Puzzle4
    global Win
    global Lose
    global setupGUI
    global debugging
    global Final
    
    
    #debug back and next buttons if True
    debugging = False
    
    #runs Puzzle 1 
    def Puzzle1():
        led = [16,21,20]          #setup GPIO 
        GPIO.setwarnings(False)
        GPIO.setmode(GPIO.BCM)
        GPIO.setup(led, GPIO.OUT)
        
        canvas = Canvas(window, width = WIDTH, height=HEIGHT, bg = 'black') #creates canvas and image to be displayed in background
        canvas.place(x=0,y=0)
        img = PhotoImage(file = "p2.gif")
        stscreen = Label(window, image = img)
        stscreen.image = img
        stscreen.place(x = 0, y=0)

        global lives           #calls lives, solution, and args to be accessed later to be accessed later
        global solution
        global arg
        textinput = []  #creates a string to be used to store the input
        arg = StringVar()
        solution1 = 0
        lives = 3
        GPIO.output(led[0], 1) #turns on the lives LED's
        GPIO.output(led[1], 1)
        GPIO.output(led[2], 1)
        label = Label(window, text = "Lives: {}".format(lives)).place(x=WIDTH/2-50, y=HEIGHT/2)
        def submit():
            global lives
            global solution
            global arg
            d = str(arg.get()) #retrieves the text stored in the entry p1input
            k = str(solution1) #retrieves the answer calculated and stored in solution1
            if (d == k): #if theyre the same, call puzzle 2
                Puzzle2()
                    
            else:
                #if false decrement lives and change the lives lights depending on the lives left
                lives = lives - 1
                label = Label(window, text = "Lives: {}".format(lives))\
                        .place(x=WIDTH/2-50, y=HEIGHT/2)
                if lives == 3:
                    GPIO.output(led[0], 1)
                    GPIO.output(led[1], 1)
                    GPIO.output(led[2], 1)
                elif lives == 2:
                    GPIO.output(led[0], 1)
                    GPIO.output(led[1], 1)
                    GPIO.output(led[2], 0)
                elif lives == 1:
                    GPIO.output(led[0], 1)
                    GPIO.output(led[1], 0)
                    GPIO.output(led[2], 0)
                elif lives == 0:
                    GPIO.output(led[0], 0)
                    GPIO.output(led[1], 0)
                    GPIO.output(led[2], 0)
                    Lose()

                    
        b1= []   #creates lists used to create the two random binary numbers
        b2=[]
        for i in range (0,9):   #uses a for loop to create two 9-bit numbers
            b1.append(str(randint(0,1)))
            b2.append(str(randint(0,1)))
                
        z = ''.join(b1)     #joins the list b1 to create a string for binary number 1
        y = ''.join(b2)      #same for b2
           
        solution1 = int(z,2) + int(y,2)     #sum (answer)

        p1input = Entry(window, textvariable = arg).place(x=310, y = 100) #creates the entry, label, and button widgets and places them
        p1label = Label(window, text = 'Please add these two binary numbers and enter the decimal representation: {} + {}'.format(z,y),font = (None, 12), fg = 'green', bg = 'black').place(x=60, y=30)
        p1button = Button(window, text = 'Submit' , command=submit, fg = 'green' , bg = 'black').place(x=350, y=200)
           
        


        #Debug buttons
        if (debugging == True):
            nextbut = Button (window, text ="next", command = lambda: Puzzle2())
            nextbut.grid(row=0, column=1)
            backbut = Button (window, text ="back", command = lambda: setupGUI())
            backbut.grid(row=0, column=0)
        
        print ("Puzzle 1")
        
    #runs Puzzle 2
    def Puzzle2():
        canvas = Canvas(window, width = WIDTH, height=HEIGHT, bg = 'black')
        canvas.place(x=0,y=0)
        welcome = Label(window, text = "Welcome to Mimic", bg = 'black' , fg = 'green')
        welcome.place(x=WIDTH/2-65,y=100)
        instruction = Label(window, text = "Instructions:\n\n As the LEDs light up press"\
                            +" the corresponding colored button.",bg = 'black', fg = 'green')
        instruction.place(x=WIDTH/2-175, y=250)
        creator = Label(window, text = "Creator: Jordan Morris", bg = 'black', fg = 'green')
        creator.place(x=WIDTH-125, y=HEIGHT-20)
        GPIO.cleanup()
        def run():
        #setting the inputs and outputs of the circuit
            led = [5,6,13,19,16,21,20]
            button = [4,17,27,22]
            
            GPIO.setwarnings(False)
            GPIO.setmode(GPIO.BCM)
            GPIO.setup(button, GPIO.IN, pull_up_down=GPIO.PUD_UP)
            GPIO.setup(led, GPIO.OUT)
            GPIO.setwarnings(False)
            
            counter = 0 
            lives = 3
            print("You have 3 lives")
            s = 30
            GPIO.output(led[4],True)
            GPIO.output(led[5],True)
            GPIO.output(led[6],True)
            
            while (True):
                time = 0
                lights = []
                lights.append(randint(0,3))
                
                for l in lights:
                    for i in range(s):
                        if (GPIO.input(button[l]) == GPIO.LOW):
                            print ("You Got it")
                            sleep(.5)
                            counter += 1
                            time += 1
                            print("Counter: {}".format(counter))
                            break
                            
                        else:
                            GPIO.output(led[l],True)
                            sleep(.1)
                    GPIO.output(led[l], False)
                    if (time == 0):
                        lives -= 1
                        print ("Lives: {}".format(lives))
                    sleep(s/10)
                
                
                if (lives==2):
                    GPIO.output(led[3],False)
                    sleep(0.1)
                    
                    
                elif (lives==1):
                
                    
                    GPIO.output(led[4],False)
                    sleep(0.1)
                    
                elif (lives==0):
                    
                    GPIO.output(led[5],False)
                    sleep(.1)
                    print("You ran out of lives")
                    sleep(2)
                    Lose()
                    break
                    
                
                if (counter >= 5 and counter < 10):
                    s = 20
                    
                if (counter >= 10 and counter < 15):
                    s = 15
                    
                if (counter >=15 and counter <20):
                    s = 10
                    
                if (counter >=20 and counter < 24):
                    s = 10
                if (counter == 25):
                    print("You won this round")
                    for i in range(5):
                        GPIO.output(led[0],True)
                        sleep(.5)
                        GPIO.output(led[0],False)
                        sleep(.1)
                        GPIO.output(led[1],True)
                        sleep(.5)
                        GPIO.output(led[1],False)
                        sleep(.1)
                        GPIO.output(led[2],True)
                        sleep(.5)
                        GPIO.output(led[2],False)
                        sleep(.1)
                    GPIO.output(led[0],True)
                    GPIO.output(led[1],True)
                    GPIO.output(led[2],True)
                    sleep(5)
                    GPIO.cleanup()
                    Puzzle3()            
        begin = Button(window, text = "Begin", command = lambda: run())
        begin.place(x=362,y=300)

        #Debug buttons
        if (debugging == True):
            nextbut = Button (window, text ="next", command = lambda: Puzzle3())
            nextbut.grid(row=0, column=1)
            backbut = Button (window, text ="back", command = lambda: Puzzle1())
            backbut.grid(row=0, column=0)
        print ("Puzzle 2")

    #runs Puzzle 3
    def Puzzle3():
        led = [4,17,27,16,21,20]
            
        GPIO.setwarnings(False)
        GPIO.setmode(GPIO.BCM)
        GPIO.setup(led, GPIO.OUT)
        GPIO.setwarnings(False)
        
        o = True
        canvas = Canvas(window, width = WIDTH, height=HEIGHT, bg = 'black')
        canvas.place(x=0,y=0)
        
        mylist = ["What is at the end of a rainbow?", "What word becoes shorter when you add two letters to it?",  "What travels around the world but stays in one spot?","What has 4 eyes but can't see?"]
        solution =['The letter w', 'Short', 'A stamp', 'Mississipi'] # list of riddle solutions

        global lives # lives counter 
        lives = 3
        l4 = Label(window, text = "Lives: {}".format(lives), bg = 'black', fg = 'green', height = 3, font = 16).place(x = 5, y = 350) # formats label window and life placement
        x = solution
        y = mylist[randint(0, len(mylist)-1)] #variable that randomizes the riddles
        c = "Correct"

        GPIO.output(led[3],True) # representation of the lives
        GPIO.output(led[4],True)
        GPIO.output(led[5],True)
        
        def check(self):
            global lives
            if (y == mylist[self]):
                l4 = Label(window, text = c, bg = 'blue', fg ='white' ,height = 3, font =(c,16)).place(x = 100, y = 350) # winning label for continung to the next function
                Continue = Button(window, text = "Continue to next Puzzle", font = 23, command = lambda:Puzzle4()).place(x = 200, y =350)
                GPIO.cleanup()
            else:
                lives -= 1 # how you losing lives affect the GPIO
                l4 = Label(window, text = "Lives: {}".format(lives), bg = 'red', fg = 'white', height = 3, font = 16).place(x = 5, y =350)
                if (lives==2):
                    GPIO.output(led[3],False)
                    GPIO.output(led[4],True) #GPIO correlates with the lives if all three are false, it brings up the Lose() function
                    GPIO.output(led[5],True)
                elif (lives==1):
                    GPIO.output(led[3],False)
                    sleep(.1)
                    GPIO.output(led[4],False)
                    sleep(.1)
                    GPIO.output(led[5],True)
                elif (lives==0):
                    GPIO.cleanup()
                    Lose()
        
        l1 = Label(window, text = y , bg = 'black', fg = 'green',height = 4, font=(y, 23))
        l1.place(x = 5 , y = 10 )
        # placement of the buttons, and the style of each

        x0 = Button(window, text = x[0], bg = 'black', fg = 'green',height =4, font=(x,24), command = lambda:check(0)).place(x = 5  , y = 180) 
        x1 = Button(window, text = x[1], bg = 'black', fg = 'green',height =4, font=(x,24), command = lambda:check(1)).place(x = 184, y = 180)
        x2 = Button(window, text = x[2], bg = 'black', fg = 'green',height =4, font=(x,24), command = lambda:check(2)).place(x = 275, y = 180)
        x3 = Button(window, text = x[3], bg = 'black', fg = 'green',height =4, font=(x,24), command = lambda:check(3)).place(x = 405, y = 180)

            
        #Debug buttons
        if (debugging == True):
            nextbut = Button (window, text ="next", command = lambda: Puzzle4())
            nextbut.grid(row=0, column=1)
            backbut = Button (window, text ="back", command = lambda: Puzzle2())
            backbut.grid(row=0, column=0)
        
        print ("Puzzle 3")

    #runs Puzzle4
    def Puzzle4():
        #create the canvas
        canvas = Canvas(window, width = WIDTH, height=HEIGHT, bg = 'black')
        canvas.place(x=0,y=0)
        
        #create the variables that will be used as text variables in the entry widget.
        entry1 = StringVar()
        entry2 = StringVar()
        entry3 = StringVar()
        entry4 = StringVar()
        entry5 = StringVar()
        entry6 = StringVar()
        entry7 = StringVar()
        entry8 = StringVar()
        entry9 = StringVar()
        entry10 = StringVar()
        entry11 = StringVar()
        entry12 = StringVar()
        entry13 = StringVar()
        entry14 = StringVar()
        entry15 = StringVar()
        entry16 = StringVar()
        entry17 = StringVar()
        entry18 = StringVar()
        entry19 = StringVar()
        entry20 = StringVar()
        entry21 = StringVar()
        entry22 = StringVar()
        entry23 = StringVar()
        entry24 = StringVar()
        entry25 = StringVar()
        entry26 = StringVar()
        entry27 = StringVar()
        entry28 = StringVar()
        entry29 = StringVar()
        entry30 = StringVar()
        entry31 = StringVar()
        entry32 = StringVar()
        entry33 = StringVar()
        entry34 = StringVar()
        entry35 = StringVar()
        entry36 = StringVar()
        entry37 = StringVar()
        entry38 = StringVar()
                    
                   
                    
        
                    
                    
            
                    
        
        #create the entry widgets and assign the text variables that were created above
        position1 = Entry(window,textvariable = entry1, width = 2)
        position2 = Entry(window,textvariable = entry2, width = 2)
        position3 = Entry(window,textvariable = entry3, width = 2)
        position4 = Entry(window,textvariable = entry4, width = 2)
        position5 = Entry(window,textvariable = entry5, width = 2)
        position6 = Entry(window,textvariable = entry6, width = 2)
        position7 = Entry(window,textvariable = entry7, width = 2)
        position8 = Entry(window,textvariable = entry8, width = 2)
        position9 = Entry(window,textvariable = entry9, width = 2)
        position10 = Entry(window,textvariable = entry10, width = 2)
        position11 = Entry(window,textvariable = entry11, width = 2)
        position12 = Entry(window,textvariable = entry12, width = 2)
        position13 = Entry(window,textvariable = entry13, width = 2)
        position14 = Entry(window,textvariable = entry14, width = 2)
        position15 = Entry(window,textvariable = entry15, width = 2)
        position16 = Entry(window,textvariable = entry16, width = 2)
        position17 = Entry(window,textvariable = entry17, width = 2)
        position18 = Entry(window,textvariable = entry18, width = 2)
        position19 = Entry(window,textvariable = entry19, width = 2)
        position20 = Entry(window,textvariable = entry20, width = 2)
        position21 = Entry(window,textvariable = entry21, width = 2)
        position22 = Entry(window,textvariable = entry22, width = 2)
        position23 = Entry(window,textvariable = entry23, width = 2)
        position24 = Entry(window,textvariable = entry24, width = 2)
        position25 = Entry(window,textvariable = entry25, width = 2)
        position26 = Entry(window,textvariable = entry26, width = 2)
        position27 = Entry(window,textvariable = entry27, width = 2)
        position28 = Entry(window,textvariable = entry28, width = 2)
        position29 = Entry(window,textvariable = entry29, width = 2)
        position30 = Entry(window,textvariable = entry30, width = 2)
        position31 = Entry(window,textvariable = entry31, width = 2)
        position32 = Entry(window,textvariable = entry32, width = 2)
        position33 = Entry(window,textvariable = entry33, width = 2)
        position34 = Entry(window,textvariable = entry34, width = 2)
        position35 = Entry(window,textvariable = entry35, width = 2)
        position36 = Entry(window,textvariable = entry36, width = 2)
        position37 = Entry(window,textvariable = entry37, width = 2)
        position38 = Entry(window,textvariable = entry38, width = 2)
        #place them in the order of the following commented words.
        #though
        h = 300
        position1.place(x=5, y = h)
        position2.place(x=23, y = h)
        position3.place(x=41, y = h)
        position4.place(x=59, y = h)
        position5.place(x=77, y = h)
        position6.place(x=95, y = h)
        #this
        position7.place(x=120, y = h)
        position8.place(x=138, y = h)
        position9.place(x=156, y = h)
        position10.place(x=174, y = h)
        #be
        position11.place(x=204, y = h)
        position12.place(x=222, y = h)
        #madness
        position13.place(x=252, y = h)
        position14.place(x=270, y = h)
        position15.place(x=288, y = h)
        position16.place(x=306, y = h)
        position17.place(x=324, y = h)
        position18.place(x=342, y = h)
        position19.place(x=360, y = h)
        #yet
        position20.place(x=390, y = h)
        position21.place(x=408, y = h)
        position22.place(x=426, y = h)
        #there
        position23.place(x=456, y = h)
        position24.place(x=474, y = h)
        position25.place(x=492, y = h)
        position26.place(x=510, y = h)
        position27.place(x=528, y = h)
        #be
        position28.place(x=558, y = h)
        position29.place(x=576, y = h)
        #method
        position30.place(x=606, y = h)
        position31.place(x=624, y = h)
        position32.place(x=642, y = h)
        position33.place(x=660, y = h)
        position34.place(x=678, y = h)
        position35.place(x=696, y = h)
        #int
        position36.place(x=5, y = h+20)
        position37.place(x=23, y = h+20)
        position38.place(x=41, y = h+20)

        # all positions stored in  a list to be used to disable, or clear in the for loops below
        possible = []

        possible.append(position1)
        possible.append(position2)
        possible.append(position3)
        possible.append(position4)
        possible.append(position5)
        possible.append(position6)
        possible.append(position7)
        possible.append(position8)
        possible.append(position9)
        possible.append(position10)
        possible.append(position11)
        possible.append(position12)
        possible.append(position13)
        possible.append(position14)
        possible.append(position15)
        possible.append(position16)
        possible.append(position17)
        possible.append(position18)
        possible.append(position19)
        possible.append(position20)
        possible.append(position21)
        possible.append(position22)
        possible.append(position23)
        possible.append(position24)
        possible.append(position25)
        possible.append(position26)
        possible.append(position27)
        possible.append(position28)
        possible.append(position29)
        possible.append(position30)
        possible.append(position31)
        possible.append(position32)
        possible.append(position33)
        possible.append(position34)
        possible.append(position35)
        possible.append(position36)
        possible.append(position37)
        possible.append(position38)
        
        def timer(time):         #timer function that counts down every second

              
            if (time > 0):   #if time > 0 create a label widget displaying the time
                label = Label(window, text = time, bg = 'black', fg = 'green', width = 20)
                label.place(x=300,y=100)

                global stop
                stop = window.after(1000,timer,time-1)   #then wait 1000 milliseconds call the timer function and decrement time
                    
                        
            if time == 0:  #if time reaches zero call the lose function
                Lose()

        
        def submit4():  #everytime the submit button is clicked this function is called
                

                    
            entries = []
                    
        #retrieve all current entries and append them to the newly created entries list
                    
            entries.append(entry1.get())
            entries.append(entry2.get())
            entries.append(entry3.get())
            entries.append(entry4.get())
            entries.append(entry5.get())
            entries.append(entry6.get())
            entries.append(entry7.get())
            entries.append(entry8.get())
            entries.append(entry9.get())
            entries.append(entry10.get())
            entries.append(entry11.get())
            entries.append(entry12.get())
            entries.append(entry13.get())
            entries.append(entry14.get())
            entries.append(entry15.get())
            entries.append(entry16.get())
            entries.append(entry17.get())
            entries.append(entry18.get())
            entries.append(entry19.get())
            entries.append(entry20.get())
            entries.append(entry21.get())
            entries.append(entry22.get())
            entries.append(entry23.get())
            entries.append(entry24.get())
            entries.append(entry25.get())
            entries.append(entry26.get())
            entries.append(entry27.get())
            entries.append(entry28.get())
            entries.append(entry29.get())
            entries.append(entry30.get())
            entries.append(entry31.get())
            entries.append(entry32.get())
            entries.append(entry33.get())
            entries.append(entry34.get())
            entries.append(entry35.get())
            entries.append(entry36.get())
            entries.append(entry37.get())
            entries.append(entry38.get())
            
            #creates the list of the correct solutions AND the list used to tell when the puzzle is over
            final = []
            solution = ["t","h","o","u","g","h","t","h","i","s","b","e","m","a","d","n","e","s","s","y","e","t","t","h","e","r","e","b","e","m","e","t","h","o","d","i","n","t"]
            
            #check the entries in solution list and compares them to the entries in the list entries.
            for i in range (0,38):
                if (solution[i] == entries[i]):   #if they are the same, disable the widget and append a 1 to the final list
                    possible[i].config(state = DISABLED)
                    final.append("1")
                else:  #else the entry at position i gets cleared.
                    possible[i].delete(0,END)
                        
                    
            #once the list has a length of 38 (AKA all entries correct) then call the next function
            if (len(final) == 38):
                window.after_cancel(stop)
                Win()   
                
                
        #creates the text and submit button
        label = Label(window, text = 'Fill in the missing letters to complete the phrase. But hurry, if time runs out, you lose.',bg = 'black', fg = 'green', font = (None, 12)).place(x=50, y=10)
        label = Label(window, text = 'HINT: From "Hamlet", by Shakespeare',bg = 'black', fg = 'green', font = (None, 12)).place(x=250, y=30)
        sbutton = Button(window,text = "Submit", command = submit4).place(x=300,y=350)
        

                    
        
                        
    
                    
                        
        timer(300) #calls the timer function for 300 seconds

        #Debug buttons
        if (debugging == True):
            nextbut = Button (window, text ="next", command = lambda: Win())
            nextbut.grid(row=0, column=1)
            backbut = Button (window, text ="back", command = lambda: Puzzle3())
            backbut.grid(row=0, column=0)
        
        print ("Puzzle 4")

    #win function if the player beats the game
    def Win():

        
        canvas = Canvas(window, width = WIDTH, height=HEIGHT, bg = 'black') #creates the widgets and places them
        canvas.place(x=0,y=0)
        label = Label(window, text = "Now you must enter the correct key to win the game!",fg = "green", bg = "black", font = (None, 13))
        label.place(x=180,y=200)
        entry = Entry(window, width = 50)
        entry.place(x=200, y = 240)
        
            #a listt of many characters used to create the key
        possible = ['1','2','3','4','5','6','7','8','9','Q','W','E','R','T','Y','U','I','O','P','A','S','D','F','G','H','J','K','L','Z','X','C','V','B','N','M','q','w','e','r','t','y','u','i','o','p','a','s','d','f','g','h','j','k','l','z','x','c','v','b','n','m']
        key = []
        for i in range(15): #for loop choosing a random index from possible and appending it  to list key
            x = randint(0,60)
            key.append(possible[x])


        z = ''.join(key) #turns list 'key' into a string
        
        def printkey():
            label1 = Label(window, text = z,fg = "green", bg = "black")  #waits 7 seconds then displays the key on screen
            label1.place(x= 320, y= 350)
        window.after(7000, printkey)
        print(z)
        def submitwin():#called when the submit button is clicked on the win screen
            i = entry.get()   #retrieves the entry from the widget
            if i == z:    #checks if the entry and key are the same
                Final()     #calls final screen
            else:
                Lose()

        button = Button(window, text = "Submit", command = submitwin, bg = "green")
        button.place(x=335,y=280)




        #Debug buttons
        if (debugging == True):
            nextbut = Button (window, text ="next", command = lambda: Lose())
            nextbut.grid(row=0, column=1)
            backbut = Button (window, text ="back", command = lambda: Puzzle4())
            backbut.grid(row=0, column=0)
        print ("You Win")

    #lose function if Player lives = 0
    def Lose():
        canvas = Canvas(window, width = WIDTH, height=HEIGHT, bg = 'black')
        canvas.place(x=0,y=0)
        #creates the label and button widgets with the commands to quit or restart
        YOULOOSE = Label(window, text = "YOU LOST THE GAME TRY AGAIN NEXT TIME",fg = 'green', bg = 'black', font = 25).place(x=200, y=HEIGHT/2)
        restart = Button(window, text = "Restart?", command = lambda: setupGUI()).place(x=WIDTH/2, y=HEIGHT/4)
        quiter = Button(window, text = "Quit", command = lambda: quit()).place(x=300, y=HEIGHT/4)

        if (debugging == True):
            backbut = Button (window, text ="back", command = lambda: Puzzle4())
            backbut.grid(row=0, column=0)  
        print("You Lose")

    def Final():
        canvas = Canvas(window, width = WIDTH, height=HEIGHT, bg = 'black')
        canvas.place(x=0,y=0)
        label = Label(window, text = "You have successfully completed the game!",fg = "green", bg = "black", font = (None, 13))
        label.place(x=210,y=200)
        

    #using the servo to turn it to release the drawer latch
    def unlock_box():
        pass

    #using the servo to turn it to lock the drawer
    def lock_box():
        pass

    #create the start menu 
    def setupGUI():
        #importing and displaying the image
        img = PhotoImage(file = "p2.gif")
        stscreen = Label(window, image = img)
        stscreen.image = img
        stscreen.place(x = 0, y=0)

        #creating and setting the button to open Puzzle 1()
        label = Label(window, text = "Your mission, if you choose to accept it, is to solve the puzzles at all costs", bg = "black", fg = "green")
        label.place(x=200,y=40)
        startbutton = Button (window, text = "Accept",bg = 'green',bd = 5, \
                      command = lambda: Puzzle1())
        startbutton.place(x = 325, y = 240, width = 200, height = 75)

        print ("setupGUI")

    #debuging
    #change the default program to bypass the start menu
    def default_Puzzle(self):
        Win()
#creates the window and calls calls setupgui and runs window mainloop
WIDTH = 800
HEIGHT = 480
window = Tk()
window.title("Project Please Anky")
p = Project(window)
p.default_Puzzle()
window.mainloop()
