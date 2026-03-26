*Subtask 1:* 

I first established a bilinear interpolation baseline, which already achieved a PSNR of 41.66
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


*Subtask 2:*

This task introduced a domain shift challenge: transferring the Task A’s EDSR to a morphologically distinct lensing dataset of only 300 images. Before training, I implemented a physics-motivated
corruption detection pipeline that flagged samples exhibiting stripe artifacts (via row/column variance
asymmetry), spectral energy imbalance, low LR–HR normalised cross-correlation, and absence of a
point-like flux peak. Given the small dataset size, I adopted a two-stage fine-tuning protocol: Stage 1
freezes the backbone (head and body) and trains only the upsampling tail with a higher learning rate
using cosine annealing for 30 epochs, allowing the reconstruction layers to adapt to the new domain
without disturbing learned lensing features. Stage 2 then unfreezes all layers with layer-wise learning
rates, backbone at 10× lower rate than the tail.

I evaluated three transfer strategies: plain two-stage fine-tuning, fine-tuning with Gaussian noise in
jection on LR images during Stage 2 (std = 0.005 and 0.01), and a few-shot tail-only fine-tuning with
K ∈{10,20,30,40} randomly sampled images over 200 epochs. Plain fine-tuning achieved the best
performance at PSNR 33.41 dB and SSIM 0.8426, while noise-augmented variants were very close at
32.22 dB and 32.08 dB respectively. This suggests that at this small dataset scale, injecting additional
degradation diversity is counter-productive. The few-shot experiments showed that with as few as K=10
real images and tail-only adaptation, the model recovers to approximately 31.2 dB, demonstrating strong
generalisation from the simulated 6A distribution. The ∼9 dB drop from Task A to Task B reflects the
genuine domain gap between the two datasets. It directly motivates the feature-level alignment and
domain randomisation strategy proposed for the GSoC pipeline.
