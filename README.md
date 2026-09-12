# Checkpoint 4 — Bug Hunt StreamFIAP

## Identificação

| Integrante                      | RM       | Turma |
| ------------------------------- | -------- | ----- |
| Felipe Souza Carvalho           | RM564779 | 2CCPH |
| Rodrigo Kenshin Viana Matayoshi | RM564026 | 2CCPH |
| Riquelme Santos da Mata         | RM565053 | 2CCPH |
| Luiz Henrique Barbosa Dias      | RM562399 | 2CCPH |

| Campo                              |             |
| ---------------------------------- | ----------- |
| **Total de bugs corrigidos**       | 12 / 12 |
| **Total de ajustes de Clean Code** | 6 / 6  |

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
| clean01 | Usuario.java |        Explicitar variáveis ambiguas    |     Renomeação de variaveis     |
| clean02 |   Arquivos pasta model    |    Quebra o encapsulamento    |   Adicionamos setters nas variaveis protected   |
| clean03 |  ConteudoController.java  |  Dead code no código  |        Retirada de codigo sem uso        |
| clean04 | ConteudoController.java |           Encapsulamento          | Aplicamos o encapsulamento no duracaoMinutos  |
| clean05 | Conteudo.java | Permite que qualquer outra classe altere seus dados diretamente| Alteramos o modificador de acesso |
| clean06 |Usuario.java | Separação de Responsabilidades | Retiramos o recibo de usuario e movemos para o controller |

---

## Parte 3 — Perguntas de reflexão

### 1. Injeção de dependência (Aula 13)

Os controllers recebem os repositories via `@Autowired` (ex.: `ConteudoController`
usa `ConteudoRepository`). Explique por que o Spring precisa gerenciar esses objetos
em vez de criarmos com `new ConteudoRepository()`. O que exatamente o Spring faz ao
injetar um bean, e por que isso não funcionaria com um `new` comum?

## Resposta

O `ConteudoController` só deveria saber usar o `ConteudoRepository`, não criar um. O `@Autowired` do Spring procura num container um bean que satisfaça aquele tipo, cria/reaproveita a instância já com a conexão ao Oracle configurada, e a injeta no campo do controller. Isso desacopla o controller da implementação concreta

---

### 2. JDBC vs Spring Data JPA (Aulas 12 e 13)

Na Aula 12 escrevemos um `ProdutoDAO` na mão com `Connection`, `PreparedStatement` e
`ResultSet`. Aqui o `ConteudoRepository` tem 2 linhas e faz CRUD completo. Compare as
duas abordagens: o que o Spring Data JPA automatiza, o que o JDBC/DAO ainda resolve
melhor, e como o `findByCategoria` consegue funcionar sem implementação.

## Resposta

Antes escrevíamos manualmente Connection, `PreparedStatement`, `ResultSet`, tratávamos `SQLException` e fazíamos o mapeamento na mão. O ConteudoRepository é só uma interface com duas linhas e o Spring Data JPA gera a implementação inteira em runtime, ele lê o nome do método `findByCategoria` e monta o SQL sozinho, sem escrevermos nenhuma query. Entretanto o JDBC/DAO ainda vence quando precisamos de queries muito complexas e performance

---

### 3. Exceções checked vs unchecked (Aula 11)

A `ClassificacaoIndicativaException` estourava como um erro genérico do servidor, sem mensagem útil para o cliente. Explique a diferença entre `extends Exception` e `extends RuntimeException` no contexto desse bug, e como você fez a mensagem daregra (classificação indicativa) chegar de forma clara ao cliente da API.

## Resposta

O `ClassificacaoIndicativaException extends Exception` é uma exceção checked pois, o compilador obriga a declarar throws mas isso não significa que o Spring saiba tratá-la: sem um `@ExceptionHandler` para ela no `GlobalExceptionHandler`, ela chega até o container e vira um 500, sem a mensagem que o `Usuario.alugar` monta corretamente. As outras exceções do projeto são unchecked (`extends RuntimeException`), não exigem throws na assinatura, mas cada uma tem seu handler mapeado para um status HTTP específico (404, 422, 409). A correção foi adicionar um `@ExceptionHandler(ClassificacaoIndicativaException.class)` no `GlobalExceptionHandler`, devolvendo a mensagem já existente com um status apropriado, igual já era feito para as outras.

