# ARQUITETURA DE DESENVOLVIMENTO JAVA - FIAP
# Desafio 
Desenvolver um sistema de e-commerce.

# Duck Store: Relatório Técnico
# Objetivos
O principal objetivo deste relatório é discursar sobre a implementação da primeira fase do projeto.
Como objetivos específicos:
Apresentar a estrutura e arquitetura;
Discutir sobre escolhas realizadas e desafios encontrados.

# Duck Store
Duck Store é um sistema de e-commerce e tem por princípios fundamentais a utilização de tecnologias modernas e escaláveis para prover uma solução otimizada e eficiente, capaz de responder às variações de demanda diárias ou sazonais.
Em desenvolvimento como trabalho de curso da pós-graduação em Arquitetura e Desenvolvimento Java na FIAP.

# Solução adotada
Acreditamos que o modelo de microsserviços se encaixou perfeitamente no contexto por proporcionar maior granularidade no espaço da solução, fornecendo confortavelmente flexibilidade para um produto que pode mudar constantemente e de maneira veloz, como podemos perceber já que existe expectativa de crescimento tão grande tanto na equipe quanto na base de usuários que tende a ser multinacional. Com essa arquitetura, a Lei de Conway pode ser facilmente observada ao atribuir pequenos times para cuidar de cada sistema e dar um suporte contínuo e de alta qualidade em cada ponto. Além disso, a possibilidade de utilizar o canary deployment e de realizar implantação em cada microsserviço de forma separada corrobora com a necessidade de manter a solução online 24h. Em suma, todos os requisitos não funcionais guiaram essa decisão.
Em contrapartida, existe uma complexidade grande envolvida nesse tipo de solução que pode ser um risco quando mal aplicada, por isso é crucial o bom planejamento em cada decisão. Explicamos a partir daqui como fizemos o processo de descoberta de cada microsserviço, quais padrões foram escolhidos e por que escolhemos cada um.
Visão arquitetural da solução em camadas

Adotamos a arquitetura em camadas, nas quais, dividimos o projeto em pacotes a saber, controladora, serviço e domínio. Estão presentes também, os padrões de Domain Driven Design (DDD) e Representational State Transfer (REST), promovendo uma estrutura eficiente e clara.
A camada de domínio assume a administração das entidades inerentes às regras de negócio.

Em paralelo, a camada de serviço é encarregada de implantar essas regras de negócio, concentrando a complexidade neste ponto, o que contribui para a robustez do nosso sistema.
Já a camada controladora tem a função de lidar com as requisições recebidas, bem como retornar as respostas adequadas para o cliente. Dessa maneira, conseguimos manter a responsabilidade da camada controladora mais enxuta e com foco estrito em sua função.
Essa distribuição de responsabilidades assegura a manutenção de um código mais limpo e organizado, além de possibilitar um gerenciamento mais eficaz da complexidade na camada de serviço.

# Desenvolvimento
# Tecnologias e Ferramentas
Configuramos um projeto utilizando a stack Java 17, Spring Boot 3 e a interface JPA (Java Persistence API) combinada com um banco de dados relacional MySQL para persistência. 

Para gerenciamento de dependências do projeto utilizamos o Maven.

Também consumimos uma API externa, a PayPal, para auxiliar na obtenção de dados de pagamento. Utilizamos o Feign Client como meio de comunicação com API´s externas.

# Camada de Domínio
Neste primeiro momento, foram implementadas três entidades do subdomínio de suporte relativas ao cadastro de informações no sistema: Item,  Purchas,PurchaseItem, Stock 
Além das entidades, também temos classes auxiliares de repositórios, responsáveis pela persistência de dados.

# Camada de Domínio Item:
Item: representa um item do sistema e gerencia informações como Nome, description,price, category, etc.
Purchase: representa o pedido de compra.
PurchaseItem: representa o relacionamento de item com compra.
Stock: trata dos dados sobre itens cadastrados em estoque.

# Camada de Domínio Buy:
CartItem: A entidade CartItem representa um item individual no carrinho de compras. É como se fosse uma linha numa lista de compras, contendo informações sobre um produto específico que o usuário deseja comprar. 
ShoppingCart: A entidade ShoppingCart representa o carrinho de compras inteiro. É como se fosse o contêiner que guarda todos os CartItems que o usuário deseja comprar. Ele pode ter vários CartItems, cada um representando um produto diferente (ou quantidades diferentes do mesmo produto) que o usuário deseja comprar.
Camadas de Controladora e Serviço
Nesta fase do projeto, ambas as camadas foram responsáveis pela implementação do CRUD das entidades.
Em nossa implementação a camada da controladora ficou responsável pela especificação dos endpoints e implantação do CRUD seguindo os preceitos do REST, como utilização dos verbos do protocolo HTML e a não persistência de estado entre as requisições. Enquanto o service, cuidou das funcionalidades do CRUD.
Por questões de organização os DTOs foram divididos entre request e response dentro da camada da controladora.
Neste momento, todos os métodos de exclusão efetivamente deletam os registros da base de dados.

