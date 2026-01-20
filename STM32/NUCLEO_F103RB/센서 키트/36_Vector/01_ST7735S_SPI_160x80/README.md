# ST7735S_SPI_160x80

<img width="1754" height="773" alt="화면 캡처 2026-01-20 165345" src="https://github.com/user-attachments/assets/12f13f22-8175-47a5-a4b0-0f923e04cc32" /><br>


## 1. STM32 설정 [문제가 되는 부분 수정]
```
- **SPI1** = pa5 output 해지 spi1 > mode활성화  
- **Prescaler** = 4로 변경
- **GPIO설정** = PA1>LCD_RES PA5>SP1_SCK PA6>LCD_DC PA7>SPI1_MOSI PB6>LCD_CS  
```


## 2. 코드수정
```

```









