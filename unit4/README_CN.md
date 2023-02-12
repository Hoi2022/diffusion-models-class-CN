# Unit 4: 深入研究扩散模型

欢迎来到 Hugging Face 扩散模型课程的第四单元！在这个单元中，我们将探讨最新研究中出现的扩散模型的许多改进和扩展。它将比以往的单元代码更少，旨在为您提供进一步研究的起点。

## 开始本单元 :rocket:

以下是本单元的学习步骤：

- 请确保你已经 [注册了该课程](https://huggingface.us17.list-manage.com/subscribe?u=7f57e683fa28b51bfc493d048&id=ef963b4162) 以便在课程中添加其他单元时通知您
- 阅读下面的材料，以了解本单元中涉及的不同主题的梗概
- 通过链接的视频和资源深入了解任何您感兴趣的主题
- 浏览演示笔记本，然后阅读“下一步”部分以获取一些项目建议

:loudspeaker: 别忘了加入我们的 [Discord频道](https://huggingface.co/join/discord), 在这里，您可以讨论课程内容，并在`#diffusion-models-class` 频道中分享您的作品！

## Table of Contents

- [Unit 4: 深入研究扩散模型](#unit-4-going-further-with-diffusion-models)
  - [开始本单元 :rocket:](#start-this-unit-rocket)
  - [课程目录](#table-of-contents)
  - [通过蒸馏进行快速采样](#faster-sampling-via-distillation)
  - [训练改进](#training-improvements)
  - [对生成与编辑的更多控制](#more-control-for-generation-and-editing)
  - [视频](#video)
  - [音频](#audio)
  - [新的体系结构和方法 - 走向“迭代优化”](#new-architectures-and-approaches---towards-iterative-refinement)
  - [动手笔记本](#hands-on-notebooks)
  - [下一步？](#where-next)


## 通过蒸馏进行快速采样

渐进蒸馏是一种采用现有扩散模型并使用它来训练需要更少推理步骤的模型的新版本的技术。“学生”模型初始化自“教师”模型的权重。在训练过程中，教师模型执行两个采样步骤，学生模型尝试在一个步骤中匹配结果预测。这个过程可以重复多次，上一次迭代的学生模型成为下一阶段的教师模型。结果是，这个模型可以用比原来的教师模型少得多的步骤(通常是4或8步)生成较好的样本。核心机制如下图所示，图片来源于[这篇论文](http://arxiv.org/abs/2202.00512):

![image](https://user-images.githubusercontent.com/6575163/211016659-7dac24a5-37e2-45f9-aba8-0c573937e7fb.png)

_渐进蒸馏图示 (来源于[该篇论文](http://arxiv.org/abs/2202.00512))_

使用现有模型“教授”新模型的想法可以扩展到创建指导模型，其中教师模型使用无分类器指导技术，学生模型必须学会在指定目标指导尺度的额外输入的基础上，在单个步骤中产生等价的输出。[这个视频](https://www.youtube.com/watch?v=ZXuK6IRJlnk) 对该方法做了一个概述。

NB: Stable Diffusion的蒸馏版将很快发布。

主要文献:
- [Progressive Distillation For Fast Sampling Of Diffusion Models](http://arxiv.org/abs/2202.00512)
- [On Distillation Of Guided Diffusion Models](http://arxiv.org/abs/2210.03142)

## 训练改进

研究者们现在已经开发了一些额外的技巧来改进扩散模型的训练过程。在本节中，我们试图从最近的论文中寻找一些相关的核心理念。更多关于改进方法的研究论文也是不断涌现，因此，如果你看到一篇你觉得应该添加在这里的论文，请联系我们!

![image](https://user-images.githubusercontent.com/6575163/211021220-e87ca296-cf15-4262-9359-7aeffeecbaae.png)
_图像2来自于 [ERNIE-ViLG 2.0 paper](http://arxiv.org/abs/2210.15257)_

关键的训练改进:
- 调整噪声计划、损失加权和采样轨迹，以获得更有效的训练。这里是一篇探讨这些设计选择的优秀论文 [Elucidating the Design Space of Diffusion-Based Generative Models](http://arxiv.org/abs/2206.00364) by Karras et al.
- 在不同的长宽比上进行训练。这一方法被描述在[课程启动活动提到的这个视频中](https://www.youtube.com/watch?v=g6tIUrMvOec)
- 级联扩散模型，首先训练一个低分辨率模型，然后训练一个或多个超分辨率模型。这一方法被广泛运用于DALLE-2，Imagen等高分辨率图像生成模型中。
- Better conditioning, incorporating rich text embeddings ([Imagen](https://arxiv.org/abs/2205.11487) uses a large language model called T5) or multiple types of conditioning ([eDiffi](http://arxiv.org/abs/2211.01324))
- 更好的调节、结合富文本嵌入 ([Imagen](https://arxiv.org/abs/2205.11487) 使用了一个名为T-5的大语言模型) 或进行多种类型的调节 ([eDiffi](http://arxiv.org/abs/2211.01324))
- “知识增强” - 将预先训练的图像描述和物体检测模型纳入训练过程，以创建更有信息的描述，并产生更好的表现 ([ERNIE-ViLG 2.0](http://arxiv.org/abs/2210.15257))
- “复合降噪专家” (MoDE) - 训练模型的不同变体(“专家”)以适应不同的噪声水平，如上图所示 [ERNIE-ViLG 2.0 paper](http://arxiv.org/abs/2210.15257).

主要文献:
- [Elucidating the Design Space of Diffusion-Based Generative Models](http://arxiv.org/abs/2206.00364)
- [eDiffi: Text-to-Image Diffusion Models with an Ensemble of Expert Denoisers](http://arxiv.org/abs/2211.01324)
- [ERNIE-ViLG 2.0: Improving Text-to-Image Diffusion Model with Knowledge-Enhanced Mixture-of-Denoising-Experts](http://arxiv.org/abs/2210.15257)
- [Imagen - Photorealistic Text-to-Image Diffusion Models with Deep Language Understanding](https://arxiv.org/abs/2205.11487) ([demo site](https://imagen.research.google/))

## 对生成与编辑的更多控制

除了训练方面的改进，在采样和推断阶段也有一些创新方法。这其中有许多甚至可以为现有扩散模型添加新的功能。

![image](https://user-images.githubusercontent.com/6575163/212529129-3de41cf4-6f70-4607-8448-e9bbe9d190cf.png)
_由'paint-with-words'生成的样本 ([eDiffi](http://arxiv.org/abs/2211.01324))_

这个视频 ['使用扩散模型编辑图片'](https://www.youtube.com/watch?v=zcG7tG3xS3s) 概述了如何使用的不同的方法来通过应用扩散模型对已有的图片进行编辑。可用的技术主要分为四个主要类别:
1) 添加噪声，然后用一个新的提示（prompt）来进行去噪. 这就是 'img2img ' 管道背后的思想，已经在各种论文中得到了扩展:
- [SDEdit](https://sde-image-editing.github.io/) and [MagicMix](https://magicmix.github.io/) 这两篇论文都是建立在这一理念的基础上的
- DDIM 反演 (TODO link tutorial)使用模型来“反转”采样轨迹来取代添加随机噪声, 从而提供了更多的控制
- [Null-text Inversion](https://null-text-inversion.github.io/) 通过在每一步中优化用于无分类器引导的无条件文本嵌入，允许极高质量的基于文本的图像编辑，极大地增强了这种方法的性能。

2) 扩展了(1)中的思想，但使用蒙版来控制效果的应用位置
- [Blended Diffusion](https://omriavrahami.com/blended-diffusion-page/) 介绍了基础的理念
- [这个 demo](https://huggingface.co/spaces/nielsr/text-based-inpainting) 使用现有的分割模型(CLIPSeg)来创建基于文本描述的掩码
- [DiffEdit](https://arxiv.org/abs/2210.11427) 是一篇展示了如何使用扩散模型本身来生成适当的掩码，以根据文本编辑图像的优秀论文。
- [SmartBrush: Text and Shape Guided Object Inpainting with Diffusion Model](https://arxiv.org/abs/2212.05034) fine-tunes a diffusion model for more accurate mask-guided inpainting.
3) Cross-attention Control: using the cross-attention mechanism in diffusion models to control the spatial location of edits for more fine-grained control.
- [Prompt-to-Prompt Image Editing with Cross Attention Control](https://arxiv.org/abs/2208.01626) is the key paper that introduced this idea, and the technique has [since been applied to Stable Diffusion](https://wandb.ai/wandb/cross-attention-control/reports/Improving-Generative-Images-with-Instructions-Prompt-to-Prompt-Image-Editing-with-Cross-Attention-Control--VmlldzoyNjk2MDAy)
- This idea is also used for 'paint-with-words' ([eDiffi](http://arxiv.org/abs/2211.01324), shown above)
4) Fine-tune ('overfit') on a single image and then generate with the fine-tuned model. The following papers both published variants of this idea at roughly the same time:
- [Imagic: Text-Based Real Image Editing with Diffusion Models](https://arxiv.org/abs/2210.09276)
- [UniTune: Text-Driven Image Editing by Fine Tuning an Image Generation Model on a Single Image
](https://arxiv.org/abs/2210.09477)

The paper [InstructPix2Pix: Learning to Follow Image Editing Instructions](https://arxiv.org/abs/2211.09800) is notable in that it used some of the image editing techniques described above to build a synthetic dataset of image pairs alongside image edit instructions (generated with GPT3.5) to train a new model capable of editing images based on natural language instructions


## 视频

![image](https://user-images.githubusercontent.com/6575163/213657523-be40178a-4357-410b-89e3-a4cbd8528900.png)
_Still frames from [样本视频通过Imagen Video生成](https://imagen.research.google/video/)_

视频可以表示为一组图像的序列，而扩散模型的核心思想可以被应用于这些序列。最近的工作主要集中在寻找合适的架构(例如“3D unet”，它可以对整个序列进行操作)并有效地处理视频数据。由于高帧率视频比静止图像涉及更多的数据，目前的方法倾向于首先生成低分辨率和低帧率视频，然后应用空间和时间超分辨率方法来生成最终的高质量视频输出。

主要文献:
- [Video Diffusion Models](https://video-diffusion.github.io/)
- [IMAGEN VIDEO: HIGH DEFINITION VIDEO GENERATION WITH DIFFUSION MODELS](https://imagen.research.google/video/paper.pdf)

## 音频

![image](https://user-images.githubusercontent.com/6575163/213657272-a1b54017-216f-453b-9b28-97c6fef21f54.png)

_用Riffusion生成的一幅光谱图 ([图片来源](https://www.riffusion.com/about))_

While there has been some work on generating audio directly using diffusion models (e.g. [DiffWave](https://arxiv.org/abs/2009.09761)) the most successful approach so far has been to convert the audio signal into something called a spectrogram, which effectively 'encodes' the audio as a 2D "image" which can then be used to train the kinds of diffusion models we're used to using for image generation. The resulting generated spectrograms can then be converted into audio using existing methods. This approach is behind the recently-released Riffusion, which fine-tuned Stable Diffusion to generate spectrograms conditioned on text - [try it out here](https://www.riffusion.com/).

The field of audio generation is moving extremely quickly. Over the past week (at the time of writing) there have been at least 5 new advances announced, which are marked with a star in the list below:

主要文献:
- [DiffWave: A Versatile Diffusion Model for Audio Synthesis](https://arxiv.org/abs/2009.09761)
- ['Riffusion'](https://www.riffusion.com/about) (and [code](https://github.com/riffusion/riffusion))
- *[MusicLM](https://google-research.github.io/seanet/musiclm/examples/) by Google generates consistent audio from text and can be conditioned with hummed or whistled melodies
- *[RAVE2](https://github.com/acids-ircam/RAVE) - a new version of a Variational Auto-Encoder that will be useful for latent diffusion on audio tasks. This is used in the soon-to-be-announced *[AudioLDM](https://twitter.com/LiuHaohe/status/1619119637660327936?s=20&t=jMkPWBFuAH19HI9m5Sklmg) model
- *[Noise2Music](https://noise2music.github.io/) - A diffusion model trained to produce high-quality 30-second clips of audio based on text descriptions
- *[Make-An-Audio: Text-To-Audio Generation with Prompt-Enhanced Diffusion Models](https://text-to-audio.github.io/) - a diffusion model trained to generate diverse sounds based on text
- *[Moûsai: Text-to-Music Generation with Long-Context Latent Diffusion](https://arxiv.org/abs/2301.11757)

## 新的体系结构和方法 - 走向“迭代优化”

![image](https://user-images.githubusercontent.com/6575163/213731066-0fbe38a7-233f-42be-99fc-38cea889c86b.png)

_图片1来自 [Cold Diffusion](http://arxiv.org/abs/2208.09392) paper_

我们正在慢慢地超越最初的“扩散”模型的狭义定义，并向更一般的、执行**迭代优化**的一类模型前进。这其中某种形式的破坏(例如在正向扩散过程中添加高斯噪声)被逐渐逆转以生成样本。这篇有关“冷扩散”的论文证明了许多其他类型的退化过程可以迭代地被“撤销”以生成图像(如上所示的示例)。同时最近基于transformer的方法也证明了符记替换(token replacement)或遮蔽(masking)作为噪声策略的有效性。

![image](https://user-images.githubusercontent.com/6575163/213731351-7fd6c98c-6ba6-4bd9-a898-230002fc334f.png)

_Pipeline from [MaskGIT](http://arxiv.org/abs/2202.04200)_

The UNet architecture at the heart of many current diffusion models is also being replaced with different alternatives, most notably various transformer-based architectures. In [Scalable Diffusion Models with Transformers (DiT)](https://www.wpeebles.com/DiT) a transformer is used in place of the UNet for a fairly standard diffusion model approach, with excellent results. [Recurrent Interface Networks](https://arxiv.org/pdf/2212.11972.pdf) applies a novel transformer-based architecture and training strategy in pursuit of additional efficiency. [MaskGIT](http://arxiv.org/abs/2202.04200) and [MUSE](http://arxiv.org/abs/2301.00704) use transformer models to work with tokenized representations of images, although the [Paella](https://arxiv.org/abs/2211.07292v1) model demonstrates that a UNet can also be applied successfully to these token-based regimes too.

随着每一篇新论文的发表，更有效的方法正在被开发出来。我们可能还需要一段时间才能看到这种迭代细化任务的巅峰性能。我们十分期待还有更多的东西被探索和发掘!

主要文献
- [Cold Diffusion: Inverting Arbitrary Image Transforms Without Noise](http://arxiv.org/abs/2208.09392)
- [Scalable Diffusion Models with Transformers (DiT)](https://www.wpeebles.com/DiT)
- [MaskGIT: Masked Generative Image Transformer](http://arxiv.org/abs/2202.04200)
- [Muse: Text-To-Image Generation via Masked Generative Transformers](http://arxiv.org/abs/2301.00704)
- [Fast Text-Conditional Discrete Denoising on Vector-Quantized Latent Spaces (Paella)](https://arxiv.org/abs/2211.07292v1)
- [Recurrent Interface Networks](https://arxiv.org/pdf/2212.11972.pdf) - 一种很有前途的新架构，可以在不依赖latent diffusion或超分辨率模型的情况下生成高分辨率图像。另请参阅 [simple diffusion: End-to-end diffusion for high-resolution images](https://arxiv.org/abs/2301.11093) 该论文强调了噪声表(noise schedule)在高分辨率训练中的重要性。

## 动手笔记本

| Chapter                                     | Colab                                                                                                                                                                                               | Kaggle                                                                                                                                                                                                   | Gradient                                                                                                                                                                               | Studio Lab                                                                                                                                                                                                   |
|:--------------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| DDIM Inversion                                | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/huggingface/diffusion-models-class/blob/main/unit4/01_ddim_inversion.ipynb)              | [![Kaggle](https://kaggle.com/static/images/open-in-kaggle.svg)](https://kaggle.com/kernels/welcome?src=https://github.com/huggingface/diffusion-models-class/blob/main/unit4/01_ddim_inversion.ipynb)              | [![Gradient](https://assets.paperspace.io/img/gradient-badge.svg)](https://console.paperspace.com/github/huggingface/diffusion-models-class/blob/main/unit4/01_ddim_inversion.ipynb)              | [![Open In SageMaker Studio Lab](https://studiolab.sagemaker.aws/studiolab.svg)](https://studiolab.sagemaker.aws/import/github/huggingface/diffusion-models-class/blob/main/unit4/01_ddim_inversion.ipynb)              |
| Diffusion for Audio                                | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/huggingface/diffusion-models-class/blob/main/unit4/02_diffusion_for_audio.ipynb)              | [![Kaggle](https://kaggle.com/static/images/open-in-kaggle.svg)](https://kaggle.com/kernels/welcome?src=https://github.com/huggingface/diffusion-models-class/blob/main/unit4/02_diffusion_for_audio.ipynb)              | [![Gradient](https://assets.paperspace.io/img/gradient-badge.svg)](https://console.paperspace.com/github/huggingface/diffusion-models-class/blob/main/unit4/02_diffusion_for_audio.ipynb)              | [![Open In SageMaker Studio Lab](https://studiolab.sagemaker.aws/studiolab.svg)](https://studiolab.sagemaker.aws/import/github/huggingface/diffusion-models-class/blob/main/unit4/02_diffusion_for_audio.ipynb)              |

在本单元中，我们已经讨论了很多不同的想法，其中许多值得在未来的后续课程中进行更详细的学习。现在，你可以通过我们准备的动手笔记本来学习其中的两个主题。
- **DDIM Inversion** 展示了如何使用一种被称为“反转”的技术来使用已有的扩散模型编辑图像
- **Diffusion for Audio** 介绍了频谱图的思想，并展示了如何对特定音乐风格的音频扩散模型进行微调。

## 下一步？

这是本课程的最后一个单元，这意味着接下来要做什么取决于你! 请记得你永远都可以来Hugging Face [discord频道](https://huggingface.co/join/discord)上来询问问题或者聊聊你自己的project。 我们期待看到你的创作！ 🤗
