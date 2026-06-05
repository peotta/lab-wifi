# Lab WiFi 2 - Associação, Autenticação e Captura de Handshake

**Disciplina:** ENE0025 - Protocolos de Transporte e Roteamento  
**Curso:** Engenharia de Redes de Comunicação  
**Professor responsável:** Prof. Dr. Laerte Peotta de Melo  
**Ambiente:** Laboratório presencial com notebook Linux e adaptador Wi-Fi compatível  
**Tema:** Associação controlada, autenticação e observação do processo de acesso em redes IEEE 802.11

---

## Objetivo

Compreender, observar e registrar o processo de **entrada de um cliente em uma WLAN**, com foco em:

- descoberta da rede;
- autenticação e associação;
- captura e identificação do **4-way handshake**;
- diferenciação entre quadros de gerenciamento e quadros de dados;
- interpretação básica dos eventos que ocorrem quando uma estação se conecta a uma rede protegida.

Este laboratório deve ser conduzido **exclusivamente em ambiente autorizado da disciplina**, sem ações sobre redes de terceiros.

---

## Introdução teórica

Em redes IEEE 802.11, o acesso de uma estação cliente a uma WLAN não ocorre de forma instantânea. Antes da troca efetiva de dados, há um conjunto de etapas lógicas e operacionais que permitem ao dispositivo descobrir a rede, selecionar um ponto de acesso, autenticar-se, associar-se e estabelecer os parâmetros necessários para comunicação protegida.

Em modo infraestrutura, o **ponto de acesso (AP)** anuncia a rede por meio de **quadros beacon**, que informam SSID, capacidades, canal, parâmetros temporais e elementos relacionados à segurança. A estação cliente, ao detectar esses anúncios ou ao realizar sondagens ativas, decide se irá iniciar a conexão. A partir daí, entram em cena os mecanismos de **autenticação** e **associação**, que formalizam a entrada do cliente no conjunto básico de serviço.

Quando a rede utiliza **WPA2-Personal** ou mecanismo equivalente, o ingresso do cliente inclui a execução do chamado **4-way handshake**, procedimento criptográfico utilizado para confirmar posse das credenciais corretas e derivar chaves de sessão para proteger o tráfego subsequente. Em termos didáticos, esse processo é fundamental porque mostra ao estudante que a segurança Wi-Fi não se resume à existência de uma senha: ela depende de uma sequência estruturada de trocas entre cliente e AP, com funções específicas de autenticação e estabelecimento de chaves.

Do ponto de vista de segurança e operação, observar esse processo é importante por pelo menos quatro razões. Primeiro, permite compreender como o acesso legítimo ocorre em uma WLAN moderna. Segundo, ajuda a distinguir problemas de autenticação, associação e conectividade IP. Terceiro, oferece base conceitual para troubleshooting em redes sem fio. Quarto, introduz o estudante à análise operacional de eventos 802.11 a partir de evidências em captura.

Neste laboratório, a proposta é **visualizar e interpretar** esse processo em um ambiente controlado, focando a captura e a leitura dos eventos, sem exploração ofensiva e sem uso indevido fora do contexto acadêmico autorizado.

---

## Situação-problema

Uma equipe de redes precisa validar se o processo de conexão de clientes a uma WLAN protegida está ocorrendo corretamente. Alguns usuários relatam dificuldade para entrar na rede, enquanto outros se conectam normalmente.

Antes de alterar configurações do AP, a equipe decide observar tecnicamente o processo de associação e autenticação, verificando:

- se o cliente encontra a rede correta;
- se o AP responde adequadamente;
- se o handshake é concluído;
- se a troca de quadros de gerenciamento ocorre de forma esperada.

---

## Competências desenvolvidas

- Identificar o fluxo básico de associação em redes 802.11.
- Diferenciar descoberta, autenticação, associação e troca de chaves.
- Capturar tráfego de gerenciamento em ambiente sem fio.
- Localizar e reconhecer quadros do handshake em ferramentas de análise.
- Relacionar eventos observados com funcionamento e segurança da WLAN.

---

## Requisitos

- Notebook com Linux (Kali, Parrot, Ubuntu ou Debian).
- Adaptador Wi-Fi com suporte a modo monitor.
- Ambiente de laboratório autorizado.
- Um AP de teste configurado pela disciplina.
- Um cliente de teste autorizado.
- Ferramentas instaladas:
  - `ip`
  - `iw`
  - `iwconfig`
  - `nmcli`
  - `airmon-ng`
  - `airodump-ng`
  - Wireshark
  - `journalctl` ou logs do NetworkManager

> Todas as atividades devem ocorrer somente em infraestrutura de laboratório autorizada.

---

## Topologia lógica

