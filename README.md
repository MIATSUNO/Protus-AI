# Protus AI

Assistente interativo de linha de comando em Python, com utilidades locais, gerador de HTML e integração opcional com um modelo de IA via API compatível com Chat Completions. Esta documentação descreve a versão revisada do projeto fornecida como `Protus.zip`, não necessariamente o conteúdo de outras versões.

Repositório informado: [github.com/MIATSUNO/Protus-AI](https://github.com/MIATSUNO/Protus-AI). O nome `Protus.zip` é o nome assumido para o pacote neste guia; não há neste documento um endereço de release presumido. Confira que o arquivo obtido é o pacote correto e de uma fonte confiável.

## Visão geral e requisitos

- Python 3 e apenas sua biblioteca padrão: **não é necessário instalar bibliotecas Python com pip** nesta versão. Python 3.14.4 foi usado nos testes; outras versões não foram verificadas.
- Um terminal interativo; algumas confirmações da IA exigem um terminal controlador acessível em `/dev/tty`.
- Internet para IA remota, busca na Wikipédia ou páginas externas; navegador configurado para abrir URLs e buscas do Google/YouTube. A abertura efetiva depende do dispositivo e do ambiente.
- Os comandos `ping` e `rede` dependem, respectivamente, dos utilitários do sistema `ping` e `ip`, e das permissões e opções disponíveis no ambiente. No Termux, `ping` pode ser um wrapper do utilitário do Android; `ip` pode ser obtido no pacote `iproute2`, sujeito à disponibilidade. A versão e permissões do Android podem restringi-los. O pacote Termux `inetutils` não fornece `ping`. Sem os utilitários necessários, essas funções informam erro; o restante não depende deles.
- O próprio Protus não exige root. A instalação de pacotes pela IA é específica do Termux e **recusa root**. NetHunter rootless não equivale a root e não há garantia de compatibilidade das verificações do Termux dentro de Kali ou chroot.

## Instalação no Termux a partir do ZIP

1. Instale o [Termux](https://github.com/termux/termux-app#installation) por uma fonte oficial indicada pelo projeto. Dentro dele, instale Python e `unzip`:

   ```sh
   pkg update
   pkg install python unzip
   python --version
   ```

2. Obtenha o pacote de fonte confiável e coloque-o na pasta de trabalho do Termux com o nome **`Protus.zip`**. Se já existir um diretório `Protus`, escolha antes outra pasta de trabalho para evitar sobrescritas. Em seguida:

   ```sh
   unzip -l Protus.zip
   unzip Protus.zip
   cd Protus
   python protus.py
   ```

   O ZIP examinado nesta entrega tem uma pasta de topo `Protus/`, com `protus.py` dentro. Se o download mantiver outro nome, ajuste o nome no comando `unzip` ou renomeie o arquivo antes de executar; não suponha que todos os arquivos ZIP de um repositório tenham a mesma estrutura. Se estiver na pasta Downloads compartilhada do Android, a concessão de acesso depende do dispositivo; veja a [documentação de armazenamento do Termux](https://wiki.termux.com/wiki/FAQ#How_can_I_access_device_storage_with_Termux). Não execute um ZIP de origem desconhecida.

3. Na linha `Você:`, digite `ajuda`. Para encerrar, digite `sair` ou use Ctrl+D/Ctrl+C no menu principal.

**Observação sobre o repositório:** o URL acima é o informado para o projeto, mas não foi verificado que ele já contenha esta revisão ou disponibilize `Protus.zip`. Portanto, os passos usam um ZIP já obtido, sem inventar link de download ou supor que `git clone` contenha os arquivos desta versão.

## Configurar IA com OpenRouter

A IA é opcional; comandos locais funcionam sem chave. Para usar a API remota:

1. Crie ou acesse uma conta em [openrouter.ai](https://openrouter.ai/).
2. Abra [OpenRouter → API Keys](https://openrouter.ai/settings/keys) e **crie uma chave de API** para sua conta. Você não precisa criar uma API própria. Consulte preços, créditos, limites, disponibilidade do modelo e políticas do provedor antes de usar; pode haver custos, inclusive por várias requisições no mesmo turno.
3. Na pasta `Protus`, em um shell que suporte `read -s` (como o bash do Termux), faça a leitura silenciosa e inicie o aplicativo:

   ```sh
   read -r -s -p 'Chave OpenRouter: ' OPENROUTER_API_KEY
   printf '\n'
   export OPENROUTER_API_KEY
   python protus.py
   unset OPENROUTER_API_KEY
   ```

   Cole a chave somente na solicitação silenciosa. Não digite seu valor no comando, nem o inclua em mensagens, prints, código, arquivos, histórico do shell, URL ou repositório. `unset` após sair remove a variável **daquele shell**, mas não a apaga de processos filhos já iniciados nem de outros locais; interromper antes do `unset` exige executá-lo manualmente. Processos com acesso ao seu ambiente podem ler a chave. Se for exposta, revogue-a no provedor e crie outra.

4. No prompt `Você:`, use `modo ia` para abrir uma sessão ou `ia Explique o que é Python` para uma pergunta. Não digite a chave no prompt do Protus.

Configuração padrão exata do código:

| Variável | Padrão/uso |
| --- | --- |
| `PROTUS_QWEN_ENDPOINT` | `https://openrouter.ai/api/v1/chat/completions` |
| `PROTUS_QWEN_MODEL` | `qwen/qwen3.8-27b` |
| `OPENROUTER_API_KEY` | Primeira opção de chave para endpoint não loopback. |
| `PROTUS_API_KEY` | Alternativa de chave para endpoint não loopback; única chave de ambiente selecionada automaticamente para loopback. |

O ID acima é **o padrão implementado**, não uma garantia de que o modelo esteja disponível ou aceite ferramentas. Consulte o [catálogo oficial de modelos](https://openrouter.ai/models) e configure um ID compatível e disponível caso necessário. Endpoint e modelo podem ser alterados por `export PROTUS_QWEN_ENDPOINT='https://…'` e `export PROTUS_QWEN_MODEL='id-do-modelo'`; use apenas endpoint confiável. Em endpoints não loopback, é exigida chave: `OPENROUTER_API_KEY` tem precedência sobre `PROTUS_API_KEY`, inclusive se o endpoint remoto for outro provedor. Não deixe a chave OpenRouter configurada ao mudar para outro provedor sem querer enviá-la. No uso direto de `qwen.chat`, `api_key` explícita tem precedência sobre ambas.

### Alternativa: servidor local compatível

Exige servidor local e modelo já instalados e em execução; Protus não os instala. Exemplo ilustrativo, usando o endereço e o ID efetivos do seu servidor:

```sh
unset OPENROUTER_API_KEY PROTUS_API_KEY
export PROTUS_QWEN_ENDPOINT='http://127.0.0.1:8000/v1/chat/completions'
export PROTUS_QWEN_MODEL='nome-do-modelo-local'
python protus.py
unset PROTUS_QWEN_ENDPOINT PROTUS_QWEN_MODEL
```

Para `localhost`, `127.0.0.1` e `[::1]`, em HTTP ou HTTPS, a chave OpenRouter **não** é usada automaticamente. Se o servidor local exigir autenticação, leia a chave silenciosamente em `PROTUS_API_KEY` e exporte-a; remova-a com `unset` após o uso. A API aceita HTTPS ou HTTP somente nesses hosts de loopback, sem credenciais na URL nem fragmento; HTTPS pode apontar para hosts privados e **não** impede todo destino indevido. Proxy de ambiente e redirecionamentos estão desativados no cliente da IA. Não há integração com ChatGPT implementada.

## Comandos do menu

Digite os comandos no prompt `Você:`; não são comandos do shell. O nome é reconhecido sem diferenciar maiúsculas/minúsculas; o argumento preserva seu conteúdo. `[argumento]` é opcional, conforme descrito; não digite os colchetes. Se não informado, algumas funções perguntam interativamente.

| Comando e aliases | Sintaxe e função |
| --- | --- |
| `ajuda`, `help`, `ver` | Mostra esta lista no programa. |
| `sair`, `parar`, `exit`, `quit` | Encerra o menu principal. |
| `pesquisar`, `pesquisa` | `pesquisar [termo]`: abre busca no Google; pergunta o termo se omitido. |
| `youtube`, `pesquisar youtube`, `pesquisar no youtube` | `youtube [termo]`: abre resultados de pesquisa no YouTube, não reprodução garantida. |
| `abrir` | `abrir [URL]`: abre URL HTTP ou HTTPS sem credenciais; pergunta se omitida. |
| `horas`, `hora`, `relógio`, `relogio` | Hora local do sistema, em `HH:MM`. |
| `conte uma piada`, `contar piada`, `piada` | Escolhe uma piada de uma lista fixa. |
| `criar poema`, `poema` | Escolhe um poema de uma lista fixa. |
| `criar história`, `criar historia`, `história`, `historia` | Escolhe uma história curta de uma lista fixa. |
| `recomendar livro ou filme`, `recomendar`, `recomendação`, `recomendacao` | Escolhe um par fixo de livro e filme. |
| `aprender programação`, `aprender programacao` | `aprender programação [variável\|loop\|função]`: explicação fixa; aceita também `variavel` e `funcao` e pergunta se omitido. |
| `notepad`, `nota`, `bloco de notas` | Nota com múltiplas linhas; digite `salvar` sozinho numa linha e informe o caminho do arquivo. |
| `gerar html`, `html` | Solicita nome do arquivo, título e conteúdo em uma linha e grava HTML. |
| `modo calculadora`, `calculadora`, `calcular` | `calculadora [expressão]`: calcula diretamente; sem expressão, inicia modo interativo. Nesse modo, `sair` ou `voltar` retorna ao menu. |
| `criar senha segura`, `senha` | `senha [comprimento]`: gera senha com `secrets`; padrão 16, intervalo inteiro de 12 a 128. Caracteres possíveis: letras ASCII, dígitos e `!@#$%_-+`. Não garante que cada categoria apareça. A senha é exibida na tela. |
| `busque por`, `wikipedia`, `wikipédia` | `wikipedia [termo]`: busca o primeiro resultado e até duas frases de resumo na Wikipédia em português; pergunta o termo se omitido. |
| `ping` | `ping [host]`: até quatro pacotes usando o utilitário `ping`; host/IP sem opções ou URL. A falta de resposta não prova que o host esteja offline. |
| `internet`, `rede` | Exibe `ip address show` e `ip route show`; não faz varredura e não comprova acesso à internet. |
| `status`, `estado` | Mostra hostname e endereços resolvidos localmente; não detecta dispositivos nem verifica conectividade. |
| `modo ia`, `ia` | `ia [pergunta]`: com argumento usa a sessão padrão; sem argumento abre nova sessão interativa. |
| `clear`, `limpar` | Envia sequência ANSI para limpar tela em terminais compatíveis; não limpa histórico da IA. |
| `oi`, `olá`, `ola` | Cumprimento. |
| `obrigado`, `obrigada`, `agradecimento` | Resposta de agradecimento. |
| `suporte` | Exibe informação geral sobre o projeto. |

Exemplos no menu:

```text
calculadora (2 + 3) * 4
senha 20
aprender programação loop
wikipedia Python
ping example.org
ia Explique a diferença entre TCP e UDP
```

A calculadora aceita números, parênteses, sinais unários e `+`, `-`, `*`, `/`, `//`, `%`, `**`, além de `×` e `÷`. Não executa código Python arbitrário. Há limites: 256 caracteres, 64 nós sintáticos, profundidade 16, inteiros de até 256 bits, valores reais finitos de módulo até `1e100` e expoente inteiro de -16 a 16. Operações inválidas retornam erro.

### Notas e HTML

Notas e páginas são salvas em UTF-8 no caminho informado, relativo ao diretório de execução se não for absoluto. Se o arquivo existir, `s` ou `sim` confirma a sobrescrita; qualquer outra resposta cancela. Links simbólicos existentes são recusados, e `O_NOFOLLOW` é usado na abertura quando disponível na plataforma. Isso não é garantia geral de segurança de caminhos ou permissões.

O gerador escapa o **título**, mas insere o conteúdo como **HTML literal sem sanitização**. Não use conteúdo não confiável: scripts e marcação podem executar quando o arquivo for aberto/publicado. Também é possível iniciar o gerador diretamente com `python geradorHTML.py`.

## Como funciona o modo IA

- `modo ia` ou `ia` sem argumento cria uma sessão nova; dentro dela, `voltar` ou `sair` retorna ao menu, e `limpar histórico` reinicia seu contexto. Cada nova abertura do modo cria outro histórico.
- `ia pergunta` no menu e qualquer entrada não reconhecida como comando usam uma **sessão padrão separada**, reiniciada ao abrir o Protus. Assim, uma frase livre pode gerar uma chamada remota; não presuma que entrada não reconhecida seja processada localmente.
- O histórico do agente fica na memória, não é gravado pelo Protus em arquivo; pode ser limpo em limites, falhas ou cancelamento. O provedor e outros componentes podem ter suas próprias políticas de registros e retenção.
- São aceitos até 8.000 caracteres por entrada e 8.000 caracteres por resultado de ferramenta enviado no histórico; há no máximo cinco chamadas à API e cinco chamadas de ferramentas por turno. Na quinta chamada à API não são oferecidas ferramentas; uma chamada de ferramenta inesperada nessa resposta não é executada. O histórico é reiniciado ao exceder 60 mensagens ou 60.000 caracteres serializados, ou ao alcançar 14 mensagens de assistente. Após uma falha, ações anteriores podem já ter produzido efeitos.
- A IA pode se equivocar ou interpretar mal dados externos. Avalie todas as respostas e execute atividades de segurança somente com autorização e escopo definidos.

### Ferramentas disponíveis à IA

Estas são funções internas que o modelo pode solicitar, **não comandos diretos do menu**. A disponibilidade efetiva depende do modelo e do provedor:

| Função interna e argumento | Comportamento e confirmação no agente |
| --- | --- |
| `pesquisar_documentacao` com `{"consulta":"termo"}` | Pesquisa até cinco resultados na Wikipédia em português, não em um buscador geral; até 200 caracteres. Mostra consulta e destino e pede `sim` em `/dev/tty` para cada chamada. |
| `ler_pagina_https` com `{"url":"https://exemplo.org/"}` | Lê página HTTPS pública, até 2.048 caracteres na URL; exibe destino e pede `sim` em `/dev/tty` por chamada. |
| `inspecionar_termux` com `{"consulta":"pwd"}` | Inspeção local sem confirmação; também aceita `python`, `binarios` e `uname`. `binarios` verifica no PATH `python`, `git`, `pkg`, `openssl`, `ssh`, `curl` e `nmap`; não executa varredura. |
| `instalar_pacote` com `{"pacote":"nano"}` | Só no Termux não root, com `pkg` executável e ambiente Termux válido. Pede digitação exata do comando completo mostrado em `/dev/tty` **a cada instalação**; `sim` não basta. |

A instalação usa `/data/data/com.termux/files/usr/bin/pkg install -y <pacote>` sem shell. O nome aceita até 80 caracteres ASCII minúsculos, números e certos símbolos (`+._-`), com restrições no início e fim. `-y` aceita automaticamente as perguntas do gerenciador **depois** da confirmação humana; pacotes dos repositórios configurados podem executar scripts com as permissões do usuário. O limite é 300 segundos; interrupção ou falha pode deixar alterações parciais. Verifique o estado antes de repetir. Sem TTY ou sem confirmação correta, a ação é recusada.

A leitura HTTPS da ferramenta exige HTTPS na porta 443, sem credenciais nem fragmento; rejeita respostas DNS com endereços não públicos ou mistos, conecta ao endereço validado e recusa redirecionamentos, respostas comprimidas e corpo acima de 262.144 bytes. Não usa proxy de ambiente. DNS pode demorar mais que o limite pretendido e o timeout de leitura de cabeçalhos não é um prazo absoluto contra envio lento. As restrições ajudam, mas não constituem garantia total de segurança. Resultados externos são dados não confiáveis; o projeto tenta instruir a IA a não obedecer a instruções vindas deles, sem garantia absoluta contra injeção de prompt.

**Importante:** a confirmação em `/dev/tty` acima vale para as ferramentas de IA executadas pelo agente. Os comandos do menu `pesquisar`, `youtube`, `abrir` e `wikipedia` podem abrir páginas ou consultar a Wikipédia diretamente **sem essa confirmação**. Ao usar `agent_tools.executar_chamada` diretamente em Python, a confirmação web do agente não é aplicada: cabe ao integrador fazer sua própria autorização. Não há ferramenta de shell arbitrário.

## Privacidade e segurança

A mensagem, o histórico e resultados das ferramentas podem ser enviados ao endpoint configurado e aos respectivos provedores. Consultas e URLs também são enviadas aos serviços de destino. Inspeção local pode revelar diretório, versão do Python, caminhos de binários e informações do sistema, inclusive ao provedor quando a ferramenta é usada. Não compartilhe segredos ou dados pessoais sensíveis.

O agente tenta ocultar ocorrências exatas das chaves presentes nas variáveis de ambiente e rejeitar chamadas com essas chaves em campos estruturais, mas isso não é proteção completa contra vazamento; não forneça credenciais ao modelo. O cliente omite corpos HTTP e detalhes potencialmente sensíveis de erros. Resultados e textos da IA não são prova de execução ou correção. Analise URLs, saídas, solicitações de instalação e repositórios antes de aprovar qualquer ação.

## Mensagens de erro e soluções

| Mensagem observável | Verificação sugerida |
| --- | --- |
| `Não foi possível usar a IA ou processar a resposta. Verifique a configuração, incluindo OPENROUTER_API_KEY ou PROTUS_API_KEY para provedor remoto.` | É a mensagem genérica do agente para falhas diversas: confira chave sem exibi-la, endpoint, modelo, conectividade, disponibilidade, créditos e compatibilidade com ferramentas. Ela não confirma que o problema seja a chave. |
| `Mensagem longa demais; limite de 8000 caracteres.` | Reduza a entrada. |
| `Limite de histórico atingido; repita sua mensagem com o contexto necessário.` | O contexto foi reiniciado; forneça novamente as informações necessárias. Se forem relatadas ações anteriores, verifique seus efeitos antes de repetir. |
| `Ação recusada ou TTY indisponível.` | Na ferramenta de IA, use um terminal controlador e autorize com `sim` somente após examinar o destino. |
| `Instalação como root não é permitida`, `Instalação disponível somente no Termux com pkg executável` ou `Confirmação humana em TTY ausente ou recusada` | Confira o ambiente Termux, execute sem root e examine/digite o comando exato quando desejar autorizar. |
| `Utilitário "ping" não encontrado; instale-o para usar esta função.` ou correspondente a `ip` | Instale o utilitário apropriado no ambiente; permissões e opções ainda podem variar. `Tempo limite atingido; resultado inconclusivo.` não prova falta de conexão. |
| `Não foi possível abrir o navegador.` / `Informe uma URL válida começando por http:// ou https://, sem credenciais.` | Confira URL e navegador disponível. |
| `Erro ao calcular:` / `Comprimento inválido. Use um inteiro entre 12 e 128.` | Confira sintaxe, limites e comprimento. |
| `Não foi possível salvar a nota:` / `Não foi possível salvar o HTML:` | Confira caminho, permissões e existência de links simbólicos. |

No uso direto de `qwen.chat`, `QwenError` pode informar, entre outras, `Chave de API não configurada.`, `Chave de API inválida.`, `Endpoint inválido: use HTTPS ou HTTP local, sem credenciais.`, `Redirecionamento HTTP recusado.`, `Erro HTTP do provedor (401).`, `Falha ao conectar ou ler a resposta do provedor.`, `Resposta excede max_response_bytes.` e `Resposta JSON inválida ou incompleta do provedor.` O agente principal geralmente substitui esses detalhes por sua mensagem genérica. Não compartilhe logs que contenham dados sensíveis.

## Estrutura e interfaces Python

```text
Protus/
├── protus.py           # menu, comandos, utilidades locais
├── geradorHTML.py      # geração e gravação de HTML/notas
├── qwen.py             # cliente Chat Completions
├── agent.py            # sessões, limites e orquestração de ferramentas
├── agent_tools.py      # ferramentas restritas e confirmação para instalação
├── CONFIGURACAO_IA.md  # configuração adicional
└── tests/              # testes unitários offline
```

Interfaces relevantes para quem integra o código:

- `geradorHTML.gerar_html(titulo, conteudo)` retorna a string HTML; `salvar_arquivo(caminho, conteudo)` grava em UTF-8, pede confirmação ao sobrescrever e retorna `True` ou `False` se cancelado.
- `qwen.chat(messages, tools=None, *, endpoint=DEFAULT_ENDPOINT, model=DEFAULT_MODEL, api_key=None, timeout=30, max_response_bytes=1048576, max_history_messages=100, max_turns=20)` faz uma requisição e retorna mensagem do assistente. Pode levantar `qwen.QwenError`. Preserva `tool_calls`, mas **não executa ferramentas**. O timeout é de inatividade do socket, não prazo total. Não trunca histórico automaticamente.
- `agent.Agente().responder(texto)` gerencia uma sessão e ferramentas; `limpar()` reinicia o histórico.
- `agent_tools.executar_chamada(name, args, confirm=False)` retorna dicionário com `ok` e `resultado` ou `erro`. Para instalação, `confirm=True` é necessário, mas não substitui a confirmação exata no TTY; confirmação web só é adicionada pela orquestração do agente, não pela chamada direta.

## Testes e limitações conhecidas

Na pasta do projeto:

```sh
python -B -m unittest discover -s tests -v
```

Na revisão examinada, **68 testes passaram offline com Python 3.14.4**. Os testes simulam transporte e confirmação de IA, cobrem validação, limites, segurança de chamadas, processos e HTML; não fazem chamadas reais ao provedor e não provam funcionamento no Android. Não foram validados nesta entrega Termux em aparelho, NetHunter rootless, instalação real de pacotes, navegador, conectividade externa nem a disponibilidade/preço/comportamento do modelo no OpenRouter. Não há garantia de compatibilidade com outras versões de Python ou plataformas. O ZIP examinado tem raiz `Protus/`; isso não comprova a estrutura de futuros pacotes.

## Licença

Não foi fornecida nem identificada uma licença no projeto atual ou nas cópias anteriores examinadas. Não se presume uma licença de código aberto nem se atribuem permissões específicas. Confirme os termos com o responsável antes de copiar, modificar ou redistribuir.

## Fontes oficiais e documentação

- [Repositório informado do Protus AI](https://github.com/MIATSUNO/Protus-AI).
- [Termux: instalação oficial](https://github.com/termux/termux-app#installation) e [pacotes oficiais](https://github.com/termux/termux-packages).
- [Python: biblioteca padrão](https://docs.python.org/3/library/) e [unittest/discovery](https://docs.python.org/3/library/unittest.html#test-discovery).
- [OpenRouter: início rápido](https://openrouter.ai/docs/quickstart), [criação de chaves](https://openrouter.ai/settings/keys), [Chat Completions](https://openrouter.ai/docs/api/api-reference/chat/create-a-chat-completion), [modelos](https://openrouter.ai/models) e [privacidade](https://openrouter.ai/privacy).
- [MediaWiki Action API](https://www.mediawiki.org/wiki/API:Main_page), usada pelas consultas à Wikipédia.
