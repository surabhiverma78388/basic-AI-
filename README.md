# Hand Gesture Volume Control

This project is a Python-based hand gesture recognition system that allows users to control their computer's volume using simple hand movements. The project leverages computer vision techniques with the help of the OpenCV and MediaPipe libraries, along with PyAutoGUI for volume control actions.

## Features
- **Real-time Hand Tracking:** Uses a webcam to capture and track hand landmarks in real time.
- **Volume Control Gestures:**
  - Move your thumb and index finger apart to increase the volume.
  - Bring them closer to decrease the volume.
- Visual indicators, such as circles on fingertips and a connecting line between the thumb and index finger.

## Prerequisites
Ensure you have Python installed on your system. Then, install the following dependencies using the command:

```bash
pip install opencv-python mediapipe pyautogui
```

## How It Works
1. The code captures video frames from the default webcam.
2. Hand landmarks are detected using MediaPipe's `Hands` solution.
3. The positions of the thumb and index finger are tracked.
4. The Euclidean distance between these fingers determines whether to increase or decrease the volume:
   - If the distance is greater than 50 pixels, the system increases the volume.
   - If the distance is less than or equal to 50 pixels, the system decreases the volume.
5. Visual elements such as lines and circles are drawn to aid visualization.

## Usage
1. Run the script using:

   ```bash
   python hand_volume_control.py
   ```

2. Ensure the webcam is working and your hand gestures are visible in the frame.
3. Use gestures as described to control the volume.
4. Press the `Esc` key to exit the application.

## Code Explanation
```python
import cv2
import mediapipe as mp
import pyautogui

# Initialize variables
x1 = y1 = x2 = y2 = 0
webcam = cv2.VideoCapture(0)
my_hands = mp.solutions.hands.Hands()
drawing_utils = mp.solutions.drawing_utils

while True:
    # Capture the frame and flip it
    _, image = webcam.read()
    image = cv2.flip(image, 1)
    frame_height, frame_width, _ = image.shape

    # Convert the image to RGB
    rgb_image = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)
    output = my_hands.process(rgb_image)
    hands = output.multi_hand_landmarks

    if hands:
        for hand in hands:
            # Draw landmarks
            drawing_utils.draw_landmarks(image, hand)
            landmarks = hand.landmark
            for id, landmark in enumerate(landmarks):
                x = int(landmark.x * frame_width)
                y = int(landmark.y * frame_height)
                if id == 8:  # Index fingertip
                    cv2.circle(img=image, center=(x, y), radius=8, color=(0, 255, 255), thickness=3)
                    x1 = x
                    y1 = y
                if id == 4:  # Thumb tip
                    cv2.circle(img=image, center=(x, y), radius=8, color=(0, 0, 255), thickness=3)
                    x2 = x
                    y2 = y

            # Calculate distance and draw line
            dist = ((x2 - x1) ** 2 + (y2 - y1) ** 2) ** 0.5 // 4
            cv2.line(image, (x1, y1), (x2, y2), (0, 255, 0), 5)

            # Control volume based on distance
            if dist > 50:
                pyautogui.press("volumeup")
            else:
                pyautogui.press("volumedown")

    # Display the frame
    cv2.imshow("hand volume control", image)

    # Exit on pressing 'Esc'
    key = cv2.waitKey(10)
    if key == 27:
        break

webcam.release()
cv2.destroyAllWindows()
```

## Notes
- The application requires an active webcam.
- Proper lighting and background contrast improve hand tracking performance.
