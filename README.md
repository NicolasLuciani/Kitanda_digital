# 🍍 KITANDA DIGITAL
## 🦴 Estrutura
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
  ## 📁 tabelas

<p>Criamos três tabelas necessáris, produtos, vendas e vendedores</p>

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
### 👀 Views
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
    produtos AS p
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
    vendas AS v
JOIN
    produtos AS p ON v.id_produto = p.id_produto
JOIN
    vendedores AS ve ON p.id_vendedor = ve.id_vendedor

GROUP BY
    ve.nome, ano, mes;

```

* Apresenta o faturamento mensal de cada vendedor, agrupando as vendas por ano e mês. Ela mostra o nome do vendedor, o período da venda e o total faturado, permitindo acompanhar tendências de vendas, comparar períodos e identificar crescimento ou queda de desempenho ao longo do tempo.<br><br><br><br>

```sql
CREATE VIEW vw_produtos_por_categoria AS view4
SELECT categoria,
    COUNT(*) AS total_produtos,
    ROUND(AVG(preco), 2) AS preco_medio
   
FROM
    produtos
GROUP BY
	categoria;

```
* Consolida informações sobre os produtos agrupados por categoria, mostrando o total de produtos cadastrados e o preço médio de cada categoria. Ela permite analisar a distribuição de produtos e preços, ajudando no planejamento de estoque e definição de estratégias comerciais.<br><br><br><br>

---
### 💻 Procedure
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
	(p_nome, p_email, p_cidade, p_estado, CURDATE()); -- o CURDATE vem de Current Date, que representa a data que é feito o cadastro
	           
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
    DECLARE v_existe INT; -- declara a existencia do produto

    SELECT 
		COUNT(*) INTO v_existe
    FROM 
		produtos
    WHERE id_produto = p_id_produto;

    IF v_exist = 0 THEN
        SELECT 'Erro: Produto não encontrado.' AS mensagem;
    ELSEIF p_quantidade < 0 THEN
        SELECT 'Erro: Quantidade inválida. Deve ser maior que zero.' AS mensagem;
    ELSE
        INSERT INTO vendas (id_produto, quantidade, data_venda)
        VALUES 
			(p_id_produto, p_quantidade, NOW());
        SELECT 
			'Venda registrada com sucesso!' AS mensagem;
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
### 🖱️ Query
```sql
SELECT
    v.nome AS NomeVendedor,
    SUM(vd.quantidade * p.preco) AS TotalVendas
FROM
    vendas AS vd
JOIN
    produtos AS p
    ON vd.id_produto = p.id_produto
JOIN
    vendedores AS v
    ON p.id_vendedor = v.id_vendedor
GROUP BY
    v.id_vendedor, v.nome
ORDER BY
    TotalVendas DESC
LIMIT 1;
```
* Essa query faz um rank com os itens mais vendidos, utilizando o nome do produto e a quantidade vendida.<br><br><br><br>

```sql
QUERY 2 
SELECT
    v.nome AS nome_vendedor,
    SUM(p.preco * ve.quantidade) AS total_vendas
FROM
    vendedores AS v
JOIN
    produtos AS p
    ON v.id_vendedor = p.id_vendedor
JOIN
    vendas ve ON p.id_produto = ve.id_produto
GROUP BY
    v.id_vendedor, v.nome
ORDER BY
    total_vendas DESC
LIMIT 1;
```
* Essa query verifica o vendedor que mais vendeu no total, utilizando o nome do vendedor e o total de vendas.<br><br><br><br>

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
              vendas AS v2
         JOIN
              produtos AS p2
              ON v2.id_produto = p2.id_produto
         WHERE
              p2.categoria = p.categoria)* 100)) AS PercentualParticipacao
FROM
    vendas AS vd
JOIN
    produtos AS p
    ON vd.id_produto = p.id_produto
JOIN
    vendedores AS v
    ON p.id_vendedor = v.id_vendedor
GROUP BY
    v.nome, p.categoria
ORDER BY
    p.categoria, TotalVendas DESC;
```
* Essa query mostra o total de vendas por categoria e vendedor, utilizando o nome do vendedor, categoria do produto, total de vendas e percentual de participação do vendedor dentro da categoria<br><br><br><br>

