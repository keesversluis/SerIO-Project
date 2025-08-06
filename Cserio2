©   * TORNADO *

; Device driver to work with ZX128K RS232 interface from BASIC,
; also in 48K mode.
; Everything runs through stream #3, the "P" channel.
; - LPRINT, PRINT #3, LLIST, LIST #3, INPUT #3 and INKEY$#3.
; - works with extended BASIC, eg. SAVE*#3, LOAD*"p" (Opus).
; Adjustable: - "t"/"b"-channel ("t" only for OUTPUT).
;             - baudrate timer
;             - border flash color

; (c) Kees Versluis
; 19 oktober 1993
; Revisited 02-07-2025 - 326 bytes

; This driver is optimized for the slightly higher clock freq.
; of the ZX128K. The TX en ZX loops take 3 T-states longer now.

           ORG  65200:DUMP60000

START      JR   INIT

STATUS     DEFB "b"
IF_NR      DEFB 2
LENGTE     DEFW EIND-START
TIMER      DEFW #000C
IOBRDR     DEFB #02
SER_FL     DEFB #00,#00

INIT       LD   HL,(23631)
           LD   DE,15
           ADD  HL,DE
           LD   DE,OUTBYTE
           LD   (HL),E
           INC  HL
           LD   (HL),D
           LD   DE,IN_BYTE
           INC  HL
           LD   (HL),E
           INC  HL
           LD   (HL),D
           XOR  A
           LD   (SER_FL),A
INIT2      LD   BC,#FFFD
           LD   A,#0E
           OUT  (C),A
           RET

OUTBYTE    LD   B,A
           LD   A,(STATUS)
           OR   %00100000
           CP   "t"
           LD   A,B
           JR   NZ,TXBYTE
FILTER     CP   #A5
           JP   NC,#0B52
           RES  0,(IY+1)
           CP   #7F
           JR   C,NOGRAF
           LD   A,"?"
NOGRAF     CP   #0D
           JR   NZ,NOT_CR
           CALL TXBYTE
           LD   A,#0A
           JR   TXBYTE
NOT_CR     CP   #20
           RET  C
           JR   NZ,TXBYTE
           SET  0,(IY+1)

TXBYTE     DI
;          CPL                    ; 1488 does the inversion
           LD   E,A
           LD   D,#0B             ; 11 bits - 8N2
           LD   A,(IOBRDR)
           OUT  (#FE),A
           CALL INIT2
TEST_CTS   CALL #1F54
           JP   NC,#054F          ; EI & BREAK-CONT repeats
           IN   A,(C)
           AND  #40               ; Resets Carry!
           JR   NZ,TEST_CTS
           LD   B,#BF
;          SCF                    ; CY=0 - 1488 inverts it to 1
SER_OUT    LD   A,%01101111
           RLA                    ; CY to bit 3
           RLCA
           RLCA
           RLCA
           OUT  (C),A
           LD   HL,(TIMER)
           DEC  HL
DELAY2     DEC  HL
           LD   A,H
           OR   L
           JR   NZ,DELAY2
           CP   #FF               ; SCF + 3 extra T-states
           RR   E                 ; CY into bit 7 of E
           DEC  D
           JP   NZ,SER_OUT
BORD_REST  LD   A,(23624)
           AND  #38
           RRCA
           RRCA
           RRCA
           OUT  (#FE),A
           EI
           RET

IN_BYTE    RES  3,(IY+2)
           BIT  5,(IY+55)
           JR   Z,RXBYTE
INPUT      LD   SP,(23613)
           POP  HL
           POP  HL
           LD   (23613),HL
INPUT2     CALL RXBYTE
           JR   NC,INPUT2
           CP   #0D
           RET  Z
           CALL #0F85
           JR   INPUT2

RXBYTE     LD   HL,SER_FL
           LD   A,(HL)
           AND  A
           JR   Z,REC_BYTE
           LD   (HL),#00
           INC  HL
           LD   A,(HL)
           SCF
           RET

REC_BYTE   DI
           CALL #1F54
           JP   NC,#054F          ; EI & BREAK-CONT repeats
           LD   A,(IOBRDR)
           OUT  (#FE),A
           LD   DE,(TIMER)
           LD   HL,250
           CALL INIT2
           LD   B,#BF
           LD   A,#FA
           OUT  (C),A
           CALL POLL
           PUSH AF
           ADD  HL,DE
DB_DELAY2  DEC  HL
           LD   A,H
           OR   L
           JR   NZ,DB_DELAY2
           ADD  HL,DE
           ADD  HL,DE
           ADD  HL,DE
           CALL POLL
           JR   NC,END_RS_IN
           LD   HL,SER_FL
           INC  (HL)
           INC  HL
           LD   (HL),A
END_RS_IN  CALL BORD_REST
           POP  AF
           RET

POLL       LD   B,#FF
POLL2      IN   A,(C)
           RLCA
           JR   C,POL_AGAIN
           IN   A,(C)
           RLCA
           JR   C,POL_AGAIN
           IN   A,(C)
           RLCA
           JR   C,POL_AGAIN
           IN   A,(C)
           RLCA
           JR   NC,STARTBIT
POL_AGAIN  DEC  HL
           LD   A,H
           OR   L
           JR   NZ,POLL2
           LD   B,#BF
           LD   A,#FE
           OUT  (C),A
           RET

STARTBIT   LD   H,D
           LD   L,E
           SRL  H
           RR   L
           DEC  HL
           DEC  HL
           DEC  HL
           LD   B,#80
SER_IN     ADD  HL,DE
           JP   BD_DELAY1         ; Takes 10 T-states
BD_DELAY1  DEC  HL
           LD   A,H
           OR   L
           JR   NZ,BD_DELAY1
           DEC  A                 ; A=#FF
           IN   A,(#FD)           ; So: IN  A,(#FFFD)
           RLCA
           RR   B
           JR   NC,SER_IN
           PUSH BC
           LD   B,#BF
           LD   A,#FE
           OUT  (C),A
           POP  AF
;          CPL                    ; 1489 did the inversion
           SCF
           RET

EIND       END
