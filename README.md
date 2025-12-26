


#Smart Greenhouse - Projeto Iot (Programação Iot)

2.  OBJETIVOS DO PROJETO 
O objetivo principal consiste no desenho e desenvolvimento de um sistema didático inteligente de gestão de 
estufa que permita demonstrar: 
* Integração de múltiplos sensores em um sistema Iot 
* Comunicação entre dispositivos usando protocolos BLE e NFC 
* Implementação de arquitetura baseada em MOM (Message Oriented Middleware) 
* Automação básica baseada em leituras de sensores 
* Persistência e visualização de dados em tempo real 

3.  REQUISITOS FUNCIONAIS 
3.1.  CONTROLO DE ACESSO (SIMULADO) 
* Simulação de Implementação de sistema abertura de porta através de leitura de TAG NFC 
* Indicação (LED) do acesso concedido/negado 
* Registo de tentaiva de  acesso com Timestasmp 
 
3.2.  SISTEMA DE VENTILAÇÃO AUTOMATIZADO 
* LED conectado ao Raspberry Pi simulando ventilador/exaustor 
* Acionamento automático em função da temperatura ambiente 
 
3.3.  MONITORIZAÇÃO AMBIENTAL 
* Leitura contínua de temperatura ambiente (sensor DHT22) 
* Leitura contínua de humidade do ar (sensor DHT22) 
* Monitorização da humidade do solo (sensor resistivo ou capacitivo) 
* Medição da luminosidade interna através de comunicação BLE (LDR ou sensor digital) 
 
3.4.  PERSISTÊNCIA DE DADOS 
* Serialização e armazenamento de todos os dados dos sensores em bases de dados 
* Estruturação dos dados para permitir análise histórica 

4.  ARQUITETURA DO SISTEMA 
4.1.  COMPONENTES DO PROTÓTIPO 
* Raspberry Pi: Microcontrolador principal com suporte a wifi e BLE 
* Sensor DHT22: Temperatura, humidade (ar, solo), luminosidade 
* Módulo RC522: Leitor NFC/RFID 
* LDR: Sensor de luminosidade 
* LED’s: Indicadores visuais (acesso, ventilação, status) 
* Protoboards e jumpers: Montagem do circuito 
* Resistores: 220 Ôhms para LED’s, 10KÔHMS para LDR 

4.2.  SOFTWARE E MIDDLEWARE 
* Broker MQTT: Broker público 
* Base de dados: Arquivo local 
* Dashboard: Interface web simples 
  
5.  ENTREGÁVEIS 
 
5.1.  DOCUMENTAÇÃO TÉCNICA 
* Diagrama de blocos da solução completa 
* Descrição detalhada da interação entre componentes 
* Especificação dos protocolos de comunicação utilizados 
* Modelo de dados da base de dados 
 
5.2.  IMPLEMENTAÇÃO 
* Scripts Python para leitura de sensores (DHT22, humidade do solo, luminosidade) 
* Script Python para controlo do módulo NFC (RC 522) 
* Cliente MQTT em python para publicação/subscrição de mensagens 
* Script do controlo dos LEDs 
* Script de base de dados SQLite (criação de tabelas e inserção de dados) 
* Ficheiros de configuaração (credenciais wifi, endereços MQTT, GPIO pins) 
 
5.3.  DEMONSTRAÇÃO E CENÁRIOS 
* Protótipo físico montado e funcional 
* Demonstração de todos os requisitos funcionais 
* Simulação de diferentes cenários (temperatura alta, acesso concedido/negado, etc) 
* Breve explicação da arquitetura e decisões técnicas 
 
CENÁRIO 1: Acesso à estufa 
* Aproximar TAG NFC autorizada (LED verde acende/Registo na base de dados) 
* Aproximar TAG não autorizada (LED vermelho acende) 

CENÁRIO 2: Ventilação automática 
* Aumentar temperatura 
* Sistema detecta temperatura > limite configurado (ex.: 20º) 
* LED de ventilação (conectado ao GPIO do Raspberry PI) acende automaticamente 
* Dados publicados no dashboard em tempo real 
 
CENÁRIO 3: Monitorização em tempo real 
* Dashboard mostra todos os valores dos sensores atualizando  
* Gráficos históricos das últimas leituras 
* Indicadores visuais do estado do sistema 
 
CENÁRIO 4: Comando manual 
* Através do Dashboard, acionar/desligar ventilação manualmente 
* Verificar resposta LED no Raspberry PI 
 
 
6.  TECNOLOGIAS E FERRAMENTAS 
 
* 1 x Raspberry Pi 3, 4 ou zero w (com wiFi e Bluetooth integrados) 
* 1 x cartão micro SD (16GB ou superior) com Raspberry PI OS 
* 1 X fonte de aliementação para Raspberry PI 
* 1 x sensor DHT22 
* 1 x sensor de humidade de solo (resistivo ou capacitivo) 
* 1 x módulo RC522 (NFC/RFID) + tags RFID 
* 1 X LDR 
* 3 x LEDs (vermelho, azul e verde) 
* 3 x resistores 220 ôhms 
* 1 x resistor de 10Kôhms 
* 1 x protoboard 
* Jumpers macho-fêmea e macho-macho 
* Cabo HDMI e monitor  
 
7.  CRONOGRAMA DE DESENVOLVIMENTO 
 
1. Fase 1: Estudo e planeamento da arquitetura 
2. Fase 2: Implementação dos sensores e comunicação BLE 
3. Fase 3: Integração com MOM e base de dados 
4. Fase 4: Desenvolvimento do controlo de acesso NFC 
5. Fase 5: Implementação da lógica de automação 
6. Fase 6: Interface de monitorização 
7. Fase 7: Testes integrados e documentação 
8. Fase 8: Demonstração final 

8.  RESULTADO ESPERADO 
 
Ao final do projeto espera-se obter um sistema funcional de estufa inteligente capaz de : 
 
* Aplicação prática de conceitos Iot em ambiente educacional 
* Integração de múltiplos sensores e protocolo de comunicação 
* Funcionamento de arquitetura baseada em MOM 
* Automação básica respondendo a estímulos ambientais 
* Persistência e visualização de dados em tempo real 
* Compreensão dos desafios de desenvolvimento de sistemas Iot 