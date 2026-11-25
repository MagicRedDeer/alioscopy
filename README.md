> Mirror of [iceanimations/alioscopy](https://github.com/iceanimations/alioscopy), published by the studio
> under EPL 2.0. Kept here for reference; the original repository is the canonical one.

# alioscopy

A stereoscopic camera rig for [Alioscopy](http://www.alioscopy.com) glasses-free displays, built in Maya
from the camera rules the displays are designed around.

Alioscopy screens are lenticular: a fixed number of views (5, 8 or 16) is interleaved behind a lens sheet,
and a render only lands correctly if the cameras are spaced, angled and shifted to match that geometry. Get
it wrong and the depth reads as noise. This tool builds the rig from those rules instead of by eye.

## How the rig works

The rig is a main camera plus a set of offset cameras, all driven by two numbers: a camera scale and the
Alioscopy parameter `p`. From the reference setup (image plane at 400 units, near plane at 6, 13.35 degree
field of view, 6.5 unit eye separation at scale 1), it derives:

```
fov        = 2 * atan( tan(origFov/2) / p )
focal      = 36 / ( 2 * tan(fov/2) )
imageZ     = origImageZ * cameraScale * p
eyeSep     = origEyeSeparation * cameraScale * p
```

Each sub-camera is then placed at

```
offset = eyeSep * (camIndex - nCams/2 + 0.5)
shift  = -offset * nearZ / imageZ
```

which is written to `translateX` and `horizontalFilmOffset`, so the views converge at the image plane
rather than at the camera. Both are **MEL expressions**, not baked values: animate the main camera and the
whole rig follows, including the field of view and film offset.

## The files

| File | Job |
|---|---|
| `cammaker.py` | Camera creation, the rig hierarchy, attribute locking and hiding |
| `expressions.py` | Builds the two expressions from the `.mel` templates and applies them |
| `mainExpression.mel` | Focal length, image plane distance and image size from `p` and scale |
| `stereoExpression.mel` | Per-camera offset and horizontal film shift |
| `utilities.py` | FOV to focal length conversion, clamping, attribute locking |

## Setup

The original README, unchanged:

1. Unpack the repository in your Maya scripts directory in a directory named `alioscopy`
2. Then in the Maya script editor:

```python
import alioscopy
alioscopy.makeCams()
```

Python 2 and pymel, as Maya was in 2015.

## Authorship

10 commits, all mine, from May 2015, plus the licence commit from the 2021 publication.

LICENSE
=======

Copyright 2021. Talha Ahmed. All rights reserved.

This work is licensed under the terms of Eclipse Public License - v 2.0.
For a copy see https://www.eclipse.org/org/documents/epl-2.0/EPL-2.0.txt
