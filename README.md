#  <span style="color:gray">Desenvolvimento local</span>

Para subir a stack de desenvolvimento
```bash
cd local/
docker-compose up -d
```

Verifique se os tres containers estão no ar 
```bash
docker-compose ps 
```

Para validar se o metrics subiu corretamente, executar o script (O valor inicial é 31, que significa que as metricas não foram populadas ainda):
```bash
while [[ `curl -s localhost:8080/metrics| wc -l` == 31 ]]
do
  sleep 1
  count=$((count + 1))
done
echo "Levou $count segundos para subir as metricas..."
while true
do
  sleep 5
  curl -s localhost:8080/metrics| wc -l
done
```

`Grafana:` localhost:3000

`Prometheus:` localhost:9090

`Exporter:` localhost:8080/metrics

`Arquivo de configuração do exporter:` gitlab-ci-pipelines-exporter.yml

### Diagrama possivel (AWS, poderia ser em qualquer outro player ou até mesmo local)

![Diagrama do Fluxo](img/gitlab.drawio.svg)



### Após subir corretamente a stack de validação, basta começar os testes e desenvolvimento. Nesse caso irá subir os dashboards que configuramos para o cliente `OuriBank`, cujo a documentação esta a seguir

### Para maiores informações de como usar e possibilidades, o projeto que usamos foi:
 > https://github.com/mvisonneau/gitlab-ci-pipelines-exporter
___
#  <span style="color:black">Dashboard Operacional</span>

### Visão Geral
Este dashboard fornece uma visão geral da utilização dos recursos do servidor GitLab, com foco em CPU, memória e espaço em disco.

### Estrutura das Linhas e Painéis

#### Linha 1
- **% Utilização de Memória por período:** Exibe a utilização percentual da memória ao longo do tempo.
- **% de utilização de CPU:** Mostra a utilização percentual da CPU.
- **Utilização de CPU por período:** Apresenta a utilização da CPU por períodos específicos, permitindo a análise de picos e tendências ao longo do tempo.

#### Linha 2
- **Espaço em disco:** Mostra o espaço em disco utilizado no servidor.
- **% Espaço em disco:** Exibe o percentual de espaço em disco utilizado.

### Limitações das Métricas
Da mesma forma que no Dashboard CI/CD, este dashboard também sofre de limitações na visualização de dados por período, especialmente quando o intervalo de tempo é aumentado. Isso pode resultar em valores agregados que não capturam a granularidade completa dos dados históricos, impactando a precisão da análise para períodos longos.

___


#  <span style="color:black">Dashboard CI/CD GitLab</span>

Este Dashboard foi desenvolvido para fornecer uma visão abrangente das operações de CI/CD no ambiente do GitLab. Ele permite identificar rapidamente problemas em pipelines, analisar a eficiência de builds e releases, e monitorar o desempenho de jobs. Com isso, a companhia pode melhorar a qualidade das entregas, reduzir o tempo de desenvolvimento e mitigar riscos associados a falhas em deploys, garantindo um processo de entrega contínua mais robusto e eficiente.

## <span style="color:red">Variáveis</span>

### Variável de Projeto
- **Nome:** `projeto`
- **Descrição:** Esta variável é preenchida automaticamente com os nomes dos projetos do GitLab disponíveis. Ela permite que você filtre os dados exibidos no dashboard para um ou mais projetos específicos.
- **Comportamento:** A variável `projeto` é usada em diversas métricas do dashboard para segmentar as visualizações por projeto. É possível selecionar múltiplos projetos ou todos os projetos, utilizando o valor padrão `$__all`.

### Variável de Repositório
- **Nome:** `repositorio`
- **Descrição:** Filtra os dados por repositório no GitLab.
- **Comportamento:** Usada para selecionar os repositórios e aplicar nas métricas dos dashboards.

### Variável de Environment
- **Nome:** `ENVIRONMENT`
- **Descrição:** Filtra os dados exibidos no dashboard por environments específicos (Deployments realizados utilizando enviroments).
- **Comportamento:** Pode-se selecionar múltiplos environments ou um único, dependendo da análise desejada. Esse campo é importante para avaliar as entregas que utilizam enviroments do Gitlab.

