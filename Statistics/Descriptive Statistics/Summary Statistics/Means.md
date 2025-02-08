## Harmonic mean
Let's say that you swim one minute of freestyle at 3 km/h and then one minute of breaststroke at 2 km/h.  Your average speed is the arithmetic mean.  But to gain intuition about this when you're restricted to laps (fixed distance rather than fixed time), we need to switch up our formula.  The harmonic mean of 1 lap of each speed gives the average speed.
$$\frac{n}{\sum{\frac{1}{n}}}$$
In this case, we have $\frac{2}{5/6}$ or, in the special case of all $n=2$, just $\frac{2xy}{x+y}$.  This works out to 2.4 instead of 2.5.  We expect this to be lower from 2.5 because we'll be swimming at 2 km/h longer than we swam at 3 km/h.
1
The harmonic mean can be used for noise reduction because it gives greater weight towards the lower values.  While the arithmetic mean of `[2, 7, 8]` is 5.67, the harmonic mean is 3.62.

```python
# Vectorized implementation of a harmonic mean filter

input1 = PIL.Image.open("Image 2.bmp")
# input1 = PIL.Image.open("bear.jpg")  # Personal image better for identifying effectiveness of filter
x, y = input1.size
input1 = np.array(input1)
input1 = input1[:, :, None] if input1.ndim==2 else input1  # Add color dim to Ls
output1 = np.zeros((y+2, x+2, input1.shape[2]))  # Could change the 2s and 3s to be dynamic if you wanted

for c in range(input1.shape[2]):
    for i in range(3):
        for j in range(3):
            output1[i:i+y, j:j+x, c] += 1 / (input1[:, :, c]+.1)  # Larger value for better noise reduction

output1 = (9 / output1[1:-1, 1:-1, :]).astype(np.uint8)

# Advanced harmonic mean (just for fun)
# Only updates pixels where it differs from the original by a set amount
# This way we have no blur but fix salt-and-pepper noise
diff = np.abs(output1-input1)
avg_diff = np.mean(diff, axis=2)
# plt.hist(avg_diff.flatten(), bins=30); plt.show()  # Check hist to decide cutoff
mask = avg_diff > 10  # Obviously a non-hard mask would work amazingly but 10 is fine enough
output1 = np.where(mask[:, :, None], output1, input1)  # Change input1 to 0 to check diff
# plt.hist(output1.flatten(), bins=256); plt.show()

output1 = PIL.Image.fromarray(np.squeeze(output1))
output1.show()

```