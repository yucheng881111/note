# Karatsuba Algorithm

X=XhB+Xl
Y=YhB+Yl
where Xl<B,Yl<B and Y<=X<(B^2)

XY=XhYhB^2+XlYlB+XhYhB+XlYl
Z=XY=ZhB^2+ZmB+Zl

Zh=XhYh
Zm=XlYh+XhYl
Zl=XlYl

Zm=(Xh+Xl)(Yh+Yl)-XhYh-XlYl=(Xh+Xl)(Yh+Yl)-Zh-Zl

## Complexity
O(n^(lg3)) = O(n^(1.59))

## Pseudo code
```
procedure karatsuba(num1, num2)
  if (num1 < 10) or (num2 < 10)
    return num1*num2
  /* calculates the size of the numbers */
  m = max(size_base10(num1), size_base10(num2))
  m2 = m/2
  /* split the digit sequences about the middle */
  high1, low1 = split_at(num1, m2)
  high2, low2 = split_at(num2, m2)
  /* 3 calls made to numbers approximately half the size */
  z0 = karatsuba(low1,low2)
  z1 = karatsuba((low1+high1),(low2+high2))
  z2 = karatsuba(high1,high2)
  return (z2*10^(2*m2))+((z1-z2-z0)*10^(m2))+(z0)
```
## Example
x=1234=12x100+34
y=5678=56x100+78
Zh=12x56=672
Zl=34x78=2652
Zm=(12+34)x(56+78)-Zh-Zl=2840

xy=672x10000+2840x100+2652=7006652
