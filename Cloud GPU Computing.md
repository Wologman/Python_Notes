## Providers short-listed
- [Lambda](https://lambdalabs.com/service/gpu-cloud)
- [Paperspace](https://www.paperspace.com/)
- [Vast.ai](https://vast.ai/)
- [Oracle Cloud Infrastructure](https://www.oracle.com/au/cloud/)

## Requirements 
- Works from New Zealand (Rules out GCP?)
- Reasonably simple and intuitive setup
- Can install Conda from terminal, or it comes pre-installed with every instance.
- Lots of memory (GPU & RAM)
- Multi-core CPU
- Linux OS
- A virtual desktop or at least pre-installed virtual instances of VS Code, & Jupyter Notebooks.
- Good integration with a cloud data storage provider, or includes > 2TB of permanent storage its self at reasonable cost
- Good value for money. Preferably pay as you go, with minimal or zero ongoing cost for subscription, for when I'm doing other things.
- Latest/fastest GPUs & TPUs would be nice, but not essential. Or at least a reasonable speed/cost tradeoff so the cost per experiment is competitive

## Comparison Table

| Provider | Memory | Costs | GPUs | V Dtop | Storage | Comment |
| ----------- | ----------- | ----------- | ----------- |----------- | ----------- | ----------- | 
|  |<sub>CPU/GPU</sub>| <sub>GPU/Trans/Store/Sbscr</sub>|<sub>Models</sub>|<sub>Y/N</sub>|<sub>Y/N</sub>|<sub>More detail </sub>|
|Lambda | Many Opts | $0.8 with A6000 + 0 + 0 + 0  | H100, A6000, A100, H100, V100  | No - Jupyter only | 10Tb/instance with A6000 or V100, Free | Docker Containers (Includes pre-configured options) or install conda from terminal |
|Paperspace | Many Opts. |$1.89/hr A6000 + 0 + 0.29/GB + US$8 or $39/month | M4000-A6000 |No - Custom web IDE only | 0.29/GB | Docker Containers |
|vast.ai | Many Opts. | $0.88 For 2x RTX A6000, 48GB + ? + ? + 0 |Many Options, cheap if using Consumer GPUs, less memory | No - Jupyter NBs or SSH only | Per instance, and not sure how much it costs | Docker containers |
|Oracle |Many Opts|$1.98 for 2xP100, $3.10 for A10|A,V,P series, 10 & 100   |  Y | Y | Bells and whistles, but old & pricey GPUs

**vast.ai** is probably the most economical, but too much faff for me
**paperspace** might be OK if I can solve the storage question for datasets I'm not using immediately.
**Lambda** meets most of my needs, except for having a virtual desktop or pre-installed Conda.
**Oracle** Offers all sorts of things, but probably aimed more at enterprise solutions.  GPU's are old, & the most expensive option.

## Cloud Data Storage costs
Since this idea works best with data storage also in the cloud, any transfer costs/and storage costs from this part needs considering if it isn't included from the GPU provider.

