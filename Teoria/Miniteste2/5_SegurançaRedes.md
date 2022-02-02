# FSI | Resumos Miniteste2

## 5. Segurança de Redes

A camada tecnológica de software e de redes não foi criada para lidar com segurança. Estamos a trabalhar sobre uma tecnologia antiga em que comtinuamos a confiar, tem poucas alterações, e oferece do ponto de vista da segurança, muito poucas garantias.

Ideia original:
- rede simples/básica onde cad máquina/host tem um endereço IP;
- complexidade nos pontos terminais (endpoints);
- funciona como um correio: entrga de pacotes != ligações físicas computadas.
- Para communicar precisamos de regras = protocolos:
  - sintaxe: especificação: especificação da estrutura das mensagens, ordem, formato;
  - semântica: qual o significado de uma mensagem, ação a desencadear;
  - normas/especificações: todos partilham as mesmas regras.

Arquitetura em Ampulheta: interoperabilidade: "todos" devem saber "falar" uma mesma língua: IP.

Dispositivos na camada de rede: routers.

Ethernet: Link Layer mais comum:
- Mensagens = frames => EtherType permite identificar protocolo de rede (IP, ARP, ...);
- Cada nó tem um endereço MAC (Media Access Control) = 6 bytes;
- Localmente broadcast => escalabilidade usando switch;
- WiFi funciona de maneira semelhante, mas os nós são móveis.

### 5.1. Protocolo IP

- Comunicação sem "ligação":
  - Best-effort: não há garantias de entrega;
  - Não há tentaticas de recuperação;
  - Pacotes podem perder-se, ser permutados, repetidos, ...
- Pacotes podem mesmo ser fragmentados;
- Estrutura de endereços hierárquica (IPv4 = 32 bits, IPV6 = 128 bits):
  - endereço atribuído de forma estática ou dinamicamente via DHCP.

Perguntas: 
- Localmente: como sabemos os endereços MAC uns dos outros?
- Internet: como é que os routers sabem para onde enviar os pacotes?

Dentro de uma sub-rede local:
- Comunicação com base em MAC address;
- Switch otimiza distribuição de pacotes na rede local (recorda endereços MAC);
- Tabela de endereços MAC: construída com pedidos/anúncios Address Resolution (ARP):
  - pedido = meu MAC + quem tem este IP?
  - resposta = meu MAC + teu MAC + meu IP.

Os routers encaminham pacotes com base em tabelas: localmente geridas por sysadmins:
- globalmente: rounting baseado no Border Gateway Protocol:
  - Internet organizada em Autonomous Systems (AS);
  - Estrutura quasi-hiherárquica com um pequeno número de AS de topo;
  - Configuração automática de tabelas de routing (muito complexo):
    - cada router manntém tabela de routing global;
    - routers anunciam a outros routers o que podem encaminhar;
    - os encaminhamentos são propagados pela rede.

### 5.2. Protocolo TCP

Comunicação fiável entre aplicações em máquinas diferentes:
- ligações de longa duração, streams de bytes => uma em cada direção;
- conexão, desconexão explícitos;
- gestão de tráfego/congestão.
- Aplicações identificadas por um número de porta (16 bits), algumas reservadas: 80, 443, 25, 22, ...

Handshake e Comunicações TCP:
- ligações geridas com base em números de sequência;
- Dados enviados em segmentos: 1 segmento = 1 pacote IP;
- Recetor confirma (ACK) receção dos dados;
- Emissor pode retransmitir e adaptar cadência em função de timeouts.

Cabeçalho TCP:
- codifica posição no byte stream;
- confirma receção de prefixo no stream;
- handshake fica sequência inicial;
- trocas seguintes aumentam número de sequência com base em número de bytes transmitidos/recebidos.

Mais TCP:
- Flag FIN termina a ligação (implica ACK);
- Flag RST permite a cliente terminar ligação sem ACK (detetou um erro);
- UDP = Weapper do IP sem ligações/gestão do TCP.

