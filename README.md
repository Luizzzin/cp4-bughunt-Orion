# Checkpoint 4 — Bug Hunt StreamFIAP

## Identificação

**Grupo:** \_\_\_

| Integrante                      | RM       | Turma |
| ------------------------------- | -------- | ----- |
| Felipe Souza Carvalho           | RM564779 | 2CCPH |
| Rodrigo Kenshin Viana Matayoshi | RM564026 | 2CCPH |
| Riquelme Santos da Mata         | RM565053 | 2CCPH |
| Luiz Henrique Barbosa Dias      | RM562399 | 2CCPH |

| Campo                              |             |
| ---------------------------------- | ----------- |
| **Total de bugs corrigidos**       | \_\_\_ / 12 |
| **Total de ajustes de Clean Code** | \_\_\_ / 6  |

---

## Parte 1 — Bugs encontrados

| #      | Sintoma observado (o que fiz/vi)                                                 | Causa raiz                                                                                                    | Correção aplicada                                                                                                     | Conceito da disciplina                              |
| ------ | -------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------- |
| bug1   | Ao criar um `Usuario` passando um nome, o nome não é armazenado.                 | `Usuario.java`: `nome = nome;` atribui o parâmetro a ele mesmo.                                               | Alterar para `this.nome = nome;`.                                                                                     | Encapsulamento                                      |
| bug2   | Usuarios com poucos creditos conseguiam alugar mídias que não deveriam.          | `Usuario.java`, linha \~25: `return preco >= this.creditos;` está invertido.                                  | Alterar para `return this.creditos >= preco;`.                                                                        | Lógica condicional (regra de negócio)               |
| bug3   | O preço utilizado em série é o preço padrão criado na classe abstrata conteúdo.  | O método tem parâmetro e não chama o `@Override`.                                                             | Aplicar o `@Override`.                                                                                                | Polimorfismo                                        |
| bug4   | Dados herdados da serie ficam vazios por causa da ausência do `super`.           | Ausência do `super`.                                                                                          | Adicionar `super` para iniciar a classe.                                                                              | Herança                                             |
| bug5   | Desconto aplicado incorretamente (1.2 diferente de 0.8).                         | Trocar multiplicação de 1.2 por 0.8 (tirar 20%).                                                              | Desconto é 0.8 (desconto de 20%).                                                                                     | Polimorfismo e herença                              |
| bug6   | É possível alugar conteúdos que não estão mais disponíveis.                      | Sem tratamento de erro para conteúdos não disponíveis (`Usuario.alugar()` não validava mídias indisponíveis). | Condicional para verificar a disponibilidade do conteúdo.                                                             | Lógica condicional (regras de negócio )             |
| bug7   | Documentários devem ser gratuitos, mas recebem o preço da classe mãe `Conteúdo`. | Herda o preço pré-definido da classe `Conteudo`.                                                              | Adicionar `Override` e mudar o preço manualmente para gratuito.                                                       | Herança                                             |
| bug8   | ID do usuário não é definido automaticamente                                     | Falta do `@GeneratedValue`.                                                                                   | Adicionar `@GeneratedValue(strategy = GenerationType.IDENTITY)` para adicionar IDs automaticamente aos usuários.      | Persistência de dados com JPA                       |
| bug 9  | Exceção capturada e retorno inadequado                                           | try/catch genérico escondendo o erro e uso desnecessário de `ResponseEntity`.                                 | Remoção do try/catch e retorno direto de `ResponseEntity<Conteudo>`                                                   | Tratamento de exceções                              |
| bug 10 | Entradas inválidas (igual ou menor que 0) no cadastro de uma mídia.              | Falta de validação para entradas e tratamento de erro.                                                        | Adicionar condicional para invalidar valores menor ou igual a zero e tratamento de erro (`IllegalArgumentException`). | Tratamento de Erros e validação.                    |
| bug 11 | Exceção de classificação indicativa não era tratada.                             | Faltava um `@ExceptionHandler` específico para `ClassificacaoIndicativaException`                             | Adicionado tratamento global `status 403`.                                                                            | Tratamento de exceções                              |
| bug 12 | Busca por categoria era feita manualmente percorrendo todos os conteúdos.        | Uso de `findAll()` com for e comparação de strings usando `==`.                                               | Utilização de `findByCategoria(categoria)` diretamente no repositório.                                                | Consultas ao banco de dados e comparação de Strings |

## Parte 2 — Ajustes de Clean Code

| #       | Onde estava | Qual princípio/boas práticas era violado | O que eu mudei |
| ------- | ----------- | ---------------------------------------- | -------------- |
| clean01 |             |                                          |                |
| clean02 |             |                                          |                |
| clean03 |             |                                          |                |
| clean04 |             |                                          |                |
| clean05 |             |                                          |                |
| clean06 |             |                                          |                |

---

## Parte 3 — Perguntas de reflexão

> Responda com suas palavras, 5 a 10 linhas cada, **usando o código real do projeto
> como exemplo**. Respostas genéricas de tutorial não pontuam.

### 1. Injeção de dependência (Aula 13)

Os controllers recebem os repositories via `@Autowired` (ex.: `ConteudoController`
usa `ConteudoRepository`). Explique por que o Spring precisa gerenciar esses objetos
em vez de criarmos com `new ConteudoRepository()`. O que exatamente o Spring faz ao
injetar um bean, e por que isso não funcionaria com um `new` comum?

### 2. JDBC vs Spring Data JPA (Aulas 12 e 13)

Na Aula 12 escrevemos um `ProdutoDAO` na mão com `Connection`, `PreparedStatement` e
`ResultSet`. Aqui o `ConteudoRepository` tem 2 linhas e faz CRUD completo. Compare as
duas abordagens: o que o Spring Data JPA automatiza, o que o JDBC/DAO ainda resolve
melhor, e como o `findByCategoria` consegue funcionar sem implementação.

### 3. Exceções checked vs unchecked (Aula 11)

A `ClassificacaoIndicativaException` estourava como um erro genérico do servidor,
sem mensagem útil para o cliente. Explique a diferença entre `extends Exception` e
`extends RuntimeException` no contexto desse bug, e como você fez a mensagem da
regra (classificação indicativa) chegar de forma clara ao cliente da API.

### 4. Sobrescrita vs sobrecarga (Aula 7)

Um dos bugs compilava sem nenhum erro: o método da `Serie` parecia sobrescrever
`calcularPrecoAluguel`, mas na verdade sobrecarregava. Explique a diferença entre
override e overload nesse caso e por que a anotação `@Override` teria impedido o bug.

### 5. Onde blindar o objeto? (Aulas 3, 4 e 13)

Vimos bugs de dados inválidos aceitos (duração negativa, créditos negativos, campos
nulos). Em quais lugares (construtor, setter, método do model) cada tipo de validação
deve ficar? Justifique usando os bugs que você encontrou e explique por que validar só
em um lugar não foi suficiente.

### 6. Abstração e interface (Aulas 8 e 9)

`Conteudo` é abstrata e `Promocionavel` é uma interface. Explique a diferença de
propósito entre as duas nesse projeto e o que mudaria no código se o Documentário
passasse a ter promoções — quais classes/linhas seriam tocadas e quais ficariam
intactas? O que isso diz sobre o design do sistema?

---

## Parte 4 — Espaço livre (opcional)

Alguma dificuldade, dúvida ou comentário sobre o checkpoint?

```

```