```sql
SELECT 
    v.nome AS vendedor,
    YEAR(v1.data_venda) AS ano,
    MONTH(v1.data_venda) AS mes,
    SUM(v1.quantidade * p.preco) AS total_mes,
ROUND((
        (SUM(v1.quantidade * p.preco) - SUM(v2.quantidade * p2.preco)) /
        SUM(v2.quantidade * p2.preco)
    )) AS variacao_percentual
FROM
    vendas AS v1
JOIN
    produtos AS p
    ON v1.id_produto = p.id_produto
JOIN
    vendedores AS v
    ON p.id_vendedor = v.id_vendedor
LEFT JOIN
    vendas AS v2
    ON MONTH(v2.data_venda) = MONTH(v1.data_venda) - 1
        AND YEAR(v2.data_venda) = YEAR(v1.data_venda)
        AND v2.id_produto = v1.id_produto
JOIN
    produtos AS p2
    ON v2.id_produto = p2.id_produto
GROUP BY
    v.nome, ano, mes
ORDER BY
    v.nome, ano, mes;
```

* Essa query apresentar a variação percentual de vendas mês a mês para cada vendedor, utilizando, o nome do vendedor, o mês, ano, o total de vendas, verificando a variação percentual entre o mês anterior.

---
* INSIRA PELO MENOS 20 VENDEDORES, 50 PRODUTOS E 150 VENDAS PARA VALIDAR SUAS CONSULTAS E OBTER UM RESULTADO SIGNIFICATIVO NAS ANÁLISES.
* Vendedores
```sql
INSERT INTO vendedores (nome, email, cidade, estado, data_cadastro) VALUES
('Ana Silva', 'ana.silva@kitanda.com', 'São Paulo', 'SP', '2023-01-15'),
('Bruno Souza', 'bruno.souza@kitanda.com', 'Rio de Janeiro', 'RJ', '2023-02-20'),
('Carla Mendes', 'carla.mendes@kitanda.com', 'Belo Horizonte', 'MG', '2023-03-10'),
('Diego Lima', 'diego.lima@kitanda.com', 'Curitiba', 'PR', '2023-04-05'),
('Eduarda Nunes', 'eduarda.nunes@kitanda.com', 'Porto Alegre', 'RS', '2023-05-12'),
('Felipe Rocha', 'felipe.rocha@kitanda.com', 'Salvador', 'BA', '2023-06-22'),
('Gabriela Alves', 'gabriela.alves@kitanda.com', 'Fortaleza', 'CE', '2023-07-18'),
('Henrique Costa', 'henrique.costa@kitanda.com', 'Manaus', 'AM', '2023-08-09'),
('Isabela Duarte', 'isabela.duarte@kitanda.com', 'Recife', 'PE', '2023-09-14'),
('João Pedro', 'joao.pedro@kitanda.com', 'Belém', 'PA', '2023-10-01'),
('Karen Oliveira', 'karen.oliveira@kitanda.com', 'Campinas', 'SP', '2023-10-21'),
('Lucas Martins', 'lucas.martins@kitanda.com', 'Florianópolis', 'SC', '2023-11-03'),
('Mariana Torres', 'mariana.torres@kitanda.com', 'Goiânia', 'GO', '2023-11-15'),
('Nathan Freitas', 'nathan.freitas@kitanda.com', 'Vitória', 'ES', '2023-11-27'),
('Otávio Santos', 'otavio.santos@kitanda.com', 'Natal', 'RN', '2023-12-05'),
('Patrícia Gomes', 'patricia.gomes@kitanda.com', 'João Pessoa', 'PB', '2023-12-20'),
('Rafael Pinto', 'rafael.pinto@kitanda.com', 'Aracaju', 'SE', '2024-01-10'),
('Sofia Ribeiro', 'sofia.ribeiro@kitanda.com', 'Cuiabá', 'MT', '2024-01-25'),
('Thiago Fernandes', 'thiago.fernandes@kitanda.com', 'Teresina', 'PI', '2024-02-12'),
('Vanessa Cardoso', 'vanessa.cardoso@kitanda.com', 'Maceió', 'AL', '2024-03-01');
```
<br><br><br>
* Produtos
```sql
INSERT INTO produtos (nome_produto, preco, categoria, id_vendedor) VALUES
('Banana Prata', 5.50, 'Frutas', 1),
('Maçã Fuji', 6.20, 'Frutas', 2),
('Laranja Lima', 4.80, 'Frutas', 3),
('Mamão Papaya', 7.00, 'Frutas', 4),
('Abacaxi Pérola', 8.50, 'Frutas', 5),
('Alface Crespa', 3.20, 'Verduras', 6),
('Couve Manteiga', 2.80, 'Verduras', 7),
('Rúcula', 3.00, 'Verduras', 8),
('Tomate Italiano', 7.50, 'Hortaliças', 9),
('Cebola Roxa', 4.90, 'Hortaliças', 10),
('Batata Inglesa', 5.20, 'Hortaliças', 11),
('Cenoura', 4.30, 'Hortaliças', 12),
('Beterraba', 4.10, 'Hortaliças', 13),
('Pepino Japonês', 3.70, 'Hortaliças', 14),
('Abobrinha', 4.50, 'Hortaliças', 15),
('Pimentão Vermelho', 6.90, 'Hortaliças', 16),
('Arroz Tipo 1', 22.90, 'Grãos', 17),
('Feijão Carioca', 8.70, 'Grãos', 18),
('Lentilha', 10.50, 'Grãos', 19),
('Grão-de-bico', 12.30, 'Grãos', 20),
('Farinha de Trigo', 6.00, 'Grãos', 1),
('Açúcar Refinado', 5.80, 'Mercearia', 2),
('Café Torrado', 18.90, 'Mercearia', 3),
('Sal Marinho', 3.40, 'Mercearia', 4),
('Óleo de Soja', 7.60, 'Mercearia', 5),
('Leite Integral', 5.50, 'Laticínios', 6),
('Queijo Minas', 25.90, 'Laticínios', 7),
('Manteiga', 14.80, 'Laticínios', 8),
('Iogurte Natural', 6.70, 'Laticínios', 9),
('Requeijão', 9.90, 'Laticínios', 10),
('Frango Inteiro', 16.80, 'Carnes', 11),
('Coxa e Sobrecoxa', 12.50, 'Carnes', 12),
('Bife de Alcatra', 39.90, 'Carnes', 13),
('Linguiça Toscana', 18.20, 'Carnes', 14),
('Carne Moída', 32.50, 'Carnes', 15),
('Peixe Tilápia', 28.90, 'Peixes', 16),
('Salmão', 59.90, 'Peixes', 17),
('Filé de Merluza', 24.80, 'Peixes', 18),
('Camarão Cinza', 74.50, 'Peixes', 19),
('Sardinha Congelada', 16.90, 'Peixes', 20),
('Detergente Neutro', 3.90, 'Limpeza', 1),
('Sabão em Pó', 14.50, 'Limpeza', 2),
('Amaciante', 9.80, 'Limpeza', 3),
('Desinfetante', 7.40, 'Limpeza', 4),
('Água Sanitária', 5.60, 'Limpeza', 5),
('Shampoo', 11.90, 'Higiene', 6),
('Sabonete', 3.20, 'Higiene', 7),
('Pasta de Dente', 4.80, 'Higiene', 8),
('Papel Higiênico', 13.50, 'Higiene', 9),
('Desodorante', 9.90, 'Higiene', 10);

```
<br><br><br>
* Vendas
```sql
INSERT INTO vendas (id_produto, quantidade, data_venda) VALUES
(1, 5, '2025-01-12'),
(2, 3, '2025-01-15'),
(3, 2, '2025-01-20'),
(4, 8, '2025-02-02'),
(5, 4, '2025-02-05'),
(6, 7, '2025-02-10'),
(7, 6, '2025-02-15'),
(8, 3, '2025-02-18'),
(9, 9, '2025-02-20'),
(10, 4, '2025-02-25'),
(11, 2, '2025-03-01'),
(12, 3, '2025-03-02'),
(13, 5, '2025-03-05'),
(14, 7, '2025-03-07'),
(15, 4, '2025-03-08'),
(16, 6, '2025-03-10'),
(17, 8, '2025-03-11'),
(18, 3, '2025-03-12'),
(19, 4, '2025-03-13'),
(22, 3, '2025-04-02'),
(23, 5, '2025-04-03'),
(34, 7, '2025-05-03'),
(35, 2, '2025-05-05'),
(36, 4, '2025-05-07'),
(37, 8, '2025-05-09'),
(38, 3, '2025-05-10'),
(39, 5, '2025-05-12'),
(40, 6, '2025-05-14'),
(41, 2, '2025-05-15'),
(42, 7, '2025-05-18'),
(43, 5, '2025-05-20'),
(44, 3, '2025-05-21'),
(20, 5, '2025-03-15'),
(21, 6, '2025-04-01'),
(45, 6, '2025-05-22'),
(46, 8, '2025-05-24'),
(47, 4, '2025-05-25'),
(48, 7, '2025-05-26'),
(24, 4, '2025-04-04'),
(25, 6, '2025-04-06'),
(26, 2, '2025-04-08'),
(27, 3, '2025-04-10'),
(28, 4, '2025-04-12'),
(29, 5, '2025-04-15'),
(30, 6, '2025-04-17'),
(31, 3, '2025-04-20'),
(32, 5, '2025-04-22'),
(33, 4, '2025-05-01'),
(49, 5, '2025-05-27'),
(50, 6, '2025-05-28'),
(1, 3, '2025-06-01'),
(2, 5, '2025-06-02'),
(3, 4, '2025-06-03'),
(4, 6, '2025-06-04'),
(5, 7, '2025-06-05'),
(6, 8, '2025-06-06'),
(7, 3, '2025-06-07'),
(8, 5, '2025-06-08'),
(9, 4, '2025-06-09'),
(10, 6, '2025-06-10'),
(11, 3, '2025-06-11'),
(12, 7, '2025-06-12'),
(13, 5, '2025-06-13'),
(14, 2, '2025-06-14'),
(6, 3, '2025-09-06'),
(7, 8, '2025-09-07'),
(15, 6, '2025-06-15'),
(16, 8, '2025-06-16'),
(17, 5, '2025-06-17'),
(18, 3, '2025-06-18'),
(19, 7, '2025-06-19'),
(20, 4, '2025-06-20'),
(21, 6, '2025-07-01'),
(22, 8, '2025-07-02'),
(23, 3, '2025-07-03'),
(24, 7, '2025-07-04'),
(25, 5, '2025-07-05'),
(26, 4, '2025-07-06'),
(27, 3, '2025-07-07'),
(30, 8, '2025-07-10'),
(31, 4, '2025-07-11'),
(32, 5, '2025-07-12'),
(33, 3, '2025-07-13'),
(34, 6, '2025-07-14'),
(35, 7, '2025-07-15'),
(36, 2, '2025-07-16'),
(37, 8, '2025-07-17'),
(38, 5, '2025-07-18'),
(39, 4, '2025-07-19'),
(40, 3, '2025-07-20'),
(41, 7, '2025-08-01'),
(42, 5, '2025-08-02'),
(43, 6, '2025-08-03'),
(44, 4, '2025-08-04'),
(45, 8, '2025-08-05'),
(46, 5, '2025-08-06'),
(47, 3, '2025-08-07'),
(48, 7, '2025-08-08'),
(1, 8, '2025-09-01'),
(2, 5, '2025-09-02'),
(3, 4, '2025-09-03'),
(4, 7, '2025-09-04'),
(5, 6, '2025-09-05'),
(8, 5, '2025-09-08'),
(9, 4, '2025-09-09'),
(10, 6, '2025-09-10'),
(11, 7, '2025-09-11'),
(12, 5, '2025-09-12'),
(23, 7, '2025-10-03'),
(13, 3, '2025-09-13'),
(28, 7, '2025-07-08'),
(29, 6, '2025-07-09'),
(14, 8, '2025-09-14'),
(15, 4, '2025-09-15'),
(16, 6, '2025-09-16'),
(17, 5, '2025-09-17'),
(18, 7, '2025-09-18'),
(19, 3, '2025-09-19'),
(20, 6, '2025-09-20'),
(21, 8, '2025-10-01'),
(22, 6, '2025-10-02'),
(24, 4, '2025-10-04'),
(25, 8, '2025-10-05'),
(26, 6, '2025-10-06'),
(27, 3, '2025-10-07'),
(49, 4, '2025-08-09'),
(50, 6, '2025-08-10'),
(28, 5, '2025-10-08'),
(29, 4, '2025-10-09'),
(30, 7, '2025-10-10');
```
<br><br><br>

---
# Diagrama:
![diagrama__bd.jpeg](https://github.com/NicolasLuciani/Kitanda_digital/blob/8dae2ad2f3dec5da571299651700f6f4a241b967/diagrama__bd.jpeg)






# NOMES:
Nicolas Luciani N°25<br>
John Wayne N°12<br>
Miguel Rosa N°21<br>
Samuel Gracias N°30<br>
Gustavo Zampiron N°09<br>
Guilherme Wallace N°08<br>
