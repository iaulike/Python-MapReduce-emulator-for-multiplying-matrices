# Python-MapReduce-emulator-for-multiplying-matrices

This is my solution to a problem in the Data Science at Scale course, run by Bill Howe at Coursera. ***I highly recommended this course. I haven't had so much fun for ages!*** 

# Problem
Assume you have two matrices A and B in a sparse matrix format, where each record is of the form i, j, value. Design a MapReduce algorithm to compute the matrix multiplication A x B

# Map Input
The input to the map function will be a row of a matrix represented as a list. Each list will be of the form [matrix, i, j, value] where matrix is a string and i, j, and value are integers.

The first item, matrix, is a string that identifies which matrix the record originates from. This field has two possible values: "a" indicates that the record is from matrix A and "b" indicates that the record is from matrix B

# Reduce Output
The output from the reduce function will also be a row of the result matrix represented as a tuple. Each tuple will be of the form (i, j, value) where each element is an integer.

"""
# Matrix Multiplication Example in the Simple Python MapReduce Framework
"""

mr = MapReduce.MapReduce()

=============================
Do not modify above this line

def mapper(record):
    
    # key: if record is element i, j from matrix a, there are k keys of the form (i,k) where k is numcols(a) or 
    # key: if record is element i, j from matrix b, there are k keys of the form (k,j) where k is numrows(b)
    # value: the input record ["a" or "b", i, j, value]
    value = record
    if record[0] == 'a':
      for k in range(5):
        key = (record[1], k)
        mr.emit_intermediate(key, value)
    if record[0] == 'b':
      for k in range(5):
        key = (k,record[2])
        mr.emit_intermediate(key, value)		

def reducer(key,list_of_values):

    # key: of the form (i,j)
    # value: all records associated with posn (i,j) of the matrix multiplication,
    # value: ie [a, i, k, value] for all columns k and [b, k, j, value] for all rows k. 
    a_vec = [0]*5
    b_vec = [0]*5	
    for element in list_of_values:
      if element[0] in 'a':       
        a_vec[element[2]] = element[3]
      if element[0] in 'b':
        b_vec[element[1]] = element[3]
    outnum = 0
    outrow=list(key)
    for k in range(5):
      outnum = outnum + a_vec[k] * b_vec[k]
    outrow.append(outnum)
    outrow=tuple(outrow)	
    mr.emit((outrow))

Do not modify below this line
=============================
if __name__ == '__main__':
  inputdata = open(sys.argv[1])
  mr.execute(inputdata, mapper, reducer)
