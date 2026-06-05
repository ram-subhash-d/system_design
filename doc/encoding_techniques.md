# Counter
A naive encoding technique. Maintain a global counter, increment and give its value for the next encoding. If only numbers are used in the counter, then the encoded string quickly becomes long over many encodings. For example if we are doing the encoding for the billionth time then the encoded string would be "1000000000".  
To solve this we can use a counter with higher base value, like base 62(numbers and both the case alphabets), this way we can take a large number and srink its size of representation.  
We can add 2 more characters to base 62 like '_' and '-' to make it base 64. There is not much of a difference between base 62 and base 64 but conversion of a number into base 64 will be easy as it is a power of 2.

# Base conversion
Below are the steps(to code) to convert a base 10 number to the desired base. Say for example we want to convert 23 into base 7.
* Divide the given number by the desired base, 23/7 in this case.
* Place the quotient in the next higher place, that is the 7's place, 3 in this case.
* Place the remainder in the current place, that is the 1's place, 2 in this case.
* If quotient is greater than base(7), we can repeat steps 2 and 3 for the next higher place.  

To convert it back to base 10, sum the digits with their place value. For this example, 32(base 7) = 2 * 7<sup>0</sup> + 3 * 7<sup>1</sup> = 23(base 10).  
Integer division on computers(binary, base 2) is easy with 2<sup>x</sup>, its like dividing with 10<sup>x</sup> in decimal. Therefore division by 64(2<sup>6</sup>) means right shifting the number by 6. 

The down side of this approach is that our short url is still predictable, as we are taking a counter and doing a base 64 on it, it can be easily decoded.