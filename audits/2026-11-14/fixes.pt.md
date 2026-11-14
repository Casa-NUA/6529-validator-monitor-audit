# Correções feitas depois da auditoria nº 1

*In English: [fixes.en.md](fixes.en.md).* As entradas mais novas vêm primeiro. O [relatório](report.pt.md) em si não é editado.

Cada entrada diz o que mudou, onde, e como a mudança foi conferida. "Revisado" quer dizer que uma instância separada e nova do modelo (`claude-fable-5-2`), que não escreveu a mudança, a examinou e tentou quebrá-la.

## 14 de novembro de 2026: repositório de dados e coletor

**Achados tratados:** A1, A2, M1 (lado da publicação), M7 e o achado baixo do README.
**Onde:** repositório público [Casa-NUA/6529-network-data](https://github.com/Casa-NUA/6529-network-data), commit [`2172b2f`](https://github.com/Casa-NUA/6529-network-data/commit/2172b2f). No ar desde a publicação das 15:48 UTC.

O que mudou:

- **A1.** As duas listas das quais se podia deduzir a conta do nó coletor (`certificate.signer_weights` e `certificate.unsigned_weights`) não são mais publicadas. O README agora diz claramente que elas foram publicadas até esta data, que continuam no histórico do repositório e que a afirmação anterior estava errada.
- **A2.** O coletor não roda mais a partir da cópia do repositório. Ele roda de uma cópia fixa fora dela, coleta e assina numa pasta própria, onde nada do repositório entra, e só então copia o resultado para a cópia do repositório. Ele se recusa a iniciar de dentro de um repositório, recusa uma árvore com links, submódulos ou arquivos de controle do git, e confere se o que vai publicar é exatamente o que coletou.
- **M7.** O coletor agora confere cada valor, em todos os níveis, contra um esquema rígido (tipo e formato). O que não está no esquema é descartado; o que é novo e desconhecido é registrado no log do coletor.
- **M1, lado da publicação.** Todo arquivo de dados também é publicado como um envelope assinado em `docs/data/signed/` (ECDSA P-256, SHA-256). O nome do arquivo faz parte do que é assinado, então um arquivo assinado não pode ser apresentado como se fosse outro. Identificadores das chaves: `9fc874c0f34c3d51` (em uso) e `04f3b06b89f126a7` (reserva).
- **README.** Lista todos os campos publicados, diz quais valores são observações de um só nó, descreve o aspecto de dados pessoais, corrige as instruções de "confira você mesmo" e dá um contato.

Como foi conferido:

- **Revisado duas vezes antes de entrar no ar.** A primeira revisão encontrou três problemas sérios na primeira versão da própria correção: um link plantado no repositório poderia ter feito o assinador publicar um arquivo local, apagar arquivos fora do repositório ou assinar arquivos que o coletor não tinha escrito. A correção foi refeita (coletar e assinar fora do repositório; assinar o nome do arquivo). A segunda revisão repetiu os ataques contra a nova versão, viu que estavam fechados e pediu mais dois reforços de uma linha, que foram aplicados.
- **Testado de ponta a ponta** numa cópia isolada, com uma chave descartável: publicação normal, segunda execução sem commit desnecessário, recusa de rodar de dentro do repositório, arquivo plantado não assinado e removido, link plantado recusado, chave ausente recusada e os 11 envelopes publicados conferindo com a chave pública.
- **Conferido no ar** depois da ativação: os arquivos assinados estão sendo servidos, e os arquivos de ciclo não trazem mais as duas listas.

Mais tarde no mesmo dia: a branch principal do repositório de dados foi protegida contra reescrita de histórico e exclusão (uma regra do GitHub sem exceções), e a chave reserva de assinatura saiu da máquina coletora.

O que continua aberto aqui:

- Os arquivos anteriores continuam no histórico público do repositório.
- (Fechado mais tarde em 14 nov. 2026: o coletor agora publica com uma credencial do GitHub que só pode escrever nesse único repositório.)
- Uma assinatura válida mostra quem publicou um arquivo, não que ele é recente: quem conseguisse alterar arquivos no GitHub poderia recolocar um arquivo assinado mais antigo. Os apps conferem o dia dentro de cada arquivo e a hora da coleta.
- A chave de assinatura fica na máquina coletora. A assinatura protege contra alterações feitas no GitHub, não contra a tomada dessa máquina.

## 14 de novembro de 2026: apps (escritas e revisadas; nas mãos das lojas ou guardadas)

**Achados tratados:** A3, A4, M1 (lado do app), M2, M5 (código), M6 e vários achados baixos.
**Onde:** os repositórios privados do app: iPhone e Apple Watch na branch `claude/audit-fixes` (commit `4f2a396`, build 8 da versão 1.0), Android e Wear OS na branch `claude/audit-fixes-android` (commit `aa6c67a`, depois `48c98c0` com os achados baixos; código de versão 4).

Em que pé está cada um:

- **iPhone e Apple Watch:** o build 8 foi enviado à Apple em 14 nov. 2026. O envio anterior da versão 1.0 (build 7) foi retirado antes da revisão. A descrição na App Store já traz os links nos 13 idiomas. O build 8 está no TestFlight para os testadores internos e espera a revisão de beta da Apple para os testadores públicos. Ele foi enviado para a revisão da App Store, junto com a assinatura, em 14 nov. 2026 às 16:13 UTC. **Os usuários em geral ainda não o têm.**
- **Android e Wear OS:** a versão 4 está gerada e guardada. Ela será enviada quando o Google terminar de revisar a primeira versão. **Os usuários ainda não a têm.**

O que mudou:

- **M1, lado do app.** Os dois apps leem só os arquivos assinados e conferem a assinatura, o nome do arquivo e o dia antes de mostrar qualquer coisa. Não há volta para os arquivos sem assinatura: um arquivo ausente ou mal assinado é tratado como um download que falhou, e o último dia bom continua na tela.
- **M2.** O arquivo de textos não pode mais substituir os textos sobre privacidade, sobre a origem dos dados ou sobre a assinatura.
- **A3.** A descrição na App Store ganha os links dos Termos de Uso e da Política de Privacidade nos 13 idiomas e perde uma frase sobre alertas instantâneos, que esta versão não pode mostrar.
- **A4.** O app do Android passa a ter links para a política de privacidade e para os termos da Casa NUA, na tela de assinatura e no menu.
- **M6.** O texto da loja do Android e a tela inicial agora dizem que o app baixa da Casa NUA um pequeno arquivo com os textos da tela.
- **M5 (só o código; o servidor continua desligado).** Os registros de "alerta já enviado" do servidor de alertas instantâneos não contêm mais o endereço de notificação do aparelho, só um hash com sal dele, de modo que nada do que resta depois de desligar pode ser convertido de volta no endereço. Os pedidos têm tamanho e formato conferidos, os nomes de versão são conferidos com rigor, e um limite de frequência pode ser ligado. A documentação dele agora diz que ele não deve ser publicado enquanto não conferir que os pedidos vêm do app verdadeiro e não ler os dados assinados. O M5 continua aberto.
- **Achados baixos.** Os dois apps recusam o que tem formato de chave privada ou de frase de recuperação e não enviam nada; nomes de versão e números de dia vindos de fora são conferidos com rigor; os dados guardados por versões anteriores, de antes de as assinaturas serem conferidas, são descartados; o Android envia um identificador genérico fixo em vez de detalhes do aparelho, deixa os seus dados fora dos backups e ignora os atalhos de teste nas versões de loja; comentários desatualizados e um rascunho antigo de privacidade foram corrigidos ou removidos.

Como foi conferido:

- **Testes automáticos com arquivos assinados reais:** arquivos válidos conferem; um byte alterado, um arquivo servido com outro nome, outra chave, uma chave desconhecida e um formato antigo são todos recusados. iPhone: 33 testes passaram. Android: 24 testes passaram.
- **Revisado.** Uma instância nova revisou as mudanças de cada app e não encontrou defeito impeditivo. Ela conferiu os bytes exatos que são assinados, que todas as partes de cada app (app, relógio, widgets, atalho de voz, atualização em segundo plano) passam pelo caminho verificado, que não sobrou nenhum endereço de arquivo sem assinatura e que os arquivos assinados no ar conferem com o código enviado. Ela apontou diferenças de texto entre as duas plataformas e um gerador de textos quebrado; os dois pontos foram alinhados e corrigidos em seguida.
- **Executado contra os dados assinados no ar:** o app do iPhone num simulador e o app do Android num emulador (instalação limpa) carregaram o dia atual pelo caminho verificado. No Android, a recusa de chave privada também foi testada à mão: a mensagem apareceu e nenhum pedido foi enviado.
- Os dois apps compilam, incluindo a etapa de minificação de lançamento do Android. O build do iPhone que foi enviado foi conferido: não leva o desbloqueio de teste nem o endereço de alertas instantâneos.

Limites conhecidos, apontados pelas revisões: um arquivo antigo corretamente assinado poderia ser reapresentado (os apps conferem o dia dentro de cada arquivo e a hora da coleta, não mais que isso); os binários finais das lojas não foram inspecionados; as traduções além do inglês e do português não foram revisadas.

## 14 de novembro de 2026: site

- Uma seção "Auditado regularmente" foi acrescentada à página do app, antes da tela de download, com links para o relatório e para a página de situação deste repositório. Ela diz que a auditoria é pedida pela Casa NUA e feita por revisores de IA, não por uma empresa externa. Revisada por uma instância nova antes da publicação.

## 14 de novembro de 2026: o coletor roda a cada 5 minutos

Não é um achado da auditoria: é uma mudança feita no mesmo dia, registrada aqui porque altera texto publicado. O coletor agora roda a cada 5 minutos, em vez de 10 (commit [`748b58f`](https://github.com/Casa-NUA/6529-network-data/commit/748b58f) no repositório de dados). O README dele explica por que não menos: o GitHub entrega os arquivos por um cache que guarda cada um por 5 minutos, então um intervalo menor ganharia pouco e acrescentaria commits. A página do app e o texto de suporte foram atualizados ([policies/2026-11-14b/](../../policies/2026-11-14b/)); os textos de dentro dos apps acompanham nas próximas versões. Revisado por uma instância nova antes de entrar no ar, que corrigiu um exagero na primeira redação dessa explicação.

## 14 de novembro de 2026: achados baixos

- **Data dos arquivos publicados (corrigido, no ar).** O `network.json` agora traz `validators_unix` e `cycle_unix`: quando a lista de validadores e o arquivo do dia foram gravados pela última vez. Commit [`fe00e77`](https://github.com/Casa-NUA/6529-network-data/commit/fe00e77) no repositório de dados. Revisado antes de entrar no ar: uma instância nova passou o coletor por todos os caminhos de falha (pasta vazia, nó sem membros, busca que falha, virada do dia, arquivo anterior danificado) e comparou o resultado com o que estava publicado; só os dois campos novos foram acrescentados.
- **Leituras em segundo plano no Android (escrito, na versão 4 guardada; commits `894e1a0` e `48c98c0`).** A atualização em segundo plano a cada 15 minutos agora roda só para assinantes, no celular e no relógio. A revisão da primeira versão desta correção mostrou que ela poderia deixar um assinante pagante sem serviço: quem tivesse a assinatura reativada fora do app (um cartão recusado e cobrado depois, um plano pausado e retomado) ficaria sem alertas até abrir o app. Isso foi corrigido antes de qualquer lançamento: por 90 dias depois do fim de uma assinatura, o app pergunta ao Google Play uma vez por dia se ela voltou, sem acessar os servidores de dados. 36 testes automáticos passam.
- **Atalhos de teste no iPhone (escrito; commit `ae42f6e`).** As opções de inicialização usadas para capturas de tela só são compiladas nas versões de desenvolvimento. Isso sai no build seguinte ao build 8, que já estava com a Apple.
- **Fontes do Google no site (aberto, por decisão).** A página que hospeda a seção do app carrega as mesmas fontes por conta própria, então tirá-las da seção não mudaria nada para o visitante. A política de privacidade agora informa isso.

## 14 de novembro de 2026: política de privacidade, termos, suporte e textos da página (publicados)

**Achados tratados:** M3, M4 e o achado baixo dos textos do site.
**Onde:** https://app.nua.casa/?app=monitor#privacy, `#terms` e `#support`; os textos ficam arquivados em [policies/2026-11-14/](../../policies/2026-11-14/), e a política que eles substituem em [policies/2026-11-13/](../../policies/2026-11-13/).

O que mudou:

- **M3.** A política de privacidade agora identifica a empresa (razão social, CNPJ, endereço) e lista cada acesso que o app faz e a quem, as bases legais, os direitos dos usuários pela lei brasileira e pela europeia, as transferências internacionais, o que o site carrega e um endereço de contato próprio deste app.
- **M4.** O app passa a ter Termos de Uso próprios (independente, só de leitura, dados não oficiais de um único nó, que podem atrasar ou estar errados, a assinatura e como cancelar, reembolsos e o direito de arrependimento de 7 dias no Brasil) e uma página de suporte própria.
- **Site.** O Android aparece como "em breve" até existir o link da loja; o dia mostrado no anel é identificado como exemplo; a nota de preço diz que a loja mostra o preço do seu país.

Como foi conferido:

- **Revisado duas vezes por instâncias novas**, contra o código do app. A primeira passada, feita ao finalizar os textos, retirou afirmações do rascunho que não eram verdadeiras (um nome de assinatura que o app não usa; widget na tela bloqueada e Siri no Android, que lá não existem). A segunda achou mais quatro pontos, todos corrigidos antes da publicação: os textos diziam que o app confere a assinatura dos dados sem dizer a partir de qual versão (as versões que estavam nas mãos das lojas naquela data não conferiam todas); três afirmações sobre o Android só valiam para a versão que estava guardada; a pasta de versões anteriores para a qual a política apontava ainda não existia; e uma frase sobre o que as lojas informam à Casa NUA deixava de fora os números de downloads e de falhas.
- **Conferências automáticas (92):** inglês e português batem seção por seção; as páginas do outro app no mesmo site aparecem idênticas com e sem o código novo, em 88 combinações de endereço e idioma; nenhum valor da barra de endereços é escrito na página.
- **Conferido no ar** depois da publicação: os arquivos servidos são idênticos, byte a byte, aos revisados.

Limite conhecido, dito nos próprios textos: a conferência de assinatura e algumas proteções do Android valem a partir da versão 1.0 (build 8) no iPhone e da versão 1.0 (4) no Android. Essa frase será retirada quando as versões de teste anteriores não estiverem mais em uso.
