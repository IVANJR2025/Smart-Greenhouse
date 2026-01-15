# Sessão 2 - Estrutura de Código Implementada

**Data:** 26/12/2025  
**Duração:** ~3 horas  
**Status:** ✅ Concluída com sucesso  

---

## 🎯 Objetivos da Sessão

- [x] Criar arquitetura de código profissional
- [x] Implementar arquivos de configuração
- [x] Desenvolver sistema de boot automático
- [x] Criar loop principal funcional
- [x] Testar integração MQTT com dados simulados
- [x] Implementar automação básica (ventilação)

---

## 📁 Arquivos Criados no ESP32

### 1. config.py - Configurações Centralizadas

**Localização:** `/config.py` (raiz do ESP32)  
**Propósito:** Centralizar todas as configurações do sistema  

**Conteúdo:**
- Credenciais WiFi (SSID e senha)
- Configurações MQTT (broker, porta, tópicos)
- Mapeamento de pinos GPIO para todos os componentes
- Intervalos de leitura e publicação
- Limiares para automação

**Vantagens:**
- Fácil de modificar configurações sem mexer no código
- Um único lugar para todas as definições
- Facilita manutenção e debug

---

### 2. boot.py - Inicialização Automática

**Localização:** `/boot.py` (raiz do ESP32)  
**Propósito:** Executar automaticamente no boot do ESP32  

**Funcionalidades Implementadas:**
- ✅ Conexão automática ao WiFi
- ✅ Timeout configurável (15 segundos)
- ✅ Indicadores visuais via LED onboard:
  - 2 piscadas = já estava conectado
  - 3 piscadas = conectou com sucesso
  - Rápido = erro de conexão
- ✅ Exibição de informações de rede (IP, gateway, DNS)
- ✅ Tratamento de erros e exceções
- ✅ Modo offline se WiFi falhar

**Fluxo de Execução:**
```
1. ESP32 liga
2. boot.py executa automaticamente
3. Ativa interface WiFi
4. Conecta à rede configurada
5. Exibe informações de rede
6. Prepara sistema para main.py
```

---

### 3. main.py - Loop Principal do Sistema

**Localização:** `/main.py` (raiz do ESP32)  
**Propósito:** Controle principal do sistema Smart Greenhouse  

**Funcionalidades Implementadas:**

#### Inicialização:
- ✅ Configuração de todos os pinos GPIO (LEDs)
- ✅ Conexão ao broker MQTT
- ✅ Publicação de status "online"

#### Loop Principal (a cada 5 segundos):
1. **Leitura de Sensores** (simulados por enquanto)
   - Temperatura: 20-30°C
   - Humidade: 40-80%
   - Humidade do solo: 200-800
   - Luminosidade: 100-900

2. **Automação de Ventilação**
   - Verifica se temperatura > 28°C
   - Liga LED azul (GPIO 21) se necessário
   - Publica estado em `greenhouse/actuators/ventilation`

3. **Publicação MQTT**
   - Publica dados em tópicos separados:
     - `greenhouse/sensors/temperature`
     - `greenhouse/sensors/humidity`
     - `greenhouse/sensors/soil_moisture`
     - `greenhouse/sensors/luminosity`

4. **Indicadores Visuais**
   - LED onboard pisca a cada publicação
   - LED azul indica ventilação ativa

#### Tratamento de Erros:
- ✅ Captura Ctrl+C (interrupção do usuário)
- ✅ Tratamento de exceções gerais
- ✅ Limpeza ao sair (desliga LEDs, desconecta MQTT)
- ✅ Publica status "offline" ao encerrar

---

### 4. umqtt_simple.py - Cliente MQTT

**Localização:** `/umqtt_simple.py` (raiz do ESP32)  
**Status:** ✅ Já existia da sessão anterior  

**Métodos Utilizados:**
- `connect()` - Conectar ao broker
- `publish(topic, msg)` - Publicar mensagem
- `disconnect()` - Desconectar

---

## 🗂️ Estrutura Final de Arquivos no ESP32

```
ESP32 (MicroPython device)
├── boot.py              # ✅ Boot automático e WiFi
├── config.py            # ✅ Configurações centralizadas
├── main.py              # ✅ Loop principal
├── umqtt_simple.py      # ✅ Biblioteca MQTT
├── test_led.py          # ⚪ Teste anterior (manter)
├── test_wifi.py         # ⚪ Teste anterior (manter)
└── test_mqtt.py         # ⚪ Teste anterior (manter)
```

**Nota:** Arquivos de teste mantidos para referência futura.

