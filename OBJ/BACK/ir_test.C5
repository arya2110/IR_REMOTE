#include	"extern.h"
BYTE interrupt_flag = 0; 
BYTE DATA_KEY_1 = 0x1;    //Data For KEY_1 (MSB first)
BYTE DATA_KEY_2	= 0x5;			//Data For KEY_2 (MSB first)
BYTE DATA_KEY_3 = 0xD;			//Data For KEY_3 (MSB first)
BYTE DATA_KEY_4	= 0xB;			//Data For KEY_4 (MSB first)
BYTE DATA_KEY_5 = 0x3;			//Data For KEY_5 (MSB first)

BYTE address1 = 0x00;
void Delay_ms(BYTE ms_Count)
{
    word i=0;
	while(i<ms_Count)
	{
		.delay(4000);
		i++;
	}
}

void Delay_us(word ms_Count)
{
    word i=0;
	while(i<ms_Count)
	{
		.delay(1);
		i++;
	}
}

void Timer_Start(BYTE x);
void logic();
void sendByte(BYTE bytes);
void sendRepeate();
void sendFrame(BYTE address, BYTE command);

void FPPA0(void)
{
  .ADJUST_IC SYSCLK = IHRC/4, IHRC = 16MHz, VDD = 3.3V; // Set system clock frequenc

  BIT LED : PA.3;
  $ LED OUT;  // set PA3 as the output
  //38khz freq timer
  $ timer TM2 , 1 ,/300;
    INTEN.6 = 1; // Enable the TIMER16 interrupt

	  
        if(1)
		{
			sendFrame(address1,DATA_KEY_1);//send the frame
            while(DATA_KEY_1==1)
			{
				sendRepeate();
				DATA_KEY_1=0;
			}
		}
		
   while (1) 
   {
	 

	}
}

void Timer_Start(BYTE x) {
    INTRQ = 0; // Clear the INTRQ register
    if(x==1)
	{
      ENGINT;
	  
	}
	else
	{
      DISGINT;
	  led=1;
	}

}

void	Interrupt (void)
{  
	pushaf;
   if (Intrq.TM2)
    {
	   if(interrupt_flag==1)
	   {
		   led=1;
		   interrupt_flag=0;
	   }
	   else
	   {
           led=0;
		   interrupt_flag=1;
	   }

	  Intrq.TM2 = 0;

    }

	popaf;
}

void sendFrame(BYTE address, BYTE command) // this routine send the whole frame including 9ms leading pulse 4.5ms space address ~address command ~command end of message bit.
{	BYTE store;
	//TMR2 = 0x00;					//clear the TMR2 register before we start generating 38Khz Signal on the GPIO
	Timer_Start(1);		//put The CCP module into PWM mode , the Duty is 50% and freqeucny is 38Khz as allredy set, 
	//delay_ms(9)   //wait for ~9ms this rountine will delay for 8.999 ms + (3*500ns) = ~9ms (500ns is the instruction execution time at 8Mhz) and next instruciton will take 3 ins cycle to execute.
    .delay(25300);
	Timer_Start(0);		//turn off the CCP module stop generating 38Khz singal
	delay_ms(4);       //wait for ~4.5ms  the value 4490 is compensated with the next instrucitons execution timing , it helps to keep precise timing. as described avobe
	.delay(1950);
    
	sendByte(address);				//send address byte. (sendByte functions should not be called independently, only sendFrame should call it)
	sendByte(~address);				//send address logical invert 
	sendByte(command);             //send command
	sendByte(~command);		       //send command logical invert
    // sendByte(0);
	// sendByte(0xff);
	 //sendByte(0x80);
	 //sendByte(0x7F);

	//TMR2 = 0x00;					//clear the TMR2 register before we start generating 38Khz Signal on the GPIO
	Timer_Start(1);		//Start generating 38Khz singal
	//delay_us(561);				//wait for ~562.5us again value 561 is compensated with the next instrucitons timing
	// .delay(2000);
	.delay(1604);
	Timer_Start(0);	//stop generating 38Khz singal.
	delay_ms(39);// wait for the Data Frame time. 
    .delay(100);
}


void sendByte(BYTE bytes)	// this function is called only by the sendFrame , to send each byte of data total 4bytes.
{
	BYTE  i=0;				//variable to hold the counter vlaue
	BYTE mask;
    BYTE m;
	while(i<8)				//loop to send 8 individual bits.
	{	
		  mask=1;
          m = 0;
         while (m < i)
          {
		    mask <<= 1;  //mask=mask<<1
             m++;
          }
		//TMR2 = 0x00;				//clear the TMR2 register before we start generating 38Khz Signal on the GPIO
		Timer_Start(1);		//Start generating 38Khz singal
		//delay_us(561);   //wait for ~562.5us again value 561 is compensated with the next instrucitons timing
         //.delay(2204);
		.delay(1604);
		Timer_Start(0);			//stop generating 38Khz singal
			
		if(bytes & mask)				// as you have already noticed , this is example we send MSB first order. 
		{							// check for MSB bit if it is 1 then delay for 1.6875ms if it is zero then delay for 562.5us
		//delay_us(1686);         //delay for ~1.6875 ms again value 1686 is compensated with the next instrucitons timing
		 .delay(6650);
		 //.printf("bytes & 0x80 = %d\n",bytes);
		}
		else
		{
		//delay_us(558);    //wait for ~562.5us again value 558 is compensated with the next instrucitons timing
		 .delay(2280);
		 //.delay(1654);
		}
		//.printf("b = %d\n",bytes);
	//	bytes = bytes<<1;//get the next lsb into msb (shift left the byte)
		
		i++;
	}	
}

/*
void sendByte(BYTE bytes)	// this function is called only by the sendFrame , to send each byte of data total 4bytes.
{   
   word	temp=bytes;
   word tempcount=0;
	BYTE  i=8;				//variable to hold the counter vlaue
	 while(tempcount<8)
		 {
			 word mask = 1;
             word i = 0;
             while (i < tempcount)
            {
			   mask <<= 1;  //mask=mask<<1
               i++;
            }
             // if(temp & (1<<tempcount)) bit manupltion not work so use mask varible
            if(temp & mask)
			{   Timer_Start(1);
                led=1;
                //delay_us(561);
				.delay(1804);
				Timer_Start(0);	

	            led=0;
		        Delay_ms(1);

			}
			else
			{  Timer_Start(1);
                led=1;
				Timer_Start(0);
                .delay(1804);
	            led=0;
		        .delay(1804);
            }
            tempcount++;
			
		  }
}
*/
void sendRepeate()
{
	//TMR2 = 0x00;					//clear the TMR2 register before we start generating 38Khz Signal on the GPIO
	Timer_Start(1);			//Start generating 38Khz singal
	//delay_us(8999);				//wait for ~9ms 
	.delay(25500);
	Timer_Start(0);			//stop generating 38Khz singal
	delay_ms(2);				//wait for 2.25ms

	//TMR2 = 0x00;					//clear the TMR2 register before we start generating 38Khz Signal on the GPIO
	Timer_Start(1);			//Start generating 38Khz singal
	//delay_us(556);				//wait for ~562.5us
	.delay(2232);
	Timer_Start(0);		//stop generating 38Khz singal	
	//delay_us(96187);	//delay for 96.187 ms before sending the next repeate code
	delay_ms(96);
	
}