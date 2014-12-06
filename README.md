Primeiro escrevi uma lista dos componentes e a estrutura do layout mas nada de HTML ou CSS.

```
header
  logo[4/12]
  menu[6/12]
  button matricule-se medium[2/12]
main
  article
    title#1
    video //curso com fundo notebook
    title#2
    p
  article
    title#3 Confira nossos cursos
    box-curso*3
      img thumb
      titulo
      resumo
    box-ver-mais
  article
    p
    button matricule-se
  article
    title#3[1/3] Aprenda diretamente com os melhores intrutores
  article
    title#3[1/3] Pronto para começar?
    - p
    img logo[1/3]
    button matricule-se large[1/3]
footer
```

Fiz algumas marcações de tamanho baseado no container para facilitar na hora de setar o grid. Perceba que nem fiz o footer pq sou vagabundo ehehheheh.

Depois criei a listagem dos primeiros átomos em `atoms/atoms-default.styl`:

```
.atom-img
.atom-img-logo

.atom-img-logo-small
.atom-img-logo-medium
.atom-img-logo-large
.atom-img-professor-avatar

.atom-input
.atom-input-email

.atom-button
.atom-button-matricule-se

.atom-button-matricule-se-small
.atom-button-matricule-se-medium
.atom-button-matricule-se-large

.atom-title
.atom-title-primary
.atom-title-secondary
.atom-title-section

.atom-link
.atom-link-menu

.atom-text
.atom-text-cursos
.atom-text-professor-resumo

```

São apenas um base para iniciar, pois provavelmente adicioneremos outros dependendo da necessidade.

Mas o correto era iniciar pelos `bósons` que são as menores unidades que irão "dar massa" aos átomo. Levando isso em consideração iniciei setando as variáveis das cores em `bosons/bosons-colors.styl`.

```
$azul-claro = #1680a7
$azul-cinza-escuro = #202b31
$branco = #fff
$verde-claro = #6ed5a2
$azul-cinza-medio = #788c95

$color-bg-body = $branco
$color-bg-header = $azul-claro
$color-bg-main = $branco
$color-bg-cursos = $azul-cinza-escuro
$color-bg-footer = $azul-cinza-escuro
$color-bg-button = $verde-claro
$color-title-main = $branco
$color-title-secondary = $azul-claro
$color-text = $azul-cinza-medio
$color-link = $verde-claro
```

Eu dei nome as cores para ficar mais facilmente indentificáveis e facilmente subistituídas. Após as cores criei o arquivo dos bósons de tipografia em `bosons/boson-typo.styl`

```
$font-default = 'Myriad Pro, Sans serif'
$font-bold = 'normal'
$font-size-base = 62.5%

set-font-size(value) {
    font-size: value; /* add PX (or original value) as backup */

    if (value is inherit) {
        /* do nothing, "inherit" has no unit */
    } else {
        font-size: remify(value);
    }
}

remify(value) { /* separate mixin, so it can be used elsewhere */
    u = unit(value);

    if (u is 'px') {
        return unit(value/16, 'rem');
    } else {
        return unit(value, u);
    }
}


```

