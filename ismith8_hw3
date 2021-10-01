#############################################################
# NOTE: this is the provided TEMPLATE as your required 
#		starting point of HW3 MIPS programming part.
#		This is the only file you should change and submit.
#
# CS465-DL1 S2021
# HW3 
#############################################################

#############################################################
# PUT YOUR TEAM INFO HERE
# Isaac Smith
# G01054048
#############################################################


#############################################################
# Data segment
#############################################################

.data # Start of Data Items
	INIT_INPUT: .asciiz "How many instructions to process? "
	INSTR_SEQUENCE: .asciiz "Please input instruction sequence (one per line)"
	NEWLINE: .asciiz "\nNext instruction: "
	ERROR_MSG: .asciiz  "\nAn error has occured, exiting program"
	CMP_MSG: .asciiz "\nCompleted execution"
	
	# Used for print output
	OUTPUT_Line1: .asciiz ":\nDependences: "
	OUTPUT_Line2: .asciiz "\nStall Cycles: "
	OUTPUT_Line3: .asciiz "\n-------------------------------------------"
	
	NONE: .asciiz "None"
	I: .asciiz ", I"
	FIRST_INSTR: .asciiz "\n\nI0:\nDependences: None\nStall Cycles: 0"
	END_PARTH: .asciiz "), "

	.align 4
	INPUT: .space 9
	
	
	

