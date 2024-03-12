# SD-CARD-
uses SPI protocol ( 8 bits )
SCLK(serial Clock)
MISO( Master in slave out )
MOSI( Master out slave in )
SD_CS

*Configuration SD CARD in STM32CubeIDE:
  FATFS -> User defined
  USE_LFN -> BSS
  Min_SS(Sector size) = 512
  Max_SS(Sector size) = 4096

*Configuration SPI in STM32CubeIDE:
  SPI -> Prescale ( Baud Rate = 8)
  -> SD_CS_pin (GPIO_output)

*Links the control system to the compiler
File (user_diskio.c)
  
DSTATUS USER_initialize (
	BYTE pdrv           /* Physical drive nmuber to identify the drive */
)
{
  /* USER CODE BEGIN INIT */

    return SD_disk_initialize(pdrv);
  /* USER CODE END INIT */
}

DSTATUS USER_status (
	BYTE pdrv       /* Physical drive number to identify the drive */
)
{
  /* USER CODE BEGIN STATUS */
    return SD_disk_status(pdrv);
  /* USER CODE END STATUS */
}

DRESULT USER_read (
	BYTE pdrv,      /* Physical drive nmuber to identify the drive */
	BYTE *buff,     /* Data buffer to store read data */
	DWORD sector,   /* Sector address in LBA */
	UINT count      /* Number of sectors to read */
)
{
  /* USER CODE BEGIN READ */
    return SD_disk_read(pdrv, buff, sector, count);
  /* USER CODE END READ */
}

DRESULT USER_write (
	BYTE pdrv,          /* Physical drive nmuber to identify the drive */
	const BYTE *buff,   /* Data to be written */
	DWORD sector,       /* Sector address in LBA */
	UINT count          /* Number of sectors to write */
)
{
  /* USER CODE BEGIN WRITE */
  /* USER CODE HERE */
    return SD_disk_write(pdrv,buff,sector,count);
  /* USER CODE END WRITE */
}

DRESULT USER_ioctl (
	BYTE pdrv,      /* Physical drive nmuber (0..) */
	BYTE cmd,       /* Control code */
	void *buff      /* Buffer to send/receive control data */
)
{
  /* USER CODE BEGIN IOCTL */
    return SD_disk_ioctl(pdrv,cmd,buff);
  /* USER CODE END IOCTL */
}

*Definition of Port, Pin of SD_CS pin (File fatls_sd.c)

FILE (stm32fxx_it.c)
*Open interrupt to determine operating time
volatile uint8_t FatFsCnt = 0;
volatile uint8_t Timer1, Timer2;
void SDTimer_Handler(void)
{
  if(Timer1 > 0)
    Timer1--;
    
  if(Timer2 > 0)
    Timer2--;
}

*The timers work independently so we need to handle it in systick
void SysTick_Handler(void)
{
    FatFsCnt ++;
    if(FatFsCnt > = 10)
    {
      FatFsCnt = 0;
      SDTimer_Handler();
    }
}

