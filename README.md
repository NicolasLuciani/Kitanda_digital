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

---
  ## tabelas

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
* Produtos, possui o id do produtos, sendo chave primaria, nome do produto, preço, categoria, id do vendedor que é um chave estrangeira.<br><br><br><br>

```sql
    CREATE TABLE vendas (
  
        id_venda INT(5) PRIMARY KEY auto_increment,
        id_produto INT(5),
        foreign key (id_produto) REFERENCES produtos(id_produto),
        quantidade INT(6),
        data_venda DATE
    );

```
* Vendas, possui o id das vendas, chave primaria, o id do produto, que é uma chave estrangeira, a quantidade, data da venda.<br><br><br><br>

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
* vendedores, possui o id das vendas, possui a chave primária, id dos produtos, chave estrangeira, quantidade e a data da venda.<br><br><br><br>

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

```
* Exibe todos os produtos disponíveis, mostrando o nome do produto, preço, categoria e as informações do vendedor responsável, incluindo nome, cidade e estado. Ela permite visualizar rapidamente os produtos com seus respectivos vendedores, facilitando consultas de catálogo ou listagens completas.<br><br><br><br>

```sql
CREATE VIEW vw_total_vendas_por_vendedor AS view4
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

```
* Calcula o valor total vendido por cada vendedor, somando o preço dos produtos multiplicado pela quantidade vendida.<br><br><br><br>

```sql
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

```

* Apresenta o faturamento mensal de cada vendedor, agrupando as vendas por ano e mês. Ela mostra o nome do vendedor, o período da venda e o total faturado, permitindo acompanhar tendências de vendas, comparar períodos e identificar crescimento ou queda de desempenho ao longo do tempo.<br><br><br><br>

```sql
CREATE VIEW vw_produtos_por_categoria AS view4
SELECT categoria,
    COUNT(*) AS total_produtos,
    ROUND(AVG(preco), 2) AS preco_medio
   
FROM
    produtos
GROUP
    BY categoria;

```
* Consolida informações sobre os produtos agrupados por categoria, mostrando o total de produtos cadastrados e o preço médio de cada categoria. Ela permite analisar a distribuição de produtos e preços, ajudando no planejamento de estoque e definição de estratégias comerciais.<br><br><br><br>

---
### Procedure
```sql
DELIMITER $$
CREATE PROCEDURE sp_cadastrar_vendedor(
IN p_nome VARCHAR(15),
            IN p_email VARCHAR(30),
            IN p_cidade VARCHAR(20),
            IN p_estado VARCHAR(20))
BEGIN
INSERT INTO vendedores(nome, email, cidade, estado)
        VALUES
(p_nome, p_email, p_cidade, p_estado, CURDATE());
           
END $$
DELIMITER ;
```

* Nesta procedure ele cadastra o vendedor, com o email, cidade e estado.<br><br><br><br>

```sql
DELIMITER $$

CREATE PROCEDURE sp_registrar_venda(
    IN p_id_produto INT,
    IN p_quantidade INT
)
BEGIN
    DECLARE v_exist INT;

    SELECT COUNT(*) INTO v_exist
    FROM produtos
    WHERE id_produto = p_id_produto;

    IF v_exist = 0 THEN
        SELECT 'Erro: Produto não encontrado.' AS mensagem;
    ELSEIF p_quantidade < 0 THEN
        SELECT 'Erro: Quantidade inválida. Deve ser maior que zero.' AS mensagem;
    ELSE
        INSERT INTO vendas (id_produto, quantidade, data_venda)
        VALUES (p_id_produto, p_quantidade, NOW());
        SELECT 'Venda registrada com sucesso!' AS mensagem;
    END IF;
END $$

DELIMITER ;
```

* Aqui ele registra uma venda, a partir do seu id e da quantidade, verificando se o produto foi encontra, se sim ele verifica a quantidade, se não da erro.<br><br><br><br>

```sql
DELIMITER $$

CREATE PROCEDURE sp_atualizar_preco_categoria(
    IN p_categoria VARCHAR(100),
    IN p_percentual DECIMAL(5,2)
)
BEGIN
    UPDATE produtos
    SET preco = preco + (preco * (p_percentual / 100))
    WHERE categoria = p_categoria;
END $$

DELIMITER ;
```
* Nesta procedure ele atualiza o perço, com a variavel 'preço'.<br><br><br><br>

---
### Query
```sql
SELECT
    v.nome AS NomeVendedor,
    SUM(vd.quantidade * p.preco) AS TotalVendas
FROM
    vendas vd
JOIN
    produtos p
    ON vd.id_produto = p.id_produto
JOIN
    vendedores v
    ON p.id_vendedor = v.id_vendedor
GROUP BY
    v.id_vendedor, v.nome
ORDER BY
    TotalVendas DESC
LIMIT 1;
```


```sql
QUERY 2 
SELECT
    v.nome AS nome_vendedor,
    SUM(p.preco * ve.quantidade) AS total_vendas
FROM
    vendedores v
JOIN
    produtos p
    ON v.id_vendedor = p.id_vendedor
JOIN
    vendas ve ON p.id_produto = ve.id_produto
GROUP BY
    v.id_vendedor, v.nome
ORDER BY
    total_vendas DESC
LIMIT 1;
```

```sql
SELECT
    v.nome AS NomeVendedor,
    p.categoria AS Categoria,
    SUM(vd.quantidade * p.preco) AS TotalVendas,
    ROUND(
        (SUM(vd.quantidade * p.preco) / 
        (SELECT
              SUM(v2.quantidade * p2.preco)
         FROM
              vendas v2
         JOIN
              produtos p2
              ON v2.id_produto = p2.id_produto
         WHERE
              p2.categoria = p.categoria)* 100)) AS PercentualParticipacao
FROM
    vendas vd
JOIN
    produtos p
    ON vd.id_produto = p.id_produto
JOIN
    vendedores v
    ON p.id_vendedor = v.id_vendedor
GROUP BY
    v.nome, p.categoria
ORDER BY
    p.categoria, TotalVendas DESC;
```



```sql

```

