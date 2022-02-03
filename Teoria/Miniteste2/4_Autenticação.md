# FSI | Resumos Miniteste2

## 4. Autenticação

Autenticação de origem de mensagens (resumos anteriores):
- destinatário tem garantia que mensagem teve origem em emissor específico (assinaturas, MACs);
- nos canais seguros estende-se a várias mensagens/pacotes e à sequência completa de mensagens enviadas;
- não existe um requisito de tempo: mensagem enviada agora/recentemente.

Autenticação de entidades (estes resumos):
- Bob tem a certeza que Alice participou ativamente/agora num passo processual;
- exemplo: autenticação para acesso a um website, para abrir uma porta, ...;
- geralmente seguido de um passo de autorização: Bob decide se Alice deve ter acesso ao recurso.

### 4.1. Solução Criptográfica

Protocolo de Desafio-Resposta:
- Bob cria um desafio fresco (ex.: valor aleatório) e envia para Alice;
- Alice assina digitalmente ou calcula um MAC sobre o desafio e devolve;
- Bob verifica assinatura/MAC, potencialmente dentro de um tempo limite.

Que propriedades devem ter os componentes?
- Desafio imprevisível: impossível replay;
- assinatura digital, MAC: chaves autênticas, impossível falsificar. 

Observação:
- se for um utilizador humano => intervém no processo?
- como se autenticam humanos?

### 4.2. Autenticação de Utilizadores

Cenário:
- Utilizador humano (Alice) pretende aceder a recurso online;
- Recurso disponível em servidor remoto (Bob);
- Pretende-se garantir participação ativa/na hora do utilizador.

Solução: 
- Utilizador humano fornece identidade e pede acesso;
- Servidor remoto solicita prova de identidade;
- Utilizador fornece prova de identidade.

Observação:
- Estrutura parecida com desafio-resposta;
- Pode ser implementado com auxílio de protocolo criptográfico anterior.

Provas de identidade:
- algo que se sabe/conhece (ex.: password);
- algo que se possui (ex.: smartcard, telemóvel, ...);
- algo que é intrinsecamente (ex.: biometria).
- Podem ser utilizadas isoladamente;
- Podem ser combinadas: autenticação multi-factor.

Algo que se sabe: um segredo que apenas um humano específico conhece:
- Código secreto gerado para o efeito: password, PIN;
- Segredo sobre a pessoa: nome de animal de estimação, música preferida, ...
- Na prática prova apenas conhecimento do segredo: implica assumir que apenas aquele humano conhece o segredo.

### 4.3. Passwords

Solução legacy: não conseguimos ainda evoluir para melhor.
- Vantagem: simples: Alice prova que conhece password fornecendo-a ao sistema.
- Problemas: 
  - Um atacante passivo pode passar a conhecer a password:
    - Precisamos de canal seguro para enviar password.
  - Um atacante ativo pode fazer-se passar pelo servidor:
    - Alice precisa de ter a certeza de que está a falar com o servidor.

Ataques a passwords: A pessoas
- Revela password inadvertidamente: engenharia social, phishing, reutilização de passwords antigas, post-it.
- Motivação de Alice em proteger a password: PIN multibanco, Gmail, Netflix, Email institucional? Em quais vai usar uma fácil/difícil de adivinhar?
- Que medidas de proteção?
  - post-it: excelente contra ataques remotos, péssimo contra ataques locais => modelo de ameaças?

Passwords Fortes
- ideal: passwords que são difíceis de adivinhar mas fáceis de memorizar;
- políticas típicas:
  - composição heterogénea: letras maiúsculas e minúsculas, números, símbolos;
  - comprimento mínimo, período de duração máximo;
  - black-list de passwords banidas.
- por vezes estar regras são contra-producentes.

