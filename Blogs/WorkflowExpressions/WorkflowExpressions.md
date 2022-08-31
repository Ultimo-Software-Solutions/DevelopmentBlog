# Workflow Expressions

In workflows, it is possible to use expressions. With expressions, you can make calculations, perform string mutations and work with dates. Expressions are very powerful and support many different data types. It can result in a calculated value, a true/false value or a string result.

We analyzed all workflows and found that one of the most common uses for expressions is string concatenation. The example below shows two strings that are concatenated with a hyphen,

```
<Assign Name="Set ExternalId" Property="${PurchaseLine.ExternalId}" Value="=#concat(${Purchase.Id},'-',${PurchaseLine.Id.LineId})" />
```

Did you know that this can be simplified? The following example has the same result. Instead of an expression, a template is used.

```
<Assign Name="Set ExternalId" Property="${PurchaseLine.ExternalId}" Value="${Purchase.Id}-${PurchaseLine.Id.LineId}" />
```

## Templates vs Expressions

When using values in a workflow, the engine will parse the text in the value property. Based on the content it chooses between the following:

| Type | Description |
|-|-|
| Variable | Return the variable value, any type.
| Template | Evaluates a template and returns a string.
| Expression | Evaluates an expression and returns the value.

Everywhere in a workflow in which are working with values, the above types can be used. For example in a PropertyFilter, Assign or Parameter. Below are some examples.

| Example       | Result  | Description |
|---------------|---------|-------------|
| `${LineId}`     | 01      | There is only a reference to a variable in the value, therefore it will return the value of the variable without type conversions.
| `${LineId}-01`  | 01-01   | There is a variable reference and a text, the workflow engine will handle this as a template. A template always returns a string. It will concatenate variables and constants.
| `${LineId}${LineId}`  | 0101   | There are multiple variable references, the workflow engine will handle this as a template. A template always returns a string. It will concatenate variables and constants
| `=${LineId}-01` | 0       | The '=' marker tells the workflow engine to parse this value as an expression. The expression will attempt to calculate the outcome. In this case, it can convert values to nummeric values and uses the minus sign as an operator.
| `=${LineId}==01` | True | The '=' marker tells the workflow engine to parse this value as an expression. The equality operator is used to compare left and right. It will attempt to converty both the string and nummeric values to the same type before comparing.
| `=#concat(${LineId}, '-', '01')` | 01-01 | The '=' marker tells the workflow engine to parse this value as an expression. This expression uses a function that explicitly concatenates strings.

There is one exception; condition fields. Conditions in workflows are always eveluated as an expression and the result is always converted to true or false.

When to use a variable, template or expression depends on the situation. However, if you only need to concatenate strings, I find templates much more readable and easier to use.

## Usefull Expression Functions

The expression engine supports many functions that can simplify your workflow. The fragment in the example below is found in multiple workflows.

```
<When Name="Check_1" Condition="#length(${LineId}) == 1">
    <Assign Name="Assign_LineId" Property="${NewLineId}" Value="=#concat('0000',${LineId})" />
</When>

<When Name="Check_2" Condition="#length(${LineId}) == 2">
    <Assign Name="Assign_LineId" Property="${NewLineId}" Value="=#concat('000',${LineId})" />
</When>

<When Name="Check_3" Condition="#length(${LineId}) == 3">
    <Assign Name="Assign_LineId" Property="${NewLineId}" Value="=#concat('00',${LineId})" />
</When>

<When Name="Check_4" Condition="#length(${LineId}) == 4">
    <Assign Name="Assign_LineId" Property="${NewLineId}" Value="=#concat('0',${LineId})" />
</When>
```
Did you know there is a function #fillstart that does the same thing? And notice that in the example below it has been written as a template. Almost all functions can be used in expressions and templates.

```
<Assign Name="Assign_LineId" Property="${NewLineId}" Value="#fillstart(${LineId}, '0', 5)" />
```

A complete list of all the functions with examples can be found in the documentation menu in the UC-Tool.