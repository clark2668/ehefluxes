# ehefluxes
A small package for providing ehe fluxes

ehefluxes is a lightweight python3-based package for calculating EHE/UHE ("extremely high energy"/"ultra high energy" ) diffuse neutrino fluxes.
"Extremely/ultra" high generally means >10 PeV.
Because the fluxes are diffuse, only energy dependence is supported
(no zenith dependence).


## Installation

Installation of ehefluxes is easy with pip:

    git clone https://github.com/clark2668/ehefluxes.git
    cd ehefluxes
    pip install . 

If you plan on actively contributing or developing, then installing with
symlink is useful:

    pip install -e .

A user may also find it helpul to install into user space, or into a 
specific target directory:

    pip install --user .
    pip install . --target=/path/to/my/install

If doing the latter, remember to update your PYTHONPATH.

### Prerequisites

`ehefluxes` is dependency light, requiring numpy, scipy, and matplotlib.
Python >=3.7 (for manging fluxes through importlib).


## Usage

Usage is quite straightforward:
```python
from ehefluxes import fluxes
import numpy
model = fluxes.EHEFlux('ahlers_gzk')
energies = numpy.logspace(5, 12, 50)
flux = model(energies, "sum")
```

Energies are in GeV, fluxes in 1/GeV/cm2/s/sr.
See below for additional discussion on units.

The second argument to the function call can request the 
sum over all neutrino species (`sum`), a single flavor (e.g. `nue`),
or can specify a species per energy bin.
See function docs for mor information.


## Fluxes & Units

Manging units in neutrino flux measurements is always challenging.
In this package, the base unit of energy is GeV.
The code stores the splines, and returns fluxes, in units of 1/GeV/cm2/s/sr.
This means the user only ever has to multiply by copies of energy to get 
particle flux or e-squared (energy) normalizations.


### Adding your Own Fluxes

Adding new fluxes can be done by adding the relevant csv file to the 
`ehefluxes/data` folder.
The file should contain seven columns.
The first column should specify the neutrino energy in GeV.
The next six columns should give the per-species neutrino fluxes
in units of 1/GeV/cm2/s/sr (see units discussion above).

The first line should specify the column headers, all lower case. E.g.

    enu, nue, nuebar, numu, numubar, nutau, nutaubar

The order of the flux columns does not matter.
(E.g. nue, nuebar, etc. can be listed in any order.)
Comments may be added to the file at any point by prepending the
line with a pound sign, e.g.

    enu, nue, nuebar, numu, numubar, nutau, nutaubar
    # we can comment here that this flux is an ahlers fluxs
    # in units of GeV, and 1/GeV/cm2/s/sr
    1e9, 3e-24, ....

Having separate columns for each species supports fluxes which are 
not equipartition between flavors and particles/antiparticles.
If the flux of interest *is* equipartition (equal distribution 
between flavors and nu/nubar) the user must still provide six columns.
In such a case, the user would divide the total flux by 1/6th,
and use those identical values in each column.

As a warning: if you are exporting your fluxes from Excel, when you call
File -> Save As, scroll down to "Comma Separated Value (.csv)".
You should *skip* the first option, which reads "CSV UTF-8 (Comma delimited) (.csv)".
