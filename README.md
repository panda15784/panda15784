stepper motor

#include <reg51.h>

void delay();
sbit SW = P0^1;

void main() {
    while(1) {
        if(SW == 1) {
            P1 = 0x08;
            delay();
            P1 = 0x04;
            delay();
            P1 = 0x02;
            delay();
            P1 = 0x01;
            delay();
        } else {
            P1 = 0x01;
            delay();
            P1 = 0x02;
            delay();
            P1 = 0x04;
            delay();
            P1 = 0x08;
            delay();
        }
    }
}

void delay() {
    TMOD = 0x01;
    TL0 = 0x00;
    TH0 = 0xDC;
    TR0 = 1;
    while(TF0 == 0);
    TR0 = 0;
    TF0 = 0;
}








square wave

#include <p18f4550.h>
#include <stdlib.h>

void timer_isr(void);

extern void _startup (void);
#pragma code _RESET_INTERRUPT_VECTOR = 0x1000
void _reset (void)
{
    _asm goto _startup _endasm
}
#pragma code
#pragma code _HIGH_INTERRUPT_VECTOR = 0x1008
void high_ISR (void)
{
    _asm goto timer_isr _endasm
}
#pragma code
#pragma code _LOW_INTERRUPT_VECTOR = 0x1018
void low_ISR (void)
{
}
#pragma code
#pragma interrupt timer_isr
void timer_isr(void)
{
    TMR0H = 0XFF;
    TMR0L = 0X00;
    PORTB = ~PORTB;
    INTCONbits.TMR0IF = 0;
}
void main()
{   
    ADCON1 = 0x0F;
    TRISB = 0;
    PORTB = 0xFF;
    T0CON = 0x00;
    TMR0H = 0xFF;
    TMR0L = 0x00;
    INTCONbits.TMR0IF = 0;
    INTCONbits.TMR0IE = 1;
    T0CONbits.TMR0ON = 1;
    INTCONbits.GIE = 1;

    while(1);
}










serial port

#include <p18f4550.h>

#pragma udata
#pragma idata
unsigned char String[] = {"WELCOME\n\rPress any key from PC\n\r"};
unsigned char String1[] = {"\n\rUART Tested \n\r"};

extern void _startup(void);
void High_ISR(void);
void Low_ISR(void);

extern void _startup(void);
#pragma code _RESET_INTERRUPT_VECTOR = 0x1000
void _reset(void)
{
    _asm goto _startup _endasm
}
#pragma code _HIGH_INTERRUPT_VECTOR = 0x1008
void _high_ISR(void)
{
}
#pragma code _LOW_INTERRUPT_VECTOR = 0x1018
void _low_ISR(void)
{
}
#pragma code

void TXbyte(char data)
{
    while (TXSTAbits.TRMT == 0);
    TXREG = data;
}
void main()
{
    unsigned char temp;
    unsigned char i = 0;
    SSPCON1 = 0;
    TRISCbits.TRISC7 = 1;
    TRISCbits.TRISC6 = 0;
    SPBRG = 0x71;
    SPBRGH = 0x02;
    TXSTA = 0x24;
    RCSTA = 0x90;
    BAUDCON = 0x88;
    temp = RCREG;
    temp = RCREG;
    
    for (i = 0; String[i] != '\0'; i++)
    {
        TXbyte(String[i]);
    }
    while (PIR1bits.RCIF == 0);
    
    for (i = 0; String1[i] != '\0'; i++)
    {
        TXbyte(String1[i]);
    }
    while (1);
}








memory transfer

#include <reg51.h>

void main(void) {
    unsigned char array[8] = {0x11, 0x22, 0x33, 0x44, 0x55, 0x66, 0x77, 0x88};
    unsigned char newarray[8];
    int i;
for(i = 0; i < 8; i++) 
{
        newarray[i] = array[i];
    }
    while(1);
}









  led buzzer


  #include <p18f4550.h>

extern void _startup (void);
#pragma code _RESET_INTERRUPT_VECTOR = 0x1000

void _reset (void)
{
    _asm goto _startup _endasm
}

#pragma code
#pragma code _HIGH_INTERRUPT_VECTOR = 0x1008
void high_ISR (void)
{
}

#pragma code
#pragma code _LOW_INTERRUPT_VECTOR = 0x1018
void low_ISR (void)
{
}
#pragma code

void MsDelay (unsigned int time)
{
    unsigned int i, j;
    for (i = 0; i < time; i++)
        for (j = 0; j < 700; j++);
}
#define lrbit PORTBbits.RB0
#define rlbit PORTBbits.RB1
#define buzzer PORTCbits.RC0
#define relay PORTCbits.RC1

