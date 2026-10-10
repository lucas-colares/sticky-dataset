# Sticky Dataset

**Amazonian arthropods on yellow sticky traps — images and YOLO object-detection annotations.**

This repository provides an annotated image dataset for training and evaluating computer vision models to locate arthropods and assign them to broad taxonomic groups. It accompanies the protocol's sticky-trap example and contains images from the field data used in [Colares, Peres & Dambros (2026), *Life history induces markedly divergent insect responses to habitat loss*](https://doi.org/10.1111/1365-2656.70117).

## A look at the dataset

<table>
  <tr>
    <td align="center"><img src="https://raw.githubusercontent.com/lucas-colares/sticky-dataset/86d450df1ff84c1c10c57fa0a32b57c7a4be9e4f/train/images/A000A2-A_1.jpg" width="260" alt="Brachycera specimen on a yellow sticky trap"><br><strong>Brachycera</strong></td>
    <td align="center"><img src="https://raw.githubusercontent.com/lucas-colares/sticky-dataset/86d450df1ff84c1c10c57fa0a32b57c7a4be9e4f/train/images/A000A2-A_13.jpg" width="260" alt="Odonata specimen on a yellow sticky trap"><br><strong>Odonata</strong></td>
    <td align="center"><img src="https://raw.githubusercontent.com/lucas-colares/sticky-dataset/86d450df1ff84c1c10c57fa0a32b57c7a4be9e4f/train/images/A000A2-A_16.jpg" width="260" alt="Nematocera specimen on a yellow sticky trap"><br><strong>Nematocera</strong></td>
  </tr>
</table>

*Examples from the training set. Captions follow the supplied annotations. Images are displayed at the same width, not at a common biological scale.*

## Ecological background

The source study investigated how forest cover relates to the diversity, composition, and body size of insects with terrestrial and aquatic life stages in the Balbina reservoir, Central Brazilian Amazon. Field sampling in October–November 2021 used 236 double-sided yellow sticky traps across forest islands, open water, and adjacent continuous forest. Traps were exposed for 24 hours and photographed on both sides. See the [paper](https://doi.org/10.1111/1365-2656.70117) for the sampling design and ecological analyses.

This repository is an image-and-annotation release from those field data. Its class mapping and split should be used as documented below; it is not the paper's archived fivefold training release.

## Dataset at a glance

| Property | Contents |
| --- | --- |
| Task | Object detection with taxonomic class assignment |
| Annotated training/validation images | 4,771 JPEG files |
| Training split | 3,817 images and 3,817 matching label files |
| Validation split | 954 images and 954 matching label files |
| Classes | 14 arthropod categories |
| Annotation format | YOLO bounding boxes in plain-text files |
| Configuration | [`data.yaml`](data.yaml) |
| Unlabeled test images | 600 crops from 30 photographs and 15 held-out traps; see [`test/README.md`](test/README.md) |

Counts refer to repository revision [`86d450d`](https://github.com/lucas-colares/sticky-dataset/tree/86d450df1ff84c1c10c57fa0a32b57c7a4be9e4f). Image counts are file counts, not counts of independent traps or sampling locations.

## Repository structure

```text
sticky-dataset/
├── data.yaml
├── train/
│   ├── images/       # Training JPEGs
│   ├── labels/       # Matching YOLO TXT annotations
│   └── labels.cache
├── val/
    ├── images/       # Validation JPEGs
    ├── labels/       # Matching YOLO TXT annotations
    └── labels.cache
└── test/
    ├── images/       # 600 unlabeled crops
    ├── crop_manifest.csv
    ├── source_audit.csv
    ├── content_match_audit.json
    └── README.md
```

Each annotated training/validation image and its annotation share the same filename stem, for example `train/images/A000A2-A_1.jpg` and `train/labels/A000A2-A_1.txt`. The `.cache` files are auxiliary label caches; the TXT files contain the annotations.

## Classes

Class IDs are zero-based and follow the exact order in [`data.yaml`](data.yaml).

| ID | Label | ID | Label |
| --- | --- | --- | --- |
| 0 | Araneae | 7 | Lepidoptera |
| 1 | Brachycera | 8 | Nematocera |
| 2 | Coleoptera | 9 | Odonata |
| 3 | Ephemeroptera | 10 | Orthoptera |
| 4 | Hemiptera | 11 | Plecoptera |
| 5 | Hymenoptera | 12 | Psocoptera |
| 6 | Isoptera | 13 | Trichoptera |

These labels represent broad taxonomic categories, not species. They are retained as exported so that class IDs remain compatible with the annotations. Araneae includes spiders, so the dataset encompasses arthropods beyond insects.

## Annotation format

Each non-empty line in a label file describes one annotated object:

```text
class_id x_center y_center width height
```

Coordinates and dimensions are normalized by image width and height. For example, `train/labels/A000A2-A_1.txt` contains:

```text
1 0.5 0.5 0.607843137254902 0.626865671641791
```

This identifies a Brachycera bounding box centered in the image.

**These are bounding-box annotations.** Workflows in the protocol that require instance-segmentation polygons need a separately prepared segmentation dataset.

## Getting started

Download the repository using **Code → Download ZIP**, or clone it:

```bash
git clone https://github.com/lucas-colares/sticky-dataset.git
cd sticky-dataset
```

Use `train/images` and `train/labels` for training, and `val/images` and `val/labels` for validation. Configure your training software to resolve these paths from the downloaded dataset root, and preserve the class order above.

The supplied `data.yaml` declares `test: test/images`. The new [`test/`](test/README.md) release contains 600 unlabeled crops for inference and qualitative review. It has no reference labels and cannot support quantitative test metrics until reviewed annotations are added. Its photographs and trap IDs were screened against the existing splits; some location codes are shared, so this is not a completely site-independent evaluation.

**Known annotation issue:** [`train/labels/F3T01A5-B_12.txt`](train/labels/F3T01A5-B_12.txt) contains a row with `NA` instead of an integer class ID. Review and correct the taxonomic assignment, or exclude the affected image and label from your local training copy. Do not silently convert the unresolved class to another category.

For evaluation on new traps or sites, keep related crops and photographs from the same sampling unit in one split. This repository does not include a split manifest establishing independence at the trap or site level. Validation results therefore require that check before being interpreted as performance on independent field samples.

## Related research resources

The paper's [data availability statement](https://doi.org/10.1111/1365-2656.70117) links to:

| Resource | Archive |
| --- | --- |
| Original sticky-trap photographs | [Figshare](https://doi.org/10.6084/m9.figshare.23823591) |
| Fivefold image dataset used in the paper | [Figshare](https://doi.org/10.6084/m9.figshare.28688198) |
| Five trained models | [Figshare](https://doi.org/10.6084/m9.figshare.28820993) |
| Processed research data | [Zenodo](https://doi.org/10.5281/zenodo.15238078) |

## Citation

When using these data, cite the associated study and identify the repository revision used:

> Colares, L. F., Peres, C. A., & Dambros, C. S. (2026). Life history induces markedly divergent insect responses to habitat loss. *Journal of Animal Ecology*, **95**, 54–64. https://doi.org/10.1111/1365-2656.70117

## Reuse and contact

This repository currently has no license file. Consult the relevant archive's license for archived materials, and contact the maintainer to clarify reuse terms for this repository.

For questions or annotation corrections, [open an issue](https://github.com/lucas-colares/sticky-dataset/issues).
