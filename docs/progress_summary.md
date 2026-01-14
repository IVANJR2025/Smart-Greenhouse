# Resumo do Progresso - Smart Greenhouse ESP32

**Projeto:** Smart Greenhouse - Sistema de Monitorização IoT  
**Hardware:** ESP32 DevKit V1 (WiFi 2.4GHz)  
**Linguagem:** MicroPython  
**Data:** 26/12/2025  

---

## 📋 Status Atual do Projeto

### ✅ Concluído

- [x] Definição da arquitetura do sistema
- [x] Escolha do hardware (ESP32 em vez de Raspberry Pi)
- [x] Instalação do ambiente de desenvolvimento
- [x] Configuração completa do ESP32
- [x] Testes de conectividade (WiFi + MQTT)
- [x] Estrutura inicial do projeto no GitHub

### ⏳ Em Andamento

- [ ] Implementação dos sensores
- [ ] Integração completa com MQTT
- [ ] Base de dados
- [ ] Dashboard de monitorização

---

## 🛠️ Ambiente de Desenvolvimento Configurado

### Software Instalado

| Software | Versão | Status | Observações |
|----------|--------|--------|-------------|
| **Mosquitto MQTT Broker** | Latest | ✅ Rodando | Instalado no Windows, porta 1883 |
| **Thonny IDE** | Latest | ✅ Instalado | IDE para programação MicroPython |
| **MicroPython** | v1.27.0 | ✅ Instalado | Firmware no ESP32 |
| **Driver USB CP2102** | Latest | ✅ Instalado | Comunicação USB-Serial |

### Hardware Configurado

| Componente | Modelo | Status | Observações |
|------------|--------|--------|-------------|
| **Microcontrolador** | ESP32 DevKit V1 | ✅ Funcionando | Porta COM3, WiFi 2.4GHz |
| **Cabo USB** | Micro USB | ✅ Testado | Transmissão de dados OK |

---

## 🔧 Configuração do ESP32 - Passo a Passo Realizado

### 1. Instalação de Drivers USB

**Problema Inicial:** ESP32 não era reconhecido pelo Windows  
**Causa:** Falta de driver USB (chip CP2102)  
**Solução:** Instalação manual do driver Silicon Labs CP210x

**Passos realizados:**
1. Download do driver CP210x de: https://www.silabs.com/developers/usb-to-uart-bridge-vcp-drivers
2. Instalação do driver
3. Reinicialização do sistema
4. Verificação no Gerenciador de Dispositivos → `Silicon Labs CP210x (COM3)` ✅

---

### 2. Instalação do MicroPython

**Firmware:** `ESP32_GENERIC-20241209-v1.27.0.bin`  
**Método:** Flash via Thonny IDE

**Passos realizados:**
1. Download do firmware de: https://micropython.org/download/ESP32_GENERIC/
2. Abertura do Thonny → Tools → Options → Interpreter
3. Seleção: "MicroPython (ESP32)"
4. Porta: COM3
5. "Install or update MicroPython (esptool)"
6. Flash do firmware (processo levou ~2 minutos)
7. Verificação: Shell mostrando `MicroPython v1.27.0` ✅

---

### 3. Teste de Funcionamento - LED Onboard

**Objetivo:** Verificar se o ESP32 responde aos comandos

**Código de teste:**
```python
from machine import Pin
import time

led = Pin(2, Pin.OUT)

print("LED piscando 5 vezes!")
for i in range(5):
    led.on()
    print(f"Piscada {i+1} - LED ON")
    time.sleep(0.5)
    led.off()
    print(f"Piscada {i+1} - LED OFF")
    time.sleep(0.5)

print("Teste concluído!")
```

**Resultado:** LED azul piscou 5 vezes ✅  
**Arquivo salvo:** `test_led.py` (no ESP32)

---

### 4. Configuração de Conectividade WiFi