Domain Name Service (DNS): permite mapear nomes legíveis (hierárquicos) em endereços OP (usa UDP).
- ~13 servidores DNS de raíz com visão completa:
  - nós hierarquicamente inferiores têm visão parcial;
  - resolução feita subindo na hierarquia e perguntando à autoridade correspondente (podemos ter de fazer vários pedidos);
  - caching de resultados para eficiência.

### 5.3. Segurança

Modelo CIA:
- Confidencialidade: quem pode aceder aos dados;
- Integridade: recetor recebe informação sem alterações de um dado emissor (conhecido);
- Disponibilidade: o serviço pode ser utilizado.
- Ataques potenciais em todos os níveis da rede e com diversos níveis de intervenção:
  - Físico, ligação direta ao meio de comunicação;
  - Dispositivos manipulados/configurados para realizar ataques;
  - Infraestruturas elaboradas de vigilância e controlo de comunicações.

Adversários: ao nível das comunicações, existe pouca ou nenhuma proteção:
- Atacantes que podem observar comunicações (eavesdropper);
- Atacantes que podem inserir pacotes (off path);
- Atacantes que podem observar e inserir pacotes (on path);
- Atacantes que podem controlar todas as comunicações (man in the middle).
- Para ter proteção na Internet é necessário utilizar criptografia: TLS, IPSec, ...

Ataques na camada física/lógica:
- Wiretapping: processo de escutar um canal de comunicações onde entidades terceiras estão a trocar informação:
  - O atacante liga um equipamento seu ao meio de comunicação para poder recolher informação;
  - No wiretapping ativo (ao contrário do passivo) o atacante pode mesmo injetar, modificar informação, ou fazer DoS;
  - Institucionalizado em muitos países, como forma de monitorização dos serviços de segurança.
- Eavesdropping/Packet Sniffing: forma de wiretapping em redes de comunicação, onde se recolhem e armazenam os pacotes trocados entre utilizadores legítimos de uma sub-rede:
  - Permite a um atacante observar todas as comunicações não cifradas: extrair passwords e outra informação sensível.
  - Basta ter acesso ao meio de comunicação e uma interface de rede capaz de funcionar em modo promíscuo:
    - em redes wireless desprotegidas é trivial;
    - em redes wireless protegidas, credenciais de acesso dão acesso e por vezes permitem wiretapping total;
    - em redes físicas, basta ter um ponto de acesso a uma tomada de rede.

Ferramentas: Existe hardware especializado para sniffing, com capacidade para lidar com ligações de alto débito:
- muitas destas ferramentas são criadas para auxiliar os técnicos que montam as infra-estruturas;
- existem também ferramentas em software que funcionam tão melhor quanto melhor for o equipamento disponível (ex.: WireShark).

MAC flooding: melhor sniffing:
- hoje em dia quase todas as redes são switched;
- um switch mantém uma tabela dos endereços MAC que estão em cada segmento;
- utiliza esta informação para otimizar a transmissão de pacotes;
- injetando mensagens com MACs novos na rede, estas tabelas são esvaziadas dos endereços reais;
- o switch começa então a retransmitir todos os pacotes permitindo obter um sniffing mais eficar;
- o switch passa a funcionar como um hub;
- o ataque também afeta switches vizinhos.

MAC spoofing: usurpar MAC address:
- quando o sniff nos revela o MAC address de uma máquina, podemos usurpá-lo;
- para isso, podemos configurar a nossa placa de rede para usar esse endereço;
- dessa forma, a nossa placa vai transmitir e receber informação como se fosse o alvo;
- possível porque não existe qualquer tipo de autenticação ao nível da camada lógica.

### 5.4. Segurança de IP

IP: não existe autenticação; podemos enviar pacotes a quem quisermos:
- scanning: detetar máquinas na rede, enviar mensagens e observar respostas para caracterizar alvo potencial, detetar vulnerabilidades, ...
- DoS: sobrecarregar alvo com mais mensagens do que as que consegue tratar.

ARP poisoning/spoofing: usurpar IP:
- Protocolo ARP permite traduzir endereços IP em endereços MAC;
- o facto de as máquinas e os switch fazerem cache desta informação permite usurpar IPs: inunda-se a rede com respostas ARP em que se associa o nosso MAC address aos IPs que nos interessam;
- permite fazer ataques Man in the Middle: convence-se nós legítimos que a nossa máquina possui o IP do interlocutos, processamos e reenviamos para o MAC address correto.

