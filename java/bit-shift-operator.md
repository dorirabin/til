# Bit shift operator

`>>>` is a unsigned right shift operator. It also adds zero to the most significant digit.

`>>` is a signed right shift operator. It adds '1' if existing value is negative, otherwise '0'.

For positive value, it gives same value for both unsigned and signed right shift operator. Also, there is no unsigned left shift operator.

Example

```
public static void main(String[] args) {
    display(Integer.toBinaryString(-2));
    display(Integer.toBinaryString(-2 >>> 1));
    display(Integer.toBinaryString(-2 >> 1));

    display(Integer.toBinaryString(2));
    display(Integer.toBinaryString(2 >>> 1));
    display(Integer.toBinaryString(2 >> 1));
}

private static void display(String str) {
    System.out.println(StringUtils.leftPad(str, 32, '0'));
}
```

Result

```
11111111111111111111111111111110
01111111111111111111111111111111
11111111111111111111111111111111
00000000000000000000000000000010
00000000000000000000000000000001
00000000000000000000000000000001
```