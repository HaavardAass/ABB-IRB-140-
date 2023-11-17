
# ABB IRB 140 colour and shape detection

For this project we created a code for picture processing in OpenCV and socket communication with ABB IRB 140, through robotstudio.
The project has mainly been focused around image processing, to get the coordinates to be as accurate possible, in multiple lightning scenes. 

###
First we import the necesarry modules for the program.
```Python
import numpy as np
import cv2
import tkinter as tk
import socket
import os
```
Now we create a class **IRB140ColorDetectionProgram** with TkInter, to create the necessary pages for the user interface. 

```Python
class IRB140ColorDetectionProgram(tk.Tk):
    def __init__(self, *args, **kwargs):
        tk.Tk.__init__(self, *args, **kwargs)
        self.title("IRB 140 Color Detection Program")
        container = tk.Frame(self)
        container.pack(side="top", fill="both", expand=True)
        container.grid_rowconfigure(0, weight=1)
        container.grid_columnconfigure(0, weight=1)
        self.frames = {}
        for F in (StartPage, Orange_robot, White_robot, Calibrate_colours, Run_Program_Orange, Run_Program_white):
            frame = F(container, self)
            self.frames[F] = frame
            frame.grid(row=0, column=0, sticky="nsew")
        self.show_frame(StartPage)
    def show_frame(self, cont):
        frame = self.frames[cont]
        frame.tkraise()

```    
Next we created class **StartPage** for the program, where you would choose which robot you are on, either white or orange. It is necesarry to send you to the correct robot to get both the correct image size, as well as the right relation between mm and px. If you press the button for orange robot, you will be brought to class **Orange_robot**. If you press the button for white robot, you will be brought to class **White_robot**.

```Python
class StartPage(tk.Frame):
    def __init__(self, parent, controller):
        tk.Frame.__init__(self, parent)
        image = tk.PhotoImage(file="C:\\Users\\s362154\\Downloads\\ABB_GUI\\ABB\\IRB140_color.png")
        background_label = tk.Label(self, image=image)
        background_label.image = image
        background_label.place(x=0, y=0, relwidth=1, relheight=1)
        # Create the button image for calibration
        button_image = tk.PhotoImage(file="C:\\Users\\s362154\\Downloads\\ABB_GUI\\ABB\\Button_orange_robot.png")
        button = tk.Button(self, image=button_image, command=lambda: controller.show_frame(Orange_robot))
        button.image = button_image
        button.place(x=20, y=670)  # Adjust the placement of the button within the frame
        # Create the button image for calibration
        button_image = tk.PhotoImage(file="C:\\Users\\s362154\\Downloads\\ABB_GUI\\ABB\\Button_White_robot.png")
        button_2 = tk.Button(self, image=button_image, command=lambda: controller.show_frame(White_robot))
        button_2.image = button_image
        button_2.place(x=1200, y=670)  # Adjust the placement of the button within the frame
```
If you press the button for orange robot you will be brought to class Orange_robot. This is a menu for the orange robot, whre you can choose between 3 buttons. **1:** Colour Calibration, **2:** Run Program or **3:** Change Robot. First button will send you to the class **Calibrate_colours**, where you could calibrate the values for colour detection. This is necesarry if the light scenario changes. The second Button sends you to class **Run_Program_Orange**, where you send the coordinates, shapes and colours to the robot controller. Third button sends you to class **White_robot**, if you pressed the wrong button on the **StartPage**

```Python
class Orange_robot(tk.Frame):
    def __init__(self, parent, controller):
        tk.Frame.__init__(self, parent)
        # Create and place the background image
        background_image = tk.PhotoImage(file="C:\\Users\\s362154\\Downloads\\ABB_GUI\\ABB\\ABB_Orange.png")
        background_label = tk.Label(self, image=background_image)
        background_label.image = background_image
        background_label.place(x=0, y=0, relwidth=1, relheight=1)
        # Create the button image for calibration
        button_image = tk.PhotoImage(file="C:\\Users\\s362154\\Downloads\\ABB_GUI\\ABB\\Button_colour_calibration.png")
        button = tk.Button(self, image=button_image, command=lambda: controller.show_frame(Calibrate_colours))
        button.image = button_image
        button.place(x=60, y=80)  # Adjust the placement of the button within the frame
        # Create the button image for calibration
        button_image = tk.PhotoImage(file="C:\\Users\\s362154\\Downloads\\ABB_GUI\\ABB\\Button_run_program.png")
        button = tk.Button(self, image=button_image, command=lambda: controller.show_frame(Run_Program_Orange))
        button.image = button_image
        button.place(x=60, y=230)  # Adjust the placement of the button within the frame
        # Create the button image for calibration
        button_image = tk.PhotoImage(file="C:\\Users\\s362154\\Downloads\\ABB_GUI\\ABB\\Button_change_robot.png")
        button = tk.Button(self, image=button_image, command=lambda: controller.show_frame(White_robot))
        button.image = button_image
        button.place(x=60, y=380)  # Adjust the placement of the button within the frame
```
If you press the button for white robot you will be brought to class Orange_robot. This is a menu for the orange robot, whre you can choose between 3 buttons. **1:** Colour Calibration, **2:** Run Program or **3:** Change Robot. First button will send you to the class **Calibrate_colours**, where you could calibrate the values for colour detection. This is necesarry if the light scenario changes. The second Button sends you to class **Run_Program_Orange**, where you send the coordinates, shapes and colours to the robot controller. Third button sends you to class **Orange_robot**, if you pressed the wrong button on the **StartPage**

