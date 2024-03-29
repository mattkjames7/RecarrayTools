# RecarrayTools
Some simple tools for managing numpy.recarray objects

## Installation

Install via `pip`:

```bash
pip3 install --user RecarrayTools
```

Or this repo:

```bash
#clone the repo
git clone https://github.com/mattkjames7/RecarrayTools
cd RecarrayTools

#either use a wheel
python3 setup.py bdist_wheel
pip3 install --user dist/RecarrayTools-0.0.3-py3-none-any.whl

#or just install directly
python3 setup.py install
```

## Usage

This module contains a small number of routines...

### ```SaveRecarray()```

This will save a record array to a binary file - note that the dtype of the record array shouldn't be too exotic (object arrays would not work - use pickle for those).

```python
import numpy as np
import RecarrayTools as RT

#create some recarray
dtype = [('a','int32'),('b','float64',(6,))]
arr = np.recarray(10,dtype=dtype)

#fill it
arr.a = blah #shape (10,)
arr.b = stuff #shape (10,6)

#save it
RT.SaveRecarray(arr,'path/to/file.name',Progress=True)
```

The file format used here is simple:

The first 4 bytes correspond to a 32-bit integer containing the size of the recarray (i.e. `arr.size`).

Then each field `arr.dtype.names` is stored contiguously as whatever dtype it was assigned with, one field at a time.

The file created in the above example would be formatted in the following way:

Bytes 0-3 : 32-bit integer - total length of the recarray

Bytes 4-43 : Array of 32-bit integers, length 10 (```arr.a```)

Bytes 44-523: Array of 64-bit floating points, shape `(10,6)`, length 60

EOF

### ```ReadRecarray()```

This will read in the files created by ```SaveRecarray()```, e.g.

```python
dtype = [('a','int32'),('b','float64',(6,))]
fname = 'path/to/file.name'
arr = RT.ReadRecarray(fname,dtype)
```

### ```ReduceRecarray()```

This reduces the number of fields in a recarray object, e.g.:

```python
#initial object with fields 'a', 'b', 'c' and 'd'
dtype = [('a','int32'),('b','float64',(6,)),('c','int64'),('d','float64')]
obj0 = np.recarray(10,dtype=dtype)

#new object with just fields 'a' and 'c'
obj1 = RT.ReduceRecarray(obj0,['a','c'])
```

### ```JoinRecarray()```

Append two recarrays with identical dtypes:

```python
C = RT.JoinRecarray(A,B)
```

### ```AppendFields()```

Append some extra fields to a recarray:

```python
#some initial recarray
A = np.recarray(n,dtype=dtype)

#new fields for the array
x = np.arange(n)
y = x**2

#add them
B = RT.AppendFields(A,[('x','float32'),('y','float32')],(x,y))
#B now has fields B.x and B.y
```

### ```InterpRecarrayFields()```

Interpolate fields within a recarray:

```python
#a would be the initial recarray, b would be the new recarray
#RefField = name of field to interpolate over
#InterpFields = list of names of fields to interpolate
b = RT.InterpRecarrayFields(a,b,RefField='x',InterpFields=['a','b','c','d','x'])
```





