# Airbnb React/JSX Style Guide

*Uma abordagem para padronização de códigos usada pela [AirBNB](http://airbnb.io/) React e JSX, traducão feita com base [nesse repositório](https://github.com/airbnb/javascript/tree/master/react)*

## Índice

  1. [Regras Básicas](#regras-básicas)
  1. [Class vs `React.createClass` vs stateless](#class-vs-reactcreateclass-vs-stateless)
  1. [Nomenclatura](#nomenclatura)
  1. [Declaration](#declaration)
  1. [Indentação](#indentacao)
  1. [Aspas](#aspas)
  1. [Espaçamento](#espacamento)
  1. [Props](#props)
  1. [Refs](#refs)
  1. [Parenteses](#parenteses)
  1. [Tags](#tags)
  1. [Métodos (Methods)](#métodos-methods)
  1. [Prioridades](#prioridades)
  1. [`isMounted`](#ismounted)

## Regras Básicas

  - Apenas **um** componente por arquivo.
    - Contudo, múltiplos Componentes [Statefull ou Stateless ](https://facebook.github.io/react/docs/reusable-components.html#stateless-functions)são permitidos por arquivo. eslint: [`react/no-multi-comp`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-multi-comp.md#ignorestateless).
    
    ```jsx
    
    function Hello(props) {
      return <div>Hello {props.name}</div>;
    }
    class HelloJohn extends React.Component {
      render() {
        return <Hello name="John" />;
      }
    }
    module.exports = HelloJohn;
    ```
  - Sempre use a sintaxe JSX.
  - Não use `React.createElement` a não ser que você estaja declarando a partir de um arquivo que não seja JSX.

## `Class` vs `React.createClass` vs `stateless`

  - Se você tem State interno e/ou refs, opte por `class extends React.Component` ao invés de `React.createClass` a menos que você tenha uma boa razão para usar um mixins. eslint: [`react/prefer-es6-class`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/prefer-es6-class.md) [`react/prefer-stateless-function`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/prefer-stateless-function.md)

    ```jsx
    // ruim
    const Listing = React.createClass({
      // ...
      render() {
        return <div>{this.state.hello}</div>;
      }
    });

    // ideal
    class Listing extends React.Component {
      // ...
      render() {
        return <div>{this.state.hello}</div>;
      }
    }
    ```

    E se você não tem `state` ou `refs` , prefira funções normais ( não arrow functions ) sobre as classes:

    ```jsx
    // ruim
    class Listing extends React.Component {
      render() {
        return <div>{this.props.hello}</div>;
      }
    }

    // ruim (relying on function name inference is discouraged)
    const Listing = ({ hello }) => (
      <div>{hello}</div>
    );

    // ideal
    function Listing({ hello }) {
      return <div>{hello}</div>;
    }
    ```

## Nomenclatura

  - **Extensão**: Use a extensão `.jsx` para componentes React.
  - **Nome do arquivo**: Use PascalCase para os arquivos. Ex.: `MeuComponente.jsx`.
  - **Referência de nomenclatura**: Use PascalCase para componentes e camelCase para as instâncias. eslint: [`react/jsx-pascal-case`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-pascal-case.md)

    ```jsx
    // ruim
    import reservationCard from './ReservationCard';

    // ideal
    import ReservationCard from './ReservationCard';

    // ruim
    const ReservationItem = <ReservationCard />;

    // ideal
    const reservationItem = <ReservationCard />; //instância em camel e componente em Pascal
    ```

  - **Nomeando componentes**: Use o mesmo nome do componente. Ex.: `MeuComponente.jsx` tem um nome que referência `MeuComponente`. No entanto, para os componentes de raiz de um diretório, use ` index.jsx` como o nome do arquivo e usar o nome de diretório como o nome do componente:
    
  * App     
      ├─┬ Footer     
      │ ├── index.jsx (O componente Footer está declarado na index)     
      │ ├── MeuComponente.jsx      
      │      
      ├─┬ Header     
      │ ├── index.jsx (O componente Header está declarado na index)     
      │ │     
      │ ├─┬ Meta     
      │ │ ├── index.jsx (O componente Meta está declarado na index)            
      │ │     
      │ ├─┬ Menu.jsx      
      │ │ ├── index.jsx      
      │ │ ├── Item.jsx      

    ```jsx
    // ruim
    import Footer from './Footer/Footer';

    // ruim
    import Footer from './Footer/index';

    // ideal
    import Footer from './Footer';
    ```

## Declaração

  - Não use ` displayName` para nomear componentes. Em vez disso, o nome do componente por referência.

    ```jsx
    // ruim
    export default React.createClass({
      displayName: 'MeuComponente',
      // alguma coisa aqui.
    });

    // ideal
    export default class MeuComponente extends React.Component {
      // todo 
    }
    ```

## Indentação

  - Siga estes estilos de indentação para sintaxe JSX. eslint: [`react/jsx-closing-bracket-location`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-closing-bracket-location.md)

    ```jsx
    // ruim
    <Componente primeiroParametro="bar"
                maisUmParametro="baz" />

    // ideal
    <Componente
      primeiroParametro="bar"
      maisUmParametro="baz"
    />

    // Se for apenas um parâmetro coloque na mesma linha.
    <Componente bar="bar" />

    // children recebe indentação normal.
    <Componente
      primeiroParametro="bar"
      maisUmParametro="baz"
    >
      <ComponenteFilho />
    </Componente>
    ```

## Aspas

  - Sempre usa aspas duplas (`"`) para atributos, mas aspas simples para todos os outros JS usados no componente. eslint: [`jsx-quotes`](http://eslint.org/docs/rules/jsx-quotes)

  > Sério? Atributos JSX [can't contain escaped quotes](http://eslint.org/docs/rules/jsx-quotes), assim aspas fazem conjunções como `"Don't"` tornando-se mais fáceis de digitar.
  > atributos regulares HTML também costumam usar aspas duplas em vez de únicas, de modo atributos JSX espelhão esta convenção.

    ```jsx
    // ruim
    <Componente bar='bar' />

    // ideal
    <Componente bar="bar" />

    // ruim
    <Componente style={{ left: "20px" }} />

    // ideal
    <Componente style={{ left: '20px' }} />
    ```

## Espaçamento

  - Sempre inclua um único espaço no fechamento de suas tags que não recebem `childrens`.

    ```jsx
    // ruim
    <Foo/>

    // tá de sacanagem néh?
    <Foo                 />

    // ruim
    <Foo
     />

    // ideal
    <Foo />
    ```

  - Não precisa usar espaço dentro nas chaves de parâmetros de um componente. eslint: [`react/jsx-curly-spacing`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-curly-spacing.md)

    ```jsx
    // ruim
    <Foo bar={ baz } />

    // ideal
    <Foo bar={baz} />
    ```

## Props

  - Sempre use `camelCase` para prop names.

    ```jsx
    // ruim
    <Componente
      UserName="hello"
      phone_number={12345678}
    />

    // ideal
    <Componente
      userName="hello"
      phoneNumber={12345678}
    />
    ```

  - Quando o valor Booleano for `true` pode ser omitido. eslint: [`react/jsx-boolean-value`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-boolean-value.md)

    ```jsx
    // ruim
    <Component
      hidden={true}
    />

    // ideal
    <Component
      hidden
    />
    ```

  - Sempre inclua o parâmetro `alt` em suas `<img>` tags. E `alt` pode ser uma string vazia em `<img>` . eslint: [`jsx-a11y/img-has-alt`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/img-has-alt.md)

    ```jsx
    // ruim
    <img src="hello.jpg" />

    // ideal
    <img src="hello.jpg" alt="Me waving hello" />

    // ideal
    <img src="hello.jpg" alt="" />
    ```

  - Não use palavras como "image", "photo", ou "picture" no `alt` de sua `<img>` . eslint: [`jsx-a11y/img-redundant-alt`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/img-redundant-alt.md)

  > Sério? Screenreaders já interpretam `img` como um elemento de imagem, por isso não há necessidade de incluir esta informação no texto do alt.

    ```jsx
    // ruim
    <img src="hello.jpg" alt="Picture of me waving hello" />

    // ideal
    <img src="hello.jpg" alt="Me waving hello" />
    ```

  - Use apenas válido, não- abstrato [ARIA roles](https://www.w3.org/TR/wai-aria/roles#role_definitions). eslint: [`jsx-a11y/aria-role`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/aria-role.md)

    ```jsx
    // ruim - não é uma ARIA role
    <div role="datepicker" />

    // ruim - abstrata ARIA role
    <div role="range" />

    // ideal
    <div role="button" />
    ```

  - Não use `accessKey` em seus elementos. eslint: [`jsx-a11y/no-access-key`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/no-access-key.md)

  > Sério? Inconsistências entre os atalhos de teclado e comandos de teclado usados ​​por pessoas que usam leitores de tela e teclados podem complicar a acessibilidade.

  ```jsx
  // ruim
  <div accessKey="h" />

  // ideal
  <div />
  ```

  - Evite usar `index` como `key` de props, opte por um ID. ([Sério?](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318))

  ```jsx
  // ruim
  {todos.map((todo, index) =>
    <Todo
      {...todo}
      key={index}
    />
  )}

  // ideal
  {todos.map(todo => (
    <Todo
      {...todo}
      key={todo.id}
    />
  ))}
  ```

## Refs

  - Sempre use `ref callbacks`. eslint: [`react/no-string-refs`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-string-refs.md)

    ```jsx
    // ruim
    <Foo
      ref="myRef"
    />

    // ideal
    <Foo
      ref={(ref) => this.myRef = ref}
    />
    ```

## Parenteses

  - Coloque Tags JSX entre parênteses quando eles abrangem mais de uma linha. eslint: [`react/wrap-multilines`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/wrap-multilines.md)

    ```jsx
    // ruim
    render() {
      return <Componente className="long body" foo="bar">
               <ComponenteFilho />
             </Componente>;
    }

    // ideal
    render() {
      return (
        <Componente className="long body" foo="bar">
          <ComponenteFilho />
        </Componente>
      );
    }

    // ideal, para uma linha.
    render() {
      const body = <div>hello</div>;
      return <Componente>{body}</Componente>;
    }
    ```

## Tags

  - Sempre que a Tag não possuir `children` use *self-close* ( <Ex /> ). eslint: [`react/self-closing-comp`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/self-closing-comp.md)

    ```jsx
    // ruim
    <Foo className="stuff"></Foo>

    // ideal
    <Foo className="stuff" />
    ```

  - Se o seu componente tiver mais de uma linha de propriedades(props), feche a Tag em uma nova linha. eslint: [`react/jsx-closing-bracket-location`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-closing-bracket-location.md)

    ```jsx
    // ruim
    <Foo
      bar="bar"
      baz="baz" />

    // ideal
    <Foo
      bar="bar"
      baz="baz"
    />
    ```

## Métodos (Methods)

  - Use arrow functions para variáveis locais.

    ```jsx
    function ItemList(props) {
      return (
        <ul>
          {props.items.map((item, index) => (
            <Item
              key={item.key}
              onClick={() => fazerAlgoCom(item.name, index)}
            />
          ))}
        </ul>
      );
    }
    ```

  - Manipuladores de evento do método render dentro do `constructor()` eslint: [`react/jsx-no-bind`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-no-bind.md)


    ```jsx
    // ruim
    class extends React.Component {
      onClickDiv() {
        // do stuff
      }

      render() {
        return <div onClick={this.onClickDiv.bind(this)} />
      }
    }

    // ideal
    class extends React.Component {
      constructor(props) {
        super(props);

        this.onClickDiv = this.onClickDiv.bind(this);
      }

      onClickDiv() {
        // do stuff
      }

      render() {
        return <div onClick={this.onClickDiv} />
      }
    }
    ```

  - Não use underscore prefix para métodos internos de um componente React.

    ```jsx
    // ruim
    React.createClass({
      _onClickSubmit() {
        // do stuff
      },

      // other stuff
    });

    // ideal
    class extends React.Component {
      onClickSubmit() {
        // do stuff
      }

      // other stuff
    }
    ```

  - Certifique-se de retornar um valor em seu método  `render`. eslint: [`require-render-return`](https://github.com/yannickcr/eslint-plugin-react/pull/502)

    ```jsx
    // ruim
    render() {
      (<div />);
    }

    // ideal
    render() {
      return (<div />);
    }
    ```

## Prioridades

  - Prioridades para  `class extends React.Component`:

  1. opcional `static`
  1. `constructor`
  1. `getChildContext`
  1. `componentWillMount`
  1. `componentDidMount`
  1. `componentWillReceiveProps`
  1. `shouldComponentUpdate`
  1. `componentWillUpdate`
  1. `componentDidUpdate`
  1. `componentWillUnmount`
  1. *clickHandlers ou eventHandlers* tipo `onClickSubmit()` ou `onChangeDescription()`
  1. *getter methods para o `render`* tipo `getSelectReason()` ou `getFooterContent()`
  1. *Métodos opcionais de render* tipo `renderNavigation()` ou `renderProfilePicture()`
  1. `render`

  - Como definir `propTypes`, `defaultProps`, `contextTypes`, etc...

    ```jsx
    import React, { PropTypes } from 'react';

    const propTypes = {
      id: PropTypes.number.isRequired,
      url: PropTypes.string.isRequired,
      text: PropTypes.string,
    };

    const defaultProps = {
      text: 'Hello World',
    };

    class Link extends React.Component {
      static methodsAreOk() {
        return true;
      }

      render() {
        return <a href={this.props.url} data-id={this.props.id}>{this.props.text}</a>
      }
    }

    Link.propTypes = propTypes;
    Link.defaultProps = defaultProps;

    export default Link;
    ```

  - Prioridades para `React.createClass`: eslint: [`react/sort-comp`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/sort-comp.md)

  1. `displayName`
  1. `propTypes`
  1. `contextTypes`
  1. `childContextTypes`
  1. `mixins`
  1. `statics`
  1. `defaultProps`
  1. `getDefaultProps`
  1. `getInitialState`
  1. `getChildContext`
  1. `componentWillMount`
  1. `componentDidMount`
  1. `componentWillReceiveProps`
  1. `shouldComponentUpdate`
  1. `componentWillUpdate`
  1. `componentDidUpdate`
  1. `componentWillUnmount`
  1. *clickHandlers ou eventHandlers* tipo `onClickSubmit()` ou `onChangeDescription()`
  1. *getter methods for `render`* tipo `getSelectReason()` ou `getFooterContent()`
  1. *Optional render methods* tipo `renderNavigation()` ou `renderProfilePicture()`
  1. `render`

## `isMounted`

  - Não use `isMounted`. eslint: [`react/no-is-mounted`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-is-mounted.md)

  > Sério? [`isMounted` é um anti-pattern][anti-pattern], não estára mais disponível nas classes ES6 (ECMA2015), e está a caminho de ser oficialmente `deprecated` (descontinuado).

  [anti-pattern]: https://facebook.github.io/react/blog/2015/12/16/ismounted-antipattern.html

## Translation

  This JSX/React style guide is also available in other languages:

  - ![cn](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/China.png) **Chinese (Simplified)**: [JasonBoy/javascript](https://github.com/JasonBoy/javascript/tree/master/react)
  - ![pl](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Poland.png) **Polish**: [pietraszekl/javascript](https://github.com/pietraszekl/javascript/tree/master/react)
  - ![kr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/South-Korea.png) **Korean**: [apple77y/javascript](https://github.com/apple77y/javascript/tree/master/react)
  - ![Br](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Brazil.png) **Brazilian**: [ronal2do/javascript](https://github.com/ronal2do/javascript/tree/master/react)

**[⬆ back to top](#table-of-contents)**