---

## 🔧 Configurações Atuais

### WiFi
- **SSID:** [Configurado em config.py]
- **Status:** ✅ Conectado automaticamente no boot
- **IP ESP32:** 192.168.1.76 (dinâmico)

### MQTT
- **Broker:** test.mosquitto.org (público)
- **Porta:** 1883
- **Client ID:** ESP32_Greenhouse
- **Status:** ✅ Conectado e publicando

### Tópicos MQTT Implementados

| Tópico | Tipo | Frequência | Dados |
|--------|------|------------|-------|
| `greenhouse/sensors/temperature` | Publicação | 5s | Float (°C) |
| `greenhouse/sensors/humidity` | Publicação | 5s | Float (%) |
| `greenhouse/sensors/soil_moisture` | Publicação | 5s | Integer (0-1023) |
| `greenhouse/sensors/luminosity` | Publicação | 5s | Integer (0-1023) |
| `greenhouse/actuators/ventilation` | Publicação | Evento | String (ON/OFF) |
| `greenhouse/system/status` | Publicação | Boot/Shutdown | String (online/offline) |

---

## 🧪 Testes Realizados

### Teste 1: Boot Automático ✅

**Procedimento:**
1. Desconectar e reconectar ESP32
2. Observar mensagens no Shell
3. Verificar LEDs

**Resultado:**
- WiFi conectou automaticamente
- IP obtido: 192.168.1.76
- LED piscou 3 vezes (sucesso)
- Sistema pronto em ~5 segundos

---

### Teste 2: Publicação MQTT ✅

**Procedimento:**
1. Sistema rodando
2. Observar mensagens no Shell
3. Verificar LED onboard

**Resultado:**
```
[Loop 1]
Publicado: T=25.3°C H=62.1% S=456 L=678

[Loop 2]
Publicado: T=27.8°C H=58.4% S=523 L=712

[Loop 3]
Temperatura alta (28.9°C)! Ativando ventilação...
Publicado: T=28.9°C H=65.2% S=389 L=445
```

**Observações:**
- Dados publicados a cada 5 segundos ✅
- LED onboard pisca a cada publicação ✅
- Valores simulados dentro da faixa esperada ✅

---

### Teste 3: Automação de Ventilação ✅

**Procedimento:**
1. Aguardar temperatura simulada > 28°C
2. Observar LED azul
3. Verificar mensagens MQTT

**Resultado:**
- Quando temp > 28°C:
  - LED azul ligou ✅
  - Mensagem "Ativando ventilação" ✅
  - Publicado "ON" no tópico ✅
- Quando temp <= 28°C:
  - LED azul desligou ✅
  - Mensagem "Desativando ventilação" ✅
  - Publicado "OFF" no tópico ✅

---

### Teste 4: Interrupção e Limpeza ✅

**Procedimento:**
1. Sistema rodando
2. Pressionar Ctrl+C
3. Observar comportamento

**Resultado:**
```
Sistema interrompido pelo usuario

Desligando sistema...
MQTT desconectado
Sistema encerrado!
```

- Todos os LEDs desligaram ✅
- MQTT desconectou corretamente ✅
- Status "offline" publicado ✅
- Sem erros ou travamentos ✅

---

## 📊 Métricas do Sistema

### Performance
- **Tempo de boot:** ~5 segundos
- **Conexão WiFi:** ~3 segundos
- **Conexão MQTT:** ~1 segundo
- **Ciclo de leitura:** 5 segundos
- **Consumo de memória:** ~20KB RAM utilizada

### Confiabilidade
- **Uptime testado:** 30+ minutos contínuos
- **Falhas de conexão:** 0
- **Exceções não tratadas:** 0
- **Reinicializações inesperadas:** 0

---

## 🎯 Funcionalidades Implementadas

### Core System ✅
- [x] Boot automático com WiFi
- [x] Configuração centralizada
- [x] Loop principal estável
- [x] Integração MQTT completa
- [x] Tratamento de erros robusto

### Sensores (Simulados) ✅
- [x] Temperatura
- [x] Humidade do ar
- [x] Humidade do solo
- [x] Luminosidade

### Automação ✅
- [x] Ventilação automática por temperatura
- [x] Indicadores visuais (LEDs)
- [x] Publicação de eventos

### Sistema ✅
- [x] Status online/offline
- [x] Limpeza ao encerrar
- [x] Logs informativos

---

## 🔮 Próximos Passos

### Fase 3: Implementação de Sensores Reais