**Rede WiFi:** Rede da empresa (2.4GHz)  
**IP Obtido:** 192.168.1.76

**Código de teste WiFi:**
```python
import network
import time

WIFI_SSID = "[CONFIGURADO]"
WIFI_PASSWORD = "[CONFIGURADO]"

wlan = network.WLAN(network.STA_IF)
wlan.active(True)
wlan.connect(WIFI_SSID, WIFI_PASSWORD)

# Aguardar conexão
while not wlan.isconnected():
    time.sleep(1)

print(f"Conectado! IP: {wlan.ifconfig()[0]}")
```

**Resultado:** Conexão estabelecida com sucesso ✅  
**Arquivo salvo:** `test_wifi.py` (no ESP32)

---

### 5. Instalação da Biblioteca MQTT

**Problema:** `upip` não disponível no MicroPython v1.27.0  
**Solução:** Instalação manual da biblioteca

**Método utilizado:** Criação manual do arquivo `umqtt_simple.py`

**Biblioteca instalada:** Cliente MQTT simplificado com métodos:
- `connect()` - Conectar ao broker
- `disconnect()` - Desconectar
- `publish(topic, msg)` - Publicar mensagem
- `subscribe(topic)` - Inscrever-se em tópico
- `wait_msg()` - Aguardar mensagem

**Arquivo criado:** `umqtt_simple.py` (no ESP32) ✅

---

### 6. Configuração MQTT

**Problema Inicial:** Firewall corporativo bloqueando porta 1883 local  
**Erro:** `[Errno 113] ECONNABORTED`

**Solução Adotada:** Uso de broker MQTT público

**Configuração Final:**
- **Broker:** test.mosquitto.org (público)
- **Porta:** 1883
- **Client ID:** ESP32_Greenhouse_001
- **Tópico de teste:** greenhouse/[usuario]/test

**Código de teste MQTT:**
```python
from umqtt_simple import MQTTClient

MQTT_BROKER = "test.mosquitto.org"
MQTT_PORT = 1883
MQTT_CLIENT_ID = "ESP32_Greenhouse_001"
MQTT_TOPIC = "greenhouse/teste/messages"

client = MQTTClient(MQTT_CLIENT_ID, MQTT_BROKER, MQTT_PORT)
client.connect()
client.publish(MQTT_TOPIC, "Ola do ESP32!")
client.disconnect()
```

**Resultado:** Mensagem publicada com sucesso ✅  
**Arquivo salvo:** `test_mqtt.py` (no ESP32)

---

## 📊 Testes Realizados e Resultados

| Teste | Objetivo | Resultado | Observações |
|-------|----------|-----------|-------------|
| **LED Onboard** | Verificar GPIO e comandos básicos | ✅ Passou | LED piscou 5x conforme programado |
| **WiFi** | Conectividade de rede | ✅ Passou | IP obtido: 192.168.1.76 |
| **MQTT Local** | Conectar ao Mosquitto local | ❌ Falhou | Firewall corporativo bloqueando |
| **MQTT Público** | Conectar ao broker público | ✅ Passou | test.mosquitto.org funcionando |

---

## 🗂️ Estrutura de Arquivos Criada

### No ESP32 (MicroPython device):

```
/
├── umqtt_simple.py      # Biblioteca cliente MQTT
├── test_led.py          # Teste do LED onboard
├── test_wifi.py         # Teste de conectividade WiFi
└── test_mqtt.py         # Teste de publicação MQTT
```

### No Repositório GitHub:

```
Smart-Greenhouse/
├── README.md                    # Enunciado do projeto
├── .gitignore
│
├── docs/                        # Documentação
│   ├── architecture_esp32.md   # Arquitetura para ESP32
│   ├── setup_guide_esp32.md    # Guia de configuração
│   └── progress_summary.md     # Este documento
│
├── database/                    # Scripts de BD (a criar)
│
└── src/                         # Código fonte
    └── esp32/                   # Código do ESP32
        └── (arquivos de teste já criados)
```

