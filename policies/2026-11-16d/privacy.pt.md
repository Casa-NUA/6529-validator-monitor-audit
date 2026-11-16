# Política de Privacidade — 6529 Validator Monitor

*Última atualização: 16 de novembro de 2026*

Esta política vale para o 6529 Validator Monitor no iPhone e no Apple Watch, nos celulares Android e relógios Wear OS, e para a página do app na web. Em resumo: sem conta, sem conexão de carteira, sem publicidade, sem rastreamento. O app lê dados públicos da rede, o que você acompanha fica nos seus aparelhos, e o app não envia à Casa NUA nada sobre você, a não ser que você ligue os alertas instantâneos no iPhone (veja “Alertas”).

## Quem é o responsável

O 6529 Validator Monitor é oferecido pela CASA NUA MUSEU DE ARTE EM REDE LTDA (“Casa NUA”, “nós”), sociedade registrada no Brasil sob o CNPJ 19.035.440/0001-02, com sede na Rua Maria Paula, 78, apto. 1010, Bela Vista, São Paulo/SP, CEP 01319-000. Para a Lei Geral de Proteção de Dados (LGPD) e para o GDPR da União Europeia e do Reino Unido, a Casa NUA é a controladora do tratamento limitado descrito aqui.

Dúvidas e pedidos sobre privacidade vão para 6529monitor@nua.casa. Esse é o canal para assuntos de proteção de dados pela LGPD.

O app é independente. Não é feito pela 6529, não é afiliado a ela nem endossado por ela.

## Sem conta, sem carteira, sem rastreamento

Não há conta para criar nem nada para assinar. O app nunca se conecta a uma carteira, nunca pede chaves nem frase de recuperação e nunca pede assinatura nem transação. Não mostra publicidade, não contém software de publicidade, de medição de uso (analytics) nem de relatório de falhas, e não rastreia você entre apps ou sites.

No Android, a assinatura usa a biblioteca Play Billing, que é um software do próprio Google e tem os seus próprios diagnósticos, enviados ao Google conforme a política de privacidade do Google. Nós não os recebemos.

O que esta política diz sobre o Android vale para a versão 1.0 (4) em diante; as versões de teste anteriores enviavam o cabeçalho padrão do sistema, que inclui o modelo do aparelho e a versão do Android, e não desligavam a transferência entre aparelhos.

## O que o app envia, e para quem

Todo pedido que o app faz usa HTTPS. Esta é a lista completa:

1\. GitHub (raw.githubusercontent.com, repositório Casa-NUA/6529-network-data, pasta docs/data/signed): o app baixa os dados do dia da rede. O coletor assina os arquivos. O app confere a assinatura antes de mostrar os dados a partir da versão 1.0 (build 8) no iPhone e da versão 1.0 (4) no Android; as versões de teste anteriores não conferiam. O pedido não leva nada sobre você nem sobre as contas que você acompanha: a lista inteira de validadores é baixada e a busca é feita no seu aparelho.

2\. 6529 (6529.io/validator/releases.json): o app baixa a lista de versões do validador, para saber qual é a mais recente. O pedido não leva nada sobre você.

3\. 6529 (api.6529.io): quando você adiciona uma conta, e só nesse momento, o app envia o handle ou o endereço de carteira que você digitou ao serviço público de perfis da 6529, para encontrar o perfil público e as carteiras dessa conta. Digite apenas um handle público ou um endereço público de carteira; nunca digite uma chave privada nem uma frase de recuperação.

4\. O servidor da própria Casa NUA, um Worker da Cloudflare (nua-published-curations.casanua.workers.dev): o app baixa um arquivo com textos de tela (explicações, glossário e textos parecidos, para que possam ser corrigidos sem atualizar o app), no máximo uma vez por hora. O pedido não leva nada sobre você nem sobre as contas que você acompanha, e o registro de pedidos está desligado nesse servidor.

