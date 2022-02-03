# Modelo 2021

O teste intercalar e o exame final serão concebidos para 1:30 horas max (1 hora deverá ser suficiente).<br>
Terão 4/5 grupos, cada um com 4/5 perguntas, das quais são exemplo as perguntas que se seguem.<br>
A cotação de cada grupo será indicada no enunciado; cada pergunta dentro do grupo terá igual peso.

## Grupo 1 - Conceitos e Terminologia de Segurança Informática (20%)

### 1.1. Indique duas motivações discutidas em aula para um atacante comprometer a máquina de um utilizador comum.
- #1: **Roubar credenciais**: roubar passwords bancárias, empresariais, para jogos.
- #2: **Ransomware**: malware que bloqueia o sistema e cobra um resgate em criptomoedas para o seu desbloqueio.
- #3: Para **utilizar o processador** -> minerar bitcoin.
- #4: Para **usurpar o endereço de rede** e parecer um utilizador normal (Spam, Denial of Service, Clicks); serviços vendidos na internet e para isso é necessário controlar um conjunto de máquinas.

### 1.2. Identifique três tipos de atores que usualmente se consideram em análise de segurança.
- #1: **Pessoas, organizações, empresas, máquinas**, onde pressupomos que são de confiança.
- #2: **Adversários/Atacantes**, com a intenção de utilizar o sistema de forma indevida ou impossibilitar a sua utilização.
- #3: **Intermediários**, que facilitam a interação entre dois atores, sendo que ambos depositam confiança no intermediário, mas não confiam um no outro.

### 1.3. Atribua um número:<br>(1: Ameaça),<br>(2: Vulnerabilidade)<br>(3: Exploit)<br> a cada um dos seguintes exemplos.
| | |
|-|-|
| update de segurança feito anualmente                    | 2: Vulnerabilidade (propriedade do sistema) |
| terramoto                                               | 1: Ameaça |
| string de formatação que causa crash                    | 3: Exploit |
| antigo colaborador em conflito                          | 1: Ameaça |
| utilização de strcpy sem teste de tamanho               | 2: Vulnerabilidade |
| duplo free                                              | 2: Vulnerabilidade                         |
| ficheiro bitmap que causa reescrita de endereço na heap | 3: Exploit |
| data breach                                             | 1: Ameaça (evento, semelhante a uma catástrofe natural) |

### 1.4. Recorde o conceito de confiabilidade e preencha as células do lado direito da tabela, explicando como se relacionam os conceitos listados na célula correspondente do lado esquerdo.
| | |
|-|-|
| ator,<br>ativo,<br>objetivo de segurança | Objetivo de segurança é proteger os ativos dos atores (o que queremos proteger e de quem?). |
| modelo de confiança,<br>modelo de ameaças,<br>política de segurança | A confiabilidade define-se pelo modelo de confiança (em que componentes/atores confiamos e para fazer o quê) e pelo modelo de ameaças (ativos e objetivos de segurança). Políticas de segurança determinam conjuntos de processos/mecanismos que devem ser seguidos para garantir segurança no modelo de ameaças, tendo por pressuposto garantido o modelo de confiança. |

## Grupo 2 - Controlo

### 2.1. Recorde o que estudou sobre a disposição clássica da stack gerida por uma função f chamada por uma função g e preencha o diagrama com os seguintes termos:<br>(1: variáveis locais de f),<br>(2: frame pointer de g),<br>(3: parâmetros passados por g),<br>(4: endereço de retorno para g).

| | Stack |
|-|-|
| Endereço mais elevado | 3: parâmetros passados por g (colocado por g) |
|                       | 4: endereço de retorno para g (colocado por g na chamada) |
|                       | 2: frame pointer de g (guardado por f quando começa a executar) |
| Endereço mais baixo   | 1: variáveis locais de f (criado por f quando começa a executar) |

### 2.2. A seguinte informação é verdadeira ou falsa? Justifique a sua resposta.<br>Um buffer overflow na stack de apenas um byte não permite a um atacante executar código arbitrário, uma vez que não permite alterar o endereço de retorno da função.
Falsa.<br>
Um overflow de 1 byte permite apenas (no pior caso) escrever por cima do byte menos significativo do frame pointer. Não é nada óbvio como é que alterando o frame pointer podemos desencadear uma tomada de controlo e, de facto, as condições que o permitem são improváveis mas não inéditas.<br>
Contudo, há casos documentados em que o "off by 1" permite alterar o frame pointer, o que provoca uma alteração da disposição da stack da função para a qual retornamos, sendo a tomada de controlo uma consequência possível dessa alteração.