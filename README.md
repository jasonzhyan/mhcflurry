[![Build Status](https://travis-ci.org/openvax/mhcflurry.svg?branch=master)](https://travis-ci.org/openvax/mhcflurry)

# mhcflurry
[MHC I](https://en.wikipedia.org/wiki/MHC_class_I) ligand
prediction package with competitive accuracy and a fast and 
[documented](http://openvax.github.io/mhcflurry/) implementation.

MHCflurry supports class I peptide/MHC binding affinity prediction. By default
it supports 112 MHC alleles using ensembles of allele-specific models.
Pan-allele prediction (supporting virtually all MHC alleles of known sequence)
is available for testing. MHCflurry runs on Python 2.7 and 3.4+ using the
[keras](https://keras.io) neural network library.
It exposes [command-line](http://openvax.github.io/mhcflurry/commandline_tutorial.html)
and [Python library](http://openvax.github.io/mhcflurry/python_tutorial.html)
interfaces.

If you find MHCflurry useful in your research please cite:

> T. J. O’Donnell, A. Rubinsteyn, M. Bonsack, A. B. Riemer, U. Laserson, and J. Hammerbacher, "MHCflurry: Open-Source Class I MHC Binding Affinity Prediction," *Cell Systems*, 2018. Available at: https://www.cell.com/cell-systems/fulltext/S2405-4712(18)30232-1.

## Installation (pip)

Install the package:

```
$ pip install mhcflurry
```

Then download our datasets and trained models:

```
$ mhcflurry-downloads fetch
```

You can now generate predictions:

```
$ mhcflurry-predict \
       --alleles HLA-A0201 HLA-A0301 \
       --peptides SIINFEKL SIINFEKD SIINFEKQ \
       --out /tmp/predictions.csv
       
Wrote: /tmp/predictions.csv
```

See the [documentation](http://openvax.github.io/mhcflurry/) for more details.

## Model variants

### Allele-specific models and mass spec

The default MHCflurry models are trained on affinity measurements. Mass spec
datasets are incorporated only in the model selection step. We also release
experimental predictors whose training data directly includes mass spec.
To download these predictors, run:

```
$ mhcflurry-downloads fetch models_class1_trained_with_mass_spec
```

and then to make them used by default:

```
$ export MHCFLURRY_DEFAULT_CLASS1_MODELS="$(mhcflurry-downloads path models_class1_trained_with_mass_spec)/models"
```

We also release predictors that do not use mass spec datasets at all. To use
these predictors, run:

```
$ mhcflurry-downloads fetch models_class1_selected_no_mass_spec
export MHCFLURRY_DEFAULT_CLASS1_MODELS="$(mhcflurry-downloads path models_class1_selected_no_mass_spec)/models"
```

### Experimental pan-allele models

We are testing new models that support prediction for any MHC I allele of known
sequence (as opposed to the 112 alleles supported by the current version).
These models are trained on both affinity measurements and mass spec.

To try the pan-allele models, first download them:

```
$ mhcflurry-downloads fetch models_class1_pan
```

then set this environment variable to use them by default:

```
$ export MHCFLURRY_DEFAULT_CLASS1_MODELS="$(mhcflurry-downloads path models_class1_pan)/models.with_mass_spec"
```

You can now generate predictions for about 14,000 MHC I alleles. For example:

```
$ mhcflurry-predict --alleles HLA-A*02:04 --peptides SIINFEKL
```

If you use these models please let us know how it goes and if you encounter
anything surprising.

#### Optimizations for pan-allele models

The pan-allele models are somewhat slow. You can set
the environment variable `MHCFLURRY_OPTIMIZATION_LEVEL=1` to turn on one
attempt to optimize these models for faster prediction speed. This will
"stitch" the pan-allele models in the ensemble (currently 32) into one large
tensorflow (or theano) graph. In our experiments it gives about a 30% speed
improvement.