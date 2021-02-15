---
layout: post
title:  "Scraping vs Crawling"
date:   2020-05-18 22:02:37 -0300
categories: scraping
---
Geralmente esses termos são difundidos de maneira equivocada. Para entender melhor, é necessário saber o que cada um deles significa. Scraping, ou aportuguesando um pouco podemos chegar em “garimpagem”, que é o ato de extrair informações e posteriormente salvá-las em algum local (geralmente uma base de dados) para depois serem analisadas. Basicamente, scraping não se limita somente a web já que é possível realizar scraping de um site, de um banco de dados, de arquivos, as opções são diversas. Já um crawler, popularmente conhecido como bot ou _spider_ (_World wide web_, _spider_, web, pegou o trocadilho?), vai buscar informações ou salvar páginas web completas para depois analisar as informações. Conseguiu perceber a diferença entre eles? Um crawler é exclusivamente para web e se difere tanto em escala quanto script que realiza scraping.

Para melhorar o entendimento, eu vou explicar o processo de funcionamento de um crawler.

Um web crawler inicia com uma lista de URLs para visitar, conhecidas como seeds. A medida que o crawler visita essas URLs, ele primeiro vai buscar novos links para adicionar em uma outra lista chamada de crawl frontier ou fronteira de raspagem. Essa nova lista é uma estrutura de dados para salvar URLs que são elegíveis para raspagem. Caso não tenha ideia, compare a lista à uma fila de prioridade.


> Essa fronteira é um dos componentes que torna possível a arquitetura de um crawler. Ela que contém a lógica e as políticas que os _spiders_ seguem quando vão visitar os sites. Nas políticas é possível incluir coisas como qual página deve ser visitada, prioridades que devem ser buscadas em cada página e em qual frequência uma página deve ser visitada.

Após esse processo de salvamento das novas URLs, as _spiders_ vão começar a arquivar as páginas (tipo o “Salvar como” de um navegador) em snapshots de modo que seja possível visualizar, ler e navegar como se fosse o próprio site.

É necessário ter alguns cuidados quando se usa um web crawler:

1. A internet é enorme. Todos os dias é criado conteúdo novo, muitas vezes sobre um mesmo assunto já existente que já foi postado e difundido em diversos blogs (como essa mesma explicação que eu estou fazendo). Com isso, é necessário se ter um cuidado extra na hora de programar um crawler para evitar um possível problema de duplicação de dados, seja no ato da captura ou no momento em que os dados serão salvos no banco de dados;
2. Aproveitando o gancho do ponto um, o número de sites que geram URLs pelo lado servidor (server-side) torna difícil o processo dos crawlers na tentativa de evitar conteúdo duplicado em suas buscas. Existem infinitas combinações de parâmetros nas URLs, no qual apenas uma porção dessas vai de fato retornar um conteúdo único (que ainda não foi capturado ou que não se encontra no banco de dados). Por exemplo, uma galeria de fotos pode oferecer algumas opções para um usuário como:
  - Três modos de ordenar uma imagem;
  - Quatro escolhas de tamanho de thumbnail;
  - Cinco formatos de arquivos;
  - Com isso eu tenho um número enorme de URLs para acessar o mesmo conteúdo. Essa infinidade de combinações é um dos problemas que os _spiders_ têm que enfrentar, visto que eles precisam ordenar entre várias URLs para trazer algo novo e único.


3. Outro problema é a coordenação das _spiders_ para requisições sucessivas. Elas precisam ser “educadas” o suficiente para saber que existe um limite de requisição em todo servidor que eles permitem e eventualmente evitar um estresse desnecessário. Vale lembrar que todo site tem sua política e regras, no qual nem todos permitem fazer crawling de seu conteúdo. Tenham isso em mente pois é muito importante;

4. Por fim, mas não menos importante, é necessário definir crawling agents, onde cada um irá gerenciar um nicho diferente de _spiders_ e seus propósitos. Existem sites sobre os mais diversos assuntos e é indicado criar políticas de crawl para um desses sites.

Com isso, finalizo esse post e espero que tenha ficado um pouco mais clara a diferença entre esses dois termos.