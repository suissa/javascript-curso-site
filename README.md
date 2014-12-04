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
