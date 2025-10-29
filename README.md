![Imagem sobre mobile first](http://designshack.net/wp-content/uploads/ds-mobilefirst-2.jpg)

Bom estou escrevendo esse passo-a-passo pois me foi pedido via inbox do Facebook para que eu mostrasse como eu iria programar o site que estou desenvolvendo para um curso de Javascript que na verdade serão vários mini-cursos podendo ter outro professores.

Então achei uma boa oportunidade para colocar em prática alguns estudos e experimentar coisas novas como: Stylus e Mobile-first. Juntando tudo com [Atomic Design](http://nomadev.com.br/atomic-design-por-que-usar/), esse post vai ficar gigante e muito provavelmente virará um mini-curso e uma palestra também, até porque de nada vale aprender se não ensinar, imho.

##Analisando

Bom vamos ver como é o layout com o qual vamos trabalhar, vamos nos basear nele pois coisas podem ser modificadas durante o desenvolvimento.

![Layout a ser utilizado no estudo](https://cldup.com/Deq7yQ11XI.jpg)


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

Para criarmos variárveis no Stylus basta definir o nome depois `=` e o valor, por definição usamos o `$` como prefixo padrão, [leia mais sobre o porquê usar $](https://github.com/LearnBoost/stylus/wiki/1.0.0#what-to-change-in-syntax).

Nomeei as cores para ficar mais facilmente indentificáveis e facilmente subistituídas. Após as cores criei o arquivo dos bósons de tipografia em `bosons/boson-typo.styl`

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
$tablet-size = 768px
$desktop-size = 1000px
```

Mas depois nossa responsividade será feita com o [Rupture](http://jenius.github.io/rupture/), bora instalar:

```
npm install rupture
```

##Iniciando

###Header

![Imagem do header do layout usado](https://cldup.com/crlHJwsVrE.jpg)


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

![Imagem com tela menor de 600px](https://cldup.com/hq2QlSU53W.png)
![Imagem com tela maior de 600px](https://cldup.com/cqzTGhvlbd.png)
**adicionei um fundo pois o logo tem muito branco*

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

Também precisamos arrumar nossos valores das *media queries*


Pronto agora podemos continuar.

![Homer feliz porque podemos continuar](http://perlbal.hi-pi.com/blog-images/410584/gd/134662167782/Ufa.jpg)



####Botão matricule-se

Vamos criar seus átomos.

```
border-radius()
  -webkit-border-radius arguments
  -moz-border-radius arguments
  border-radius arguments

$atom-button
  max-width: 100%
  border: 0
  border-radius 5px
$atom-button-matricule-se
  @extend $atom-button
  background-color: $verde-claro
  color: $branco
  font-size: 0.70rem
  font-weight: 600
  text-transform: uppercase
  letter-spacing: 0.0625*2rem
  padding-top: 6px
.atom-button-matricule-se-small
  @extend $atom-button-matricule-se
  width: 138px
  height: 42px
// .atom-button-matricule-se-medium
// .atom-button-matricule-se-large
```

Esse *mixin* transparente `border-radius` é o primeiro exemplo dado no próprio site do [Stylus](http://learnboost.github.io/stylus/)!

Adicionamos a chamada do nosso módulo em `atoms-default`

```
@import 'atoms/atoms-img'
@import 'atoms/atoms-menu'
@import 'atoms/atoms-button'
```


![Botão matricule-se no menu mobile](https://cldup.com/0vFfEesAAD.png)
![Botão matricule-se no menu tablet](https://cldup.com/Ef2d5xhDQK.png)
![Botão matricule-se no menu desktop](https://cldup.com/nvHsQuS-rV.png)

Legal finalizamos nosso organismo do header, depois quando modularizarmos melhor nossa `view` ficará mais fácil de ver.

###Main

![Imagem mostrando o main do layout](https://cldup.com/crlHJwsVrE.jpg)

Vamos iniciar criando uma seção para o vídeo de chamada do curso.

![Imagem mostrando a seção da chamada do vídeo](https://cldup.com/UYtqmRxICy.jpg)

```
main
  article
    h1.atom-title-primary
      | Quer aprender Javascript de VERDADE?
    .molecule-video-chamada
      video.atom-video(src='https://www.youtube.com/watch?v=gS7oD8r9YSY')
    h2.atom-title-secondary Aprenda, capacite-se e destaque-se no mercado.
    p
      | Lorem ipsum dolor sit amet, consectetur adipisicing elit. Illum, 
      | incidunt! Quis culpa ex, provident nesciunt minima recusandae 
      | veniam nihil accusantium, id deleniti doloremque ut sit iste 
      | officiis commodi asperiores laudantium.
```

Encapsulamos em um article seguindo a [orientação do HTML5](http://alistapart.com/article/previewofhtml5), vamos iniciar estilizando o `atom-title-primary`:

```
.atom-title-primary
  @extend $atom-title
  color: $branco
  font-size: 2rem
  text-align: center
```

Em `bosons/boson-typo` criamos o `$atom-title`:

```
$atom-title
  font-weight: 800
  clear: both
```

![Tela com o título primário no mobile](https://cldup.com/eXp2urUvYT.png)
![Tela com o título primário no desktop](https://cldup.com/076FJ4wOeV.png)

###Vídeo de chamada

![Imagem mostrando o vídeo e o fundo preto](https://cldup.com/yzTncJr2zA.jpg)

Agora vamos chegar em um ponto crítico do nosso projeto, pois haverá uma grande mudança de layout do `mobile` para o `desktop`.

```
$atom-video
  width: 100%
  max-width: 100%
  min-height: 16rem
  @media (min-width: $tablet-size)
    min-height: 30rem
  @media (min-width: $desktop-size)
    min-height: 35rem

.atom-video-chamada-curso
  @extend $atom-video
```


![Tela mostrando o video no mobile](https://cldup.com/p04NSAcmLE.png)
![Tela mostrando o video no tablet](https://cldup.com/mjzhKzN3OC.png)
![Tela mostrando o video no desktop](https://cldup.com/HAtQJcgCQv.png)

Porém no layout o vídeo fica dentro de um notebook, no `desktop`, que precisamos usar de fundo para esse vídeo.

![](https://cldup.com/dvYuOZspaT.png)

Refatorando nossa view:

```
section.molecule-video-chamada
  iframe.atom-video-chamada-curso(src='//www.youtube.com/embed/gS7oD8r9YSY',
                                  frameborder='0',
                                  allowfullscreen='')
```


E nosso visual:

```
$atom-video
  width: 100%
  max-width: 100%
  min-height: 16rem
  display: block
  @media (min-width: $tablet-size)
    min-height: 30rem
  @media (min-width: $desktop-size)
    min-height: 340px
    width: 550px
    margin: 0 auto
    position: relative
    top: 40px

.atom-video-chamada-curso
  @extend $atom-video

```

E precisamos adicionar o fundo na nossa `section` para que o vídeo fique inserido nele.

```
.molecule-video-chamada
  @media (min-width: $desktop-size)
    background: url('../img/video-mac-bg.png') no-repeat top center
    height: 500px
```

![Tela mostrando o vídeo inserido em um notebook quando estiver no desktop](https://cldup.com/6zcl__9v9y.png)

Legal porém nós estamos adicionando regras específicas para o vídeo, porém elas só serão usadas na seção da chamada do curso, logo eu vou passar essas regras para modificar esse átomo apenas nesse contexto. Bom vamos ao código que ficará mais fácil de entender:

```
.molecule-video-chamada
  @media (min-width: $desktop-size)
    background: url('../img/video-mac-bg.png') no-repeat top center
    height: 500px
    .atom-video-chamada-curso
      min-height: 340px
      width: 550px
      margin: 0 auto
      position: relative
      top: 40px
```

Nesse caso `atom-video-chamada-curso` apenas entrará no formato para caber nesse fundo `background: url('../img/video-mac-bg.png')` se estiver dentro da molécula `molecule-video-chamada` e com a *media query* `@media (min-width: $desktop-size)`.

**Nesse momento percebi que estava usando um azul `#4161af` setado no `background-color` do html, mudei para `$azul-claro` para utilizar do nosso padrão setado nas variáveis, por isso o fundo será diferente daqui para frente.**

Agora para finalizar a parte do vídeo sua sessão possui um fundo escuro e continua com um fundo branco. Adicionei as seguintes regras no `main.styl`, depois precisamos refatorar:

```
body
  background-color: $azul-claro
header
  height: 7.4rem
main
  background-color: $branco
  padding-bottom: 4em
```

E refatorei a `view`:

```
main
  article
    div.molecule-curso-chamada
      h1.atom-title-primary
        | Quer aprender Javascript de VERDADE?
      section.molecule-video-chamada
        iframe.atom-video-chamada-curso(src='//www.youtube.com/embed/gS7oD8r9YSY',
                                        frameborder='0',
                                        allowfullscreen='')


    h2.atom-title-secondary Aprenda, capacite-se e destaque-se no mercado.
    p
      | Lorem ipsum dolor sit amet, consectetur adipisicing elit. Illum, 
      | incidunt! Quis culpa ex, provident nesciunt minima recusandae 
      | veniam nihil accusantium, id deleniti doloremque ut sit iste 
      | officiis commodi asperiores laudantium.
```



###Título secundário

Vamos iniciar a criação do `atom-title-secondary`.

```
.atom-title-secondary
  @extend $atom-title
  color: $azul-claro
  text-align: center
```

Perceba que estamos re-usando o *placeholder* `atom-title` e só modificamos sua cor e seu alinhamento.

![Tela mostrando o título secundário no mobile](https://cldup.com/_D6tLyQdKY.png)
![Tela mostrando o título secundário no desktop](https://cldup.com/hZuMbfXQRA.png)

Antes de continuarmos vamos pegar uma font diferente no [Google Fonts](http://www.google.com/fonts)

Alterando o arquivo `atom-title` deixamos ele assim:

```
@import url(http://fonts.googleapis.com/css?family=Coda)

.atom-title-primary
  @extend $atom-title
  color: $branco
  font-family: 'Coda', cursive
  font-size: 2.8rem
  text-align: center

.atom-title-secondary
  @extend $atom-title
  color: $azul-claro
  font-family: 'Coda', cursive
  font-size: 2rem
  text-align: center
```

*ps: não esquecer de chamar no index.jade*

```
link(rel='stylesheet', type='text/css', href='http://fonts.googleapis.com/css?family=Coda')
```


Agora sim já está ficando com uma cara mais agradável.

![Mostrando nova fonte no mobile](https://cldup.com/JtP6y3sAYJ.png)
![Mostrando nova fonte no desktop](https://cldup.com/2U1MA2JUMb.png)

Para deixar com o fundo que tem no layout precisei modificar um pouco a estrutura da `view`:

```
section.molecule-curso-chamada
  h1.atom-title-primary
    | Quer aprender Javascript de VERDADE?
  div.molecule-video-chamada
    iframe.atom-video-chamada-curso(src='//www.youtube.com/embed/gS7oD8r9YSY',
                                    frameborder='0',
                                    allowfullscreen='')
  div.molecule-video-text
    h2.atom-title-secondary Aprenda, capacite-se e destaque-se no mercado.
    p.atom-text-video-chamada
    | Não perca essa grande oportunidade de se diferenciar no mercado
    | tendo em vista que o Javascript só cresce cada vez mais.
```

Separando em 2 blocos pois preciso o contexto/fundo do texto da chamada será diferente do vídeo. Logo precisei criar a molécula `molecule-video-text`:

```
.molecule-video-text
  background-color: $branco
  padding-top: 2rem
```

Desse jeito nossa tela no `mobile` ficará assim:

![tela mostrando a divisão entre o vídeo e a sessão abaixo no mobile](https://cldup.com/0wgHKBQYbr.png)


####Texto de chamada do curso

Vamos no `boson-typo` e criar o *placeholder* `atom-text` e adicionar umas variáveis:

```
$font-family = 'Myriad Pro, Sans serif'
$font-bold = 'normal'
$font-size-base = 16px
$font-align = 'left'

$atom-text
  font-weight: $font-bold
  font-family: $font-family
  text-align: $font-align
```

Agora usamos no átomo `atom-text-video-chamada` criando o arquivo `atom-text`:

```
@import url(http://fonts.googleapis.com/css?family=Coda)

.atom-text-video-chamada
  @extend $atom-text
  color: $azul-cinza-claro
  font-size: 1.4rem
  margin: 0 auto
  text-align: center
  width: 90%
  @media(min-width: $desktop-size)
    width: 80%
  @media(min-width: $desktop-medium-size)
    width: 50%
```

Já adicionei o comportamento responsivo como o Atomic Design nos diz, nesse caso o texto não ficará apenas em uma linha quando a tela for muito grande, depois podemos refatorar quando começarmos a etapa do `Template` que é onde setamos a Arquitetura da nossa página utilizando um *grid*.

![Texto mostrado no mobile](https://cldup.com/_XP28AXvA8.png)
![Texto mostrado no tablet](https://cldup.com/rj8EgulaWL.png)
![Texto mostrado no desktop](https://cldup.com/7iPyJMPN5j.png)
![Texto mostrado no desktop maior](https://cldup.com/Vfgk0A3l00.png)

Agora nosso componente do texto já está finalizado com sua responsividade, porém ainda precisamos fazer mais uma coisinha ...

#Hora da REFATORAÇÃO

![imagem da Hora da Aventura](http://img2.wikia.nocookie.net/__cb20130504060912/tudosobrehoradeaventura/pt-br/images/8/89/Hora_de_aventura_by_angieeditions09-d5elww0.png)

Percebeu que estamos chamando a mesma *font* em 2 arquivos diferentes? Bom como essa será a *font* padrão do projeto vamos adicionar essa *font*, em `boson-typo`:

```
@import url(http://fonts.googleapis.com/css?family=Coda)

$font-family = 'Coda', cursive
$font-bold = normal
$font-size-base = 16px
$font-align = left

// Setando a font-size base para o REM
html
  font-size: $font-size-base
  font-family: $font-family

```

Aproveitamos já para setar no `html` o tamanho e a família da *font*, o interessante de usar REM é que para aumentar ou diminuirmos nossos textos basta mudar o tamanho da *font* no `html` e retiramos a chamada de `atom-text` e `atom-title`.

Com isso precisamos dar uma corrigida nos tamanhos das *fonts* do **menu** e do **botão matricule-se**. 

Vamos mudar o tamanho das *fonts* do `atom-link-menu`:

```
.atom-link-menu
  @extend $atom-link
  color: #fff
  text-transform: uppercase
  font-size: 1rem
  // font maior para o tablet por causa do espaço
  @media (min-width: $tablet-size)
    font-size: 1.3rem
    // font menor para o desktop para deixar horizontal
  @media (min-width: $desktop-size)
    font-size: 0.9rem
    letter-spacing: 0.0625*2rem
```

E do *placeholder* `atom-button-matricule-se` pois como ele é extendido no átomo `atom-button-matricule-se-small` não precisamos mudar em mais nenhum lugar.

```
$atom-button-matricule-se
  @extend $atom-button
  background-color: $verde-claro
  color: $branco
  font-size: 0.8rem
  font-weight: 800
  text-transform: uppercase
  letter-spacing: 0.0625*1rem
  padding-top: 8px
```

Para corrigir o espaçamento entre as seções vamos retirar o margin de cima do `atom-title-secondary`:

```
.atom-title-secondary
  @extend $atom-title
  color: $azul-claro
  font-family: 'Coda', cursive
  font-size: 2rem
  margin-top: 0
  text-align: center
```

E mudar quando for `desktop` retirar o `padding-top` de `molecule-video-text`:

```
.molecule-video-text
  background-color: $branco
  padding-top: 2rem
  @media (min-width: $desktop-size)
    padding-top: 0
```

Ótimo agora o `header` já está ajeitadinho.

![tela mostrando o header no mobile](https://cldup.com/TEBjM8jCz0.png)
![tela mostrando o header no tablet](https://cldup.com/9g49_UKvak.png)
![tela mostrando o header no desktop](https://cldup.com/EdUUvN26UD.png)


![Voltemos ao assunto por favor](http://estragafilmes.blog.br/wp-content/uploads/2011/06/DE-VOLTA-PARA-O-FUTURO.jpg)

Vamos voltar para o layout e ver qual o próximo componente a ser estilizado.

![Parte do layout que mostra a listagem dos cursos](https://cldup.com/kaAk0gAfVr.jpg)

Então vamos criar a estrutura da nossa `view`:

```
article.aulas-wrapper
  h3.atom-title-section Confira nossas aulas
  p Essa é a lista das aulas que fazem parte desse curso
  section.molecule-box-curso
    div.molecule-box-curso-wrapper
      img.atom-curso-thumb( src='img/thumb-video-aula.jpg', 
                            alt='Thumbnail do vídeo da aula desse curso')
      h4.atom-title-curso
        | Nome do Curso
      p.atom-curso-resume
        | Lorem ipsum dolor sit amet, consectetur adipisicing elit. Magnam, 
        | commodi, officia...
    div.molecule-box-curso-wrapper
      img.atom-curso-thumb( src='img/thumb-video-aula.jpg', 
                            alt='Thumbnail do vídeo da aula desse curso')
      h4.atom-title-curso
        | Nome do Curso
      p.atom-curso-resume
        | Lorem ipsum dolor sit amet, consectetur adipisicing elit. Magnam, 
        | commodi, officia...
    div.molecule-box-curso-wrapper
      img.atom-curso-thumb( src='img/thumb-video-aula.jpg', 
                            alt='Thumbnail do vídeo da aula desse curso')
      h4.atom-title-curso
        | Nome do Curso
      p.atom-curso-resume
        | Lorem ipsum dolor sit amet, consectetur adipisicing elit. Magnam, 
        | commodi, officia...
    div.molecule-box-curso-wrapper
      button.atom-button-ver-mais ver mais
```

*Lembrando que ela pode ser refatorada quando necessário.*

Sem nenhuma estilização nossa view estará assim:

![tela mostrando a listagem das aulas sem estilo](https://cldup.com/frvHn1hLU2.png)

Primeiro vamos colocar a cor correta na seção, no `main.styl` criamos a seguinte regra:

```
.aulas-wrapper
  background-color: $azul-cinza-escuro
```

E vamos mudar a cor de cada caixa que contém a aula.

```
.molecule-box-curso-wrapper
  background-color: $branco
```

Porém como estamos fazendo Mobile-first vamos mudar a aquitetura dessa caixa colocando o vídeo na esquerda e o conteúdo de texto na direita, antes vamos criar o átomo da thumbnail.

```
.atom-curso-thumb
  @extend $atom-img
```

E depois vamos estilizar o componente no contexto dessa molécula sem precisar que modifiquemos nossos átomos.

```
.molecule-box-curso-wrapper
  background-color: $branco
  margin-bottom: 1rem
  padding-top: 0.2rem
  clear: both
  width: 100%
  max-width: 100%
  height: 12rem
  .atom-curso-thumb
    margin-top: 1rem
    display: inline-block
    width: 30%
    float: left
  .atom-title-curso,
  .atom-curso-resume
    float: left
    width: 60%
    margin-left: 1rem
```

![atenção agora!](https://cldup.com/7bqkq5IjH8.jpg)

Percebeu que os átomos agora só ganham esses estilos específicos pois estão dentros dessa molécula, que nesse contexto adiciona propriedades e/ou subistitui.

Dessa forma quando finalizarmos a criação de todos os átomos não seria mais necessário mexer neles, ainda precisamos refatorar nossos outros códigos, mas essa será a etapa após a finalização da estilização visual.

Trabalhando com o Atomic Design você só precisará modificar futuramente as regras específicas das suas moléculas e organismos, conseguindo re-usar os mesmos átomos sempre.

Além do mais que criando-os genéricamente com variáveis em seus valores é bem simples criar um tema e mudar automaticamente, mas isso ficará para um próximo artigo.



Repositório oficial: [https://github.com/suissa/javascript-curso-site](https://github.com/suissa/javascript-curso-site)



## Para rodar o projeto

### Instalando

Além obviamente do Node.js precisará do Gulp.

- [NodeJS](http://nodejs.org/)
- [GulpJS](http://gulpjs.com/)


```sh
npm install -g gulp

# Clone this repository
$ git clone git://github.com/willianjusten/Fast.git new_project
$ cd new_project

# install dependencies
$ npm install
```

With the commands above, you have everything to start.

### Pastas e Arquivos

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

### Tarefas

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
