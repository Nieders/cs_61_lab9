
.orig x3000
	LD R4, BASE
	LD R5, MAX
	LD R6, TOS
	
	LEA R0, intro
	PUTS
	GETC
	OUT
	LD R3, SUB_STACK_PUSH
	JSRR R3
	
	LEA R0, intro2
	PUTS
	GETC
	OUT
	LD R3, SUB_STACK_PUSH
	
	JSRR R3
	LEA R0, intro3
	PUTS
	GETC
	OUT
	
	LD R3, SUB_RPN_ADD
	JSRR R3
	
	LD R3, SUB_STACK_PUSH
	JSRR R3
	
	LEA R0, answer
	PUTS
	LD R3, SUB_STACK_POP
	JSRR R3
	
halt
;-----------------------------------------------------------------------------------------------
; test harness local data:
BASE .FILL xA000
MAX .FILL xA005
TOS .FILL xA000
SUB_STACK_PUSH .FILL x3200
SUB_STACK_POP .FILL x3400
SUB_RPN_ADD .FILL x3600
intro .STRINGZ "Enter the first single digit numeric character:\n"
intro2 .STRINGZ "\nEnter the second digit numeric character:\n"
intro3 .STRINGZ "\nEnter the operation symbol:\n"
answer .STRINGZ "\n= "
;===============================================================================================
.end

; subroutines:
;------------------------------------------------------------------------------------------
; Subroutine: SUB_STACK_PUSH
; Parameter (R0): The value to push onto the stack
; Parameter (R4): BASE: A pointer to the base (one less than the lowest available
;                       address) of the stack
; Parameter (R5): MAX: The "highest" available address in the stack
; Parameter (R6): TOS (Top of Stack): A pointer to the current top of the stack
; Postcondition: The subroutine has pushed (R0) onto the stack (i.e to address TOS+1). 
;		    If the stack was already full (TOS = MAX), the subroutine has printed an
;		    overflow error message and terminated.
; Return Value: R6 ← updated TOS
;------------------------------------------------------------------------------------------
.orig x3200
    AND R2, R2, x0
	NOT R2, R6
	ADD R2, R2, #1
	ADD R2, R5, R2
	BRp continue_push
	LEA R0, OVERFLOW			 
	PUTS
	BRnzp exit_push
continue_push
    ADD R6, R6, #1
    STR R0, R6, #0
exit_push

ret
;-----------------------------------------------------------------------------------------------
; SUB_STACK_PUSH local data
OVERFLOW .STRINGZ "\nStack overflowed!!\n"


;===============================================================================================
.end


;------------------------------------------------------------------------------------------
; Subroutine: SUB_STACK_POP
; Parameter (R4): BASE: A pointer to the base (one less than the lowest available                      
;                       address) of the stack
; Parameter (R5): MAX: The "highest" available address in the stack
; Parameter (R6): TOS (Top of Stack): A pointer to the current top of the stack
; Postcondition: The subroutine has popped MEM[TOS] off of the stack.
;		    If the stack was already empty (TOS = BASE), the subroutine has printed
;                an underflow error message and terminated.
; Return Value: R0 ← value popped off the stack
;		   R6 ← updated TOS
;------------------------------------------------------------------------------------------
.orig x3400

LD R1, DEC_48
    AND R2, R2, x0
	NOT R2, R6
	ADD R2, R2, #1
	ADD R2, R4, R2
	BRn continue_pop
	LEA R0, UNDERFLOW			 
	PUTS
	BRnzp exit_pop
continue_pop
    LDR R0, R6, #0
    ADD R0, R0, R1
    OUT
    ADD R6, R6, #-1
exit_pop

ret
;-----------------------------------------------------------------------------------------------
; SUB_STACK_POP local data
UNDERFLOW .STRINGZ "\nStack underflowed!!\n"
DEC_48 .FILL #48
;===============================================================================================
.end

;------------------------------------------------------------------------------------------
; Subroutine: SUB_RPN_ADD
; Parameter (R4): BASE: A pointer to the base (one less than the lowest available
;                       address) of the stack
; Parameter (R5): MAX: The "highest" available address in the stack
; Parameter (R6): TOS (Top of Stack): A pointer to the current top of the stack
; Postcondition: The subroutine has popped off the top two values of the stack,
;		    added them together, and pushed the resulting value back
;		    onto the stack.
; Return Value: R6 ← updated TOS address
;------------------------------------------------------------------------------------------
.orig x3600
LD R3, HEX_30
NOT R3, R3
ADD R3, R3, #1

	LDR R1, R6, #0
	ADD R1, R3, R1
	ADD R6, R6, #-1
	LDR R2, R6, #0
	ADD R2, R3, R2
	AND R0, R0, x0
	ADD R0, R1, R2
	ADD R6, R6, #-1
				 
ret
;-----------------------------------------------------------------------------------------------
; SUB_RPN_MULTIPLY local data
HEX_30 .FILL x30

;===============================================================================================
.end

