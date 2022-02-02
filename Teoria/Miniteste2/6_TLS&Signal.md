# FSI | Resumos Miniteste2

## 6. TLS e Signal

### 6.1. Transport Layer Security (TLS)

Modelo de Segurança:
- atacante na rede (já vimos):
  - controla a infra-estrutura: routers, DNS, ...;
  - escuta, injeta, bloqueia, altera ppacotes/mensagens.
- ex.: rede sem fios num café, rede num hotel, nosso ISP, ...

Handshake TLS1.3 = Diffie-Hellman Autenticado:
- historicamente o handshake TLS utilizava:
  - transporte de uma chave de sessão do cliente para o servidor (RSA);
  - autenticação implícita do servidor => utilização correta da chave de sessão.
- hoje em dia são evidentes as vantagens DH:
  - eficiência com utilização de curvas elípticas;
  - perfect forward secrecy:
    - chaves de longa duração são utilizadas para assinaturas e não para transporte de chaves de sessão;
    - comprometer uma chave de longa duração não compromete acordos de chave passados.

TLS implica PKI:
- Servidor autentica a troca DH com uma assinatura digital (assinatura cliente = opcional);
- Como é que o cliente conhece a chave de verificação do servidor?
  - Servidor envia o seu certificado de chave pública;
  - Cliente verifica validade do certificado (root CAs pré instaladas):
    - nome de domínio faz match com subject do certificado;
    - é possível utiliza um wildcard no item mais à esquerda do DN (ex.: *.a.com).
  - Cliente utiliza chave pública no certificado para verificar assinatura digital no protocolo DH;
- Recordar: cliente não autenticado => servidor não sabe com quem fala/estabeleceu chave.

Handshake TLS 1.3: Otimização:
- Dados cifrados com chave pré-partilhada: vulnerável a ataques por repetição;
- Pode ser viável para pedidos que não causam side-effects.

### 6.2. Integração TLS/HTTP

Mensagens HTTP são transmitidas usualmente como payloads TLS depois do canal estabelecido.
- Problemas/Soluções:
  - web proxy: proxy precisa de saber o cabeçalho HTTP para estabelecer ligação;
    - cliente envia nome de domínio antes do client-hello do TLS.
  - virtual hosts: mesmo IP com múltiplos DNS => como sabe o servidor que certificado devolver?
    - solução antiga: client-hello inclui nome de domínio do servidor;
    - TLS 1.3 tenta preservar privacidade do nome de domínio (certificado cifrado);
      - solução futura: nome de domínio cifrado com chave pública proveniente do DNS.
- Porque não utilizar sempre HTTPS para todo o tráfego?
  - antigamente => performance;
  - hoje em dia => AES-NI: aceleradores de hardware (não há desculpas);
  - Desde 2018 browsers tendem a catalogar sites HTTP como inseguros.

TLS/HTTPS nos browsers:
- duvidoso se os indicadores visuais são suficientes:
  - confirmamos o nome de domínio?
  - confirmamos que o aloquete está ativo?
- E se nos convencem a fazer uma ligação HTTP?
  - política correta do servidor => redirecionar pedidos HTTP para HTTPS;
  - mas um Man-in-the-Middle poderia ligar-se em nosso nome a HTTPS;
    - SSL Strip Attack (browsers modernos apresentam avisos);
    - solução: possível incluir nos cabeçalhos informação de que todas as ligações futuras devem ser feitas por HTTPS (Strict Transport Security) => desaparece com limpeza da cache.
- Não esquecer ataques Man in the Middle: só são impedidos se conseguirmos autenticar chave pública do servidor;
- O impacto de uma Autoridade de Certificação corrompida é devastador;
- Só se pode corrigir eliminando certificados dos sistemas operativos/browsers;
- Potenciais problemas de mixed content HTTP/HTTPS (hoje em dia os browsers avisam destes problemas):
  - nunca utilizar objetos embebidos a partir de links HTTP;
  - ex.: ir buscar um script JavaScript por HTTP: atacante pode substituir o script.

### 6.3. Privacidade

TLS revela ainda muita meta informação (tamanho e número de mensagens).
- Análise de tráfego permite reconhecer:
  - com que servidor se está a interagir;
  - que operações se estão a fazer.

### Signal

- Vários protocolos de messaging seguro com "end-to-end-encryption" surgiram nos últimos anos, que protegem contra a curiosidade do próprio servidor;
- Os grandes fabricantes de software e fornecedores de serviços adoptaram esse standard de segurança, sob pena de perderem utilizadores;
- O mais proeminente é o Signal, que tem as suas próprias aplicações, e é também adotado pelo WhatsApp e pelo Facebook Messenger.

Estrutura:
- Comunicação tem de ser possível mesmo com uma das parties offline: implica utilizar o servidor como buffer e, inicialmente, como canal que autentica utilizadores;
- Bootstrap feito quando um utilizador regista uma identity key (chave de assinatura de longa duração) no servidor: autenticação com base no telemóvel.

Acordo de chaves tem 3 partes:
- Handshake inicial: extended tripple Diffie-Hellman;
- Asymmetric ratchet (quando recebemos DH fresco): recalcula-se chave de sessão com mistura de DH antigo e DH novo;
- Symmetric ratchet (quando não recebemos DH fresco): recalcula-se chave de sessão com base em hashing.

Cada mensagem é protegida com uma chave diferente => perfect forward secrecy.

Introduz-se um novo objetivo de segurança: post-compromise security, que permite recuperar segurança mesmo se o estado interno do protocolo for revelado.

Comunicação Offline:
- para garantir comunicação quando um contacto está offline, todos os contactos submetem regularmente um número significativo de chaves DH efémeras para o servidor;
- quando queremos enviar uma mensagem a um contacto, podemos pedir uma dessas chaves ao servidor, para termos um elemento fresco para o ratcheting assimétrico ou handshake inicial.

Registo:
- Identity Key (Kid): DH longo prazo + assinatura;
- Signed DH prekey de médio prazo assinada com Kid (partilhadas por todos os emissores);
- Chaves DH de curto prazo, descartadas pelo servidor uma vez entregues a um emissor.
- Resumo:
  - Existem chaves DH de curta e média duração para acautelar ataques MitM localizados no tempo (necessário intercetar ambas);
  - Existem chaves de assinatura de longa duração => necessário intervir no início para ser possível lançar ataques MitM sobre chaves DH de média duração.

Ratcheting
- depende das mensagens transmitidas e recebidas;
- quando transmitimos várias seguidas, usamos ratchet simétrico;
- quando recebemos um novo valor DH do destinatário, fazemos ratchet assimétrico;
- destroem-se as chaves de transmissão (mk) e guarda-se a última chave de cadeia (ck).

Pressupostos e garantias:
- Recebemos o idk inicial por canal autêntico;
- Recebemos DH do primeiro triple handshake por canal autêntico;
- Isto garante confidencialidade e autenticidade do master secret;
- O esquecimento das chaves garante perfect forward secrecy;
- Se houver corrupção total do estado, podemos recuperar segurança se houver um ratchet assimétrico que o adversário não controla poderemos recuperar segurança;
- Corrupção total do estado implica que idk já não é autêntica, e portanto novas sessões serão vulneráveis a MitM;
- Não tem como objetivo a deniability, mas seria possível usar qualquer coisa como no OTR.