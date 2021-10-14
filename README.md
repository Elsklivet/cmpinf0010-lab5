# Lab 5 Exercise
*Gavin Heinrichs (Elsklivet), Shannon Gorny (sng41), Carly Ukalovic*

This program takes two inputs, a user's `name` and `age`. It uses these two inputs to generate a fairly unique 7-digit PeopleSoft ID for the user.

## How it works

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

And that's it! After that, it will print out an ID. This ID should be the same if the same name and age is entered, but different otherwise. Thus, a student can enter their name and age to get an ID unique to them. (Note, the IDs will collide if you only enter first names and two students share a first name and age, so use full names.)