---

## 🔌 Configuração de Rede

### Informações de IP

| Dispositivo | IP | Observações |
|-------------|-----|-------------|
| **PC Windows** | 192.168.1.68 | Host do Mosquitto local |
| **ESP32** | 192.168.1.76 | IP dinâmico atribuído pelo DHCP |

### Portas Utilizadas

| Serviço | Porta | Status |
|---------|-------|--------|
| **MQTT (Mosquitto)** | 1883 | Bloqueado por Firewall corporativo |
| **MQTT (test.mosquitto.org)** | 1883 | ✅ Funcionando |

---

## ⚠️ Problemas Encontrados e Soluções

### Problema 1: ESP32 não detectado pelo Windows

**Sintomas:**
- LED do ESP32 acendia
- Windows não fazia som de conexão
- Nenhuma porta COM aparecia

**Diagnóstico:**
- Cabo USB transmitia apenas energia (não dados)
- Driver USB não instalado

**Solução:**
1. Troca de cabo USB
2. Instalação do driver CP210x
3. Reinicialização do sistema

**Lição aprendida:** Cabos USB de carregador geralmente não têm fios de dados

---

### Problema 2: MicroPython não respondia no Thonny

**Sintomas:**
- Erro: "Device is busy or does not respond"
- Shell não mostrava o prompt `>>>`

**Diagnóstico:**
- ESP32 sem firmware MicroPython instalado

**Solução:**
1. Download do firmware oficial
2. Flash via Thonny IDE
3. Reinicialização do ESP32

---

### Problema 3: Biblioteca MQTT não disponível

**Sintomas:**
- `ImportError: no module named 'upip'`
- `upip.install()` não funcionava

**Diagnóstico:**
- MicroPython v1.27.0 não inclui `upip`

**Solução:**
- Instalação manual da biblioteca
- Criação do arquivo `umqtt_simple.py` com código completo do cliente MQTT

---

### Problema 4: Firewall bloqueando MQTT local

**Sintomas:**
- `[Errno 113] ECONNABORTED`
- ESP32 não conseguia conectar ao Mosquitto local

**Diagnóstico:**
- Firewall corporativo do Windows bloqueando porta 1883
- Impossibilidade de alterar configurações (PC da empresa)

**Solução:**
- Mudança para broker MQTT público (test.mosquitto.org)
- Funciona perfeitamente para desenvolvimento e testes

**Nota:** Para apresentação final, usar laptop pessoal sem restrições de firewall

---

## 🎯 Próximos Passos Planejados

### Fase 1: Estrutura do Código (Próxima)

- [ ] Criar `config.py` - Configurações centralizadas
- [ ] Criar `boot.py` - Executa no boot do ESP32
- [ ] Criar `main.py` - Loop principal do sistema
- [ ] Criar estrutura de pastas: `/sensors`, `/actuators`, `/lib`

### Fase 2: Implementação dos Sensores

**Ordem de implementação:**
1. [ ] DHT22 (Temperatura e Humidade) - GPIO 4
2. [ ] Sensor de Solo (Humidade) - GPIO 34 (ADC)
3. [ ] LDR (Luminosidade) - GPIO 26 (ADC)
4. [ ] RC522 (NFC) - SPI (GPIO 5, 18, 19, 23, 15)
5. [ ] LEDs (Indicadores) - GPIO 17, 21, 22

**Para cada sensor:**
- Criar módulo individual em `/sensors`
- Testar isoladamente
- Integrar com MQTT
- Documentar pinout e configuração

### Fase 3: Integração MQTT Completa

- [ ] Estrutura de tópicos definida:
  ```
  greenhouse/sensors/temperature
  greenhouse/sensors/humidity
  greenhouse/sensors/soil_moisture
  greenhouse/sensors/luminosity
  greenhouse/actuators/ventilation
  greenhouse/access/nfc/status
  greenhouse/access/nfc/card_id
  greenhouse/system/status
  ```
