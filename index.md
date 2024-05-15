<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.10.2/dist/katex.min.css" integrity="sha384-yFRtMMDnQtDRO8rLpMIKrtPCD5jdktao2TV19YiZYWMDkUR5GQZR/NOVTdquEx1j" crossorigin="anonymous">
<script defer src="https://cdn.jsdelivr.net/npm/katex@0.10.2/dist/katex.min.js" integrity="sha384-9Nhn55MVVN0/4OFx7EE5kpFBPsEMZxKTCnA+4fqDmg12eCTqGi6+BB2LjY8brQxJ" crossorigin="anonymous"></script>
<script defer src="https://cdn.jsdelivr.net/npm/katex@0.10.2/dist/contrib/auto-render.min.js" integrity="sha384-kWPLUVMOks5AQFrykwIup5lo0m3iMkkHrD0uJ4H5cjeGihAutqP0yW0J6dpFiVkI" crossorigin="anonymous" onload="renderMathInElement(document.body);"></script>

<style>
audio{
  width:200px;
  height:40px;
}
audio::-webkit-media-controls-timeline,
audio::-webkit-media-controls-seek-back-button,
audio::-webkit-media-controls-seek-forward-button {
  display: none !important;
}

.diag{
  display: block;
  margin-left: auto;
  margin-right: auto;
  /* width: 60%; */
  height:200px;
}
</style>

# Diff-A-Riff: Musical Accompaniment Co-creation via Latent Diffusion Models

