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
  ```