5\. O servidor da Casa NUA para os dados ao vivo da 6529 chain, um Worker da Cloudflare (monitor6529-live.casanua.workers.dev): o app pede a ele os blocos mais recentes da 6529 chain quando você abre ou atualiza a tela principal, e a cada poucos segundos enquanto a página Chain estiver aberta. O pedido não leva nada sobre você nem sobre as contas que você acompanha, e o servidor não guarda registro dos pedidos.

6\. Apple ou Google: quando você assina, restaura uma compra ou o app confere a sua assinatura, a compra é tratada pela App Store ou pelo Google Play no seu aparelho, na sua Conta Apple ou Conta Google.

7\. Só se você ligar os alertas instantâneos no iPhone: o servidor de alertas da Casa NUA, um Worker da Cloudflare, recebe o registro descrito em “Alertas”, e o app envia junto, a cada registro, o comprovante da Apple (DeviceCheck); para entregar cada aviso, esse servidor envia o endereço de notificação à Apple (APNs).

Como em qualquer pedido pela internet, cada um desses serviços necessariamente vê o endereço IP do seu aparelho e os cabeçalhos padrão do pedido. No iPhone e no Apple Watch, são os que o sistema acrescenta, como o nome e a versão do app e o seu idioma. No Android, os pedidos do próprio app levam um nome fixo do app, sem modelo do aparelho e sem versão do Android. GitHub, 6529, Cloudflare, Apple e Google tratam essas informações conforme as suas próprias políticas de privacidade. A Casa NUA não as recebe deles.

## O que fica nos seus aparelhos

Ficam guardados apenas no seu aparelho: as contas que você acompanha (o que você digitou, o handle e as carteiras públicas), um histórico de 60 dias do que o app viu, as suas configurações de alertas, a data até a qual a sua assinatura vale e uma cópia dos dados públicos e do arquivo de textos. Nada secreto é guardado. Nós não temos acesso a nada disso, exceto, se você ligar os alertas instantâneos no iPhone, ao registro descrito em “Alertas”, até você desligar.

O seu celular compartilha a conta que você acompanha e a data até a qual a sua assinatura vale com os widgets do próprio app e com o seu próprio relógio (o seu Apple Watch pareado, ou o seu relógio Wear OS, pela ligação entre celular e relógio do próprio Google (Google Play services)), para que mostrem a mesma informação.

Apagar o app do celular e do relógio apaga esses dados.

## Backups do aparelho

No iPhone, os dados descritos acima podem entrar nos backups do seu aparelho, inclusive no Backup do iCloud, se você usar. Esses backups são seus e são tratados pela Apple na sua Conta Apple; nós não temos acesso a eles. No Android, o backup e a transferência entre aparelhos dos dados do app estão desligados.

## Alertas

A não ser que você ligue os alertas instantâneos no iPhone, os lembretes e alertas são feitos no seu aparelho pelo próprio app, a partir dos dados públicos acima, e nada é enviado para nós para que funcionem.

Alertas instantâneos (iPhone, opcional, desligado por padrão). Se você ligar essa opção nos ajustes de alertas do app, os avisos passam a ser enviados por um servidor da Casa NUA (um Cloudflare Worker), em vez de dependerem de o iPhone acordar o app. Para isso o servidor guarda, enquanto a opção estiver ligada:

• o endereço de notificação do seu aparelho (o “token” que a Apple dá ao app para receber notificações; ele não diz quem você é);

• a carteira pública da conta de validador que você acompanha como principal;

• quais tipos de alerta estão ligados, e quais alertas o aparelho já tinha mostrado.

A cada registro (ao ligar a opção e, depois, cerca de uma vez por dia), o app também envia um comprovante da Apple (DeviceCheck) de que o pedido vem de um app da Casa NUA num aparelho Apple de verdade. O servidor confere esse comprovante com a Apple e não o guarda; guarda apenas, por um dia, um código embaralhado dele, para que o mesmo comprovante não seja usado duas vezes.

O servidor não guarda nome, e-mail, endereço IP nem registro dos pedidos. O endereço IP é usado por até um minuto pela Cloudflare apenas para limitar pedidos em excesso. Para entregar cada aviso, ele envia o endereço de notificação à Apple (serviço de notificações, APNs).