# API
Nossa da API foi disponibilizada no Postman. Nela está o detalhamento das nossas rotas de API, com exemplos de requisições e respostas.
API:
Item / Auth / Buy 

https://documenter.getpostman.com/view/28287473/2sA35D53PD

# API Payment

https://documenter.getpostman.com/view/16901424/2sA35D53cW

# Discussões
Nesta seção, iremos abordar alguns pontos de destaque no desenvolvimento desta primeira fase:
Persistência: JPA e Banco de dados MySQL;
Tratamento de Exceções e Handler;
Validação de dados: expansão das anotações;
Consumo de API externa PayPa.

# Segurança.
Persistência de dados
No projeto, na persistência de dados, optamos pelo uso da interface JPA em conjunto com o banco de dados MySQL. Dessa forma, aproveitamos para testar o funcionamento de nossas funcionalidades enquanto em desenvolvimento.
Tratamento de Exceções
Para o tratamento de exceções, optamos por utilizar um Handler para capturar e retornar uma resposta padronizada para o cliente. Dessa forma, não é necessário tratar as exceções em cada método da controladora, o que pode ser considerado uma boa prática para melhorar a compreensão do código. Também, para padronizarmos o retorno dos erros na API criamos um DTO, chamado de ErrorResponse, responsável por encapsular e padronizar esses retornos.

# Validações de dados
Adicionalmente, utilizamos as anotações de validação do framework Jakarta nas variáveis dos DTOs. Dessa forma, conseguimos especificar se uma variável deve ou não ser nula, estar em branco ou se datas devem ser passadas ou futuras. No caso de um dado não passar na validação, também ocorre o lançamento de uma exceção.
Consumo de API externa
O serviço da API do PayPal é uma interface que permite a integração de sistemas com a plataforma de pagamentos online do PayPal. Essa API oferece uma variedade de funcionalidades, como processamento de pagamentos, reembolsos, consultas de saldo, entre outras operações relacionadas a transações financeiras.

Para consumir a API do PayPal, foi necessário configurar as credenciais de acesso e criar um serviço que simulou as operações de pagamento.

Também foi usado para  os endpoints da API do PayPal  requisições do tipo REST, a qual, cada método  corresponde a um endpoint específico do controller. O método createPayment() simula o endpoint de criação de pagamento, o listAllPayments() simula o endpoint de listagem de todos os pagamentos e o getPaymentDetails() chama o endpoint de obtenção dos detalhes de um pagamento específico.
Considerações Finais
Portanto, podemos afirmar que o projeto foi concluído com sucesso. A implementação das funcionalidades foi realizada de forma satisfatória, com a criação dos CRUDs das entidades de suporte e a utilização da arquitetura em camadas, alcançando assim os objetivos propostos. 
Além disso, a equipe conseguiu superar os desafios encontrados durante o desenvolvimento, tais como a implementação do Handler Exception e do PayPal, como também foi observado questões de boas práticas fundamentadas no SOLID e DDD, o que contribuiu para o aprendizado de todos os membros do grupo.

# Repositórios
https://github.com/FIAP-Pos-tech/duck-item

https://github.com/FIAP-Pos-tech/duck-store

https://github.com/FIAP-Pos-tech/duck-payment

https://github.com/FIAP-Pos-tech/duck-auth

# Pré-requisitos
Java JDK 17;
Maven;

# Instalação do Java 17
Acesse o site oficial do OpenJDK (https://adoptium.net/) ou do Oracle (https://www.oracle.com/java/technologies/javase-jdk17-downloads.html) para fazer o download do JDK 17 de acordo com o seu sistema operacional.
Na página de downloads, escolha a versão adequada ao seu sistema operacional (Windows, macOS ou Linux) e clique no link de download.
Siga as instruções de instalação específicas do seu sistema operacional

# Instalação do Maven

Acesse o site oficial do Apache Maven em https://maven.apache.org/download.cgi.
Baixe a versão binária (ZIP) mais recente.
Extraia o conteúdo do arquivo ZIP para um diretório de sua escolha.
Configure a variável de ambiente M2_HOME para o diretório do Maven.
Adicione %M2_HOME%\bin ao caminho (Path) do sistema.
Abra um novo terminal ou prompt de comando.
Execute o comando mvn --version para verificar se a instalação foi concluída com sucesso.











 





