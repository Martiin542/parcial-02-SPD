# Parcial SPD 02 (26/6/23)
## Alumno
- Martin Simone

![image](https://github.com/Martiin542/parcial-02-SPD/assets/116306654/60d7574a-ef6f-4cd5-98ae-3e08108f2576)


## Proyecto: 
diseñar un sistema de incendio utilizando Arduino que pueda
detectar cambios de temperatura y activar un servo motor en caso de detectar un incendio.
Además, se mostrará la temperatura actual y la estación del año en un display LCD.

## Consigna:
Configura el sensor de temperatura y realiza la lectura de la temperatura ambiente.
Muestra la temperatura actual en el display LCD. Define un umbral de temperatura a partir del cual se considera que hay un incendio (por
ejemplo, temperatura superior a 60 grados Celsius).
Cuando se detecta un incendio (temperatura por encima del umbral), se activa el servo
motor para simular una respuesta del sistema de incendio. Muestra la temperatura actual y la estación del año en el display LCD.
Cuando se detecta un incendio, muestra un mensaje de alarma en el display LCD. Configura el control remoto IR para recibir señales.
Define los comandos necesarios para activar y desactivar el sistema de incendio.

~~~ C++
#include <Servo.h>
#include <IRremote.h>
#include <LiquidCrystal.h>
// Seteo display
LiquidCrystal lcd(2,3,4,5,6,7);
// Seteo TMP
int pinTMP = 0;
float temperatura = 0;
// Seteo Servo
Servo myServo;
//leds
#define led_1 12
#define led_2 11
//IR
int estadoActualUno = 0;
int estadoActualDos = 0;
int estadoUltimo = 0;
int contador = 0;

void setup()
{
  myServo.attach(8,500,2500);
  Serial.begin(9600);
  lcd.begin(16,2);
  pinMode(led_1, OUTPUT); 
  pinMode(led_2, OUTPUT);
}

void loop()
{
  temperatura = map(analogRead(pinTMP),0,1023,-49,450);
  String estacion = calcularEstacion(temperatura);
  int estadoReceptor;
  control_ir(estadoReceptor);
  Serial.println(estadoReceptor);
  
  if(estadoReceptor == HIGH)
  {
  	if(estacion != "Inciendio")
  	{
      lcd.setCursor(0, 0);
      lcd.print("Temp: ");
      lcd.print(temperatura);

      lcd.setCursor(0, 1);
      lcd.print("Est: ");
      lcd.print(estacion);
      delay(250);
  	}
  	else
  	{
      lcd.setCursor(0, 0);
      lcd.print("Temp: ");
      lcd.print(temperatura);

      lcd.setCursor(0, 1);
      lcd.print(estacion);
      delay(250);

      myServo.write(0); 
      delay(1000); 
      myServo.write(180); 
      delay(1000); 
    
      secuencia_led(led_1, led_2);
  	}
  }
  else
  {
    lcd.clear();
  	lcd.setCursor(0, 0);
    lcd.print("OFF");
  }
}
  

String calcularEstacion(float temp)
{
  String estacion;
  
  switch(int(temp))
  {
  	case 51 ... 125:
      estacion = "Inciendio";
      break;
    case 26 ... 50:
      estacion = "Verano";
      break;
    case 20 ... 24:
      estacion = "Primavera";
      break;
    case 10 ... 19:
      estacion = "Otoño";
      break;
    default:
      estacion = "Invierno";
      break;
  }
  
  return estacion;
}

void secuencia_led(int primer_led, int segundo_led)
{ 
  digitalWrite(primer_led, HIGH); 
  digitalWrite(segundo_led, LOW); 
  delay(250); 
  digitalWrite(primer_led, LOW); 
  digitalWrite(segundo_led, HIGH); 
  delay(375); 
}

  
  
  
void control_ir(int &estadoReceptor)
{
  estadoActualUno = digitalRead(9);
  delay(90);
  estadoActualDos = digitalRead(9);
  
  if (estadoActualUno != estadoUltimo)
  {
    if (estadoActualUno == HIGH)
    {
      contador = contador + 1;
    }
  }
  
  estadoUltimo = estadoActualUno;
  
  if (contador % 2 == 0)
  {
    estadoReceptor = HIGH;
  }
  else
  {
    estadoReceptor = LOW;
  }
}
~~~

## Diagrama esquematico:
![Parcial 02 - SPD Martin Simone_page-0001](https://github.com/Martiin542/parcial-02-SPD/assets/116306654/dd9975bb-634a-47fa-be5d-e05f36adac4a)

* Sensor temperatura [TMP36]: Permite conocer la temperatura de una habitacion, ademas de reconocer cuando se produce un incendio.
* Display 16x2: Permite mostrar la temperatura que lee el sensor TMP36, la estacion del año segun la temperatura registrada y si se produce un incendio.
* Servo motor: Se acciona cunado se produce un incendio.
* Sensor IR: Permite reciber los comandos del control remoto IR.
* Control Remoto IR: Permite prender y apagar el sistema de incendio.

## Link al projecto:
* https://www.tinkercad.com/things/g7ZRgBKu9zq-martin-simone/editel?sharecode=qTldVlSyi4Ljp7keMHPkJ_EijO9guws3PA3rjNQUT6E
