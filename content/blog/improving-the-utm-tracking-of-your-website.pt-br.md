---
title: "Melhorando a rastreabilidade de UTM no seu site"
description: "Melhore a rastreabilidade dos parâmetros de UTM nas landing pages de seus site de forma fácil com o utmkeeper"
publishDate: 2018-09-14
slug: melhorando-a-rastreabilidade-de-utm-no-seu-site
tags:
  - javascript
  - utm
images:
  - /img/utmkeeper/utm.png
aliases:
  - improving-the-utm-tracking-of-your-website
  - /blog/melhorando-a-rastreabilidade-de-utm-no-seu-site

---

Esses dias tive a oportunidade de encarar um desafio de frontend lá na [Configr](https://configr.com/?utm_source=humberto&utm_campaign=post-utm&utm_content=link-intro) que consistiu em melhorar a rastreabilidade dos usuários novos (os famosos leads da galera do marketing) que entram no no sistema. Mais especificamente a rastreabilidade dos [parâmetros de utm](https://en.wikipedia.org/wiki/UTM_parameters) das campanhas de marketing da empresa.

{{< tip class="info" >}}
**TLDR**: Eu criei uma biblioteca **javascript** chamada **utmkeeper** que faz a mágica para você e está disponível em [https://github.com/humrochagf/utmkeeper](https://github.com/humrochagf/utmkeeper)
{{< /tip >}}

{{< tip class="info" >}}
**UPDATE (21/09/2018)** Postagem atualizada para a cobrir a versão **0.3** do **utmkeeper**
{{< /tip >}}

## UTM

O termo **UTM** surgiu do **Urchin** que era um software de Analytics feito para analisar a efetividade de campanhas de marketing e utilizava os parâmetros do **U**rchin **T**raffic **M**odule para realizar esta tarefa. Com o tempo o **Google** passou a dar suporte a estes parâmetros, em seguida o **Facebook** e atualmente estes parâmetros são comumente utilizados para rastrear a efetividade de campanhas de marketing na internet popularmente conhecidos como **as utm tags**, ou simplesmente, **as utms**.

Elas funcionam com uma mecânica simples, você as codifica como parâmetro de **url** (aquelas coisas que vem depois do ponto de interrogação no link) utilizando o prefixo `utm_` seguido do tipo, que por padrão são cinco:

- `utm_source`: indica a origem de onde veio o lead
- `utm_medium`: indica o tipo do link, como por exemplo cpc no caso de vir de uma campanha de custo por clique
- `utm_campaign`: indica de qual campanha ele veio
- `utm_term`: indica o termo de busca utilizado para chegar na página
- `utm_content`: indica qual conteúdo da página foi clicado, se foi um link ou um banner por exemplo

Estes parâmetros foram definidos como o suficiente para rastrear a efetividade de uma campanha de marketing e atualmente são suportados pelas ferramentas de analytics, mas nada impede você de criar o seu `utm_algumacoisa`. Porém, para considerar estas customizações em métricas diferentes das fornecidas por estas ferramentas a implementação fica por sua conta dentro do seu sistema, foi daí que surgiu este desafio.

## O desafio

As **utms** chegam em sua landing page no primeiro acesso vindo de um anúncio ou link que as contém. Neste momento as ferramentas de analytics executam seu rastreio e, durante a navegação do usuário, as tags se perdem deixando as métricas limitadas apenas ao que estas ferramentas lhe oferecem. Análises mais aprofundadas e customizadas sobre o comportamento dos usuários não podem ser realizadas pois as **utms** se perderam no caminho e não chegaram até o cadastro, onde seriam salvas no seu sistema.

Pensando em alguma forma de resolver esta questão levei em consideração algumas coisas:

- Era preciso garantir a propagação da **utm** do acesso inicial, passando pela navegação entre as páginas até atingir o cadastro
- Esta propagação precisava acontecer entre domínios diferentes, o que faz com que o uso de [cookie](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Cookies) não seja uma opção muito boa para isso
- Um segundo acesso a página vindo de um outro anúncio antes do cadastro deve levar somente as **utms** do segundo anúncio em consideração, pois foi ela que motivou o cadastro, o que conta mais um ponto negativo para o cookie pois seria necessário criar regras de invalidação de cookie e replica-las através de landing pages em vários domínios.
- Era preciso ser algo simples de ser aplicado, seja em um django, em um html estático, wordpress, ghost e etc.

Com isso em mente, escrevi o **utmkeeper** (nome bem sugestivo né?!).

## utmkeeper

O **utmkeeper** é uma biblioteca escrita em **javacript** que tem o único propósito de capturar as **utms** e aplicá-las em todos os links e formulários da página sem interferir em sua navegabilidade.

Com ele eu consigo propagar as **utms** na navegação do usuário mesmo que ela aconteça entre domínios, manter sempre o último conjunto de **utms** utilizado para chegar inicialmente no site e aplicá-lo facilmente em diferentes plataformas.

Para usar o **utmkeeper** basta baixar o pacote pelo repositório [https://github.com/humrochagf/utmkeeper/releases](https://github.com/humrochagf/utmkeeper/releases)  ou instalar através do npm:

{{< highlight console >}}
npm install utmkeeper
{{< / highlight >}}

Em seguida adicionar o script ao final do **html** antes da tag de fechamento `</body>`:

{{< highlight html >}}
<script src="path/to/utmkeeper.js" charset="utf-8"></script>
<script charset="utf-8">
  utmkeeper.load();
</script>
{{< / highlight >}}

A função `load` é responsável por carregar a biblioteca e persistir a **utm** pela navegação. Nela podemos passar um **objeto** de configuração:

{{< highlight javascript >}}
{
  forceOriginUTM: true,
  fillForms: true,
  utmObject: {},
  postLoad: null,
}
{{< / highlight >}}

- **forceOriginUTM**: recebe um **booleano** que indica se você quer ou não sobrescrever qualquer **utm** que já estiver em algum link da página. Seu valor padrão é `true`
- **fillForms**: recebe um **booleano** que indica se você deseja adicionar as **utms** nos formulários da página. Caso já exista um campo para ele seu valor será preenchido, caso contrário um campo do tipo `hidden` será adicionado no formulário para cada **utm**. Seu valor padrão é `true`
- **utmObject**: recebe um **objeto** onde serão salvos as **utms**, pode ser usado para enviar valores iniciais de utm para serem usados caso algo aquela **utm** não seja preenchida
- **postLoad**: recebe uma função de um parâmetro que será rodada ao final do processamento da função `load` sendo este parâmetro um objeto com as **utms** extraídas. Ele pode ser usado para rodar alguma lógica customizada como enviar estes dados para um gerenciador de leads como o [mautic](https://www.mautic.org/)

Um exemplo da função `load` com todos os parâmetros é o seguinte:

{{< highlight javascript >}}
utmkeeper.load({
  forceOriginUTM: true,
  fillForms: true,
  utmObject: {
    utm_test: 'test'
  },
  postLoad: function(utms){console.log(utms);}
});
{{< / highlight >}}

## Resultado

O gráfico a seguir é um recorte do período de transição em que foi feita a aplicação do **utmkeeper** nas landing pages da [Configr](https://configr.com/?utm_source=humberto&utm_campaign=post-utm&utm_content=link-resultado) sendo representado pela cor **azul** o total de usuários cadastrados por dia, em **vermelho** o total que chegavam ao cadastro sem a **utm** e em **verde** o total de usuários que chegavam ao cadastro com **utm**:

![gráfico de utm](/img/utmkeeper/utm-graph.png)

A facilidade de aplicação do **utmkeeper** ajudou bastante no processo de implantação da solução trazendo um rápido resultado nas métricas de rastreio.