---

### 4. Sobrescrita vs sobrecarga (Aula 7)

Um dos bugs compilava sem nenhum erro: o método da `Serie` parecia sobrescrever
`calcularPrecoAluguel`, mas na verdade sobrecarregava. Explique a diferença entre
override e overload nesse caso e por que a anotação `@Override` teria impedido o bug.

## Resposta

Sobrescrita (override) é quando a subclasse redefine um método com a mesma assinatura da superclasse, mudando o comportamento mas mantendo o contrato. Sobrecarga (overload) é criar um método com o mesmo nome mas parâmetros diferentes, é um método novo, não substitui nada. Serie declarava `calcularPrecoAluguel(double desconto)`, com um parâmetro a mais do que `calcularPrecoAluguel()` de Conteudo. Para o compilador isso é overload, não override, então quando `AluguelController`/`Usuario.alugar` chama `conteudo.calcularPrecoAluguel()`, o Java resolve para a implementação de Conteudo, que sempre devolve 9.90 onde a regra de 4,90 por temporada nunca era executada. Se o método da Serie tivesse `@Override`, o compilador teria acusado o erro, expondo o bug em tempo de compilação em vez de deixá-lo passar silenciosamente

---

### 5. Onde blindar o objeto? (Aulas 3, 4 e 13)

Vimos bugs de dados inválidos aceitos (duração negativa, créditos negativos, campos
nulos). Em quais lugares (construtor, setter, método do model) cada tipo de validação
deve ficar? Justifique usando os bugs que você encontrou e explique por que validar só
em um lugar não foi suficiente.

## Resposta

Validação de invariante de estado do objeto (duração <= 0, créditos negativos) deve morar no construtor e nos setters do model, porque é lá que a entidade garante que nunca existirá em um estado inválido, independentemente de quem a cria. No projeto, duracaoMinutos é público e sem nenhuma validação em lugar nenhum e por isso qualquer POST aceita duração negativa ou zero. Já a validação de regra de negócio contextual, como "usuário não pode alugar se não tiver créditos" ou "se for menor que a classificação", faz mais sentido no método que executa a ação (`Usuario.alugar`), porque depende da interação entre dois objetos (`usuário` e `conteúdo`), não é um invariante isolado de um único objeto. Validar só no controller não basta, porque qualquer outro caminho de código que crie/chame o model diretamente pula a validação então o model acaba precisando se proteger sozinho.

---

### 6. Abstração e interface (Aulas 8 e 9)

`Conteudo` é abstrata e `Promocionavel` é uma interface. Explique a diferença de
propósito entre as duas nesse projeto e o que mudaria no código se o Documentário
passasse a ter promoções — quais classes/linhas seriam tocadas e quais ficariam
intactas? O que isso diz sobre o design do sistema?

## Resposta

`Conteudo` é uma classe abstrata porque representa um "é um" comum a Filme, Serie e Documentario (eles compartilham estados como: titulo, duracaoMinutos, classificacaoEtaria) e um comportamento padrão (`calcularPrecoAluguel()`), então faz sentido herança. `Promocionavel` é uma interface porque representa uma capacidade opcional ("pode ser promovido"), não uma identidade (nem todo Conteudo participa de promoção), então usar herança forçaria todo mundo a ter o método. Se o Documentário passasse a ter promoção, nós só precisariamos mudar `public class Documentario extends Conteudo` para `implements Promocionavel` e implementar `aplicarPromocao(double preco)` e nada mudaria em Filme, Serie, Conteudo ou nos controllers, porque `calcularPrecoPromocional()` em Conteudo já faz `if (this instanceof Promocionavel)`. Isso mostra que separar herança de interface deixa o sistema aberto para extensão sem tocar em código que já funciona (o princípio Open/Closed).

---

## Parte 4 — Espaço livre (opcional)

Alguma dificuldade, dúvida ou comentário sobre o checkpoint?

```

```