Phishing: Eve leva Alice a simplesmente dar-lhe a password.
- Vetor de ataque: a Alice pode não perceber que está a fazer login no servidor errado.
- HTTPS => certificado válido com nome de domínio;
- Ligados a servidor UP?
  - Não necessariamente;
  - Ligados a servidor que:
    - possui chave privada;
    - associada a certificado;
    - com DN = sigarra.up.pt
  - E se fosse sigarra.vp.pt ou sigarra.up.qt? Quem reparava?
  - Pressuposto que utilizador verifica DNS;
  - Browsers procuram tornar isso mais fácil;
  - Mesmo assim é possível tentar enganar o utilizador.
  - Homoglyphs: registar domínios parecidos ou com outro set de caracteres

Ataques a Passwords: Endpoint
- Ataques a endpoints, software e periféricos (shoulder surfing, key-loggers em hardware e malware);
- Sempre possíveis, importância de passwords fortes e não reutilizáveis;
- HW keyloggers: dispositivos colocados entre teclado e PC;
- SW keyloggers: malware que interceta keystrokes;
- Outros tipos de malware:
  - passwords em memória (clipboard);
  - passwords armazenadas (passwords.txt, cache dos browsers, ...).

Ataques a Passwords: Rede
- Para intercetar password ou passar-se por servidor;
- Ligação TLS geralmente suficiente: autentica o servidor.

Ataques a Passwords: Servidor
- Data breaches: muito prováveis, importância de passwords fortes e não reutilizáveis.
- Ataques online: usar servidor como oráculo para tentar acertar;
- Difícil com proteções atuais, erros opacos, número de tentativas, ...;
- Sempre possível com passwords comuns.

Armazenamento de passwords: server-side
- Qual o impacto de uma data breach?
  - que informação revela sobre passwords armazenadas num servidor?
  - depende da forma como são geridas/armazenadas
  - solução naive:
    - lista de password/username;
    - data breach revela toda a informação;
    - se passwords reutilizadas noutro sistema => quinou!
- O servidor não precisa de saber a password: apenas preciso de ser capaz de reconhecer a password correta.
- O servidor pode guardar apenas H(pw):
  - se H for uma função de hash criptográfica;
  - tem geralmente uma propriedade => difícil encontrar pré-imagens:
    - difícil encontrar pw dado H(pw);
    - se pw for difícil de adivinhar.

Armazenar H(pw) ainda não é a solução ideal:
- dado H(pw) um atacante pode verificar os seus palpites;
- passwords iguais são armazenadas da mesma maneira;
- ataques de dicionário:
  - dicionário => coleção de passwords possíveis/prováveis;
  - tentar todas as possibilidades até encontrar a correta
  - pré-computar todos os H(pw) no dicionário e construir uma hash-table: dado H(pw*) para pw* desconhecido basta procurar na hash-table.
  - quantas passwords pode um atacante testar?
    - passwords apenas com letras e dígitos;
    - cada posição => 64 opções;
    - 64^n = 2^6n passwords de tamanho n;
    - Se n = 6, 2^36 passwords possíveis: tabela de 1TB com hashes de 160 bits (fácil de adivinhar).
  - como tornar os ataques de dicionário mais difíceis:
    - uma tabela serve para todos os utilizadores e todos os servidores;
    - Salt
      - armazenar (r, H(r||pw)) em que r se chama salt (como se verifica? Recalcular H(r||pw));
      - aleatório, idealmente para cada utilizador;
      - agora é precisa uma tabela para cada valor de salt possível;
      - já não é realista pré-computar => tempo de trabalho depois de entrar no servidor;
      - data breaches não ajudam a pré-computar tabelas atacar outros servidores.
    - Salt pode não ser suficiente:
      - hardware especializado para realizar ataques por dicionário;
      - atacantes poderosos (estados) podem construir HW dedicado.
      - medida de mitigação adicional:
        - utilizar funções de hash especialmente pesadas (tempo, recursos);
        - náo afeta os servidores significamente;
        - impacto significativo em ataques de dicionário (ex.: bcrypt).

### 4.4. Multi-factor

- Defesa em profundidade;
- Password vai ser quebrada;
- Preparar sistema para utilizações suspeitas/operações críticas;
- Utilizar fatores adicionais para confirmação e/ou deteção de problemas.

