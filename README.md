# Notion Formula Tutorial: Convert Numbers with Decimals into Moon Phase Emojis

There are many Notion formula tutorials that convert number ratings into cute emojis, but none seem to address ratings with decimals like 3.5, 2.2, or 4.75. To fill this gap, I wrote my own formula. This will be particularly useful for nuanced book and movie ratings.

We will use a rating scale of 1-5, represented with different moon phase emojis: 🌑🌘🌗🌖🌕.

---

## Preparing the Database

You can utilize any database view, but for clarity in this tutorial, I recommend using the Table view.

### Step 1. Create a Number Property

Within your database, create a number property named “Number Rating.” This property will store your numerical ratings. You may choose to hide it later if you prefer displaying only the final moon ratings.

### Step 2. Create a Formula Property.

The name of this property is flexible. In my setup, I call it “Moon Ratings.” This is where we will write our formula.

![[Pasted image 20240525155207.png|500]]

---

## Assigning Values to the Emojis

First, let’s establish the values associated with each moon phase emoji:

- 🌑 = 0
- 🌘 = for decimals less than 0.5
- 🌗 = for a decimal value of exactly 0.5
- 🌖 = for decimals greater than 0.5
- 🌕 = 1

Integers will be represented by full moons. For example, the whole number 3 will be depicted as three full moons (🌕🌕🌕) or as three full moons and two new moons (🌕🌕🌕🌑🌑).

Adding new moons will show that the rating scale is up to 5. This approach also maintains a uniform number of emojis, making the moon ratings appear tidier.

**More examples:**

- 4.25 = 🌕🌕🌕🌕🌘
- 2.75 = 🌕🌕🌖🌑🌑
- 5.00 = 🌕🌕🌕🌕🌕

---

## Writing the Formula

Writing the formula will be done in four stages: Full Moons, Half-Moons, New Moons, and Integration. Upon completing the first three stages, you’ll have three distinct formulas. Set aside each formula upon completion of its respective stage. Ultimately, in the final Integration stage, we’ll consolidate these formulas to derive our comprehensive moon ratings formula.

Before we begin, please note the following: Avoid copying and pasting the formulas provided below directly into your database, as this method won’t function properly. Instead, type out the formula manually, ensuring that you input the property name (“Number Rating” in this case) within the formula. Notion lacks the capability to automatically link the property name in copied formulas with the corresponding property in your database, necessitating manual input for the formula to work.

### Stage 1. Full Moons

#### Step 1. Isolate the Integers

To isolate the integer part of our number rating, we use the `floor` function. This function provides the largest whole number that’s equal to or less than the given decimal. For example, whether it’s 2.3 or 2.85, `floor` will always return 2. If the number is already a whole number, like 3, it will simply return 3.

```
floor(Number Rating)
```

#### Step 2. Convert Integers to Full Moons

Now that we have our integers, let’s convert it to full moons using the `repeat` function.

```
repeat("🌕", floor(Number Rating))
```

The `repeat` function repeats the specified text a given number of times. In the first argument, we designate the full moon emoji `“🌕”` as the text to be repeated. In the second argument, we indicate how many times to repeat it using the integer expression `floor(Number Rating)` we obtained from the previous step.

Test the formula in your database. If it works, you should now have something like this:

![[Pasted image 20240525171443.png]]

### Stage 2. Half-Moons

#### Step 1. Isolate the Decimals

We can isolate the decimal part of the rating by subtracting the integer `floor(Number Rating)` from the total number:

```
Number Rating - floor(Number Rating)
```

If the rating is 3.75, the expression above should return the decimal value of 0.75.

#### Step 2. Let *x* Be the Decimal

Each half-moon corresponds to a distinct decimal value range. Consequently, we must assess our decimal expression `Number Rating - floor(Number Rating)` against multiple conditions to determine the appropriate half-moon. Rather than repeatedly invoking `Number Rating - floor(Number Rating)`, we streamline the process by representing it with a variable.

We establish variables using the `let` function:

```
let(x, Number Rating - floor(Number Rating))
```

#### Step 3. Convert Decimals to Half-Moons

Now that we have our decimals as a variable, we can proceed to test it against different conditions. To write this, we’ll employ the `ifs` function, which is ideal for handling multiple conditions simultaneously. It will return the value that corresponds to the first true condition.

**Important:**

Write the `ifs` conditions inside the `let` function from the previous step. This is necessary for the variable `x` to work as intended.

**First Condition:** If the decimal is greater than 0 and less than 0.5, return a 🌘

```
let(x, Number Rating - floor(Number Rating), ifs(x > 0 and x < 0.5, "🌘",
```

The first condition `x > 0 and x < 0.5, "🌘"` ends with a comma. We do not close the `ifs` and `let` functions just yet because we still have to add the other conditions.

**Second Condition:** If the decimal is equal to 0.5, return a 🌗

Add the second condition `x == 0.5, "🌗"` right after the first, then end with another comma.

```
let(x, Number Rating - floor(Number Rating), ifs(x > 0 and x < 0.5, "🌘", x == 0.5, "🌗",
```

**Third Condition:** If the decimal is greater than 0.5, return a 🌖

Add the third condition `x > 0.5, "🌖"` right after the second, then end with another comma.

```
let(x, Number Rating - floor(Number Rating), ifs(x > 0 and x < 0.5, "🌘", x == 0.5, "🌗", x > 0.5, "🌖",
```

**Fourth Condition:** If there is no decimal part (i.e., the rating is a whole number), do not add any half-moons.

Add two straight double quotes `""` , then close the `ifs` and `let` functions with two parentheses `))`.

```
let(x, Number Rating - floor(Number Rating), ifs(x > 0 and x < 0.5, "🌘", x == 0.5, "🌗", x > 0.5, "🌖", ""))
```

Test the formula in your database. If everything works correctly, you should have something like this:

![[Pasted image 20240525181756.png]]

### Stage 3. New Moons

#### Step 1. Calculate the Number of New Moons

The number of new moons needed in the moon rating is decided by the difference between the rounded-up number rating and the scale’s upper limit, which is 5.

We can use the `ceil` function to obtain the rounded-up number, then subtract it from 5.

```
5 - ceil(Number Rating)
```

#### Step 2. Convert Number to New Moons

We can use the `repeat` function again from Stage 1 Step 2, but this time with the expression we used in the previous step.

```
repeat("🌑", 5 - ceil(Number Rating))
```

If everything works well, you should now have something like this:

![[Pasted image 20240525202042.png]]

### Stage 4. Integration

Now, let’s consolidate the three formulas from the previous stages.

**Full Moons:**

```
repeat("🌕", floor(Number Rating))
```

**Half-Moons:**

```
let(x, Number Rating - floor(Number Rating), ifs(x > 0 and x < 0.5, "🌘", x == 0.5, "🌗", x > 0.5, "🌖", ""))
```

**New Moons:**

```
repeat("🌑", 5 - ceil(Number Rating))
```

Combining these three is straightforward. We simply add them together using `+`:

```
repeat("🌕", floor(Number Rating)) + let(x, Number Rating - floor(Number Rating), ifs(x > 0 and x < 0.5, "🌘", x == 0.5, "🌗", x > 0.5, "🌖", "")) + repeat("🌑", 5 - ceil(Number Rating))
```

And we are done!

![[Pasted image 20240525203042.png]]
