# Setta-Challenger
Teste para Estágio.

Monitoramento de Eficiência de Máquina

1. Visão geral

Este projeto foi desenvolvido em Mendix para o desafio técnico de estágio da Setta Digital Labs. A aplicação simula um cenário de monitoramento industrial em que a temperatura atual é obtida por uma API externa, utilizada para calcular a eficiência de uma máquina, armazenada no banco de dados e exibida em uma dashboard com histórico.

A dashboard atualmente contem:

temperatura atual, eficiência atual, data e hora da leitura mais recente, status operacional, botão de atualização manual na data atual, ativar/desativar atualização automatica de 1 em 1 minuto, alertas visuais caso a temperatura supere ou esteja abaixo dos limites, grafico das ultimas 7 leituras e uma tabela contendo todo o historico.

2. Regra da eficiência:

A eficiência na aplicação é calculada conforme a documentação dos requisitos do projeto, seguindo as margens pré definidas de temperatura abaixo ou igual a 21°C = eficiencia em 23%, e em temperaturas maiores ou iguais a 32°C = 100%. Com base nessas informações, foi definida uma formula de calculo de eficiência enquanto a temperatura estiver entre 21° e 32°:

Eficiência = 23 + ((Temperatura - 21) * 7)

Porque a eficiência precisa subir de 23% para 100% em um intervalo de 11°C:

(100 - 23) / (32 - 21) = 7

Ou seja, pegamos o intervalo em graus = 11° e o invervalo em % de eficiência = 77, 77/11 = 7, então a cada grau acima de 21°C, +7% de eficiência. (21° = 23%, 22° = 30%)

Com a logica definida, foi separada em um microflow especifico para este calculo com os dados de temperatura da API OpenWeather.

3. Alertas operacionais

Além da regra obrigatória de eficiência, foi adicionada uma classificação simples de temperatura para destacar condições potencialmente anormais, definidas opcionalmente no desenvolvimento como extra, essas são:

abaixo de 10°C: temperatura abaixo do limite;

entre 10°C e 45°C: condição normal;

acima de 45°C: temperatura acima do limite.

Temperaturas fora desse escopo definido opcionalmente, são consideradas anormais, e encerram a operação da maquina, exibindo o alerta visual na dashboard de Temperatura abaixo ou acima do limite.

4. Tecnologias utilizadas

Mendix Studio Pro

Foi escolhido por permitir desenvolver rapidamente a interface, regras de negócio, persistência e integração REST em uma única plataforma. Também possibilitou organizar a aplicação em microflows pequenos, separados por responsabilidade.

Mendix foi uma escolha visando o aprendizado de uma tecnologia já utilizada na empresa, mesmo sem nenhum conhecimento da ferramenta, o desenvolvimento ocorreu totalmente em mendix, com auxilio de video, guias, documentação e auxilio de A.I para duvidas e erros.

OpenWeather

A aplicação utiliza uma API real do OpenWeather para obter a temperatura atual da localização configurada.

Banco de dados interno do Mendix

Foi utilizado o mecanismo de banco de dados interno do Mendix. O banco é criado e gerenciado automaticamente durante a execução local do projeto, sem necessidade de instalar qualquer aplicação externa ao Mendix.

SCSS/CSS

Foi utilizado para personalizar a dashboard, cards, botões, indicadores e alertas visuais.

5. Organização dos dados

A aplicação utiliza principalmente duas entidades.

- maquina

Representa a máquina monitorada e armazena informações de configuração, como:

nome, cidade, configuração de atualização automática.

- calcMaquina

Representa uma leitura histórica da máquina. Cada nova medição gera um novo registro, preservando as leituras anteriores.

Principais informações armazenadas:

temperatura, eficiência calculada, data e hora da leitura, status da temperatura, e a associação com a máquina correspondente.

A escolha de salvar cada medição como um novo registro permite acompanhar a evolução da eficiência ao longo do tempo sem sobrescrever o histórico.

Dessa maneira também se é possivel em futuras melhorias, adicionar outras demais maquinas, com criterios de calculo alternativos.

<img width="531" height="130" alt="image" src="https://github.com/user-attachments/assets/f82c4051-b46c-4a32-8bdd-f72546b1a123" />

6. Fluxo principal

O fluxo de uma nova leitura é:

Buscar temperatura na API
        ↓
Calcular eficiência
        ↓
Verificar status da temperatura
        ↓
Criar uma nova medição
        ↓
Salvar no banco
        ↓
Atualizar dashboard e histórico. Pode ser observado no microflow flow_Maquina. Caso a chamada à API falhe, o erro é tratado e uma mensagem é exibida ao usuário e a leitura não é gravada no histórico.

7. Atualização das medições

Existem duas formas de obter uma nova medição, Atualização manual, onde o botão Atualizar executa imediatamente o fluxo de coleta, cálculo e salva a informação, e por meio de uma atualização automatica de 1 em 1 minuto, que pode ser ativada/desativada por meio do botão switch "Auto Update"

A implementação atual utiliza um evento associado à página. Portanto, a atualização automática ocorre enquanto a dashboard permanece aberta.

<img width="1910" height="1037" alt="image" src="https://github.com/user-attachments/assets/ca7106d3-b77f-4629-9612-0968c0b1e0fb" />


8. Gráfico e histórico

O gráfico apresenta somente as 7 medições mais recentes, evitando excesso de informação visual.

