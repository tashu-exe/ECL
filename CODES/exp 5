#include <Arduino_LSM9DS1.h>        // For accelerometer
#include <edge-impulse-sdk/classifier/ei_run_classifier.h>  // From your Edge Impulse library

#define ACC_BUFFER_SIZE 128         // Adjust based on your model's input window size
float features[EI_CLASSIFIER_DSP_INPUT_FRAME_SIZE];

void setup() {
  Serial.begin(115200);
  while (!Serial);

  if (!IMU.begin()) {
    Serial.println("Failed to initialize IMU!");
    while (1);
  }

  Serial.println("Edge Impulse TinyML - Inference Starting...");
}

void loop() {
  // Collect sensor data
  for (int i = 0; i < EI_CLASSIFIER_RAW_SAMPLE_COUNT; i++) {
    while (!IMU.accelerationAvailable());
    float x, y, z;
    IMU.readAcceleration(x, y, z);
    features[i * 3 + 0] = x;
    features[i * 3 + 1] = y;
    features[i * 3 + 2] = z;
    delay(10);  // Adjust to match your model’s sample rate (e.g., 100Hz -> 10ms)
  }

  // Run the classifier
  signal_t signal;
  signal.total_length = EI_CLASSIFIER_DSP_INPUT_FRAME_SIZE;
  signal.get_data = &ei_buffer_get_data;

  ei_impulse_result_t result = { 0 };

  EI_IMPULSE_ERROR res = run_classifier(&signal, &result, false);
  if (res != EI_IMPULSE_OK) {
    Serial.print("Classifier error: ");
    Serial.println(res);
    return;
  }

  // Print predictions
  Serial.println("Predictions:");
  for (size_t ix = 0; ix < EI_CLASSIFIER_LABEL_COUNT; ix++) {
    Serial.print(result.classification[ix].label);
    Serial.print(": ");
    Serial.println(result.classification[ix].value, 3);
  }

  delay(1000); // Optional delay between inferences
}

// This is required by Edge Impulse classifier
int ei_buffer_get_data(size_t offset, size_t length, float *out_ptr) {
  memcpy(out_ptr, features + offset, length * sizeof(float));
  return 0;
}
