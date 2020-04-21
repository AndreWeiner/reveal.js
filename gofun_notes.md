# Notes for GOFUN presentation slides

## Slide 1
- message: integration of ML in OF workflow has becomes really easy
- boost performance of simulations using data


## Slide 2

### Slide 2.1
- convection-dominated mass transfer crucial step in gas-liquid reactors
- reactors of various sizes, numerical tools would be great
- explain Taylor bubble

### Slide 2.2
- explain zoom
- high Pe or Sc number problem

### Slide 2.3
- explain name
- deformation + boundary layer: 1200^3 cells

### Slide 2.4
- idea not new, but implementation different

## Slide 3
- first show overview of single steps
- walk through most of the steps


## Slide 4

### Slide 4.1
- VOF simulation, extract and parametrize interface, save as STL

### Slide 4.2
- STL used in OpenFOAM to create single-phase mesh
- single phase flow dynamics; difficulty to model velocity boundary condition as bubble surface
- species transport


## Slide 5

### Slide 5.1
- conducted in Basilisk, not much detail, keywords outline the most important algorithms
- one-field approach: single momentum equation, mixed quantities, reconstruction of interfacial data contains noise

### Slide 5.2
- investigation of characteristic bubble shapes


## Slide 6

### Slide 6.1
- in contrast to solid particle, velocity at gas-liquid interface is not zero
- from mass conservation and in absence of phase change: continuity of velocity normal to interface
- steady shape: normal contribution zero
- typical assumption: continuity of full velocity vector
- difficulty to model tangential contribution in single-phase flow

### Slide 6.2
- actually we know approximately how velocity looks like, extraction from two-phase simulations noisy
- use ML model to create an approximate function
- simple case: function of polar angle
- complex case: function of arc length
- ML model created in PyTorch
- why PyTorch: backend in C and C++, frontend in Python and C++, model exchange between Python and C++ frontend via torch script

### Slide 6.3
- dimpled ellipsoidal bubble as example, don't expect to understand it
- no details due to time constraint
- learn more

## Slide 7

### Slide 7.1
- How do we use model in single phase simulation?
- explain domain: shape embedded in sphere, inlet velocity equal to rise velocity in two-phase simulation, model as boundary condition for bubble patch

### Slide 7.2
- create derived class based on fixedValueFvPatchVectorField
- two private members related to model: name of torch script file + model object

### Slide 7.3
- in the default constructor: read name and load model

### Slide 7.4
- in update coefficients: create feature tensor with polar angle for all face centers of patch, run forward pass (model evaluation), accessor object which makes it easy to access the model results
- tensor contiguous in memory, here trivial, in general not easy to access elements directly
- apply output as boundary condition

### Slide 7.5
- what you would do as user

### Slide 7.6
- mesh creation for completeness
- wedge mesh creation with snappy is a little bit tedious, many steps, final result OK

### Slide 7.7
- ready to compute velocity field with standard solver like simpleFoam
- benefit of ML boundary condition
- slip: no shear stress introduced by gas phase
- ML BC much close to two-phase results, free-slip qualitatively wrong


## Slide 8

### Slide 8.1
- finally mass transfer
- simple convection-diffusion equation, complex reaction possible

### Slide 8.2
- mass transfer in terms of global Sh
- even for simple cases difference close to 10 percent between standard and more realistic data-driven BC


## Slide 9

### Slide 9.1
- decoupling, efficiency, ML for mapping information

### Slide 9.2
- not as limited as it seems
- dynamic interfaces with very little changes in the workflow

## Slide 10
- thank you and get in touch