Псевдо кольцевой буфер на DMA.

Пример расчитан на приём пакетов переменной длины:

Указываем размер входящего буфера, например 20 байт, прилетает пакет 5 байт, в бесконечном цикле проверяем если что-то в буфере - if(usart1_available()), видим что там что-то есть и забираем пришедшедшие данные...

```
while(usart1_available())
{
	buf[i++] = usart1_read();
	if(i > RX_BUFFER_SIZE - 1) break;
	HAL_Delay(1);
}
```

Потом прилетает ещё пакет, снова забираем - индекс будет указывать на новую партию данных. Как только буфер заполнится, DMA остановится, и запустится только после того как все данные будут вычитаны. 

Минусом этого примера является то, что часть данных будет терятся, то есть, допустим буфер выделен 20 байт, а данные приходят пакетами по 8 байт, тогда два пакета будут приняты 8 + 8 = 16, а последний пакет примется только наполовину. 
Это ни какое-то решение "всех проблем", а просто вариант, который можно использовать в том случае, когда потеря единичного пакета не критична. Допустим если указать буфер 500 байт, то будет теряться один пакет из 62-х.  

Плюс - никаких прерываний, DMA подсовывает нам байтики, а мы их забираем когда нам нужно, а ни когда DMA закончит работу.

Размер буфера указать в файле usart_dma_rx_buf.h - RX_BUFFER_SIZE. Механизм в файле usart_dma_rx_buf.c

