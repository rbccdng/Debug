# LAUDO TÉCNICO — Operação Resgate: Detetives de Bugs

Projeto: Central de Achados (Flutter + Firebase).
Nenhuma funcionalidade foi apagada, a autenticação foi mantida e as regras de segurança não foram desativadas.

> Observação: o arquivo `lib/firebase_options.dart` não vem no projeto (o README já avisa). Ele deve ser gerado com `flutterfire configure`. Sem ele o app não compila.

---

**Bug 1**
- Arquivo: `lib/pages/central_page.dart` (subtitle do ListTile)
- Sintoma: o app nem compilava (erro de string).
- Causa: a string com aspas simples tinha uma quebra de linha de verdade dentro dela.
- Correção: troquei a quebra de linha por `\n`.
- Teste: `flutter analyze` / `flutter run` sem erro, e a lista mostra "Por <nome>" na segunda linha.

**Bug 2**
- Arquivo: `lib/pages/central_page.dart` (onTap)
- Sintoma: erro de compilação ao abrir o detalhe.
- Causa: `DetalhePage()` foi chamado sem o parâmetro obrigatório `ocorrencia`.
- Correção: `DetalhePage(ocorrencia: o)`.
- Teste: cliquei numa ocorrência e o detalhe abriu com os dados dela.

**Bug 3**
- Arquivo: `lib/models/ocorrencia.dart`
- Sintoma: o botão "Marcar como resolvido" não aparecia para o dono / a autorização falhava.
- Causa: `autorId` estava lendo o campo `autorNome`.
- Correção: `autorId: d['autorId']`.
- Teste: na conta dona o botão aparece; na outra conta não.

**Bug 4**
- Arquivo: `lib/models/ocorrencia.dart`
- Sintoma: itens abertos apareciam como "Resolvido" e vice-versa.
- Causa: `resolvido` estava com `!` (invertido).
- Correção: removi o `!`.
- Teste: criei uma ocorrência nova e ela apareceu "Em aberto".

**Bug 5**
- Arquivo: `lib/services/central_service.dart` (`registrar`)
- Sintoma: o responsável ficava errado e o dono nunca era reconhecido.
- Causa: `autorId` era salvo com `u.email`, mas a comparação usa `u.uid`.
- Correção: `'autorId': u.uid`.
- Teste: no Firestore o campo `autorId` ficou com o uid; o dono consegue alterar o status.

**Bug 6**
- Arquivo: `lib/services/central_service.dart` (`alterarStatus`)
- Sintoma: ao clicar em "Marcar como resolvido" nada mudava.
- Causa: gravava o valor atual (`item.resolvido`) em vez do inverso.
- Correção: `!item.resolvido`.
- Teste: marquei como resolvido e o status mudou na lista; reabrir também funciona.

**Bug 7**
- Arquivo: `lib/services/central_service.dart` (`enviarPista`)
- Sintoma: a pista não aparecia na ocorrência certa (ia para um lugar só, para todos).
- Causa: usava `.doc('pistas')` em vez do id da ocorrência.
- Correção: `.doc(ocorrenciaId)`.
- Teste: enviei pista na ocorrência A e ela só aparece na A.

**Bug 8**
- Arquivo: `lib/services/central_service.dart` (`acompanhar`)
- Sintoma: os registros mais novos ficavam no fim da lista.
- Causa: `descending: false`.
- Correção: `descending: true`.
- Teste: registrei dois objetos e o último apareceu primeiro.

**Bug 9**
- Arquivo: `lib/pages/central_page.dart` (`cadastrar`)
- Sintoma: dava para registrar com título ou local vazio.
- Causa: a validação usava `&&` (só barrava se os dois estivessem vazios).
- Correção: trocado por `||`.
- Teste: tentei registrar só com título e apareceu "Informe título e local."

**Bug 10**
- Arquivo: `lib/pages/central_page.dart` (Switch)
- Sintoma: o interruptor "Somente não resolvidos" funcionava ao contrário.
- Causa: `onChanged` fazia `somenteAbertos = !value`.
- Correção: `somenteAbertos = value`.
- Teste: ligar o interruptor ativa o filtro.

**Bug 11**
- Arquivo: `lib/pages/central_page.dart` (filtro)
- Sintoma: o filtro mostrava só os resolvidos.
- Causa: `where((o) => o.resolvido)`.
- Correção: `where((o) => !o.resolvido)`.
- Teste: com o filtro ligado, só aparecem os "Em aberto".

**Bug 12**
- Arquivo: `lib/pages/detalhe_page.dart` (`enviar`)
- Sintoma: a pista nunca era enviada, ou era enviada vazia.
- Causa: a condição estava invertida (`isNotEmpty` retornava) e o texto enviado era `''` em vez de `pista.text`.
- Correção: `isEmpty` e `enviarPista(id, pista.text)`.
- Teste: escrevi uma pista, enviei, e ela apareceu na lista com o nome de quem enviou, nas duas contas.

**Extra (não conta como bug do app)**
- `test/widget_test.dart` usava `MyApp` e o pacote `bugsresolver`, que não existem. Troquei por um teste simples do modelo.
- Adicionei `firestore.rules` com regras: só logado lê/cria, só o dono altera.
