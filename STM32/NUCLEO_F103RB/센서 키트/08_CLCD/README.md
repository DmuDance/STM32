# I2C CLCD

<img width="706" height="170" alt="129" src="https://github.com/user-attachments/assets/8fdba439-49cd-47d9-8069-223c4ea9305b" />

  * PB8 - SCL
  * PB9 - SDA

<img width="644" height="586" alt="F103RB-pin" src="https://github.com/user-attachments/assets/5a174c00-4edc-4481-a59f-00297ecf229d" />
<br><br>

<img width="800" height="600" alt="I2C_001" src="https://github.com/user-attachments/assets/6ad1eefb-17c6-4073-8355-276e65266cdb" />
<br>
<img width="800" height="600" alt="I2C_002" src="https://github.com/user-attachments/assets/4da3d974-64f7-48d9-8080-a8792f981041" />
<br>
<img width="800" height="600" alt="I2C_003" src="https://github.com/user-attachments/assets/06a7ed9a-ca43-4011-9b45-76428a27ada8" />
<br>

## 주의사항
```
핀이 pb5&6에 할 당되어 있으므로 보드에 맞추어 pb8&9로 이동시켜야함
pb8&9로 가서 마우스 왼쪽 누르고 I2C1_SCL선택
```
<p align="center">
<img width="50%" alt="I2C_004" src="https://github.com/user-attachments/assets/3c7069a0-57fa-4805-8b35-baa58dbc10e1" />
</p>


<p align="center">
<img width="30%" alt="I2C_005" src="https://github.com/user-attachments/assets/06aab658-744f-4e9e-b253-0ddf668f8f10" />
</p>

```c
/* USER CODE BEGIN Includes */
#include <stdio.h>
/* USER CODE END Includes */
```

```c
/* USER CODE BEGIN PFP */
void I2C_ScanAddresses(void);
/* USER CODE END PFP */
```

```c
/* USER CODE BEGIN 0 */
#ifdef __GNUC__
/* With GCC, small printf (option LD Linker->Libraries->Small printf
   set to 'Yes') calls __io_putchar() */
#define PUTCHAR_PROTOTYPE int __io_putchar(int ch)
#else
#define PUTCHAR_PROTOTYPE int fputc(int ch, FILE *f)
#endif /* __GNUC__ */

/**
  * @brief  Retargets the C library printf function to the USART.
  * @param  None
  * @retval None
  */
PUTCHAR_PROTOTYPE
{
  /* Place your implementation of fputc here */
  /* e.g. write a character to the USART1 and Loop until the end of transmission */
  if (ch == '\n')
    HAL_UART_Transmit (&huart2, (uint8_t*) "\r", 1, 0xFFFF);
  HAL_UART_Transmit (&huart2, (uint8_t*) &ch, 1, 0xFFFF);

  return ch;
}

void I2C_ScanAddresses(void) {
    HAL_StatusTypeDef result;
    uint8_t i;


    printf("Scanning I2C addresses...\r\n");


    for (i = 1; i < 128; i++) {
        /*
         * HAL_I2C_IsDeviceReady: If a device at the specified address exists return HAL_OK.
         * Since I2C devices must have an 8-bit address, the 7-bit address is shifted left by 1 bit.
         */
        result = HAL_I2C_IsDeviceReady(&hi2c1, (uint16_t)(i << 1), 1, 10);
        if (result == HAL_OK) {
            printf("I2C device found at address 0x%02X\r\n", i);
        }
    }


    printf("Scan complete.\r\n");
}

/* USER CODE END 0 */
```

```c
  /* USER CODE BEGIN 2 */
  I2C_ScanAddresses();
  /* USER CODE END 2 */
```


---
CLCD "Hello World!"
---

<img width="800" height="600" alt="CLCD" src="https://github.com/user-attachments/assets/a590d783-9ded-40ba-b23d-ce6f93a5b430" />

```c
/* USER CODE BEGIN Includes */
#include <stdio.h>
/* USER CODE END Includes */
```

