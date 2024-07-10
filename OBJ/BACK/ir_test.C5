#include	"extern.h"

BYTE interrupt_flag = 0; 
BYTE DATA_KEY_1 = 0x1;    //Data For KEY_1 (MSB first)
BYTE DATA_KEY_2	= 0x9;			//Data For KEY_2 (MSB first)
BYTE DATA_KEY_3 = 0xD;			//Data For KEY_3 (MSB first)
BYTE DATA_KEY_4	= 0xB;			//Data For KEY_4 (MSB first)
BYTE DATA_KEY_5 = 0x3;			//Data For KEY_5 (MSB first)

BYTE address1 = 0x00;
BYTE intput_flag;
//WORD TIME36S = 2126800;
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
void DeepSleep_2s(void)
{
  WORD sleep = 0;
  STT16 sleep;

  INTRQ.T16 = 0;

  $ T16M = ILRC, /64, BIT11; // T16 clock source = ILRC (typ. 62 kHz on PMC150), / 64 => approx 1 kHz timer, 11BIT = 2048 => approx 2 seconds

  while( !INTRQ.T16 ) // STOPEXE could be interrupted by multiple sources, we wait for T16 overflow here
    

  $ T16M = STOP;
}
void Timer_Start(BYTE x);
void logic();
void sendByte(BYTE bytes);
void sendRepeate();
void sendFrame(BYTE address, BYTE command);

void FPPA0(void)
{
  .ADJUST_IC SYSCLK = IHRC/4, IHRC = 16MHz, VDD = 2.2V; // Set system clock frequenc
  
  BIT LED : PA.3;
  $ LED OUT;  // set PA3 as the output

    BIT KEY_1 :pa.6;
    BIT KEY_2 :pa.7;
    $  KEY_1       In;       //  configure Key_In as Input, Pull High
    $  KEY_2       In;       //  configure Key_In as Input, Pull High
    PAPH  = 0b_1100_0000; // Port A Pull-High Register: enable pull-up on PA.6 and PA.7
	PADIER = 0b_1111_1001; 
	//PAPH.6=1;
	//PAPH.7=1;
  //38khz freq timer
  $ timer TM2 , 1 ,/300;
    INTEN.6 = 1; // Enable the TIMER6 interrupt
  //  INTEN.0 = 1; // Enable the PA0 interrupt
//	INTEN.1 = 1; // Enable the PA4 interrupt
//	INTEGS = 0b_0000_0001; // Edge select register - PA0 rising edge to trigger
    //INTEGS = 0b_0000_0010;
    

 //   PADIER = 0; // disable pins as wakeup source
//	 PADIER.6 = 1; // Enable PA.6 as wake-up source
  //   PADIER.7 = 1; // Enable PA.7 as wake-up source
//	INTEN = 0;  // Make sure all interrupts are disabled
 //   INTRQ = 0;
  CLKMD = 0xF4; 
  CLKMD.4 = 0;

                                    CLKMD = 0xF4; // Change clock from IHRC to ILRC CLKMD.4 = 0; // disable IHRC … while (1) { STOPSYS; // enter power-down if (…) break; // if wakeup happen and check OK, then return to high speed, // else stay in power-down mode again } CLKMD = 0x34; // Change clock from ILRC to IHRC/2
  while (1) {
        // Enter Power-Down mode
        STOPSYS;

        // After waking up, re-enable IHRC and switch clock source back
        CLKMD = 0x14;  // Change clock from ILRC to IHRC/2

        // Small delay for debouncing
        .delay(1000);

        // Check if either key is pressed after waking up
        if (!KEY_1) {  // Check if the Key_1 is pressed (GPIO is low)
            // Send the frame for KEY_1
            sendFrame(address1, DATA_KEY_1);
            while (!KEY_1) {  // If the key is still pressed 
                sendRepeate();  // Send Repeate codes
            }
        } else if (!KEY_2) {  // Check if the Key_2 is pressed
            // Send the frame for KEY_2
            sendFrame(address1, DATA_KEY_2);
            while (!KEY_2) {  // If the key is still pressed 
                sendRepeate();  // Send Repeate codes
            }
        }

        // Disable IHRC before re-entering Power-Down mode
        CLKMD = 0xF4;  // Change clock from IHRC to ILRC
        CLKMD.4 = 0;   // Disable IHRC
    }
}

 // CLKMD = 0x14;

  CLKMD.4 = 0; CLKMD.4 = 0;CLKMD = 0xF4; // Change clock from IHRC to ILRC CLKMD.4 = 0; // disable IHRC … while (1) { STOPSYS; // enter power-down if (…) break; // if wakeup happen and check OK, then return to high speed, // else stay in power-down mode again } CLKMD = 0x34; // Change clock from ILRC to IHRC/2   CLKMD = 0xF4; // Change clock from IHRC to ILRC CLKMD.4 = 0; // disable IHRC … while (1) { STOPSYS; // enter power-down if (…) break; // if wakeup happen and check OK, then return to high speed, // else stay in power-down mode again } CLKMD = 0x34; // Change clock from ILRC to IHRC/2
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

    if (INTRQ.PA0)
	{
        intput_flag=0;
	}
	   if (INTRQ.PA4)
	{
        intput_flag=0;
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