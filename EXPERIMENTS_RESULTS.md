# Experiment Results

| Run | Score | Author | Summary | Date | Info |
|---|---:|---|---|---|---|
| Iteration 1 | 1.3575 | ryanznie | Stacked baseline with partial RoPE, SmearGate, BigramHash, LeakyReLU squared, XSA, LN scale, and EMA-free export under the 16 MB limit | 2026-04-05 | [3d78388](https://github.com/ryanznie/parameter-golf/commit/3d78388) |
| Iteration 2 | 1.4237 | ryanznie | EMA export run with slower throughput and oversized compressed artifact exceeding the 16 MB limit | 2026-04-05 | [a69f58c](https://github.com/ryanznie/parameter-golf/commit/a69f58c) |

## Iteration 1

**Config**

- Trainer: `train_gpt.py`
- Stop condition: wallclock cap at 600s
- Final train step reached: `1133/20000`

**Learning Curve**

| Step | Train Loss | Val Loss | Val BPB | Train Time | Step Avg |
|---|---:|---:|---:|---:|---:|
| 600 | 2.4952 | 2.4451 | 1.4482 | 319732 ms | 532.89 ms |
| 800 | 2.3327 | 2.3641 | 1.4002 | 424852 ms | 531.07 ms |
| 1000 | 2.3412 | 2.3096 | 1.3679 | 531061 ms | 531.06 ms |
| 1133 | — | 2.2892 | 1.3558 | 600292 ms | 529.82 ms |

**Final Eval**

- Final roundtrip val loss: `2.2921`
- Final roundtrip val BPB: `1.3575`
- Exact final roundtrip val loss: `2.29206303`
- Exact final roundtrip val BPB: `1.35748883`
- Eval time: `11334 ms`

**Memory**

- Peak allocated: `10450 MiB`
- Peak reserved: `10860 MiB`

**Artifact Size**

- Serialized model: `68014811` bytes
- Code size: `51635` bytes
- Total raw submission size: `68066446` bytes
- Serialized model `int8+zlib`: `12801246` bytes
- Quantized payload bytes: `17578276`
- Raw torch quant blob bytes: `17624911`
- Payload ratio: `3.87x`
- Total submission size `int8+zlib`: `12852881` bytes

**Summary**

- The run improved steadily through the 10-minute budget, reaching `1.3558` val BPB before the wallclock stop.
- The final quantized roundtrip score held almost all of the pre-export quality at `1.3575` val BPB.
- The compressed artifact stayed under the 16 MB challenge limit at `12,852,881` bytes total including code.

## Iteration 2

**Config**

- Trainer: `train_gpt.py`
- Stop condition: wallclock cap at 600s
- Final train step reached: `1043/20000`
- EMA applied before export

**Learning Curve**

| Step | Train Loss | Val Loss | Val BPB | Train Time | Step Avg |
|---|---:|---:|---:|---:|---:|
| 600 | 2.4801 | 2.4312 | 1.4399 | 345033 ms | 575.05 ms |
| 700 | — | 2.3849 | 1.4125 | 402497 ms | 575.00 ms |
| 800 | 2.3183 | 2.3503 | 1.3920 | 460368 ms | 575.46 ms |
| 900 | — | 2.3242 | 1.3765 | 519093 ms | 576.77 ms |
| 1000 | 2.3378 | 2.3046 | 1.3649 | 576158 ms | 576.16 ms |
| 1043 | — | 2.3013 | 1.3630 | 600522 ms | 575.76 ms |

**Final Eval**

- Final roundtrip val loss: `2.4038`
- Final roundtrip val BPB: `1.4237`
- Exact final roundtrip val loss: `2.40381756`
- Exact final roundtrip val BPB: `1.42367616`
- Eval time: `15309 ms`
- Export used EMA weights

**Memory**

- Peak allocated: `14279 MiB`
- Peak reserved: `14340 MiB`

**Artifact Size**

- Serialized model: `106313663` bytes
- Code size: `54482` bytes
- Total raw submission size: `106368145` bytes
- Serialized model `int8+zlib`: `16391349` bytes
- Quantized payload bytes: `27323748`
- Raw torch quant blob bytes: `27380239`
- Payload ratio: `3.89x`
- Total submission size `int8+zlib`: `16445831` bytes

**Summary**

- This run was slower than Iteration 1, reaching only `1043` steps within the same 600-second budget.
- Validation improved steadily to `1.3630` val BPB before stopping, but the final EMA-exported artifact landed at `1.4237` val BPB.
- The compressed submission exceeded the 16 MB limit at `16,445,831` bytes total including code, so this configuration is not submission-safe as logged.