Hijacking de routing
- ICMP RRouter Discovery Protocol (IRDP) permite a descoberta de um router na rede;
- IRDP spoofing => anunciar um router falso: atacante faz com que as máquinas dessa subnet passem a utilizar como router uma máquina controlada pelo atacante;
- informação de routing não autenticada: ao nível do BGP é possível contaminar as tabelas e redirecionar código.

Rogue DHCP
- Protocolo DHCP:
  - Cliente faz anúncio de que precisa de um IP;
  - Servidor responde com uma proposta de IP;
  - Cliente pede ao servidor DHCP o endereço IP;
  - Servidor responde com um ACK;
  - Quando existem várioas sub-redes, um agente DHCP pode servir de intermediário para falar com o servidor DHCP.
- Rogue DHCP server pode convencer um cliente de que o router/gateway está num IP controlado pelo adversário, permitindo um ataque Man in the Middle.

DNS: nada é autenticado: é possível sermos direcionados para máquina diferente;
- imperativo usar autenticação da máquina relativamente a nome de domínio.

DNS spoofing: convencer o alvo que o servidor DNS é uma máquina controlada pelo atacante, que permite:
- fornecer IPs falsos para máquinas controladas pelo atacante;
- enganar os utilizadores apresentando-lhes serviços credíveis;
- extrair informação sensível (ex.: passwords).
- Primeiro passo (usurpar DNS) pode ser efetuado diretamente na máquina do alvo.

DNS (cache) poisoning: Kaminsky Attack:
- Bombardear servidor DNS local com respostas de resolução DNS;
- Brute-force aos parâmetros desconhecidos na pergunta DNS;
- Servidor DNS local aceita resposta que contem IP controlado pelo atacante;
- Servidor DNS local informa máquina do utilizador com IP errado.
- Impacto aumenta com o caching de endereços resolvidos.

### 5.5. Camada de Transporte (TCP)

Não existe qualquer forma de um nó saber se um pacote veio do emissor com quem pretende falar: basta que o pacote tenha o formato correto para ser aceite.
- Um ataque simples é a terminação de ligações indesejadas: enviar RST a um cliente termina a tentativa de ligação;
- Para lançar este tipo de ataques é importante controlar (parte da) infra-estrutura.

Spoofing às cegas (off path): será que conseguimos estabelecer uma sessão em nome de uma origem que não controlamos?
- Envia-se mensagem inicial SYN;
- Não conseguimos ver o número de sequência na resposta;
- Adivinhamos o número de sequência.
- Mitigação: usar números de sequência aleatórios.

TCP Session Hijacking: usurpação de uma sessão legítima por parte de um atacante; o atacante intromete-se na sessão para entregar mensagens em nome de outra entidade.
- Assumindo que existe uma troca inicial que estabelece uma autenticação, o atacante já não necessita de ter credenciais:
  - Faz hijacking da sessão já depois de o utilizador legítimo se ter autenticado;
  - Diferente de spoofing: aí o atacante inicia a sessão em nome de um utilizador legítimo e precisa das credenciais;
- Fases: Tracking, Des-sincronização, Injeção;
- Eficaz:
  - Não há contra-medidas eficazes a n ser criptografia;
  - TCP/IP intrinsecamente vulnerável e o ataque é simples de executar;
  - permite fazer bypass a processos de autenticação que não são efetuados / associados sobre/a um canal seguro: passwords, challenge-response, ...
- Permite enganar routers/proxies que estão a filtrarsources porque os pacotes aparentam vir de fontes/sessóes legítimas.
- Dificuldade: mais difícil que spoofing e exige capacidade de observar tráfego.
- Sniffing para obter informação necessária e identificar o momento de intervir;
- Dessincronizar (avançar seq number de servidor com mensagem);
- Agora o atacante sabe os números de sequência;
- Pode comunicar com o alvo entregando mensagens e vendo respostas.

UDP Hijacking: UDP não tem controlo de tráfego como no TCP:
- Atacante consegue facilmente interferir na transferência, e tentar responder primeiro que os pares;
- Utilzando técnicas MiM o ataque torna-se mais simples: não é necessário ser mais rápido na resposta.