```mermaid
flowchart LR
    AP["Ponto de Acesso<br/>SSID: LAB-WIFI-SEG<br/>WPA2/WPA3"]
    MON["Notebook de monitoramento<br/>Linux + modo monitor"]
    STA["Cliente de teste<br/>notebook / smartphone"]
    LAN["Infraestrutura local<br/>switch / roteador / internet"]

    AP --- LAN
    STA -. autenticação / associação .- AP
    MON -. captura passiva 802.11 .- AP
    MON -. observação dos eventos .- STA

    classDef ap fill:#dbeafe,stroke:#1d4ed8,color:#111827,stroke-width:1.5px;
    classDef mon fill:#fef3c7,stroke:#d97706,color:#111827,stroke-width:1.5px;
    classDef sta fill:#dcfce7,stroke:#16a34a,color:#111827,stroke-width:1.5px;
    classDef lan fill:#e9d5ff,stroke:#7e22ce,color:#111827,stroke-width:1.5px;

    class AP ap;
    class MON mon;
    class STA sta;
    class LAN lan;
```

---

## Conceitos essenciais

### Estação (STA)
Dispositivo cliente que tenta entrar na rede Wi-Fi.

### AP (Access Point)
Equipamento que anuncia a rede e coordena o acesso sem fio.

### Autenticação
Etapa inicial de validação entre cliente e AP, antes da associação completa.

### Associação
Processo pelo qual a estação passa a integrar logicamente a WLAN.

### EAPOL
Conjunto de quadros usado na troca relacionada ao processo de autenticação/chaves em redes protegidas.

### 4-way handshake
Sequência de quatro mensagens usada para derivar e confirmar chaves de sessão em redes WPA2/WPA3-Personal.

### Beacon
Quadro periódico de gerenciamento enviado pelo AP para anunciar a rede.

### Probe
Quadro usado para descoberta ativa de redes Wi-Fi.

---

## Premissas do laboratório

Considere que:

- existe um AP de teste previamente configurado;
- a senha da rede será fornecida pelo professor;
- o cliente autorizado poderá desconectar e reconectar à rede para fins de observação;
- o objetivo é **capturar e interpretar** o processo, e não atacar a rede.

---

## Etapa 1 - Identificação da interface sem fio

Listar interfaces:

```bash
ip link show
```

ou

```bash
iw dev
```

Verificar detalhes:

```bash
iwconfig
```

Identificar a interface Wi-Fi de captura, por exemplo:

- `wlan0`
- `wlp2s0`

---

## Etapa 2 - Ativação do modo monitor

Verificar interfaces disponíveis:

```bash
sudo airmon-ng
```

Ativar modo monitor:

```bash
sudo airmon-ng start wlan0
```

Verificar o novo nome da interface:

```bash
iw dev
```

Em muitos cenários, surgirá algo como:

- `wlan0mon`

---

## Etapa 3 - Descoberta do AP de interesse

Capturar o ambiente:

```bash
sudo airodump-ng wlan0mon
```

Identificar:

- SSID do laboratório;
- BSSID do AP;
- canal utilizado;
- tipo de segurança anunciado.

Anotar os dados para uso nas próximas etapas.

### Exemplo de dados a registrar

| Campo | Valor |
|---|---|
| SSID | LAB-WIFI-SEG |
| BSSID | AA:BB:CC:DD:EE:FF |
| Canal | 6 |
| Segurança | WPA2-PSK / WPA3 |

---

## Etapa 4 - Foco no AP específico

Monitorar somente o AP do laboratório:

```bash
sudo airodump-ng --bssid AA:BB:CC:DD:EE:FF --channel 6 wlan0mon
```

> Substitua pelo BSSID e canal reais.

### Objetivo
Reduzir ruído e concentrar a captura no AP relevante.

---

## Etapa 5 - Captura do handshake em arquivo

Salvar a captura:

```bash
sudo airodump-ng --bssid AA:BB:CC:DD:EE:FF --channel 6 --write captura_labwifi2 wlan0mon
```

Arquivos esperados:

- `captura_labwifi2-01.cap`
- arquivos auxiliares `.csv` / `.netxml`, dependendo da ferramenta

### Objetivo
Registrar a reconexão do cliente e salvar os quadros para análise posterior.

---

## Etapa 6 - Reconexão controlada do cliente de teste

No cliente autorizado, solicitar uma **desconexão e reconexão legítima** à rede do laboratório.

Exemplos possíveis no Linux:

```bash
nmcli connection down "LAB-WIFI-SEG"
nmcli connection up "LAB-WIFI-SEG"
```

ou desabilitar e habilitar o Wi-Fi:

```bash
nmcli radio wifi off
nmcli radio wifi on
```

Em smartphone ou outro notebook, o procedimento pode ser feito manualmente, sempre no ambiente autorizado.

### Resultado esperado
Durante a reconexão, o monitor deve registrar:

- quadros de descoberta;
- autenticação;
- associação;
- quadros EAPOL do handshake.

---

## Etapa 7 - Verificação visual do handshake no airodump-ng

Durante a captura, o `airodump-ng` poderá indicar handshake detectado.

Exemplo de indicação visual:

- `WPA handshake: AA:BB:CC:DD:EE:FF`

