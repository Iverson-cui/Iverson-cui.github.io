---
title: "EPARA: Parallelizing Categorized AI Inference in Edge Clouds"
collection: publications
category: manuscripts
permalink: /publication/2025-07-07-EPARA-Parallelizing-Categorized-AI-Inference-in-Edge-Clouds
excerpt: '2nd Author. This paper is under review by ACM Symposium on Cloud Computing 2025'
date: 2025-07-07
# venue: 'Journal 1'
# slidesurl: 'http://academicpages.github.io/files/slides1.pdf'
# paperurl: 'http://academicpages.github.io/files/paper1.pdf'
# citation: 'Your Name, You. (2025). &quot;EPARA: Parallelizing Categorized AI Inference in Edge Clouds.&quot; <i>Journal 1</i>. 1(1).'
---

With the increasing adoption of AI applications such as large language models and computer vision AI, the computational demands on AI inference systems are continuously rising, making the enhancement of task processing capacity using existing hardware a primary objective in edge clouds. 
This paper proposes Epara, an end-to-end AI parallel inference framework in edge, aimed at enhancing the edge AI serving capability. Our key idea is to categorize tasks based on their sensitivity to latency/frequency and requirement for GPU resources, thereby achieving both request-level and service-level task-resource allocation. The main challenge is to achieve high-precision parallelism while ensuring real-time service. Therefore, in the architectural design, EPARA adopts three time granularities ranging from fine to coarse, corresponding to request handling, information synchronization, and service placement, three critical components, integrating decentralized and centralized mechanisms to enable real-time parallelism. 
EPARA consists of three core components: 1) a task-categorized parallelism allocator that decides the parallel mode of each task, 2) a distributed request handler that performs the calculation for the specific request, and 3) a state-aware scheduler that periodically updates service placement in edge clouds. 
During implementation, we established EPARA protocols for device management and adaptive deployment. 
We implement a EPARA prototype and conduct a case study on the EPARA operation for LLMs and segmentation tasks. 
We evaluate EPARA through testbed experiments involving edge servers, embedded devices, and microcomputers, demonstrating its up to 2.1$\times$ higher goodput in production workloads compared to prior frameworks, while adapting to multiple edge AI inference services.
Additionally, we perform large-scale simulations based on the innovative ParaSim simulator and conducted deep-dive experiments to validate the effectiveness of individual components.
Evaluation through testbed experiments involving edge servers, embedded devices, and microcomputers shows that EPARA achieves up to 2.1$\times$ higher goodput in production workloads compared to prior frameworks, while adapting to various edge AI inference tasks.

In this paper, my main responsibility lies in some part of the experiments. I use HC-05 bluetooth module with Xilinx Basys 3 FPGA as an independent edge device to connect to the edge server, and evaluate EPARA’s bluetooth processing capabilities. My verilog code is on my github. 

Aside from that, I also take great part in paper writing, polishment, and graph. 

What I have gained from this experience is to have the basic understanding of the full process of publishing a paper, including experimentation, writing, refinement etc. I also got the chance to improve my skills in FPGA programming and hardware design. 