O processo utilizado é buscar as 7 medições mais recentes pela data, em ordem decrescente, ordenar essas 7 medições em ordem crescente e enviar a lista resultante para o gráfico.

O histórico em tabela mantém as leituras armazenadas e apresenta temperatura, eficiência, data/hora e status com o adendo de filtragem + exclusão das leituras.

9. Dados de demonstração

Foram utilizados alguns registros históricos simulados para demonstrar o comportamento do gráfico e do histórico mesmo antes de existir uma quantidade suficiente de leituras reais, são apenas registros aleatórios para fins de teste e facilitar a demonstração do projeto.

As novas medições realizadas pelo botão Atualizar ou pela atualização automática utilizam a temperatura real retornada pelo OpenWeather no momento da operação.

10. Pré-requisitos

Para executar o projeto localmente:

Mendix Studio Pro (recomendado na versão 11.14.0, utilizada no desenvolvimento do projeto.), conexão com a internet, e uma chave válida da API OpenWeather. Não é necessário instalar ou configurar um banco de dados externo.

Foi utilizado o Widget "Events" do própio marketplace do Mendix, caso haja algum erro.

11. Como executar localmente

Abra o projeto no Mendix Studio Pro.

Configure uma chave válida do OpenWeather no local utilizado pela configuração da requisição REST do projeto, se encontra no Microflow flow_Maquina.

Execute a aplicação utilizando Run Locally.

Abra a dashboard no navegador.

Caso não possua dados, utilize o botão dados para criar 5 leituras pré definidas para serem utilizadas de exemplo, e a partir de agora, ao clicar em atualizar ou ativar a atualização automatica, será gravada e atualizada na dashboard.

O banco de dados local do Mendix é inicializado automaticamente durante a execução.

12. Limitações conhecidas

A aplicação foi desenvolvida como solução de demonstração para o desafio e não como sistema de produção em massa.

As principais limitações atuais são:

A atualização automática depende de a página estar aberta.

A aplicação foi construída principalmente para uma máquina, inicialmente o planejamento era suportar mais de uma maquina, porem para fins de evitar atrasos devido a inexperiencia com a ferramenta, suporta apenas uma maquina, mas com plena capacidade de rapidamente suportar mais maquinas, devido a estrutura.

Os alertas utilizam limites simples e ficticios, podendo ser alterados conforme maquinas reais e sensores reais.

a temperatura do OpenWeather representa a temperatura da localização configurada e não um sensor físico instalado em uma máquina real.

Dashboard com visual simples/limitado devido a falta de experiencia e tempo de aprendizado com Mendix.

Organização do projeto no quesito microflows mais simples e reutilizaveis, conforme o desenvolvimento e aprendizado da ferramente, foi notado pontos de mudança futuros para melhoria, porem sem o devido tempo para executar.

Mais suportes de erros e testes com grandes quantias de dados precisariam ser efetuados.

13. Parte 3 — Raciocínio e visão

Se em vez de 1 máquina fossem 100, enviando leituras a cada 5 minutos, o que precisaria mudar ou poderia travar?

Com 100 máquinas enviando uma leitura a cada 5 minutos, seriam geradas aproximadamente 28.800 leituras por dia. A estrutura atual ainda poderia servir como base funcional, porem como a leitura só é feita com a pagina da aplicação aberta, possivelmente se tornaria um incomodo ou lentidão nos resultados. Creio que um processo de fila tanto para coleta quanto exibição dos dados deveria ser desenvolvido para suportar tamanha quantia de dados, alem de não depender da pagina aberta no navegador para armazenar tais informações. Uma nova dashboard especializada na visualização desses dados poderia ser desenvolvida com a premissa de conseguir um melhor vislumbre da quantia massiva de dados.

Se quiséssemos prever falhas antes que aconteçam, quais dados históricos seriam úteis coletar?

Além de temperatura e eficiência, seria interessante armazenar algo como o ID da máquina, duração de operação, carga de trabalho e registros de alertas. Mais informações sobre a operação da maquina, como carga horaria diaria, e principalmente sensores precisos de partes críticas da maquina, hotspot, vibração e etc, facilitariam bastante na previsão de falhas. Com base nos dados a mais citados, podemos ter um relatorio da quantia de estresse da maquina em um bom escopo de historico, sabendo por quanto tempo operou fora da margem ideal de temperatura ou estado. Armazenar o historico de manutenção e falhas reais da maquina também pode facilitar para relacionar comportamentos atuais da maquina com problemas passados.

Qual melhoria eu faria se tivesse mais tempo?

A principal melhoria seria mover a atualização automática para um processo de backend, utilizando uma tarefa agendada no servidor, para não depender de uma pagina aberta no computador, depois, finalizar o suporte para varias maquinas, suporte para varios sensores, e uma organização de microflows que facilite a manutenção e modificação para melhorias e adaptações da aplicação (padronização de microflows de calculos por exemplo). 

Criterios mais precisos de alertas e a possibilidade de gerar relatorios mais complexos com as informações adicionais como mais sensores, dados da maquina e etc...

14. Resumo das funcionalidades implementadas

integração com OpenWeather;

cálculo de eficiência;

persistência das medições;

leitura mais recente na dashboard;

histórico em tabela;

gráfico de linha;

limite de 7 medições no gráfico;

atualização manual;

atualização automática;

opção para ativar/desativar atualização automática;

alertas visuais de temperatura;

tratamento de erro da API;

interface personalizada com CSS/SCSS.
