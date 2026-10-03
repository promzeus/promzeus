# Dmytro Onyshko

**Senior DevOps / SRE engineer · Kubernetes · LLM inference on edge hardware · Embedded DSP & SDR · Slovakia**

DevOps engineer with a background in radio electronics and microelectronics. I build infrastructure tooling for Kubernetes (failover, storage, backups), run large-model quantization pipelines on AWS, and write embedded signal-processing code for software-defined radio. Author of the FT8 decoder in the official HackRF + PortaPack Mayhem firmware.

> **Current focus (2026):** getting GLM-4.6, a 355B mixture-of-experts LLM, to run on a single NVIDIA GB10 box. See [gh0stx-glm46-gb10](https://github.com/promzeus/gh0stx-glm46-gb10).

---

## Highlights

### 🧠 gh0stx-glm46-gb10: a 355B MoE LLM on one NVIDIA GB10 *(active)*
Runs **GLM-4.6 (355B parameters, 160 experts)** on one ASUS GX10 / DGX Spark-class box with **121.6 GiB of unified memory**. The bf16 checkpoint is about 710 GB, so the model has to shrink to roughly a sixth of that size and still reason correctly.

- **Quantization pipeline on AWS EKS + Karpenter.** Spot `r8i.24xlarge` nodes handle the full conversion: bf16 → Q8_0 GGUF (379 GB) → mixed-precision GGUF. GPU nodes handle quantization research.
- **Mixed precision with imatrix calibration.** Expert tensors use IQ2_XXS and the rest of the network uses Q5_K. The imatrix comes from my own calibration corpus of 1.63M tokens.
- **Speculative decoding.** The model's built-in MTP layer serves as a draft head in llama.cpp. EAGLE-3 and small-draft-model variants are experimental.
- **Evidence-driven dead ends.** I tried REAP expert pruning (160 → 64 experts) and NVFP4 quantization, then closed both after my long-reasoning loop tests failed in 7–9 of 12 cases. Those results are documented, not hidden.
- **Serving and evaluation.** llama.cpp / vLLM serving on the GX10, plus a test suite for reasoning loops, throughput, and draft acceptance.

➡️ [repo](https://github.com/promzeus/gh0stx-glm46-gb10)

### 📡 FT8 RX: first on-device FT8 decoder for HackRF + PortaPack
Author of the FT8 receiver merged into the upstream [portapack-mayhem/mayhem-firmware](https://github.com/portapack-mayhem/mayhem-firmware), the main open-source firmware for HackRF + PortaPack. The ham community had been asking for this feature since 2024. Until then, FT8 with a HackRF required a PC running WSJT-X.

- **RTC-free slot synchronisation.** FT8 needs sub-symbol alignment to the 15 s T/R slot. A Costas-array correlator measures where the transmission sits in the analysis window, and a three-state loop (Searching → Syncing → Locked) steers the capture onto it. Only CRC-valid decodes are trusted during acquisition.
- **Porting ft8_lib to the Cortex-M baseband core** with stack-to-static and zero-variance patches for the target.
- **Gain design without AGC.** The decoder needs linear amplitude, so I added a clipping-aware channel-level meter in place of AGC.
- Tested on air on PortaPack H2 (40 m band).

➡️ [PR #3320: FT8 RX](https://github.com/portapack-mayhem/mayhem-firmware/pull/3320) · [PR #3329: on-air follow-up](https://github.com/portapack-mayhem/mayhem-firmware/pull/3329)

### ☸️ redis-sentinel-gateway
A Go controller that watches Redis Sentinel and updates Kubernetes Endpoints on master failover. It gives clients a single entry point with no Sentinel logic of their own. HA is handled through leader election on Kubernetes Leases. The image has 3K+ pulls on Docker Hub and the project is listed on Artifact Hub.

➡️ [repo](https://github.com/promzeus/redis-sentinel-gateway) · [Docker Hub](https://hub.docker.com/r/promzeus/redis-sentinel-gateway)

### ☸️ RedisOperator-haproxy-failover
The predecessor of the gateway above. It is a Helm chart for spotahome/redis-operator with two failover modes: HAProxy for small workloads, and endpoint-switching for heavy ones.

➡️ [repo](https://github.com/promzeus/RedisOperator-haproxy-failover)

### Other infrastructure work
- [wait_connection](https://github.com/promzeus/wait_connection): Go tool that blocks until a Kubernetes pod has an active connection on a port
- [bareos-k8s](https://github.com/promzeus/bareos-k8s): Bareos backup integration for Kubernetes
- [iptables](https://github.com/promzeus/iptables): firewall rules for Docker Swarm
- [csi-s3](https://github.com/promzeus/csi-s3): S3-backed CSI volumes (fork)

---

## Stack
- **Infra:** Kubernetes, AWS EKS, Karpenter, Helm, Docker / Swarm, Redis Sentinel, HAProxy, Bareos, iptables, Prometheus, Linux
- **LLM / ML infra:** llama.cpp, GGUF quantization (imatrix, IQ2/Q5_K), vLLM, speculative decoding (MTP, EAGLE-3), NVIDIA GB10 / DGX Spark
- **Code:** Go, Python, Bash, C/C++ (embedded, ARM Cortex-M)
- **RF / DSP:** SDR, HackRF + PortaPack, digital-mode decoding (FT8)

## Contact
🌐 [mikudice.org](https://mikudice.org)
