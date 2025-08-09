Projeto de Estudo: Monitoramento com Prometheus e Grafana

Este post é um guia para um pequeno projeto usando o Grafana e o Pometheus para fazer monitoramento de recursos de uma maquina vitural aqui estou usando um Virtual Box com Ubuntu 24.04 (CPU, memória, disco, etc.) de uma máquina virtual com sistema operacional Linux.
Antes de começar, certifique-se de que:

O Docker está instalado e funcionando com permissões de root (usei o modo root com cd ~).

As portas 9100, 9090 e 3000 estão liberadas:

  -  9100 – para o Node Exporter

  -  9090 – para o Prometheus

  - 3000 – para o Grafana


🔍 O que são essas ferramentas?

1. Node Exporter
Coletor de métricas que roda em servidores Linux e expõe informações do sistema operacional — como uso de CPU, memória, disco e rede — para que o Prometheus possa coletar e armazenar esses dados.

Como funciona:
  Roda como um serviço no servidor.
  Expõe métricas via HTTP na porta 9100.
  O Prometheus coleta essas métricas periodicamente.
  Os dados são armazenados e visualizados em dashboards como o Grafana.

2. Prometheus

Ferramenta de monitoramento e coleta de métricas, amplamente usada em ambientes de infraestrutura em nuvem e containers.

Como funciona:
  Coleta dados de endpoints HTTP que expõem métricas.
  Armazena essas métricas com carimbo de tempo.
  Permite consultas, visualizações e configuração de alertas.

3. Grafana

Plataforma open source para visualização de dados em tempo real. Transforma métricas em gráficos interativos e dashboards personalizados.

Como funciona:

    Conecta-se a uma ou mais fontes de dados.

    Cria dashboards com gráficos, tabelas e indicadores.

    Configura alertas e filtros.

    Compartilha painéis com sua equipe ou exibe em telas públicas.

📦 Instalação e Configuração
1. Instalar o Docker

Siga este [Guia](https://livro.descomplicandodocker.com.br/chapters/chapter_03.html)
```bash
docker run -d \
  --name node-exporter \
  --net host \
  --pid host \
  -v /:/host:ro \
  quay.io/prometheus/node-exporter:latest \
  --path.rootfs=/host
```
Verifique se está funcionando: 
