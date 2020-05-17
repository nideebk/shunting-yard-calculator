# shunting-yard-calculator

![Calculator Picture](https://github.com/nideebk/shunting-yard-calculator/blob/master/images/BasicCalculatorImg.jpg)

This is a calculator that parses infix expressions to post-fix notation to compute expressions.

In this file, the process of parsing the input expression to post-fix notation is thoroughly explained. The reader willed be walked through how the calculator separates the elements in an expression in an array, how the shunting-yard algorithm is used to parse the expression from infix to post-fix notation, and how the post-fix expression is solved using an algorithm.

## Introduction
This WinForm project is a four function calculator which converts infix to postfix notation to solve expressions. It is based of the TI-108 calculator basic calculator. This calculator also has a text box below it that displays errors when they occur.

## The PostFix Parser
### The Shunting-Yard Algorithm:
The shunting-yard algorithm is a simple and efficient way to parse an expression into postfix notation. It uses three data structures to contain the data:

- An array of expression elements
- A stack to hold operands
- An output array/list (to hold the postfix expression)

The shunting yard algorithm contains a series of steps to convert an expression from infix to postfix:

- If the element is an operand, then add it to the output array
- If the element is an operator
  - While the operator on the top of the operator stack is greater than or equal to the current operator, pop operators from the stack onto the output array
  - Push the current operator into the operator stack
- If the element is a left parenthesis, the add it to the output array
- If the element is a right parenthesis, then pop operators from the operator stack into the output array till you reach the left parenthesis, then get rid of the left parenthesis by popping it
- After all the elements are read, then pop out all of the operands in the stack onto the output array

With this method, an expression in infix can be converted into postfix notation. For an example to walk through this process, look at the picture below:  

![Calculator Picture](https://github.com/nideebk/shunting-yard-calculator/blob/master/images/ShuntingYard1.png)

![Calculator Picture](https://github.com/nideebk/shunting-yard-calculator/blob/master/images/ShuntingYard2.png)

![Calculator Picture](https://github.com/nideebk/shunting-yard-calculator/blob/master/images/ShuntingYard3.png)

![Calculator Picture](https://github.com/nideebk/shunting-yard-calculator/blob/master/images/ShuntingYard4.png)

![Calculator Picture](https://github.com/nideebk/shunting-yard-calculator/blob/master/images/ShuntingYard5.png)

**Important: This will only work if the expression is valid.** 

For the calculator, I needed to parse the elements in the expression into postfix notation to make it easier to solve it. I used the shunting-yard algorithm to do this:

    while (expression[exprIndex] != null)
    {
        if (expression[exprIndex].GetType() == typeof(Operand))
            output.Add(expression[exprIndex]);
        else if (expression[exprIndex].GetType() == typeof(Operator))
        {
            while ((operStack.Count() != 0) && (operStack.Peek().GetType() == typeof(Operator)) && ((operStack.Peek() as IOperator).GetPrecedence() >= (expression[exprIndex] as Operator).GetPrecedence()))
                output.Add(operStack.Pop());
            operStack.Push(expression[exprIndex]);
        }
        else if (expression[exprIndex].GetType() == typeof(LeftParaOperator))
            operStack.Push(expression[exprIndex]);
        else if (expression[exprIndex].GetType() == typeof(RightParaOperator))
        {
            while (operStack.Peek().GetType() != typeof(LeftParaOperator))
                output.Add(operStack.Pop());
            operStack.Pop();
        }

        exprIndex++;
    }

    if (operStack.Count() > 0)
        output.Add(operStack.Pop());

    return output;

- **expression**: Array of expression elements
- **exprIndex**: The counter to move through the array
- **operStack**: The stack of operators
- **output**: The output list
- **Operand**: A class for operands
- **Operator**: A class for operators
- **LeftParaOperator**: A class for left parenthesis
- **RightParaOperator**: A class for right parenthesis

By using the shunting-yard algorithm, I pushed operands from the array into a stack, then popped them back into a list when the if statement's conditions were met. In this way, I could effectivly convert the expression stored in the array into postfix notation.

To Solve the Postfix Expression:
After parsing the expression from infix to postfix notation, the expression needs to be solved. A speciific method needs to be utilized to solve postfix expressions. Two data structures are needed for this method:

The array/list with the postfix expression
A stack for operands
While solving an expression in postfix notation, you have to go though every expression element from the **start** of the list to the **end** of the list. To solve the postfix expression, there are three simple steps;

If the element is an operand, push into the operand stack
If the element is an operator, pop the **required number** of operands for the specific operator out of the stack, perform the operation, then push the number back in the stack
After all the elements are read, then pop the number in the stack as your answer

![Calculator Picture](https://github.com/nideebk/shunting-yard-calculator/blob/master/images/PostFixExample.png)

For the calculator, I created an method to solve the expression in postfix notation. To follow the steps in an efficient way, I used the code below:

    foreach (IExpressionElement counter in postFix)
    {
        if (counter.GetType() == typeof(Operand))
        {
            numStack.Push(Convert.ToDouble((counter as IOperand).GetValue()));
        }
        else if ((counter as IOperator).GetOper() == "+")
        {
            a = Convert.ToDouble(numStack.Pop());
            b = Convert.ToDouble(numStack.Pop());
            numStack.Push(a + b);
        }
        else if ((counter as Operator).GetOper() == "-")
        {
            a = Convert.ToDouble(numStack.Pop());
            b = Convert.ToDouble(numStack.Pop());
            numStack.Push(b - a);
        }
        else if ((counter as Operator).GetOper() == "*")
        {
            a = Convert.ToDouble(numStack.Pop());
            b = Convert.ToDouble(numStack.Pop());
            numStack.Push(a * b);
        }
        else if ((counter as Operator).GetOper() == "/")
        {
            a = Convert.ToDouble(numStack.Pop());
            b = Convert.ToDouble(numStack.Pop());
            if (a == 0)
            {
                clickAbility = false;
                return "Undefined";
            }
            numStack.Push(b / a);
        }
    }

- **IExpressionElement**: Interface for every element in an expression
- **postFix**: The list for the postfix expression
- **numStack**: The stack for the operands
- **a** and **b**: Doubles to hold the values for the popped operands (I used only two for my purposes, but more may need to be used)
- **clickAbility**: A boolean which states if all buttons (except clear) are allowed to clicked or not

In this method, I use a foreach loop to do two things: pop operands into a stack and pop two operators out and use an operation them when an operator is found.

But, another piece of code needs to be added to check if the expression is valid or not, because **this algorithm will not work if the expression is not valid.**

    catch
    {
        clickAbility = false;
        ErrorBox.Text = "Invalid Input";
        return "Error";
    }

I placed a try block around the inside of the method to check if the expression is valid or not. If the expression is not valid, the catch block is called, resulting in clickAbility turning on to prevent any buttons being pressed. sending an Error statement to the main TextBox, and setting the error condition for the ErrorBox, the error message textbox as "Invalid Input." Through this, all the flaws of the method can be covered and the postfix expression can be solved.

## Key Points
### Element Verification:

In the calculator, to store the initial expression, I took each elements of an expression and stored them in an array. Before letting the user input any type of element after a previous element, the program needs to check if that element is valid after the previous element (this is to prevent mistakes such as an expression like "2--1" being entered). 

    public enum AllowedNext
    {
        AllowOperatorNext = 1,
        AllowOperandNext = 2,
        AllowLeftParethesisNext = 4,
        AllowRightParenthesisNext = 8,
        AllowInvalidNext = 16,
        AllowMemRecNext = 32
    }

In this code snippet, I created an enum to store integers in specific bit locations. For each element in the expression, I used a bitwise or operator to link specific elements in the enum to show what type of element. is allowed after the specific element.

E.g.: For the operator element, I used the bitwise or operator to link AllowOperandNext, AllowLeftParenthesisNext, and AllowMemRecNext (to recall the operand in stored in the memory). I stored this value in an integer called allowedValue.

After this, I created an method to check if a specific element is valid after the previous element. The code for this is below:

    private bool ValidateValue(AllowedNext calcaction)
    {
        if ((allowable & (int)calcaction) != 0)
            return true;
        else
            return false;
    }

In this method, there are two bytes stored as integers. Allowable is the allowedValue of the previous element of the expression and calcAction is an element in the enum representing what the current element is (e.g.: current element is operand and element in the enum is AllowOperandNext). I used an bitwise and operator to check if the places in the bits overlap; if they do, the element is allowed, and if not, the element is not allowed.

## History
Version 1.0 - First Release of the Shunting Yard Calculator
