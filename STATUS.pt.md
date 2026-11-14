# Situação atual de cada achado

**Última atualização:** 14 de novembro de 2026 · *In English: [STATUS.md](STATUS.md)*

O que cada situação quer dizer:

- **Corrigido**: a correção está no ar e foi conferida.
- **Correção escrita, ainda não lançada**: a mudança no código ou no texto existe, passou nos testes e foi revisada, mas os usuários ainda não a têm (ela espera a revisão de uma loja, ou está guardada para a próxima versão).
- **Aberto**: não corrigido.
- **Inativo**: trata de algo que não está ligado.

Detalhes e evidências de cada correção: [audits/2026-11-14/fixes.pt.md](audits/2026-11-14/fixes.pt.md).

## Auditoria nº 1 (14 de novembro de 2026)

### Altos

| | Achado | Situação |
|---|---|---|
| A1 | Os dados publicados permitem descobrir a qual conta pertence o nó coletor | **Corrigido** em 14 nov. 2026 para todas as novas publicações. Os arquivos anteriores continuam no histórico público do repositório de dados e não podem ser removidos. |
| A2 | Quem conseguir escrever no repositório de dados poderia executar código na máquina que hospeda o validador | **Corrigido** em 14 nov. 2026. A branch principal está protegida contra reescrita de histórico, e o coletor agora publica com uma credencial limitada a esse único repositório. |
| A3 | A página do app na App Store não tem link para os Termos de Uso | **Corrigido na página da loja** em 14 nov. 2026: a descrição na App Store agora traz os links dos Termos de Uso e da Política de Privacidade nos 13 idiomas. Ela fica visível ao público quando a Apple aprovar a versão 1.0. |
| A4 | O app do Android não tem link para a política de privacidade, e o link de Termos leva aos termos do Google | **Correção escrita, ainda não lançada.** Está na versão 4 do Android, gerada e guardada até o Google terminar de revisar a primeira versão e as páginas de termos e de suporte serem publicadas. |

### Médios

| | Achado | Situação |
|---|---|---|
| M1 | Os dados da rede não são assinados | **Corrigido do lado da publicação** em 14 nov. 2026: todo arquivo de dados agora é publicado assinado. **Do lado do app: correção escrita, ainda não lançada**: o build 8 do iPhone (enviado para a revisão da App Store em 14 nov. 2026) e a versão 4 do Android (guardada) só aceitam arquivos assinados. |
| M2 | Um arquivo de textos no servidor da Casa NUA pode substituir quase qualquer texto do app | **Correção escrita, ainda não lançada.** Os textos de privacidade, de origem dos dados e de assinatura não podem mais ser substituídos. |
| M3 | A política de privacidade não diz quem é a empresa responsável nem lista os direitos dos usuários | **Aberto.** Uma política revisada está em rascunho e espera a revisão da Casa NUA. |
| M4 | Não há termos escritos para este app; a página de suporte trata de outro app | **Aberto.** Os termos e uma seção de suporte estão em rascunho e esperam a revisão da Casa NUA. |
| M5 | O servidor de alertas instantâneos, se fosse ligado como está escrito, não corresponderia à política | **Inativo**, e **aberto**. Os registros dele não guardam mais o endereço de notificação do aparelho depois de desligar (código corrigido em 14 nov. 2026). O servidor continua desligado até também conferir que os pedidos vêm do app verdadeiro. |
| M6 | O texto da loja do Android diz "nada é enviado para nós", mas o app busca um arquivo de textos na Casa NUA | **Correção escrita, ainda não lançada.** |
| M7 | O coletor filtra só o primeiro nível do que copia | **Corrigido** em 14 nov. 2026. |

### Baixos

| Achado | Situação |
|---|---|
| Uma chave privada colada no campo do handle seria enviada ao servidor da 6529 | **Correção escrita, ainda não lançada** (os dois apps recusam e não enviam nada). |
| Textos vindos de fontes externas não são conferidos com rigor suficiente | **Correção escrita, ainda não lançada** (formato rígido de versão, conferência do número do dia). |
| O Android atualiza em segundo plano a cada 15 minutos para todos os usuários | **Aberto.** |
| Atalhos de teste escondidos continuam nas versões de loja | **Correção escrita, ainda não lançada** no Android. **Aberto** no iPhone. |
| Dados guardados antes de as assinaturas serem conferidas ainda poderiam aparecer depois de uma atualização (encontrado na revisão das correções) | **Correção escrita, ainda não lançada**: as novas versões os descartam. |
| O site carrega fontes do Google | **Aberto.** |
| Textos do site: Android listado sem "em breve", um preço só para todos, dia de exemplo sem aviso | **Aberto.** Os textos de substituição estão em rascunho. |
| Os arquivos publicados não trazem a própria data | **Aberto.** |
| O README do repositório de dados não lista todos os campos nem dá contato de segurança | **Corrigido** em 14 nov. 2026. |
| Comentários internos e arquivo de privacidade desatualizados | **Correção escrita, ainda não lançada.** |
| A conferência da assinatura é feita no aparelho | **Aberto**, aceito: afeta a receita da Casa NUA, não os usuários. |
