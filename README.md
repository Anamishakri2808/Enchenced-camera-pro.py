# Enchenced-camera-pro.py
import cv2
import time
# Define threshold for motion detection
threshold = 0

# Define video writer object (initially None)
writer = None
    
def is_motion_detected(frame1, frame2):
  """
  Compare two frames to check for motion detection.
  """
  gray1 = cv2.cvtColor(frame1, cv2.COLOR_BGR2GRAY)
  gray2 = cv2.cvtColor(frame2, cv2.COLOR_BGR2GRAY)
  diff = cv2.absdiff(gray1, gray2)
  mask = cv2.threshold(diff,0 , 255, cv2.THRESH_BINARY)[1]
  count = cv2.countNonZero(mask)
  return count > threshold

# Start video capture
cap = cv2.VideoCapture(0)  # Change 0 to your camera index

# Capture and save the first frame
ret, frame = cap.read()
frame_prev = frame

frame_count = 0
start_time = None

while True:
  # Capture frame
  ret, frame = cap.read() 

  # Exit if frame not captured
  if not ret:
    break

  # Initialize video writer if motion detected and recording not started
  if is_motion_detected(frame, frame_prev) and writer is None:
    start_time = time.time()
    filename = f"recording_{start_time}.avi"
    writer = cv2.VideoWriter(filename, cv2.VideoWriter_fourcc(*"MJPG"), 20, (640, 480))

  # Write frame to video if recording in progress
  if writer is not None:
    writer.write(frame)

  # Check if recording should stop (3 seconds after no motion)
  if writer is not None and start_time is not None and time.time() - start_time > 3:
    writer.release()
    writer = None
    start_time = None

  # Update previous frame
  frame_prev = frame

  # Display frame
  cv2.imshow("Frame", frame)

  # Exit on 'q' key press
  if cv2.waitKey(1) & 0xFF == ord("q"):
    break

# Release resources
cap.release()
if writer is not None:
  writer.release()
cv2.destroyAllWindows()
  
  
  
  
  
  
  
  
  
