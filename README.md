# okdacrypt
Алгоритм шифрования, сделанный чисто для шутки, но с большим будущим :D

## Описание работы алгоритма

### Шифрование

Сначала считается сумма кодов символов ключа:
```python
keysum = sum(list(key)) # Ключ у нас в байтах, поэтому сначала приводим к списку, а затем считаем сумму
```
Затем мы удлиняем ключ до длины сообщения:
```python
key = _clone_array(key, len(message)) # Лямбда-функция _clone_array просто продолжает список до нужной длины. Подробнее - смотрите саму функцию
```
После этого мы проводим операцию **XOR** с каждым байтом сообщения и ключа:
```python
data = list(map(lambda d: d[0] ^ d[1], zip(message, key))) # Мне лично было удобнее так, хоть это и выглядит страшно
```
Далее мы просто оборачиваем наше сообщение в base64
```python3
data = encodestring(bytes(data)).replace(b'\n', b'') # заодно уберем переносы строк
```
Следующим шагом мы сдвигаем полученную строку по алфавиту на  %keysum% символов вправо
```python3
data = shift64(data, keysum) # самое "простое" здесь
```
И на последнем этапе мы просто переводим коды символов в HEX-код и добавляем своеобразную метку, что это наш алгоритм:
```python
data = ' '.join('%.2X'%v for v in data) # Очередной адский код от UV
return '0K DA %s 0K DA'%data
```

### Дешифровка
Здесь все почти так же, как и в шифровании, поэтому часть кода будет опущена

Сначала мы считаем сумму кодов символов ключа.

Далее мы сдвигаем влево на эту сумму и сразу декодируем:
```python
message = decodestring(shift64(message, -keysum)) # да, всего лишь отрицательный сдвиг
```
Затем мы уже удлиняем ключ до длины сообщения и просто снова проводим операцию XOR с ключом и сообщением:
```python3
message = bytes(map(lambda d: d[0] ^ d[1], zip(message, key))) # О-даа, лямбды~
return message
```

Да, вот так просто устроен этот алгоритм. Но несмотря на свою простоту он имеет несколько преимуществ:
  * Во-первых, длина сообщения и ключа может быть какой угодно. Желательно, чтобы ключ был уникальным, но это не столь важно.
  * Алгоритм легко повторяется (портирован с py2 на py3 только спустя год).
  * Сложность взлома методом грубой силы.
  * Можно модифицировать, добавив сдвиг base64-сообщения еще и по ASCII-таблице или вовсе перейти на base85.

Как видите, все очень просто. Надеюсь, описание алгоритма помогло Вам в понимании его работы.