Conclusão:
- Protocolos de rede não são secure by design: segurança é adicionada à posteriori;
- Soluções criptográficas permiter resolver o problema em parte:
  - segurança entre pontos de terminação (endpoints):
    - camada de rede (IPSec) => ligar 2 routers diretamente um ao outro;
    - camada de transporte (TLS) => todas as ligações HTTPS, TLS;
    - camada de aplicação (Signal, Whatsapp)
- Não evitam ataques à infra-estrutura, metadados, DoS: evidência de que estes ataques são explorados na prática em grande escala.

### 5.6. Defesas

Problema: Como proteger as nossas máquinas ligadas à rede?
- Maior número de máquinas expostas => maior a superfície de ataque;
- Chega desligar todos os serviços desnecessários?
  - Difícil de fazer (número de máquinas e utilizadores pode ser muito grande);
  - Insuficiente: definição pouco clara da superfície de ataque e não permite defesa em profundidade, mediação completa.

Solução típica: definir uma fronteira clara entre organização e internet:
- Firewalls:
  - locais (host-based): correm nas máquinas das aplicações/utilizadores e podem incluir filtros baseados no contexto da aplicação/utilizador;
  - na rede (network-based): intercetam comunicações de/para muitas máquinas e definem fronteira com o exterior (tudo o que está fora é hostil).
  - filtragem de pacotes:
    - olham tipicamente para os cabeçalhos dos pacotes: endereços IP, protocolo/portas TCP/UDP;
    - tentam eliminar comunicações maliciosas;
    - protegem máquinas internas de acesso externo;
    - fornecem às máquinas internas funcionalidades que exigem acesso externo.
  - Distinguir de proxies: trabalham ao nível de uma aplicação específica.
- Network Address Translation (NAT): tradução de endereços "globais" para endereços "locais".
- Proxies de aplicações específicas;
- Network Intrusion Detection Systems.
- Sempre: proteger a rede interna de ameaças externas;
- Por vezes: proteger a rede interna de máquinas locais infetadas.
- Tecnologia: filtrar/limitar tráfego de rede => como/onde se faz essa filtragem?

Políticas de Controlo de Acessos:
- uma Firewall implementa uma política de controlo de acessos: quem fala com quem e quem acede a que serviço;
- distingue tráfego recebido de tráfego enviado;
- política simples (suficiente?):
  - permitir todos os acessos de dentro para fora;
  - restringir acessos de fora para dentro:
    - permitir acesso a serviços explicitamente designados para exposição;
    - negar todos os outros acessos a serviços designados como internos.
- como tratar tráfego que não referido explicitamente nas políticas?
  - default allow => permitir todos os acessos exceto problemas específicos;
  - default deny => permitir apenas alguns acessos comuns a todos os sistemas;
    - Escolha mais conservadora, proteção por omissão;
    - Instabilidade inicial;
    - Começar por esta escolhae adicionar exceções.

Filtragem de Pacotes: cada pacote é verificado relativamente a regras => forward/drop:
- utiliza-se informação das camadas de rede e de transporte: source/destination IP, source/destination port, flags;
- exemplo de regras:
  - bloquear propagação de DNS exceto de servidores conhecidos;
  - bloquear pedidos de HTTPS exceto aqueles vindo de IPs da organização;
  - bloquear endereços internos desconhecidos.
- Filtragem sem estado: forma limitada porque não tem em conta se pacotes estão no contexto de uma ligação => tem de ser mais permissiva;
- Filtragem com estado: tenta manter registo de ligações ativas para verificar se pacote "faz sentido" nesse contexto;
- exemplo: se máquina interna estabeleceu ligação TCP, então permite pacotes externos que contêm a resposta;
- difícil de fazer para contextos elaborados: como eliminar sequências de pacotes que fazem login como root?
  - inúmeras maneiras de segmentar os comandos em pacotes.
- contornar a filtragem de pacotes: necessário para utilizador legítimo ou pode ser utilizado por adversários:
  - padrão: usar porta geralmente usada por outro serviço;
  - túneis: encapsular um protocolo dentro de outro protocolo (SSH, VPN, ...).

