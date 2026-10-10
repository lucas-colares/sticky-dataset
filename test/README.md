# Unlabeled sticky-trap test images

This release provides **600 native-resolution JPEG crops from 30 source photographs and 15 traps** for applying a frozen detector to images not found in the repository's training or validation partitions. It contains no ground-truth annotations. Use it for prediction and qualitative review, **not for calculating precision, recall, mAP, or confirmed organism counts** until independent, reviewed labels are available. Prediction TXT files must not be treated as reference labels.

| Source location code | Photographs | Traps | Crops |
| --- | ---: | ---: | ---: |
| A007 | 10 | 5 | 200 |
| F3T14 | 12 | 6 | 240 |
| I36T1 | 8 | 4 | 160 |
| Total | 30 | 15 | 600 |

## Selection and overlap audit

The input archives supplied by the dataset author on 10 October 2026 contained 44 JPEG entries, representing 34 unique files after SHA-256 deduplication. The I36T1 archive included ten exact duplicate entries under alternate filenames; all aliases are recorded in `source_audit.csv`.

Comparison with all 3,817 training and 954 validation image names at repository commit `7aa9521c9c546f24c738308987dfa42ede08425d` identified source photographs `A007A6-A.jpg` and `I36T1A1-A.jpg` as already represented. Their corresponding 20 and 17 image crops occur in the existing partitions. Both source photographs were excluded. The opposite sides, `A007A6-B.jpg` and `I36T1A1-B.jpg`, were also excluded to keep these traps out of the new set. Normalization removed crop suffixes and punctuation when comparing identifiers, including I36T1 filenames with and without hyphens.

An additional SIFT feature-matching screen compared the 34 source photographs with all 4,771 existing images, requiring at least eight geometrically consistent matches per candidate pair. Its results are recorded in `content_match_audit.json`. This is a secondary screen for renamed or recropped images, not a mathematical guarantee against every possible near-duplicate. Source identifiers remain the primary provenance evidence.

A007 and I36T1 location codes already occur in the existing data. Consequently, this set holds out the selected photographs and traps, **not all sampling locations**. It does not establish generalization to new regions or sampling periods. Crop files are correlated views, not 600 independent biological samples. The audit concerns the supplied repository; it does not reconstruct undocumented historical training datasets.

## Crop construction and provenance

Each retained source photograph contributes a fixed grid of four columns by five rows, with approximately 10% overlap between adjacent crops. No crop was selected or removed according to model predictions. A yellow-surface color mask on a thumbnail defines the trap bounding rectangle, with a margin equal to 2% of the shorter original-image dimension. The rectangle is axis-aligned; images are not warped, sharpened, or resized. EXIF orientation is applied before cropping. JPEGs are saved at quality 95 with chroma subsampling disabled.

`crop_manifest.csv` records the source filename, trap and location code, grid row/column, crop bounds, source dimensions, trap-region bounds, and SHA-256 fingerprints of the original file and released crop. Coordinates refer to the orientation-corrected source image, with the origin at its upper-left corner; `x1` and `y1` are exclusive crop bounds. The source photographs remain in the author's original archives.

Overlap helps preserve specimens near crop edges, but the same individual can appear in more than one crop. Map predictions back to source coordinates using `x0` and `y0`, inspect boundary cases, and reconcile repeated individuals before deriving trap-level counts. A crop may still truncate an organism; neighboring crops and the original photograph provide context. Do not sum raw crop counts as independent organism counts.

## Use

Apply the trained checkpoint to `test/images`, for example with `model.predict(source='test/images', ...)`. Keep prediction images, TXT files, CSV tables, and review notes outside `test/images` and outside any reference-label directory. This release deliberately has **no `test/labels` folder**, including no empty label files that could falsely imply verified background-only images.

The `test: test/images` path already present in the repository configuration now resolves to these images. It does not make this an annotated evaluation partition: do not run quantitative test validation against it until expert-reviewed annotations are added in a separately versioned release. Keep it separate from model fitting and hyperparameter selection if it is intended for future final evaluation.