This is the accompanying website to "Diff-A-Riff: Musical Accompaniment Co-creation via Latent Diffusion Models".

  * [System's Overview](#systems-overview)
  * [Sound Examples](#sound-examples)
    + [Accompaniment Generation : a context is provided](#accompaniment-generation--a-context-is-provided)
      - [With Audio CLAP](#with-audio-clap)
      - [With Text CLAP](#with-text-clap)
      - [Context-only, no CLAP](#context-only-no-clap)
      - [Complete Music Excerpts](#complete-music-excerpts)
    + [Single Track generation : no context is provided](#single-track-generation--no-context-is-provided)
      - [With Audio CLAP](#with-audio-clap-1)
      - [With Text CLAP](#with-text-clap-1)
      - [Fully Unconditional](#fully-unconditional)
    + [Bonus](#bonus)
      - [Inpainting](#inpainting)
      - [Interpolations](#interpolations)
      - [Stereo width](#stereo-width)
      - [Variations](#variations)
      - [Loop Sampling](#loop-sampling)
  * [User Study Sample Questions](#user-study-sample-questions)
    + [Audio Quality Assessment](#audio-quality-assessment)
    + [Subjective Audio Prompt Adherence](#subjective-audio-prompt-adherence)
  * [Ethics Statement](#ethics-statement)
  * [Extra References](#extra-references)

## System's Overview 

<figure>
  <img src="https://sonycslparis.github.io/diffariff-companion/diff-a-riff-website.png" alt="Overview of Diff-A-Riff"/>
  <!-- <figcaption> <b>Overview of Diff-A-Riff.</b> The CAE Encoder transforms the music context into a compressed representation, concatenated with a noisy sample, and further processed through the multi-scale U-Net. The generated latent sequence is decoded into audio via the CAE Decoder.</figcaption> -->
</figure>



In this work, we introduce Diff-A-Riff, a Latent Diffusion Model capable of generating instrumental accompaniments for any musical audio context. 

Our approach relies on a pretrained consistency model-based Autoencoder (CAE) and we train a generative model on its latent embeddings. The proposed generative model is a LDM following the framework of Elucidated Diffusion Models (EDMs). The architecture follows DDPM++, an upgraded version of the originally proposed Diffusion Probabilistic Model. 

Given a pair of input context and target accompaniment audio segments, the model is trained to reconstruct the accompaniment given the context and a CLAP embedding derived from a randomly selected sub-segment of the target itself. At inference time, Diff-A-Riff allows to generate single instrument tracks under different conditioning signals.
- First, a user can choose to provide a context, which is a piece of music that the generated material has to fit into. If provided, the context is encoded by the CAE to give a sequence of latents that we call $$\textit{Context}$$. When a context provided, we talk of accompaniment generation instead of single instrument generation.
- Then, the user can also rely on CLAP-derived embedings to further specify the material to be generated. CLAP provides a multimodal embedding space shared between audio and text modalities. This means that the user can provide either a music reference or a text prompt, which after being encoded in CLAP give $$\textit{CLAP}_\text{A}$$ and $$\textit{CLAP}_\text{T}$$ respectively.

## Sound Examples
In this section, we demonstrate the generation abilities of our model under different conditioning signals. When no detail is provided, the generations are computed using $$\text{CFG}_\textit{Context} = \text{CFG}_\textit{CLAP} = 1.25$$, $$T=30$$ diffusion steps and a stereo width $$s_\text{Stereo}=0.4$$ .

### Accompaniment Generation : a context is provided
In this section, we demonstrate the ability of Diff-A-Riff to generate **accompaniments**, single tracks that fit a pre-existing context.

#### With Audio CLAP

<!-- ![Context + Audio CLAP setup](https://sonycslparis.github.io/diffariff-companion/diags/ctx_clapa.png) -->
<img class="diag" src="https://sonycslparis.github.io/diffariff-companion/diags/ctx_clapa.png" alt="Context + Audio CLAP setup"/>


Diff-A-Riff allows the generation of accompaniments based on an audio reference. This represent conditioning on $$\textit{CLAP}_\text{A}$$ and $$\textit{Context}$$, and is equivalent to the training setup. Here, we present various context music pieces and various audio-based accompaniments.

<table class="tg">
<thead>
  <tr>
    <th class="tg-0pky">Context</th>
    <th class="tg-0pky">Audio Reference</th>
    <th class="tg-0lax" colspan="3">Accompaniments</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-0pky" rowspan="2"><sup>1</sup> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/context/1/context.mp3" type="audio/mp3"> Your Browser does not support the audio tag </audio></td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/context/1/ref1/ref.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/context/1/ref1/gen1_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/context/1/ref1/gen2_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/context/1/ref2/ref.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/context/1/ref2/gen1_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/context/1/ref2/gen2_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0pky" rowspan="2"><sup>2</sup><audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/context/2/context.mp3" type="audio/mp3"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/context/2/ref1/ref.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/context/2/ref1/gen1_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/context/2/ref1/gen2_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/context/2/ref2/ref.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/context/2/ref2/gen1_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/context/2/ref2/gen2_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
    <tr>
    <td class="tg-0pky" rowspan="2"><sup>3</sup> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/context/3/context.mp3" type="audio/mp3"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/context/3/ref1/ref.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/context/3/ref1/gen1_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/context/3/ref1/gen2_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/context/3/ref2/ref.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/context/3/ref2/gen1_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/context/3/ref2/gen2_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
</tbody>
</table>

<sup>1</sup> : James May - If You Say, from the [DSD100 Dataset](https://sigsep.github.io/datasets/dsd100.html) [[2](#extra-references)]

<sup>2</sup> : Sambasevam Shanmugam - Kaathaadi, from the [DSD100 Dataset](https://sigsep.github.io/datasets/dsd100.html)

<sup>3</sup> : Melody loop by DaveJf, from [FreeSound](https://freesound.org/s/724935/)

#### With Text CLAP

<!-- ![Context + Text CLAP setup](https://sonycslparis.github.io/diffariff-companion/diags/ctx_clapt.png) -->
<img class="diag" src="https://sonycslparis.github.io/diffariff-companion/diags/ctx_clapt.png" alt="Context + Text CLAP setup" />

Diff-A-Riff also allows to specify the accompaniment using a text prompt. This represents conditioning on $$\textit{CLAP}_\text{T}$$ and $$\textit{Context}$$. Here, we present various context music pieces and various text-based accompaniments.

<table class="tg">
<thead>
  <tr>
    <th class="tg-0pky">Context</th>
    <th class="tg-0pky">Text Prompt</th>
    <th class="tg-0lax" colspan="3">Accompaniments</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-0pky" rowspan="2"><sup>3</sup> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/text-cond/context/1/context.mp3" type="audio/mp3"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky">"A solo ukulele delivering a cheerful and sunny accompaniment."</td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/text-cond/context/1/prompt1/gen1_mix_vol.wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/text-cond/context/1/prompt1/gen2_mix_vol.wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0pky">"A mellow synthesizer playing ethereal pads."</td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/text-cond/context/1/prompt2/gen1_mix_vol.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/text-cond/context/1/prompt2/gen2_mix_vol.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0pky" rowspan="2"> <sup>4</sup><audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/text-cond/context/2/context.mp3" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky">"Drums with reverb and a lot of toms."</td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/text-cond/context/2/prompt1/gen1_mix_vol.wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/text-cond/context/2/prompt1/gen2_mix_vol.wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0pky">"Drums with reverb and a lot of hats/cymbals."</td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/text-cond/context/2/prompt2/gen1_mix_vol.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/text-cond/context/2/prompt2/gen2_mix_vol.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
</tbody>
</table>

<sup>3</sup> : Fergessen - Back From The Start, from the [DSD100 Dataset](https://sigsep.github.io/datasets/dsd100.html)

<sup>4</sup> : Leaf - Come Around, from the [DSD100 Dataset](https://sigsep.github.io/datasets/dsd100.html) 

#### Context-only, no CLAP

<!-- ![Context only setup](https://sonycslparis.github.io/diffariff-companion/diags/noclap.png) -->
<img class="diag" src="https://sonycslparis.github.io/diffariff-companion/diags/noclap.png" alt="Context only setup"/>



The model can generate accompaniments from a context only, without the need for CLAP embeddings ($$\textit{Context}$$ only).

<table class="tg">
<thead>
  <tr>
    <th class="tg-0pky">Context</th>
    <th class="tg-0pky" colspan="4">Accompaniments</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/context-only/1/context.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/context-only/1/gen1_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/context-only/1/gen2_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/context-only/1/gen3_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/context-only/1/gen4_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/context-only/2/context.mp3" type="audio/mp3"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/context-only/2/gen1_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/context-only/2/gen2_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/context-only/2/gen3_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
        <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/context-only/2/gen4_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/context-only/3/context.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/context-only/3/gen5_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/context-only/3/gen2_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/context-only/3/gen3_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <!-- <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/context-only/3/gen4_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td> -->
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/context-only/3/gen1_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>  
  <tr>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/context-only/4/context.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/context-only/4/gen1_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/context-only/4/gen2_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/context-only/4/gen3_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/context-only/4/gen4_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
</tbody>
</table>


#### Complete Music Excerpts

<img src="https://sonycslparis.github.io/diffariff-companion/diags/fulltracks.png" alt="Complete Music Excerpts"/>

Despite Diff-A-Riff generating only solo instrumental tracks, we are able to generate multi track music pieces. Starting from an ensemble of $$n$$ audio references from the evaluation set and an empty context, we iteratively generate new tracks which are summed into the context to condition the next iteration. After $$n$$ iterations, the initially empty context has become a full mix. Here you can find excerpts of multitrack music generated this way.

<table class="tg">
<tbody>
  <tr>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/full/track_1.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/full/track_2.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/full/track_3.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/full/track_4.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/full/track_5.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/full/track_6.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/full/track_7.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/full/track_8.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/full/track_9.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>

  <tr>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/full/track_10.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/full/track_11.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/full/track_12.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
 
</tbody>
</table>



### Single Track generation : no context is provided
Diff-A-Riff also allows the generation of solo instrument tracks without a context. We then refer to it as **single track generation**, rather than accompaniment.

#### With Audio CLAP

<!-- ![Audio CLAP only setup](https://sonycslparis.github.io/diffariff-companion/diags/clapa.png) -->
<img class="diag" src="https://sonycslparis.github.io/diffariff-companion/diags/clapa.png" alt="Audio CLAP only setup"/>


Diff-A-Riff also allows the generation of solo instrument tracks conditioned on audio only ($$\textit{CLAP}_\text{A}$$ only).
<table class="tg">
<thead>
  <tr>
    <th class="tg-0pky">Audio Reference</th>
    <th class="tg-0lax" colspan="3">Generations</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/no-context/1/ref.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/no-context/1/gen1.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/no-context/1/gen2.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/no-context/1/gen3.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/no-context/2/ref.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/no-context/2/gen1.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/no-context/2/gen2.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/no-context/2/gen3.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/no-context/3/ref.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/no-context/3/gen1.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/no-context/3/gen2.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/audio-cond/no-context/3/gen3.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
</tbody>
</table>

#### With Text CLAP

<!-- ![Text CLAP only setup](https://sonycslparis.github.io/diffariff-companion/diags/clapt.png) -->
<img class="diag" src="https://sonycslparis.github.io/diffariff-companion/diags/clapt.png" alt="Text CLAP only setup"/>


In this section, you can hear single instrument tracks generated solely from a text prompt ($$\textit{CLAP}_\text{T}$$ only).

<table class="tg">
<thead>
  <tr>
    <th class="tg-0pky">Text Prompt</th>
    <th class="tg-0lax" colspan="3">Generations</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-0pky">"Guitar played folk style."</td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/text-cond/no-context/1/gen1.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/text-cond/no-context/1/gen2.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0pky">"Slow evolving pad synth."</td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/text-cond/no-context/2/gen1.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/text-cond/no-context/2/gen2.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0pky">"A vibrant, funky bassline characterized by the electrifying slap technique, where each note pops with a distinct rhythmic snap and sizzle."</td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/text-cond/no-context/3/gen1.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/text-cond/no-context/3/gen2.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0pky">"A pulsating techno drum beat, where a deep bass kick thunders every quarter note, creating a relentless and hypnotic pulse."</td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/text-cond/no-context/4/gen1.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/text-cond/no-context/4/gen2.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
</tbody>
</table>


#### Fully Unconditional

<!-- ![Unconditional setup](https://sonycslparis.github.io/diffariff-companion/diags/uncond.png) -->
<img class="diag" src="https://sonycslparis.github.io/diffariff-companion/diags/uncond.png" alt="Unconditional setup"/>


In this section, we show single instrument tracks generated without context or CLAP conditioning.
<table class="tg">
<tbody>
  <tr>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/uncond/gen.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/uncond/gen1.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/uncond/gen2.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/uncond/gen3.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/uncond/gen4.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/uncond/gen5.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/uncond/gen6.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/uncond/gen7.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/uncond/gen8.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/uncond/gen9.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/uncond/gen10.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/uncond/gen11.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/uncond/gen12.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/uncond/gen13.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/uncond/gen14.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  
</tbody>
</table>





### Bonus
In this section, we demonstrate additional controls derived from the use of a diffusion framework.

#### Inpainting
As done traditionally with diffusion models, Diff-A-Riff can be used to perform audio inpainting. At each denoising step, the region to be kept is replaced with the corresponding noisy region of the final output, while the inpainted region is denoised normally. This way, we can enforce the inpainted region to blend well with the surroundings.

In line with most diffusion models, Diff-A-Riff allows to perform audio inpainting. During each denoising iteration, the inpainted area undergoes standard denoising, while the region to keep is substituted with its noisy counterpart from the final output. This approach ensures seamless integration of the inpainted section with its surroundings.
In the following examples, all tracks are inpainted from second 5 to 8.

<table class="tg">
<thead>
  <tr>
    <th class="tg-0pky">Context</th>
    <th class="tg-0lax">Masked Original Accompaniment</th>
    <!-- <th class="tg-0lax" colspan="4">Inpaintings</th> -->
    <th class="tg-0pky">Inpainting w/ Mix</th>
    <th class="tg-0lax">Inpainting Solo</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-0lax" rowspan="3"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/inpainting/1/context.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax" rowspan="3"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/inpainting/1/original_masked.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/inpainting/1/inpainting0_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/inpainting/1/inpainting0.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/inpainting/1/inpainting1_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/inpainting/1/inpainting1.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/inpainting/1/inpainting2_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/inpainting/1/inpainting2.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
<!-- new ctx -->
  <tr>
    <td class="tg-0lax" rowspan="3"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/inpainting/2/context.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax" rowspan="3"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/inpainting/2/original_masked.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/inpainting/2/inpainting0_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/inpainting/2/inpainting0.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/inpainting/2/inpainting1_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/inpainting/2/inpainting1.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/inpainting/2/inpainting2_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/inpainting/2/inpainting2.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
<!-- new ctx -->
  <tr>
    <td class="tg-0lax" rowspan="3"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/inpainting/3/context.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax" rowspan="3"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/inpainting/3/original_masked.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/inpainting/3/inpainting0_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/inpainting/3/inpainting0.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/inpainting/3/inpainting1_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/inpainting/3/inpainting1.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/inpainting/3/inpainting2_mix.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/inpainting/3/inpainting2.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
<!-- new ctx -->
</tbody>
</table>

#### Interpolations

<img src="https://sonycslparis.github.io/diffariff-companion/diags/interpolations.png" alt="Interpolations"/>

We can interpolate between different references in the CLAP space. Here, we demonstrate the impact of a interpolation between an audio-derived CLAP embedding and a text-derived one, with an interpolation ratio $$r$$.

<table class="tg">
<tbody>
  <tr>
    <th class="tg-0pky"> Audio Reference </th>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/2/ref.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/3/ref.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/4/ref.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/5/ref.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <th class="tg-0pky"> $$ r = 0 $$ </th>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/2/gen0.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/3/gen0.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/4/gen0.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/5/gen0.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <th class="tg-0pky"> $$ r = 0.2 $$ </th>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/2/gen0.2.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/3/gen0.2.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/4/gen0.2.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/5/gen0.2.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>

  <tr>
    <th class="tg-0pky"> $$ r = 0.4 $$ </th>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/2/gen0.4.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/3/gen0.4.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/4/gen0.4.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/5/gen0.4.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <th class="tg-0pky"> $$ r = 0.6 $$ </th>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/2/gen0.6.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/3/gen0.6.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/4/gen0.6.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/5/gen0.6.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <th class="tg-0pky"> $$ r = 0.8 $$ </th>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/2/gen0.8.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/3/gen0.8.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/4/gen0.8.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/5/gen0.8.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <th class="tg-0pky"> $$ r = 1 $$ </th>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/2/gen1.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/3/gen1.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/4/gen1.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0pky"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/interpolations/5/gen1.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
    <tr>
    <th class="tg-0pky"> Text Prompt </th>
    <td class="tg-0pky"> "Latin Percussion" </td>
    <td class="tg-0pky"> "Oriental Percussion Texture" </td>
    <td class="tg-0pky"> "The Cathciest Darbouka Rhythm" </td>
    <td class="tg-0pky"> "Rhythm on Huge Industrial Metal Plates" </td>
  </tr>
  
</tbody>
</table>

#### Variations

<img class="diag" src="https://sonycslparis.github.io/diffariff-companion/diags/variation.png" alt="Variation Generation"/>

Given an audio file, we can encode it in the CAE latent space and get the corresponding latent sequence. By adding noise to it, and denoising this noisy sequence again, we end up with a variation of the first sequence. We can then decode it to obtain a variation of the input audio. The amount of noise added is controlled through the variation strength parameter $$s_\text{Var}$$, which allows to control how different to the original a variation can be.

<table class="tg">
<thead>
  <tr>
    <th class="tg-0pky">Reference</th>
    <th class="tg-0lax">$$s_\text{Var} = 0.2$$</th>
    <th class="tg-0lax">$$0.5$$</th>
    <th class="tg-0lax">$$0.8$$</th>

  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/variations/1/orig.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/variations/1/0.20.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/variations/1/0.50.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/variations/1/0.80.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/variations/2/orig.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/variations/2/0.20.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/variations/2/0.50.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/variations/2/0.80.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/variations/3/orig.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/variations/3/0.20.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/variations/3/0.50.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/variations/3/0.80.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/variations/4/orig.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/variations/4/0.20.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/variations/4/0.50.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/variations/4/0.80.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>

</tbody>
</table>


#### Stereo width

<img src="https://sonycslparis.github.io/diffariff-companion/diags/stereo.png" alt="Pseudo Stereo Generation"/>

Following the same principle as for variations, for any mono signal, we can create a slight variation of it and use the original and the variation as left and right channels, creating what we call pseudo-stereo. Here, you can find examples of pseudo stereo files, generated from different stereo width $$s_\text{Stereo}$$.

<table class="tg">
<thead>
  <tr>
    <th class="tg-0pky"> $$s_\text{Stereo} = 0$$ </th>
    <th class="tg-0lax">$$0.2$$</th>
    <th class="tg-0lax">$$0.4$$</th>
    <th class="tg-0lax">$$0.5$$</th>

  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/stereo/1/0.0.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/stereo/1/0.2.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/stereo/1/0.4.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/stereo/1/0.5.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/stereo/2/0.0.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/stereo/2/0.2.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/stereo/2/0.4.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/stereo/2/0.5.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
    <tr>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/stereo/3/0.0.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/stereo/3/0.2.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/stereo/3/0.4.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/stereo/3/0.5.wav" type="audio/wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
</tbody>
</table>

#### Loop Sampling

<img src="https://sonycslparis.github.io/diffariff-companion/diags/loop.png" alt="Loop Sampling"/>

By repeating a portion of the data being denoised, we can enforce repetitions in the generated material. We can enforce this repetition for a fraction $$s_\text{Loop}$$ of the diffusion steps, and let the model denoise normally for the remaining last steps to introduce slight variations.
<table class="tg">
<thead>
  <tr>
    <th class="tg-0pky">Number of Repetitions</th>
    <th class="tg-0pky">$$s_\text{Loop}$$</th>
    <th class="tg-0lax" colspan="3">Generations</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-0pky" rowspan="3"> 2 </td>
    <td class="tg-0pky">0.5</td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/loops/0.5_2_reps.wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/loops/0.5_2_reps_1.wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/loops/0.5_2_reps_2.wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0pky">0.8</td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/loops/0.8_2_reps.wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/loops/0.8_2_reps_1.wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/loops/0.8_2_reps_2.wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0pky">1</td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/loops/1_2_reps.wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/loops/1_2_reps_1.wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/loops/1_2_reps_2.wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0pky" rowspan="3"> 4 </td>
    <td class="tg-0pky">0.5</td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/loops/0.5_4_reps.wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/loops/0.5_4_reps_1.wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/loops/0.5_4_reps_2.wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0pky">0.8</td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/loops/0.8_4_reps.wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/loops/0.8_4_reps_1.wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/loops/0.8_4_reps_2.wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
  <tr>
    <td class="tg-0pky">1</td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/loops/1_4_reps.wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/loops/1_4_reps_1.wav"> Your Browser does not support the audio tag </audio> </td>
    <td class="tg-0lax"> <audio controls controlsList="nodownload"><source src="https://sonycslparis.github.io/diffariff-companion/audios/bonus/loops/1_4_reps_2.wav"> Your Browser does not support the audio tag </audio> </td>
  </tr>
</tbody>
</table>


## User Study Sample Questions
Here, you can find examples of the questions participants had to answer in our user studies. The user studies were conducted using the [GoListen](https://golisten.ucd.ie/) online platform [[1](#extra-references)].

### Audio Quality Assessment 

[Here is a link to a demo of the Quality Assessment test.](https://golisten.ucd.ie/task/acr-test/662643b2552c347eae00849d)

### Subjective Audio Prompt Adherence

[Here is a link to a demo of the Subjective Audio Prompt Adherence test.](https://golisten.ucd.ie/task/mushra-test/662644d5552c347eae00849e)

## Ethics Statement

Diff-A-Riff raises ethical considerations regarding musical agency  emphasizing the need for a commitment to ethical practices. This technology is designed to support and enhance human creativity, not replace it. The technology provides tools for generating and manipulating musical elements with human guidance, ensuring that artists remain in control of creative decisions. Additionally, the technology is programmed to adapt to pre-existing material. Therefore, it is important for users to verify the uniqueness of their compositions to prevent plagiarism, as Diff-A-Riff is trained on copyrighted data.
We recognize the impact of technology on society and are committed to continuously evaluating the ethical implications of Diff-A-Riff. We work with the music community to ensure our practices align with societal values and artistic norms, fostering trust and confidence in our technology. Furthermore, it is important to note that we cannot share our dataset as it is private and, while legally obtained, it contains material that prevents us from releasing the model in its current form. This issue raises concerns about scientific reproducibility. We are eager to discuss ethical and moral alternatives with anyone interested in this essential aspect of scientific progress.

## Extra References
[1] *Barry, Dan, et al. "Go Listen: An End-to-End Online Listening Test Platform." Journal of Open Research Software 9.1 (2021)*

[2] *Liutkus, Antoine, et al. "The 2016 signal separation evaluation campaign." Latent Variable Analysis and Signal Separation: 13th International Conference, LVA/ICA 2017, Grenoble, France, February 21-23, 2017, Proceedings 13. Springer International Publishing, 2017.*


<script>
// upon play of one audio element stop others
for (audio of document.getElementsByTagName("audio")) {
    audio.addEventListener("play", function(event) {
	for (other of document.getElementsByTagName("audio")) {
	    if ( this != other ) {
		other.pause();
    other.currentTime = 0;
	    }
	}
    });
}
</script>