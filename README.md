#  Gateway IT/OT: Conversão de HTTP/XML para Modbus TCP

![Node-RED](https://img.shields.io/badge/Node--RED-%238F0000.svg?style=for-the-badge&logo=node-red&logoColor=white)
![XML](https://img.shields.io/badge/XML-00599C?style=for-the-badge&logo=xml&logoColor=white)
![Modbus](https://img.shields.io/badge/Modbus-FF8C00?style=for-the-badge)
![Schneider Electric](https://img.shields.io/badge/Schneider_Electric-3DCD58?style=for-the-badge&logo=schneiderelectric&logoColor=black)

[![Repositório no GitHub](https://img.shields.io/badge/-Reposit%C3%B3rio_no_GitHub-0D1117?style=for-the-badge&logo=github&logoColor=white)](https://github.com/MichelDiego/XML-Node-Red-e-EBO)&nbsp;
[![Google Drive](https://img.shields.io/badge/-Documenta%C3%A7%C3%A3o_do_Projeto_(Google_Drive)-0D1117?style=for-the-badge&logo=googledrive&logoColor=34A853)](https://drive.google.com/drive/u/1/folders/1N2tnpOpwcRsQfyTwCerbXTq-GGT0abVG)

Este repositório contém o fluxo de integração desenvolvido no **Node-RED** para atuar como um Gateway de software (IT/OT). O projeto foi desenhado e implementado em campo para resolver problemas crônicos de interoperabilidade e gargalos de comunicação entre equipamentos proprietários (que expõem dados via web/XML) e sistemas centrais de Automação Predial (BMS), com foco na integração com o **Schneider EcoStruxure Building Operation (EBO)**.

---

##  O Problema

Em sistemas de Automação Predial (BMS), a comunicação direta e massiva via Modbus com múltiplos equipamentos de infraestrutura pode gerar sérios gargalos de tráfego e latência na rede principal do sistema central. 

Além disso, muitos desses dispositivos disponibilizam suas variáveis operacionais apenas por interfaces web ou arquivos XML proprietários (`.dxml`), limitando a estabilidade da leitura direta por redes tradicionais de automação e forçando, muitas vezes, a compra de hardwares conversores caros.

---

##  A Solução (Gateway em Software)

Em vez de depender de gateways físicos comerciais de alto custo (CAPEX) ou reestruturação física de rede, este fluxo utiliza o **Node-RED** como um middleware leve para atuar como uma ponte bidirecional entre as camadas de TI (HTTP/XML) e TA (Modbus TCP). 

O sistema realiza a varredura assíncrona dos dados web, trata as informações de forma isolada e as disponibiliza localmente em um servidor Modbus dedicado, aliviando completamente a rede principal.

---

##  Arquitetura do Fluxo (Node-RED)

O pipeline de dados opera em um ciclo contínuo e estável de 20 segundos:

1. **Trigger (Inject):** Um gatilho temporal inicia o ciclo de varredura a cada 20 segundos.
2. **Polling de Dados (HTTP GET):** O sistema faz uma requisição diretamente ao IP do equipamento (ex: `http://192.168.x.xx/MonVariaveis.dxml`) para capturar o arquivo de variáveis atualizado, reduzindo o *broadcast* e as requisições diretas na rede do supervisório.
3. **Parsing e Tratamento (XML Node):** O *payload* em formato XML bruto é convertido estruturalmente para um objeto JavaScript (JSON) mapeável e manipulável.
4. **Servidor Modbus TCP (Modbus-Server):** O Node-RED instancia nativamente um servidor Modbus TCP operando na porta `10502`. As variáveis tratadas são injetadas sequencialmente nos registradores virtuais (*Holding Registers*) deste servidor para consumo otimizado pelo EBO.

---

##  Impacto da Implementação em Campo

* **Fim dos Gargalos de Rede:** O sistema supervisório (EBO) passou a ler os dados em tempo real através de um servidor local de forma limpa, estável e sem latência.
* **Redução Drástica de Custos:** Eliminação total da necessidade de aquisição, instalação e manutenção de hardwares conversores de protocolo ou roteadores adicionais.
* **Escalabilidade Ilimitada:** O fluxo pode ser facilmente replicado ou ajustado para ler múltiplos IPs e disponibilizar centenas de variáveis simultaneamente sem perda de performance.

---

##  Tecnologias Utilizadas

* **Node-RED** (Core de processamento e Fog Computing)
* **node-red-contrib-modbus** (Biblioteca para instanciação do servidor TCP)
* **HTTP/XML** (Camada de extração de dados - TI)
* **Modbus TCP/IP** (Camada de disponibilização de dados - TA/OT)

---

##  Instruções de Uso

Para replicar este ambiente e rodar o Gateway em seu servidor local:

1. Importe o arquivo `.json` contido neste repositório para o seu ambiente Node-RED.
2. Certifique-se de que a biblioteca `node-red-contrib-modbus` (versão `5.45.2` ou superior) está instalada em sua paleta *(Manage Palette)*.
3. Altere o endereço IP no nó **HTTP GET** para o IP do equipamento físico que você deseja monitorar na sua rede local.
4. Configure o seu cliente Modbus (EBO, CLP ou software SCADA) para ler o IP do servidor onde o Node-RED está rodando, apontando para a porta `10502`.

---

##  Demonstração

<img width="1408" height="768" alt="1780765775045 (1)" src="https://github.com/user-attachments/assets/fa9f8fcf-ad89-451d-b3fb-aaaeb9cc49cf" />


---

##  Autor

**Michel Coutinho** Tecnólogo em Automação Industrial | Eletromecânica  
Focado na convergência IT/OT e Automação Predial (BMS).