NAT: endereços IP não podem ser únicos globalmente (não temos suficientes).
- quase todas as redes locais usam NAT;
- mantém tabela: (endereço IP, porta) internos => porta na rede pública;
- para mensagens enviadas para o exterior:
  - verifica se tabela contém porta pública, senão cria entrada;
  - tradur (IP, porta) internos para (endereço router, porta pública);
- para mensagens recebidas pelo router do exterior:
  - verifica se tabela contém porta pública, senão faz drop;
  - traduz (endereço router, porta pública) para (IP, porta) internos.
- Vantagens:
  - reduz exposição ao exterior: apenas um endereço IP e ligações externas descartadas, a não ser que iniciadas internamente.
- Desvantagens:
  - pode perturbar o funcionamento de alguns protocolos;
  - fácil de fazer bypass para adversário ativo (slipstream):
    - site do atacante corre JavaScript em cliente na rede interna;
    - envia mensagens de dentro para fora que "baralham as tabelas NAT";
    - ex.: passa a ser possível aceder a outras portas na mesma máquina.

Proxies de aplicação:
- Ideia:
  - obrigar tráfego de uma ou mais aplicações a passar por proxy;
  - um proxy é um man-in-the-middle "do bem".
- ex.: SMTP (scan de vírus, spam), SSH (log de autenticação, inspecionar texto cifrado), HTTP/HTTPS (bloquear URLs proibidas)

### 5.7. Deteção/Prevenção de Intrusões

Já vimos:
- deteção/prevenção de malware na rede vsv. host-based (application-layer filtering);
- vimos que os anti-vírus são formas de deteção de malware host-based;
- mencionámos que é possível fazer IDS/IPS ao nível das comunicações.

Como fazer IDS/IPS ao nível das comunicações:
- ideia: monitorizar tráfego e tentar identificar um ataque (scanning, login como root, ...);
- também pode ser feito host-based ou globalmente na rede.

Network IDS/IPS:
- ideia:
  - manter tabela das ligações ativas e acompanhar o estado de cada uma;
  - procurar padrões (parciais) como /etc/passwd.
- Vantagem: não é necessário alterar máquinas individuais.
- Desvantagem:
  - exigente em termos de processamento em ligações de alto débito;
  - menos preciso (para reduzir falsos negativos => muitos falsos positivos).

Host-Based IDS/IPS:
- defesa em profundidade: fazer deteção mais agressiva em algumas máquinas;
- exemplo: servidores web:
  - é possível fazer deteção após remoção de camada criptográfica;
  - é possível procurar padrões de ataque específicos para servidores web.
- não se generaliza por causa do custo de adotar esta aproximação em todas as máquinas.

Análise de Logs: ferramenta de deteção de intrusões baratas:
- podem ser corridas offline;
- analisam a interpretação dos servidores/máquinas: tudo o q sejam técnicas de evasão (segmentação, escape de strings, ...) já foram removidas;
- apenas reativo => deteção após o ataque: não permite previnir/bloquear ataque em tempo real;
- o atacante pode modificar os próprios logs;
- podem ser utilizador para atualizar regras de firewalls/IDS (black/ban lists) => perigo de self block.

Pen-Testing:
- ideia: em vez de esperar por um ataque, simular situações de ataque e ver como o sistema reage;
- opções:
  - o mínimo: ferramentas de vulnerability scanning automáticas;
  - auditoria técnica feita por especialistas/ethical hackers.
- Vantagens:
  - proativo: falha não causa perda de valor e permite corrigir sistema;
  - otimização: permite melhorar sistema/reduzir falsos positivos.
- Desvantagens:
  - custos elevados;
  - por vezes um ataque, mesmo simulado, pode causar danos (ex.: down time).

Honey-Pots: combinação com os sistemas anteriores:
- objetivo: acelerar deteção expondo um sistema atraente para atacantes;
- entidades internas sabem que sistema não serve para nada: necessário disfarçar isto dos atacantes;
- qualquer tentativa de acesso é ataque ou erro;
- funciona melhor com ataques automatizados.