.text
main:
	la $a0, INIT_INPUT
	li $v0, 4
	syscall # Print out message asking for N (number of instructions to process)
	
	li $v0, 5
	syscall # read in Int 
	
	# saves num inputs
	add $s1, $zero, $v0
	add $t6, $s1, $zero

	
	#####################################
	# Note:     	                    #
	#                                   #
	# My methodology for this is        #
	# to use a 2d array for storing     #
	# the pieces of instruction.        #
	#                                   #
	# [ instr_num, dest, src1, src2 ]   #
	#                                   #
	# instr_num table:                  #
	#    add -> 1     lw -> 5           #
	#    addi -> 2    sw -> 6           #
	#    sll -> 3     bne -> 7          #
	#    slt -> 4     jr -> 8           #
	#                                   #
	#####################################
	
	# create 2D array of 4 byte ints
	addi $t1, $zero, 16
	multu $t6, $t1
	mflo $t0
	move $a0, $t0
	li $v0, 9
	syscall
	
	# save array
	move $s0, $v0
	
	# load address pointer
	la $t7, ($s0)
	
	# Print
	la $a0, INSTR_SEQUENCE
	li $v0, 4
	syscall 
	
	Loop: # Read in N strings
		la $a0, NEWLINE # Print out prompt for next instruction
		li $v0, 4
		syscall 												

		la $a0, INPUT
		li $a1, 9
		li $v0, 8
		syscall # read in one string and store in INPUT


		###########################################
		# Process the input                       #
		###########################################
	
		# prepare for function call
		la $a0, INPUT
		jal hexToInt
		
		move $t0, $v0
		
		# bit mask to isolate, 1111 1100
		andi $t1, $t0, 0xfc000000
	
		# shift to make math easier
		srl $t1, $t1, 26
		
		# check format
		beqz $t1, RFormat
		bgt $t1, 3, IFormat
		
		j Error
		
		# I format storage
		IFormat:
			# check value, jump according
			beq $t1, 35, LW
			beq $t1, 43, SW
			beq $t1, 5, BNE
			beq $t1, 8, ADDI
		
			# error handling
			j Error
			
			# Assigns value to array[i][0]
			LW: 
				li $t2, 5
				sw $t2, 0($t7)
				j IRegs
			SW: 	
				li $t2, 4
				sw $t2, ($t7)
				j IRegs
			BNE: 
				li $t2, 3
				sw $t2, ($t7)
				j IRegs
			ADDI: 
				li $t2, 4
				sw $t2, ($t7)
				j IRegs
				
				
			# Set registers
			IRegs:
				# DEST
				# bit mask
				andi $t1, $t0, 0x001f0000
				
				# adjust to calculate numeric
				srl $t1, $t1, 16
				
				# Handle error
				bgt $t1, 31, Error
				
				# increment, save to array[i][1]
				addi $t7, $t7, 4
				sw $t1, ($t7)

				# SRC 1
				# bit mask
				andi $t1, $t0, 0x03e00000
				
				# adjust to calculate numeric
				srl $t1, $t1, 21
				
				# Handle error
				bgt $t1, 31, Error
				
				# increment, save to array[i][2]
				addi $t7, $t7, 4
				sw $t1, ($t7)
				
				# SRC 2
				# increment, save to array [i][3]
				addi $t7, $t7, 4
				sw $zero, ($t7)
				
				j Iterate
		
		
		# R format storage
		RFormat:
			# bit mask isolate funct
			andi $t1, $t0, 0x3f
			
			# check value, jump according
			beq $t1, 32, ADD
			beq $t1, 0, SLL
			beq $t1, 42, SLT
			beq $t1, 8, JR
			
			# error handling
			j Error
			
			# Assigns value to array[i][0] based on 
			# num instruction steps in datapath
			ADD:
				li $t2, 4
				sw $t2, ($t7)
				j RRegs
			SLL:
				li $t2, 4
				sw $t2, ($t7)
				j RRegs
			SLT:
				li $t2, 4
				sw $t2, ($t7)
				j RRegs
			JR:
				li $t2, 1
				sw $t2, ($t7)
				j RRegs
		
		
			# Set registers
			RRegs:
				# DEST
				# bit mask
				andi $t1, $t0, 0x0000f800
				
				# adjust to calculate numeric
				srl $t1, $t1, 11
				
				# Handle error
				bgt $t1, 31, Error
				
				# increment, save to array[i][1]
				addi $t7, $t7, 4
				sw $t1, ($t7)

				# SRC 1
				# bit mask
				andi $t1, $t0, 0x03e00000
				
				# adjust to calculate numeric
				srl $t1, $t1, 21
				
				# Handle error
				bgt $t1, 31, Error
				
				# increment, save to array[i][2]
				addi $t7, $t7, 4
				sw $t1, ($t7)
				
				# SRC 2
				# bit mask
				andi $t1, $t0, 0x001f0000
				
				# adjust to calculate numeric
				srl $t1, $t1, 16
				
				# Handle error
				bgt $t1, 31, Error
				
				# increment, save to array[i][2]
				addi $t7, $t7, 4
				sw $t1, ($t7)
				
				j Iterate
			
			
		# Handles iteration
		Iterate:
			# increment
			sub $t6, $t6, 1
			
			bgt $t6, 0, ItrArr
			
			ItrArr:
				addi $t7, $t7, 4
			
			# break when i = 0
			beqz $t6, Dependence
			
			j Loop


	# Finds dependences, prints information
	# Loops forward through the array [i] and
	# then backwards through the instructions
	# to find dependences
	Dependence:
		
		# load pointer
		la $t7, ($s0)
		
		# Outer counter, i
		li $t0, 0
		
		# Print new line
		li $a0, '\n'
		li $v0, 11
		syscall
		
		# Loops through rows of array 
		OuterLoop:
			
			# Print
			li $a0, '\n'
			la $v0, 11
			syscall
			
			li $a0, 'I'
			li $v0, 11
			syscall
			
			# print int
			add $a0, $t0, $zero
			li $v0, 1
			syscall
			
			# Print line 2
			la $a0, OUTPUT_Line1
			li $v0, 4
			syscall
			
			# get next x instructions to cycle back through
			lw $t1, 0($t7)
			subi $t1, $t1, 1

			# set dependence none flag to 0
			add, $t4, $zero, $zero

			# Exits loop on count == num lines
			beq $t0, $s1, Exit
			
			
			
			# Counter E
			subi $t6, $t0, 1
			#add $t6, $zero, $t0
			
			la $t5, ($t7)
			
			# counter for stall cycles
			add $t8, $zero, $zero
			
			
			# Skips on first outer loop iteration
			bne $t0,  $zero, InnerLoop
			
			j OuterLoopIterate
			
			# Goes backwards compared to Outerloop
			InnerLoop:
			
				# goes back one [i]
				subi $t5, $t5, 16
				
				# Comparison
				lw $t2, 4($t5)
				
				# get SRC 1
				lw $t3, 8($t7)
				

				bne $t2, $t3, SRC2
				
					# tally total cycles (i - e) - num_instr - 1
					sub $t9, $t0, $t6
					abs $t9, $t9
					sub $t9, $t9, $t1
					abs $t9, $t9
					add $t8, $t9, $t8
					
					
					# SET FLAG
					addi $t4, $t4, 1
				
					# PRINT DEPENDENCE
					li $a0, '('
					li $v0, 11
					syscall
					
					add $a0, $zero, $t2
					li $v0, 1
					syscall
							
					la $a0, I
					li $v0, 4
					syscall	
						
					add $a0, $t6, $zero
					li $v0, 1
					syscall
					
					la $a0, I
					li $v0, 4
					syscall	
						
					add $a0, $t0, $zero
					li $v0, 1
					syscall
						
					la $a0, END_PARTH
					li $v0, 4
					syscall
				
				SRC2:
								
					# get SRC 2
					lw $t3, 12($t7)
					bne $t2, $t3, InnerLoopIterate
					
					# tally total cycles (i - e) - num_instr - 1
					sub $t9, $t0, $t6
					abs $t9, $t9
					sub $t9, $t9, $t1
					abs $t9, $t9
					add $t8, $t9, $t8
					
					# SET FLAG
					addi $t4, $t4, 1
					
					# PRINT DEPENDENCE
					li $a0, '('
					li $v0, 11
					syscall
					
					add $a0, $zero, $t2
					li $v0, 1
					syscall
							
					la $a0, I
					li $v0, 4
					syscall	
						
					add $a0, $t6, $zero
					li $v0, 1
					syscall
					
					la $a0, I
					li $v0, 4
					syscall	
						
					add $a0, $t0, $zero
					li $v0, 1
					syscall
						
					la $a0, END_PARTH
					li $v0, 4
					syscall
				
				InnerLoopIterate: 
					# break if e == 0
					beqz $t6, OuterLoopIterate
					
					# decrement
					subi $t6, $t6, 1
					#subi $t1, $t1, 1

					j InnerLoop

			OuterLoopIterate:
			
				# If no dependence, print None
				beq $t4, $zero, NoDependence
				
				J ContinueIterate
					
				NoDependence:
					la $a0, NONE
					li $v0, 4
					syscall
					
				
					
				ContinueIterate: 
					# Print Stall Cycle Count
					la $a0, OUTPUT_Line2
					li $v0, 4
					syscall
				
					add $a0, $t8, $zero
					li $v0, 1
					syscall
			
					# Print line
					la $a0, OUTPUT_Line3
					li $v0, 4
					syscall
			
					li $t6, 0
					li $t1, 0
			
					# iterate [i]
					addi $t7, $t7, 16
					addi $t0, $t0, 1
			
					# while not equal, continue loop
					bne $t0, $s1, OuterLoop
	
		

		j Exit


	Exit:
		# Print compltion message
		la $a0, CMP_MSG
		li $v0, 4
		syscall
		
		# Exit
		li $v0, 10
		syscall		

	Error:
		# Print error message
		la $a0, ERROR_MSG
		li $v0, 4
		syscall
		
		# Exit
		li $v0, 10
		syscall
	

