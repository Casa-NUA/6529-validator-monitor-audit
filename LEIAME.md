# 6529 Validator Monitor: auditorias de transparência

Relatórios públicos de auditoria do app [6529 Validator Monitor](https://app.nua.casa/#validator-6529-monitor)
(iPhone, Apple Watch, Android, Wear OS), publicados pela Casa NUA.

*In English: [README.md](README.md).*

## Comece por aqui

| | |
|---|---|
| **Situação atual de cada achado** | [STATUS.pt.md](STATUS.pt.md) |
| **Última auditoria** (nº 1, 14 de novembro de 2026) | [Relatório](audits/2026-11-14/report.pt.md) · [Correções feitas desde então](audits/2026-11-14/fixes.pt.md) · [Resultado completo dos revisores, em inglês](audits/2026-11-14/raw/) |

## O que são estas auditorias

- **Não são auditorias de terceiros.** Quem pede é a Casa NUA, que publica o app. Quem executa é o Claude, um modelo de IA da Anthropic, que também foi usado para escrever o app.
- Cada auditoria é feita por instâncias separadas e novas do modelo, que não viram como o app foi construído. Elas recebem instruções escritas e acesso só de leitura, com a orientação de serem céticas e citarem arquivo e linha para cada afirmação.
- Cada relatório informa o modelo exato, as versões exatas do código que foram lidas, as instruções completas, o resultado completo dos revisores e o que **não** foi verificado.
- Nenhum profissional de segurança humano as revisa. Trate-as como um autoexame cuidadoso e documentado, não como um certificado.

## Como este repositório funciona

- **Um relatório nunca é editado depois de publicado.** Ele diz o que era verdade na data da auditoria.
- **As correções são registradas à parte**, no arquivo `fixes` da auditoria, cada uma com a data, o que mudou e como foi conferida (correções em qualquer coisa em produção são revisadas por outra instância nova antes de entrarem no ar).
- **[STATUS.pt.md](STATUS.pt.md)** mostra sempre em que pé está cada achado hoje.
- **Cada nova auditoria ganha uma nova pasta datada.** O histórico do git deste repositório mostra que nada foi reescrito.
- Um achado que poderia ser usado contra os sistemas da Casa NUA é corrigido antes de o relatório que o descreve ser publicado.

## O que é auditado

O código do app (iPhone, Apple Watch, Android, Wear OS), o repositório público de dados
[Casa-NUA/6529-network-data](https://github.com/Casa-NUA/6529-network-data) e o seu coletor, a página do app e os
servidores por trás dela, e a política de privacidade, os termos e os textos das lojas, comparados com o que o
código faz. O código-fonte do app é privado; o repositório de dados e o coletor são públicos.

## Chaves de assinatura dos dados

Desde as correções que se seguiram à auditoria nº 1, os dados da rede são publicados assinados, e as versões do app lançadas depois dela só os aceitam se estiverem assinados por uma destas chaves (identificador = os 16 primeiros dígitos
hexadecimais do SHA-256 da chave pública em formato DER). Elas ficam listadas aqui como um segundo lugar para
conferi-las, além do próprio repositório de dados:

- `9fc874c0f34c3d51`: a chave em uso.
- `04f3b06b89f126a7`: a chave reserva.

## Contato

Dúvidas, correções e avisos de segurança: bugs@nua.casa
