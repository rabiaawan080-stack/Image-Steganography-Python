import cv2
import numpy as np
import matplotlib.pyplot as plt

# 1. Load your images
path_cover = "/content/cover (1).png"
path_secret = "/content/secret (2).png"

cover = cv2.imread(path_cover)
secret = cv2.imread(path_secret)

if cover is not None and secret is not None:
    # 2. Resize secret to match cover
    secret = cv2.resize(secret, (cover.shape[1], cover.shape[0]))

    # 3. Create an empty image for the result
    stego_img = np.zeros_like(cover)

    # 4. Process each color channel (0=Blue, 1=Green, 2=Red) separately
    # This prevents one color from taking over the whole image
    for i in range(3):
        c_chan = cover[:, :, i]
        s_chan = secret[:, :, i]
        
        # Keep top 5 bits of cover, take top 3 bits of secret
        # This 5:3 ratio makes the secret a "ghost" but keeps colors perfect
        stego_img[:, :, i] = cv2.bitwise_or(cv2.bitwise_and(c_chan, 0xF8), s_chan >> 5)

    # 5. Extract for preview
    extracted_secret = np.zeros_like(stego_img)
    for i in range(3):
        extracted_secret[:, :, i] = cv2.bitwise_and(stego_img[:, :, i], 0x07) << 5

    # 6. Display
    plt.figure(figsize=(20, 8))

    plt.subplot(1, 3, 1)
    plt.imshow(cv2.cvtColor(cover, cv2.COLOR_BGR2RGB))
    plt.title("1. Original Cover")
    plt.axis('off')

    plt.subplot(1, 3, 2)
    plt.imshow(cv2.cvtColor(stego_img, cv2.COLOR_BGR2RGB))
    plt.title("2. Stego (Ghost Hidden Inside)")
    plt.axis('off')

    plt.subplot(1, 3, 3)
    # Brighten the extracted image so it's easy to see
    extracted_view = cv2.normalize(extracted_secret, None, 0, 255, cv2.NORM_MINMAX)
    plt.imshow(cv2.cvtColor(extracted_view, cv2.COLOR_BGR2RGB))
    plt.title("3. Extracted Secret")
    plt.axis('off')

    plt.show()

    cv2.imwrite('stego_fixed.png', stego_img)
    print("Done! If Image 2 looks like the original Mountain, you've cracked it!")

else:
    print("Check your file paths! Make sure 'cover (1).png' is in the sidebar.")
