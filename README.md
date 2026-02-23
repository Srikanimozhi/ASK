# ASK
# Aim
Write a simple Python program for the modulation and demodulation of ASK and FSK.
# Tools required
# Program
```asm
import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import butter, lfilter

lpf = lambda x,f,fs: lfilter(*butter(4, f/(0.5*fs), 'low'), x)

fs, br, T = 1000, 10, 1
t = np.arange(0, T, 1/fs)
bd = fs//br
bits = np.random.randint(0,2,br)
msg = np.repeat(bits, bd)

# ASK
fc = 50
ask = msg*np.sin(2*np.pi*fc*t)
ask_dec = (lpf(ask*np.sin(2*np.pi*fc*t),fc,fs)[::bd] > 0.25).astype(int)

# FSK
f1, f2 = 30, 70
fsk = np.hstack([np.sin(2*np.pi*(f2 if b else f1)*t[i*bd:(i+1)*bd])
                 for i,b in enumerate(bits)])
d1 = lpf(fsk*np.sin(2*np.pi*f1*t),f1,fs)
d2 = lpf(fsk*np.sin(2*np.pi*f2*t),f2,fs)
fsk_demod = np.repeat([np.sum(d2[i*bd:(i+1)*bd]**2) >
                       np.sum(d1[i*bd:(i+1)*bd]**2)
                       for i in range(br)], bd)

# Plot
plt.figure(figsize=(14,18))
for i,data,title in [
    (1,msg,"Message"),
    (2,np.sin(2*np.pi*fc*t),"ASK Carrier"),
    (3,ask,"ASK Signal"),
    (4,ask_dec,"ASK Decoded"),
    (5,np.sin(2*np.pi*f1*t),"FSK Carrier 1"),
    (6,np.sin(2*np.pi*f2*t),"FSK Carrier 2"),
    (7,fsk,"FSK Signal"),
    (8,fsk_demod,"FSK Demodulated")]:
    
    plt.subplot(8,1,i)
    if i in (4,8):
        plt.step(range(br), data[::bd] if i==8 else data, where='mid')
    else:
        plt.plot(t, data)
    plt.title(title)

plt.tight_layout()
plt.show()
```
# Output Waveform
<img width="1390" height="1789" alt="image" src="https://github.com/user-attachments/assets/525ff4f6-9566-47cc-bb8d-15c41fccc4f6" />

# Results
```
Attach the output waveform
```
# Hardware experiment output waveform.