### Variável de Filtro Branch
- **Nome:** `branch`
- **Descrição:** Esta variável permite filtrar os dados exibidos no dashboard por branches específicas. Por padrão, o valor é ".*", o que significa que todas as branches serão consideradas.
- **Uso:** 
  - Para visualizar dados de uma branch específica, como `master`, basta digitar `master`.
  - Para visualizar todas as branches que começam com um determinado prefixo, como `master`, utilize a expressão `master.*`.
  - O valor padrão ".*" coleta todas as branches, permitindo uma visão abrangente das operações CI/CD em todas as branches.

## <span style="color:red">Categorias</span>

### <span style="color:green">1. JOBS \#</span>

#### 1.1 JOBS TOTAL #
- **Descrição:** Exibe o número total de jobs nos projetos especificados.
- **Interpretação:** Um número elevado de jobs pode indicar uma alta carga de trabalho nos pipelines, o que pode impactar os recursos do sistema.

#### 1.2 JOBS FALHAS #
- **Descrição:** Exibe o número total de jobs que falharam.
- **Interpretação:** Um alto número de falhas pode sugerir problemas nos scripts ou na configuração dos jobs.

#### 1.3 JOBS POR PERÍODO #
- **Descrição:** Exibe o número de jobs executados em um período específico.
- **Interpretação:** Permite monitorar a frequência de execução de jobs e identificar possíveis picos de utilização.

#### 1.4 Job com maior tempo de execução
- **Descrição:** Exibe os jobs com maior tempo de execução.
- **Interpretação:** Jobs com maior tempo de execução podem ser candidatos para otimização.

#### 1.5 % JOBS
- **Descrição:** Exibe a porcentagem de jobs bem-sucedidos em comparação com os que falharam.
- **Interpretação:** Um baixo percentual de sucesso pode indicar a necessidade de ajustes no pipeline.

#### 1.6 Jobs com maior índice de falhas
- **Descrição:** Exibe os jobs com maior número de falhas.
- **Interpretação:** Jobs com falhas recorrentes devem ser analisados e corrigidos para melhorar a confiabilidade do pipeline.

#### 1.7 Tempo médio de JOB
- **Descrição:** Mede o tempo médio de execução dos jobs.
- **Interpretação:** Um aumento no tempo médio pode indicar gargalos ou ineficiências que precisam ser investigadas.

#### 1.8 RUNS POR RUNNERS #
- **Descrição:** Exibe o número de jobs executados por cada runner.
- **Interpretação:** Permite monitorar a carga de trabalho de cada runner e identificar possíveis sobrecargas ou quais são mais utilizados.

#### 1.9 TOP jobs com sucesso
- **Descrição:** Lista os jobs que mais tiveram sucesso nas execuções.
- **Interpretação:** Ajuda a identificar quais jobs são mais estáveis e confiáveis.

#### 1.10 JOBS COMPLETADOS COM SUCESSO #
- **Descrição:** Exibe o número de jobs que foram completados com sucesso.
- **Interpretação:** Um número alto de completamentos bem-sucedidos é um indicador positivo da estabilidade do pipeline.

#### 1.11 RUNNING, FAILED OU NÃO COMPLETADOS #
- **Descrição:** Exibe o status atual dos jobs, se estão em execução, falharam ou não foram completados.
- **Interpretação:** Acompanhar esses estados ajuda a garantir que os jobs estão sendo processados corretamente. É possivel acompanhar e ser direcionado diretamente pelo ID da pipeline até a URI referente ao JOB do Gitlab.

#### 1.12 Execuções de jobs por período
- **Descrição:** Exibe o número de execuções de jobs em um período de tempo especificado.
- **Interpretação:** Pode ser usado para avaliar a demanda por execução de jobs ao longo do tempo.

### <span style="color:green">2. PIPELINES \#</span>

#### 2.1 PIPELINES POR PERÍODO #
- **Descrição:** Exibe o número de pipelines executados em um período de tempo específico.
- **Interpretação:** Ajuda a monitorar a frequência de execução de pipelines ao longo do tempo.

