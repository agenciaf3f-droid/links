# HUB de Links da Agência F3F — Escopo

## Status atual

- [x] Escopo definido (baseado no site de referência)
- [x] Logo e paleta extraídos do Canva (`assets/f3f_mark.png`, `assets/f3f_wordmark.png`) e aplicados em `index.html`
- [x] Estrutura pública (cabeçalho, busca, seções, rodapé, painel admin) implementada e no ar via Vercel
- [ ] Links/seções reais — `SECOES` está vazio em `index.html`, aguardando conteúdo
- [ ] Supabase — `SB_URL` / `SB_KEY` / `SB_TABELA` no topo do `<script>` de `index.html` ainda em branco; painel admin fica bloqueado até isso ser preenchido

## Objetivo

Central de links interna da agência: uma página única, protegida por senha, reunindo todos os acessos, ferramentas e materiais dos projetos/clientes em um só lugar — estilo "link in bio", organizada por seções.

## Referência analisada

`sinergiaet.com.br/links-internos/` (senha testada: 123456) — hub "Central de Links" da Sinergia Empresarial & Tributária. Acessei a página (senha nativa do WordPress + Supabase por trás) e mapeei estrutura, funcionalidades e arquitetura direto do código.

**Observação:** o rodapé do site credita "Organizado por Agência F3F" — mesma agência (confirmado pelo domínio deste repo, `agenciaf3f-droid`). Dá pra reaproveitar o mesmo motor/projeto Supabase, só trocando tabela, senha e marca.

## O que o site de referência tem

### Página pública

- Cabeçalho: etiqueta (kicker) + nome (com um trecho em destaque, cor dourada) + descrição curta
- Busca de links (filtro em tempo real)
- Conteúdo organizado em seções (categorias), cada uma com sua lista de links
- Cada link tem: título, descrição opcional, URL, e marcação "destaque" (aparece com ênfase visual)
- Estados vazios tratados ("nenhum link encontrado para essa busca", "nenhum link cadastrado ainda")
- Rodapé com texto customizável + crédito de quem organiza

### Painel administrativo (embutido na mesma página, não é wp-admin)

- Protegido por uma segunda senha, diferente da senha de visualização
- Validação da senha de escrita acontece no servidor (função RPC no Supabase) — a chave pública do banco sozinha não permite gravar nada
- Aba "Adicionar": cria link (título, descrição, URL, destaque) dentro de uma seção
- Aba "Gerenciar": edita/renomeia/remove seções e links existentes
- Aba "Geral": edita os textos globais da marca (kicker, nome, trecho em destaque, descrição, rodapé)
- Botão "Salvar no site" — persiste as mudanças no banco
- Indicador de "alteração pendente" — avisa quando há mudança não salva
- "Baixar cópia de segurança" (exporta JSON) e "Descartar e recarregar" (desfaz mudanças não salvas)
- Fallback embutido no próprio código: se o banco cair, mostra um conteúdo de reserva fixo em vez de tela branca

### Arquitetura técnica identificada

- Front-end: HTML/CSS/JS puro, sem framework e sem build step — um bloco único embutido via widget de HTML do Elementor numa página WordPress
- Camada 1 (ver o hub): senha nativa de post protegido do WordPress
- Camada 2 (editar o hub): senha própria do painel, checada via função RPC no Supabase
- Dados: Supabase (Postgres), uma tabela única guardando `MARCA` (config de textos/marca) e `SECOES` (array de seções, cada uma com seus links)
- Design: tema escuro com dourado de destaque, variáveis CSS (`--sh-*`), fontes serif+sans configuráveis

## Proposta para a Agência F3F

Replicar o mesmo modelo — já validado, simples, sem servidor próprio pra manter:

1. Mesmo motor genérico (HTML/CSS/JS + Supabase), só trocando: projeto/tabela do Supabase, as duas senhas, textos de marca e paleta de cores
2. Mesmo modelo de dados: `MARCA` (kicker, nome, trecho em destaque, descrição, rodapé) + `SEÇÕES` (cada uma com uma lista de links: título, descrição, URL, destaque)
3. Duas senhas, como na referência: uma pra visualizar o hub, outra pra editar
4. Hospedagem — **em aberto**: depende de a agência ter WordPress pra embutir a página, ou preferir uma página estática avulsa (Vercel/Netlify/Cloudflare Pages). Sem WordPress, a senha de visualização (camada 1) precisa de outra solução, já que não existe o "post protegido" nativo — ex.: gate simples em JS na própria página, ou checagem via Supabase.

## Fora de escopo nesta v1

- Analytics / rastreamento de cliques nos links
- Múltiplos hubs / multi-tenant (um hub por vez)
- Login individual por usuário — segue senha compartilhada, como na referência
- Editor visual de cores/tema pelo painel — paleta fica fixa no CSS, editada direto no código

## O que falta pra fechar (usuário manda depois)

- ~~Paleta de cores e fontes~~ — resolvido via Canva (`assets/`)
- ~~Onde hospedar~~ — resolvido, Vercel conectado ao repo
- Lista real de seções e links — quais projetos/ferramentas entram e como agrupar
- Confirmar se reaproveita o projeto Supabase já usado no site de referência (mesma agência, ver observação acima) ou se cria um novo do zero

## Próximos passos

1. Usuário manda projetos/links reais
2. Decidir Supabase: reaproveitar o projeto do site de referência ou criar um novo
3. Preencher `SB_URL` / `SB_KEY` / `SB_TABELA` em `index.html`, popular `SECOES`, testar as duas senhas
