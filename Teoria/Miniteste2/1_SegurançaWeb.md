# FSI | Resumos Miniteste2

## 1. Segurança Web

### 1.1. Protocolo HTTP

- Hyper Text Transfer Protocol
- Protocolo que usa logical links / hyperlinks (permite num determinado documento referenciar outras páginas web / documentos). O utilizador pode seguir essa hyperlink, clicando nesse link.
- Um recurso é identificado por uma localização uniforme (URL):
  - esquema ou protocolo (HTTP, HTTPS, ...);
  - domínio (aaa.bbb.cc), potencialmente com uma porta específica;
  - caminho para o recurso (path);
  - informação extra (informação da query ou identificador de fragmento).
- Comunicação efetuada através de pedidos:
  - GET: obter recurso numa URL específica (não deve ser usado para alterar o estado do servidor pois tem sempre side-effects);
  - POST: criar um novo recurso numa URL específica (utilizado para quase tudo o que originalmente era previsto para PUT, PATCH e DELETE);
  - PUT: substituir representação de recurso existente com outro conteúdo;
  - PATCH: alterar parte de um recurso;
  - DELETE: apagar recurso numa URL específica.

### 1.2. Cookies

Forma de as aplicações do servidor reconhecerem pedidos relacionados: estado -> cookie:
  - servidor envia para browser a cookie, e este armazena essa informação num ficheiro;
  - sempre que uma URL é acedida no mesmo servidor a cookie é enviada.
  - útil para: gestão de sessões, personalização, rastreamento/profiling.
  - só é possível definir cookies para a origem ou domínios que estão acima hierarquicamente da origem.

### 1.3. Modelo de Execução

- Complexo, pois estamos a considerar uma aplicação (browser) onde correm várias sub-aplicações (iframes) que vêm de diversas origens e o browser está de alguma forma a tentar garantir que essas aplicações não interferem umas com as outras.
- Origem: protocolo que está a ser utilizado e o domínio (e.g.: protocolo: HTTP; domínio: feup.up.pt).
- JavaScript consegue aceder a todos os recursos que vêm da mesma origem, incluindo a própria origem do JavaScript. No entanto, o JavaScript também consegue executar código de outra origem se for executado no meu contexto (em meu nome).
- Computações do lado do servidor: bases de dados, gestão de sessões, personalização.
- Computações do lado do cliente: browsers (pequenos sistema de virtualização), onde cada janela:
  - processa respostas HTML;
  - executa JavaScript se necessário;
  - efetua pedidos para sub-recursos (imagens, CSS, JavaScript);
  - responde a eventos do utilizador ou eventos definidos pelo próprio site.
- Uma janela do browser pode ter várias tabs com conteúdo de diferentes origens. Cada tab é uma frame e possui várias iframes (elementos HTML que permitem embeber uma página web dentro de outra).
- Vantagens de (i)frames:
  - Delegar uma área do ecrã para outra origem (anúncios);
  - Aproveitar proteção do browser direcionada para isolamento de frames;
  - Isolamento entre frames permite que a página mãe funcione mesmo que uma (i)frame falhe.
- O código JavaScript pode ler e alterar o estado de uma página com o DOM: interface orientada a objetos, que representa toda a página de forma hierárquica e que permite alterar, inspecionar e adicionar elementos dessa página.

### 1.4. Modelos de Ataque

- Ataque externo/rede: adversário que controla apenas o meio de comunicação.
- Ataque interno/web: adversário que controla parte da aplicação web. Variantes:
  - Adversário que controla servidor;
  - Adversário que controla o cliente;
  - Adversário que controla uma página do cliente;
  - Adversário que controla um objeto embebido numa página.

### 1.5. Modelo de Segurança

Same Origin Policy (SOP): contexto de isolamento que corresponde à fronteira de confiança na Web.
- confidencialidade: dados de uma origem não podem ser acedidos por código de origem diferente;
- integridade: dados de uma origem não podem ser alterados por código com uma origem diferente.
- SOP no DOM: cada frame tem uma origem e código numa frame só pode aceder a dados com a mesma origem;
- SOP para mensagens: frames podem comunicar entre si;
- SOP para cookies: as cookies apenas são enviadas pelo browser para servidores com a mesma origem que as criou:
  - SameSite = None, envio de cookies se domínio da cookie for sufixo do domínio da URL e se a path da cookie for prefixo da path da URL;
  - SameSite = Strict, envio de cookies apenas quando o pedido tem a mesma origem que a top-level frame (página principal);
  - SameSite = Lax, envio de cookies quando o utilizador está a navegar no site de origem; todos os links presentes num dado site terão acesso às cookies -> permite ataques como CSRF;
  - Secure cookies, envio de cookies apenas por HTTPS (para evitar que alguém as inspecione na rede).