Algo que se possui: utiliza-se um dispositivo que apenas a Alice possui:
- chave, cartão de códigos, smartcard, RFID, token específico;
- utilizado frequentemente como 2º fator (além da password):
  - two-factor authentication token;
  - estritamente: prova de posse de token != prova de identidade;
  - mas reforça a evidência de identidade.

Smartcards: processador embutido em cartão de plástico:
- Alice traz cartão consigo;
- cartão pode armazenar e processar chaves criptográficas;
- processador do cartão interage com o exterior através de NFC ou contactos;
- muitas utilizações para além da identificação: cartões SIM, descodificadores de satélite, multibanco, ...;
- exemplo de autenticação: challenge-response de uma assinatura digittal (FIDO);
- o próprio smartcard frequentemente inclui outro fator de autenticação: PIN.

### 4.5. One-Time Tokens

- Ideia semelhante ao smartcard, mas com design específico para autenticação (baixo custo, alta segurança, ...);
- geralmente sem interface eletrónico:
  - não é possível inserir desafio, ou muito limitado;
  - resposta visualizada num pequeno ecrã.

Protocolo típico não interativo:
- criptografia simétrica: segredo pré-partilhado com servidor;
- periodicamente token gera MAC de hora atual;
- servidor pede password e MAC recente (2 fatores).

Vantagens:
- defesa em profundidade: conhecer password não é suficiente;
- códigos MAC são de uso único (não se copiam, roubam);
- códigos MAC futuros são imprevisíveis.

Desvantagens:
- mesmo canal: vulnerável a Man-in-the-Middle e phishing (como password);
- servidor precisa de armazenar chaves secretas (escalabilidade, ponto único de falha).

### 4.6. One-Time Passcode

Soluções mais recentes utilizam simplesmente um dispositivo existente:
- utilizar App noutro dispositivo para gerar códigos one-time;
- utilizar App noutro dispositivo para fazer challenge-response;
- aka token virtual (ex:: no telefone, tablet, relógio).

Vantagens:
- menos um dispositivo;
- chaves mais fáceis de gerir.

Desvantagens:
- menor garantia de independência entre fatores (ex.: telefone roubado).

Casos mais simples: código enviado por SMS e introduzido com password.
- utiliza-se muitas vezes para aumentar garantias de segurança:
  - transação bancária que implica transferência de valores;
  - confirmação de identidade em caso de comportamento suspeito;
  - confirmação de identidade em caso de alteração de password.
- Nestes mecanismos são também utilizados outros canais: ex.: email:
  - independência entre canais: garantia de se tratar da pessoa correta.

Conclusão:
- muito utilizado, adequado a casos de uso comuns: usabilidade vs. segurança.
- insuficiente para cenários security-critical:
  - ex.: confirmar lançamento de míssil com SMS;
  - ex.: confirmar transferência de 1M€ com SMS; ...

### 4.7. Biometria

Prova de identidade por característica intrínseca da pessoa:
- característica física: impressão digital, forma da iris, face;
- característica comportamental: caligrafia, uso do teclado;
- combinação de ambas: voz, forma de andar (gait).

Transposição para o meio digital da forma como nos identificamos em sociedade:
- vantagens: não é transferível, usabilidade ideal, pode dar garantias fortes;
- desvantagens: problemas de privacidade, direito ao esquecimento.

Processo:
- Enrollment => Registo
  - recolha de amostras;
  - extração de "templates";
- Autenticação:
  - recolha de amostra;
  - match com templates.

Autenticação remota temos várias hipóteses:
- servidor recebe amostra(s) em bruto:
  - servidor confia no computador da Alice para recolher amostras;
  - toda a informação biométrica está armazenada num servidor central.
- servidor recebe "templates"/características já processadas para match:
  - servidor confia no computador da Alice para recolher amostrar e calcular/extrair templates;
  - servidor (ainda) tem acesso a um conjunto de templates/características;
  - informação biométrica ainda armazenada no servidor.
