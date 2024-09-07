# Comandos básicos do GitHub

Após a configuração inicial, podemos criar um repositório e começar a
trabalhar com arquivos que serão "rastreados" pelo Git. 

Um repositório (ou *repo*) é um diretório local onde os arquivos de um
projeto serão armazenados. O Git funciona normalmente em um repositório
local, e não é necessário haver um servidor central para o armazenamento
dos arquivos (como é comum em *sistemas controladores de versão
centralizados* como CVS e Subversion). No entanto, o uso de um servidor
central é fundamental para projetos colaborativos e de software livre.

Um servidor do Git pode ser local (um servidor privado por exemplo), ou
remoto. Um servidor remoto é mais simples e fácil de ser utilizado pois
já existem alguns serviços disponíveis para hospedar repositórios do
Git. O GitHub é um destes servidores, mas existem
[vários outros disponíveis](https://git.wiki.kernel.org/index.php/GitHosting). A
escolha de um deles em particular não irá interferir no funcionamento do
Git, pois os comandos são os mesmos. A diferença se dá apenas na
visualização *on-line* e outras facilidades que cada servidor
apresenta. Aqui vamos utilizar o GitHub. 

## Criando um repositório

Para criar um repositório no GitHub, clique no botão `New repository`
disponível na sua página inicial, especifique um nome e opcionalmente
uma descrição. Aqui neste exemplo vou criar um repo chamado `git-teste`,
que está disponível em (https://github.com/fernandomayer/git-teste).

Até aqui o repositório foi criado no GitHub, e isso é importante pois
agora temos um endereço no servidor para onde vamos enviar os arquivos
criados localmente. Agora é a hora de criar um diretório local e iniciar
o Git fazendo ele se comunicar com o servidor. Para isso fazemos

```bash
# cria o diretório
$ mkdir git-teste
# entra
$ cd git-teste
# inicia o git
$ git init
```

O comando `git init` serve para "iniciar" o rastreamento de arquivos
pelo Git. Esse comando cria um diretório (oculto) `.git`, contendo as
configurações necessárias para o funcionamento do sistema. Esse comando
só é necessário uma vez.

Para informar o Git que este diretório deve se comunicar com o servidor
do GitHub criado acima fazemos

```bash
$ git remote add origin git@github.com:fernandomayer/git-teste.git
```

O comando `git remote add` serve para adicionar um repositório "remoto",
que por padrão o Git chama de `origin`, e que nada mais é do que um
"atalho" para o endereço do servidor. Note que neste caso estamos usando
um endereço no formato do `SSH`, mas poderia ser também o endereço
`https`. 

Nesse ponto podemos utilizar o comando `git status` para verificar o que
está contecendo no repositório

```bash
$ git status 
# On branch master
#
# Initial commit
#
nothing to commit (create/copy files and use "git add" to track)
```

Duas coisas chamam a atenção: o Git está mostrando que estamos em um
*branch* (ramo) chamado `master`, e que não há nada para ser enviado ou
adicionado ao repositório (*commit*). Um branch é como uma "linha de
desenvolvimento", sendo que sempre deve existir uma principal (que por
padrão é chamada de `master`), mas podem existir muitas outras. Por
enquanto vamos trabalhar apenas com o branch principal.

## Criando e rastreando arquivos

Para começar a usar o Git precisamos criar arquivos. No GitHub, um
arquivo chamdo `README` serve para identificar um projeto e aparece
autoomaticamente na página principal do repositório. Por isso, vamos
criar esse arquivo (usando um editor de texto qualquer) e adicionar
algum texto como

```
Repositório criado para testes com o GitHub.
```

Depois de salvar o arquivo podemos ver novamente o status

```bash
$ git status 
# On branch master
#
# Initial commit
#
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#	README
nothing added to commit but untracked files present (use "git add" to track)
```

Agora vemos que o Git automaticamente identificou que existe um arquivo
criado no repositório, mas ele ainda não está sendo rastreado. Se
quisermos que o Git rastreie todas as modificações de um arquivo temos
que adicioná-lo ao repositório com o comando `git add`

```bash
$ git add README
```

E o status irá mostrar que o arquivo foi adicionado, mas ainda não foi
enviado para o repositório local

```bash
$ git status 
# On branch master
#
# Initial commit
#
# Changes to be committed:
#   (use "git rm --cached <file>..." to unstage)
#
#	new file:   README
#
```

Nesse momento, o arquivo está em uma *staging area*, ou um lugar pronto
para ser enviado ao repositório local. Até aqui o arquivo não faz parte
do repositório do Git! Para isso temos que "enviá-lo", ou fazer um
*commit* 

```bash
$ git commit -m 'comentário'
[master (root-commit) 1490ab0] primeiro commit
 1 file changed, 1 insertion(+)
 create mode 100644 README
$ git status 
# On branch master
nothing to commit (working directory clean)
```

Repare que a flag `-m` serve para adicionar um comentário sobre o commit
que você está fazendo. Adicionar um comentário é **obrigatório** para
todos os commits. Se você não usar o `-m`, o editor de texto padrão do
seu sistema (ou aquele configurado inicialmente com `core.editor`,
[ver a etapa de configuração](./0_configuracao-inicial.md)) irá abrir
para que você entre com uma mensagem.

Alternativamente, podemos adicionar o arquivo (`git add`) e fazer o
commit (`git commit`) em apenas uma etapa, usando a opção `-a` no commit

```bash
$ git commit -a -m 'comentário'
```

Dessa forma o arquivo é indexado diretamente, sem passar pela *staging
area*. A vantagem é que elimina um comando. A desvantagem é que não
existe mais a chance de reverter um arquivo (*unstage*) antes de
comitar. 

Agora que o arquivo foi comitado, ele faz parte do repositório do Git,
mas apenas **localmente**. Para enviá-lo ao servidor remoto (já
configurado), usamos o comando `git push` com dois argumentos. O
primeiro indica o servidor remoto (`origin`), e o segundo o branch
(`master`) 

```bash
$ git push origin master 
Counting objects: 3, done.
Writing objects: 100% (3/3), 258 bytes, done.
Total 3 (delta 0), reused 0 (delta 0)
To git@github.com:fernandomayer/git-teste.git
 * [new branch]      master -> master
```

## Resumo: uso típico do Git

O processo mostrado em detalhes acima é o procedimento mais simples e
básico para rastrear arquivos com o Git. Esse processo se repete toda
vez que arquivos forem modificados e/ou adicionados ou removidos do
repositório. 

Podemos resumir esse *workflow* do Git dessa forma:

```bash
# arquivo foo.R adicionado/modificado
$ git add foo.R
$ git commit -m 'mensagem'
$ git push origin master

# arquivo bar.R adicionado/modificado
$ git add bar.R
$ git commit -m 'mensagem'
$ git push origin master

# arquivos foo.R e bar.R modificados
$ git add .
$ git commit -m 'mensagem'
$ git push origin master
```

## Alguns comandos úteis

### Remover e mover arquivos

Comandos similares aos do shell `rm` e `mv`, mas mantém a
rastreabilidade do git

```bash
$ git rm foo.R
$ git mv foo.R
```
Para remover arquivos do repositorio remoto, mas manter localmente

```bash
$ git rm --cached foo.R
```
este comando é útil se você enviou para o repositório remoto algum
arquivo que não deveria ou não precisava (como arquivos de compilação
do LaTeX, ...). Para evitar que isso aconteça é possível criar um
arquivo chamado .gitignore contendo os arquivos que não precisam ser
enviados ao servidor remoto

### Logs

Mostra o log das alterações nos arquivos (mensagens de commit)

```bash
$ git log
# em uma linha
$ git log --pretty=oneline
```

### Recuperando versões de arquivos

Para reverter um único arquivo para uma versão anterior (sabendo que o
arquivo é foo.R e o hash SHA do commit é, por exemplo,
`03f9c0abe911440339ecb82e08ec8ae65ca11a7b`)

```bash
$ git checkout 03f9c0a foo.R
```

Para reverter um unico arquivo para uma versao anterior (sendo que
esse arquivo foi DELETADO em algum commit anterior)
se voce nao lembra o nome do arquivo, execute esse comando que
identifica os modos 'delete'

```bash
$ git log --diff-filter=D --summary | grep delete
```
Sabendo o nome do arquivo, voce pode conferir o log para identificar o
commit que o deletou

```bash
$ git log -- <arquivo-deletado>
```
NOTE que se o arquivo estava em um sub-diretorio dentro do seu
projeto, ele deve ser especificado com o caminho relativo para esse
diretorio. Para ter certeza do commit que deletou o arquivo, veja

```bash
$ git rev-list -n 1 HEAD -- <arquivo-deletado>
```
E finalmente para recuperar o arquivo faca o checkout no commit
anterior (por isso o `^` ao final do commit)

```basg
$ git checkout <commit-que-deletou>^ -- <arquivo-deletado>
```
O arquivo deletado devera voltar para o diretorio (ou sub-diretorio se
ele estava em um) como um novo arquivo.

### Commits

Para corrigir uma mensagem de commit que ainda não foi enviada para um
remote. Essa será a mensagem utilizada nos logs e irá substituir
qualquer outra anterior

```bash
$ git commit --amend -m 'mensagem nova'
```

### Tags

Criar tags em determinados pontos de um projeto é uma maneira eficaz de
poder voltar facilmente naquele ponto e verificar a situação dos
arquivos naquele momento. No desenvolvimento de um software por exemplo,
os tags podem marcar as versões (ex.: `v0.1`, `v0.2`, ...) do código ao
longo do tempo.

No git, as tags podem ser leves (*lightweight*) ou anotadas
(*annotated*). A diferença é que uma tag leve é só um "ponteiro" para um
commit específico, enquanto que uma tag anotada é como se fosse um novo
commit: ela armazena o nome de quem criou a tag, e-mail, data, e uma
mensagem.

Para criar uma tag leve faça

```bash
git tag v0.1
```

Para criar uma tag anotada (geralmente mais recomendado), faça:

```bash
git tag -a v0.1 -m 'mensagem de commit'
```

A mensagem de commit aqui também é obrigatória. Se você não especificar
com a opção `-m`, um editor irá abrir para você inserir uma mensagem.

Para verificar as tags existentes ou criadas, veja

```bash
git tag -l
```

Para ver todos os commits especificos associados a uma tag, use

```bash
git show v0.1
```

IMPORTANTE: se você está trabalhando com um repositório remoto (como o
GitHub), é necessário dar um push específico para que a tag também seja
enviada para o servidor. As tags criadas acima são apenas locais. Para
enviar a tag para o servidor, faça um push na tag:

```bash
git push origin v0.1
```

*****

Voltar: [Configuração inicial](0_configuracao-inicial.md) | 
Continuar: [Trabalhando com branches](2_trabalhando-com-branches.md)