#### 2.2 % de falha e sucesso em execução de build
- **Descrição:** Exibe a porcentagem de builds bem-sucedidas em comparação com as falhadas.
- **Interpretação:** Um percentual elevado de falhas pode indicar problemas nos scripts de build ou até mesmo na qualidade de desenvolvimento.

#### 2.3 10 mais stages de build que falharam
- **Descrição:** Lista os 10 stages de build com o maior número de falhas.
- **Interpretação:** Identifica quais stages de build que podem precisar de revisão e otimização.

#### 2.4 PIPELINES TOTAL #
- **Descrição:** Exibe o número total de pipelines executadas.
- **Interpretação:** O número total de pipelines pode refletir a carga geral de trabalho no ambiente CI/CD.

#### 2.5 PIPELINES FALHAS #
- **Descrição:** Exibe o número de pipelines que falharam.
- **Interpretação:** Um número alto de pipelines com falha pode indicar problemas sistêmicos ou de configuração, ou de templates, definição ou qualidade de codigo.

#### 2.6 Tempo médio das Pipelines mais demoradas
- **Descrição:** Exibe o tempo médio de execução das pipelines que levam mais tempo para completar.
- **Interpretação:** Pipelines com tempos elevados podem ser candidatas para otimização.

#### 2.7 Execuções de pipelines por período
- **Descrição:** Exibe o número de pipelines executadas em um período de tempo especificado.
- **Interpretação:** Permite avaliar a demanda por execução de pipelines ao longo do tempo.

### <span style="color:green">3. ENVIRONMENTS \#</span>

#### 3.1 ENVIRONMENTS
- **Descrição:** Exibe os detalhes dos environments, como o nome, ID e status das execuções.
- **Interpretação:** Permite acompanhar os deployments realizados em diferentes environments, exibindo informações relevantes como ids, commits, enviroments, status, etc.

#### 3.2 DEPLOYMENTS POR PERÍODO #
- **Descrição:** Exibe o número de deploys realizados em um período específico.
- **Interpretação:** Ajuda a monitorar a frequência de deploys por periodo de visualização.

#### 3.3 DEPLOYS COM FALHA #
- **Descrição:** Exibe o número de deploys que falharam.
- **Interpretação:** Um alto número de deploys com falha pode indicar problemas no código ou na configuração da infraestrutura.

#### 3.4 DEPLOYS COM SUCESSO #
- **Descrição:** Exibe o número de deploys realizados com sucesso.
- **Interpretação:** Um número alto de deploys bem-sucedidos é um bom indicador da estabilidade do processo de deploy.

#### 3.7 Projetos com maiores atrasos nas entregas (Commits que não foram para deploy)
- **Descrição:** Este painel mostra os projetos com maior quantidade de commits que ainda não foram enviados para o ambiente de deploy.
- **Interpretação:** A presença de muitos commits que ainda não foram para o deploy pode indicar problemas no fluxo de entrega contínua. Isso pode causar atrasos no ciclo de desenvolvimento, afetando o tempo de lançamento de novas funcionalidades ou correções de bugs.

#### 3.7 PROJETOS COM ATRASO (Commits sem deploys)
- **Descrição:** Exibe os projetos que possuem commits que ainda não foram enviados para o ambiente de deploy.
- **Interpretação:** Um alto número de commits sem deploy pode indicar problemas na entrega contínua ou no processo de integração. Isso pode resultar em atrasos na liberação de funcionalidades ou correções, impactando o ciclo de desenvolvimento.

#### 3.5 Tempo médio de Deployment
- **Descrição:** Mede o tempo médio de execução dos deploys.
- **Interpretação:** Um aumento no tempo médio de deploy pode sugerir problemas de performance ou complexidade.

#### 3.6 DEPLOYS POR DEV
- **Descrição:** Exibe a quantidade de deploys realizados por cada desenvolvedor.
- **Interpretação:** Ajuda a identificar quem está mais envolvido no processo de entrega.
