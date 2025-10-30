# KITANDA DIGITAL
## Estrutura
- produtos
  - id_produto
  - nome_produto
  - preco
  - categoria
  - id_vendedor

- vendas
  - id_venda
  - id_produto
  - quantidade
  - data_venda
 
- vendedores
  - id_vendedor
  - nome
  - email
  - cidade
  - estado
  - data_cadastro

  ## Query
  ### tabelas

### Crimos três tabelas necessáris, produtos, vendas e vendedores</p>
```sql
    CREATE TABLE produtos (
  
      id_produto INT AUTO_INCREMENT PRIMARY KEY,
      nome_produto VARCHAR(100) NOT NULL,
      preco DECIMAL(10,2) NOT NULL,
      categoria VARCHAR(50),
      id_vendedor INT,
      FOREIGN KEY (id_vendedor) REFERENCES vendedores(id_vendedor)
    );

```
<p> - produtos, possui o id do produtos, sendo chave primaria, nome do produto, preço, categoria, id do vendedor que é um chave estrangeira</p>

```sql
    CREATE TABLE vendas (
  
        id_venda INT(5) PRIMARY KEY auto_increment,
        id_produto INT(5),
        foreign key (id_produto) REFERENCES produtos(id_produto),
        quantidade INT(6),
        data_venda DATE
    );

```
<p> - vendas, possui o id das vendas, chave primaria, o id do produto, que é uma chave estrangeira, a quantidade, data da venda.</p>

```sql
CREATE TABLE vendedores (
    id_vendedor INT(3) PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(15),
    email VARCHAR(30),
    cidade VARCHAR(20),
    estado VARCHAR(20),
    data_cadastro DATE
);

```
<p> - vendedores, possui o id das vendas, possui a chave primária, id dos produtos, chave estrangeira, quantidade e a data da venda</p>

---
### Views
```sql
CREATE VIEW vw_produtos_detalhados AS view1
SELECT
    p.nome_produto AS NomeProduto,
    p.preco AS Preco,
    v.nome AS NomeVendedor,
    v.cidade AS Cidade,
    v.estado AS Estado,
    p.categoria AS Categoria
FROM
    produtos p
JOIN
    vendedores v ON p.id_vendedor = v.id_vendedor;


CREATE VIEW vw_total_vendas_por_vendedor AS
SELECT
    a.nome AS NOME,
    SUM(b.quantidade * c.preco) AS QTD_VENDAS
FROM
    vendedores AS a
JOIN
    produtos AS c ON a.id_vendedor = c.id_vendedor
JOIN
    vendas AS b ON c.id_produto = b.id_produto
GROUP BY
    a.nome;


CREATE VIEW vw_vendas_mensais AS view3
SELECT
    v.nome,
    YEAR(ve.data_venda) AS ano,
    MONTH(ve.data_venda) AS mes,
    SUM(ve.quantidade * p.preco) AS total_vendas
FROM
    vendas ve
JOIN
    produtos p ON ve.id_produto = p.id_produto
JOIN
    vendedores v ON p.id_vendedor = v.id_vendedor

GROUP BY
    v.nome, ano, mes;


CREATE VIEW vw_produtos_por_categoria AS view4
SELECT categoria,
    COUNT(*) AS total_produtos,
    ROUND(AVG(preco), 2) AS preco_medio
   
FROM
    produtos
GROUP
    BY categoria;

```
<p> - A view vw_produtos_detalhados exibe todos os produtos disponíveis no marketplace, mostrando o nome do produto, preço, categoria e as informações do vendedor responsável, incluindo nome, cidade e estado. Ela permite visualizar rapidamente os produtos com seus respectivos vendedores, facilitando consultas de catálogo ou listagens completas.</p>

<p> - A view vw_total_vendas_por_vendedor calcula o valor total vendido por cada vendedor, somando o preço dos produtos multiplicado pela quantidade vendida. É útil para identificar quais vendedores possuem maior faturamento, permitindo análises de desempenho e ranking de vendas.</p>

<p> - A view vw_vendas_mensais apresenta o faturamento mensal de cada vendedor, agrupando as vendas por ano e mês. Ela mostra o nome do vendedor, o período da venda e o total faturado, permitindo acompanhar tendências de vendas, comparar períodos e identificar crescimento ou queda de desempenho ao longo do tempo.</p>

<p> - A view vw_produtos_por_categoria consolida informações sobre os produtos agrupados por categoria, mostrando o total de produtos cadastrados e o preço médio de cada categoria. Ela permite analisar a distribuição de produtos e preços, ajudando no planejamento de estoque e definição de estratégias comerciais.</p>

