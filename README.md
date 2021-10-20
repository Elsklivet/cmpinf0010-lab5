# Lab 5 Exercise
*Gavin Heinrichs (Elsklivet), Shannon Gorny (sng41), Carly Ukalovic*

This program takes two inputs, a user's `name` and `age`. It uses these two inputs to generate a fairly unique 7-digit PeopleSoft ID for the user.

## What the software does...

First we get the inputs and make sure that the integer age is the correct type, otherwise we give it a fail value of 20 (0x14 in hex).
```python
name = input("Input a name: ")
age = input("Input an age: ")

try:
    age = int(age)
except ValueError:
    age = 0x14
```

Next we need to generate a hash from the name that was given by the user. We'll use the SHA1 algorithm and then get the hexadecimal out of the object that will be returned by the function call. That returns a string though, and we want to do math with this later, so we extract the bottom 8 bytes (16 characters). Note that 8 bytes was chosen simply because it's the size of a `long long` on this machine.
```python
_hash = hashlib.sha1(name.lower().encode()).hexdigest()
_hash = int(_hash[-16:], 16)
```

After that we want to take the age we were given and do a very naive version of [Donalt Knuth's multiplicative hash](https://stackoverflow.com/a/41537995). This will generate a sort of hash based on the age.
```python
age = (age*2654435761) % (2**32)
```

Now we will use the two hashes that we got in combination to make a unique ID. The ID combines the name and age of a student by using bitwise AND to extract the bits that are the same across both hashes.
```python
ID = (_hash & age)
```

Afterwards, we need to make sure that the ID is only 7 digits long at most. To do this we'll mask out the bottom 23 bits of the result from above. (Note: we know to use 23 bits because 2^23 = 8388608 and 2^24 = 16777216, which is one too many digits, so 23 bits is the maximum number we can use to guarantee 7 or less digits.)
```python
ID = ID & 0b11111111111111111111111
```

Finally, some ages will cause the result to be only six digits, so append a zero onto these IDs so they hit the 7 digit requirement:
```python
ID = ID * 10 if ID < 1000000 else ID
```

And that's it! After that, it will print out an ID. This ID should be the same if the same name and age is entered, but different otherwise. Thus, a student can enter their name and age to get an ID unique to them. (Note, the IDs will collide if you only enter first names and two students share a first name and age, so use students' *full names*.)

## How to use the software...

### Installation
To install, first open the project you'd like to use the software in using [JupyterHub](https://jupyter.org/hub). Open a terminal in the project by using the `+` icon and choosing `Terminal`. Move to the directory you want to use with `cd` and then type:

```
git clone https://github.com/Elsklivet/cmpinf0010-lab5 <directory to clone into>
```

At that point a Notebook file (`.ipynb`) will be in the folder you cloned into. The code can be copied into any other notebook if you'd like to include it in already-existing code, or you can use the given notebook as a starter. Because it is a notebook, **there is no module information, `__init__`, or ability to do a `pip install`!** You must either use the notebook or copy into a `.py` file of your own creation.

### Usage
Since the program is designed to just output an ID based on input, it can be run directly from JupyterHub. Once the repository is cloned, open the notebook file and press the run button on the top of the JupyterHub interface. The program will expect a name and an age as input from `stdin`.

## How to contribute...
*All contributions must follow the guidelines laid out in [the code of conduct](CODE_OF_CONDUCT.md).*

To contribute to the software, create a fork of the repository in GitHub. Make any changes you want within your repository, then open a pull request in the base repository (this one). We will review your changes and open a discussion on any further changes that need to be made before your edits are flush with ours and ready to merge. Once we determine the changes follow our Code of Conduct and are appropriate additions to the project, we will merge your pull request.

## Licensing Information

This software uses the [GNU GPL v3](LICENSE.md), which is a permissive/copyleft license. We chose this license because it allows the community to use the software in a very free manner. The GPL permits you to:
* Modify this program
* Redistribute this program
* Use this program commercially, privately, and in patents.

Additionally, we, the authors of the software, are free of liability if you use the software and we do not provide any warranty along with it.

## Code-Of-Conduct Information

The code we used was a pre-existing Citizen Code of Conduct, which is inclusive allowing a large number of contributors with a variety of backgrounds. The code of conducts outlines the expectations of CMPINF0010 along with the consequences. The conduct list is as followed:
* Purpose
* Open [Source/Culture/Tech] Citizenship
* Expected Behavior
* Unacceptable Behavior
* Weapons Policy
* Consequences of Unacceptable Behavior
* Reporting Guidelines
* Addressing Grievences 
* Scope
* Contact Info
* License and attribution

Generally this Code of Conduct covers every aspect of guidelines and sets up for the basics.
