
# **Laboratórios de Redes Wi-Fi: Descoberta, Acesso, Hardening e Troubleshooting**

<img width="2752" height="1506" alt="unnamed (1)2" src="https://github.com/user-attachments/assets/26b6a551-bc67-4d2d-bd23-a221a54aeb0a" />




### **Lab WiFi 1 - Reconhecimento do ambiente sem fio**

**Foco:** entender a rede Wi-Fi “no ar”.

**Objetivos**

- identificar SSID, BSSID, canal, largura de canal e banda 2,4/5 GHz;
- diferenciar AP, cliente, beacon, probe e associação;
- usar modo monitor para observar tráfego 802.11.

**Prática**

- colocar a interface em modo monitor;
- listar redes próximas;
- capturar beacons e probes;
- identificar canal do AP e intensidade do sinal.

**Ferramentas**

- `iw`, `ip`, `airmon-ng`, `airodump-ng`, `tcpdump` ou Wireshark.

**Entregáveis**

- tabela com SSID, BSSID, canal, segurança e potência;
- print da captura;
- breve interpretação do ambiente.

---

### **Lab WiFi 2 - Associação, autenticação e captura de handshake**

**Foco:** funcionamento do acesso Wi-Fi protegido.

**Objetivos**

- compreender associação e autenticação;
- capturar o **4-way handshake** do WPA2/WPA3-Personal;
- diferenciar tráfego de gerenciamento, controle e dados.

**Prática**

- monitorar um AP específico;
- observar associação de um cliente;
- capturar handshake durante reconexão controlada;
- abrir a captura no Wireshark e localizar os frames EAPOL.

**Ferramentas**

- `airodump-ng`, Wireshark, `aireplay-ng` apenas em ambiente autorizado.

**Entregáveis**

- arquivo `.cap` ou prints dos pacotes;
- identificação dos 4 frames do handshake;
- explicação curta do papel de cada etapa.

---

### **Lab WiFi 3 - Hardening e configuração segura de uma WLAN**

**Foco:** defesa e boas práticas.

**Objetivos**

- configurar rede Wi-Fi com segurança adequada;
- comparar WPA2 e WPA3;
- analisar impacto de senha fraca, WPS e canal inadequado.

**Prática**

- configurar AP com SSID de teste;
- desabilitar WPS;
- aplicar senha forte;
- testar canais menos congestionados;
- comparar configuração insegura vs segura.

**Itens avaliados**

- escolha do padrão de segurança;
- política de senha;
- segmentação básica, se possível;
- justificativa técnica das decisões.

**Entregáveis**

- checklist de hardening;
- captura ou prints da configuração;
- conclusão sobre o que foi corrigido.

---

### **Lab WiFi 4 - Troubleshooting e análise de segurança Wi-Fi**

**Foco:** diagnóstico e investigação.

**Objetivos**

- investigar problemas de conectividade, sinal e interferência;
- observar eventos suspeitos;
- correlacionar sintomas com causa provável.

**Cenários possíveis**

- canal congestionado;
- sinal fraco;
- cliente desconectando;
- roaming ruim;
- AP falso/laboratorial;
- tentativa de deauth em ambiente controlado.

**Prática**

- medir RSSI;
- verificar canal e sobreposição;
- analisar frames deauth/disassoc;
- comparar comportamento normal e anômalo.

**Ferramentas**

- `airodump-ng`, Wireshark, `wavemon`, `iw dev`, `journalctl`, `nmcli`.

**Entregáveis**

- relatório curto com:
  - sintoma;
  - evidência;
  - causa provável;
  - ação corretiva.

---

## Progressão 

1. **Enxergar a rede**
2. **Entender como o cliente entra**
3. **Configurar com segurança**
4. **Diagnosticar e investigar**



