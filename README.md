# Image Format Lab

JPEG vs WebP vs AVIF on your own photo — a quality sweep with real file sizes and SSIM scores, a difference view, and an honest check of what your browser can actually encode. Nothing is uploaded.

**Live:** <https://image-format-lab.slippylabs.com/>

## What it does

- Encode your image at any quality in any format your browser supports, and see the file size, bits per pixel, SSIM and PSNR.
- Run a sweep across fourteen quality steps per format and get a size-against-similarity chart, plus the answer to the only question that matters: **which format reaches SSIM 0.98 in the fewest bytes, on this picture**.
- A difference view (per-pixel error × 8) showing where the encoder spent its errors — almost always edges and fine texture.

## How it works

SSIM is the structural similarity index of Wang et al. (2004), computed on luma with the standard 11×11 Gaussian window and C1/C2 constants. Encoding is `canvas.toBlob`, so the available formats are whatever your browser can write.

**The trap this tool is built around:** a browser asked for a format it cannot encode does not have to fail. The spec says `toBlob` falls back to PNG, and Chromium does exactly that for AVIF — the blob arrives, with a plausible size, and nothing about the call says anything went wrong. A naive sweep charts PNG bytes in an AVIF column and concludes AVIF is terrible. Every encode here is verified against the MIME type that came back, and a format that fakes it is reported as unsupported, with the type it actually returned.

## Verification

SSIM is checked against an independent NumPy implementation written from the paper (agreement to 1e-9), plus the properties it must have regardless of implementation: exactly 1 against itself, symmetric, monotonically falling as noise is added, and scoring a constant brightness shift better than noise of the same magnitude — because it measures structure. The Gaussian window is checked to be normalised and symmetric, and the blur to leave a constant image untouched (edge clamping, not zero padding). The format guard is tested against every shape a silent fallback can take.

## Run it locally

A static site. No build step, no package manager, no dependencies:

```
git clone git@github.com:slippylabs/image-format-lab.slippylabs.com.git
cd image-format-lab.slippylabs.com
python3 -m http.server 8000
```

Then open <http://localhost:8000>.

---

Part of [Slippy Labs](https://slippylabs.com). Every tool is indexed at
[projects.slippylabs.com](https://projects.slippylabs.com).
