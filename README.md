# Guia de estilo do Airbnb React/JSX

Uma abordagem bastante razoável para React e JSX

Este guia de estilo é baseado, principalmente, nos padrões que prevalecem atualmente no JavaScript, embora algumas convenções (ou seja, campos estáticos de classe ou async/await) ainda possam ser incluídas o proibidas, caso a caso.

_Traducão feita com base [nesse repositório](https://github.com/airbnb/javascript/tree/master/react)_

## Índice

1. [Regras Básicas](#regras-básicas)
2. [Class vs `React.createClass` vs stateless](#class-vs-reactcreateclass-vs-stateless)
3. ['Atalhos' ou Mixins](#mixins)
4. [Nomenclatura](#nomenclatura)
5. [Declaração](#declaração)
6. [Indentação](#indentação)
7. [Aspas](#aspas)
8. [Espaçamento](#espacamento)
9. ['Propriedades' ou Props](#props)
10. ['Referências' ou Refs](#refs)
11. [Parenteses](#parenteses)
12. [Tags](#tags)
13. [Métodos (Methods)](#métodos-methods)
14. [Prioridades](#prioridades)
15. [`isMounted`](#ismounted)

## Regras Básicas

- Inclua apenas **um** componente por arquivo.

  - Contudo, múltiplos [componentes sem estado ou puros](https://facebook.github.io/react/docs/reusable-components.html#stateless-functions) são permitidos por arquivo. eslint: [`react/no-multi-comp`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-multi-comp.md#ignorestateless).

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
- Não use `React.createElement`. A não ser que você estaja declarando a partir de um arquivo que não seja JSX.
- [`react/forbid-prop-types`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/forbid-prop-types.md) permitirá `arrays` e `objects` somente se for explicitamente indicado o que eles contém, usando `arrayOf`, `objectOf`, ou `shape`.

## Class vs `React.createClass` vs stateless

- Se você tem estado interno e/ou referência `(state, ref)`, prefira `class extends React.Component` ao invés de `React.createClass`. eslint: [`react/prefer-es6-class`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/prefer-es6-class.md) [`react/prefer-stateless-function`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/prefer-stateless-function.md)

  ```jsx
  // ruim
  const Listing = React.createClass({
    // ...
    render() {
      return <div>{this.state.hello}</div>;
    },
  });

  // bom
  class Listing extends React.Component {
    // ...
    render() {
      return <div>{this.state.hello}</div>;
    }
  }
  ```

  E, se você não tem `state` ou `refs`, prefira funções normais (não funções de seta (arrow functions)) sobre as classes:

  ```jsx
  // ruim
  class Listing extends React.Component {
    render() {
      return <div>{this.props.hello}</div>;
    }
  }

  // ruim (confiar na inferência do nome da função é desencorajado)
  const Listing = ({ hello }) => <div>{hello}</div>;

  // bom
  function Listing({ hello }) {
    return <div>{hello}</div>;
  }
  ```

## Mixins (Atalhos)

- [Não use mixins](https://facebook.github.io/react/blog/2016/07/13/mixins-considered-harmful.html).
  > Por quê? Mixins introduzem dependências implícitas, causam conflitos de nomes e uma complexidade crescente. A maioria dos casos de uso para mixins, pode ser realizada, de uma forma melhor, por meio de componentes, componentes de ordem superior ou módulos utilitários.

## Nomenclatura

- **Extensões**: Use a extensão `.jsx` para componentes. eslint: [`react/jsx-filename-extension`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-filename-extension.md)
- **Nome do arquivo**: Use PascalCase para os arquivos. Ex.: `MeuComponente.jsx`.
- **Referência de nomenclatura**: Use PascalCase para componentes e camelCase para suas instâncias. eslint: [`react/jsx-pascal-case`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-pascal-case.md)

  ```jsx
  // ruim
  import meuComponente from './MeuComponente';

  // bom
  import MeuComponente from './MeuComponente';

  // ruim
  const MeuComponenteItem = <MeuComponente />;

  // bom
  const meuComponenteItem = <MeuComponente />;
  ```

- **Nomeando componentes**: Use o nome do arquivo como o nome do componente. Por exemplo: `MeuComponente.jsx` deve ter um nome de referência `MeuComponente`. No entanto, para componentes raiz de um diretório, use ` index.jsx` como o nome do arquivo e use o nome do diretório como o nome do componente:
- App

  - Footer  
    |── index.jsx (O componente `Footer` está declarado aqui dentro.)  
    |── MeuComponente.jsx
  - Header  
    |── index.jsx (O componente `Header` está declarado aqui dentro.)  
    |── OutroComponente.jsx
  - Meta  
    |── index.jsx (O componente `Meta` está declarado aqui dentro.)

  ```jsx
  // ruim
  import Footer from './Footer/Footer';

  // ruim
  import Footer from './Footer/index';

  // bom
  import Footer from './Footer';
  ```

- **Nomenclatura de componentes de ordem superior**: Use uma composição de nome de componente de ordem superior e do nome do componente passado como `displayName` no componente gerado. Por exemplo, o componente de ordem superior `withFoo()`, quando passado um componente `Bar` deve produzir um componente com um `displayName` de `withFoo(Bar)`.

> Por quê? O `displayName` de um componente pode ser usado por ferramentas de desenvolvimento ou em mensagens de erro, e ter um valor que expresse claramente essa relação ajuda as pessoas a entender o que está acontecendo.

```jsx
  // ruim
  export default function withFoo(WrappedComponent) {
    return function WithFoo(props) {
      return <WrappedComponent {...props} foo />;
    }
  }

  // bom
  export default function withFoo(WrappedComponent) {
    function WithFoo(props) {
      return <WrappedComponent {...props} foo />;
    }

    const wrappedComponentName = WrappedComponent.displayName
      || WrappedComponent.name
      || 'Component';

    WithFoo.displayName = `withFoo(${wrappedComponentName})`;
    return WithFoo;
  }
```

- **Nomenclatura de propriedades (`Props`)**: Evite usar nomes de props de componentes DOM para objetivos diferentes.

> Por quê? As pessoas esperam que _props_ como `style` e `className` signifiquem uma coisa específica. Variar essa API para um subconjunto de seu aplicativo torna o código menos legível e complicado de manter, e pode causar bugs.

```jsx
    // ruim
    <MyComponent style="fancy" />

    // ruim
    <MyComponent className="fancy" />

    // bom
    <MyComponent variant="fancy" />
```

## Declaração

- Não use ` displayName` para nomear componentes. Em vez disso, nomeie o componente por referência.

  ```jsx
  // ruim
  export default React.createClass({
    displayName: 'MeuComponente',
    // alguma coisa aqui.
  });

  // bom
  export default class MeuComponente extends React.Component {
  }
  ```

## Indentação

- Siga estes estilos de indentação para sintaxe JSX. eslint: [`react/jsx-closing-bracket-location`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-closing-bracket-location.md) [`react/jsx-closing-tag-location`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-closing-tag-location.md)

  ```jsx
  // ruim
  <Componente primeiroParametro="bar"
              segundoParametro="baz" />

  // bom
  <Componente
    primeiroParametro="bar"
    segundoParametro="baz"
  />

  // Se os (props) couberem em uma linha, mantenha-os nela.
  <Componente bar="bar" />

  // Os filhos (children) ficam recuados normalmente.
  <Componente
    primeiroParametro="bar"
    segundoParametro="baz"
  >
    <ComponenteFilho />
  </Componente>

  // ruim
  {exibirBotao &&
    <Botao />
  }

  // ruim
  {
    exibirBotao &&
      <Botao />
  }

  // bom
  {exibirBotao && (
    <Botao />
  )}

  // bom
  {exibirBotao && <Botao />}

  // bom
  {algumaCondicionalGrande
    && outraCondicionalLonga
    && (
      <Componente
        parametroGrande="bar"
        outroParametroGrande="baz"
      />
    )
  }

  // bom
  {algumaCondicional ? (
    <Componente />
  ) : (
    <Componente
      parametroGrande="bar"
      outroParametroGrande="baz"
    />
  )}
  ```

## Aspas

- Sempre use aspas duplas (`"`) para atributos JSX, mas aspas simples (`'`) para todas as outras coisas no componente. eslint: [`jsx-quotes`](http://eslint.org/docs/rules/jsx-quotes)

> Por quê? Atributos HTML regulares também costumam usar aspas duplas em vez de simples, portanto, os atributos JSX espelham essa convenção.

    ```jsx
    // ruim
    <Componente bar='bar' />

    // bom
    <Componente bar="bar" />

    // ruim
    <Componente style={{ left: "20px" }} />

    // bom
    <Componente style={{ left: '20px' }} />
    ```

## Espaçamento

- Sempre inclua um único espaço em sua tag de autofechamento. eslint: [`no-multi-spaces`](https://eslint.org/docs/rules/no-multi-spaces), [`react/jsx-tag-spacing`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-tag-spacing.md)

  ```jsx
  // ruim
  <Componente/>

  // péssimo
  <Componente                 />

  // ruim
  <Componente
   />

  // bom
  <Componente />
  ```

- Não preencha chaves JSX com espaços. eslint: [`react/jsx-curly-spacing`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-curly-spacing.md)

  ```jsx
  // ruim
  <Componente bar={ baz } />

  // bom
  <Componente bar={baz} />
  ```

## Props

- Sempre use camelCase para nomes de props, ou PascalCase se o valor da prop for um componente React.

  ```jsx
  // ruim
  <Componente
    NomeUsuario="francisco"
    numero_telefone={12345678}
  />

  // bom
  <Componente
    nomeUsuario="francisco"
    numeroTelefone={12345678}
    Componente={AlgumComponente}
  />
  ```

- Quando o valor Booleano for `true` pode ser omitido. eslint: [`react/jsx-boolean-value`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-boolean-value.md)

  ```jsx
  // ruim
  <Component
    hidden={true}
  />

  // bom
  <Component
    hidden
  />

  // bom
  <Component hidden />
  ```

- Sempre inclua uma propriedade (prop) `alt` nas tags `<img>`. Se a imagem for de apresentação, `alt` pode ser uma string vazia ou o `<img>` deve ter `role="presentation"`. eslint: [`jsx-a11y/alt-text`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/alt-text.md)

  ```jsx
  // ruim
  <img src="hello.jpg" />

  // bom
  <img src="hello.jpg" alt="Me waving hello" />

  // bom
  <img src="hello.jpg" alt="" />

  // bom
  <img src="hello.jpg" role="presentation" />
  ```

- Não use palavras como "image", "photo", ou "picture" no `alt` de sua `<img>`. eslint: [`jsx-a11y/img-redundant-alt`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/img-redundant-alt.md)

> Por quê? Os leitores de tela já interpretam elementos `img` como imagens, por isso não há necessidade de incluir esta informação no texto de `alt`.

    ```jsx
    // ruim
    <img src="hello.jpg" alt="Picture of me waving hello" />

    // bom
    <img src="hello.jpg" alt="Me waving hello" />
    ```

- Use apenas válido, não abstrato [ARIA roles](https://www.w3.org/TR/wai-aria/roles#role_definitions). eslint: [`jsx-a11y/aria-role`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/aria-role.md)

  ```jsx
  // ruim - não é uma ARIA role
  <div role="datepicker" />

  // ruim - abstrata ARIA role
  <div role="range" />

  // bom
  <div role="button" />
  ```

- Não use `accessKey` em elementos. eslint: [`jsx-a11y/no-access-key`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/no-access-key.md)

> Por quê? Inconsistências entre os atalhos de teclado e comandos de teclado, usados ​​por pessoas que usam leitores de tela e teclados, podem complicar a acessibilidade.

```jsx
// ruim
<div accessKey="h" />

// bom
<div />
```

- Evite usar `index` como `key` de props, opte por um ID estável. eslint: [`react/no-array-index-key`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-array-index-key.md)

> Por quê? Não usar um ID estável [é um antipadrão](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318), pois pode afetar negativamente o desempenho e causar problemas com o estado do componente.

Não recomendamos o uso de índices para chaves se a ordem dos itens puder mudar.

```jsx
// ruim
{
  todos.map((todo, index) => <Todo {...todo} key={index} />);
}

// bom
{
  todos.map((todo) => <Todo {...todo} key={todo.id} />);
}
```

- Sempre defina, explicitamente, defaultProps para todas as props não obrigatórias.

> Por quê? propTypes são uma forma de documentação, e fornecer defaultProps significa que o leitor do seu código não precisa interpretar tanto. Além disso, pode significar que seu código pode omitir determinadas verificações de tipo.

```jsx
// ruim
function SFC({ foo, bar, children }) {
  return (
    <div>
      {foo}
      {bar}
      {children}
    </div>
  );
}
SFC.propTypes = {
  foo: PropTypes.number.isRequired,
  bar: PropTypes.string,
  children: PropTypes.node,
};

// bom
function SFC({ foo, bar, children }) {
  return (
    <div>
      {foo}
      {bar}
      {children}
    </div>
  );
}
SFC.propTypes = {
  foo: PropTypes.number.isRequired,
  bar: PropTypes.string,
  children: PropTypes.node,
};
SFC.defaultProps = {
  bar: '',
  children: null,
};
```

- Use `spread props` com moderação.
  > Por quê? Caso contrário, é mais provável que você passe `props` desnecessários para os componentes. E, para o React v15.6.1 e anteriores, poderá [passar atributos HTML inválidos para o DOM](https://reactjs.org/blog/2017/09/08/dom-attributes-in-react-16.html).

Exceções:

- HOCs que fazem proxy de `props` e sobem os propTypes

```jsx
function HOC(WrappedComponent) {
 return class Proxy extends React.Component {
   Proxy.propTypes = {
     text: PropTypes.string,
     isLoading: PropTypes.bool
   };

   render() {
     return <WrappedComponent {...this.props} />
   }
 }
}
```

- Espalhar objetos com `props` conhecidos e explícitos. Isso pode ser particularmente útil ao testar componentes React com `beforeEach()` do Mocha (testes unitários).

```jsx
export default function Componente {
  const props = {
    text: '',
    isPublished: false
  }

  return (<div {...props} />);
}
```

Observações: Filtre `props` desnecessários quando possível. Além disso, use [prop-types-exact](https://www.npmjs.com/package/prop-types-exact) para ajudar a evitar erros.

```jsx
// ruim
render() {
  const { propComum, ...propsImportantes } = this.props;
  return <ComponenteEnvolvido {...this.props} />
}

// bom
render() {
  const { propComum, ...propsImportantes } = this.props;
  return <ComponenteEnvolvido {...propsImportantes} />
}
```

## Referências `Refs`

- Sempre use retorno de chamada da referência ou `ref callbacks`. eslint: [`react/no-string-refs`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-string-refs.md)

  ```jsx
  // ruim
  <Componente
    ref="myRef"
  />

  // bom
  <Componente
    ref={(ref) => this.myRef = ref}
  />
  ```

## Parênteses

- Coloque Tags JSX entre parênteses quando eles abrangem mais de uma linha. eslint: [`react/jsx-wrap-multilines`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-wrap-multilines.md)

  ```jsx
  // ruim
  render() {
    return <Componente className="long body" foo="bar">
             <ComponenteFilho />
           </Componente>;
  }

  // bom
  render() {
    return (
      <Componente className="long body" foo="bar">
        <ComponenteFilho />
      </Componente>
    );
  }

  // bom, para uma linha.
  render() {
    const body = <div>hello</div>;
    return <Componente>{body}</Componente>;
  }
  ```

## Tags

- Sempre feche automaticamente as tags que não têm filhos. eslint: [`react/self-closing-comp`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/self-closing-comp.md)

  ```jsx
  // ruim
  <Componente className="stuff"></Componente>

  // bom
  <Componente className="stuff" />
  ```

- Se o seu componente tiver propriedades (`props`) de várias linhas, feche sua tag em uma nova linha. eslint: [`react/jsx-closing-bracket-location`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-closing-bracket-location.md)

  ```jsx
  // ruim
  <Componente
    bar="bar"
    baz="baz" />

  // bom
  <Componente
    bar="bar"
    baz="baz"
  />
  ```

## Métodos (Methods)

- Use as funções de seta para fechar as variáveis locais. É útil quando você precisa passar dados adicionais para um manipulador de eventos. No entanto, certifique-se de que eles [não prejudiquem massivamente o desempenho](https://www.bignerdranch.com/blog/choosing-the-best-approach-for-react-event-handlers/), em particular quando passados para componentes personalizados que podem ser PureComponents, porque eles acionarão uma rerenderização possivelmente desnecessária todas as vezes.

  ```jsx
  function ItemDaLista(props) {
    return (
      <ul>
        {props.items.map((item, index) => (
          <Item key={item.key} onClick={() => fazerAlgoCom(item.name, index)} />
        ))}
      </ul>
    );
  }
  ```

- Vincule manipuladores de eventos para o método de renderização no construtor. eslint: [`react/jsx-no-bind`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-no-bind.md)

> Por quê? Uma chamada de ligação (`bind`) no caminho de renderização cria uma nova função em cada renderização. Não use funções de seta em campos de classe, porque isso [dificulta o teste e a depuração e pode afetar negativamente o desempenho](https://medium.com/@charpeni/arrow-functions-in-class-properties-might-not-be-as-great-as-we-think-3b3551c440b1) e porque, conceitualmente, os campos de classe são para dados, não para lógica.

  ```
  // ruim
  class extends React.Component {
    onClickDiv() {
      // alguma coisa
    }

    render() {
      return <div onClick={this.onClickDiv.bind(this)} />;
    }
  }

  // péssimo
  class extends React.Component {
    onClickDiv = () => {
      // alguma coisa
    }

    render() {
      return <div onClick={this.onClickDiv} />
    }
  }

  // bom
  class extends React.Component {
    constructor(props) {
      super(props);

      this.onClickDiv = this.onClickDiv.bind(this);
    }

    onClickDiv() {
      // alguma coisa
    }

    render() {
      return <div onClick={this.onClickDiv} />;
    }
  }
  ```

- Não use o prefixo de sublinhado (`_`) para métodos internos de um componente React.

> Por quê? Os prefixos de sublinhado às vezes são usados como uma convenção em outros idiomas para denotar privacidade. Mas, diferentemente dessas linguagens, não há suporte nativo para privacidade em JavaScript, tudo é público. Independentemente de suas intenções, adicionar prefixos de sublinhado às suas propriedades não as torna realmente privadas, e qualquer propriedade (com prefixo de sublinhado ou não) deve ser tratada como pública. Veja as edições [#1024](https://github.com/airbnb/javascript/issues/1024) e [#490](https://github.com/airbnb/javascript/issues/490) para uma discussão mais aprofundada.

  ```jsx
  // ruim
  React.createClass({
    _onClickSubmit() {
      // alguma coisa
    },

    // outra coisa
  });

  // bom
  class extends React.Component {
    onClickSubmit() {
      // alguma coisa
    }

    // outra coisa
  }
  ```

- Certifique-se de retornar um valor em seu método `render`. eslint: [`require-render-return`](https://github.com/yannickcr/eslint-plugin-react/pull/502)

  ```jsx
  // ruim
  render() {
    (<div />);
  }

  // bom
  render() {
    return (<div />);
  }
  ```

## Prioridades

- Prioridades para `class extends React.Component`:

1. métodos `static` opcionais
2. `constructor`
3. `getChildContext`
4. `componentWillMount`
5. `componentDidMount`
6. `componentWillReceiveProps`
7. `shouldComponentUpdate`
8. `componentWillUpdate`
9. `componentDidUpdate`
10. `componentWillUnmount`
11. *manipuladores de eventos começando com 'handle'*, como `handleSubmit()` ou `handleChangeDescription()`
12. *manipuladores de eventos começando com 'on'* como `onClickSubmit()` ou `onChangeDescription()`
13. *métodos getter para `render`* como `getSelectReason()` ou `getFooterContent()`
14. *métodos de renderização opcionais* como `renderNavigation()` ou `renderProfilePicture()`
15. `render`

- Como definir `propTypes`, `defaultProps`, `contextTypes`, etc...

  ```jsx
  import React from 'react';
  import PropTypes from 'prop-types';

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
      return <a href={this.props.url} data-id={this.props.id}>{this.props.text}</a>;
    }
  }

  Link.propTypes = propTypes;
  Link.defaultProps = defaultProps;

  export default Link;
  ```

- Prioridades para `React.createClass`: eslint: [`react/sort-comp`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/sort-comp.md)

1. `displayName`
2. `propTypes`
3. `contextTypes`
4. `childContextTypes`
5. `mixins`
6. `statics`
7. `defaultProps`
8. `getDefaultProps`
9. `getInitialState`
10. `getChildContext`
11. `componentWillMount`
12. `componentDidMount`
13. `componentWillReceiveProps`
14. `shouldComponentUpdate`
15. `componentWillUpdate`
16. `componentDidUpdate`
17. `componentWillUnmount`
18. *clickHandlers ou eventHandlers* como `onClickSubmit()` ou `onChangeDescription()`
19. *métodos getter para `render`* como `getSelectReason()` ou `getFooterContent()`
20. *métodos de renderização opcionais* como `renderNavigation()` ou `renderProfilePicture()`
21. `render`

## `isMounted`

- Não use `isMounted`. eslint: [`react/no-is-mounted`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-is-mounted.md)

> Por quê? [`isMounted` é um antipadrão](https://facebook.github.io/react/blog/2015/12/16/ismounted-antipattern.html), não está disponível ao usar classes ES6 e está a caminho de ser oficialmente obsoleto.


## Tradução

Este guia de estilo JSX/React também está disponível em outros idiomas:

  - ![cn](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/China.png) **Chinese (Simplified)**: [jhcccc/javascript](https://github.com/jhcccc/javascript/tree/master/react)
  - ![tw](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Taiwan.png) **Chinese (Traditional)**: [jigsawye/javascript](https://github.com/jigsawye/javascript/tree/master/react)
  - ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Spain.png) **Español**: [agrcrobles/javascript](https://github.com/agrcrobles/javascript/tree/master/react)
  - ![jp](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Japan.png) **Japanese**: [mitsuruog/javascript-style-guide](https://github.com/mitsuruog/javascript-style-guide/tree/master/react)
  - ![kr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/South-Korea.png) **Korean**: [apple77y/javascript](https://github.com/apple77y/javascript/tree/master/react)
  - ![pl](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Poland.png) **Polish**: [pietraszekl/javascript](https://github.com/pietraszekl/javascript/tree/master/react)
  - ![Br](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Brazil.png) **Portuguese**: [ronal2do/javascript](https://github.com/ronal2do/airbnb-react-styleguide)
  - ![ru](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Russia.png) **Russian**: [leonidlebedev/javascript-airbnb](https://github.com/leonidlebedev/javascript-airbnb/tree/master/react)
  - ![th](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Thailand.png) **Thai**: [lvarayut/javascript-style-guide](https://github.com/lvarayut/javascript-style-guide/tree/master/react)
  - ![tr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Turkey.png) **Turkish**: [alioguzhan/react-style-guide](https://github.com/alioguzhan/react-style-guide)
  - ![ua](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Ukraine.png) **Ukrainian**: [ivanzusko/javascript](https://github.com/ivanzusko/javascript/tree/master/react)
  - ![vn](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Vietnam.png) **Vietnam**: [uetcodecamp/jsx-style-guide](https://github.com/UETCodeCamp/jsx-style-guide)

**[⬆ back to top](#table-of-contents)**
