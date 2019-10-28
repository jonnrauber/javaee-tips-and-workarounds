# Atributo _rendered_ de um componente JSF "não funciona"

```xhtml
<h:outputText value="Rá! Pegadinha do Mallandro!" 
        id="otPegadinha" rendered="#{minhaBean.exibePegadinha}">
```
## O problema
Esse é um erro que enche o saco do programador Java EE no início da carreira. 

Você quer que um elemento esteja invisível quando a página for carregada, mas que ele apareça quando alguma interação for feita. É aí que o desgraçado do componente não aparece. Você não entende o porquê, pois a regra para a exibição condicional já está toda feita no Bean/Controller e está tudo certo no XHTML... ou não?

## A solução

Na verdade faltou que alguma outra _tag_ envolva o componente que você quer que seja renderizado. Por exemplo um _panelGroup_:

```xhtml
<h:panelGroup id="pngPegadinha">
    <h:outputText value="Rá! Pegadinha do Mallandro!" 
        id="otPegadinha" rendered="#{minhaBean.exibePegadinha}">
</h:panelGroup>
```

## Explicação
> "Você não pode atualizar com AJAX um componente que por si só é renderizado condicionalmente. O AJAX/JSF não adiciona ou remove o destino de atualização na árvore DOM/HTML; em vez disso, substitui esse destino de atualização na árvore DOM/HTML. Se o componente não for renderizado, o AJAX/JSF não terá nada para substituir. Portanto você deve atualizar um componente pai, que é SEMPRE renderizado."

Adaptado de [BalusC no StackOverflow](https://stackoverflow.com/questions/15805389/attribute-rendered-is-not-working-properly-with-primefaces-3-5-fileupload)
