import cv2
import numpy as np
import matplotlib.pyplot as plt

# 1. Load the images
# Your new cover image path
path_cover = '/content/pic of flowersss.jfif'
path_secret = '/content/image5.jpg'

cover = cv2.imread(path_cover)
secret = cv2.imread(path_secret)

# Check if images loaded correctly
if cover is not None and secret is not None:
    # 2. Pre-processing: Resize secret to match cover exactly
    secret = cv2.resize(secret, (cover.shape[1], cover.shape[0]))

    # 3. ENCODING (The Hiding Process)
    # Clear the bottom 4 bits of the cover (240 = 11110000)
    cover_msb = cv2.bitwise_and(cover, 240)

    # Shift the top 4 bits of the secret to the bottom 4 positions
    secret_lsb = secret >> 4

    # COMBINE using bitwise OR instead of ADD to prevent color distortion
    stego_img = cv2.bitwise_or(cover_msb, secret_lsb)

    # 4. DECODING (The Extraction Process)
    # Take the stego image and shift bits back up to reveal the secret
    extracted_secret = (stego_img << 4)

    # 5. Display the Results
    plt.figure(figsize=(18, 6))

    plt.subplot(1, 3, 1)
    plt.imshow(cv2.cvtColor(cover, cv2.COLOR_BGR2RGB))
    plt.title("1. Original Cover (Flowers)")
    plt.axis('off')

    plt.subplot(1, 3, 2)
    plt.imshow(cv2.cvtColor(stego_img, cv2.COLOR_BGR2RGB))
    plt.title("2. Stego Image (Secret Hidden)")
    plt.axis('off')

    plt.subplot(1, 3, 3)
    plt.imshow(cv2.cvtColor(extracted_secret, cv2.COLOR_BGR2RGB))
    plt.title("3. Extracted Secret (Recovered)")
    plt.axis('off')

    plt.tight_layout()
    plt.show()

    # Save the output file
    cv2.imwrite('stego_output.png', stego_img)
    print("Success! 'stego_output.png' saved. The middle image should now look much closer to the original.")

else:
    print("Error: Check your file paths. Ensure 'pic of flowersss.jfif' and 'image5.jpg' are in the /content/ folder.")