```c
/* USER CODE BEGIN PD */
#define delay_ms HAL_Delay

#define ADDRESS   0x3F << 1
//#define ADDRESS   0x27 << 1

#define RS1_EN1   0x05
#define RS1_EN0   0x01
#define RS0_EN1   0x04
#define RS0_EN0   0x00
#define BackLight 0x08
/* USER CODE END PD */
```

```c
/* USER CODE BEGIN PV */
int delay = 0;
int value = 0;
/* USER CODE END PV */
```

```c
/* Private function prototypes -----------------------------------------------*/
void SystemClock_Config(void);
static void MX_GPIO_Init(void);
static void MX_USART2_UART_Init(void);
static void MX_I2C1_Init(void);
/* USER CODE BEGIN PFP */
void I2C_ScanAddresses(void);

void delay_us(int us);
void LCD_DATA(uint8_t data);
void LCD_CMD(uint8_t cmd);
void LCD_CMD_4bit(uint8_t cmd);
void LCD_INIT(void);
void LCD_XY(char x, char y);
void LCD_CLEAR(void);
void LCD_PUTS(char *str);
/* USER CODE END PFP */
```

```c
/* USER CODE BEGIN 0 */
#ifdef __GNUC__
/* With GCC, small printf (option LD Linker->Libraries->Small printf
   set to 'Yes') calls __io_putchar() */
#define PUTCHAR_PROTOTYPE int __io_putchar(int ch)
#else
#define PUTCHAR_PROTOTYPE int fputc(int ch, FILE *f)
#endif /* __GNUC__ */

/**
  * @brief  Retargets the C library printf function to the USART.
  * @param  None
  * @retval None
  */
PUTCHAR_PROTOTYPE
{
  /* Place your implementation of fputc here */
  /* e.g. write a character to the USART1 and Loop until the end of transmission */
  if (ch == '\n')
    HAL_UART_Transmit (&huart2, (uint8_t*) "\r", 1, 0xFFFF);
  HAL_UART_Transmit (&huart2, (uint8_t*) &ch, 1, 0xFFFF);

  return ch;
}

void I2C_ScanAddresses(void) {
    HAL_StatusTypeDef result;
    uint8_t i;

    printf("Scanning I2C addresses...\r\n");

    for (i = 1; i < 128; i++) {
        /*
         * HAL_I2C_IsDeviceReady: If a device at the specified address exists return HAL_OK.
         * Since I2C devices must have an 8-bit address, the 7-bit address is shifted left by 1 bit.
         */
        result = HAL_I2C_IsDeviceReady(&hi2c1, (uint16_t)(i << 1), 1, 10);
        if (result == HAL_OK) {
            printf("I2C device found at address 0x%02X\r\n", i);
        }
    }

    printf("Scan complete.\r\n");
}

void delay_us(int us){
	value = 3;
	delay = us * value;
	for(int i=0;i < delay;i++);
}

void LCD_DATA(uint8_t data) {
	uint8_t temp=(data & 0xF0)|RS1_EN1|BackLight;

	while(HAL_I2C_Master_Transmit(&hi2c1, ADDRESS, &temp, 1, 1000)!=HAL_OK);
	temp=(data & 0xF0)|RS1_EN0|BackLight;
	while(HAL_I2C_Master_Transmit(&hi2c1, ADDRESS, &temp, 1, 1000)!=HAL_OK);
	delay_us(4);

	temp=((data << 4) & 0xF0)|RS1_EN1|BackLight;
	while(HAL_I2C_Master_Transmit(&hi2c1, ADDRESS, &temp, 1, 1000)!=HAL_OK);
	temp = ((data << 4) & 0xF0)|RS1_EN0|BackLight;
	while(HAL_I2C_Master_Transmit(&hi2c1, ADDRESS, &temp, 1, 1000)!=HAL_OK);
	delay_us(50);
}

void LCD_CMD(uint8_t cmd) {
	uint8_t temp=(cmd & 0xF0)|RS0_EN1|BackLight;
	while(HAL_I2C_Master_Transmit(&hi2c1, ADDRESS, &temp, 1, 1000)!=HAL_OK);
	temp=(cmd & 0xF0)|RS0_EN0|BackLight;
	while(HAL_I2C_Master_Transmit(&hi2c1, ADDRESS, &temp, 1, 1000)!=HAL_OK);
	delay_us(4);

	temp=((cmd << 4) & 0xF0)|RS0_EN1|BackLight;
	while(HAL_I2C_Master_Transmit(&hi2c1, ADDRESS, &temp, 1, 1000)!=HAL_OK);
	temp=((cmd << 4) & 0xF0)|RS0_EN0|BackLight;
	while(HAL_I2C_Master_Transmit(&hi2c1, ADDRESS, &temp, 1, 1000)!=HAL_OK);
	delay_us(50);
}

void LCD_CMD_4bit(uint8_t cmd) {
	uint8_t temp=((cmd << 4) & 0xF0)|RS0_EN1|BackLight;
	while(HAL_I2C_Master_Transmit(&hi2c1, ADDRESS, &temp, 1, 1000)!=HAL_OK);
	temp=((cmd << 4) & 0xF0)|RS0_EN0|BackLight;
	while(HAL_I2C_Master_Transmit(&hi2c1, ADDRESS, &temp, 1, 1000)!=HAL_OK);
	delay_us(50);
}

void LCD_INIT(void) {

	delay_ms(100);

	LCD_CMD_4bit(0x03); delay_ms(5);
	LCD_CMD_4bit(0x03); delay_us(100);
	LCD_CMD_4bit(0x03); delay_us(100);
	LCD_CMD_4bit(0x02); delay_us(100);
	LCD_CMD(0x28);  // 4 bits, 2 line, 5x8 font
	LCD_CMD(0x08);  // display off, cursor off, blink off
	LCD_CMD(0x01);  // clear display
	delay_ms(3);
	LCD_CMD(0x06);  // cursor movint direction
	LCD_CMD(0x0C);  // display on, cursor off, blink off
}

void LCD_XY(char x, char y) {
	if      (y == 0) LCD_CMD(0x80 + x);
	else if (y == 1) LCD_CMD(0xC0 + x);
	else if (y == 2) LCD_CMD(0x94 + x);
	else if (y == 3) LCD_CMD(0xD4 + x);
}

void LCD_CLEAR(void) {
	LCD_CMD(0x01);
	delay_ms(2);
}

void LCD_PUTS(char *str) {
	while (*str) LCD_DATA(*str++);
}
/* USER CODE END 0 */
```