###########################################
# Converts hex to decimal value
###########################################

hexToInt:

	# move args
	la $t0, ($a0)
	
	# result stored
	li $t4, 0
	
	# zeroth byte
	lbu $t1, 0($t0)
	subi $t2, $t1, 48
	slti $t3, $t2, 10
	beqz $t3, L0A
	
	# 16 ^ 7
	li $t3, 268435456
	mult $t2, $t3
	mflo $t2
	addu $t4, $t4, $t2
	
	L1:
		# first byte
		lbu $t1, 1($t0)
		subi $t2, $t1, 48
		slti $t3, $t2, 10
		beqz $t3, L1A
		
		# 16 ^ 6
		li $t3, 16777216
		mult $t2, $t3
		mflo $t2
		addu $t4, $t4, $t2
		
	L2:
		# second byte
		lbu $t1, 2($t0)
		subi $t2, $t1, 48
		slti $t3, $t2, 10
		beqz $t3, L2A
		
		# 16 ^ 5
		li $t3, 1048576
		mult $t2, $t3
		mflo $t2
		addu $t4, $t4, $t2
		
	L3:
		# third byte
		lbu $t1, 3($t0)
		subi $t2, $t1, 48
		slti $t3, $t2, 10
		beqz $t3, L3A
		
		# 16 ^ 4
		li $t3, 65536
		mult $t2, $t3
		mflo $t2
		addu $t4, $t4, $t2
		
	L4:
		# fourth byte
		lbu $t1, 4($t0)
		subi $t2, $t1, 48
		slti $t3, $t2, 10
		beqz $t3, L4A
		
		# 16 ^ 3
		li $t3, 4096
		mult $t2, $t3
		mflo $t2
		addu $t4, $t4, $t2
		
	L5:
		# fifth byte
		lbu $t1, 5($t0)
		subi $t2, $t1, 48
		slti $t3, $t2, 10
		beqz $t3, L5A
		
		# 16 ^ 2
		li $t3, 256
		mult $t2, $t3
		mflo $t2
		addu $t4, $t4, $t2
		
	L6:
		# sixth byte
		lbu $t1, 6($t0)
		subi $t2, $t1, 48
		slti $t3, $t2, 10
		beqz $t3, L6A
		
		# 16 ^ 1
		li $t3, 16
		mult $t2, $t3
		mflo $t2
		addu $t4, $t4, $t2
	L7:
		#seventh byte
		lbu $t1, 7($t0)
		subi $t2, $t1, 48
		slti $t3, $t2, 10
		beqz $t3, L7A
		
		# 16 ^ 0
		addu $t4, $t4, $t2
		
		j L8
		
	# HANLDES IF A - F
	
	# zeroth byte
	L0A:
		subi $t2, $t2, 7
		# 16 ^ 7
		li $t3, 268435456
		mult $t2, $t3
		mflo $t2
		addu $t4, $t4, $t2
		
		j L1
		
	# first byte
	L1A:
		subi $t2, $t2, 7
		
		# 16 ^ 6
		li $t3, 16777216
		mult $t2, $t3
		mflo $t2
		addu $t4, $t4, $t2
		
		j L2
	
	# second byte
	L2A:
		subi $t2, $t2, 7
		
		# 16 ^ 5
		li $t3, 1048576
		mult $t2, $t3
		mflo $t2
		addu $t4, $t4, $t2
		
		j L3
		
	# third byte
	L3A:
		subi $t2, $t2, 7
		
		# 16 ^ 4
		li $t3, 65536
		mult $t2, $t3
		mflo $t2
		addu $t4, $t4, $t2
		
		j L4
	
	# fourth byte
	L4A:
		subi $t2, $t2, 7
		
		# 16 ^ 3
		li $t3, 4096
		mult $t2, $t3
		mflo $t2
		addu $t4, $t4, $t2
		
		j L5
	
	# fifth byte
	L5A:
		subi $t2, $t2, 7
		
		# 16 ^ 2
		li $t3, 256
		mult $t2, $t3
		mflo $t2
		addu $t4, $t4, $t2
		
		j L6
		
	# sixth byte
	L6A:
		subi $t2, $t2, 7
		
		# 16 ^ 1
		li $t3, 16
		mult $t2, $t3
		mflo $t2
		addu $t4, $t4, $t2
		j L7
		
	# seventh byte
	L7A:
		subi $t2, $t2, 7
		
		# 16 ^ 0
		addu $t4, $t4, $t2
		j L8
	
	# after computation 	
	L8:
		move $v0, $t4
		jr $ra
		

	
	

	


