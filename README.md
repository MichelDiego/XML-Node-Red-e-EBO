# Gateway IT/OT: Conversão de HTTP/XML para Modbus TCP

Este repositório contém o fluxo de integração desenvolvido no Node-RED para atuar como um Gateway de software (IT/OT). O projeto foi implementado em campo para resolver problemas de interoperabilidade entre equipamentos proprietários (que expõem dados via web/XML) e sistemas centrais de Automação Predial (BMS), especificamente focado na integração com o EcoStruxure Building Operation (EBO).

## Demonstração

[![Google Drive](https://img.shields.io/badge/-Documentação_do_Projeto_(Google_Drive)-0D1117?style=for-the-badge&logo=googledrive&logoColor=34A853)](https://drive.google.com/drive/u/1/folders/1N2tnpOpwcRsQfyTwCerbXTq-GGT0abVG)

## O Problema

Muitos equipamentos de infraestrutura expõem suas variáveis operacionais apenas através de interfaces web ou arquivos XML proprietários. Sistemas de gerenciamento predial (BMS/SCADA) legados ou padronizados operam majoritariamente sobre redes de Tecnologia de Automação (TA), exigindo protocolos industriais como o **Modbus TCP** para leitura e controle de dados.

## A Solução (Gateway em Software)

Em vez de depender de gateways físicos comerciais de alto custo, este fluxo utiliza o **Node-RED** para atuar como uma ponte bidirecional entre as camadas de TI e TA. O sistema realiza o *polling* dos dados web, trata as informações e as disponibiliza em um servidor Modbus local.

### ⚙️ Arquitetura do Fluxo (Node-RED)

O pipeline de dados opera em um ciclo contínuo de 20 segundos:

1. **Trigger (Inject):** Um gatilho temporal inicia o ciclo de varredura a cada 20 segundos.
2. **Polling de Dados (HTTP GET):** O sistema faz uma requisição diretamente ao IP do equipamento (ex: `http://192.168.x.xx/MonVariaveis.dxml`) para capturar o arquivo de variáveis atualizado.
3. **Parsing e Tratamento (XML Node):** O payload em formato XML bruto é convertido estruturalmente para um objeto JavaScript (JSON) manipulável.
4. **Servidor Modbus TCP (Modbus-Server):** O Node-RED instancia nativamente um servidor Modbus TCP operando na porta `10502`. As variáveis tratadas são injetadas nos registradores virtuais deste servidor.

## Impacto da Implementação em Campo

* **Interoperabilidade Total:** Permite que o BMS (EBO) leia dados de equipamentos que antes eram "cegos" para a rede de automação principal.
* **Redução de Custos (CAPEX):** Eliminação da necessidade de aquisição, instalação e manutenção de hardwares conversores de protocolo.
* **Escalabilidade:** O fluxo pode ser facilmente replicado ou ajustado para ler múltiplos IPs e disponibilizar centenas de *Holding Registers* simultaneamente.

## Tecnologias Utilizadas

* **Node-RED** (Core de processamento)
* **node-red-contrib-modbus** (Biblioteca para instanciação do servidor TCP)
* **HTTP/XML** (Camada de extração de dados IT)
* **Modbus TCP/IP** (Camada de disponibilização OT)

## Instruções de Uso

1. Importe o arquivo `.json` contido neste repositório para o seu ambiente Node-RED.
2. Certifique-se de que a biblioteca `node-red-contrib-modbus` (versão 5.45.2 ou superior) está instalada em sua paleta.
3. Altere o endereço IP no nó **HTTP GET** para o IP do equipamento físico que você deseja monitorar na sua rede local.
4. Configure o seu cliente Modbus (CLP, EBO ou software SCADA) para ler o IP do servidor onde o Node-RED está rodando, apontando para a porta `10502`.
