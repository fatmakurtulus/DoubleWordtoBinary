name "fileio"

; general rules for file system emulation:

; 1. the emulator emulates all drive paths in c:\emu8086\vdrive\
;    for example: the real path for "c:\test1" is "c:\emu8086\vdrive\c\test1"

; 2. paths without drive letter are emulated to c:\emu8086\MyBuild\
;    for example: the real path for "myfile.txt" is "c:\emu8086\MyBuild\myfile.txt"

; 3. if compiled file is running outside of the emulator rules 1 and 2 do not apply.

; ==================================================================================
; run this example slowly in step-by-step mode and observe what it does.
; ==================================================================================

org  100h

jmp start

nl db 0AH,0DH
nl_size = $ - offset nl

file1 db "c:\test1\file1.txt", 0

handle dw ?


zero db "0"
one  db "1" 

array dd 1234h, 5454h,2324h
array_size =  $ - offset array


start:
mov ax, cs
mov dx, ax
mov es, ax



; create and open file: c:\emu8086\vdrive\C\test1\file1.txt
mov ah, 3ch
mov cx, 0
mov dx, offset file1
int 21h

mov handle, ax      ; handle contains the file handler or descriptor
; write to file:  

  
call    writeArray     


 
 
 


; close the file
mov ah, 3eh
mov bx, handle
int 21h
err2:
nop





ret  

    
    

writeArray:    
    push    si             ; need to preserve esi

    mov     si, offset Array
    mov     cx,  Array_Size - 1  
    
    
 

  

ShowIt:
    mov     bl, [si]        ; get the current byte from memory
       
    
    push cx 
      
      
    mov cx,8
    write:
    
    test bl, 10000000b   ; test a single bit, whether it is zero or one
    jz szero
    
     
   
    ; write to file 1:
    
    push ax
    push bx
    push cx
    push dx
    mov ah, 40h
    mov bx, handle
    mov dx, offset one    ; wrting one
    mov cx, 1
    int 21h 
    pop dx
    pop cx
    pop bx
    pop ax
           
    jmp write_end
    szero:
         
    ; write to file 0: 
    push ax
    push bx
    push cx
    push dx
    mov ah, 40h
    mov bx, handle
    mov dx, offset zero    ; wrting zero
    mov cx, 1
    int 21h
    pop dx
    pop cx
    pop bx
    pop ax  
     
        
          
    write_end:
       shl bl, 1 
       cmp cx,5   ; check if 4 bits completed
       jne loop_end   
       
       ; writing new line
       push ax
        push bx
        push cx
        push dx
        mov ah, 40h
        mov bx, handle
        mov dx, offset nl    ; writing new line
        mov cx, nl_size
        int 21h
        pop dx
        pop cx
        pop bx
        pop ax  
       
       loop_end:
       loop write
    
    
    pop cx 
    
    ; wrting new line after full byte 
    
    push ax
    push bx
    push cx
    push dx
    mov ah, 40h
    mov bx, handle
    mov dx, offset nl
    mov cx, nl_size
    int 21h
    pop dx
    pop cx
    pop bx
    pop ax  
      
    inc si          ; increment the index si
    dec     cx
    jns     ShowIt           ; is ecx ! neg repeat loop
                                                        
                                                        
                                                        
    pop     si
ret
  