### Observação
Mesmo que a ferramenta mostre a indicação visual, a validação didática principal deve ser feita no **Wireshark**, com inspeção dos quadros.

---

## Etapa 8 - Análise no Wireshark

Abrir o arquivo `.cap` no Wireshark.

Filtros úteis:

### Beacons

```text
wlan.fc.type_subtype == 8
```

### Probe requests/responses

```text
wlan.fc.type_subtype == 4 || wlan.fc.type_subtype == 5
```

### Autenticação e associação

```text
wlan.fc.type_subtype == 11 || wlan.fc.type_subtype == 0 || wlan.fc.type_subtype == 1
```

### EAPOL

```text
eapol
```

### O que identificar

- beacon do AP;
- possíveis probes;
- autenticação;
- associação;
- os quatro quadros principais do handshake, quando presentes.

---

## Etapa 9 - Registro dos quadros observados

Preencha a tabela abaixo com base na captura.

| Evento observado | Encontrado? | Evidência / Observação |
|---|---|---|
| Beacon do AP |  |  |
| Probe Request |  |  |
| Probe Response |  |  |
| Authentication |  |  |
| Association Request |  |  |
| Association Response |  |  |
| EAPOL 1 |  |  |
| EAPOL 2 |  |  |
| EAPOL 3 |  |  |
| EAPOL 4 |  |  |

---

## Etapa 10 - Interpretação didática do processo

### Sequência simplificada esperada

1. o AP anuncia a rede por beacon;
2. o cliente identifica a rede;
3. o cliente inicia autenticação;
4. cliente e AP realizam associação;
5. ocorre o 4-way handshake;
6. a estação passa a trocar dados protegidos.

### Resultado esperado
O aluno deve compreender que o acesso à WLAN protegida depende de um fluxo ordenado de eventos, e que problemas em qualquer etapa podem impedir a conexão.

---

## Diferenciação entre tipos de quadros

### Quadros de gerenciamento
Usados para anunciar, descobrir, autenticar e associar.

Exemplos:
- beacon;
- probe request;
- probe response;
- authentication;
- association request;
- association response.

### Quadros de controle
Auxiliam a coordenação do uso do meio.

### Quadros de dados
Transportam tráfego útil após a conexão estar estabelecida.

---

## Atividade complementar opcional

No cliente Linux, acompanhar logs de conexão:

```bash
journalctl -fu NetworkManager
```

ou

```bash
sudo dmesg -w
```

### Objetivo
Correlacionar:
- evento observado na captura;
- evento registrado pelo sistema operacional.

---

## Questões para análise

1. Qual a diferença entre autenticação e associação em uma WLAN?
2. O que é o 4-way handshake?
3. Qual o papel dos quadros beacon no processo de entrada na rede?
4. Por que a captura deve ser feita no canal correto do AP?
5. O que o filtro `eapol` permite observar?
6. Todo processo de reconexão gera os mesmos quadros exatamente na mesma ordem? Explique.
7. Qual a diferença entre um quadro de gerenciamento e um quadro de dados?
8. Por que o handshake é relevante do ponto de vista de segurança?
9. Um cliente pode ver o SSID da rede sem estar associado a ela? Explique.
10. Qual foi a principal evidência prática de que o cliente entrou corretamente na rede?

---

## Boas práticas observadas no laboratório

- trabalhar apenas em rede autorizada;
- usar cliente de teste controlado;
- registrar canal, BSSID e horário da captura;
- evitar tráfego desnecessário durante a observação;
- correlacionar evidência de captura com comportamento do cliente.

---

## Critérios de avaliação

| Critério | Pontos |
|---|---:|
| Identificação correta da interface e ativação do modo monitor | 1,5 |
| Identificação do AP, canal e BSSID corretos | 1,5 |
| Captura adequada do processo de reconexão | 2,0 |
| Localização dos quadros no Wireshark | 2,0 |
| Preenchimento da tabela de eventos | 1,5 |
| Interpretação técnica do handshake | 1,5 |

**Total: 10,0**

---

## Entregáveis

Cada aluno ou dupla deve entregar:

- print do `airodump-ng` focado no AP do laboratório;
- print ou evidência do arquivo de captura;
- print do Wireshark com filtro `eapol`;
- tabela de eventos preenchida;
- resposta das questões analíticas;
- conclusão curta sobre o comportamento observado.

---

## Modelo de conclusão esperada

Ao final do laboratório, o estudante deve ser capaz de concluir algo como:

> Foi possível observar o processo de entrada de um cliente em uma WLAN protegida, incluindo quadros de gerenciamento e a troca relacionada ao handshake. A análise evidenciou que a conexão sem fio depende de descoberta, autenticação, associação e estabelecimento de chaves, e que a captura desses eventos é útil tanto para compreensão da segurança quanto para troubleshooting de redes Wi-Fi.

---

## Encerramento do modo monitor

Ao final da atividade:

```bash
sudo airmon-ng stop wlan0mon
sudo systemctl restart NetworkManager
```

> Ajuste o nome da interface, se necessário.

---