Percebam esses 2 mixins `set-font-size` e `remify` eles irão fazer o trabalho de converter as unidades de pixels para REM, então você não precisará fazer mais cálculos, esses mixins foram retirados [daqui - Stylus REM](http://davidwalsh.name/stylus-rem).

Um exemplo do que será gerado com esse código:

```
.smaller {
  set-font-size(13px);
}
```

Vira:

```
.smaller {
  font-size: 13px;
  font-size: .8125rem;
  }
```

Outro caso interessante de se notar que como usamos REM para fazer nossas fonts diminuírem ou aumentarem dependendo da responsividade, podemos apenas setar o valor da font na tag HTML que é a root de todas e é de onde o REM deriva seu valor, como visto [nesse artigo](http://css-tricks.com/rems-ems/)

**BREAK IN**

Antes de iniciarmos vamos utilizar o normalize para podermos iniciar nosso visual igual em todos navegadores. Para isso o temos o [reset.styl](https://www.npmjs.org/package/reset.styl) lembrando que o Stylus eh JAVASCRIPT <3

```
npm install reset.styl
```

E para usarmos:

```
use_reset('normalize')
```

**BREAK OUT**

Agora vamos iniciar criando os estilos para nossos atomos, lembrando que eles precisam ser responsíveis, nosso padrão será o mobile-first. Então vamos criar as variáveis de tamanho em `bosons/boson-responsive.styl`

```
$tablet-size = '768px'
$desktop = '1000px'
```

Mas nossa responsividade será feita com o [Rupture](http://jenius.github.io/rupture/), bora instalar:

```
npm install rupture
```

##Iniciando

###Header

Vamos iniciar o desenvolvimento visual a partir dos componentes, então vamos começar pelo `header` que será um organismo pois agregará átomos e moléculas.

```
header
  img.atom-logo-header( src='logo-header.png', 
                        alt='Logo da empresa Webschool', 
                        aria-label='Logo da empresa Webschool')
  ul.molecule-menu-header
    li.atom-link-menu menu 1
    li.atom-link-menu menu 1
    li.atom-link-menu menu 1

  button.atom-matricule-se-small matricule-se
```

Para criarmos o `atom-logo-header` precisamos inicialmente criar o átomo padrão das imagens, `.atom-img` e posteriormente o `.atom-img-logo`:

```
// configurações básicas
// usando placeholder para 
// não criar uma classe a mais
$atom-img
  border-image 0
  margin 0
  padding 0
  display inline-block

// configurações específicas do logo
.atom-logo-header
  @extend $atom-img
  // para não ficar sem imagem caso o href não seja passado
  background-image url('../img/logo-header.png')
  // tamanho fixo da imagem
  // mesmo em telas maiores ele se mantém
  width 244
  height 103
```

####Placeholder

Utilizo o *placeholder* pois ele só gerará o código quando extendido, sem precisar criar uma classe antes para extender.

Por exemplo:

```
$bold
  font-weight bold

.title
  @extend $bold

```

Vai gerar:

```
.title{
  font-weight: bold;
}
```

Enquanto que usando classe fica:

```
.bold
  font-weight bold

.title
  @extend $bold

```


Que gera:

```
.bold,
.title{
  font-weight: bold;
}
```

No final das contas irá diminuir nosso código porém há um problema ainda com os pré-processadores onde você não pode re-utilizar um *placeholder* em uma `media query` que foi criado fora dela, mas para isso existem algumas *alternativas*.

Como a qual o [Diogo Moretti](https://github.com/diogomoretti/) fez no [grider](https://github.com/diogomoretti/grider) que gerou o mesmo grid para cada `media query`.

[Esse artigo que fala do mesmo tema porém com SASS também é uma ideia interessante e mais aprimorada](http://www.sitepoint.com/cross-media-query-extend-sass/).

Vamos usar uma forma um pouco diferente e mais simples, ideia dada pelo [Fernando Daciuk](https://github.com/fdaciuk), iremos definir as medias em um *mixin* e re-usá-las no nosso átomo, como visto abaixo:

```
$atom-img
  border-image: 0
  margin: 0 auto
  padding: 0
  display: block
  max-width: 100%
  margin-left: 20px
  margin-top: 8px
  width: 80%
  // tamanho padrão p/ telas acima do mobile
  @media (min-width: 600px)
    width: 200px

// configurações específicas do logo
atom-logo-header-medias()
 @media (min-width: 600px)
   width: 244px

.atom-logo-header
  atom-logo-header-medias()
  @extend $atom-img
  width: 40%
```

O resultado é esse:

![Imagem com tela maior de 600px](https://cldup.com/cqzTGhvlbd.png)
![Imagem com tela menor de 600px](https://cldup.com/hq2QlSU53W.png)
*\*adicionei um fundo pois o logo tem muito branco*

Infelizmente esse código duplica nosso CSS, porém infelizmente para re-usar um código em uma media query você precisa definir ele dentro dela. Existem formas melhores de fazer isso, quando refatoraremos essa parte.

```
.atom-logo-header {
  -webkit-border-image: 0;
  -o-border-image: 0;
     border-image: 0;
  margin: 0 auto;
  padding: 0;
  display: block;
  max-width: 100%;
  margin-left: 20px;
  margin-top: 8px;
  width: 80%;
}
@media (min-width: 600px) {
  .atom-logo-header,
  .atom-logo-header {
    width: 200px;
  }
}
.atom-logo-header {
  width: 40%;
}
@media (min-width: 600px) {
  .atom-logo-header {
    width: 244px;
  }
}
```

####Menu
Pronto agora vamos estilizar nosso componente de menu, inicialmente estilizamos apenas os `li's`, pois o `ul` é uma molécula que irá agregar 1 ou mais átomos, podendo esses ser qualquer coisa. Ex.: link, texto, imagem, vídeo, etc.

```
.atom-link-menu
  @extend $boson-link
  text-transform: uppercase
  color: $branco
```

Percebeu que extendemos o placeholder do `boson-link`? Pois ele será base para outros links assim como `atom-img` é para as imagens, iremos refatorar futuramente.

Ele está definido em `bosons/boson-typo`:

```
$atom-link
  text-align: left
  text-decoration: none
  font-size: 1em
  color: $verde-claro
```


Como estamos desevolvendo orientado a componentes vamos estilizar nossa molécula do menu para que ele se pareça com um menu mobile. Criamos o arquivo `molecules/index` pois ele será o único a ser importado no `main`, precisamos refatorar o de átomos também para que fique mais simples de gerenciar e manter.

```
.molecule-menu-header
  list-style: none
  display: inline-block
  float: right
  margin-right: 0.6em
```

Como estamos fazendo **mobile-first** vamos ter que criar um menu escondido e mostrar apenas aquele ícone que todos conhecem das 3 listras horizontais.

```
.atom-link-menu-icon
  background: url('../img/icon-png2x.png') no-repeat top center
  color: transparent
  display: block
  text-align: center
  text-indent: 99999px
  width: 2em
  height: 2em
  a
    span
      display: none
```

Nossa `view` poderia ficar assim:

```
ul.molecule-menu-header
  li.atom-link-menu-icon
    a(href='#')
      img(src='img/icon-png2x.png', alt='Clique para abrir o menu')
  li
    a.atom-link-menu(href='#') menuu
  li
    a.atom-link-menu(href='#') menu
  li
    a.atom-link-menu(href='#') menu
  li
    a.atom-link-menu(href='#') menu
```

![Imagem demonstrando como o menu está com o ícone](https://cldup.com/Yr2L05qyRN.png)


Porém ainda estamos mostrando a listagem abaixo, vamos refatorar e chegar na usabilidade já conhecida dos menus mobile.

Para isso vamos iniciar utilizando uma técnica onde criamos um `checkbox` invisível e adicionamos um `label` com o ícone do menu, quando esse label é clickado ou tocado nosso CSS irá fazer a lista do menu aparecer.

Vamos começar pela estrutura do nosso menu:

```
input(type="checkbox",
      id="atom-menu-checkbox",
      class="atom-menu-checkbox")
nav.molecule-menu
  label(for="atom-menu-checkbox",
      class="atom-menu-handle")
  ul.molecule-menu-header
    li.atom-item-menu
      a.atom-link-menu(href='#') menuu
    li.atom-item-menu
      a.atom-link-menu(href='#') menu
    li.atom-item-menu
      a.atom-link-menu(href='#') menu
    li.atom-item-menu
      a.atom-link-menu(href='#') menu
    li.atom-item-menu
      button.atom-matricule-se-small matricule-se
```

Percebeu que o `input` está fora?

É para termos acesso a ele quando o menu estiver escondido. Então vamos estilizar nosso `label` e esconder o `checkbox`:

```
.atom-menu-handle
  background: url('../img/icon-png2x.png') no-repeat top right
  clear: both
  cursor: pointer
  display: inline-block;
  float: right
  margin: 0.6rem 0.6rem 0 0
  width: 36px
  height: 24px
  position: relative

.atom-menu-checkbox
    display: none;
```

*O melhor seria usar um ícone em SVG :x*

E agora vamos criar a regra quando o usuário clickar/tocar no ícone para mostrar a lista do menu:

```
.atom-menu-checkbox:checked + .molecule-menu
  .molecule-menu-header
    max-height: 300px;
    clear: both
  .atom-item-menu
    visibility: visible;
```

Bem simples não? Técnica malandra mas não acho que seja gambiarra :p

![Foto de uma gambiarra real em um motor](https://atitudereflexiva.files.wordpress.com/2010/02/gambiarra_motor.jpg)

Antes de prosseguirmos vamos adicionar uma pitada de **acessibilidade** no nosso menu:

```
header(role="navigation" aria-label="Menu Principal")
  img.atom-logo-header( src='img/logo-header.png',
                        alt='Logo da empresa Webschool',
                        aria-label='Logo da empresa Webschool')

  input(type='checkbox',
        id='atom-menu-checkbox',
        class='atom-menu-checkbox')
  nav.molecule-menu(role='menu')
    label(for='atom-menu-checkbox',
          class='atom-menu-handle',
          role='menuitem',
          tabindex='0',
          aria-haspopup='true')
    ul.molecule-menu-header(role='menubar', 
                            aria-hidden='true')
      li.atom-item-menu(role='menuitem',
                        tabindex='-1')
        a.atom-link-menu(href='#') menuu
      li.atom-item-menu(role='menuitem',
                        tabindex='-1')
        a.atom-link-menu(href='#') menu
      li.atom-item-menu(role='menuitem',
                        tabindex='-1')
        a.atom-link-menu(href='#') menu
      li.atom-item-menu(role='menuitem',
                        tabindex='-1')
        a.atom-link-menu(href='#') menu
      li.atom-item-menu(role='menuitem',
                        tabindex='-1')
        button.atom-matricule-se-small matricule-se
```

Como não sou manjador quando em proporem melhorias eu atualizo aqui.

![Tela mostrando apenas o ícone e o menu escondido](https://cldup.com/thsEp8hnKu.png)
![Tela mostrando o ícone e o menu visível](https://cldup.com/b_PJj5QWvl.png)

No `tablet` vamos deixar a mesma identidade, só mudaremos para o `desktop` então vamos começar a refatorar:

**Próximo passo**

Vamos deixar o menu na horizontal quando passar do `tablet` para o `desktop`, para isso vamos refatorar o `atom-link-menu` para adicionar suas responsividades:

```
.atom-link-menu
  @extend $atom-link
  color: #fff
  text-transform: uppercase
  font-size: 0.8rem
  // font maior para o tablet por causa do espaço
  @media (min-width: 800px)
    font-size: 1.1rem
    // font menor para o desktop para deixar horizontal
  @media (min-width: 1000px)
    font-size: 0.75rem
    letter-spacing: 0.0625*2rem
```

Depois precisamos deixar o menu visível e os links na horizontal, caso o tamanho da tela for para desktop.

```
.atom-menu-checkbox + .molecule-menu
  .atom-item-menu
    @media (min-width: 1000px)
      display: inline
      visibility: visible
```

Não podemos esquecer de esconder o ícone do menu quando for desktop:

```
.atom-menu-handle
  background: url('../img/icon-png2x.png') no-repeat top right
  clear: both
  cursor: pointer
  float: right
  margin: 0.6rem 0.6rem 0 1rem
  width: 36px
  height: 24px
  position: relative
  @media (min-width: 1000px)
    display: none
```

Precisamos acertar algumas coisas na `molecule-menu` quando for desktop:

```
.molecule-menu
  max-width: 40%
  display: inline-block
  float: right
    @media (min-width: 1000px)
      max-width: 70%
      margin-top: 1.8rem
  .molecule-menu-header
    max-height: 0
    overflow: hidden
    @media (min-width: 1000px)
      overflow: visible
  .atom-item-menu
    visibility: hidden
    display: block
    padding: 0.5em 0.6em
    border: none
```

Com isso nosso menu ficará assim:

![Tela mostrando menu aberto no mobile](https://cldup.com/Edr1xeBYdY.thumb.png)
![Tela mostrando menu aberto no tablet](https://cldup.com/BCtXoL3z4S.png)
![Tela mostrando menu aberto no desktop](https://cldup.com/6TLeI7Snmf.png)

**Refatoração**

Antes de continuar vamos criar o arquivo `atoms/index.styl` com o seguinte código:

```
@import "atoms/atoms-default"
```

Dessa forma o `main` sempre chamará apenas um `index` de cada pasta.

```
@import "bosons/index"
@import "atoms/index"
@import "molecules/index"
```

E em `atoms/atoms-default` deixei a chamada para os 2 arquivos em separado, pois eles já estavam ficando muito grandes juntos.

```
@import 'atoms/atoms-img'
@import 'atoms/atoms-menu'
```

`atoms/atoms-img`:

```
$atom-img
  border-image: 0
  margin: 0 auto
  padding: 0
  display: block
  max-width: 100%
  margin-left: 20px
  margin-top: 8px
  width: 80%
  // tamanho padrão p/ telas acima do mobile
  @media (min-width: 600px)
    width: 200px

// configurações específicas do logo
atom-logo-header-medias()
 @media (min-width: 600px)
   width: 244px

.atom-logo-header
  atom-logo-header-medias()
  @extend $atom-img
  width: 40%
  display: inline-block
  float: left
```

`atoms/atoms-menu`:

```
.atom-menu-handle
  background: url('../img/icon-png2x.png') no-repeat top right
  clear: both
  cursor: pointer
  float: right
  margin: 0.6rem 0.6rem 0 1rem
  width: 36px
  height: 24px
  position: relative
  @media (min-width: 1000px)
    display: none

.atom-menu-checkbox
  display: none

.atom-menu-checkbox + .molecule-menu
  .atom-item-menu
    @media (min-width: 1000px)
      display: inline
      visibility: visible

.atom-menu-checkbox:checked + .molecule-menu
  .molecule-menu-header
    max-height: 300px
    clear: both
  .atom-item-menu
    visibility: visible

.atom-item-menu
  text-align: right
// Menu Desktop

.atom-link-menu
  @extend $atom-link
  color: #fff
  text-transform: uppercase
  font-size: 0.8rem
  // font maior para o tablet por causa do espaço
  @media (min-width: 800px)
    font-size: 1.1rem
    // font menor para o desktop para deixar horizontal
  @media (min-width: 1000px)
    font-size: 0.75rem
    letter-spacing: 0.0625*2rem
```

E o `bosons/index` fica:

```
@import "bosons/boson-colors"
@import "bosons/boson-responsive"
@import "bosons/boson-typo"
```

Pronto agora podemos continuar.

![Homer feliz porque podemos continuar](http://perlbal.hi-pi.com/blog-images/410584/gd/134662167782/Ufa.jpg)



####Botão matricule-se

Vamos criar seus átomos.




## Getting Started

### Installation

First of all, install the dependencies to run this boilerplate.

- [NodeJS](http://nodejs.org/)
- [GulpJS](http://gulpjs.com/)


```sh
# Clone this repository
$ git clone git://github.com/willianjusten/Fast.git new_project
$ cd new_project

# install dependencies
$ npm install
```

With the commands above, you have everything to start.

### Folders and Files

```sh
new_project -
	/build -
		/css
			main.css
		/img
		/js
			main.js
		.htaccess
	/src -
		/img
		/js
		/styl
		/templates
		.editorconfig
		gulpfile.js
		package.json
	/tests -
		/unit
```

### Tasks

- `gulp`: Initialize watch for changes and a server(localhost:8080)
- `gulp js`: execute js files
- `gulp jade`: compile jade files
- `gulp stylus`: compile stylus files
- `gulp imagemin`:compress image files
- `gulp connect`: inicialize a server
- `gulp watch`: call for watch files
- `gulp -p`: minify all files for production
- `gulp build -p`: minify files and deploy via rsync
- `karma start`: launch a phantonjs and watch for tests