```c
  /* USER CODE BEGIN 2 */
  I2C_ScanAddresses();

  LCD_INIT();
  LCD_XY(0, 0) ; LCD_PUTS((char *)"LCD Display test");
  LCD_XY(0, 1) ; LCD_PUTS((char *)"Hello World.....");

  /* USER CODE END 2 */
```
<p align="center">
<img width="30%" alt="I2C_006" src="https://github.com/user-attachments/assets/5333b2df-d0e8-42e3-984e-7169d2ed195b" />
</p>


**번외프로젝트[CPU온도출력]**

STM32따라하기 155p를 참고하여 셋팅을 진행

<p align="center">
<img width="30%" alt="I2C_006" src="https://github.com/user-attachments/assets/d674fea2-5a91-4ad5-947c-8447f99dfa19" />
</p>

기존의 CPU TEMP를 측정하는 ADC Temp프로젝트에서 코드를 변환 삽입 후 GPT의 도움으로 CPU TEMP 출력 성공

<p align="center">
<img width="30%" alt="I2C_006" src="https://github.com/user-attachments/assets/fb8e86dd-889e-405f-b9d7-80e8c6171435" />
</p>

문자 비트맵 추가 후 CGRAM 생성 함수를 추가하여 C를 특수문자℃로 교체 성공 후 프로젝트 마무리

[CLCD ADC 온도측정]<br>(https://youtube.com/shorts/r_MQdw2jnWE?feature=share)
오실로스코프 측정
<p align="center">
<img width="30%" alt="I2C_006" src="https://github.com/user-attachments/assets/6eab0617-df4f-4faa-b548-4b7ac504305b" />
</p>





