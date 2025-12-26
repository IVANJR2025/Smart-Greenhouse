┌─────────────────────────────────────────────────────────────────────┐
│                          SMART GREENHOUSE                            │
│                     Raspberry Pi Pico W (Controlador)                │
└─────────────────────────────────────────────────────────────────────┘
                                    │
                    ┌───────────────┼───────────────┐
                    │               │               │
            ┌───────▼──────┐ ┌─────▼─────┐ ┌──────▼──────┐
            │   SENSORES   │ │ ATUADORES │ │  CONTROLE   │
            └──────────────┘ └───────────┘ └─────────────┘
                    │               │               │
        ┌───────────┼───────┐       │       ┌───────┼────────┐
        │           │       │       │       │       │        │
    ┌───▼───┐  ┌───▼───┐ ┌─▼──┐  ┌─▼──┐  ┌─▼──┐  ┌─▼───┐
    │ DHT22 │  │ Solo  │ │LDR │  │LED │  │NFC │  │Status│
    │Temp + │  │Humid. │ │Luz │  │Vent│  │RC52│  │ LEDs │
    │ Humid │  │       │ │    │  │    │  │ 2  │  │      │
    └───┬───┘  └───┬───┘ └─┬──┘  └─┬──┘  └─┬──┘  └──┬───┘
        │          │       │       │       │        │
        └──────────┴───────┴───────┴───────┴────────┘
                            │
                    ┌───────▼────────┐
                    │  WiFi 2.4GHz   │
                    │  Conectividade │
                    └───────┬────────┘
                            │
                    ┌───────▼────────┐
                    │ MQTT BROKER    │
                    │  (Mosquitto)   │
                    │   localhost    │
                    └───────┬────────┘
                            │
                ┌───────────┼───────────┐
                │           │           │
        ┌───────▼──────┐ ┌──▼────────┐ ┌─▼──────────┐
        │  SUBSCRIBER  │ │ SUBSCRIBER│ │ SUBSCRIBER │
        │  Database    │ │ Dashboard │ │  Logger    │
        │   Script     │ │    Web    │ │   (opt)    │
        └──────────────┘ └───────────┘ └────────────┘
                │               │
        ┌───────▼──────┐ ┌─────▼──────┐
        │   SQLite     │ │   Node-RED │
        │   Database   │ │  ou Flask  │
        └──────────────┘ └────────────┘

 Mapeamento de Pinos GPIO - Raspberry Pi Pico W

 COMPONENTE         PINO PICO W           FUNÇÃO    OBSERVAÇÕES
 DHT 22             GPIO 15 (PINO 20)     DATA      Sensor Temp. + humidade
 DHT 22             3V3 (PINO 36)         VCC       Alimentação 3.3v
 DHT 22             GND (PINO 38)         GND       Terra

 SOLO               GPIO 26/ADCO(pino 31) Analog    Humidade do solo
 SOLO               3V3 (PINO 36)         VCC       Alimentação 3.3v
 SOLO               GND (PINO 38)         GND       Terra

 LDR                GPIO27/ADC1(pino32)   Analog    Luminosidade
 LDR                3V3 (PINO 36)         VCC       Alimentação 3.3v
 LDR                GND (PINO 38)         GND       Terra

 RC522 NFC          GPIO 17 (PINO 22)     MOSI/SDA  SPI DATA OUT
 RC522 NFC          GPIO 16 (PINO 21)     MISO      SPI DATA IN
 RC522 NFC          GPIO 18 (PINO 24)     SCK       SPI CLOCK
 RC522 NFC          GPIO 14 (PINO 19)     CS/SDA    CHIP SELECT
 RC522 NFC          GPIO 13 (PINO 17)     RST       RESET
 RC522 NFC          3V3 (PINO 36)         VCC       Alimentação 3.3v
 RC522 NFC          GND (PINO 38)         GND       Terra
 
 LED Verde          GPIO 10 (PINO 14)     digital   Acesso Autorizado
 LED Verde          GND (PINO 13)         GND       Terra

 LED Vermelho       GPIO 11 (PINO 15)     Digital   Acesso Negado
 LED Vermelho       GND (PINO 13)         GND       Terra

 LED AZUL           GPIO 12 (PINO 16)     Digital   Ventilação Ativa
 LED AZUL           GND (PINO 13)         GND       Terra

 Diagrama Visual dos Pinos

 Raspberry Pi Pico W
    ┌─────────────────┐
    │ USB             │
    │  ┌───┐          │
    │  └───┘          │
    │                 │
