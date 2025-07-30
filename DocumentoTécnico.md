 *Autor*: Pedro Simplicio
 *Data:* 27/07/2025  
 *Versão:* 1.0

## Sumário Executivo
Durante a análise da infraestrutura de rede simulada via Docker, foi realizado um mapeamento completo das três sub-redes (infraestrutura, corporativa e a de convidados). Foram identificados ativos com e sem portas abertas, inconsistências em alguns nomes de host, e serviços que apresentam risco potencial devido à exposição de portas sensíveis ou ausência de segmentação adequada. Recomendam-se ações imediatas em serviços expostos desnecessariamente, reforço de segmentações e adoção de boas práticas de hardening.

## Objetivo
Este documento tem como objetivo apresentar o mapeamento da rede fornecida no laboratório Docker, identificar falhas de exposição e segmentação, e propor um plano de ação com foco 80/20 visando mitigar rapidamente os maiores riscos identificados.

## Escopo
A análise abrange as sub-redes:

   infra_net – 10.10.50.0/24  
   corp_net – 10.10.30.0/24  
   guest_net – 10.10.40.0/24

## Metodologia
As ferramentas e abordagens utilizadas incluíram:

   Nmap (para descoberta de hosts, detecção de SO e scan de portas)
   OSINT (reconhecimento passivo de padrões nos hostnames)
   Organização manual dos dados levantados
   Avaliação qualitativa de exposição baseada em melhores práticas de segurança


## Recomendações

  Desativar ou isolar hosts silenciosos se não forem necessários, reduzindo superfície de ataque e confusão operacional.
  Desabilitar SMB em estações que não exigem compartilhamento de arquivos porque mitiga os vetores clássicos de ataque internos (ex: EternalBlue).
  Implementar segmentação via firewall entre redes guest e infra para proteger ativos críticos de exposição acidental à rede de visitantes.
  Aplicar política de nomenclatura para os hosts já que a padronização facilita controle de ativos e resposta a incidentes.

## Plano de Ação (80/20)

| Ação                                                  | Impacto | Facilidade | Prioridade |
|-------------------------------------------------------|---------|------------|------------|
| Bloquear tráfego da rede guest_net para infra_net     | Alto    | Alta       | Alta       |
| Desabilitar SMB nas estações da corp_net              | Alto    | Média      | Alta       |
| Revisar existência e necessidade de hosts silenciosos | Médio   | Alta       | Média      |
| Aplicar padrão de nomenclatura nos hosts              | Baixo   | Alta       | Baixa      |

## Conclusão
A rede apresenta exposição desnecessária em alguns serviços e falhas de segmentação. Reforços básicos de segurança e uma boa política de organização de ativos já trariam grande melhoria com esforço relativamente baixo. A aplicação do plano de ação proposto permite elevar significativamente a maturidade de segurança da rede com foco nos principais riscos detectados.

## Anexos
  ## Inventário de Ativos

Este anexo apresenta o inventário de ativos identificados e utilizados no escopo da análise. Foram considerados todos os dispositivos visíveis na rede durante os testes de varredura e diagnóstico, bem como os que foram adicionados na simulação para fins ilustrativos.

Cada ativo está descrito com as seguintes informações, quando disponíveis:

- Nome do host ou identificador
- Endereço IP
- Sistema operacional (quando detectado)
- Função na rede (ex.: servidor, estação de trabalho, roteador)
- Status de atividade durante os testes (ativo, silencioso, inacessível)

Este inventário serve como referência para correlacionar os resultados obtidos nas análises de conectividade, varredura de portas e detecção de serviços.

![InventariodeAtivos](a/DesafioFinal/InventariodeAtivos.md)

## Diagrama de Rede

O diagrama da rede foi desenvolvido no Cisco Packet Tracer com o objetivo de representar visualmente a estrutura da rede abordada neste projeto. Essa simulação não incluiu o tráfego real ou a configuração funcional de serviços, sendo utilizada apenas como ilustração topológica dos dispositivos, segmentos e conexões analisados nos testes de diagnóstico e varredura.

![DiagramadaRede](a/img/DesafioFinal/DiagramadaRede.png)
 
 
 
## Saídas dos scans do Nmap
  Scripts e comandos utilizados (disponível sob demanda)
  A seguir estão listados os comandos efetivamente utilizados para realizar a descoberta de hosts, escaneamento de portas e análise de serviços nas sub-redes da infraestrutura simulada.

1. Teste de Conectividade (Ping Manual)

ping
Utilizado para verificar se os hosts estavam ativos e responsivos a ICMP.

2. Scan Inicial de Sub-rede (infra_net)

nmap -sS -p- -T4 -n 10.10.50.0/24
Scan SYN completo (-sS) de todas as portas TCP (-p-), com performance acelerada (-T4), sem resolução DNS (-n), aplicado à sub-rede de infraestrutura (infra_net).
Objetivo: detectar hosts ativos e identificar comportamentos silenciosos.

3. Scan Individual Profundo de Hosts

nmap -A -sV 10.10.X.X
Aplicado host a host para coleta de detalhes avançados:

-A: detecção de SO, traceroute e scripts.

-sV: versão dos serviços identificados.

Utilizado especialmente nos ativos suspeitos, como o 10.10.30.3 com SMB exposto.

4. Scan Completo da Rede Corporativa (corp_net)

nmap -sS -sV -O -T4 -Pn -p- 10.10.30.0/24
Varredura completa na sub-rede corporativa:

-sS: SYN scan

-sV: detecção de versões dos serviços

-O: detecção de sistema operacional

-T4: maior velocidade

-Pn: ignora ping (força o scan mesmo que ICMP esteja bloqueado)

-p-: todas as portas TCP

Objetivo: identificar serviços expostos e potenciais vetores de ataque.
