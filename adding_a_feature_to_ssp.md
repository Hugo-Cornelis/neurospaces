G3 Doc: genesis-add-feature-ssp

# Introduction #

This document describes how to integrate the PulseGen object with ssp.

# Reading Parameters #

To integrate your object with ssp you must first make ssp aware of the objects parameters. We know that PulseGen takes parameters for Level1, Width1, Delay1, Level2, Width2, Delay2, BaseLevel, and TriggerMode. So we must allow for ssp to read in a value for each as an argument to the program. First we declare variable for the optional arguments near the top of the _bin/ssp_ file:

```
my $option_pulsegen_level1;
my $option_pulsegen_width1;
my $option_pulsegen_delay1;
my $option_pulsegen_level2;
my $option_pulsegen_width2;
my $option_pulsegen_delay2;
my $option_pulsegen_baselvel;
my $option_pulsegen_triggermode;
```

Then in the perl subroutine _read\_cmd\_line_ we add the options to be read in to the call to _GetOptions_ like this:

```
	     "pulsegen-level1=s" => \$option_pulsegen_level1,
	     "pulsegen-width1=s" => \$option_pulsegen_width1,
	     "pulsegen-delay1=s" => \$option_pulsegen_delay1,
	     "pulsegen-level2=s" => \$option_pulsegen_level2,
	     "pulsegen-width2=s" => \$option_pulsegen_width2,
	     "pulsegen-delay2=s" => \$option_pulsegen_delay2,
```

This simply allows us to read in the values from the command line and get them into memory. Next we must pass them on to PulseGen.



# Passing data to PulseGen #

Now what the program can read in values we must check for each parameter and pass it on to the PulseGen object. We do this by using a simple _if_ statement which checks if the variable has been defined and passes on the value to scheduler. A block similar to this one must be made for each parameter that can be accepted by PulseGen, here is an example for setting width1 in PulseGen:

```
	if (defined $option_pulsegen_width1)
	{
	    my $pulsegen_inputclassname = "pulsegen";

	    my $inputclass
		= {
		   module_name => "Heccer",
		   options => {
			       command => $option_pulsegen_width1,
			       name => "pulsegen set to $option_pulsegen_width1",
			      },
		   package => "Heccer::Pulsegen",
		  };

	    my $inputs
		= [
		   {
		    component_name => "$model_root/segments/soma",
		    field => "WIDTH1",
		    inputclass => $pulsegen_inputclassname,
		   },
		  ];

	    $scheduler->{inputclasses}->{$pulsegen_inputclassname} = $inputclass;

	    if (!defined $scheduler->{inputs})
	    {
		$scheduler->{inputs} = [];
	    }

	    push @{$scheduler->{inputs}}, @$inputs;
	}
```


---


# Creating an SSP specification #

For testing purposes we will create an SSP specification that checks the functionality of the PulseGen object. Our test will load a model, create a PulseGen solver object, then clamp the PulseGen output to the models membrane potential in its soma. This is biologically not correct, however it provides a good base case for a test.

Here we create a file _pulsegen\_freerun.yml_ as a large YAML hash. We indicate that the simulation is going to run for 200 steps, similar to the Heccer test, and GENESIS 2 script that was previously created:

```
--- !!perl/hash:SSP
apply:
  results:
    - arguments:
        - commands:
            - perl -e 'my $result = [ `cat /tmp/output`, ]'
      method: shell
  simulation:
    - arguments:
        - 200
      method: steps
```


Here we declare our PulseGen object as an input class, assigning it all of the parameters previously used in the Heccer test and GENESIS script.

```
inputclasses:
  pulsegen:
    module_name: Heccer
    options:
      level1: 50.0
      width1: 3.0
      delay1: 5.0
      level2: -20.0
      width2: 5.0
      delay2: 8.0
      baselevel: 10.0
      triggermode: 0
    package: Heccer::PulseGen
```


Here we "clamp" the PulseGen to the models soma.

```
inputs:
  - component_name: /Purkinje/segments/soma
    field: Vm
    inputclass: pulsegen
models:
  - modelname: /Purkinje
    solverclass: heccer
name: purkinje cell pulsegen
```


Here we declare an output object _double\_2\_ascii_ which will write the output from the membrane potential we previously clamped (Vm) to the file _/tmp/output_.

```
outputclasses:
  double_2_ascii:
    module_name: Heccer
    options:
      filename: /tmp/output
    package: Heccer::Output
outputs:
  - component_name: /Purkinje/segments/soma
    field: Vm
    outputclass: double_2_ascii

```


Here the model-container is invoked in order to load the desired model. Parameters for the model container can also be set in this block:

```
services:
  model_container:
    initializers:
      - arguments:
          - filename: cells/purkinje/edsjb1994.ndf
            no-use-library: 1
          -
            - ssp configuration for edsjb1994 using pulsegen
            - -R
            - -A
        method: load
    module_name: Neurospaces
```


Finally here we invoke heccer, we set the output granularity to 1 and the step size to 0.5 seconds, again to keep in line with the previous examples:

```
solverclasses:
  heccer:
    constructor_settings:
      configuration:
        reporting:
          granularity: 1
          tested_things: 6225920
      dStep: 0.5
      options:
        iOptions: 4
    module_name: Heccer
    service_name: model_container
```


Now after saving our specification we can start the simulation with the command:

```
ssp pulsegen_freerun.yml
```

Our expected output is then written to the file _/tmp/output_ as we declared in our specification.