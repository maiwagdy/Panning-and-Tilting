
# Panning & Tilting

## First, what's panning & tilting?

Panning and tilting are fundamental camera movements that mimic the natural motion of the human head, allowing the camera to capture wide spaces or follow action without changing its physical location. A pan moves the camera horizontally (left or right), while a tilt moves it vertically (up or down).

![image alt](https://github.com/maiwagdy/Panning-and-Tilting/blob/84625ea19852bd3f3a2287201500bb179ed30745/panning-tilting.png)

## Problem 1 

- **Understading the camera's system**

The camera frame has a width (W) & height (H) measured in pixels.

- The center of the image located by the camera is at:

**(X*center*, Y*center*) = (W/2, H/2)**

- The outputs of the object's current position as a pixel coordinate:

**(X*object*, Y*objects*)**

## Problem 2

## Coverting pixels to angles 

To convert the target's distance from the center (measured in **pixels**) into physical movement (measured in **degrees**).

- You need to know how many degrees of the real world each individual pixel represents. 

- Think of your camera's image as a grid. If you know the total real-world width the camera can see, you can divide that width by the total number of pixels to find the value of a single pixel.

### **1. Calculating the pixel's error**

To center a target exactly in a camera's frame using panning and tilting, the system must calculate the pixel offset between the target's current position and the frame's true center, then convert that offset into motor angles.

- The camera first determines how far the target is from the exact center of the image sensor.

Let the target's center be (X*target*, Y*target*), and the image's center detected by the camera is (X*center*, Y*center*)

     ∴ Error{x} = X(target) - X(center)

     ∴ Error{y} = Y(target) - Y(center)

![image alt](https://github.com/maiwagdy/Panning-and-Tilting/blob/3ed256746c19518504c4c21dd3c36c41dbf36409/visualizing-the-correction-matrix.png)
    
where, 

- **Horizontal Error{x}:** 

Determines the direction and magnitude of the pan movement.

- **Vertical Error{y}**: 

Determines the direction and magnitude of the tilt movement.

### **2. Convert pixels to angles**

### The core formula

To find the physical angle change **Δθ** needed to center the target,
use this formula for both the **pan** & **tilt** axes:

           Required angle step = Pixel error × (Feied of view / Total resolution)

### The Components Explained:

- **Pixel error:** How many pixels the target is from the exact center.
- **Field of view (FOV):** The angular view of the lens in degrees (how **wide** it sees).
- **Total resolution:** The total number of pixels along that specific axis.
- **Degrees per pixel:** The term in the parentheses **FOV/Resolution** tells you exactly how many degrees one single pixel represents.

### **3. Apply a PID feedback loop**

#### **Problem -->** 
Directly moving the camera by the calculated angle often causes jerky movements or overshooting. **Where the camera oscillates back and forth past the target**. 

#### **Solution -->**
Implementing a **Proportional-Integral-Derivative (PID)** controller ensures the pan-tilt mechanism **slows down** smoothly as the target nears the exact center.

- **Proportional (P):** Moves the motor faster when the target is far from the center, and slower as it gets closer.

- **Integral (I)**: Corrects small, persistent errors (like wind or motor friction) that prevent the camera from reaching the perfect center.

- **Derivative (D)**: Dampens the movement to prevent the camera from overshooting the target.

### **4. Continuous loop execution**

**The system repeats this process continuously at a high frame rate (e.g., 30 to 60 times per second)**. Through these following steps:

- **A. Detect:** Locate the target's bounding box using computer vision.
- **B. Measure:** Calculate the distance from the center.Correct:
- **C. Command:** the pan/tilt motors to adjust.
- **D. Repeat:** Stop moving only when Error{x} = 0 & Error{y} = 0
