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

###Iniciando

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

Vamos usar uma forma um pouco diferente e mais simples, iremos definir as medias no nosso *placeholder* e re-usá-las no nosso átomo, como visto abaixo:

```
// configurações básicas
// usando placeholder para 
// não criar uma classe a mais
$atom-img
  border-image: 10px
  margin: 0
  padding: 0
  display: inline-block
  max-width: 100%
  // no mobile a imagem é centralizada
  @media (max-width: 400px)
    display block
    margin 0 auto

// configurações específicas do logo
.atom-logo-header
  @extend $atom-img
  // para não ficar sem imagem caso o href não seja passado
  background-image: url('../img/logo-header.png')
  background-repeat: none
  // tamanho fixo da imagem
  // mesmo em telas maiores ele se mantém
  width: 244px
  height: 103px
  @media (min-width: 600px)
    @extend $atom-img
  @media (min-width: 800px)
    @extend $atom-img
```

O resultado é esse (adicionei um fundo pois o logo tem muito branco):

![Imagem com tela maior de 400px](https://cldup.com/6C8a9Hu_RP.png)
![Imagem com tela menor de 400px](https://cldup.com/boepXEqPW1.png)



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
