# Nomes dos integrantes
- Allan Bastos
- Lucas padilha
- Mateus Carvalho
- Victor Lopes

# 1. Definição do Banco de Dados
Como base de dados, o grupo optou por selecionar uma amostra fornecida pela própria Oracle para o Oracle DB, com documentação disponível no [site da empresa](https://docs.oracle.com/en/database/oracle/oracle-database/21/comsc/installing-sample-schemas.html#GUID-1E645D09-F91F-4BA6-A286-57C5EC66321D). Várias amostras estão disponíveis, cada uma com um propósito de testes diferente, mas que podem ser utilizadas de forma independente. O banco de dados escolhido foi o esquema “Sales History”, que foi projetado para demonstrações com grandes quantidades de dados. Tal escolha se baseou no conteúdo do projeto, que trata sobre visões, [visões materializadas](https://ead.prc.ifsp.edu.br/mod/resource/view.php?id=28236) e desempenho, tornando um esquema grande um candidato ideal para as atividades. O repositório do banco se encontra [no GitHub](https://github.com/oracle-samples/db-sample-schemas/tree/main/sales_history), e a versão utilizada foi a mais recente: 23c. Os testes iniciais com os computadores do laboratório usavam a versão 18c do Oracle DB. Embora a versão da base de dados usada não seja oficialmente compatível com a versão instalada do banco (18c), os testes realizados pelo grupo concluíram que os scripts podem ser executados sem problemas. A versão mais nova foi escolhida no lugar da versão oficialmente compatível com o Oracle DB mais antigo por duas razões: na versão mais antiga do banco, todos os esquemas de amostras precisam ser instalados juntos (já na mais nova cada um é independente), e o processo de instalação antigo é demorado e trabalhoso, precisando também de alterações manuais para cada computador onde os esquemas precisam ser instalados.
No final, levar em consideração a versão do Oracle DB compatível com os computadores do laboratório tornou-se irrelevante, algo explicado melhor mais adiante.
Por ser uma amostra com uma quantidade de dados extremamente volumosa, a parte de carregamento de dados é dividida em dois formatos: uma parte é inserida no próprio arquivo SQL, e outras dependem de arquivos CSV que vêm inclusos ao realizar o download do repositório da amostra.

# 2. Análise da Viabilidade
Quanto a análise da viabilidade, no dia 08/05/2023, foi realizado um teste de instalação do banco nos computadores do laboratório. O principal problema encontrado foi uma incompatibilidade da versão mais recente (21c) do Oracle DB com o Windows 7 instalados nas máquinas.
Foi preciso recorrer a uma [versão bem mais antiga do banco (18c)](https://www.oracle.com/database/technologies/xe18c-downloads.html). Apesar da demora na instalação, a princípio obteve-se êxito. Outro detalhe importante é que o Oracle DB Express (a versão gratuita utilizada pelo grupo) não acompanha um SGBD, sendo necessário baixar o [Oracle SQLDeveloper](https://www.oracle.com/database/sqldeveloper/technologies/download/) a parte, ou usar uma alternativa como o [DBeaver](https://dbeaver.io/). Ainda foi preciso instalar a interface de linha de comando SQLcl, da Oracle, para instalar a amostra escolhida.
O grupo estudou a possibilidade de hospedar o banco em algum sistema de nuvem, possibilitando que haja preocupação apenas com a liberação de acesso para o restante da turma no dia da apresentação, sendo necessário instalar apenas o DBeaver ou o SQL Developer. Até então, existiam duas possibilidades: usar o [serviço gratuito da AWS](https://aws.amazon.com/free/database/) ou usar o [Oracle Cloud](https://developer.oracle.com/free.html#:~:text=Oracle%20Cloud%20Free%20Tier%20allows,an%20unlimited%20period%20of%20time.), que também possui serviços gratuitos. A ideia foi usar aquele que ofereça os melhores recursos, e que facilite a integração do banco.
A escolha do serviço AWS foi realizada, e após dias de dificuldades com a execução do script no banco, esse foi importado com sucesso para a nuvem. Porém, o grupo descobriu da pior forma possível, na aula do dia 05/06/2023, que o serviço gratuito do AWS, não era gratuito (pelo menos não para Oracle). Assim, surgiu uma outra ideia: rodar o banco em uma máquina pessoal na rede do Instituto Federal, e usar apenas o DBeaver nas outras máquinas para se conectar ao servidor.
Essa ideia foi testada com sucesso no mesmo dia, e até agora se provou como a melhor opção em todos os quesitos: é gratuita, fácil de configurar, possui controle ilimitado, não depende da internet para funcionar, é rápida de configurar em uma nova máquina, não precisa de nenhum software adicional nos outros computadores alem do DBeaver e possibilita executar a última versão do Oracle DB (21c) no servidor e a versão mais atual do script, evitando a necessidade de utilizar as versões mais antigas dos softwares por conta das limitações do laboratório.

# 3. Definição do Esquema e Carga de Dados
## Foram anotadas as instruções executadas no computador servidor para instalar o banco e carregar nele os dados. Os arquivos necessários foram baixados e reunidos em uma pasta, contendo as subpastas descritas nas instruções:
1. **Preparação:**
    - Copiar as pastas "Banco", "OracleXE18" e "Programas" para alguma pasta "segura" no computador.
2. **Instalação do Oracle DB 18c:**
    - Abrir a pasta "OracleXE18" e executar o arquivo "setup.exe";
    - Prosseguir com a instalação normalmente, definindo uma senha quando solicitado;
    - Após a instalação, encontrar a pasta onde o Oracle foi instalado (normalmente "C:\app\[USUARIO]\product\18c\dbhomeXE");
    - Na pasta do Oracle, abrir a pasta network\admin e abrir os arquivos "listener.ora" e "tnsnames.ora" com o bloco de notas;
    - Nos arquivos, trocar os IPs por "localhost" (sem as aspas) e salvar os arquivos;
    - Reiniciar os serviços do Oracle no Windows (no menu iniciar, digitar "services.msc" e procurar por "OracleServiceXE" e "OracleXETNSListener", clicar com o botão direito e reiniciar os dois);
    - (Opcional) - Excluir a pasta "OracleXE18" do computador. 
3. **Programas adicionais:**
    - Manter a pasta "Programas" em algum lugar seguro;
    - (Opcional/Solução de problemas) - Instalar o runtime do Java (rodar o arquivo "Programas\Java JRE 20.0.1 Setup.msi" e prosseguir com a instalação normalmente, adicionando o caminho do Java ao PATH do Windows quando solicitado);
    - (Opcional) - adicionar o caminho do executável do SQLcl ao PATH do Windows (no menu iniciar, digitar "variáveis de ambiente" e clicar em "Editar as variáveis de ambiente do sistema", clicar em "Variáveis de ambiente", selecionar a variável "Path" e clicar em "Editar", clicar em "Novo" e adicionar o caminho da pasta "Programas\sqlcl\bin" e clicar em "OK" em todas as janelas);
    - (Opcional) - instalar o DBeaver como alternativa ao SQL Developer (rodar o arquivo "DBeaver Setup.exe" dentro da pasta "Programas" e prosseguir com a instalação normalmente).
4. **Criação do banco de dados de amostra:**
    - Abrir o SQLcl ("Programas\SQLcl\bin\sql.exe") e conectar-se ao banco de dados com o usuário "SYSTEM" e a senha definida na instalação;
    - Navegar até a pasta "Banco/sales_history" (no SQLcl, digitar `cd [CAMINHO NO PC]/Banco/sales_history` e pressionar enter, sempre usando barras invertidas);
    - Rodar o seguinte comando e pressionar enter: `alter session set "_ORACLE_SCRIPT"=true;`
    - Rodar o seguinte comando e pressionar enter: `@sh_install.sql;`
    - Quando solicitado, digitar uma senha para o usuário "SH" e pressionar enter;
    - Quando solicitado o tablespace, pressionar enter para usar o tablespace padrão;
    - Aguardar a instalação do banco de dados de amostra e verificar se o banco foi instalado corretamente (a quantidade de linhas inseridas em cada tabela e a quantidade de linhas esperadas serão exibidas no final da instalação).
5. **Criação da conexão com o banco:**
    - Abrir o SQLDeveloper ("Programas\SQL_Developer\sqldeveloper.exe");
    - Clicar em "New Connection" e preencher os campos com os seguintes valores:
        - Connection Name: Sales History
        - Username: sh
        - Password: [SENHA DEFINIDA NA INSTALAÇÃO]
        - Hostname: localhost
        - Port: 1521
        - SID: xe
    - Clicar em "Test" e aguardar a mensagem de sucesso;
    - Clicar em "Save" e em "Connect";
    - (Opcional) - Executar consultas no banco de dados de amostra para verificar se a instalação foi bem sucedida;
    - (Opcional/Alternativa) - Usar o DBeaver ao invés do SQLDeveloper.

## Descrição do esquema do banco de dados de amostra:
A amostra escolhida é composta por 8 tabelas, sendo elas: costs, products, channels, promotions, sales, costumers, times, countries. A importação do esquema é feita por um script chamado sh_install.sql, que chamará e executará os outros scripts referentes a criação da tabela e carga de dados. No projeto são os arquivos sh_create.sql, sh_populate.sql, respectivamente. O script sh_create.sql também define constraints, chaves estrangeiras, comentários e algumas visões e visões materializadas.
### Alguns comandos usados pelos scripts para criar os esquemas são:
1. **Tabela Countries:**
    - Todas as colunas criadas para essa tabela são determinadas com a constraint `NOT NULL`
    - `CONSTRAINT countries_pk PRIMARY KEY (country_id) //define a PK da tabela para a coluna country_id`
 
2. **Tabela Costumers:**
    - `CONSTRAINT customers_pk PRIMARY KEY (cust_id) //define a PK da tabela para a coluna cust_id`
    - `CONSTRAINT customers_country_fk  FOREIGN KEY (country_id) REFERENCES countries (country_id) //a coluna country_id é uma chave estrangeira que referencia a coluna country_id da tabela Country`
 
3. **Tabela Promotions:**
    - Todas as colunas desta tabela possuem a constraint `NOT NULL` declarada.
    - `CONSTRAINT promo_pk PRIMARY KEY (promo_id) //definição da chave primária`
 
4. **Tabela Products:**
    - Algumas colunas desta tabela possuem a constraint `NOT NULL` declarada.
    - `CONSTRAINT products_pk PRIMARY KEY (prod_id) //definição da chave primária`
 
5. **Tabela Times:**
    - Todas as colunas desta tabela possuem a constraint `NOT NULL` declarada.
    - `CONSTRAINT times_pk PRIMARY KEY (time_id) //definição da chave primária`
 
6. **Tabela Channels:**
    - Todas as colunas desta tabela possuem a constraint `NOT NULL` declarada.
    - `CONSTRAINT channels_pk PRIMARY KEY (channel_id)//definição da chave primária`
 
7. **Tabela Sales:**
    - Todas as colunas desta tabela possuem a constraint `NOT NULL` declarada.
    - 6 das 7 colunas desta tabela são chaves estrangeiras que referenciam as chaves primárias das tabelas anteriores.
 
8. **Tabela Costs:**
    - Todas as colunas desta tabela possuem a constraint `NOT NULL` declarada.
    - Semelhante a tabela Sales, possui 4 de suas colunas referenciando chaves primárias de outras tabelas

# 4. Consultas Simples
## Ao fim da execução do script, são exibidas quantas entradas foram inseridas, em cada uma das tabelas, comparando os valores com os números esperados:
```
Installation verification
____________________________
Verification:

Table                            provided    actual
_____________________________ ___________ _________
channels                                5         5
costs                               82112     82112
countries                              35        35
customers                           55500     55500
products                               72        72
promotions                            503       503
sales                              918843    918843
times                                1826      1826
supplementary_demographics           4500      4500

Thank you!
___________________________________________________________
The installation of the sample schema is now finished.
Please check the installation verification output above.
```
### O comando executado pelo script foi:
```SQL
SELECT 'Verification:' AS "Installation verification" FROM dual;
 
SELECT 'channels' AS "Table", 5 AS "provided", count(1) AS "actual" FROM channels
UNION ALL
SELECT 'costs' AS "Table", 82112 AS "provided", count(1) AS "actual" FROM costs
UNION ALL
SELECT 'countries' AS "Table", 35 AS "provided", count(1) AS "actual" FROM countries
UNION ALL
SELECT 'customers' AS "Table", 55500 AS "provided", count(1) AS "actual" FROM customers
UNION ALL
SELECT 'products' AS "Table", 72 AS "provided", count(1) AS "actual" FROM products
UNION ALL
SELECT 'promotions' AS "Table", 503 AS "provided", count(1) AS "actual" FROM promotions
UNION ALL
SELECT 'sales' AS "Table", 918843 AS "provided", count(1) AS "actual" FROM sales
UNION ALL
SELECT 'times' AS "Table", 1826 AS "provided", count(1) AS "actual" FROM times
UNION ALL
SELECT 'supplementary_demographics' AS "Table", 4500 AS "provided", count(1) AS "actual" FROM supplementary_demographics;
```
### Para uma verificação manual mais detalhada, o comando `SELECT * FROM products;` foi executado, retornando as informações de 72 produtos inseridos no banco:
![Query no SQLDeveloper](https://user-images.githubusercontent.com/77900343/244724009-4477be40-279f-4beb-812f-b0aaf9a875b3.png)
A tabela products foi escolhida devido a sua quantidade menor de entradas, mas o mesmo teste foi realizado com a tabela promotions, de 503 entradas (`SELECT * FROM promotions;`).

# 5. Transações
Como já citado, o grupo realizou um teste utilizando uma máquina pessoal como servidor. Para isso, as seguintes intruções foram seguidas:
1. Abrir a porta 1521 na máquina usada como servidor
2. Instalar o DBeaver na máquina secundária
3. Criar uma nova conexão usando os seguintes parámetros:
    - Username: sh
    - Password: [SENHA DEFINIDA NA INSTALAÇÃO]
    - Hostname: [IP DO SERVIDOR]
    - Port: 1521
    - SID: xe
4. Realizar consultas simples para testar a conexão.

Após o fim do teste, o grupo iniciou a pesquisa sobre transações no Oracle. No Oracle, transações unidades lógicas atômicas de trabalho que contém um ou mais comandos SQL. Essas transações são então grupos de comandos que podem ser aplicados ao banco de dados em sua totalidade (*commit*) ou desfeitos em sua totalidade (*rollback*). O Oracle atribui um ID único para cada transação.
As transações do Oracle obedeçem às propriedades ACID, acronimo que significa:
- **Atomicidade**
 Ou todas as tarefas são executadas, ou nenhuma é. Transações não são parciais, e caso haja uma falha no meio de sua execução, a transação é revertida em sua totalidade.
- **Consistência**
  A tranasação deve tirar o banco de dados de um estado consistente e o levar para outro estado consistente. Falhas não podem resultar em inconsistência de dados.
- **Isolação**
 As transações são isoladas entre si, ou seja, os efeitos de uma transação não são visiveis para outras até que essa receba um *commit*.
- **Durabilidade**
 Mudanças feitas por transções que receberam um *commit* são permanentes, e não são perdidas no banco.

Transações em um banco de dados possuem um ou mais comandos, e um começo e fim. No Oracle, o começo de uma transação é demarcado pelo primeiro comando SQL executável encontrado. Quando uma nova transação começa, o Oracle DB a associa a um conjunto de dados para serem "desfeitos", e depois atribui à transação um ID. Já o fim de uma transação pode ser uma das seguintes situações:
- O usuário executa um comando `COMMIT` ou `ROLLBACK` sem haver um *savepoint* prévio.
- O usuário executa um comando de definição de dados, como `CREATE`, `DROP`, `RENAME` ou `ALTER`. Nesse caso, o Oracle implicitamente executa um comando `COMMIT` na transação.
- O usuário sai da aplicação. Nesse caso, o Oracle implicitamente executa um comando `COMMIT` na transação.
- Um processo termina abruptamente. Nesse caso, a transação é implicitamente desfeita pelo Oracle.

Uma transação pode começar implicitamente ao ser nomeada, usando o comando SQL `SET TRANSACTION NAME 'nome';` (onde "nome" é um nome qualquer). O controle de transações no Oracle funciona da seguinte forma:
- Um comando `COMMIT` encerra a transação atual, apaga todos os *savepoints* e torna as mudanças efetuadas permanentes.
- Um comando `ROLLBACK` desfaz todas as mudanças desde o início da transação. Um comando `ROLLBACK TO SAVEPOINT nome` desfaz as mudanças efetuadas na transação desde o *savepoint* chamado "nome", mas não a encerra.
- Um comando `SAVEPOINT nome` (onde "nome" é um nome qualquer) cria um savepoint, para o qual é possível retornar depois.

## Exemplo de transações
### Para criar um novo usuário, que fará as operações simples ao banco, é necessário:
1. Logar como usuário SYSTEM no Oracle
2. Garantir ao usuário sh permissão para criar usuários e manipular o banco com o comando `GRANT ALL PRIVILEGES to sh;`, após rodar `alter session set "_ORACLE_SCRIPT"=true;`
3. Voltar na conexão do usuário sh e criar o novo usuário (com a senha "usuario") usando o comando `CREATE USER sh_usuario IDENTIFIED BY "usuario";`
4. Garantir ao novo usuário as permissões de conexão ao banco de dados, rodando o comando `GRANT CREATE SESSION to sh_usuario;`
5. Garantir ao novo usuário permissão para selecionar dados das tabelas e inserir novas vendas e canais, rodando os comandos:
    ```sql
    GRANT SELECT ON sh.CHANNELS TO sh_usuario;
    GRANT SELECT ON sh.COSTS TO sh_usuario;
    GRANT SELECT ON sh.COUNTRIES TO sh_usuario;
    GRANT SELECT ON sh.CUSTOMERS TO sh_usuario;
    GRANT SELECT ON sh.PRODUCTS TO sh_usuario;
    GRANT SELECT ON sh.PROMOTIONS TO sh_usuario;
    GRANT SELECT ON sh.SALES TO sh_usuario;
    GRANT SELECT ON sh.SUPPLEMENTARY_DEMOGRAPHICS TO sh_usuario;
    GRANT SELECT ON sh.TIMES TO sh_usuario;

    GRANT INSERT ON sh.sales to sh_usuario;
    GRANT INSERT ON sh.channels to sh_usuario;
    ```
6. Criar uma nova conexão usando os seguintes parámetros:
    - Username: sh_usuario
    - Password: usuario
    - Hostname: [IP DO SERVIDOR]
    - Port: 1521
    - SID: xe
Feito isso, é possível se conectar ao banco com o usuário sh_usuario e selecionar dados das tabelas de sh, bem como inserir novos dados em sh.sales e sh.channels.

### Testando as transações com múltiplos usuários
1. Se conectar ao banco com o usuário sh_usuário em duas instâncias diferentes (na mesma máquina em processos diferentes ou em máquinas separadas) usando o SQL Developer, DBeaver, SQL Plus, SQLcl etc.
    ![image](https://user-images.githubusercontent.com/77900343/244971953-09bc8c5c-bf88-44a6-be46-c98bb552de53.png)
2. Para fins de comparação, executar (em ambas instâncias) o comando `SELECT * FROM sh.channels;`
    ![image](https://user-images.githubusercontent.com/77900343/244973657-179e6fdc-7d3c-4d81-a8aa-ee89e86aa207.png)
3. Após evidenciadas as mesmas tuplas, iniciar uma nova transação, inserindo um novo canal em uma das instâncias:
    - Rodar o comando SQL `INSERT INTO sh.channels VALUES ( 6, 'Subscriptions', 'Direct',   12, 'Channel total', 1 );`
    - ![image](https://user-images.githubusercontent.com/77900343/244973839-324c0686-599f-4d08-b632-0cb12ab25f86.png)
4. Mais uma vez, rodar o comando `SELECT` do passo 2 em ambas instâncias
    - ![image](https://user-images.githubusercontent.com/77900343/244973905-af91c5df-9860-4557-ae9a-4f645a249844.png)
    - Apenas o usuário que iniciou a transação vê os resultados do `INSERT`, evidenciando a propriedade de isolação das transações.
5. Tornar permantentes as alterações no banco, com o comando `COMMIT;` (no usuário que iniciou a transação de inserção) e executar novamente os comandos `SELECT` nas duas instâncias
    - ![image](https://user-images.githubusercontent.com/77900343/244974581-bfe7bd39-c582-45a0-b617-0b6f4128b836.png)
    - Após a execução do `COMMIT`, os dados são mantidos permanentes (propriedade de durabilidade) e podem ser vistos nas duas instâncias.

# Referências (esboço):
- https://docs.oracle.com/en/database/oracle/oracle-database/21/comsc/installing-sample-schemas.html#GUID-1E645D09-F91F-4BA6-A286-57C5EC66321D
- 