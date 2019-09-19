# CiO2
![](https://github.com/dlrds/Cio2/blob/master/Imagens/logo%20(2).png)
Projeto final para a disciplina de Circuitos Lógicos


## Objetivos do Projeto
* Reutilizar água desperdiçada dos ar condicionados da instituição;
* Exibir valores (temperatura, umidade do ar e quantidade de irrigações diárias) periodicamente;
* Monitorar o desenvolvimento de plantas por meio de um sistema de irrigação.


## Introdução
Algo que é trivial de todo indivíduo saber é que a crise hídrica mundial é uma situação existente, e que o mal uso daágua pode agravar bastante a situação. Dentre as enormes formas de desperdício de água, está no uso do ar-condicionado,onde, indiretamente, é desperdiçada uma grande quantidade de água que é liberada por tal.


No caso de lugares com grandes estruturas, como no caso das universidades, esse desperdício tende a ser muito maior.Pensando nisso, decidimos reutilizar a água desperdicidada dos ares-condicionados do Ci (Centro Informática) da UniversidadeFederal da Paraíba, com o propósito de irrigar plantas que serão cultivadas na instituição, quando estas necessitarem de tal irrigação.


## Metodologia

Nosso trabalho foi dividido em quatro frentes: a criação do ambiente projetado em maquete, a constução do sistemade irrigação, a montagem do relógio digital e a mostra da temperatura e da umidade. Utilizamos uma maquete baseada naestrutura do Ci para melhor entendimento da aplicação do projeto. A montagem do prédio principal feito de caixa de papelão,acriílico e plástico.

O relógio foi utilizado o Real Time Clock RTC DS3231, módulo utilizado como memória, 4 codificadores de binário paraa montagem dos displays de sete segmentos e os próprios displays. Os valores de temperatura e umidade foram adquiridosa partir do sensor “DHT11” , sendo estes expostos por meio de displays de sete segmentos. Para a montagem do sistema deirrigação, foi projetado um sensor de umidade do solo caseiro e um clock com o CI555, estes utilizados no flip-flop tipo D que irácontrolar uma válvula solenóide, que permite ou não a passagem da água.


## Descrição do projeto

Serão exibidas em displays de sete segmentos a temperatura e a umidade do ar. O momento em que a água será liberadapara essas plantas será feito a partir do monitoramento de um sensor de umidade do solo caseiro. E ainda, os sistemas serãosimulados e representados com o auxílio de uma maquete.


## Execução do projeto, testes e resultados

## Relógio

### Código do relógio
```c
    #include "Wire.h"
#define DS3231_I2C_ADDRESS 0x68
//---------------------------------
// Converte de números decimais para BCD
byte decToBcd(byte val)
{return( (val/10*16) + (val%10) );}

// Converte de BCD para números decimais
byte bcdToDec(byte val)
{return( (val/16*10) + (val%16) );}
//---------------------------------

//Primeiro digito dos min
#define PinoA0 6
#define PinoA1 7
#define PinoA2 8
#define PinoA3 9

//Segundo digito dos min
#define PinoA4 2
#define PinoA5 3
#define PinoA6 4
#define PinoA7 5

//Primeiro digito das horas
#define PinoA8 10
#define PinoA9 11
#define PinoA10 12
#define PinoA11 13

//Segundo digite das horas
#define PinoA12 22
#define PinoA13 23
#define PinoA14 24
#define PinoA15 25


void setup() {
  Wire.begin(); //Inicia a comunicação com a biblioteca
  Serial.begin(9600);
  // Configure o tempo do DS3231 aqui:
  // Ordem exata: Segundos => Minutos => Horas
   setDS3231time(00,00,16);
   
   
   pinMode(PinoA0, OUTPUT);
   pinMode(PinoA1, OUTPUT);
   pinMode(PinoA2, OUTPUT);
   pinMode(PinoA3, OUTPUT);
   pinMode(PinoA4, OUTPUT);
   pinMode(PinoA5, OUTPUT);
   pinMode(PinoA6, OUTPUT);
   pinMode(PinoA7, OUTPUT);
   pinMode(PinoA8, OUTPUT);
   pinMode(PinoA9, OUTPUT);
   pinMode(PinoA10, OUTPUT);
   pinMode(PinoA11, OUTPUT);
   pinMode(PinoA12, OUTPUT);
   pinMode(PinoA13, OUTPUT);
   pinMode(PinoA14, OUTPUT);
   pinMode(PinoA15, OUTPUT);
  //Mensagem inicial serial Monitor
  Serial.println("Teste de CD4511n");
}
void setDS3231time(byte second, byte minute, byte hour)
{
  // Aplica o horário escolhido acima no DS3231
  Wire.beginTransmission(DS3231_I2C_ADDRESS);
  Wire.write(0);
  Wire.write(decToBcd(second)); // Setando segundos
  Wire.write(decToBcd(minute)); // Setando minutos
  Wire.write(decToBcd(hour)); // Setando horas
  Wire.endTransmission(); // Fim da transmissão
}
void readDS3231time(byte *second,
byte *minute,
byte *hour)
{
  Wire.beginTransmission(DS3231_I2C_ADDRESS);
  Wire.write(0); // Setar o registrador do DS3231 para 00h
  Wire.endTransmission();
  Wire.requestFrom(DS3231_I2C_ADDRESS, 7);
  // Requisita 7 bytes a partir do registro 00h
  *second = bcdToDec(Wire.read() & 0x7f);
  *minute = bcdToDec(Wire.read());
  *hour = bcdToDec(Wire.read() & 0x3f);
}
void displayTime()
{
  byte second, minute, hour;
  
  // Exibe os dados do DS3231
  readDS3231time(&second, &minute, &hour);

  /*------------------------------------------------------------------------------------------------------
    O Horário utilizado é separado por dígito
  --------------------------------------------------------------------------------------------------------*/

  int minuto1, minuto2;

  Serial.print("minutos em decimal: ");
  
  minuto1 = minute / 10;
  Serial.print(minuto1);
  minuto2 = minute % 10;
  Serial.print(minuto2);
  Serial.print("\n");

    Serial.print("O primeiro número é");
    if(minuto1 == 0) {
      digitalWrite(PinoA0, LOW);  
      digitalWrite(PinoA1, LOW);  
      digitalWrite(PinoA2, LOW);  
      digitalWrite(PinoA3, LOW);
      Serial.print(" zero");
      //delay(1000);
      }
    if(minuto1 == 1) {
      Serial.print(" um");
      digitalWrite(PinoA0, HIGH);  
      digitalWrite(PinoA1, LOW);  
      digitalWrite(PinoA2, LOW);  
      digitalWrite(PinoA3, LOW);
      //delay(1000);
    }
    if(minuto1 == 2){ 
      Serial.print(" dois");
      digitalWrite(PinoA0, LOW);  
      digitalWrite(PinoA1, HIGH);  
      digitalWrite(PinoA2, LOW);  
      digitalWrite(PinoA3, LOW);
      //delay(1000);
    }
    if(minuto1 == 3){ 
      Serial.print(" três");
      digitalWrite(PinoA0, HIGH);  
      digitalWrite(PinoA1, HIGH);  
      digitalWrite(PinoA2, LOW);  
      digitalWrite(PinoA3, LOW);
      //delay(1000);
    }
    if(minuto1 == 4){ 
      Serial.print(" quatro");
      digitalWrite(PinoA0, LOW);  
      digitalWrite(PinoA1, LOW);  
      digitalWrite(PinoA2, HIGH);  
      digitalWrite(PinoA3, LOW);
      //delay(1000);
    }
    if(minuto1 == 5){
      Serial.print(" cinco");
      digitalWrite(PinoA0, HIGH);  
      digitalWrite(PinoA1, LOW);  
      digitalWrite(PinoA2, HIGH);  
      digitalWrite(PinoA3, LOW);
      //delay(1000);
    }
    
    
    // Pula linha
      Serial.println("");
      
    Serial.print("O segundo número é");
    if(minuto2 == 0) {Serial.print(" zero");
      digitalWrite(PinoA4, LOW);
      digitalWrite(PinoA5, LOW);
      digitalWrite(PinoA6, LOW);
      digitalWrite(PinoA7, LOW);}
    if(minuto2 == 1){ Serial.print(" um");
      digitalWrite(PinoA4, HIGH);
      digitalWrite(PinoA5, LOW);
      digitalWrite(PinoA6, LOW);
      digitalWrite(PinoA7, LOW);}
    if(minuto2 == 2) {Serial.print(" dois");
      digitalWrite(PinoA4, LOW);
      digitalWrite(PinoA5, HIGH);
      digitalWrite(PinoA6, LOW);
      digitalWrite(PinoA7, LOW);}
    if(minuto2 == 3){ Serial.print(" três");
      digitalWrite(PinoA4, HIGH);
      digitalWrite(PinoA5, HIGH);
      digitalWrite(PinoA6, LOW);
      digitalWrite(PinoA7, LOW);}
    if(minuto2 == 4){ Serial.print(" quatro");
      digitalWrite(PinoA4, LOW);
      digitalWrite(PinoA5, LOW);
      digitalWrite(PinoA6, HIGH);
      digitalWrite(PinoA7, LOW);}
    if(minuto2 == 5){ Serial.print(" cinco");
      digitalWrite(PinoA4, HIGH);
      digitalWrite(PinoA5, LOW);
      digitalWrite(PinoA6, HIGH);
      digitalWrite(PinoA7, LOW);}
    if(minuto2 == 6){ Serial.print(" seis");
      digitalWrite(PinoA4, LOW);
      digitalWrite(PinoA5, HIGH);
      digitalWrite(PinoA6, HIGH);
      digitalWrite(PinoA7, LOW);}
    if(minuto2 == 7){ Serial.print(" sete");
      digitalWrite(PinoA4, HIGH);
      digitalWrite(PinoA5, HIGH);
      digitalWrite(PinoA6, HIGH);
      digitalWrite(PinoA7, LOW);}
    if(minuto2 == 8) {Serial.print(" oito");
      digitalWrite(PinoA4, LOW);
      digitalWrite(PinoA5, LOW);
      digitalWrite(PinoA6, LOW);
      digitalWrite(PinoA7, HIGH);}
    if(minuto2 == 9){ Serial.print(" nove");
      digitalWrite(PinoA4, HIGH);
      digitalWrite(PinoA5, LOW);
      digitalWrite(PinoA6, LOW);
      digitalWrite(PinoA7, HIGH);}

       int hora1, hora2;

  Serial.print("\nhoras em decimal: ");
  
  hora1 = hour / 10;
  Serial.print(hora1);
  hora2 = hour % 10;
  Serial.print(hora2);
  Serial.print("\n");

    Serial.print("O primeiro número é");
    if(hora1 == 0) {
      digitalWrite(PinoA8, LOW);  
      digitalWrite(PinoA9, LOW);  
      digitalWrite(PinoA10, LOW);  
      digitalWrite(PinoA11, LOW);
      Serial.print(" zero");
      //delay(1000);
      }
    if(hora1 == 1) {
      Serial.print(" um");
      digitalWrite(PinoA8, HIGH);  
      digitalWrite(PinoA9, LOW);  
      digitalWrite(PinoA10, LOW);  
      digitalWrite(PinoA11, LOW);
      //delay(1000);
    }
    if(hora1 == 2){ 
      Serial.print(" dois");
      digitalWrite(PinoA8, LOW);  
      digitalWrite(PinoA9, HIGH);  
      digitalWrite(PinoA10, LOW);  
      digitalWrite(PinoA11, LOW);
      //delay(1000);
    }
       
    // Pula linha
      Serial.println("");
      
    Serial.print("O segundo número é");
    if(hora2 == 0) {Serial.print(" zero");
      digitalWrite(PinoA12, LOW);
      digitalWrite(PinoA13, LOW);
      digitalWrite(PinoA14, LOW);
      digitalWrite(PinoA15, LOW);}
    if(hora2 == 1){ Serial.print(" um");
      digitalWrite(PinoA12, HIGH);
      digitalWrite(PinoA13, LOW);
      digitalWrite(PinoA14, LOW);
      digitalWrite(PinoA15, LOW);}
    if(hora2 == 2) {Serial.print(" dois");
      digitalWrite(PinoA12, LOW);
      digitalWrite(PinoA13, HIGH);
      digitalWrite(PinoA14, LOW);
      digitalWrite(PinoA15, LOW);}
    if(hora2 == 3){ Serial.print(" três");
      digitalWrite(PinoA12, HIGH);
      digitalWrite(PinoA13, HIGH);
      digitalWrite(PinoA14, LOW);
      digitalWrite(PinoA15, LOW);}
    if(hora2 == 4){ Serial.print(" quatro");
      digitalWrite(PinoA12, LOW);
      digitalWrite(PinoA13, LOW);
      digitalWrite(PinoA14, HIGH);
      digitalWrite(PinoA15, LOW);}
    if(hora2 == 5){ Serial.print(" cinco");
      digitalWrite(PinoA12, HIGH);
      digitalWrite(PinoA13, LOW);
      digitalWrite(PinoA14, HIGH);
      digitalWrite(PinoA15, LOW);}
    if(hora2 == 6){ Serial.print(" seis");
      digitalWrite(PinoA12, LOW);
      digitalWrite(PinoA13, HIGH);
      digitalWrite(PinoA14, HIGH);
      digitalWrite(PinoA15, LOW);}
    if(hora2 == 7){ Serial.print(" sete");
      digitalWrite(PinoA12, HIGH);
      digitalWrite(PinoA13, HIGH);
      digitalWrite(PinoA14, HIGH);
      digitalWrite(PinoA15, LOW);}
    if(hora2 == 8) {Serial.print(" oito");
      digitalWrite(PinoA12, LOW);
      digitalWrite(PinoA13, LOW);
      digitalWrite(PinoA14, LOW);
      digitalWrite(PinoA15, HIGH);}
    if(hora2 == 9){ Serial.print(" nove");
      digitalWrite(PinoA12, HIGH);
      digitalWrite(PinoA13, LOW);
      digitalWrite(PinoA14, LOW);
      digitalWrite(PinoA15, HIGH);}

 // Pula linha
  Serial.println("");
   
 // Printa uma linha de 30 "-"
  for(int i=0; i<15; i++){
  Serial.print("--");}

 // Pula linha
  Serial.println("");
  
}
void loop() {
  // put your main code here, to run repeatedly:
  displayTime(); // Chamada de função para exibir o tempo real
  delay(1000); // Exibe a cada segundo
}
``` 

### Circuito do relógio
![](https://github.com/dlrds/Cio2/blob/master/Imagens/Circuito.jpeg)


### Circuito sensor de umidade
![](https://github.com/dlrds/Cio2/blob/master/Imagens/Circuito2.jpeg)


### Circuito DHT11
![](https://github.com/dlrds/Cio2/blob/master/Imagens/Circuito2.jpeg)


### Código do DHT11
```c
    #include <stdio.h>
``` 
# Referências
 * MEIRA, Marcos Cavalcante; NETO, José Diniz; LIMA, Cláudio Galeno Queiroga Oliveira; JÚNIOR, Walmeran José Trindade;DIAS, José Artur Alves; BARBACENA, Ilton Luiz. Estudo e desenvolvimento de um circuito de um relógio digital utilizandofundamentalmente conhecimentos em circuitos lógicos. Circuitos lógicos, portas lógicas, relógio digital, Instituto Federalda Paraiba, 2012. Disponível em:http://propi.ifto.edu.br/ocs/index.php/connepi/vii/paper/viewFile/1076/2509. Acesso em: 9 ago. 2019.
 
 * SANTOS, Jean Carlos Fabiano dos. Sistema Inteligente de Automatização de Bombas de Irrigação por Temperatura eUmidade, Centro de Tecnologia e Urbanismo Departamento de Engenharia Elétrica, 17 nov. 2014. Disponível em:http://www.uel.br/ctu/deel/TCC/TCC2014-JeanCarlosFabianoSantos.pdf. Acesso em: 10 ago. 2019.
 
 * CAPUANO, Francisco Gabriel; IDOETA, Ivan Valeiji. Estudo e desenvolvimento de um circuito de um relógio digital utilizandofundamentalmente conhecimentos em circuitos lógicos. In: CAPUANO, Francisco Gabriel; IDOETA, Ivan Valeiji. ElementosDe Eletrônica Digital. 41. ed. São Paulo: érica, 2012. cap. Circuitos Combinacionais, p. 175-248. ISBN 978-85-7194-019-2.
 
 * SENSOR de Umidade Super Fácil. Produção: TDC PROJETOS. [S. l.: s. n.], 2015. Disponível em: https://www.clubedohardware.com.br/forums/topic/1263932-sensor-de-umidade-caseiro/. Acesso em: 14 set. 2019.
