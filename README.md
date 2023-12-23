import sounddevice as sd
import numpy as np

def activate_on_sound(indicator_duration=3, threshold=0.05, device_index=None):
    print("Listening for sound...")

    # Set up audio stream
    duration = 60  # seconds (adjust as needed)
    sample_rate = 44100  # Hz
    samples = int(duration * sample_rate)
    stream = sd.InputStream(device=device_index, channels=1, samplerate=sample_rate)

    # Initialize variables
    triggered = False
    indicator_counter = 0

    with stream:
        while True:
            # Read a chunk of audio data
            data, overflowed = stream.read(samples)
            amplitude = np.max(np.abs(data))

            # Check if the sound exceeds the threshold
            if amplitude > threshold:
                if not triggered:
                    print("Sound detected! Activating...")
                    triggered = True

                # Reset the indicator counter
                indicator_counter = 0
            else:
                if triggered:
                    # Check for continuous activation
                    indicator_counter += 1
                    if indicator_counter > indicator_duration * (sample_rate // samples):
                        print("Deactivating...")
                        triggered = False

if __name__ == "__main__":
    activate_on_sound()