Ao desligar a opção, o registro é apagado na hora. Se o app deixar de renovar o registro (app apagado, assinatura encerrada), ele some sozinho em 7 dias. Para não repetir avisos, o servidor mantém por até 3 dias (60 dias no caso dos avisos de versão nova, de atualização urgente e de prazo) uma marca de “aviso já enviado” e, para o alerta de bloco feito, quantos blocos do dia já foram avisados e quando saiu o último aviso, ligadas a um código embaralhado que não pode ser convertido de volta no endereço do aparelho.

## Assinatura

No iPhone, a assinatura é vendida pela Apple na App Store. No Android, é vendida pelo Google no Google Play. A Apple ou o Google cuida do pagamento; nós nunca recebemos o seu nome nem dados de cartão ou de pagamento. O app pergunta à loja, no seu aparelho, se a sua conta tem a assinatura, guarda a data final no aparelho e a passa apenas para o seu próprio relógio.

A Apple e o Google nunca nos dizem quem comprou; vemos os totais de vendas. Como em todo app, eles também nos mostram números de downloads e dados de falhas e de uso que não identificam ninguém, de quem aceitou compartilhá-los e de quem testa pelo TestFlight.

## Siri, widgets, atalhos e relógios

As respostas da Siri e os Atalhos no iPhone, os widgets, os apps de relógio e as complicações e, no Android, o atalho Status (que fala a resposta com a conversão de texto em voz do próprio celular) leem os mesmos dados públicos e as mesmas informações do aparelho descritas acima. Nada disso é enviado para nós. Quando você fala com a Siri, a Apple trata o seu pedido conforme a política de privacidade da Apple.

## O que o app não usa

O app não usa sua localização, contatos, fotos, microfone nem câmera. No Android, a única permissão que ele pede a você é a de mostrar notificações.

## Por que esse tratamento é permitido (bases legais)

Em palavras simples: para mostrar o que você pede, o app precisa fazer os pedidos listados acima, e a internet não entrega uma resposta sem um endereço IP. Nós nos apoiamos em:

Execução do serviço que você pede (LGPD, art. 7º, V; GDPR, art. 6(1)(b)): baixar os dados, procurar o handle ou a carteira que você digita, entregar os textos do app e conferir a sua assinatura.

Legítimo interesse em manter o serviço seguro e funcionando (LGPD, art. 7º, IX; GDPR, art. 6(1)(f)): o nosso provedor de hospedagem, a Cloudflare, trata endereços IP por um instante para entregar o arquivo de textos e proteger o serviço contra abuso. Não guardamos registro desses pedidos.

Consentimento (LGPD, art. 7º, I; GDPR, art. 6(1)(a)) para os alertas instantâneos: você liga a opção, e pode retirar o consentimento desligando-a a qualquer momento.

Fora os alertas instantâneos, não pedimos consentimento porque não usamos os seus dados para publicidade, perfis nem medição de uso (analytics). As notificações só aparecem se você permitir nas configurações do seu aparelho.

## Por quanto tempo os dados ficam guardados

A Casa NUA não guarda nenhum dado dos usuários nos seus servidores, com uma exceção: se você ligar os alertas instantâneos no iPhone, o registro descrito acima, até você desligar. No seu aparelho, o histórico cobre 60 dias e o restante fica até você remover uma conta ou apagar o app.

Se você nos escrever, guardamos o seu e-mail só pelo tempo necessário para responder, e o apagamos quando você pedir.

## Seus direitos

Pela LGPD (art. 18), você pode pedir a confirmação de que tratamos os seus dados, o acesso a eles, a correção, a anonimização, o bloqueio ou a eliminação, a portabilidade e a informação sobre com quem são compartilhados, e pode se opor ao tratamento e revogar o consentimento, quando ele for usado. Pelo GDPR, você tem os direitos de acesso, retificação, apagamento, limitação, portabilidade e oposição.

