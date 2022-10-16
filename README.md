# About

This repository implements
[Model Free Monte Carlo](http://www.jmlr.org/proceedings/papers/v9/fonteneau10a/fonteneau10a.pdf)
(MFMC),
which is a method for generating off-policy Monte Carlo trajectories from a
database of state transitions.
We use this code base for making fast interactive visualizations for
Markov Decision Processes (MDPs), but you could potentially also use it for
MDP optimization as well.
We include several extensions to MFMC that exploit **i**ndependencies among
state variables. Hence, we call
our algorithm "MFMCi." Our research paper is available [here](https://seanbmcgregor.com/papers/WildfireAssistant.pdf).

`McGregor, S., Houtman, R. M., Metoyer, R., & Dietterich, T. G. (2019). Connecting Conservation Research and Implementation: Building a Wildfire Assistant. In Artificial Intelligence and Conservation. Cambridge Press.`

**WARNING: This code has never been used outside our research group.**
This means it will likely be more difficult for you to apply it to
your problem, but it also means we are _very_ eager to see people use it.
Contact the maintainer if you need help.

# Hello World

Out of the box you can use MFMCi on three problem domains:

* Mountain Car
* HIV Treatment
* Wildfire Suppression (paper under review): A large state space MDP defined by a computationally expensive simulator.

To invoke each of these domains, you can start the web server:

`python serve.py MountainCar`, `python serve.py HIV`, or `python serve.py wildfire`

now visit [mdpvis.github.io](http://mdpvis.github.io/) and point it at the
web server you have running locally.
You can explore synthesized rollouts
MDPvis includes functionality for downloading
trajectory sets generated by your queries.

# How to use MFMCi on your problem

MFMCi requires you to (1) provide a CSV with a database of state transitions,
(2) annotate the columns of the CSV so MFMCi will use the proper columns
in synthesizing state transitions, and (3) specify parameterized
policy classes such that you can query MFMCi for trajectories from policies under
different parameterizations.
We integrated a web server with this repository so
[MDPvis](https://github.com/MDPvis/MDPvis.github.io) will be integrated
by default. We recommend using MDPvis for exploring the performance of
MFMCi on your problem, but you can also hook your optimization code
directly into MFMCi by including the MFMCi python script.

The directory structure is:

    databases/DOMAIN/database.csv # The database (optional if compressed)
    databases/DOMAIN/database.csv.bz2 # The compressed database (optional if uncompressed)
    databases/DOMAIN/annotate.py # Labels for how we will use each column int he database
    databases/DOMAIN/policies.py # The policy classes that you will query

## Generating the Database

Databases should use comma separated values with a header row. Each column will be
referenced by the name found in the header row,
with the surrounding whitespace in the header stripped.

MFMCi assumes that every transition in the database is matched with transitions for each
action in the action space. This avoids introducing additional bias in the transition.
Details of this effect is included in an (under review) research paper.

For an example of the database format, you can view the files in the databases folder.
Databases should either be named `database.csv`, or `database.csv.bz2`. When there is no
`database.csv` file, mfmci will attempt to decompress a bz2 compressed file.

Each row of the database should include all the variables necessary for evaluating
state transitions, including the starting state variables, result state variables,
and any variables that you may need to evaluate the policy or generate a visualization.

Three required columns are `action`, `onPolicy`, and `timeStep`, which give an integer values for the evaluated action,
an indicator that the transition is on the sampling policy (or not), and a counter of the time step in which
the transition was generated.

Additionally, you must annotate the columns of the database so mfmci will know
how to use them.

## Annotating Columns

You need to annotate the columns of the database in a separate file named `annotate.py`.
We recommend you copy an existing `annotate.py` file
(e.g. `databases/wildfire/annotate.py`) and update the entries to match your
database. We define each of the column labels below. We have an (under review)
research paper discussing which columns should be included in the pre/post transition
state.

**Pre-Transition State:**
pre and post-transition columns are used in evaluating the MFMC distance
metric. The pre-transition state transitions to the post-transition state,
which is subsequently used to find a transition in the state transition database
with a matching pre-transition.

**Post-Transition State:**
These are the result state from the pre-transition state.

**State Summary:** These variables will be included
in the trajectory object generated by MFMCi.
These might include rewards, composite state summaries, the post-transition state,
or other variables.

## Specifying Policy Classes

Policies can be a function of any of the variables found in the database.
MDPvis queries trajectories from the policy classes you specify by
setting the parameters of the policies.

We recommend you copy one of the existing policy classes from the databases
folder (e.g. `databases/wildfire/policies.py`) and update it to suit your needs.

## (Optional) Specify a Distance Metric

Currently all distance metrics use Euclidean distance normalized by the inverse variance of the
variable used in the distance metric. To allow experimentation with different distance metrics,
we process the database the first time it is loaded to find the variance of all the numeric variables.
You cannot currently include actions in the distance metric. If you want support for actions, please
open an issue on the repository.

# Testing and Contributing

This repository has a set of unit tests that are run every time commits
are pushed to GitHub. You can run them locally by issuing `nosetest tests`.
We encourage anyone submitting code to also submit unit tests covering
the submitted code. You can see the current tests in the `tests` folder.

# Credits and Contact

If you are having trouble with MFMCi,
please open an issue on the repository or use
one of the contacts found below.

Maintainer: [Sean McGregor](http://seanbmcgregor.com)  
Email: mfmciGitHubReadme --the at sign-- seanbmcgregor.com  
Maintainer Mailing Address: PO Box 79, Corvallis, OR 97339, United States of America  

Implementation by: Sean McGregor  
With: Rachel Houtman, Hailey Buckingham, Claire Montgomery, Ronald Metoyer, and Thomas G. Dietterich
