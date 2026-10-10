# Documentação

## Problema Encontrado:

Exposição da mensagem secreta no pipeline, prejudicando a confidencialidade dos dados.

## Nome do Secret

MENSAGEM_SECRETA

## Secret -> Aplicação

O Secret é criado em Settings -> Security and quality -> Secrets and variables -> Actions -> New repository secret. Depois é injetado como valor da variável de ambiente MENSAGEM_SECRETA a partir do uso ${{ secrets.MENSAGEM_SECRETA }} na chave env no passo "Executar aplicação" da pipeline (main.yml). Então, ela é disponível para o programa em Node.js, no qual será referenciada no arquivo server.js através do comando process.env.MENSAGEM_SECRETA.

## Evidência de Execução

Saída da execução do passo "Executar aplicação":

```
Run cd app
  cd app
  node server.js
  shell: /usr/bin/bash -e {0}
  env:
    MENSAGEM_SECRETA: ***
Secret recebido pela aplicação com sucesso.
Quantidade de caracteres: 84
```

## Situação Inicial - Respostas

### 1. Onde o segredo está armazenado?

No próprio código-fonte, ou seja, no arquivo main.yml exposto.

### 2. Quem poderia visualizar essa informação?

Qualquer pessoa com acesso ao link do repositório, pois ele é público.

### 3. Por que essa prática não é recomendada?

Porque dados sensíveis serão expostos, compromentendo a confidencialidade, aumentando a superfície de ataque de algum usuário malicioso usando esses dados disponíveis no código-fonte.

### 4. O que poderia acontecer se essa informação fosse uma senha ou um token real?

Qualquer pessoa poderia ter acesso ao serviço utilizando essa senha ou token. Ela poderia fazer qualquer tipo de ação, como roubo de dados, remover informações importantes, ou até usar algum recurso de forma exagerada, gerando grandes prejuízos financeiros para quem expôs.

## Questões

### 1. Qual é a diferença entre armazenar o valor diretamente no pipeline.yml e armazená-lo no GitHub Secrets?

Ao armazenar o valor no GitHub Secrets ele é criptografado e mascarado nos logs da pipeline e apenas o adminsitradores do repositório podem modificar, já usando diretametne ele será exposto tanto no código-fonte quanto no log para qualquer pessoa que tiver acesso.

### 2. Onde o valor do Secret fica configurado?

O valor ficar armazenado nos servidores do GitHub de foram criptografada. Podemos configurá-los seguinto o caminho: Settings -> Security and quality -> Secrets and variables -> Actions -> New repository secret

### 3. O valor do Secret deve ser colocado no código da aplicação?

Jamais! O valor do Secret nunca deve ser exposto no código.

### 4. Qual é a função de: process.env.MENSAGEM_SECRETA

É a forma como podemos referenciar a variável de ambiente MENSAGEM_SECRETA no node.js.

### 5. Por que utilizar Secrets é importante em uma pipeline de CI/CD?

Para evitar exposição de dados sensíveis que podem ser usados por qualquer pessoa não autorizada.

### 6. Se fosse necessário utilizar uma senha real de banco de dados, por que seria inadequado colocar essa senha diretamente no arquivo .yml?

Porque qualquer pessoa com acesso público ao .yml conseguiria ver essa senha e usar para fins prejudiciais como roubo de dados e até para ataques de ransomware.