Para exercer qualquer um deles, escreva para 6529monitor@nua.casa. Respondemos sem custo, no prazo da lei.

Na prática: a Casa NUA não tem contas e não guarda, nos seus servidores, nenhum dado sobre quem usa o app, com uma exceção: se você ligar os alertas instantâneos no iPhone, o registro descrito acima, até você desligar; nesse caso, desligar a opção apaga o registro, e você também pode nos pedir a exclusão pelo e-mail de contato. Por isso, na maioria dos pedidos, a nossa resposta será que não temos nada sobre você: os dados estão no seu aparelho, onde você pode vê-los no app e apagá-los removendo uma conta ou apagando o app. Fora esse registro, o único dado pessoal que guardamos é o e-mail que você eventualmente nos enviar, e você pode pedir que seja apagado; os números que as lojas nos mostram, descritos em “Assinatura”, não identificam ninguém. Para dados em poder do GitHub, da 6529, da Cloudflare, da Apple ou do Google, use os canais dessas empresas.

Você também pode reclamar a uma autoridade de proteção de dados: no Brasil, a Autoridade Nacional de Proteção de Dados (ANPD, gov.br/anpd); na União Europeia ou no Reino Unido, a autoridade de controle do lugar onde você mora.

## Transferências internacionais

A Casa NUA fica no Brasil. Os serviços com os quais o app fala (GitHub, 6529, Cloudflare, Apple e Google) são operados fora do Brasil. Usá-los é necessário para entregar o que você pede ao app: os pedidos listados acima não podem ser atendidos de outro modo.

## Califórnia

Não vendemos informações pessoais e não as compartilhamos para publicidade comportamental entre contextos. Não fizemos isso nos últimos 12 meses.

## Crianças

O app não é direcionado a crianças, e não guardamos conscientemente dados sobre elas. O app não tem contas, e a Casa NUA não coleta por ele dados pessoais de ninguém, de nenhuma idade, exceto, se os alertas instantâneos forem ligados no iPhone, o registro descrito em “Alertas”.

## O site

A página do app na web (a seção do 6529 Validator Monitor em app.nua.casa, e também esta política, os Termos e a página de Suporte) não usa ferramentas de medição de uso (analytics) nem publicidade. Ela carrega as fontes do Google Fonts; por isso o Google vê o endereço IP e os cabeçalhos padrão de quem visita. Os textos são carregados do Worker da Casa NUA na Cloudflare, o mesmo servidor que o app usa, com o registro de pedidos desligado. O site grava um cookie da Cloudflare de curta duração (__cf_bm, cerca de 30 minutos), que a Cloudflare usa para distinguir pessoas de robôs. A página lembra o idioma que você escolheu no armazenamento local do seu navegador; isso nunca sai do navegador. O site é feito com o Carrd e servido pela Cloudflare, que tratam os pedidos conforme as suas próprias políticas.

O mesmo site também tem páginas do NUA, o outro app da Casa NUA. Essas páginas seguem a política de privacidade do próprio NUA.

## Idiomas

Esta política é publicada em inglês e em português. Para usuários no Brasil, prevalece o texto em português; nos demais lugares, prevalece o texto em inglês, na medida em que a lei permitir.

## Mudanças

Quando esta política mudar, esta página muda junto e a data no topo é atualizada. As versões anteriores ficam guardadas no repositório público da auditoria, na pasta “policies/”:

https://github.com/Casa-NUA/6529-validator-monitor-audit

Se uma mudança acrescentar um novo uso de dados, a política é atualizada antes de o recurso ser ligado.

## Contato

Casa NUA: CASA NUA MUSEU DE ARTE EM REDE LTDA, CNPJ 19.035.440/0001-02, Rua Maria Paula, 78, apto. 1010, Bela Vista, São Paulo/SP, CEP 01319-000, Brasil.

Dúvidas sobre esta política e pedidos sobre privacidade: 6529monitor@nua.casa

---

Publicado em https://app.nua.casa/?app=monitor#privacy · arquivado em 16 de novembro de 2026.
