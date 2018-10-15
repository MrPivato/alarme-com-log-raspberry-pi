## Exemplo de alarme com GPIO

## Montagem

![1](img/montagem.png)

## Código

[Link código](python_code/script.py)

```python
#importa modulos necessarios
import RPi.GPIO as GPIO         # da GPIO
import time                     # do tempo
from datetime import datetime   # da data
import logging                  # para criar arquivos de log

# dir e nome onde o arquivo de log sera criado
nameLogFile = "/home/pi/uploadGDrive/log_movimentos.txt"

# alcance da detectcao (use centimetros)
detectionRange = 30.00

# usar os pinos numerados
GPIO.setmode(GPIO.BCM)

# define os pinos a serem usados
trigPin = 14
echoPin = 15
buzzPin = 18

# seta os pinos de input e output
GPIO.setup(trigPin, GPIO.OUT)
GPIO.setup(buzzPin, GPIO.OUT)
GPIO.setup(echoPin, GPIO.IN)

# function para criar logs
def createLog(dataHora):
    logging.basicConfig(level = logging.WARNING, filename = nameLogFile)
    msg = dataHora.strftime("Movimento detectado %Hhrs:%Mmin:%Sseg_%d-%m-%Y, " + distanceMsg)
    logging.warning(msg)
    print(msg)

try:

    while True:

        GPIO.output(trigPin, True)
        time.sleep(0.75)
        GPIO.output(trigPin, False)

        startTime = time.time()
        stopTime = time.time()

        while 0 == GPIO.input(echoPin):
            startTime = time.time()

        while 1 == GPIO.input(echoPin):
            stopTime = time.time()

        timeElapsed = stopTime - startTime # T2 - T1
        distance = (timeElapsed * 34300) / 2

        distanceMsg = "Distancia: %.1f cm" % distance

        print (distanceMsg)

        if distance <= detectionRange:
            dataHora = datetime.now()
            createLog(dataHora)
            GPIO.output(buzzPin, True)
            time.sleep(1.00)
            GPIO.output(buzzPin, False)

except KeyboardInterrupt:
    GPIO.cleanup()
```
