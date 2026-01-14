┌─────────────────────────────────────────────────────────┐
│              SMART GREENHOUSE - ESP32                    │
│           Microcontrolador com WiFi 2.4GHz              │
└─────────────────────────────────────────────────────────┘
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
   ┌────▼────┐       ┌─────▼─────┐      ┌────▼────┐
   │SENSORES │       │ ATUADORES │      │CONTROLE │
   └─────────┘       └───────────┘      └─────────┘
        │                  │                  │
   ┌────┼────┐            │             ┌────┼────┐
   │    │    │            │             │         │
 DHT22 SOLO LDR         LEDs           NFC      STATUS
   │    │    │            │             │         │
   └────┴────┴────────────┴─────────────┴─────────┘
                           │
                    ┌──────▼──────┐
                    │WiFi 2.4GHz  │
                    │  802.11n    │
                    └──────┬──────┘
                           │
                    ┌──────▼──────┐
                    │ PC Windows  │
                    │  Mosquitto  │
                    │    MQTT     │
                    └──────┬──────┘
                           │
              ┌────────────┼────────────┐
              │            │            │
        ┌─────▼─────┐ ┌────▼────┐ ┌────▼────┐
        │  SQLite   │ │Dashboard│ │  Logs   │
        │ Database  │ │   Web   │ │  (opt)  │
        └───────────┘ └─────────┘ └─────────┘

ESP32 DevKit V1
                  ┌──────────────┐
                  │     USB      │
                  └──────────────┘
                  
         3V3  ●                    ● GND
          EN  ●                    ● GPIO23  (MOSI - NFC)
    GPIO36/VP ●                    ● GPIO22  (LED Vermelho)
    GPIO39/VN ●                    ● GPIO1/TX
    GPIO34    ●                    ● GPIO3/RX
    GPIO35    ●                    ● GPIO21  (LED Azul)
    GPIO32    ●      [ESP32]       ● GND
    GPIO33    ●      [WROOM]       ● GPIO19  (MISO - NFC)
    GPIO25    ●                    ● GPIO18  (SCK - NFC)
    GPIO26/A0 ● (LDR)              ● GPIO5   (CS - NFC)
    GPIO27/A1 ●                    ● GPIO17  (LED Verde)
    GPIO14    ●                    ● GPIO16  
    GPIO12    ●                    ● GPIO4   (DHT22)
         GND  ●                    ● GPIO0
    GPIO13    ●                    ● GPIO2   (LED Onboard)
     GPIO9/D2 ●                    ● GPIO15  (RST - NFC)
    GPIO10/D3 ●                    ● GPIO8/D1
    GPIO11/CMD●                    ● GPIO7/D0
         VIN  ●                    ● GPIO6/CLK
                  └──────────────┘

ESTRUTURA DE TÓPICOS MQTT
greenhouse/
├── sensors/
│   ├── temperature         # Float (°C)
│   ├── humidity           # Float (%)
│   ├── soil_moisture      # Integer (0-4095, 12-bit ADC)
│   └── luminosity         # Integer (0-4095, 12-bit ADC)
│
├── actuators/
│   └── ventilation        # Boolean (true/false)
│
├── access/
│   ├── nfc/status         # String (authorized/denied/error)
│   └── nfc/card_id        # String (UID do cartão)
│
└── system/
    ├── status             # String (online/offline)
    ├── uptime             # Integer (segundos)
    ├── wifi_signal        # Integer (dBm)
    └── ip_address         # String (IP local)


FLUXO DE OPERAÇÃO

BOOT (boot.py)

# Configurações iniciais
# Conecta ao WiFi
# Sincroniza horário (NTP)
# LED onboard indica status

MAIN LOOP (main.py)

# 1. Conecta ao broker MQTT
# 2. Publica status: "online"
# 3. Subscreve: "greenhouse/actuators/#"
# 4. Loop infinito:
#    - Lê sensores (cada 5s)
#    - Publica dados via MQTT
#    - Verifica mensagens recebidas
#    - Controla LEDs
#    - Monitora NFC

AUTOMAÇÃO

# Se temperatura > 28°C:
#   - Liga LED azul (ventilação)
#   - Publica estado
# Se NFC detectado:
#   - Verifica autorização
#   - Liga LED verde/vermelho
#   - Publica evento

