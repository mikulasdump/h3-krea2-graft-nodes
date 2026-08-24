# H3 x Krea2 Graft Nodes

ComfyUI nodes for grafting Krea2 into MiniMax H3. Attention/MLP content
transplant + a separate attention-sharpness transplant. No official H3
docs, all reverse-engineered from testing + TenStrip's and joeygambino's
public writeups. Use at your own risk, still WIP.

## What's here

- `comfyui_tenstrip_graft/` -- content graft (Q/V/K/out/MLP, per-head).
  Method from TenStrip's H3 grafts.
- `comfyui_qknorm_transplant/` -- Q-norm gain transplant only, no content
  weights touched. Method from joeygambino (Z-Image donor originally,
  adapted for Krea2 here).
- `comfyui_krea_h3_graft_lora_v2/` -- apply a Krea2-trained LoRA onto an
  already-grafted H3 checkpoint. Separate use case.

## TL;DR results
https://www.reddit.com/r/StableDiffusion/s/dtlMsvtGs4
Content graft works. Same character-shift (color scheme, helmet shape)
showed up consistently across multiple parameter runs, same seed -- not
one lucky image. That's the strongest evidence so far this isn't just
noise.

- **K at low strength (~0.1-0.2): fine, no real damage.** Don't need to
  avoid it like the doc says, at least not at low values.
- **QK-norm across all blocks (0:50): kills audio.** Doesn't even touch
  K -- so attention sharpness itself hits audio, not just K specifically.
- **QK-norm blocks 20:50: audio ok, but does nothing for character.**
  It's a texture/sharpness knob, not a content one. Don't expect it to
  carry character.
- **attn_ramp_start_frac at 1.0 (no gentle ramp-in) + early blocks (0:20):
  breaks.** Keep the ramp soft if you go early.
- Combining content graft + QK-norm at full strength on both = worse
  than either alone. Still not solved.

## Install

Each folder -> its own subfolder in `ComfyUI/custom_nodes/`. Don't merge
them. Restart ComfyUI fully after adding.

## Credits

- **TenStrip** (huggingface.co/TenStrip) -- the per-head band-aware graft
  methodology (10Eros-Max / h3_graft_methodology.md).
- **joeygambino** (huggingface.co/joeygambino) -- the Q-norm sharpness
  transplant idea (MiniMax-H3-x-Z-Image-GGUF).

Neither published source code. These nodes are our own implementation
from their public descriptions + our own testing.