GP0 │●              ●│ VBUS
GP1 │●              ●│ VSYS
GND │●              ●│ GND
GP2 │●              ●│ 3V3_EN
GP3 │●              ●│ 3V3(OUT) ──→ VCC (Sensores)
GP4 │●              ●│ ADC_VREF
GP5 │●              ●│ GP28/ADC2
GND │●              ●│ GND ──→ GND (Comum)
GP6 │●              ●│ GP27/ADC1 ──→ LDR
GP7 │●              ●│ GP26/ADC0 ──→ Sensor Solo
GP8 │●              ●│ RUN
GP9 │●              ●│ GP22
G10 │●              ●│ GND ──→ GND LEDs
G11 │●              ●│ GP21
G12 │●              ●│ GP20
G13 │●   [RP2040]   ●│ GP19
GND │●              ●│ GP18 ──→ NFC SCK
G14 │●              ●│ GND
G15 │●              ●│ GP17 ──→ NFC MOSI
G16 │●              ●│ GP16 ──→ NFC MISO
G17 │●              ●│ GP15 ──→ DHT22
GND │●              ●│ GP14 ──→ NFC CS
    └─────────────────┘

Fluxo de Dados MQTT

greenhouse/
├── sensors/
│   ├── temperature         #Float (°C)
│   ├── humidity           # Float (%)
│   ├── soil_moisture      # Integer (0-1023)
│   └── luminosity         # Integer (0-1023)
│
├── actuators/
│   └── ventilation        # Boolean (ON/OFF)
│
├── access/
│   ├── nfc/status         # String (authorized/denied/error)
│   └── nfc/card_id        # String (UID do cartão)
│
└── system/
    ├── status             # String (online/offline)
    ├── power_consumption  # Float (simulado em Watts)
    └── errors             # String (mensagens de erro)

Formato das Mensagens JSON

Sensores (Publicação a cada 5 segundos):

{
  "timestamp": "2025-12-26T15:30:45",
  "temperatura": 25.4,
  "humidade": 62.3,
  "solo": 512,
  "luminosidade": 768
}

Atuadores (Subscrição):

{
  "ventilação": true,
  "timestamp": "2025-12-26T15:30:45"
}

NFC Acesso (Publicação):

{
  "card_id": "A3:B2:C1:D4",
  "status": "autorizado",
  "timestamp": "2025-12-26T15:30:45"
}

 Fluxo de Operação do Sistema

 1. Inicialização
   
 2. 1. Pico W inicia
1. Conecta ao WiFi
2. Conecta ao broker MQTT (localhost)
3. Publica "greenhouse/system/status": "online"
4. Subscreve a "greenhouse/actuators/ventilation"
   
2. Loop Principal (a cada 5 segundos)
   
1. Lê DHT22 → temperatura e humidade
2. Lê sensor de solo → humidade do solo
3. Lê LDR → luminosidade
4. Calcula consumo (simulado)
5. Empacota em JSON
6. Publica em "greenhouse/sensors/*"
7. Verifica comandos recebidos
8. Atualiza estado dos LEDs

3. Evento NFC

1. Detecta cartão/tag aproximado
2. Lê UID do cartão
3. Verifica se está autorizado (lista local)
4. Acende LED verde (autorizado) ou vermelho (negado)
5. Publica em "greenhouse/access/nfc/*"

4. Controle de Ventilação

1. Se temperatura > 28°C → Liga LED azul automaticamente
2. Dashboard pode ligar/desligar manualmente
3. Estado publicado em "greenhouse/actuators/ventilation"

Componentes Necessários (Lista de Compras)

1 Raspberry PI Pico W
1 Sensor DHT 22
1 Sensor de humidade do solo
1 LDR
1 Módulo RC522 NFC
3 LEDs (vermelho, verde, azul)
3 Resistores 220 ôhms
1 Resistor 10Kôhms
1 Protoboard
20 Jumpers M-M
10 Jumpers M-F
1 Cabo USB Micro