# FSI | Resumos Miniteste2

## 3. PKI (Public-Key Infrastructure)

A criptografia de chave pública pressupõe chaves públicas autênticas. Caso contrário: ataques Man-in-the-Middle.

No mundo real isto pode ser resolvido de forma ad-hoc:
- confirmar manualmente que a chave pública pertence à entidade correta;
- utilizar sistemas de autenticação de chaves públicas como PGP/GPG.

Quando é necessária cobertura legal/regulamentar => PKI:
- Normas técnicas: que algoritmos/formatos utilizar;
- Regulamentação: como devem ser utilizadas as normas técnicas;
- Mais regulamentação: responsabilidades/direitos dos participantes;
- Leis: garantias formais e penalizações em caso de violação das regras.

### 3.1. Certificados de Chave Pública

Objetivo:
- Alice envia a Bob uma chave pública pk através de canal inseguro;
- Bob tem de obter garantia de que Alice detém a chave secreta correspondente.

Solução trivial:
- Bob tem canal autenticado com Trusted-Third-Party (TTP);
- Alice demonstrou anteriormente a TTP que é dona de pk (como?);
- Bob pergunta a TTP (online) se pk pertence a Alice.

Problemas na prática:
1. Como se constrói o canal entre o Bob e a TTP?
2. E se a TTP estiver offline?
3. Como é que garantimos que o Bob e a Alice confiam na mesma TTP?
4. O que é que "Trust"/confiança na TTP significa?

- #1 e #2: Tecnologia: Certificados;
- #3 e #4: PKI: Procedimentos/Regulamentos.

