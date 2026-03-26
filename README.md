*Subtask 1:* I first established a bilinear interpolation baseline, which already achieved a PSNR of 41.66
dB and SSIM of 0.9757 on the full dataset. This strong baseline revealed that the LR–HR signal gap is
low-frequency dominant and structurally smooth, making a GAN-style approach unnecessary overkill
and RRDB’s dense texture recovery capacity largely redundant and prone to overfitting on this data. I
therefore selected EDSR with 16 residual blocks, 64 feature channels, and residual scaling (0.1) as the
right balance of capacity and stability for this task.

I trained EDSR with a combined loss, where the frequency-domain component
penalises differences in the Fourier coefficient magnitudes between predicted and target images,
preserving fine arc structure that L1 alone over-smooths. Training ran for 40 epochs on an RTX 3050Ti.
The final model achieved PSNR 42.33 dB and SSIM 0.9776 on the held-out test set, an improvement
of +0.67 dB PSNR over bilinear interpolation. The modest but consistent gain demonstrates that the
combined loss helps recover high-frequency arc structure beyond what simple interpolation preserves,
while confirming that the dataset does not require a generative model for competitive performance.