- SOP para comunicações com servidor:
  - Uma página pode fazer pedidos HTTP fora do seu domínio;
  - Escrita permitida (permite enviar/expor dados a serviços);
  - Embedding de recursos de outras origens é permitido;
  - Dados contidos na resposta podem ser processados pelo browser mas não podem ser analisados programaticamente.

Cross-Origin Resource Sharing (CORS): permite relaxar os pedidos cross-origin que são permitidos. 
- Pedidos simples de site A de recursos no servidor B:
  - não devem causar side-effects no servidor B;
  - browser faz pedido e verifica se resposta admite que o código em A pode aceder aos recursos provenientes de B;
  - o servidor B pode permitir mais casos de uso através do atributo Access-Control-Allow-Origin.
- Pedidos pre-flighted de site A de recursos no servidor B:
  - podem causar side-effects no servidor B;
  - browser faz pedido dummy e verifica se resposta admite que o código em A pode aceder aos recursos provenientes de B;
  - o servidor B pode permitir mais casos de uso através do atributo Access-Control-Allow-Origin;
  - se o acesso for permitido então browser faz pedido real.

### 1.6. Vulnerabilidades e Ataques

- Cross-Site Request Forgery (CSRF): ataque que provoca side-effects no servidor e que explora a confiança que um utilizador tem do browser.
  - Tipo de exploit malicioso de um website, no qual comandos não autorizados são transmitidos a partir de um utilizador em quem a aplicação web confia.
  - Há vários meios em que um website malicioso pode transmitir tais comandos (tags de imagem, formulários ocultos, XMLHttpRequests). Estes podem funcionar sem a interação do utilizador ou mesmo sem o seu conhecimento.
  - Problema: servidor é o alvo, visto que este não sabe se quem faz o pedido é legítimo ou um atacante.
  - Solução:
    - para impedir login: token secreto (dinâmico) na form HTML que site JavaScript não consegue ler e devolver no POST;
    - para impedir uso de cookies: SameSite = Strict

- Injeção de Comandos: input não é validado e input malicioso causa sequência de execução anómala (código escolhido por atacante) que pode acontecer na shell, numa base de dados, ...
  - Mesma ideia dos buffer overflows mas a um nível tecnológico diferente.
    - Node.js: eval() é um avaliador de código arbitrário;
    - PHP: exec() é uma chamada à shell;
  - Nenhuma chamada a este tipo de recurso pode incluir inputs sem validação.

- SQL Injection (SQLi): input do utilizador malicioso altera a semântica do comando SQL construído dinamicamente. Alguns motores de bases de dados permitem ainda chamadas ao sistema a partir de SQL.
  - Solução:
    - Nunca criar comandos SQL dinamicamente como strings:
    - Usar sempre instrumentos fornecidos pelas próprias linguagens de programação e/ou software stacks:
      - comandos parametrizados: comandos estáticos no código com placeholders
      - bibliotecas ORM: oferece abstração independente de SQL e do backend de BD. Esta implementação oferece mecanismos de sanitização de inputs (quando oferece...).

- Cross Site Scripting (XSS): Injeção de código do lado do cliente:
  - atacante consegue que um site legítimo envie código malicioso para o browser do cliente;
  - reflected XSS: o atacante faz um pedido a um site legítimo e a payload maliciosa é "refletida" para executar na máquina do cliente;
  - stored XSS: o atacante conseguir armazenar a payload maliciosa num recurso armazenado no site legítimo (ex.: entrada na BD).
  - Solução:
    - aplicar filtros aos inputs/pedidos;
    - Content Security Policy (CSP):
      - cada site pode incluir este atributo para fazer white-listing de origens para scripts;
      - scripts inlined não são executados: apenas scripts provenientes explicitamente de sites na white-list;
      - exemplo: Content-Security-Policy: default-src 'self'; img-src *; script-src cdn.jquery.com.
    - Subresource Integrity: especificação que não executará um script se detetar que este foi alterado: associar uma hash a cada script da white-list.

- Session Hijacking: interceção da communicação entre o cliente e o servidor.
  - Objetivo: conseguir provocar o envio de uma cookie e apanhá-la em trânsito.
    - Site malicioso pede recurso;
    - Browser envia essa cookie para o servidor;
    - Embora o atacante não tenha acesso a essa cookie, é transmitida na mesma. Se não for enviada através de um canal seguro (se não usar HTTPS), alguém à escuta na rede pode observar a cookie a passar.
    - O atacante consegue então "roubar a sessão", pois só precisa da session-id para estar autenticado.