void main()
{
    unsigned char val = 0;
    unsigned int k;
    INTCON2bits.RBPU = 0; 
    ADCON1 = 0x0F;

    TRISBbits.TRISB0 = 1;
    TRISBbits.TRISB1 = 1;
    TRISCbits.TRISC0 = 0;
    TRISCbits.TRISC1 = 0;
    
    TRISB = 0x03;
    PORTB = 0x10;
    buzzer = 0;
    relay = 0;

    while (1)
    {
        if (!(lrbit))
            val = 1;
        if (!(rlbit))
            val = 0;
    
        if (val)
        {
            buzzer = 1;
            relay = 1;
            LATB = LATB >> 1;
            MsDelay(250);
            if (LATB == 0x10 || LATB == 0x00)
            {
                LATB = 0x80;
                MsDelay(250);
            }
        }
        else
        {
            buzzer = 0;
            relay = 0;
            LATB = LATB << 1;
            MsDelay(250);
            if (LATB == 0x80 || LATB == 0x00)
            {
                LATB = 0x10;
                MsDelay(250);
            }
        }
    }
}











lcd display


#include <p18f4550.h>

#define LCD_DATA  PORTD
#define ctrl      PORTE
#define en        PORTEbits.RE2
#define rw        PORTEbits.RE1
#define rs        PORTEbits.RE0
#define BUSY      PORTDbits.RD7

void LCD_BUSY(void);
void LCD_cmd(unsigned char cmd);
void init_LCD(void);
void LCD_write(unsigned char data);
void LCD_write_string(static char *str);

extern void _startup (void);
#pragma code _RESET_INTERRUPT_VECTOR = 0x1000

void _reset (void)
{
    _asm goto _startup _endasm
}

#pragma code
#pragma code _HIGH_INTERRUPT_VECTOR = 0x1008
void high_ISR (void)
{
}

#pragma code
#pragma code _LOW_INTERRUPT_VECTOR = 0x1018
void low_ISR (void)
{
}
#pragma code

void myMsDelay (unsigned int time)
{
    unsigned int i, j;
    for (i = 0; i < time; i++)
        for (j = 0; j < 710; j++);
}

void init_LCD(void)
{
    LCD_cmd(0x38);      
    myMsDelay(15);

    LCD_cmd(0x01);      
    myMsDelay(15);

    LCD_cmd(0x0C);      
    myMsDelay(15);

    LCD_cmd(0x80);     
    myMsDelay(15);
    return;
}

void LCD_cmd(unsigned char cmd)
{
    LCD_DATA = cmd;
    rs = 0;
    rw = 0;
    en = 1;
    myMsDelay(15);
    en = 0;
    myMsDelay(15);
    return;
}

void LCD_write(unsigned char data)
{
    LCD_DATA = data;
    rs = 1;
    rw = 0;
    en = 1;
    myMsDelay(15);
    en = 0;
    myMsDelay(15);
    return;
}

void LCD_write_string(static char *str) 
{
    int i = 0;
    while (str[i] != 0)
    {
        LCD_write(str[i]);      
        myMsDelay(15);
        i++;
    }
    return;
}

void main(void)
{     
    char var1[] = "WELCOME";
    ADCON1 = 0x0F;        
    TRISD = 0x00;        
    TRISE = 0x00;         
    
    init_LCD();           
    myMsDelay(50);       
    LCD_write_string(var1);
    while (1);
}









dc motor


#include <p18f4550.h>
void myMsDelay(unsigned int time);
extern void _startup(void);
#pragma code _RESET_INTERRUPT_VECTOR = 0x1000
void _reset(void)
{
    _asm goto _startup _endasm
}
#pragma code
#pragma code _HIGH_INTERRUPT_VECTOR = 0x1008
void _high_ISR(void)
{
}
#pragma code _LOW_INTERRUPT_VECTOR = 0x1018
void _low_ISR(void)
{
}
#pragma code
void main()
{
    TRISCbits.TRISC2 = 0;
    TRISCbits.TRISC6 = 0;
    TRISCbits.TRISC7 = 0;
    PR2 = 0x4A;
    CCPR1L = 0x12;
    CCP1CON = 0x3C;
    T2CON = 0x07;
    PORTCbits.RC6 = 1;
    PORTCbits.RC7 = 0;
    while (1)
    {
        PR2 = 0x00;
        myMsDelay(1000);
        PR2 = 0x3F;
        myMsDelay(1000);
        PR2 = 0xBF;
        myMsDelay(1000);
        PR2 = 0xFF;
        myMsDelay(1000);
    }
}
void myMsDelay(unsigned int time)
{
    unsigned int i, j;
    for (i = 0; i < time; i++)
        for (j = 0; j < 710; j++);
}















led blinking


#include<reg51.h>
void delay(int count)
{
	int i,j;
	for(i=0;i<count;i++)
{
		for(j=0;j<200;j++)
{
			
		}
	}
}
void main(){
	while(1){
		P0=0XFF;
		delay(250);
		P0=0X00;
		delay(250);
		
	}
	
}