**Prioridade Alta:**
1. [ ] **DHT22** - Temperatura e Humidade real
   - Instalar biblioteca `dht.py`
   - Criar módulo `sensors/dht22.py`
   - Substituir valores simulados
   - Testar leituras

2. [ ] **Sensor de Solo** - ADC GPIO 34
   - Criar módulo `sensors/soil.py`
   - Calibrar valores (seco vs úmido)
   - Integrar no main.py

3. [ ] **LDR** - Luminosidade via ADC GPIO 26
   - Criar módulo `sensors/ldr.py`
   - Calibrar leitura analógica
   - Integrar no main.py

**Prioridade Média:**
4. [ ] **NFC RC522** - Controle de acesso
   - Instalar biblioteca `mfrc522.py`
   - Criar módulo `sensors/nfc.py`
   - Implementar lista de tags autorizadas
   - Controlar LEDs verde/vermelho

**Prioridade Baixa:**
5. [ ] **Consumo Elétrico** (simulado calculado)

---

### Fase 4: Base de Dados e Dashboard

**Base de Dados:**
- [ ] Instalar SQLite no PC
- [ ] Criar schema de tabelas
- [ ] Script Python subscriber MQTT → BD
- [ ] Testes de gravação

**Dashboard:**
- [ ] Escolher tecnologia (Node-RED, Flask ou Grafana)
- [ ] Criar interface básica
- [ ] Gráficos em tempo real
- [ ] Controles manuais

---

### Fase 5: Melhorias e Otimizações

**Confiabilidade:**
- [ ] Reconexão automática WiFi
- [ ] Reconexão automática MQTT
- [ ] Watchdog timer
- [ ] Log de erros em arquivo

**Performance:**
- [ ] Otimizar uso de memória
- [ ] Deep sleep entre leituras (opcional)
- [ ] Buffer de mensagens MQTT

**Segurança:**
- [ ] MQTT com autenticação (quando usar broker local)
- [ ] Criptografia TLS (opcional)

---

## 📝 Notas Importantes

### Valores Simulados
Atualmente, os sensores retornam valores aleatórios para teste:
- **Temperatura:** 20-30°C (função `random.uniform()`)
- **Humidade:** 40-80% (função `random.uniform()`)
- **Solo:** 200-800 (função `random.randint()`)
- **Luz:** 100-900 (função `random.randint()`)

**Motivo:** Permite testar toda a lógica do sistema antes de ter os sensores físicos.

### Broker Público
Usando `test.mosquitto.org` por causa do Firewall corporativo.

**Para apresentação final:**
- Usar laptop pessoal
- Instalar Mosquitto local
- Mudar `config.py` para `MQTT_BROKER = "192.168.1.X"`

### Automação
Threshold de temperatura (28°C) é configurável em `config.py`.

---

## 💡 Lições Aprendidas

1. **Arquitetura Modular:** Separar configurações do código facilita manutenção
2. **Boot Automático:** `boot.py` torna o sistema mais profissional
3. **Indicadores Visuais:** LEDs ajudam muito no debug
4. **Tratamento de Erros:** `try/except` em todos os lugares críticos é essencial
5. **Testes Incrementais:** Valores simulados permitem testar lógica antes do hardware

---

## 📊 Status do Projeto Geral

### Concluído (60%)
- ✅ Arquitetura definida
- ✅ Hardware escolhido e configurado
- ✅ Ambiente de desenvolvimento
- ✅ Sistema de boot e configuração
- ✅ Loop principal funcional
- ✅ Integração MQTT completa
- ✅ Automação básica implementada
- ✅ Testes de sistema realizados

### Em Progresso (0%)
- Nenhuma tarefa em andamento (pausa)

### Pendente (40%)
- ⏳ Sensores reais (DHT22, Solo, LDR, NFC)
- ⏳ Base de dados
- ⏳ Dashboard de monitorização
- ⏳ Documentação final
- ⏳ Apresentação

---

## 🎉 Conquistas desta Sessão

✨ **Sistema IoT Completo e Funcional!**
- Boot automático
- Conectividade WiFi estável
- Publicação MQTT contínua
- Automação funcionando
- Código limpo e organizado
- Tratamento de erros robusto

**O projeto está muito avançado!** 🚀

---

**Próxima Sessão:** Implementar sensores reais (DHT22, Solo, LDR)  
**Tempo Estimado:** 2-3 horas  
**Pré-requisito:** Ter os componentes físicos disponíveis  

---

**Última Atualização:** 26/12/2025  
**Tempo Total Investido:** ~6 horas (2 sessões)  
**Progresso Geral:** 60% ✅