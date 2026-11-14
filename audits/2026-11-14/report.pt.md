# 6529 Validator Monitor: auditoria de transparência nº 1

**Data da auditoria:** 14 de novembro de 2026 · **Publicado:** 14 de novembro de 2026 · **Próxima auditoria:** a cada versão importante

> Este relatório descreve o que foi encontrado na data da auditoria e não é editado depois.
> Para o que foi corrigido desde então, veja [STATUS.pt.md](../../STATUS.pt.md) e [fixes.pt.md](fixes.pt.md).

Este relatório diz o que o app 6529 Validator Monitor faz com os seus dados, o que foi verificado, o que foi encontrado de errado e o que está sendo feito a respeito. Ele é escrito para quem usa o app e lista os problemas com a mesma clareza das boas notícias.

## Em resumo

- **O app não consegue mexer na sua carteira.** Ele não conecta carteira, não guarda chaves e não assina nada. Só lê dados públicos. As cinco revisões confirmaram isso no código.
- **O app não tem login, anúncios, estatísticas de uso nem rastreadores.** Não contém código de terceiros além das bibliotecas de sistema da Apple e do Google.
- **Os servidores da Casa NUA não guardam dados sobre você** nas versões revisadas. O servidor opcional de "alertas instantâneos", descrito na política de privacidade, não está ligado.
- **O que você vê é a visão de um nó.** Os dados da rede vêm de um único nó Validador 6529, mantido pela Casa NUA e republicado no GitHub. Eles não são assinados, então o app não consegue provar que são autênticos. Você pode conferir com o seu próprio nó.
- **A auditoria encontrou 4 achados altos, 7 médios e vários baixos.** Nenhum permite a alguém alcançar a carteira ou dados pessoais de um usuário. Os altos tratam do repositório de dados da própria Casa NUA e de links legais que faltam nas lojas. Estão listados abaixo, com a situação de cada um.

## Quem fez esta auditoria e o quanto ela é independente

Leia isto antes de confiar no resto.

- **Esta não é uma auditoria de terceiros.** Quem pediu foi a Casa NUA, que publica o app. Quem executou foi o Claude, um modelo de IA da Anthropic. O próprio app também foi escrito com o Claude.
- **O que "independente" quer dizer aqui:** cada revisão foi feita por uma instância separada e nova do modelo, que nunca viu a conversa em que o app foi construído. Ela recebeu apenas instruções escritas e acesso de leitura ao código. A instrução era ser cética, citar arquivo e linha para cada afirmação e não suavizar problemas.
- **Modelo:** `claude-fable-5-2` (Claude Fable 5.2) nas cinco revisões, na conferência e na redação. O nome do modelo foi lido dos registros da execução.
- **Conferência:** depois das revisões, os achados altos foram conferidos de novo diretamente no código, nos dados publicados e na App Store Connect, antes de entrarem aqui.
- **Nenhum profissional de segurança humano revisou este relatório.** Trate-o como um autoexame cuidadoso e documentado, não como um certificado.

### As cinco revisões

| Revisão | O que foi lido | Versão |
|---|---|---|
| App do iPhone e do Apple Watch | App, Watch, widgets, Siri, compras e o código do servidor de alertas | commit `03e128d` (a versão enviada à App Store como 1.0) |
| App do Android e do Wear OS | App, Wear, widgets, compras e scripts de build | commit `708767c` |
| Repositório público de dados | Código do coletor, script de publicação, todos os arquivos publicados e os 120 commits do histórico | `Casa-NUA/6529-network-data` em `f3f6f81` |
| Site e servidores | A página do app, o servidor que a entrega e que entrega o arquivo de textos do app, e o servidor de alertas | NUA-frame, commit `24d0d72` |
| Políticas e textos das lojas | Política de privacidade, termos, página de suporte, textos das lojas e tela de assinatura, comparados com o que o código faz | páginas no ar em 14 nov. 2026 |

As instruções completas dadas a cada revisão são publicadas junto com este relatório ([raw/0-instructions-to-reviewers.md](raw/0-instructions-to-reviewers.md)), assim como o resultado completo de cada uma ([raw/](raw/)), em inglês. Qualquer pessoa pode repetir a auditoria ou conferir que nada ficou de fora. Os caminhos de pastas locais foram encurtados, e um endereço de carteira foi omitido (veja o achado A1).

### O que não foi verificado

- Os binários finais distribuídos pela Apple e pelo Google. A auditoria leu o código-fonte e supõe que as versões das lojas saem dele.
- As configurações internas das contas da Casa NUA no Cloudflare, GitHub, Apple e Google (quem tem acesso, registros, autenticação em dois fatores).
- O comportamento em execução. Nada foi executado nem interceptado; as conclusões vêm da leitura do código.
- As práticas dos terceiros com quem o app fala: 6529.io, GitHub, Cloudflare, Apple e Google.
- As traduções além do inglês e do português.
- Isto não é aconselhamento jurídico. A revisão das políticas é uma lista prática de verificação, não o parecer de um advogado.