- [ ] Formato JSON para mensagens
- [ ] Publicação periódica (a cada 5 segundos)
- [ ] Subscrição para comandos de controle

### Fase 4: Base de Dados

- [ ] Instalar SQLite no PC
- [ ] Criar schema (tabelas para sensores, eventos, acessos)
- [ ] Script Python subscriber MQTT → BD
- [ ] Testes de persistência

### Fase 5: Dashboard de Monitorização

**Opções a avaliar:**
- Node-RED (visual, drag-and-drop)
- Flask + Chart.js (customizável)
- Grafana (profissional)

**Funcionalidades:**
- [ ] Visualização em tempo real
- [ ] Gráficos históricos
- [ ] Controle manual de atuadores
- [ ] Indicadores de status
- [ ] Log de eventos

### Fase 6: Documentação e Apresentação

- [ ] Diagrama elétrico (Fritzing)
- [ ] Fotos do protótipo montado
- [ ] Vídeo de demonstração
- [ ] Manual de uso
- [ ] Slides para apresentação

---

## 📚 Referências e Links Úteis

### Documentação Oficial

- **MicroPython ESP32:** https://docs.micropython.org/en/latest/esp32/quickref.html
- **Eclipse Mosquitto:** https://mosquitto.org/documentation/
- **ESP32 Datasheet:** https://www.espressif.com/en/products/socs/esp32

### Drivers e Software

- **CP210x Drivers:** https://www.silabs.com/developers/usb-to-uart-bridge-vcp-drivers
- **Thonny IDE:** https://thonny.org/
- **MicroPython Downloads:** https://micropython.org/download/

### Tutoriais Seguidos

- Instalação MicroPython no ESP32
- Configuração cliente MQTT em MicroPython
- Resolução de problemas de conectividade

---

## 💡 Lições Aprendidas

1. **Hardware:** Sempre verificar se cabos USB transmitem dados, não apenas energia
2. **Drivers:** Em ambientes corporativos, instalar drivers pode requerer privilégios de administrador
3. **Firewall:** Redes corporativas frequentemente bloqueiam portas não padrão; brokers públicos são alternativa viável para desenvolvimento
4. **MicroPython:** Versões mais recentes podem não incluir `upip`; instalação manual de bibliotecas é necessária
5. **Testes:** Testar cada componente isoladamente antes de integrar facilita debug
6. **Documentação:** Manter registro detalhado do progresso é essencial em projetos acadêmicos

---

## 📞 Informações de Suporte

### Configurações do Projeto

**WiFi:** Rede da empresa (configuração privada)  
**MQTT Broker:** test.mosquitto.org (público)  
**IDE:** Thonny  
**Porta Serial:** COM3  
**IP ESP32:** 192.168.1.76 (dinâmico)

### Arquivos de Configuração

Todos os arquivos de teste e configuração estão salvos no ESP32 e podem ser acessados via:
- Thonny → View → Files → MicroPython device

---

## ✅ Checklist de Verificação

Antes de continuar para a próxima fase, confirmar:

- [x] ESP32 reconhecido pelo Windows (COM3)
- [x] MicroPython v1.27.0 instalado e funcionando
- [x] LED onboard responde a comandos
- [x] WiFi conecta automaticamente
- [x] Cliente MQTT funciona com broker público
- [x] Biblioteca MQTT instalada no ESP32
- [x] Documentação atualizada no GitHub
- [ ] Componentes físicos adquiridos (sensores, LEDs, NFC)
- [ ] Protoboard e jumpers preparados
- [ ] Pinout do ESP32 estudado

---

**Última Atualização:** 26/12/2025  
**Responsável:** Projeto Smart Greenhouse  
**Status:** Fase de configuração concluída com sucesso ✅