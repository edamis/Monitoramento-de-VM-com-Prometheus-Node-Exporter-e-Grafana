📊 Projeto de Estudo: Monitoramento com Prometheus e Grafana

Aqui vai uma pequena dica para quem está iniciando no mundo do monitoramento com Grafana e Prometheus. Este post é um guia de estudo para um projeto simples de monitoramento de recursos (CPU, memória, disco, etc.) de uma máquina virtual com sistema operacional Linux.

🔧 Como pré-requisitos, é indicado que o Docker esteja instalado na máquina, com acesso root aos containers (utilizei o modo root com cd ~). Também será necessário garantir permissões para as seguintes portas:

- ✅ O Docker está instalado e funcionando corretamente
- ✅ Você tem acesso root à máquina (utilizei `cd ~` como root)
- ✅ As seguintes portas estão liberadas:

| Serviço        | Porta  |
|----------------|--------|
| Node Exporter  | 9100   |
| Prometheus     | 9090   |
| Grafana        | 3000   |

Ok o que é cada ferramenta?

🧩 Node Exporter

Coletor de métricas para sistemas Linux. Expõe informações como uso de CPU, memória, disco e rede para o Prometheus.

  - Roda como serviço no servidor
  -  Expõe métricas via HTTP na porta 9100
  -  Prometheus coleta essas métricas periodicamente
  -  Os dados são visualizados via Grafana

📡 Prometheus

Ferramenta de monitoramento que coleta métricas de endpoints HTTP e armazena com carimbo de tempo.

  - Coleta dados de serviços como o Node Exporter
  - Armazena métricas para consulta e visualização
  - Permite configurar alertas e dashboards

📈 Grafana

Plataforma open source para visualização de métricas em tempo real.

  - Conecta-se a fontes de dados como Prometheus
  - Cria dashboards interativos e personalizados
  - Permite configurar alertas e compartilhar painéis

Agora que já sabemos o que são e como funcionam os serviços que iremos utilizar, vamos prosseguir com a instalação e configuração no nosso sistema Linux.

🛠️ Instalação e Configuração

Antes de tudo, é necessário instalar o Docker. Você pode seguir [este](https://livro.descomplicandodocker.com.br/chapters/chapter_03.html) guia.

Após isso, abra o terminal com privilégios de administrador (usando sudo) e execute os seguintes comandos para instalar o Docker:
```bash
docker run -d \
  --name node-exporter \
  --net host \
  --pid host \
  -v /:/host:ro \
  quay.io/prometheus/node-exporter:latest \
  --path.rootfs=/host
```

Dica - Verifique se o Node Exporter está rodando:

```bash
curl http://localhost:9100/metrics
```

Após instalar o Node Exporter e validar seu funcionamento com o comando curl, vamos configurar o Prometheus.

Crie um arquivo no diretório local chamado prometheus.yml utilizando o comando nano, e insira os seguintes campos:

```bash
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'node_exporter'
    static_configs:
      - targets: ['<IP_DA_VM>:9100']
```
Obs.: Substitua <IP_DA_VM> pelo endereço da sua VM. Como estou usando uma VM usei o comando ifconfig. 

Agora vamos rodar o container do Prometheus. Para manter a organização, primeiro criaremos um diretório usando o comando mkdir, onde serão armazenados os arquivos de configuração e os dados do serviço. Use o seguinte comando:
```bash
mkdir -p ~/prometheus/{data,conf}
```

Também vamos salvar o arquivo criado na etapa anterior, o prometheus.yml, no diretório ~/prometheus/conf/. Para isso, utilize o comando mv para mover o arquivo do diretório atual para ~/prometheus/conf/

Iremos executar os comandos abaixo para criar o docker com o prometheus com a porta 9090
```bash
docker run -d \
  --name prometheus \
  -p 9090:9090 \
  -v ~/prometheus/conf/prometheus.yml:/etc/prometheus/prometheus.yml \
  prom/prometheus
```

Depois acesse: http://localhost:9090 ao acessar o link iremos verificar o endpoint node_exporter com status "up".

Se você chegou até esta etapa, significa que já concluímos a instalação e configuração do Node Exporter e do Prometheus. Agora, vamos partir para o Grafana, que permitirá visualizar nosso monitoramento de forma mais amigável e eficiente.

Instale e configure o Grafana utilizando os comandos abaixo:

```bash
docker run -d \
  --name grafana \
  -p 3000:3000 \
  grafana/grafana:latest
```

Agora, acesse o Grafana pelo seu navegador utilizando o endereço: http://<IP_DA_VM>:3000 (sim, aquele mesmo IP que usamos no arquivo na etapa anterior). O usuário e a senha padrão são admin — a troca da senha será solicitada no primeiro acesso 😉.

📊 Configurar o Dashboard no Grafana

  - Vá em Configuration > Data Sources > Add data source
  - Escolha Prometheus e configure a URL: http://<IP_DA_VM>:9090
  - Vá em Dashboards > Manage > Import
  - Busque pelo ID 1860 (Node Exporter Full)
  - Ajuste a fonte de dados para “Prometheus” e clique em Import

Pronto! Agora você pode visualizar o consumo de recursos da sua VM em gráficos interativos 😊

🧹 Limpeza (opcional)

Se não quiser mais utilizar o docker e images criadas podemos executar a remoção da seguinte forma 
```bash
	docker system prune -a
```

🙌 Conclusão

Este projeto é uma introdução prática ao monitoramento com Prometheus e Grafana. Ideal para quem quer entender como essas ferramentas funcionam juntas em um ambiente Linux com Docker.

Obrigado por acompanhar!
