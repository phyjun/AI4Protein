# AI4Protein


# Scaffold generate models

| Release Date | Model Name        | Reference                                                    | Repository                                                   | Model Type                | Tasks Type                               |
|--------------|-------------------|--------------------------------------------------------------|--------------------------------------------------------------|---------------------------|------------------------------------------|
| 2024-02-10   | Scaffold-Lab      | [BioRxiv \(2024\): 2024-02](http://biorxiv.org/lookup/doi/10.1101/2024.02.10.579743) | [Github](https://github.com/Immortals-33/Scaffold-Lab)       |                           | Benchmark                                |
| 2022-07-21   | RFdesign          | [Science 377.6604 \(2022\): 387](https://www.science.org/doi/10.1126/science.abn2100) | [Github](https://github.com/RosettaCommons/RFDesign)         | AF2-based                 | Hallucination, Unconditional, Inpainting |
| 2022-06-27   | Ig-VAE            | [PLoS Comput. Biol 18.6 \(2022\): e1010271](https://journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.1010271) | [Github](https://github.com/ProteinDesignLab/IgVAE)          | VAE-based                 | Unconditional                            |
| 2022-07-01   | ProteinSGM        | [Nat. Comput. Sci 3.5 \(2023\): 382](https://www.nature.com/articles/s43588-023-00440-3#citeas) | [Gitlab](https://gitlab.com/mjslee0921/proteinsgm)           | Diffusion-based           | Unconditional, Inpainting                |
| 2023-01-01   | DiffSDS           | [ArXiv:2301.09642](https://arxiv.org/abs/2301.09642)         | [Github](https://github.com/A4Bio/DiffSDS_open)              | Diffusion-based           | Inpainting                               |
| 2023-01-29   | Genie             | [ArXiv:2301.12485](http://arxiv.org/abs/2301.12485)          | [Github](https://github.com/aqlaboratory/genie)              | Diffusion-based           | Unconditional                            |
| 2023-02-05   | FrameDiff         | [ArXiv:2302.02277](http://arxiv.org/abs/2302.02277)          | [Github](https://github.com/jasonkyuyim/se3_diffusion)       | Diffusion-based           | Unconditional                            |
| 2023-05-10   | Protein Generator | [BioRxiv \(2023\): 2023-05](https://www.biorxiv.org/content/10.1101/2023.05.08.539766v1) | [Github](https://github.com/RosettaCommons/protein_generator) [Huggingface](https://huggingface.co/spaces/merle/PROTEIN_GENERATOR) | Diffusion-based           | Unconditional, Inpainting                |
| 2023-05-25   | Protpardelle      | [BioRxiv \(2023\): 2023-05](https://www.biorxiv.org/content/10.1101/2023.05.24.542194v1.full) | [Github](https://github.com/ProteinDesignLab/protpardelle)   | Diffusion-based           | Unconditional, Side-chain conditional    |
| 2023-06-11   | RFdiffusion       | [Nature 620.7976 \(2023\): 1089](https://www.nature.com/articles/s41586-023-06415-8) | [Github](https://github.com/RosettaCommons/RFdiffusion)      | Diffusion-based           | General                                  |
| 2023-06-30   | TDS               | [ArXiv:2306.17775](https://arxiv.org/abs/2306.17775)         | [Github](https://github.com/blt2114/twisted_diffusion_sampler) | Diffusion-based           | Conditional                              |
| 2023-10-30   | GPDL              | [BioRxiv \(2023\): 2023-10](https://www.biorxiv.org/content/10.1101/2023.10.26.564121v1) | [Github](https://github.com/sirius777coder/GPDL)             | ESM-based                 | Conditional                              |
| 2023-10-08   | FrameFlow         | [ArXiv:2310.05297](https://arxiv.org/abs/2310.05297)         | [Github](https://github.com/microsoft/protein-frame-flow)    | Flow-based                | Unconditional                            |
| 2023-11-15   | Chroma            | [Nature 623.7989 \(2023\): 1070](https://www.nature.com/articles/s41586-023-06728-8) | [Github](https://github.com/generatebio/chroma)              | Diffusion-based           | General                                  |
| 2024–02-28   | PocketGen         | [BioRxiv \(2024\) 2024-02](https://doi.org/10.1101/2024.02.25.581968) | [Github](https://github.com/zaixizhang/PocketGen)            | Bilevel graph transformer | Inpainting                               |
| 2024–03-07   | RFdiffusion-AA    | [Science \(2024\): eadl2528](https://www.science.org/doi/10.1126/science.adl2528) | [Github](https://github.com/baker-laboratory/rf_diffusion_all_atom) | Diffusion-based           | General                                  |


# de novo peptide design
| Release Date | Name             | Reference                                                    | Repository                                                   | Model Type                 | Experimental Validation | Notes                  |
|--------------|------------------|--------------------------------------------------------------|--------------------------------------------------------------|----------------------------|-------------------------|------------------------|
| 2022-05-10   | TERMs            | [Protein Science 31.6 \(2022\): e4322](https://onlinelibrary.wiley.com/doi/abs/10.1002/pro.4322) | N/A                                                          | Scaffold-based             | No                      |                        |
| 2022-05-24   | Rifdock          | [Nature 605.7910 \(2022\): 551](https://www.nature.com/articles/s41586-022-04654-9) | [Github](https://www.nature.com/articles/s41586-022-04654-9#code-availability) | RifDock + RifGen           | Yes                     | David Baker            |
| 2022-07-21   | RFdesign         | [Science 377.6604 \(2022\): 387](https://www.science.org/doi/full/10.1126/science.abn2100) | [Github](https://github.com/RosettaCommons/RFDesign)         | Hallucination + Inpainting | Yes                     | David Baker            |
| 2023-02-26   | AfCycDesign      | [BioRxiv \(2023\): 2023-02](https://www.biorxiv.org/content/10.1101/2023.02.25.529956v1) | [Github](https://github.com/sokrypton/ColabDesign/blob/main/af/examples/af_cyc_design.ipynb) | AF2-based                  | Yes                     | Cyclic Peptide         |
| 2023-05-26   | dl-binder-design | [Nat. Commun 14.1 \(2023\): 2625](https://www.nature.com/articles/s41467-023-38328-5) | [Github](https://github.com/nrbennet/dl_binder_design)       | Hallucination + Inpainting | Yes                     | David Baker, Benchmark |
| 2023-06-28   | PepPrCLIP        | [BioRxiv \(2023\): 2023-06](https://www.biorxiv.org/content/10.1101/2023.06.26.546591v1) | [Github](https://github.com/programmablebio/pepprclip)N/A    | ESM2 + Diffusion           | Yes                     |                        |
| 2023-08-08   | Evoplay          | [Nat. Mach. Intell 5.8 \(2023\): 845](https://www.nature.com/articles/s42256-023-00691-9) | [Github](https://github.com/melobio/EvoPlay)                 | RL-based                   | Yes                     |                        |
| 2023-08-26   | AfDesign         | [IJMS 24.17 \(2023\): 13257](https://www.mdpi.com/1422-0067/24/17/13257) | [Code1](https://github.com/ohuelab/ColabDesign-cyclic-binder), [Code2](https://github.com/ohuelab/ColabFold-cycpep-dock) | AF2-based                  | No                      | Cyclic Peptide         |
| 2023-09-24   | EvoPro           | [PNAS 120.49 \(2023\): e2307371120](https://www.pnas.org/doi/abs/10.1073/pnas.2307371120) | [Github](https://github.com/Kuhlman-Lab/evopro)              | AF2 + ProteinMPNN          | Yes                     |                        |
| 2023-10-05   | PepMLM           | [ArXiv:2310.03842](https://arxiv.org/abs/2310.03842)         | [Github](https://github.com/programmablebio/pepmlm)          | ESM2-based                 | Yes                     |                        |
| 2023-10-23   | ColabFold + BO   | [GenBio@NeurIPS2023 Poster](https://openreview.net/forum?id=CsjGuWD7hk) | N/A                                                          | ColabFold and BO           | No                      | Bayesian Optimization  |
| 2023-10-25   | EvoBind          | [Commun. Chem 6.1 \(2023\): 229](https://www.nature.com/articles/s42004-023-01029-7) | [Gitlab](https://gitlab.com/patrickbryant1/binder_design)    | Foldseek + ESM-IF1         | Yes                     |                        |
| 2024-03-08   | PPFlow           | [BioRxiv \(2024\): 2024-03](https://www.biorxiv.org/content/10.1101/2024.03.07.583831v1) | N/A                                                          | Flow-based                 | No                      | PPBench2024            |
| 2024-02-21   | PepGLAD          | [ArXiv:2402.13555](https://arxiv.org/abs/2402.13555)         | N/A                                                          | Diffusion-based            | No                      |                        |
| 2024-03-06   | AMP-Diffusion    | [BioRxiv \(2024\): 2024-03](https://www.biorxiv.org/content/10.1101/2024.03.03.583201v1.abstract) | N/A                                                          | ESM2 + Diffusion           | No                      |                        |
