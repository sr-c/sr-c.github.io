---
title: "DNA Features Viewer使用"
date: 2020-07-21T16:21:29+08:00
tags: [python, conda, biopython]
categories: [Visualization]
---

最近发表在*Bioinformatics*的[DNA Features Viewer](https://doi.org/10.1093/bioinformatics/btaa213)是一个可视化DNA序列特征的Python工具。此为尝鲜。

<!-- more -->

# 安装

官方文档目前只提供pip安装方式。issue中有反映要求conda安装方式，[评论](https://github.com/Edinburgh-Genome-Foundry/DnaFeaturesViewer/issues/11#issuecomment-656485115)中有个人提供的linux conda版本，但我没有安装成功。

```bash
conda create -n biopython biopython -y ## create a biopython env
pip install dna_features_viewer ## install via pip
```

# 使用

## 自定义序列特征绘图

```python
"""Simple example where a few features are defined "by hand" and are displayed
and exported as PNG, first with a linear view, then with a circular
view.
"""

from dna_features_viewer import (
    GraphicFeature,
    GraphicRecord,
    CircularGraphicRecord,
)

features = [
    GraphicFeature(
        start=5, end=20, strand=+1, color="#ffd700", label="Small feature"
    ),
    GraphicFeature(
        start=20,
        end=500,
        strand=+1,
        color="#ffcccc",
        label="Gene 1 with a very long name",
    ),
    GraphicFeature(
        start=400, end=700, strand=-1, color="#cffccc", label="Gene 2"
    ),
    GraphicFeature(
        start=600, end=900, strand=+1, color="#ccccff", label="Gene 3"
    ),
]


# PLOT AND EXPORT A LINEAR VIEW OF THE CONSTRUCT
record = GraphicRecord(sequence_length=1000, features=features)
ax, _ = record.plot(figure_width=5)
ax.figure.savefig("graphic_record_defined_by_hand.png")


# PLOT AND EXPORT A CIRCULAR VIEW OF THE CONSTRUCT
circular_rec = CircularGraphicRecord(sequence_length=1000, features=features)
ax2, _ = circular_rec.plot(figure_width=4)
ax2.figure.tight_layout()
ax2.figure.savefig(
    "graphic_record_defined_by_hand_circular.png", bbox_inches="tight"
)
```

## 读取genbank数据绘图

`dna_features_viewer`中含有`translate_record`函数可读取`GenBank`格式的数据。但是，将读取到的这些特征解释为绘图内容就可以使用其提供的`BiopythonTranslator`或自定义`MyCustomTranslator`.

### 自定义特征解释器

```python
from dna_features_viewer import BiopythonTranslator

class MyCustomTranslator(BiopythonTranslator):
    """Custom translator implementing the following theme:

    - Color terminators in green, CDS in blue, all other features in gold.
    - Do not display features that are restriction sites unless they are BamHI
    - Do not display labels for restriction sites.
    - For CDS labels just write "CDS here" instead of the name of the gene.

    """

    def compute_feature_color(self, feature):
        if feature.type == "CDS":
            return "blue"
        elif feature.type == "terminator":
            return "green"
        else:
            return "gold"

    def compute_feature_label(self, feature):
        if feature.type == 'restriction_site':
            return None
        elif feature.type == "CDS":
            return "CDS here"
        else:
            return BiopythonTranslator.compute_feature_label(self, feature)

    def compute_filtered_features(self, features):
        """Do not display promoters. Just because."""
        return [
            feature for feature in features
            if (feature.type != "restriction_site")
            or ("BamHI" in str(feature.qualifiers.get("label", '')))
        ]


graphic_record = MyCustomTranslator().translate_record("example_sequence.gb")
ax, _ = graphic_record.plot(figure_width=10)
ax.figure.tight_layout()
ax.figure.savefig("custom_biopython_translator.png")
```

![custom_biopython_translator.png](https://edinburgh-genome-foundry.github.io/DnaFeaturesViewer/_images/custom_biopython_translator.png)

### Biopython默认特征解释器

```python
from dna_features_viewer import BiopythonTranslator

graphic_record = BiopythonTranslator().translate_record("example_sequence.gb")
ax, _ = graphic_record.plot(figure_width=10, strand_in_label_threshold=7)
ax.figure.tight_layout()
ax.figure.savefig("from_genbank.png")
```

![from_genbank](https://edinburgh-genome-foundry.github.io/DnaFeaturesViewer/_images/from_genbank.png)

可以绘制圈图，但是所有的注释信息都只能标注在图像上方，而不是像一般的Snapgene之类的工具紧密地随特征标注。

### 显示GC比例的线性图

```python
"""In this example we plot a record's annotations on top of the curve of the
local GC content in the record's sequence.
"""
import matplotlib.pyplot as plt
from dna_features_viewer import BiopythonTranslator
from Bio import SeqIO
import numpy as np


def plot_local_gc_content(record, window_size, ax):
    """Plot windowed GC content on a designated Matplotlib ax."""
    def gc_content(s):
        return 100.0 * len([c for c in s if c in "GC"]) / len(s)

    yy = [
        gc_content(record.seq[i : i + window_size])
        for i in range(len(record.seq) - window_size)
    ]
    xx = np.arange(len(record.seq) - window_size) + 25
    ax.fill_between(xx, yy, alpha=0.3)
    ax.set_ylim(bottom=0)
    ax.set_ylabel("GC(%)")


record = SeqIO.read("example_sequence.gb", "genbank")
translator = BiopythonTranslator()
graphic_record = translator.translate_record(record)

fig, (ax1, ax2) = plt.subplots(
    2, 1, figsize=(10, 3), sharex=True, gridspec_kw={"height_ratios": [4, 1]}
)
graphic_record.plot(ax=ax1, with_ruler=False, strand_in_label_threshold=4)
plot_local_gc_content(record, window_size=50, ax=ax2)

fig.tight_layout()  # Resize the figure to the right height
fig.savefig("with_gc_plot.png")
```

![with_gc_plot](https://edinburgh-genome-foundry.github.io/DnaFeaturesViewer/_images/with_gc_plot.png)

### 氨基酸编码序列

```python
from dna_features_viewer import GraphicFeature, GraphicRecord

record = GraphicRecord(sequence="ATGCATGCATGCATGCATGCATGCATGC", features=[
    GraphicFeature(start=5, end=10, strand=+1, color='#ffcccc'),
    GraphicFeature(start=8, end=15, strand=+1, color='#ccccff')
])

ax, _ = record.plot(figure_width=6)
record.plot_sequence(ax, guides_intensity=0.2)
fontdict = {'weight': 'bold'}
record.plot_translation(ax, (8, 23), fontdict=fontdict, guides_intensity=0.8)
ax.figure.savefig('sequence_and_translation.png', bbox_inches='tight')
```

![sequence_and_translation](https://edinburgh-genome-foundry.github.io/DnaFeaturesViewer/_images/sequence_and_translation.png)

### GIF

```python
"""An example with GIF generation at the end. How cool is that!

This example requires the Moviepy library  installed (pip install moviepy).

"""
from Bio import Entrez, SeqIO
import moviepy.editor as mpe
from moviepy.video.io.bindings import mplfig_to_npimage
import matplotlib.pyplot as plt
from dna_features_viewer import BiopythonTranslator, CircularGraphicRecord

# DOWNLOAD THE PLASMID's RECORD FROM NCBI

handle = Entrez.efetch(
    db="nucleotide", id=1473096477, rettype="gb", retmode="text"
)
record = SeqIO.read(handle, "genbank")

# CREATE THE GRAPHIC RECORD WITH DNA_FEATURES_VIEWER

color_map = {
    "rep_origin": "yellow",
    "CDS": "orange",
    "regulatory": "red",
    "misc_recomb": "darkblue",
    "misc_feature": "lightblue",
}
translator = BiopythonTranslator(
    features_filters=(lambda f: f.type not in ["gene", "source"],),
    features_properties=lambda f: {"color": color_map.get(f.type, "white")},
)
translator.max_line_length = 15
graphic_record = translator.translate_record(
    record, record_class=CircularGraphicRecord
)
graphic_record.labels_spacing = 15

# ANIMATE INTO A GIF WITH MOVIEPY

duration = 5


def make_frame(t):
    top_nucleotide_index = t * graphic_record.sequence_length / duration
    graphic_record.top_position = top_nucleotide_index
    ax, _ = graphic_record.plot(figure_width=8, figure_height=11)
    ax.set_ylim(top=2)
    np_image = mplfig_to_npimage(ax.figure)
    plt.close(ax.figure)
    return np_image


clip = mpe.VideoClip(make_frame, duration=duration)
small_clip = clip.crop(x1=60, x2=-60, y1=100, y2=-100).resize(0.5)
small_clip.write_gif("example_with_gif.gif", fps=15)
```

![example_with_gif](https://edinburgh-genome-foundry.github.io/DnaFeaturesViewer/_images/example_with_gif.gif)

# 其他

[issues #28](https://github.com/Edinburgh-Genome-Foundry/DnaFeaturesViewer/issues/28)中表示希望圈图的注释信息只能位于上方，不能够自定义位置。作者表示可以通过调整[CircularGraphicRecord.py](https://github.com/Edinburgh-Genome-Foundry/DnaFeaturesViewer/blob/master/dna_features_viewer/CircularGraphicRecord/CircularGraphicRecord.py)实现在上下两个区域标注信息，完整地自定义注释位置目前实现起来还比较困难。

# 参考来源

https://edinburgh-genome-foundry.github.io/DnaFeaturesViewer/examples.html

https://edinburgh-genome-foundry.github.io/DnaFeaturesViewer/