Os certificados de chhave pública usam assinaturas digitais para resolver os pontos #1 e #2:
- TTP: Autoridade de Certificação (CA);
- Alice prova a CA que possui pk:
  - Assinando um pedido de certificado que contém pk usando a chave secreta (PKCS#11);
  - Simplesmente porque CA fornece pk e chave secreta correspondente a Alice.
- CA fixa/verifica todos os dados relevantes para certificado:
  - Identidade de Alice + chave pública de Alice;
  - Informação específica da CA: identidade e novo número de série;
  - Validade (datas de início/fim).
- CA assina documento eletrónico com esta informação.

Tecnicamente um certificado é:
- documento codificado com regras ASN.1/DER.
- Abstract Syntax Notation 1: independente de plataforma/linguagem;
- Legacy: linguagem de especificação herdada das normas para protocolos;
- Normas usam ASN.1 para especificar estruturas de dados (pacotes);
- DER (Distinguished Encoding Rules) especificam codificação em bytes.

Como é que os certificados resolvem os aspetos 1 e 2:
- Assinatura digital garante a Bob que informação veio de CA/TTP;
- CA pode estar offline: Bob pode obter o certificado via Alice.

Podemos assim transferir todos os certificados por canais inseguros?

Outras perguntas importantes:
- Como é que Bob "conhece" a CA e verifica a sua assinatura?
- Em que aspeto é a CA confiável para Bob e Alice?

### 3.2. Verificação de Certificados

Suponhamos que Alice envia a Bob certificado com:
- Identidade de Alice + Chave pública de Alice;
- Período de validade;
- Metainformação adicional;
- Tudo assinado por uma CA.

O Bob deve fazer o seguinte:
1. Verificar correção da identidade da Alice (ex.: DNS de um servidor);
2. Verificar que hora atual está no período de validade;
3. Verificar metainformação (específico para cada aplicação);
4. Verificar que CA é de confiança;
5. Obter chave pública da CA para verificar assinatura no certificado.

Os 3 primeiros pontos e a verificação de assinatura são puramente tecnológicos;

A PKI resolve os pontos 4 e 5.

### 3.3. Ficha técnica de certificados de chave pública

- Normalizados no X.509 e transpostos para a internet pela IETF;
- Tipos de dados importantes => identificadores de objeto (OI) standard fixos pela IETF;
- versão atual é a 3, que inclui campos standard: subject, issuer, validity, public key info, serial.
- extensões, algumas das quais podem ser assinaladas como críticas:
  - todas as extensões têm um OI;
  - se estiver marcada como crítica e não for conhecida => certificado inválido;
- extensões importantes:
  - Subject/Authority key identifier: hash da chave pública;
  - Basis constraints: flag que assinala certificado como pertencendo a CA;
  - Key usage: CA pode restringir utilização do certificado.

### 3.4. Funcionamento e Gestão

PKI: Tudo o que é necessário para assegurar que:
- O utilizador de um certificado (end entity) recebe garantia;
- Dada por autoridade de certificação de confiança;
- De que uma chave pública pertence a outra entidade;
- E pode ser utilizada para um determinado fim;
- Com responsabilidades/obrigações bem definidas para todos.

Como os certificados circulam na rede?
- Vários protocolos especificam como isso pode ser feito:
  - podem ser armazenados em repositórios ou simplesmente nas aplicações/SOs;
  - podem ser transferidos entre aplicações em protocolos específicos (HTTP, FTP, MIME);
  - têm de ser codificados em formatos que garantam interoperabilidade.
- Já falamos de várias normas que desempenham este papel:
  - No TLS o RFC especifica como é que se trocam certificados;
  - Os SOs/browsers gerem certificados em componentes seguros dedicados.

Perguntas importantes:
- Como é que um utilizador contacta/toma conhecimento de uma CA?
- Como é que o Bob verifica a assinatura produzida por uma CA num certificado?

Resposta:
- Numa PKI, todas as chaves públicas são codificadas em certificados X.509;
- Alguns certificados especiais contêm chaves públicas de CAs;
- O Bob obtém a chave pública da CA num outro certificado;
- O Bob usa a chave pública no certificado da CA para verificar assinatura no certificado da Alice;
- Verificação OK => Bob pode usar a chave pública da Alice.
- Conclusão:
  - O Bob conhece de antemão o certificado da CA que emitiu o certificado da Alice;
  - Bob confia nessa CA para ter verificado que, de facto, a Alice é titular daquela chave pública (possui a chave privada correspondente).

Pergunta de teste: Qual é a garantia que eu tenho se alguém me mandar o certificado dessa pessoa e o certificado de autoridade de certificação que o assinou, que nunca vi antes, pela internet?
- Zero garantias, porque qualquer pessoa podia ter criado esses certificados. Se isso fosse garantido, ataques Man-in-the-Middle funcionariam sempre.

Como é que o Bob sabe se pode confiar na CA?
- O Bob obtém o certificado diretamente da CA via canal seguro;
- O Bob confia na CA implicitamente (porque a comunidade confia).

### 3.5. Cadeias de Certificação

Caso simples: Bob confia na CA usada pela Alice de forma implícita.

Normalmente não é assim tão simples:
- Bob é inicializado com certificados de raíz;
- O Bob confia implicitamente nestas CAs;
- Os certificados de raíz são auto-assinados:
  - CA gera um par de chaves (sk, pk);
  - CA cria o seu próprio certificado com subject = issuer = CA name;
  - O certificado inclui pk e a CA assina-o com sk.

Qualquer pessoa pode gerar um certificado auto-assinado a dizer que é uma CA específica.

Validar um certificado de raíz/auto-assinado implica:
- acreditar que a chave secreta associada pertence, de facto, à CA;
- acreditar que essa CA é de confiança => só produz certificados bons.

Geralmente as Root CAs não emitem diretamente certificados para utilizadores finais:
- Existe uma hierarquia de CAs:
  - Se a CA com nome A assina o certificado da CA com nome B;
  - Então confiança em B <= confiança em A.

Confiança = validação contínua até encontrar Root CA em que o utilizador confia.

Autoridades de Registo (RA):
- Front-end: contacto direto com os utilizadores;
- Responsáveis por verificar os dados colocados nos certificados;
- Responsáveis por verificar posse de chave privada correspondente à chave pública presente no certificado.

Autoridades de Certificação (CA):
- Back-end: infra-estrutura que produz chaves e certificados;
- Tipicamente high-security: air gaps, segurança física, ...

Revogação
- Certificados são sempre inválidos quando fora do período de validade assinado;
- Como se pode invalidar um certificado válido?
  - Perda da chave secreta, CA corrompida, metadados deixam de ser válidos.
- É preciso revogar os certificados enquanto eles ainda são válidos.
- Certificate Revokation Lists (CRL):
  - CA publica periodicamente black-list de certificados revogados;
  - Consumidores de certificados devem publicar CRL mais recente;
  - CRLs podem ser publicadas excecionalmente, mas apenas best-effort.

3 soluções para este problema:
- Trusted Service Provider Lists (TSL):
  - white-list atualizada de certificados;
  - usada em comunicações pequenas/fechadas e high-security.
- Online Certificate Status Protocol (OCSP):
  - servidor seguro (geralmente gerido pela própria CA) verifica estado de revogação;
  - usado tipicamente em contextos organizacionais.
- Certificate Pinning:
  - web servers/browsers/aplicações gerem white-lists próprias;
  - permitem identificar certificados mais importantes para entidades críticas (ex.: Google).

### 3.6. Políticas de Certificação

PKI pode ser utilizada para dar valor legal a assinaturas digitais => assinaturas qualificadas.

Política de Certificação: conjunto de regras de operação:
  - direitos e responsabilidades para titulares e utilizadores;
  - direitos e responsabilidades para as CAs.
  - Estes direitos e responsabilidades podem ser estipulados legalmente.

Uma política de certificação recebe um object identifier (OID):
- certificados podem incluir este OID;
- por lei, apenas pode usar este OID uma autoridade que foi sujeita a auditoria e credenciada para o efeito.