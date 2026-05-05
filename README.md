# CASA0018 - Blink - Animatronic Eye & Machine Learning

<img width="632" height="438" alt="Screenshot 2026-05-04 at 10 57 30" src="https://github.com/user-attachments/assets/5b751bd3-26f9-476a-999f-381c8032e702" />


BLINK is a cap-mounted eye tracking system that uses deep learning to detect eye movement and control an animatronic eye in real time.
An OV7675 camera module wired to an ESP32-S3 Feather captures a live feed of the wearer's eye and runs inference using a
TensorFlow Lite model on the device. When a blink is detected, the prediction is sent over WiFi UDP to a Feather M0 WiFi,
which drives a servo motor to close the animatronic eyelid. The project explores how machine learning can be used as a creative tool in performance and animation, 
using the performer's own physiological data as input rather than removing them from the process.

Edge Impulse project: https://studio.edgeimpulse.com/studio/973156
VIDEO: https://youtu.be/RPCHt_jFTuw

<img width="867" height="600" alt="Screenshot 2026-05-04 at 17 37 08" src="https://github.com/user-attachments/assets/c5e4cc55-aa75-456d-9d1b-166a41f87a70" />


**How to Replicate:**

Hardware required: Adafruit Feather ESP32-S3, Adafruit Feather M0 WiFi, OV7675 camera module, 
servo motor, and a 3D printed animatronic eye mechanism built following Morgan Manly's single eye workshop on Instructables.

Wire the OV7675 to the ESP32-S3 and mount it on a cap so the camera points directly at the wearer's eye. 
Mount the animatronic eye and servo on a separate structure connected to the Feather M0 WiFi.
Upload the ESP32-S3 with the inference code and the Feather M0 with the servo receiver code.
Both boards must be on the same WiFi network with the correct IP address set in the sender code.

To collect training data, run the web server sketch on the ESP32-S3, open the IP address in a browser, 
and use the capture button to save labelled JPEGs directly to your laptop.
Collect data with the camera already mounted in its final position on the cap,
as any difference in angle or distance between training and deployment will reduce on-device accuracy.
Upload the images to Edge Impulse and train a MobileNetV1 96x96 0.25 model with a 30 neuron classification head, quantised to INT8.

Export the Arduino library from Edge Impulse and make two edits before deploying.
In ei_classifier_porting.cpp, replace the ei_malloc and ei_calloc functions with custom implementations that route any allocation above 4KB to PSRAM via heap_caps_malloc(size, MALLOC_CAP_SPIRAM).
In ei_classifier_config.h, disable ESP-NN hardware acceleration. These edits are required every time a new library version is exported. 
Set the camera clock to 10MHz to bring inference below 1 second per frame.

**Conclusion** 

BLINK demonstrates that real-time eye tracking inference is achievable on an embedded microcontroller and can be used to drive a physical animatronic output over WiFi. 
Blink detection works reliably when the camera is correctly positioned, though left/right classification remains limited by resolution and dataset size. 
The OV7675 and ESP32-S3 combination caused significant compatibility issues throughout, and a different camera module such as the OV2640 would be the first thing to change in a future iteration. 
Most importantly, the project points toward a way of using AI in performance and making, one where the technology responds to and amplifies the performer rather than replacing them.

**Bibliography**

Costescu, C. (2023). Eye Tracking, Animatronic Fursuit Head Base. YouTube. https://www.youtube.com/watch?v=xfGlPQP8tPY

MorganManly. (no date). Animatronic Eyes (Double and Single, 3D Printed, Compact, With Arduino). Instructables. https://www.instructables.com/member/MorganManly/

