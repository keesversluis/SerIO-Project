"Serio2set" LINE 70

1 LET C=65200: LET N$="Cserio"+STR$ PEEK (C+3): LET T=PEEK (C+2):
     LET T=(T<>CODE "T")+(T<>CODE "t"): LET L=PEEK (C+4)+PEEK (C+5)*256: LET M$="Serio2set"
   2 LET CF=3546900: LET B=INT (CF/((PEEK (C+6)+PEEK (C+7)*256+2)*26+3)+.5): LET F=PEEK (C+8)
   3 PRINT AT 7,0;"SETTINGS OF """;N$;"""";'
                ''"1 CHANNEL TYPE","TB"(T)'
                '"2 BAUDRATE",B,'
                '"3 BORDER FLASH ",F'
                '"4 OPEN #3;""";"TB"(T);""": STOP "'
                '"5 SAVE """;N$;"""CODE ";C;",";L'
                '"6 SAVE """;M$;""" LINE 70";#0;"?": BEEP .3,5

   4 LET K=CODE INKEY$-CODE "0": IF K<1 OR K>6 THEN GO TO 4
   5 INPUT ;: GO SUB K*10: GO TO 1: REM All credits: 090992 EdW

  10 POKE C+2,CODE "BT"(T): RETURN 
  20 INPUT "2)",B: RANDOMIZE CF/26/B-2: POKE C+6,PEEK 23670: POKE C+7,PEEK 23671: RETURN 
  30 POKE C+8,1+F AND F<7: RETURN 
  40 RANDOMIZE USR C: CLEAR : STOP 
  50 SAVE N$CODE C,L: RUN 
  60 SAVE M$ LINE 70: RUN 
  70 CLEAR 65199: LET N$="Cserio2": LOAD N$CODE 65200: RUN 