## O que o app envia, e para quem

Todas as conexões usam HTTPS. Esta tabela é a lista completa encontrada no código.

| Vai para | O que é enviado | Quando |
|---|---|---|
| GitHub (`raw.githubusercontent.com`, repositório `Casa-NUA/6529-network-data`) | Nada sobre você. A lista inteira de validadores é baixada e a busca é feita no seu aparelho. | A cada atualização e em segundo plano |
| 6529 (`6529.io/validator/releases.json`) | Nada sobre você. | A cada atualização |
| 6529 (`api.6529.io/api/profiles/…`) | O handle ou o endereço de carteira que você digitou. | Só quando você adiciona uma conta |
| Casa NUA (`…casanua.workers.dev/site/monitor-texts.json`) | Nada sobre você. O app baixa um arquivo de textos de substituição. | No máximo uma vez por hora |
| Apple ou Google | O processamento da compra, feito pelo sistema. | Quando você assina |

Como em qualquer acesso à internet, cada um desses serviços vê o seu endereço IP e informações básicas do aparelho nos cabeçalhos da conexão.

**Fica só no seu aparelho:** as contas que você acompanha, um histórico de 60 dias do que o app viu, as suas opções de alerta e a data até quando a assinatura vale. Nada secreto é guardado.

## Afirmações conferidas

| Afirmação | Resultado |
|---|---|
| Só leitura: sem carteira conectada, sem chaves, nada para assinar | **Confirmado** no iPhone, Watch, Android e Wear OS |
| Sem conta nem login | **Confirmado** |
| Sem anúncios, estatísticas ou rastreadores | **Confirmado.** Observação: a biblioteca de cobrança do Google no Android inclui diagnósticos do próprio Google |
| Selo "Dados não coletados" na App Store | **Vale para a versão 1.0.** Deixaria de ser verdade se o servidor de alertas instantâneos fosse ligado |
| Os dados da rede vêm do repositório público | **Confirmado,** mais os dois endereços da 6529 da tabela acima |
| "Só lê o que a rede já publica" | **Em parte.** O app também baixa o arquivo de textos da Casa NUA, e os dados são a visão de um nó, não uma publicação oficial da 6529. O app diz "não oficial" |
| A assinatura libera widgets, Watch, alertas, Siri e 5 contas | **Confirmado.** As compras são verificadas com as assinaturas digitais da Apple e do Google |
| Um interruptor de teste que libera tudo não pode sair numa versão de loja | **Confirmado** nos scripts de build |
| Nenhuma senha ou chave secreta nos repositórios ou no histórico | **Confirmado** |
| O repositório de dados "não identifica nada da máquina coletora" | **Não confirmado.** Veja o achado A1 |

## Achados

A gravidade considera o efeito sobre os usuários e sobre a confiabilidade do que o app mostra. "Aberto" quer dizer ainda não corrigido na data deste relatório.

### Altos

**A1. Os dados publicados permitem descobrir a qual conta pertence o nó coletor.** *Aberto.*
O nó deixa o próprio peso fora de uma lista no arquivo de cada dia. Subtraindo a lista do total chega-se a esse peso, que bate com uma única conta. O README do repositório diz que nada identifica a máquina coletora; isso está errado. Quem fica exposto é a Casa NUA, não os usuários. Os dados não podem ser retirados do histórico público.
*Previsto:* parar de publicar essas listas e dizer abertamente no README qual conta roda o coletor.

**A2. Quem conseguir escrever no repositório de dados poderia executar código na máquina que hospeda o validador.** *Aberto.*
O script de publicação baixa o repositório e depois executa o coletor a partir dessa mesma cópia, a cada 10 minutos. Um login do GitHub roubado daria, portanto, controle dessa máquina e dos dados que o app mostra.
*Previsto:* executar o coletor a partir de uma cópia fixa fora do repositório, proteger a branch principal e usar uma credencial limitada a esse único repositório.

**A3. A página do app na App Store não tem link para os Termos de Uso.** *Aberto.*
A Apple exige esse link em apps com assinatura. O link existe dentro do app, mas falta na descrição da loja nos 13 idiomas.
*Previsto:* acrescentar os links à descrição.

**A4. O app do Android não tem link para a política de privacidade, e o link de Termos leva aos termos do Google, não aos da Casa NUA.** *Aberto.*
Encontrado no código do Android revisado. A política de privacidade existe e é pública.
*Previsto:* acrescentar os links antes de o app do Android sair da fase de testes.

### Médios