- servidor recebe apenas resultado de match:
  - servidor confia no computador da Alice para processo de autenticação => implica alguma forma de atestação/hardware confiável;
  - servidor não tem acesso a informação biométrica.
- Ordem crescente de necessidade de confiança servidor => cliente.
- Ordem decrescente de necessidade de confiança cliente => servidor.

Desafios:
- técnicos:
  - precisão;
  - usabilidade, nomeadamente no registo;
  - estabilidade dos templates e características;
  - armazenamento de informação sensível/dados pessoais;
  - frescura:. evitar replay attacks, integridade, robustez, ...
- não técnicos:
  - aceitação pelos utilizadores.

Registo:
- muito mais elaborado do que para uma password;
- obriga a fazer um conjunto de medidas até que uma precisão adequada possa ser garantida;
- usual existirem indivíduos para os quais é difícil obter a precisão desejada.

Precisão:
- 2 eixos (pode ser definido para um indivíduo ou para todo o sistema):
  - taxa de falsos positivos (FAR): probabilidade de aceitar indevidamente;
  - taxa de falsos negativos (FRR): probabilidade de rejeitar indevidamente.
- FAR baixo => parece desejável;
  - mais difícil de atacar, não aceitamos "o atacante", mas:
  - sempre associado a FRR alto, o que prejudica a usabilidade;
  - muitas vezes calibra-se para o Equal Error Rate Point: FAR = FRR.

Segurança:
- ataques maliciosos a biometria são geralmente de 2 tipos:
  - interceção: perda de confidencialidade:
    - permite falsificação (ver spoofing), problemático porque característica biométrica não pode ser alterada nos indivíduos;
    - perda de privacidade: podemos ser reconhecidos.
  - usurpação (spoofing):
    - criação de característica falsa que engana o sensor (ex.: modelo de dedo, imagem de iris, ...);
    - pode ser simplesmente um replay attack (fácil de detetar com log);
    - maior precisão na verificação pode tornar difícil este tipo de ataques;
    - podem usar-se fatores biométricos adicionais para "liveness", como temperatura, pulso, ...;
    - pode usar-se combinação com fator não biométrico: PIN, token, ...

### 4.8. Sessões Web

Sessão: sequência de pedidos/resposta a um (ou mais) sites/aplicações:
- pode ser longa (ex.: Gmail) ou curta;
- sem o conceito de sessão: todos os pedidos exigiriam nova autenticação;
- autenticar utilizador uma vez;
- manter essa informação para os pedidos seguintes.

HTTP Auth na pré-história:
- Servidores HTTP mantinham ficheiros de (hash de) passwords em pastas;
- Resposta do servidor incluía pedido de autenticação por password;
- Browser mostra formulário;
- Browser envia login/password (em base64) em todos os pedidos subsequentes para a mesma pasta;
- Não utilizar hoje em dia;
- Log-out implica fechar o browser;
  - como gerir múltiplas contas do mesmo utilizador?
- O site não controla a interface para inserção de password:
  - essa interface é confusa/facilmente se engana utilizadores.

Tokens de sessão: servidor cria um "testemunho" que fica guardado do lado do cliente e é devolvido em todos os pedidos relacionados:
- cookie;
- informação embutida nos links clicáveis;
- campos escondidos em formulários.

Hoje em dia utiliza-se uma combinação destes mecanismos, para garantir robustez.

Tokens devem ser imprevisíveis, e invalidados (em ambos os lados) no logout.

Ataques a tokens de sessão: Session Hijacking:
- roubo de token:
  - Cross-Site Scripting (XSS);
  - eavesdropping sobre HTTP ou MitM em HTTPS;
  - falha de logout (token não invalidado no servidor);
  - mitigação: ligar token à máquina (ex.: endereço IP (pode levar a logout acidental)).
- token fixation:
  - atacante inicia sessão (low privilege) e recebe token;
  - atacante "convence" utilizador a fazer login com o mesmo token (ex.: embutido numa URL);
    - o token do atacante passa a ter privilégios do utilizador;
  - mitigação: nunca elevar privilégios/fazer login sem criar token novo.