# PolTiny PL — a Polish language model on the Amiga 500

The Amiga 500 is a 1987 home computer, now vintage hardware. A roughly 7 MHz processor, half a megabyte of memory and floppy disks instead of modern storage. Running a Polish language model on it sounds like science fiction. That was our starting point.

A stock Amiga 500: 0.5 MB RAM (512 KiB), no memory expansion, accelerator or FPU.

## A computer from 1987. An idea from the AI era.

The Amiga 500 is a 1987 home computer, now vintage hardware: roughly 7 MHz, half a megabyte of RAM and floppy disks. A Polish language model on this machine sounds like science fiction. That was the target: a stock A500, no expansions, with all inference running locally.

512 KiB RAM, a roughly 7.09 MHz 68000, Kickstart 1.3 and one 880 KiB DD floppy. A slow, limited Tiny PL model was an explicitly accepted trade-off.

## It started with Tiny. Then: “Make it run on an Amiga.”

The starting point was a PurePC article about Tiny 2.1 and the WollyMonjac/Tiny-2.1 source project: a small character-level transformer originally designed for DOS and x86 computers.

Original model: 366,528 parameters, four layers and a 97-token alphabet. MIT-licensed code. Tiny's original weights were already int8; later improvements also changed how the calculations were performed.

## First success: it runs. First problem: the wait.

A native Amiga window appeared, but even a simple question could take minutes. The screenshots advanced from 7/31 to 9/31: the program was working, although it could look frozen to the user.

Every prompt and output character required processing the layers and reading weights from the floppy again. Without an FPU, even short float32 operations were expensive.

## Insert the ADF. The program starts itself.

The initial image was a data disk. A later iteration added a Kickstart 1.x-compatible bootblock, an OFS filesystem and a Startup-Sequence that launched the program with a suitable stack.

Self-booting 880 KiB ADF. No separate Workbench disk required. Kickstart 1.3 is still required; the image does not include a ROM or files from a Workbench disk.

## Less expensive computation. The same test output.

Matrix products and attention were reworked around int16-scaled activations, int8 weights and int32 accumulation. Unused output projections and unnecessary weight-file seeks were reduced too.

Same English model, same question, same first four output characters: “Thre”. This measures emulated 68000 CPU cycles, excluding physical floppy latency and full GUI costs. It is not a comparison against the later, larger PolTiny model.

## 2 + 5 should not wait for a transformer.

The biggest usability change was routing recognised arithmetic expressions to a conventional parser and exact calculator. Results no longer required loading the model, and the panel displayed the actual calculation steps.

Supports +, −, ×, /, powers and parentheses using bounded exact fractions. 0.1 + 0.2 returns 0.3. Division by zero is reported immediately. The roughly 0.020 s figure counts CPU cycles, not physical floppy program-loading time.

## From Polish helpers to a new Polish model.

An intermediate fast mode combined a calculator with local lookup: 86 Polish answer sets and short explanations. The next phase pursued a different goal: new Polish weights, rather than lookup alone.

PolTiny was trained from random initialisation: 914 material entries, 4,123 training questions and 1,337 held-out phrasings. Its 115-token alphabet includes Polish characters. Training ran on a modern computer; inference runs offline on the Amiga.

## Polish explanations. Visible calculation steps.

We added a Polish interface, short explanations in the training material and explicit calculator steps. During generation, users can inspect layer activity, attention and next-character probability, or stop the calculation.

The charts are real engine telemetry, not a textual record of thoughts. The model has no separate general-reasoning module. A character's probability is not a measure of factual correctness.

## A larger model. Still one floppy disk.

PolTiny has 687,616 parameters, about 1.88× the original model. Int8 weights with per-row scales occupy 708,442 bytes. Useful files and OFS overhead fill 99.55% of the disk.

The engine allocates 186 KiB on its heap for the KV cache, matrix buffer and gains. Code, stack, display and OS need additional memory. A larger network increases text-generation cost; the calculator continues to bypass that cost.

## What improved. What remains limited.

The result is a small experimental Polish language model with a fast calculator and useful instrumentation. It reproduces training questions well, but handles rephrased questions much less reliably.

Evaluation after quantisation, requiring an exact output-text match. Held-out questions rephrase known facts; this is not an open-domain knowledge benchmark. “stolica polski” → “Warszawa.” required roughly 7.5 minutes of 68000 CPU cycles alone. Physical floppy latency and full GUI costs are excluded.

## Running in WinUAE. No separate Workbench disk.

The project author confirmed that the program starts and works in WinUAE. The configuration target remains an Amiga 500 with 0.5 MB RAM and no expansions. The ADF boots itself and the program works in Polish.

WinUAE confirmation is author-reported. Separate checks covered 68000 execution in VAMOS, arithmetic, model integrity, question evaluation, OFS and the bootblock. No independent physical-A500 test is claimed. Developed with OpenAI Codex, based on Tiny 2.1 / WollyMonjac (MIT).


Tiny 2.1 / WollyMonjac: https://github.com/WollyMonjac/Tiny-2.1
Dane / Data: mledoze/countries (ODbL), Periodic-Table-JSON (CC BY-SA 3.0), original Polish examples.