**M1. Os dados da rede não são assinados.** *Aberto, por decisão de projeto por enquanto.*
O app confia no nó da Casa NUA, na máquina coletora, na conta dela no GitHub e na hospedagem do GitHub. Se qualquer um deles fosse comprometido, o app poderia mostrar validadores errados, um estado "assinou" errado ou um alerta falso. Não poderia alcançar uma carteira. Hoje os dados podem ser conferidos rodando o coletor, que é de código aberto, no seu próprio nó, ou comparando com o site independente 6529scan, cujos resultados de ciclo bateram nos sete dias comparados.
*Previsto:* assinar cada publicação e fazer o app verificar; comparar os resultados com uma segunda fonte.

**M2. Um arquivo de textos no servidor da Casa NUA pode substituir quase qualquer texto do app depois da revisão das lojas.** *Aberto.*
Ele não pode acrescentar links, executar código nem mudar preços, e o texto legal da assinatura é protegido. Mas poderia mudar o que o app diz sobre privacidade ou sobre a origem dos dados. Hoje o arquivo está vazio.
*Previsto:* proteger também os textos de privacidade e de assinatura, ou assinar o arquivo.

**M3. A política de privacidade não diz quem é a empresa responsável nem lista os direitos dos usuários.** *Aberto.*
Ela cita todos os servidores que o app acessa, o que é bom. Faltam a razão social e o endereço da empresa, a base legal do tratamento, como exercer os direitos previstos na lei brasileira (LGPD) e na europeia (GDPR) e uma declaração sobre transferência internacional. Ela existe só em inglês e português, enquanto o app sai em 12 idiomas.

**M4. Não há termos escritos para este app, e a página de suporte trata de outro app.** *Aberto.*
No iPhone o app usa a licença padrão da Apple. Em nenhum lugar está dito, em termos que o usuário aceita, que os dados podem estar atrasados ou errados e não devem ser a única conferência de que um validador assinou. O link de suporte leva à ajuda do outro app da Casa NUA.

**M5. O servidor de alertas instantâneos, se fosse ligado como está escrito, não corresponderia ao que a política diz.** *Inativo.*
O servidor não está no ar e o interruptor dele no app está escondido. Como está escrito, ele aceitaria cadastros de qualquer pessoa e guardaria o endereço de notificação do aparelho por até 60 dias depois de os alertas serem desligados, enquanto a política diz que ele é apagado na hora.
*Previsto:* corrigir as duas coisas antes de ligá-lo e atualizar o selo da App Store nesse momento.

**M6. O texto da loja do Android diz "nada é enviado para nós", mas o app busca o arquivo de textos na Casa NUA.** *Aberto.*
O acesso não leva dados pessoais, mas a frase deveria mencioná-lo.

**M7. O coletor filtra só o primeiro nível do que copia.** *Aberto.*
Detalhes novos acrescentados dentro de uma seção existente por uma versão futura do nó seriam publicados automaticamente. O achado A1 é um exemplo disso.

### Baixos

- Uma chave privada colada por engano no campo do handle seria enviada ao servidor da 6529 como se fosse um handle. O app deveria recusar qualquer coisa com formato de chave.
- Textos vindos de fontes externas (um nome de versão, um número de dia) não são conferidos com rigor suficiente. Uma fonte corrompida poderia pôr um texto estranho numa notificação ou derrubar o app. Não poderia executar código.
- No Android, o app atualiza em segundo plano a cada 15 minutos para todos os usuários, inclusive os que não têm alertas nem widgets.
- Atalhos de teste escondidos, usados para capturas de tela, continuam nas versões de loja. Eles não liberam nada.
- O site carrega fontes do Google, então o Google vê o endereço IP dos visitantes. A página em si não usa cookies nem estatísticas.
- O site lista o Android sem dizer que ainda não está disponível, mostra US$2 a visitantes cujo preço local é menor e usa um dia de exemplo sem avisar que é exemplo.
- Os arquivos publicados não trazem a própria data, então dados velhos não ficam evidentes.
- O README do repositório de dados não lista todos os campos publicados e não dá um contato de segurança.
- Alguns comentários internos e um arquivo de privacidade ainda descrevem um projeto antigo.
- A conferência da assinatura é feita no aparelho. Um telefone modificado poderia liberar os recursos pagos. Isso afeta a receita da Casa NUA, não os usuários.

## O que acontece agora

1. Cada correção é registrada em [fixes.pt.md](fixes.pt.md), com a data e como foi conferida, e a linha do achado em [STATUS.pt.md](../../STATUS.pt.md) é atualizada. Este relatório em si fica como está.
2. A auditoria é repetida a cada versão importante, numa nova pasta datada. Os relatórios anteriores continuam disponíveis, sem alteração.

Dúvidas sobre este relatório: bugs@nua.casa