```Python
class White_robot(tk.Frame):
    def __init__(self, parent, controller):
        tk.Frame.__init__(self, parent)
        # Create and place the background image
        background_image = tk.PhotoImage(file="C:\\Users\\s362154\\Downloads\\ABB_GUI\\ABB\\ABB_White.png")
        background_label = tk.Label(self, image=background_image)
        background_label.image = background_image
        background_label.place(x=0, y=0, relwidth=1, relheight=1)
        # Create the button image for calibration
        button_image = tk.PhotoImage(file="C:\\Users\\s362154\\Downloads\\ABB_GUI\\ABB\\Button_colour_calibration.png")
        button = tk.Button(self, image=button_image, command=lambda: controller.show_frame(Calibrate_colours))
        button.image = button_image
        button.place(x=60, y=80)  # Adjust the placement of the button within the frame
        # Create the button image for calibration
        button_image = tk.PhotoImage(file="C:\\Users\\s362154\\Downloads\\ABB_GUI\\ABB\\Button_run_program.png")
        button = tk.Button(self, image=button_image, command=lambda: controller.show_frame(Run_Program_white))
        button.image = button_image
        button.place(x=60, y=230)  # Adjust the placement of the button within the frame
        # Create the button image for calibration
        button_image = tk.PhotoImage(file="C:\\Users\\s362154\\Downloads\\ABB_GUI\\ABB\\Button_change_robot.png")
        button = tk.Button(self, image=button_image, command=lambda: controller.show_frame(Orange_robot))
        button.image = button_image
        button.place(x=60, y=380)  # Adjust the placement of the button within the frame
```
If you pressed the button colour calibration, you will be brough to class **Calibrate_colours**, where you can use trackbar sliders to calibrate the hsv values, so that class **Run_Program_Orange** and **Run_Program_white**, easily can track the colours and shapes, which will be sent to the robot controller. In this class, there are 4 buttons, which starts the right function for the correct colour to be calibrated. The last button on the bottom sends you back to the robot menu. The new values for the hsv, will be displayed underneath the button after the calibration is done at each channel. Remember to press **q** before closing the calibrate window. **We recommend to always start with a calibration, every time you open the program**. The program uses trackbars to asjust the tresholds of each colours mask. 
```Python
class Calibrate_colours(tk.Frame):
     def __init__(self, parent, controller):
         tk.Frame.__init__(self,parent)
         def detect_color_red():
            def nothing(x):
                pass
            cv2.namedWindow('Color Detection')
            # Red trackbars
            cv2.createTrackbar("Red_L - H", "Color Detection", 0, 255, nothing)
            cv2.createTrackbar("Red_L - S", "Color Detection", 240, 255, nothing)
            cv2.createTrackbar("Red_L - V", "Color Detection", 192, 255, nothing)
            cv2.createTrackbar("Red_U - H", "Color Detection", 26, 255, nothing)
            cv2.createTrackbar("Red_U - S", "Color Detection", 255, 255, nothing)
            cv2.createTrackbar("Red_U - V", "Color Detection", 255, 255, nothing)
            height = 800 
            width = 2048
            webcam = cv2.VideoCapture(0)
            webcam.set(cv2.CAP_PROP_FRAME_HEIGHT,height)
            webcam.set(cv2.CAP_PROP_FRAME_WIDTH,width)
            while True:
                _, imageFrame = webcam.read()
                hsvFrame = cv2.cvtColor(imageFrame, cv2.COLOR_BGR2HSV)  
                # Red trackbars
                red_lower = np.array([cv2.getTrackbarPos("Red_L - H", "Color Detection"),
                                      cv2.getTrackbarPos("Red_L - S", "Color Detection"),
                                      cv2.getTrackbarPos("Red_L - V", "Color Detection")])
                red_upper = np.array([cv2.getTrackbarPos("Red_U - H", "Color Detection"),
                                      cv2.getTrackbarPos("Red_U - S", "Color Detection"),
                                      cv2.getTrackbarPos("Red_U - V", "Color Detection")])
                red_mask = cv2.inRange(hsvFrame, red_lower, red_upper)
                kernel = np.ones((5, 5), "uint8")
                red_mask = cv2.dilate(red_mask, kernel)
                res_red = cv2.bitwise_and(imageFrame, imageFrame, mask=red_mask)
            
                cv2.putText(res_red, 'Color Calibration - Red', (10, 20), cv2.FONT_HERSHEY_SIMPLEX, 0.7, (255, 255, 255), 2)
                cv2.imshow("Color Calibration", res_red)
                if cv2.waitKey(1) & 0xFF == ord('q'):
                    global red_lower_updated
                    red_lower_updated = np.array(red_lower)
                    global red_upper_updated
                    red_upper_updated = np.array(red_upper)
                    self.red_values.config(text=f"Red Lower: {red_lower_updated} Red Upper: {red_upper_updated}")
                    webcam.release()
                    return red_lower_updated, red_upper_updated
                    break
            cv2.destroyAllWindows()
         def detect_color_blue():
            def nothing(x):
                pass
            cv2.namedWindow('Color Detection')
            # Blue trackbars
            cv2.createTrackbar("Blue_L - H", "Color Detection", 56, 255, nothing)
            cv2.createTrackbar("Blue_L - S", "Color Detection", 0, 255, nothing)
            cv2.createTrackbar("Blue_L - V", "Color Detection", 142, 255, nothing)
            cv2.createTrackbar("Blue_U - H", "Color Detection", 123, 255, nothing)
            cv2.createTrackbar("Blue_U - S", "Color Detection", 246, 255, nothing)
            cv2.createTrackbar("Blue_U - V", "Color Detection", 243, 255, nothing)
            height = 800 
            width = 2048
            webcam = cv2.VideoCapture(0)
            webcam.set(cv2.CAP_PROP_FRAME_HEIGHT,height)
            webcam.set(cv2.CAP_PROP_FRAME_WIDTH,width)
            while True:
                _, imageFrame = webcam.read()
                hsvFrame = cv2.cvtColor(imageFrame, cv2.COLOR_BGR2HSV)
                # Blue trackbars
                blue_lower = np.array([cv2.getTrackbarPos("Blue_L - H", "Color Detection"),
                                      cv2.getTrackbarPos("Blue_L - S", "Color Detection"),
                                      cv2.getTrackbarPos("Blue_L - V", "Color Detection")])
                blue_upper = np.array([cv2.getTrackbarPos("Blue_U - H", "Color Detection"),
                                      cv2.getTrackbarPos("Blue_U - S", "Color Detection"),
                                      cv2.getTrackbarPos("Blue_U - V", "Color Detection")])
       
                blue_mask = cv2.inRange(hsvFrame, blue_lower, blue_upper)
                kernel = np.ones((5, 5), "uint8")
                blue_mask = cv2.dilate(blue_mask, kernel)
                res_blue = cv2.bitwise_and(imageFrame, imageFrame, mask=blue_mask)
            
                cv2.putText(res_blue, 'Color Calibration - Blue', (10, 20), cv2.FONT_HERSHEY_SIMPLEX, 0.7, (255, 255, 255), 2)
                cv2.imshow("Color Calibration", res_blue)        
 
                if cv2.waitKey(1) & 0xFF == ord('q'):
                    global blue_lower_updated
                    blue_lower_updated = np.array(blue_lower)
                    global blue_upper_updated
                    blue_upper_updated = np.array(blue_upper)
                    self.blue_values.config(text=f"Blue Lower: {blue_lower} BlueUpper: {blue_upper}")
                    return blue_lower_updated, blue_upper_updated
                    webcam.release()
                    break
            cv2.destroyAllWindows()
         def detect_color_green():
            def nothing(x):
                pass
            cv2.namedWindow('Color Detection')
            # Red trackbars
            cv2.createTrackbar("Green_L - H", "Color Detection", 36, 255, nothing)
            cv2.createTrackbar("Green_L - S", "Color Detection", 213, 255, nothing)
            cv2.createTrackbar("Green_L - V", "Color Detection", 142, 255, nothing)
            cv2.createTrackbar("Green_U - H", "Color Detection", 47, 255, nothing)
            cv2.createTrackbar("Green_U - S", "Color Detection", 255, 255, nothing)
            cv2.createTrackbar("Green_U - V", "Color Detection", 255, 255, nothing)
            height = 800 
            width = 2048
            webcam = cv2.VideoCapture(0)
            webcam.set(cv2.CAP_PROP_FRAME_HEIGHT,height)
            webcam.set(cv2.CAP_PROP_FRAME_WIDTH,width)
            while True:
                _, imageFrame = webcam.read()
                hsvFrame = cv2.cvtColor(imageFrame, cv2.COLOR_BGR2HSV)
                # Red trackbars
                green_lower = np.array([cv2.getTrackbarPos("Green_L - H", "Color Detection"),
                                      cv2.getTrackbarPos("Green_L - S", "Color Detection"),
                                      cv2.getTrackbarPos("Green_L - V", "Color Detection")])
                green_upper = np.array([cv2.getTrackbarPos("Green_U - H", "Color Detection"),
                                      cv2.getTrackbarPos("Green_U - S", "Color Detection"),
                                      cv2.getTrackbarPos("Green_U - V", "Color Detection")])
                green_mask = cv2.inRange(hsvFrame, green_lower, green_upper)
                kernel = np.ones((5, 5), "uint8")
                green_mask = cv2.dilate(green_mask, kernel)
                res_green = cv2.bitwise_and(imageFrame, imageFrame, mask=green_mask)
            
                cv2.putText(res_green, 'Color Calibration - Green', (10, 20), cv2.FONT_HERSHEY_SIMPLEX, 0.7, (255, 255, 255), 2)
                cv2.imshow("Color Calibration", res_green)
                if cv2.waitKey(1) & 0xFF == ord('q'):
                    global green_lower_updated
                    green_lower_updated = np.array(green_lower)
                    global green_upper_updated                    
                    green_upper_updated = np.array(green_upper)
                    self.green_values.config(text=f"Green Lower: {green_lower} Green Upper: {green_upper}")
                    webcam.release()
                    return green_lower_updated, green_upper_updated
                    break
            cv2.destroyAllWindows()
         # Create labels to display color values
         tk.Frame.__init__(self, parent)
         image = tk.PhotoImage(file="C:\\Users\\s362251\\Downloads\\ABB_GUI\\ABB\\ABB.png")
         background_label = tk.Label(self, image=image)
         background_label.image = image
         background_label.place(x=0, y=0, relwidth=1, relheight=1)
         button_image = tk.PhotoImage(file="C:\\Users\\s362251\\Downloads\\ABB_GUI\\ABB\\Button_red_cal.png")
         button = tk.Button(self, image=button_image,command=detect_color_red)
         button.image = button_image
         button.place(x=60, y=80)  
         button_image = tk.PhotoImage(file="C:\\Users\\s362251\\Downloads\\ABB_GUI\\ABB\\Button_blue_cal.png")
         button = tk.Button(self, image=button_image,command=detect_color_blue)
         button.image = button_image
         button.place(x=620, y=80)  
         button_image = tk.PhotoImage(file="C:\\Users\\s362251\\Downloads\\ABB_GUI\\ABB\\Button_green_cal.png")
         button = tk.Button(self, image=button_image,command=detect_color_green)
         button.image = button_image
         button.place(x=1180, y=80) 
         button_image = tk.PhotoImage(file="C:\\Users\\s362251\\Downloads\\ABB_GUI\\ABB\\Button_rob.png")
         button = tk.Button(self, image=button_image,command=lambda: controller.show_frame(White_robot))
         button.image = button_image
         button.place(x=620, y=700) 
         self.red_values = tk.Label(self, text="Red Lower: None Upper: None ")
         self.red_values.place(x=60, y=650)
         self.red_values.lift
         self.blue_values = tk.Label(self, text="Blue Lower: None Upper: None")
         self.blue_values.place(x=620, y=650)
         self.blue_values.lift
         self.green_values = tk.Label(self, text="Green Lower: None Upper: None")
         self.green_values.place(x=1080, y=650)
         self.green_values.lift()
```
If you pressed the button Run Program, you will be sent to class **Run_Program_White**, which is the class, where we detect the center point of each shape, colour of the shape and the figure of the shape. The program starts when you press the button **Run Program**. First the program creates a socket to the robot controller. Then we set the frame size of the camera, and then it proceeds to do colour detection, shape detection and find the center point, which in the end will be sent over to the robot controller. The program first detect the colour of the object by threshould values, next the program looks at the contours to find the area of the object to determine a pick point. Nest the progrram looks for how many edges it can create within the object, to determine what shape the object is. At last we send data to the robot controller, which consist of no. of objects, colours of the objects, shape of the objects and pick points.
```Python
class Run_Program_white(tk.Frame):         
    def run_program(self):
        print("Hello form run_program")
        height = 600
        width = 1440  
        list_cont = []
        def main():
            print("Hello from main in run_program")
            client = socket_setup()
            white_rob = False
            mm_Px_rel_X, mm_Px_rel_Y = mmPx_rel(white_rob)
            
            webcam = init_cam()
            _, imageFrame = webcam.read()
            hsvFrame = cv2.cvtColor(imageFrame, cv2.COLOR_BGR2HSV) 
            # Access the red, blue, and green values from the calibration instance
            global red_lower_updated, red_upper_updated, blue_lower_updated, blue_upper_updated, green_lower_updated, green_upper_updated
            #red_lower = np.array([0, 222, 237], np.uint8) 
            #red_upper = np.array([17, 255, 255], np.uint8) 
            red_mask = cv2.inRange(hsvFrame, red_lower_updated, red_upper_updated) 
            #green_lower = np.array([15, 255, 140], np.uint8) 
            #green_upper = np.array([173, 255, 255], np.uint8) 
            green_mask = cv2.inRange(hsvFrame, green_lower_updated, green_upper_updated)
            #blue_lower = np.array([92, 191, 0], np.uint8) 
            #blue_upper = np.array([145, 255, 233], np.uint8) 
            blue_mask = cv2.inRange(hsvFrame, blue_lower_updated, blue_upper_updated)
            kernel = np.ones((5, 5), "uint8") 
            red_mask = cv2.dilate(red_mask, kernel) 
            res_red = cv2.bitwise_and(imageFrame, imageFrame,  
                                      mask = red_mask) 
            green_mask = cv2.dilate(green_mask, kernel) 
            res_green = cv2.bitwise_and(imageFrame, imageFrame, 
                                        mask = green_mask) 
            blue_mask = cv2.dilate(blue_mask, kernel) 
            res_blue = cv2.bitwise_and(imageFrame, imageFrame, 
                                       mask = blue_mask) 
            contours, hierarchy = cv2.findContours(red_mask, 
                                                   cv2.RETR_TREE, 
                                                   cv2.CHAIN_APPROX_SIMPLE) 
        
            for pic, contour in enumerate(contours):
                area = cv2.contourArea(contour) 
                if(area > 2000): 
                    M = cv2.moments(contour)
                    cx = int(M['m01']/(M['m00']+ 1e-5))
                    cy = int(M['m10']/(M['m00']+ 1e-5))
                    x_cord_robot = round(cx*mm_Px_rel_X)
                    y_cord_robot = round(cy*mm_Px_rel_Y)
                    print("x, y: ", x_cord_robot, y_cord_robot)
                    approx = cv2.approxPolyDP(contour, 0.02*cv2.arcLength(contour, True), True)
                    x = approx.ravel()[0]
                    y = approx.ravel()[1]
                    cv2.drawContours(imageFrame, [approx], 0, (0, 0, 0), 5)
                    if len(approx) == 4:
                        list_cont.append(("red", "square", str(x_cord_robot), str(y_cord_robot)))
                        cv2.putText(imageFrame, "Square", (x, y), cv2.FONT_HERSHEY_SIMPLEX, 1, (0, 0, 0))
                    elif 7 < len(approx) < 20:
                        list_cont.append(("red", "circle", str(x_cord_robot), str(y_cord_robot)))
                        cv2.putText(imageFrame, "Circle", (x, y), cv2.FONT_HERSHEY_SIMPLEX, 1, (0, 0, 0))
                    x, y, w, h = cv2.boundingRect(contour)
                    imageFrame = cv2.rectangle(imageFrame, (x, y),  
                                               (x + w, y + h),  
                                               (0, 0, 255), 2)
                    cv2.putText(imageFrame, "red", (x, y), 
                                cv2.FONT_HERSHEY_SIMPLEX, 1.0, 
                                (0, 0, 255))     
            contours, hierarchy = cv2.findContours(green_mask, 
                                                   cv2.RETR_TREE, 
                                                   cv2.CHAIN_APPROX_SIMPLE)
            for pic, contour in enumerate(contours): 
                area = cv2.contourArea(contour) 
                if(area > 2000): 
                    M = cv2.moments(contour)
                    cx = int(M['m01']/(M['m00']+ 1e-5))
                    cy = int(M['m10']/(M['m00']+ 1e-5))
                    x_cord_robot = round(cx*mm_Px_rel_X)
                    y_cord_robot = round(cy*mm_Px_rel_Y)
                    print("x, y: ", x_cord_robot, y_cord_robot)
                    approx = cv2.approxPolyDP(contour, 0.02*cv2.arcLength(contour, True), True)
                    x = approx.ravel()[0]
                    y = approx.ravel()[1]
                    cv2.drawContours(imageFrame, [approx], 0, (0, 0, 0), 5)
                    if len(approx) == 4:
                        list_cont.append(("green", "square", str(x_cord_robot), str(y_cord_robot)))
                        cv2.putText(imageFrame, "Square", (x, y), cv2.FONT_HERSHEY_SIMPLEX, 1, (0, 0, 0))
                    elif 7 < len(approx) < 20:
                        list_cont.append(("green", "circle", str(x_cord_robot), str(y_cord_robot)))
                        cv2.putText(imageFrame, "Circle", (x, y), cv2.FONT_HERSHEY_SIMPLEX, 1, (0, 0, 0))
                    x, y, w, h = cv2.boundingRect(contour)
                    x, y, w, h = cv2.boundingRect(contour) 
                    imageFrame = cv2.rectangle(imageFrame, (x, y),  
                                               (x + w, y + h), 
                                               (0, 255, 0), 2) 
                    cv2.putText(imageFrame, "green", (x, y), 
                                cv2.FONT_HERSHEY_SIMPLEX,  
                                1.0, (0, 255, 0)) 
            contours, hierarchy = cv2.findContours(blue_mask, 
                                                   cv2.RETR_TREE, 
                                                   cv2.CHAIN_APPROX_SIMPLE) 
            for pic, contour in enumerate(contours): 
                area = cv2.contourArea(contour) 
                if(area > 2000): 
                    M = cv2.moments(contour)
                    cx = int(M['m01']/(M['m00']+ 1e-5))
                    cy = int(M['m10']/(M['m00']+ 1e-5))
                    x_cord_robot = round(cx*mm_Px_rel_X)
                    y_cord_robot = round(cy*mm_Px_rel_Y)
                    print("x, y: ", x_cord_robot, y_cord_robot)
                    approx = cv2.approxPolyDP(contour, 0.02*cv2.arcLength(contour, True), True)
                    x = approx.ravel()[0]
                    y = approx.ravel()[1]
                    cv2.drawContours(imageFrame, [approx], 0, (0, 0, 0), 5)
                    if len(approx) == 4:
                        list_cont.append(("blue", "square", str(x_cord_robot), str(y_cord_robot)))
                        cv2.putText(imageFrame, "Square", (x, y), cv2.FONT_HERSHEY_SIMPLEX, 1, (0, 0, 0))
                    elif 7 < len(approx) < 20:
                        list_cont.append(("blue", "circle", str(x_cord_robot), str(y_cord_robot)))
                        cv2.putText(imageFrame, "Circle", (x, y), cv2.FONT_HERSHEY_SIMPLEX, 1, (0, 0, 0))
                    x, y, w, h = cv2.boundingRect(contour)
                    x, y, w, h = cv2.boundingRect(contour) 
                    imageFrame = cv2.rectangle(imageFrame, (x, y), 
                                               (x + w, y + h), 
                                               (255, 0, 0), 2) 
                    cv2.putText(imageFrame, "blue", (x, y), 
                                cv2.FONT_HERSHEY_SIMPLEX, 
                                1.0, (255, 0, 0))
            
            
            print("Len of list:", len(list_cont))
            print("List contents:", list_cont)
            send_data_to_rapid(client, list_cont)
            
            
            cv2.imshow("Multiple Color Detection in Real-TIme", imageFrame) 
            if cv2.waitKey(10) & 0xFF == ord('q'):  
                cv2.destroyAllWindows() 
            #C:/Users/s362251/OneDrive - OsloMet/Dokumenter/lab2bilder
            directory = r'C:\Users\s362251\Documents\lab2bilder'
            os.chdir(directory)
            nytt_filnavn = 'savedImage.jpg'
            existing_files = os.listdir(directory)
            if nytt_filnavn in existing_files:
                nytt_filnavn = str(len(existing_files) + 1) + "_" + nytt_filnavn
                cv2.imwrite(nytt_filnavn, blue_mask)
            else:
                cv2.imwrite(nytt_filnavn, blue_mask)
            existing_files = os.listdir(directory)
            if nytt_filnavn in existing_files:
                nytt_filnavn = str(len(existing_files) + 1) + "_" + nytt_filnavn
                cv2.imwrite(nytt_filnavn, red_mask)
            else:
                cv2.imwrite(nytt_filnavn, red_mask)
            existing_files = os.listdir(directory)
            if nytt_filnavn in existing_files:
                nytt_filnavn = str(len(existing_files) + 1) + "_" + nytt_filnavn
                cv2.imwrite(nytt_filnavn, green_mask)
            else:
                cv2.imwrite(nytt_filnavn, green_mask)
            existing_files = os.listdir(directory)
            if nytt_filnavn in existing_files:
                nytt_filnavn = str(len(existing_files) + 1) + "_" + nytt_filnavn
                cv2.imwrite(nytt_filnavn, imageFrame)
            else:
                cv2.imwrite(nytt_filnavn, imageFrame)
            
            webcam.release()
            
        def socket_setup():
            print("I'm inside socket_setup")
            client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            host = "192.168.12.62"
            port = 2222
            client.connect((host,port))
            return client
        
        def mmPx_rel(white_rob):
            if white_rob:
                # Hvit robot workobject koordinater
                x1, x2, x3, x4 =  658.4,  307.2,  644.7,  283.9
                y1, y2, y3, y4 = -427.2,  435.8, -430.0,  427.3
            else:
                # Oransj robot workobject koordinater
                x1, x2, x3, x4 =  657.7,  353.6,  665.9,  362.8
                y1, y2, y3, y4 = -414.3,  363.3, -404.9,  365.3
            X_avg = ((x1-x2)+(x3-x4))/2
            Y_avg = abs(((y1-y2)+(y3-y4))/2)
            mm_Px_relation_X = X_avg/height
            mm_Px_relation_Y = Y_avg/width
            return mm_Px_relation_X, mm_Px_relation_Y
        
        def init_cam():
            #Initialiserer camera
            print("Hello from inside init_cam")
            cam = cv2.VideoCapture(0, cv2.CAP_DSHOW)
            print(cam)
            cam.set(cv2.CAP_PROP_FRAME_HEIGHT,height)
            cam.set(cv2.CAP_PROP_FRAME_WIDTH,width)
            return cam
        
        def send_data_to_rapid(client, list_cont: list):
            """
            Expects a connected client and a list containing tuples where every element in the tuples are strings
            Iterates over every element in every tuple and sending them separately
            """
            # Sends a string containing the length of list_cont
            # Equivalent to sending the number of objects detected
            client.send(bytes(str(len(list_cont)), "utf-8"))
            # Get the response from RAPID
            response = client.recv(4096)
            response = response.decode("utf-8")
            print(response)
            # Checks if too many objects were recieved
            # Terminates if true
            # Sends all obj data if false
            if response == "Too many objects. Terminating...":
                exit()
            else:
                # Iterates over each obj/tuple in list_cont
                for tuple in list_cont:
                    # Iterates over every data point in obj/tuple
                    for elem in tuple:
                        print("Sending data:", elem, end=", ")
                        client.send(bytes(elem, "utf-8"))
                        # Recieves a response once data has been recieved
                        response = client.recv(4096)
                        response = response.decode("utf-8")
                        print(response)
        
        print("I'm about to call main within run_program")
        print("rgb: ", red_lower_updated,green_lower_updated, blue_lower_updated)
        main()
        print("Can I even get here?")    
        
    def __init__(self, parent, controller):
        tk.Frame.__init__(self, parent)
        image = tk.PhotoImage(file="C:\\Users\\s362251\\Downloads\\ABB_GUI\\ABB\\ABB.png")
        background_label = tk.Label(self, image=image)
        background_label.image = image
        background_label.place(x=0, y=0, relwidth=1, relheight=1) 
        button_image = tk.PhotoImage(file="C:\\Users\\s362251\\Downloads\\ABB_GUI\\ABB\\Button_run_program.png")
        button = tk.Button(self, image=button_image,command=self.run_program)
        button.image = button_image
        button.place(x=660, y=80) 
```
If you pressed the button Run Program, you will be sent to class **Run_Program_Orange**, which is the class, where we detect the center point of each shape, colour of the shape and the figure of the shape. The program starts when you press the button **Run Program**. First the program creates a socket to the robot controller. Then we set the frame size of the camera, and then it proceeds to do colour detection, shape detection and find the center point, which in the end will be sent over to the robot controller. The program first detect the colour of the object by threshould values, next the program looks at the contours to find the area of the object to determine a pick point. Nest the progrram looks for how many edges it can create within the object, to determine what shape the object is. At last we send data to the robot controller, which consist of no. of objects, colours of the objects, shape of the objects and pick points.
```Python
class Run_Program_Orange(tk.Frame):        
    def run_program(self):
        print("Hello form run_program")
        height = 800 
        width = 2048  
        list_cont = []
        def main():
            print("Hello from main in run_program")
            client = socket_setup()
            white_rob = False
            mm_Px_rel_X, mm_Px_rel_Y = mmPx_rel(white_rob)
            
            webcam = init_cam()
            _, imageFrame = webcam.read()
            hsvFrame = cv2.cvtColor(imageFrame, cv2.COLOR_BGR2HSV) 
            # Access the red, blue, and green values from the calibration instance
            global red_lower_updated, red_upper_updated, blue_lower_updated, blue_upper_updated, green_lower_updated, green_upper_updated
            #red_lower = np.array([0, 222, 237], np.uint8) 
            #red_upper = np.array([17, 255, 255], np.uint8) 
            red_mask = cv2.inRange(hsvFrame, red_lower_updated, red_upper_updated) 
            #green_lower = np.array([15, 255, 140], np.uint8) 
            #green_upper = np.array([173, 255, 255], np.uint8) 
            green_mask = cv2.inRange(hsvFrame, green_lower_updated, green_upper_updated)
            #blue_lower = np.array([92, 191, 0], np.uint8) 
            #blue_upper = np.array([145, 255, 233], np.uint8) 
            blue_mask = cv2.inRange(hsvFrame, blue_lower_updated, blue_upper_updated)
            kernel = np.ones((5, 5), "uint8") 
            red_mask = cv2.dilate(red_mask, kernel) 
            res_red = cv2.bitwise_and(imageFrame, imageFrame,  
                                      mask = red_mask) 
            green_mask = cv2.dilate(green_mask, kernel) 
            res_green = cv2.bitwise_and(imageFrame, imageFrame, 
                                        mask = green_mask) 
            blue_mask = cv2.dilate(blue_mask, kernel) 
            res_blue = cv2.bitwise_and(imageFrame, imageFrame, 
                                       mask = blue_mask) 
            contours, hierarchy = cv2.findContours(red_mask, 
                                                   cv2.RETR_TREE, 
                                                   cv2.CHAIN_APPROX_SIMPLE) 
        
            for pic, contour in enumerate(contours):
                area = cv2.contourArea(contour) 
                if(area > 2000): 
                    M = cv2.moments(contour)
                    cx = int(M['m01']/(M['m00']+ 1e-5))
                    cy = int(M['m10']/(M['m00']+ 1e-5))
                    x_cord_robot = round(cx*mm_Px_rel_X)
                    y_cord_robot = round(cy*mm_Px_rel_Y)
                    print("x, y: ", x_cord_robot, y_cord_robot)
                    approx = cv2.approxPolyDP(contour, 0.02*cv2.arcLength(contour, True), True)
                    x = approx.ravel()[0]
                    y = approx.ravel()[1]
                    cv2.drawContours(imageFrame, [approx], 0, (0, 0, 0), 5)
                    if len(approx) == 4:
                        list_cont.append(("red", "square", str(x_cord_robot), str(y_cord_robot)))
                        cv2.putText(imageFrame, "Square", (x, y), cv2.FONT_HERSHEY_SIMPLEX, 1, (0, 0, 0))
                    elif 7 < len(approx) < 20:
                        list_cont.append(("red", "circle", str(x_cord_robot), str(y_cord_robot)))
                        cv2.putText(imageFrame, "Circle", (x, y), cv2.FONT_HERSHEY_SIMPLEX, 1, (0, 0, 0))
                    x, y, w, h = cv2.boundingRect(contour)
                    imageFrame = cv2.rectangle(imageFrame, (x, y),  
                                               (x + w, y + h),  
                                               (0, 0, 255), 2)
                    cv2.putText(imageFrame, "red", (x, y), 
                                cv2.FONT_HERSHEY_SIMPLEX, 1.0, 
                                (0, 0, 255))     
            contours, hierarchy = cv2.findContours(green_mask, 
                                                   cv2.RETR_TREE, 
                                                   cv2.CHAIN_APPROX_SIMPLE)
            for pic, contour in enumerate(contours): 
                area = cv2.contourArea(contour) 
                if(area > 2000): 
                    M = cv2.moments(contour)
                    cx = int(M['m01']/(M['m00']+ 1e-5))
                    cy = int(M['m10']/(M['m00']+ 1e-5))
                    x_cord_robot = round(cx*mm_Px_rel_X)
                    y_cord_robot = round(cy*mm_Px_rel_Y)
                    print("x, y: ", x_cord_robot, y_cord_robot)
                    approx = cv2.approxPolyDP(contour, 0.02*cv2.arcLength(contour, True), True)
                    x = approx.ravel()[0]
                    y = approx.ravel()[1]
                    cv2.drawContours(imageFrame, [approx], 0, (0, 0, 0), 5)
                    if len(approx) == 4:
                        list_cont.append(("green", "square", str(x_cord_robot), str(y_cord_robot)))
                        cv2.putText(imageFrame, "Square", (x, y), cv2.FONT_HERSHEY_SIMPLEX, 1, (0, 0, 0))
                    elif 7 < len(approx) < 20:
                        list_cont.append(("green", "circle", str(x_cord_robot), str(y_cord_robot)))
                        cv2.putText(imageFrame, "Circle", (x, y), cv2.FONT_HERSHEY_SIMPLEX, 1, (0, 0, 0))
                    x, y, w, h = cv2.boundingRect(contour)
                    x, y, w, h = cv2.boundingRect(contour) 
                    imageFrame = cv2.rectangle(imageFrame, (x, y),  
                                               (x + w, y + h), 
                                               (0, 255, 0), 2) 
                    cv2.putText(imageFrame, "green", (x, y), 
                                cv2.FONT_HERSHEY_SIMPLEX,  
                                1.0, (0, 255, 0)) 
            contours, hierarchy = cv2.findContours(blue_mask, 
                                                   cv2.RETR_TREE, 
                                                   cv2.CHAIN_APPROX_SIMPLE) 
            for pic, contour in enumerate(contours): 
                area = cv2.contourArea(contour) 
                if(area > 2000): 
                    M = cv2.moments(contour)
                    cx = int(M['m01']/(M['m00']+ 1e-5))
                    cy = int(M['m10']/(M['m00']+ 1e-5))
                    x_cord_robot = round(cx*mm_Px_rel_X)
                    y_cord_robot = round(cy*mm_Px_rel_Y)
                    print("x, y: ", x_cord_robot, y_cord_robot)
                    approx = cv2.approxPolyDP(contour, 0.02*cv2.arcLength(contour, True), True)
                    x = approx.ravel()[0]
                    y = approx.ravel()[1]
                    cv2.drawContours(imageFrame, [approx], 0, (0, 0, 0), 5)
                    if len(approx) == 4:
                        list_cont.append(("blue", "square", str(x_cord_robot), str(y_cord_robot)))
                        cv2.putText(imageFrame, "Square", (x, y), cv2.FONT_HERSHEY_SIMPLEX, 1, (0, 0, 0))
                    elif 7 < len(approx) < 20:
                        list_cont.append(("blue", "circle", str(x_cord_robot), str(y_cord_robot)))
                        cv2.putText(imageFrame, "Circle", (x, y), cv2.FONT_HERSHEY_SIMPLEX, 1, (0, 0, 0))
                    x, y, w, h = cv2.boundingRect(contour)
                    x, y, w, h = cv2.boundingRect(contour) 
                    imageFrame = cv2.rectangle(imageFrame, (x, y), 
                                               (x + w, y + h), 
                                               (255, 0, 0), 2) 
                    cv2.putText(imageFrame, "blue", (x, y), 
                                cv2.FONT_HERSHEY_SIMPLEX, 
                                1.0, (255, 0, 0))
            
            
            print("Len of list:", len(list_cont))
            print("List contents:", list_cont)
            send_data_to_rapid(client, list_cont)
            
            
            cv2.imshow("Multiple Color Detection in Real-TIme", imageFrame) 
            if cv2.waitKey(10) & 0xFF == ord('q'):  
                cv2.destroyAllWindows() 
            #C:/Users/s362251/OneDrive - OsloMet/Dokumenter/lab2bilder
            directory = r'C:\Users\s362251\Documents\lab2bilder'
            os.chdir(directory)
            nytt_filnavn = 'savedImage.jpg'
            existing_files = os.listdir(directory)
            if nytt_filnavn in existing_files:
                nytt_filnavn = str(len(existing_files) + 1) + "_" + nytt_filnavn
                cv2.imwrite(nytt_filnavn, blue_mask)
            else:
                cv2.imwrite(nytt_filnavn, blue_mask)
            existing_files = os.listdir(directory)
            if nytt_filnavn in existing_files:
                nytt_filnavn = str(len(existing_files) + 1) + "_" + nytt_filnavn
                cv2.imwrite(nytt_filnavn, red_mask)
            else:
                cv2.imwrite(nytt_filnavn, red_mask)
            existing_files = os.listdir(directory)
            if nytt_filnavn in existing_files:
                nytt_filnavn = str(len(existing_files) + 1) + "_" + nytt_filnavn
                cv2.imwrite(nytt_filnavn, green_mask)
            else:
                cv2.imwrite(nytt_filnavn, green_mask)
            existing_files = os.listdir(directory)
            if nytt_filnavn in existing_files:
                nytt_filnavn = str(len(existing_files) + 1) + "_" + nytt_filnavn
                cv2.imwrite(nytt_filnavn, imageFrame)
            else:
                cv2.imwrite(nytt_filnavn, imageFrame)
            
            webcam.release()
            
        def socket_setup():
            print("I'm inside socket_setup")
            client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            host = "192.168.12.85"
            port = 2222
            client.connect((host,port))
            return client
        
        def mmPx_rel(white_rob):
            if white_rob:
                # Hvit robot workobject koordinater
                x1, x2, x3, x4 =  658.4,  307.2,  644.7,  283.9
                y1, y2, y3, y4 = -427.2,  435.8, -430.0,  427.3
            else:
                # Oransj robot workobject koordinater
                x1, x2, x3, x4 =  657.7,  353.6,  665.9,  362.8
                y1, y2, y3, y4 = -414.3,  363.3, -404.9,  365.3
            X_avg = ((x1-x2)+(x3-x4))/2
            Y_avg = abs(((y1-y2)+(y3-y4))/2)
            mm_Px_relation_X = X_avg/height
            mm_Px_relation_Y = Y_avg/width
            return mm_Px_relation_X, mm_Px_relation_Y
        
        def init_cam():
            #Initialiserer camera
            print("Hello from inside init_cam")
            cam = cv2.VideoCapture(0, cv2.CAP_DSHOW)
            print(cam)
            cam.set(cv2.CAP_PROP_FRAME_HEIGHT,height)
            cam.set(cv2.CAP_PROP_FRAME_WIDTH,width)
            return cam
        
        def send_data_to_rapid(client, list_cont: list):
            """
            Expects a connected client and a list containing tuples where every element in the tuples are strings
            Iterates over every element in every tuple and sending them separately
            """
            # Sends a string containing the length of list_cont
            # Equivalent to sending the number of objects detected
            client.send(bytes(str(len(list_cont)), "utf-8"))
            # Get the response from RAPID
            response = client.recv(4096)
            response = response.decode("utf-8")
            print(response)
            # Checks if too many objects were recieved
            # Terminates if true
            # Sends all obj data if false
            if response == "Too many objects. Terminating...":
                exit()
            else:
                # Iterates over each obj/tuple in list_cont
                for tuple in list_cont:
                    # Iterates over every data point in obj/tuple
                    for elem in tuple:
                        print("Sending data:", elem, end=", ")
                        client.send(bytes(elem, "utf-8"))
                        # Recieves a response once data has been recieved
                        response = client.recv(4096)
                        response = response.decode("utf-8")
                        print(response)
        
        print("I'm about to call main within run_program")
        print("rgb: ", red_lower_updated,green_lower_updated, blue_lower_updated)
        main()
        print("Can I even get here?")    
````
At last we run the program and set the window size for the program. 
```Python
app = IRB140ColorDetectionProgram()
app.geometry("1440x810")  # Setting window size to 1440x810
app.mainloop()
```





## Author

- [HaavardAass](https://github.com/HaavardAass)
- Jostein Hansen
- Ann-Kristin Krane Schleis
- Thomas Folkvord Fredriksen
