## WHAT IS IT?

This is a prototype of the bacteria that live in our guts; our human gut microbiome. In this model, they digest carbohydrates, reproduce, and die.

## HOW IT WORKS

To simulate real-life meals, the model comes equipped with an internal clock. The model consumes 3 meals each day, at 9 a.m., 3 p.m., and 9 p.m..

When the meal is "consumed", food appears at the top of the rectangular interface, which resembles the gut. Food will then move downwards in a pulsating motion as it gets digested by the gut bacteria. When food accumlates in the bottom, defecation takes place.

In this model, each bacterium is equipped with a single metabolic gene, which controls the size of the carbohydrate particle it can digest. G1 bacteria can digest large carbs, G2 -> medium carbs, and G3 -> small ones.



## HOW TO USE IT

(how to use the model, including a description of each of the items in the Interface tab)
The following is a description of each variable in the interface:


 1. Bacteria?: The number of bacteria starting in the model.
 2. Carb?: The number of carbohydrate particles per meal.
 3. Reproduction-threshold: if the energy level in bacteria exceeds this, they reproduce.
 4. Reproduction-cost: when bacteria reproduce, they lose this amount of energy, and
    divide the remaining amount, where each daughter bacterium recieves the half.
 5. Defecation-threshold: this multiplier affects the amount of carbs needed to accumlate
    in the bottom for defecation to take place.
 6. Starting-energy: This variable is used to determine the starting energy of bacteria.   7. D-duration?: the time each bacterium takes to digest its food.


## THINGS TO NOTICE

Note how G3 bacteria skyrocket, compared to G2 or G1. This is because there is an exponential increase in the number of particles available for digestion as large particles are broken down to smaller ones.

## THINGS TO TRY

You can try anything you wish, just remember to press the Halt command in case the model gets overloaded with agents. Halt can be found under tools in the task bar.

## EXTENDING THE MODEL

This model needs a valid crowding-index, that makes it harder to reproduce if there area around a single bacterium is crowded. Can you think of one?

It also needs to simulate bacterial waste, where the more bacteria gain energy, the more waste they produce. Waste can either be absorbed by the body, or pass in stool, and could inhibit some bacteria's growth. Can you think about how to code into the model?

Finally, what do you think will happen if one bacteria had two genes instead of one?
What about if each gene's effective size was much smaller? Play around with it and see what comes out!


## CREDITS AND REFERENCES

Copyright 2019 Abdalrhman Mostafa.

I thank Duncan Mcgreggor for the helping me program the pulsation movement of food, and Jaime Fernandez and Andreas Sjostedt for the useful suggestions.
