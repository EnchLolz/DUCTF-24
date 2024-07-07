# Wacky Recipe

Category: MISC (Easy)

Points: 141

Solves: 152

>Our Cyber Chef has been creating some wacky recipes recently, though he has been rather protective of his secret ingredients. Use this Chicken Parmi recipe and decipher the missing values to discover the chef's secret ingredient!

### Solution

It seems like we are given a program in the [Chef esoteric language](https://esolangs.org/wiki/Chef), where the values of the vars (declared in `Ingredients.`) `pain` and `effort` are redacted. These vars are then used in some convoluted set of operations (declared in `Method.`) to create a stack of numbers. These numbers are then converted to chars using ascii values (Liquefy). Finally, the string is outputted:

```
...
Ingredients.
?? dashes pain
?? cups effort
1 cup water
4 kg bread crumbs
26 ml hot canola oil
13 kg egg yolks
...
Method.
Put water into 1st mixing bowl.
Add water to 1st mixing bowl.
Add water to 1st mixing bowl.
Add water to 1st mixing bowl.
Combine pain into 1st mixing bowl.
Remove bread crumbs from 1st mixing bowl.
...
Liquefy contents of the mixing bowl.
Pour contents of the mixing bowl into the 1st baking dish.
Refrigerate for 1 hour.
```

To solve this, we can write a script to help interperate this the esolang for us and keep track of when `pain` and `effort` are used. The only functions we really need to implement are `Put`, `Add`, `Combine`, and `Remove`. Looking over [documentation](http://progopedia.com/language/chef/):

-   Put: pushes the value of var onto stack
-   Add: adds the value of var to the top item of the stack
-   Combine: multiplies the value of var to the top item of the stack
-   Remove: subtracts the value of var from the top item of the stack

We can write a simple python script to evaluate the program:
```py
#!/usr/bin/env python3

# Open file and retrieve each line
lines = []
with open("recipe.txt") as recipe:
    for line in recipe:
        lines.append(line.strip())

# Skip to ingredients (variables) list
idx = 0
while idx < len(lines):
    if lines[idx] == 'Ingredients.':
        break
    idx+=1
idx+=1

# Store ingredients
ingredients = dict()
while lines[idx]:
    # number, unit (has no effect), variable name
    value, tmp, var = lines[idx].split(' ',2)
    # Filters out Pain and Effort
    if value == '??':
        ingredients[var] = value
    else:
        ingredients[var] = int(value)
    idx+=1


# Skip to method (where the actual instructions are done)
while idx < len(lines):
    if lines[idx] == 'Method.':
        break
    idx+=1
idx+=1

# Process method
stack = []
while idx < len(lines):
    # Function will be the first word
    func, temp = lines[idx].split(' ',1)
    # Liquefy is after all numbers are calculated and 
    if func == 'Liquefy':
        break

    # Get name of var
    # Methods are in the form Fun Var in/into nth mixing bowl
    # This strips the last part off since we only use 1st mixing bowl
    var = temp[::-1].split(' ',4)[4][::-1]

    # Push item to stack
    if func == 'Put':
        # Initial value in the form a+b*pain+c*effort
        val = [0,0,0]
        if var == 'pain':
            val[1]+=1
        elif var == 'effort':
            val[2]+=1
        else:
            val[0]+= ingredients[var]
        stack.append(val)

    # Add to top value
    if func == 'Add':
        # If add pain or effort add 1 to their counters
        if var == 'pain':
            stack[-1][1]+=1
        elif var == 'effort':
            stack[-1][2]+=1
        # Else add real value
        else:
            stack[-1][0]+= ingredients[var]
    
    # Subtract from top value
    if func == 'Remove':
        # Basically add but reverse
        if var == 'pain':
            stack[-1][1]-=1
        elif var == 'effort':
            stack[-1][2]-=1
        else:
            stack[-1][0]-= ingredients[var]

    # Multiply to top value
    if func == 'Combine':
        # If I had a value like pain^2 that would be a pain to store
        # Luckily that didn't happen
        if var == 'pain':
            # This was a pain
            # If value so far is fully known then val --> val*pain
            if stack[-1][1] == 0 and stack[-1][2] == 0:
                # Shift val into pain register
                stack[-1][1] = stack[-1][0]
                stack[-1][0] = 0
            # Never had a case with pain^2 or pain*effort thankfully
            else:
                raise NotImplementedError("pls don't multiply by pain")


        elif var == 'effort':
            # Never multiplied effort :D
            raise NotImplementedError("pls don't multiply by effort")
        else:
            # If real value just multiply everything
            stack[-1][0]*= ingredients[var]
            stack[-1][1]*= ingredients[var]
            stack[-1][2]*= ingredients[var]

    idx+=1

# Playing around with https://esolangpark.vercel.app/ide/chef
# The stack is printed in reverse order
stack = stack[::-1]
print(stack)
```

Running this gives us:

`[[20, 1, 1], [10, 2, 1], [19, 1, 1], [9, 2, 1], [-5, 2, 1], [-6, 4, 1], [2, 1, 1], [-13, 4, 1], [-14, 4, 1], [10, 3, 1], [-7, 3, 1], [11, 2, 1], [-1, 3, 1], [-26, 4, 1], [26, 2, 1], [7, 3, 1], [-24, 4, 1], [-13, 4, 1], [26, 2, 1], [-4, 4, 1]]`

Each triple's value is `a+b*pain+c*effort`. Since we know the flag wrapper the first two triples must have value `D` and `U` respectively, so we can solve for pain and effort algebraically.

```math
20+1p+1e = 68 \\
10+2p+1e = 85 \\
\text{Subtract top from bottom}\\
p-10=17 \\
p = 27 \\
\text{Solve for effort}\\
20+17+1e = 68 \\
e = 21
```

Plugging these values in and running yields the flag:

```py
pain = 27
effort = 21

for x in stack:
    print(chr(x[0]+x[1]*pain+x[2]*effort),end="")
# DUCTF{2tsp_Vegemite}
```

### Flag

```DUCTF{2tsp_Vegemite}```