# h3-krea2-graft-nodes
Experiments in Minimax H3 grafting potential

# H3 x Krea2 Graft Nodes

ComfyUI custom nodes for cross-model attention/weight grafting between
MiniMax H3 and Krea2. Built to test whether Krea2's texture/character
can be transplanted into H3 without full retraining. No official H3
architecture documentation exists (that we've found) -- everything here
is reverse-engineered from testing, plus published methodology from
TenStrip and joeygambino (credits below). Contributions, better tuning,
and corrections welcome.

## Nodes in this repo

### `comfyui_tenstrip_graft/` -- content graft (concepts, texture)
Per-head Q/V/out_proj/MLP transplant. K never touched (breaks audio).
Concepts land reliably; character fidelity still faint even after
tuning -- open problem. See the folder's own README for exact
strength/block-range findings.

### `comfyui_qknorm_transplant/` -- attention sharpness
Touches only the per-head Q-norm gain vector, no content weights.
Doesn't transfer knowledge -- seems to change how decisively H3 commits
to content it already knows. Real texture gain alone, but currently
breaks LoRAs trained on native H3 (cause not fully understood). See the
folder's own README, including a donor-specific block-range finding that
contradicts the source technique's original guidance.

### `comfyui_krea_h3_graft_lora_v2/` -- apply a Krea2-trained LoRA onto a grafted checkpoint
For layering a LoRA trained on Krea2's own architecture onto an
H3 checkpoint that's already been graft-merged. Separate use case from
the two nodes above -- for after you've baked a checkpoint, not for
testing the graft itself.

## Status

Content graft and QK-norm nodes both work individually and are
reasonably well understood. Combining both at once needs more tuning --
full strength on both was worse than either alone; lower content-graft
strength plus `orthogonal=False` on that node, stacked with QK-norm at
its own solo-tuned settings, looked more promising but wasn't fully
dialed in. If you pick this up, that's the open thread.

## Install

Each node folder goes into `ComfyUI/custom_nodes/` as its OWN separate
subfolder -- don't merge the contents together, each needs its own
`__init__.py` intact. Restart ComfyUI completely after adding.

## Credits

- **TenStrip** (huggingface.co/TenStrip) -- documented the per-head,
  band-aware, orthogonal + magnitude-preserving graft methodology this
  content-graft node is built from (see `10Eros-Max` /
  `h3_graft_methodology.md`, and the `Krea2-H3-Style-Lora`,
  `Wan2.2_H3_Motion_Lora`, `Anima-H3-Booster-Lora` repos for related
  donor experiments).
- **joeygambino** (huggingface.co/joeygambino) -- described the QK-norm
  sharpness-transplant idea (`MiniMax-H3-x-Z-Image-GGUF`), originally
  with Z-Image as donor. Adapted here for Krea2.

Neither of the above published source code for their exact
implementations -- both node implementations here are our own, built
from their public descriptions and our own testing, not a copy of their
scripts.
