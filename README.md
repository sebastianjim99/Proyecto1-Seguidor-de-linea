# Proyecto1-Seguidor-de-linea
el siguiente proyecto contiene el código en assemblea de un robot seguidor de lineal  con 5 sensores para un PIC16F877A 
;-----------------------------------------------------------------------------
;   		        ______         _                  
;                      (_____ \       | |             _   
;                       _____) ) ___  | |__    ___  _| |_ 
;                      |  __  / / _ \ |  _ \  / _ \(_   _)
;                      | |  \ \| |_| || |_) )| |_| | | |_ 
;                      |_|   |_|\___/ |____/  \___/   \__)
;                                                         
;       ______                       _      _                      _        
;      / _____)                     (_)    | |                    | |       
;     ( (____   _____   ____  _   _  _   __| |  ___    ____     __| | _____ 
;      \____ \ | ___ | / _  || | | || | / _  | / _ \  / ___)   / _  || ___ |
;      _____) )| ____|( (_| || |_| || |( (_| || |_| || |      ( (_| || ____|
;     (______/ |_____) \___ ||____/ |_| \____| \___/ |_|       \____||_____)
;                     (_____|                                               
;                           _   _                      
;                          | | (_)                     
;                          | |  _  ____   _____  _____ 
;                          | | | ||  _ \ | ___ |(____ |
;                          | | | || | | || ____|/ ___ |
;                           \_)|_||_| |_||_____)\_____|
;______________________________________________________________________________

;Archivo: Seguidor_de_linea.s
;Fecha de creaciòn: 06/03/21    
;Autores:
;	JUAN SEBASTIAN JIMENEZ PEÑA     --2420192018
;	JUAN FELIPE BETANCOURTH LEDEZMA --2420191018
;Dispositivo: PIC16F877A
;File Version: XC8, PIC-as 2.31 
;Descripciòn: El robot seguidor de línea tiene un sencillo propósito 
;	      el cual es seguir una trayectoria demarcada en una pista sin
;             salirse o perder el camino, haciendolo en el menor tiempo posible.
;______________________________________________________________________________
    
PROCESSOR 16F877A

#include <xc.inc>
    
    

;------------------------------------------------------------------------------
  
; Configuraciones (pag 144- datasheet)
CONFIG CP=OFF     ; Flash Program Memory
CONFIG DEBUG=OFF  ; Background debugger disabled
CONFIG WRT=OFF    ; Flash Program Memory Write
CONFIG CPD=OFF    ; Data EEPROM Memory Code Protection
CONFIG WDTE = OFF ; Watchdog Timer
CONFIG LVP=ON     ; Low voltage programming enabled, MCLR pin, MCLRE ignored
CONFIG FOSC=XT    ;Oscillator
CONFIG PWRTE=ON   ;Power-up Timer
CONFIG BOREN=OFF  ; Brown-out Reset
 
PSECT udata_bank0 ; Reserva una parte de la memoria RAM; para guardar los datos   
;------------------------------------------------------------------------------
max:
DS 1 ;reserve 1 byte for max

tmp:
DS 1 ;reserve 1 byte for tmp
PSECT resetVec,class=CODE,delta=2

resetVec:
    PAGESEL main ;jump to the main routine
    goto INISYS
   
PSECT code ; Aqui inicia el codigo
 
;CAMBIANDO DE BANCO
;Por defecto, estamos en el Banco 0

INISYS:
    
BCF STATUS, 6 ; RP1=0
BSF STATUS, 5 ; RP0=1
; 01---pasamos al Banco 1

;DEFINIENDO ENTRADAS Y SALIDAS
    
;Entradas
BSF TRISB, 0  ;PORT_BO <--Entrada S1
BSF TRISB, 1  ;PORT_B1 <--Entrada S2
BSF TRISB, 2  ;PORT_B2 <--Entrada S3
BSF TRISB, 3  ;PORT_B3 <--Entrada S4
BSF TRISB, 4  ;PORT_B4 <--Entrada S5
;Salidas
BCF TRISC, 0  ;PORT_C0 <--Salida MOTOR MI
BCF TRISC, 1  ;PORT_C1 <--Salida MOTOR MD 
BCF TRISC, 2  ;PORT_C2 <--Salida MOTOR MI2 
BCF TRISC, 3  ;PORT_C3 <--Salida MOTOR MD2 
BCF TRISC, 4  ;PORT_C4 <--Salida LED AMARILLO IZQUIERDO 
BCF TRISC, 5  ;PORT_C5 <--Salida LED AMARILLO DERECHO
BCF TRISC, 6  ;PORT_C6 <--Salida LED ROJO CENTRO
 
 ;REGRESANDO AL BANCO 0
 
 
BCF STATUS, 5 ; RP0=0

MOVF PORTB,0
    
 
;------------------------------------------------------------------------------
main:
 
;!S1 -> R21
    MOVF PORTB,0 ;000S10000
    ANDLW 0b00010000
    MOVWF 0x21
    RRF   0x21,1
    RRF   0x21,1
    RRF   0x21,1
    RRF   0x21,1
    COMF  0x21,1
    MOVF  0x21,0
    ANDLW 0b00000001
    MOVWF 0x21
    
    
;!S2  -> R22
    MOVF PORTB,0 ;0000S2000
    ANDLW 0b00001000
    MOVWF 0x22
    RRF   0x22,1
    RRF   0x22,1
    RRF   0x22,1
    COMF  0x22,1
    MOVF  0x22,0
    ANDLW 0b00000001
    MOVWF 0x22
    
    
 ;!S3  -> R23
    MOVF PORTB,0 ;00000S300
    ANDLW 0b00000100
    MOVWF 0x23
    RRF   0x23,1
    RRF   0x23,1
    COMF  0x23,1
    MOVF  0x23,0
    ANDLW 0b00000001
    MOVWF 0x23
    
    
 ;!S4  -> R24
    MOVF PORTB,0;000000S40
    ANDLW 0b00000010
    MOVWF 0x24
    RRF   0x24,1
    COMF  0x24,1
    MOVF  0x24,0
    ANDLW 0b00000001
    MOVWF 0x24
     
   
 ;!S5 -> R25
    MOVF PORTB,0;0000000S5
    ANDLW 0b00000001
    MOVWF 0x25
    COMF  0x25,1
    MOVF  0x25,0
    ANDLW 0b00000001
    MOVWF 0x25
    
  
;S1 -> R31
    MOVF PORTB,0 ;000S10000
    ANDLW 0b00010000
    MOVWF 0x31
    RRF   0x31,1
    RRF   0x31,1
    RRF   0x31,1
    RRF   0x31,1
    MOVF  0x31,0
    ANDLW 0b00000001
    MOVWF 0x31
    
    
;S2 -> R32
    MOVF PORTB,0; 0000S2000
    ANDLW 0b00001000
    MOVWF 0x32
    RRF   0x32,1
    RRF   0x32,1
    RRF   0x32,1
    MOVF  0x32,0
    ANDLW 0b00000001
    MOVWF 0x32
   
    
;S3 -> R33
    MOVF PORTB,0 ; 00000S300
    ANDLW 0b00000100
    MOVWF 0x33
    RRF   0x33,1
    RRF   0x33,1
    MOVF  0x33,0
    ANDLW 0b00000001
    MOVWF 0x33
    
;S4 -> R34
    MOVF PORTB,0 ;000000S40
    ANDLW 0b00000010
    MOVWF 0x34
    RRF   0x34,1
    MOVF  0x34,0
    ANDLW 0b00000001
    MOVWF 0x34
  
   
;S5 -> R35
    MOVF PORTB,0 ;0000000S5
    ANDLW 0b00000001
    MOVWF 0x35
    MOVF  0x35,0
    ANDLW 0b00000001
    MOVWF 0x35
   
 ;----------------------------------------------------------------------------
 ;OPERACION 
 ;k = karnaugh
 ;--------------------------------
 
 ;MI
 
 ;!S3 S5
 MOVF  0x23,0
 ANDWF 0x35,0
 MOVWF 0x40; Este es el primer dato del K1
 
 ;!S3 S4 
 MOVF  0x23,0
 ANDWF 0x34,0
 MOVWF 0x41; Este es el segundo dato del K1
 
 ;!S2 S3
 MOVF  0x22,0
 ANDWF 0x33,0
 MOVWF 0x42; Este es el Tercer dato del K1
 
 ;Se suman los registro 40+41+42
 MOVF  0x40,0
 IORWF 0x41,0
 IORWF 0x42,0
 MOVWF 0x43; Este es el RESULTADO del K1
 ;--------------------------------------------------
 
 ;MD
 
 ;S2 !S3
 MOVF  0x32,0
 ANDWF 0x23,0
 MOVWF 0x44; Este es el primer dato del K2
 
 ;S1 !S2
 MOVF  0x31,0
 ANDWF 0x22,0
 MOVWF 0x45; Este es el segundo dato del K2
 
 ;S3 !S4 
 MOVF  0x33,0
 ANDWF 0x24,0
 MOVWF 0x46; Este es el Tercer dato del K2
 
 
 ;Se suman los del registro 44+45+46
 MOVF  0x44,0
 IORWF 0x45,0
 IORWF 0x46,0
 MOVWF 0x48; Este es el RESULTADO del K2
 ;----------------------------------------
 ; MI2
 ;S1 !S3
 MOVF  0x31,0
 ANDWF 0x23,0
 MOVWF 0x49; Este es el primer dato del K3
 
 
 ;!S2 !S3 !S4 !S5
 MOVF  0x22,0
 ANDWF 0x23,0
 ANDWF 0x24,0
 ANDWF 0x25,0
 MOVWF 0x50; Este es el segundo dato del K3
 
 ;Se suman los registro 49+50
 MOVF  0x49,0
 IORWF 0x50,0
 MOVWF 0x51; Este es el RESULTADO del K3
 
 ;----------------------------------------
 
 ;MD2
 
 ; !S3 S5
 MOVF  0x23,0
 ANDWF 0x35,0
 MOVWF 0x53; Este es el primer dato del K4
 
  
 ;!S1 !S2 !S3 !S4
 MOVF  0x21,0
 ANDWF 0x22,0
 ANDWF 0x23,0
 ANDWF 0x24,0
 MOVWF 0x54; Este es el segundo dato del K4
 
 ;ahora la suma del registro 53+54
 MOVF  0x53,0
 IORWF 0x54,0
 MOVWF 0x55; Este es el RESULTADO del K4
 
 ;KARNAUGH LEDS DIRECCIONALES   
 
 ;LED IZQUIERDA
; !S1 S2 
 MOVF 0x21,0
 ANDWF 0x32,0
 MOVWF 0x56; Este es el primer dato de led-izq
 
; S1 !S3
 MOVF  0x31,0
 ANDWF 0x23,0
 MOVWF 0x57; Este es el segundo dato de led-izq
 
 ;Se suman los registro 56+57
 MOVF  0x56,0
 IORWF 0x57,0
 MOVWF 0x58   ;Este es el registro del RESULTADO para la salida del led-izq
 
 ;LED CENTRO
; S1 S3 
 MOVF  0x31,0
 ANDWF 0x33,0
 MOVWF 0x59   ;Este es el primer dato de led-centro
 
 ;!S1 !S2 !S3 !S4 !S5
 MOVF  0x21,0
 ANDWF 0x22,0
 ANDWF 0x23,0
 ANDWF 0x24,0
 ANDWF 0x25,0
 MOVWF 0x60   ;Este es el segundo dato de led-centro
 
  ;ahora la suma del registro 59+60
 MOVF  0x59,0
 IORWF 0x60,0
 MOVWF 0x61; Este es el registro del RESULTADO de la salida del led-centro
 
 ;LED DERECHO
; !S3 S5 
 MOVF  0x23,0
 ANDWF 0x35,0
 MOVWF 0x62; Este es el primer dato de led-derecho
 
; S4 !S5
 MOVF  0x34,0
 ANDWF 0x25,0
 MOVWF 0x63; Este es el segundo dato de led-derecho
 
 ;ahora la suma del registro 62+63
 MOVF  0x62,0
 IORWF 0x63,0
 MOVWF 0x64; Este es el registro del RESULTADO de la salida del led-derecha
 
  ;------------------
 ;ASIGNACION DE RESULTADOS AL PUERTO C 
 
  ;MI ROTACION
  ;REGISTRO 43 no fue modificado porque ya esta en el menos significativo 
  ;SALIDA 000000 0 R43 
 
  ;MD ROTACION
  MOVF  0x48,0 
  RLF   0x48,1
  MOVF  0x48,0
  MOVWF 0x65 ; SALIDA 0 0 0 0 0 0 R65 0 
 
  
  ;MI2 ROTACION
  MOVF  0x51,0
  RLF   0x51,1
  RLF   0x51,1
  MOVF  0x51,0
  MOVWF 0x66 ; SALIDA 0 0 0 0 0 R66 0 0 
  
  ;MD2 ROTACION
  MOVF  0x55,0
  RLF   0x55,1
  RLF   0x55,1
  RLF   0x55,1
  MOVF  0x55,0
  MOVWF 0x67 ; SALIDA 0 0 0 0 R67 0 0 0 
  
 ;LED-IZQUIERDO 
 MOVF  0x58,0
 RLF   0x58,1
 RLF   0x58,1
 RLF   0x58,1
 RLF   0x58,1
 MOVF  0x58,0
 MOVWF 0x68   ;0 0 0 R68 0 0 0 0
 
 
 
 
 ;LED-DERECHO 
 MOVF  0x64,0
 RLF   0x64,1
 RLF   0x64,1
 RLF   0x64,1
 RLF   0x64,1
 RLF   0x64,1
 MOVF  0x64,0
 MOVWF 0x69   ;0 0 R69 0 0 0 0 0
 
 
 ;LED-CENTRO 
 MOVF  0x61,0
 RLF   0x61,1
 RLF   0x61,1
 RLF   0x61,1
 RLF   0x61,1
 RLF   0x61,1
 RLF   0x61,1
 MOVF  0x61,0
 MOVWF 0x70   ;0 R70 0 0 0 0 0 0
 
;SUMA PARA LA SALIDA EN EL PUERTO C 
 MOVF  0x43,0
 IORWF 0x65,0
 IORWF 0x66,0
 IORWF 0x67,0
 IORWF 0x68,0
 IORWF 0x69,0
 IORWF 0x70,0
 MOVWF PORTC  ;RESULTADO SALIDA DEL PUERTO 
 
goto main  ;leer de nuevo 
 
END
