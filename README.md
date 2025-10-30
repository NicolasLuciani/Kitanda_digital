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
  ```sql
  
    CREATE TABLE produtos (
  
      id_produto INT AUTO_INCREMENT PRIMARY KEY,
      nome_produto VARCHAR(100) NOT NULL,
      preco DECIMAL(10,2) NOT NULL,
      categoria VARCHAR(50),
      id_vendedor INT,
      FOREIGN KEY (id_vendedor) REFERENCES vendedores(id_vendedor)
    );


    CREATE TABLE vendas (
  
        id_venda INT(5) PRIMARY KEY auto_increment,
        id_produto INT(5),
        foreign key (id_produto) REFERENCES produtos(id_produto),
        quantidade INT(6),
        data_venda DATE
    );
  
  ```

<p> - Crimos três tabelas necessáris, produtos, vendas e vendedores</p>
<p></p>
<p> - produtos, possui o id do produtos, sendo chave primaria, nome do produto, preço, categoria, id do vendedor que é um chave estrangeira</p>
<p> - vendas, possui o id das vendas, chave primaria, o id do produto, que é uma chave estrangeira, a quantidade, data da venda.</p>
<p> - vendedores, </p>
