# Microbiome-dynamics
My first netlogo project that aims to unravel the dynamics of the gut microbiome



extensions [ palette ]

globals [
  day-counter current-hour                                        ; Model clock
  reproduction-counter digestion-counter                          ; Count no. of reproductions and digestions
  meal-1-lock meal-2-lock meal-3-lock                             ; Regulate meal timings
]


patches-own [ O-glycan-level ]


; See the human/mouse microbiome project, and decide on how to construct your breeds.
; IDEA: Need to create a code-generator, which generates code depending on what species you want to create.


breed [ bacteria bacterium ]
bacteria-own [ energy gene1 gene2 gene3 digestion-lock digestion-duration ]


breed [ carbs carb ]
carbs-own [ Bulk ]                                          ; Bulk is the size of carbs


to setup
  ca
  create-flora
  create-gut
  reset-variables
  upload-genes
  reset-ticks
end


to go
  ;initiate-food
  clock
  day-meals
  initiate-bacteria
  digest
  tick
end

to reset-variables
  set day-counter 0
  set current-hour 0
  set meal-1-lock false
  set meal-2-lock false
  set meal-3-lock false
end


to create-gut
  ask patches [
    set pcolor palette:scale-gradient [[254 232 200] [253 187 132] [227 74 51]] pycor max-pycor min-pycor
  ]
end


to create-flora
  create-bacteria bacteria? [
    set color red
    set shape "ecoli"
    set size 0.5
    set energy random starting-energy
    setxy random-xcor random-ycor
  ]
end

to upload-genes
  let gene1-bac n-of (round (33 / 100 * bacteria?)) bacteria
  let gene2-bac n-of (round (33 / 100 * bacteria?)) bacteria with [not member? self gene1-bac]
  let gene3-bac bacteria with [not member? self gene1-bac and not member? self gene2-bac]

  ask gene1-bac [ set gene1 true set gene2 false set gene3 false ]
  ask gene2-bac [ set gene1 false set gene2 true set gene3 false ]
  ask gene3-bac [ set gene1 false set gene2 false set gene3 true ]
end


to create-food
  create-carbs Carb? [
    set shape "circle"
    set Bulk 2 + random-float 1.0
    set size Bulk
    set color blue
    setxy (random-float 6 - 3) 30
    ]
end


to initiate-bacteria
  ask bacteria [
    ifelse energy < 10 or digestion-lock = true
    [ stop ]
    [ fd random-float 2.0
    rt random 360
    set energy energy - 1
    reproduce-&-die
    ]
  ]
end

to digest

; Digestion coefficients are used to determine a random cutsize of carbs.
  ;For instance, a 3-sized carb can be digested into a 0.1 and a 2.9 carb or a 1.5 and a 1.5 sized carb.

  let digestion-co-1 random 100
  let digestion-co-2 (100 - digestion-co-1)
  ask bacteria [
  (ifelse
      gene1 = true and count carbs-here with [size <= 3.0 and size >= 2.0] > 0 and digestion-lock = false
      [ create-link-with one-of carbs-here [ tie ]
        set digestion-lock true ]
      gene2 = true and count carbs-here with [size <= 2.0 and size >= 1.25] > 0 and digestion-lock = false
      [ create-link-with one-of carbs-here [ tie ]
        set digestion-lock true ]
      gene3 = true and count carbs-here with [size <= 1.25 and size >= 0.1] > 0 and digestion-lock = false
      [ create-link-with one-of carbs-here [ tie ]
        set digestion-lock true ]
      digestion-duration < d-duration? and digestion-lock = true
      [ set digestion-duration digestion-duration + 1 ]
      digestion-duration = d-duration?
      [ ask link-neighbors [
      hatch-carbs 1 [
        set bulk bulk * digestion-co-1 / 100
        set size bulk
       ]
      hatch-carbs 1 [
        set bulk bulk * digestion-co-2 / 100
        set size bulk
      ]
        die
      ]
       ask my-links [ die ]
       set energy energy + 20
       set digestion-counter digestion-counter + 1
       set digestion-lock false
       set digestion-duration 0
      ]
    [ ])
    ]

end

to reproduce-&-die
  ask bacteria [
    if energy > reproduction-threshold
      [ hatch-bacteria 2
        [ set energy (energy - reproduction-cost?) / 2
          ; insert genetic algorithm for mutation during reproduction
          ;(ifelse
          ;  random 100 < 3 and gene1 = true [
          ;    set gene1 = false or set gene2 = true or set gene3 = true ]
          ;  random 100 < 3 and gene2 = true [
          ;    set gene1 = true or set gene2 = false or set gene3 = true ]
          ;  random 100 < 3 and gene3 = true [
          ;    set gene1 = true or set gene2 = true or set gene3 = false ]
          ;  [ ])
          set reproduction-counter reproduction-counter + 1
        ]
        die
    ]
  if energy < 1 [
      die
    ]
  ]
end

to initiate-food

  let laggers n-of (round (15 / 100 * count carbs)) carbs
  let regulars carbs with [not member? self laggers]
    ask regulars [
      set-direction
      fd positive-pulse
    ]
    ask laggers [
      set-direction
      fd pulse
    ]

  ;]
  if count carbs with [ ycor < -29 ] > carb? * defecation-threshold
  [ defecate ]
end


to set-direction
  set heading (180 + (random 90 - 45))
end


to-report pulse
  report 0.50 * (0.1 + random-float 1.0) * (sin (ticks * 3.34))
end


to-report positive-pulse
  report abs pulse
end



to day-meals
  (ifelse
    current-hour = 9 and meal-1-lock = false [
      create-food
      set meal-1-lock true
    ]
    current-hour = 15 and meal-2-lock = false [
      create-food
      set meal-2-lock true
    ]
    current-hour = 20 and meal-3-lock = false [
      create-food
      set meal-3-lock true
    ]
    current-hour = 0 [
      set meal-1-lock false
      set meal-2-lock false
      set meal-3-lock false
    ]
  count carbs != 0 [
      initiate-food
    ]
    [ ])

end


to clock
  if ticks mod 60 = 0
  [ set current-hour current-hour + 1 ]
  if current-hour = 24
  [ set current-hour 0
    set day-counter day-counter + 1 ]
end


to defecate
  ask carbs with [ ycor < -25 ] [ die ]
  ask bacteria with [ ycor < -25 ] [ die ]
end
