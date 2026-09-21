# Exercises

> 来源：book-rev7.pdf，第 25–32 页（英文原文）

1. Look at real operating systems to see how they size memory. 2. If xv6 had not used super pages, what would be the right declaration for en-

```c
trypgdir?
```

3. Unix implementations of exec traditionally include special handling for shell scripts. If the file to execute begins with the text #!, then the first line is taken to be a program to run to interpret the file. For example, if exec is called to run myprog arg1 and myprog ’s first line is #!/interp, then exec runs /interp with command line /interp myprog arg1. Implement support for this convention in xv6.
