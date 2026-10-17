# Twist — Especificação de Requisitos do Sistema

> **Projeto:** Twist  
> **Contexto:** Trabalho acadêmico da faculdade  
> **Objetivo:** Desenvolver uma plataforma social de filmes inspirada no Letterboxd, com identidade visual própria e interface inspirada no Spotify.

---

## 1. Visão geral

O Twist permitirá que usuários:

- Criem e gerenciem suas contas;
- Pesquisem filmes utilizando a API do TMDB;
- Marquem filmes como assistidos;
- Adicionem filmes à lista "Quero assistir";
- Curtam e deem deslike em filmes;
- Avaliem filmes com estrelas;
- Publiquem resenhas;
- Curtam resenhas;
- Criem playlists públicas ou privadas;
- Sigam outros usuários;
- Visualizem perfis e estatísticas;
- Descubram filmes, rankings e resenhas populares.

A aplicação será desenvolvida com frontend em React, backend em Python utilizando Django e Django REST Framework, e banco de dados PostgreSQL.

---

# 2. Requisitos funcionais

## RF01 — Cadastro, autenticação e gerenciamento de conta

### RF01.01 — Cadastro

O sistema deve permitir o cadastro de usuários com:

- E-mail;
- Nome de usuário;
- Senha;
- Nome;
- Sobrenome;
- Data de nascimento;
- Foto de perfil opcional.

### RF01.02 — Validação do cadastro

O sistema deve:

- Validar campos obrigatórios;
- Validar o formato do e-mail;
- Impedir e-mails duplicados;
- Impedir nomes de usuário duplicados;
- Validar os critérios mínimos de senha;
- Impedir datas de nascimento inválidas.

### RF01.03 — Login

O sistema deve permitir autenticação por e-mail ou nome de usuário e senha.

Após o login, a API deve emitir tokens JWT para acesso às rotas protegidas.

### RF01.04 — Renovação de token

O sistema deve permitir a renovação do token de acesso por meio de refresh token, quando essa estratégia for utilizada.

### RF01.05 — Logout

O sistema deve permitir o encerramento da sessão ativa.

O backend deve implementar uma estratégia de invalidação ou revogação de refresh tokens. A estratégia escolhida foi a revogação (blacklist) do refresh token; o access token não é invalidado ativamente, expirando naturalmente pelo tempo curto de vida configurado.

### RF01.06 — Recuperação de senha

O sistema deve permitir que o usuário solicite recuperação de senha por e-mail.

O fluxo deverá:

- Gerar token temporário;
- Definir prazo de expiração;
- Permitir uso limitado do token;
- Não revelar se determinado e-mail está cadastrado;
- Permitir a definição de uma nova senha.

### RF01.07 — Alteração de senha

O usuário autenticado deve poder alterar sua senha mediante validação adequada.

### RF01.08 — Edição do perfil

O usuário deve poder editar:

- Nome de usuário;
- E-mail;
- Data de nascimento;
- Foto de perfil;
- Descrição ou biografia.

### RF01.09 — Exclusão de conta

O sistema deve permitir que o usuário solicite a exclusão definitiva da conta, mediante confirmação.

A exclusão deverá ser integral quando tecnicamente possível, respeitando dependências, integridade referencial, obrigações de auditoria e regras de retenção de dados.

---

## RF02 — Perfil público e rede social

### RF02.01 — Visualização do perfil

Todos os perfis serão públicos e poderão apresentar:

- Foto;
- Nome de usuário;
- Biografia;
- Número de seguidores;
- Número de perfis seguidos;
- Quantidade de filmes assistidos;
- Quantidade de avaliações;
- Quantidade de resenhas;
- Playlists públicas;
- Filmes curtidos;
- Histórico público de atividades, conforme definição do produto.

### RF02.02 — Seguir usuários

O sistema deve permitir que um usuário siga outro usuário.

### RF02.03 — Deixar de seguir

O sistema deve permitir que um usuário deixe de seguir outro usuário.

### RF02.04 — Listagem de seguidores

O sistema deve permitir visualizar:

- Seguidores de um usuário;
- Perfis seguidos por um usuário.

### RF02.05 — Estatísticas do perfil

O sistema deve apresentar, no mínimo:

- Total de filmes assistidos;
- Total de resenhas publicadas;
- Total de avaliações;
- Total de seguidores;
- Total de perfis seguidos.

---

## RF03 — Catálogo de filmes via TMDB

### RF03.01 — Integração com a API do TMDB

O sistema deve utilizar a API do TMDB como fonte principal de dados do catálogo.

A integração deverá considerar:

- Chave de API armazenada em variável de ambiente;
- Tratamento de erros da API;
- Limites de requisição;
- Cache de respostas quando apropriado;
- Armazenamento do identificador externo do TMDB;
- Respeito aos termos de uso e às exigências de atribuição do TMDB.

### RF03.02 — Busca de filmes

O usuário deve poder pesquisar filmes por título utilizando a API do TMDB.

### RF03.03 — Detalhes do filme

O sistema deve apresentar:

- Título;
- Título original, quando disponível;
- Pôster;
- Imagem de fundo, quando disponível;
- Sinopse;
- Data de lançamento;
- Duração;
- Classificação indicativa, quando disponível;
- Gêneros;
- Elenco;
- Equipe técnica;
- Nota média;
- Quantidade de avaliações;
- Informações de lançamento;
- Resenhas dos usuários do Twist.

### RF03.04 — Filme não encontrado

Caso o usuário não encontre um filme na busca, o sistema deve oferecer uma opção como:

> "Não encontrou o filme? Solicite o cadastro."

O usuário poderá enviar uma solicitação contendo, quando possível:

- Título do filme;
- Ano de lançamento;
- Título original;
- Link externo ou link do TMDB, se existir;
- Motivo ou observação;
- Nome do solicitante, vinculado à conta autenticada.

### RF03.05 — Registro de solicitação de filme

As solicitações de filmes deverão ser armazenadas em uma tabela própria do banco de dados, por exemplo `MovieRequest`.

Campos sugeridos:

- `id`;
- `requested_by`;
- `title`;
- `original_title`;
- `release_year`;
- `external_url`;
- `description`;
- `status`;
- `admin_notes`;
- `created_at`;
- `updated_at`;
- `reviewed_by`;
- `reviewed_at`.

Status sugeridos:

- `PENDING` — Pendente;
- `IN_REVIEW` — Em análise;
- `APPROVED` — Aprovada;
- `REJECTED` — Rejeitada;
- `DUPLICATE` — Filme já existente ou solicitação duplicada.

### RF03.06 — Análise das solicitações

O sistema deve disponibilizar uma área administrativa ou mecanismo interno para que a equipe:

- Liste solicitações pendentes;
- Pesquise solicitações;
- Verifique se o filme já existe no TMDB;
- Confira possíveis duplicidades;
- Aprove a solicitação;
- Rejeite a solicitação;
- Registre observações;
- Atualize o status da solicitação.

### RF03.07 — Cadastro após aprovação

Após a aprovação, a equipe poderá:

- Associar a solicitação a um filme já existente no TMDB;
- Cadastrar ou sincronizar o filme;
- Registrar o identificador do TMDB;
- Marcar a solicitação como concluída.

A solicitação não deve criar automaticamente registros duplicados de filmes.

---

## RF04 — Busca global

### RF04.01 — Busca unificada

O sistema deve permitir pesquisar:

- Filmes;
- Usuários;
- Playlists públicas.

### RF04.02 — Filtros por categoria

O usuário deve poder filtrar os resultados por tipo:

- Filmes;
- Pessoas;
- Playlists públicas.

### RF04.03 — Filtros de filmes

A busca de filmes poderá incluir:

- Gênero;
- Ano de lançamento;
- Nota mínima;
- Classificação indicativa;
- Relevância;
- Título.

### RF04.04 — Paginação

Os resultados deverão ser paginados ou carregados de forma incremental.

### RF04.05 — Nenhum resultado

O sistema deve exibir uma mensagem adequada quando a busca não retornar resultados e oferecer a opção de solicitar o cadastro de um filme.

---

## RF05 — Registro de filmes

### RF05.01 — Marcar como assistido

O usuário deve poder marcar um filme como assistido.

### RF05.02 — Registro único

A primeira versão utilizará um registro por usuário e filme.

Um usuário não poderá possuir mais de um registro ativo de assistido para o mesmo filme.

### RF05.03 — Remover registro

O usuário poderá remover o registro de filme assistido.

### RF05.04 — Quero assistir

O usuário deve poder adicionar filmes à lista "Quero assistir".

### RF05.05 — Remover da lista

O usuário deve poder remover filmes da lista "Quero assistir".

### RF05.06 — Histórico

O sistema deve permitir a consulta dos filmes assistidos pelo usuário.

A data de visualização será registrada automaticamente pelo sistema no momento em que o usuário marcar o filme como assistido.

---

## RF06 — Curtidas e deslikes em filmes

### RF06.01 — Curtir filme

O usuário deve poder curtir um filme.

### RF06.02 — Remover curtida

O usuário deve poder remover sua curtida.

### RF06.03 — Dar deslike

O usuário deve poder dar deslike em um filme.

### RF06.04 — Remover deslike

O usuário deve poder remover o deslike.

### RF06.05 — Regra de exclusividade

Um usuário não poderá curtir e dar deslike no mesmo filme simultaneamente.

A implementação deverá utilizar uma regra de estado ou restrição que garanta a consistência dessas interações.

### RF06.06 — Contadores

A página do filme poderá apresentar:

- Número de curtidas;
- Número de deslikes;
- Número de usuários que marcaram como assistido.

---

## RF07 — Avaliações por estrelas

### RF07.01 — Avaliar filme

O usuário deve poder avaliar um filme com uma escala de 1 a 5 estrelas, utilizando números inteiros.

Valores permitidos:

- 1 estrela;
- 2 estrelas;
- 3 estrelas;
- 4 estrelas;
- 5 estrelas.

### RF07.02 — Alterar avaliação

O usuário poderá alterar sua avaliação.

### RF07.03 — Excluir avaliação

O usuário poderá remover sua avaliação.

### RF07.04 — Nota média

O sistema deve calcular a média das avaliações válidas de cada filme.

### RF07.05 — Regra sobre assistido

A exigência de marcar o filme como assistido antes de avaliá-lo permanece como uma decisão de produto ainda não definida.

### RF07.06 — Unicidade

Cada usuário poderá possuir uma avaliação ativa por filme.

---

## RF08 — Resenhas

### RF08.01 — Criar resenha

O usuário poderá publicar uma resenha vinculada a um filme.

A resenha deverá conter:

- Texto;
- Autor;
- Filme;
- Data de criação;
- Data de atualização;
- Indicador de spoiler, caso seja implementado.

### RF08.02 — Uma resenha ativa por filme

A regra inicial será permitir uma resenha ativa por usuário para cada filme.

### RF08.03 — Editar resenha

O autor poderá editar sua própria resenha.

### RF08.04 — Excluir resenha

O autor poderá excluir sua própria resenha.

### RF08.05 — Curtir resenha

Os usuários poderão curtir resenhas.

Cada usuário poderá curtir uma determinada resenha apenas uma vez.

### RF08.06 — Resenhas populares da semana

O sistema deve apresentar as resenhas mais populares da semana com base na quantidade de curtidas recebidas no período.

A equipe deverá definir:

- Data inicial e final do período;
- Fuso horário;
- Como tratar empates;
- Se resenhas antigas podem aparecer quando recebem novas curtidas;
- Se o ranking será calculado em tempo real ou por tarefa agendada.

### RF08.07 — Comentários em resenhas

A versão inicial não terá comentários ou respostas em resenhas.

A interação social será feita por meio de curtidas.

### RF08.08 — Dados exibidos

Cada item da lista de resenhas populares deverá exibir:

- Filme;
- Capa ou pôster;
- Nome do usuário;
- Foto do usuário;
- Texto da resenha;
- Data de publicação;
- Número de curtidas;
- Link para o filme;
- Link para o perfil do autor.

---

## RF09 — Playlists

### RF09.01 — Criar playlist

O usuário poderá criar playlists contendo:

- Nome;
- Descrição opcional;
- Foto ou capa opcional, caso implementada;
- Visibilidade.

### RF09.02 — Visibilidade

As playlists poderão ser:

- Públicas;
- Privadas.

### RF09.03 — Adicionar filmes

O usuário poderá adicionar filmes às suas playlists.

### RF09.04 — Remover filmes

O usuário poderá remover filmes de suas playlists.

### RF09.05 — Editar playlist

O proprietário poderá alterar:

- Nome;
- Descrição;
- Visibilidade;
- Capa, caso implementada.

### RF09.06 — Excluir playlist

O proprietário poderá excluir sua playlist.

### RF09.07 — Ordenação

O sistema poderá permitir a ordenação dos filmes dentro da playlist por posição manual ou ordem de inclusão.

### RF09.08 — Acesso público

Playlists públicas poderão aparecer nos resultados da busca e ser visualizadas por outros usuários.

Playlists privadas não deverão aparecer em resultados públicos.

---

## RF10 — Página inicial

### RF10.01 — Barra de busca

A Home deverá possuir uma barra de busca para filmes, usuários e playlists públicas.

### RF10.02 — Assistidos recentemente

O sistema deverá apresentar os filmes marcados como assistidos recentemente pelo usuário autenticado.

### RF10.03 — Mais assistidos

O sistema deverá apresentar filmes com maior quantidade de registros de assistidos.

O período dos rankings de filmes deverá ser definido na implementação, sem alterar a regra de que as curtidas e os registros de assistidos serão contabilizados conforme o período configurado.

### RF10.04 — Mais curtidos

O sistema deverá apresentar filmes com maior quantidade de curtidas.

O período dos rankings de filmes deverá ser definido na implementação, sem alterar a regra de que as curtidas e os registros de assistidos serão contabilizados conforme o período configurado.

### RF10.05 — Melhor nota

O sistema deverá apresentar filmes com melhores médias de avaliação.

O ranking deverá considerar uma quantidade mínima de avaliações para evitar distorções.

### RF10.06 — Resenhas populares

A Home deverá apresentar as resenhas mais curtidas da semana.

### RF10.07 — Navegação

O usuário deverá conseguir acessar:

- Página do filme;
- Perfil do autor;
- Playlist pública;
- Lista completa de resenhas;
- Resultados completos dos rankings.

---

# 3. Requisitos não funcionais

## RNF01 — Arquitetura

O backend deverá utilizar Python, Django e Django REST Framework.

## RNF02 — Banco de dados

O sistema deverá utilizar PostgreSQL.

## RNF03 — API REST

A aplicação deverá disponibilizar endpoints REST organizados por recurso e domínio de negócio.

## RNF04 — Segurança de senhas

As senhas deverão ser armazenadas usando o sistema de hash seguro do Django.

Nenhuma senha poderá ser armazenada em texto puro.

## RNF05 — Autenticação

As rotas protegidas deverão exigir autenticação JWT válida.

## RNF06 — Autorização

O sistema deverá validar se o usuário possui permissão para editar ou excluir determinado recurso.

## RNF07 — Validação

Os dados recebidos pela API deverão ser validados antes de serem persistidos.

## RNF08 — Integridade dos dados

O banco deverá utilizar:

- Chaves primárias;
- Chaves estrangeiras;
- Restrições de unicidade;
- Índices;
- Regras de integridade referencial.

## RNF09 — Desempenho

As consultas deverão utilizar paginação e índices apropriados.

O backend deverá avaliar o uso de `select_related` e `prefetch_related` para reduzir consultas desnecessárias.

## RNF10 — Integração com TMDB

A chave da API do TMDB deverá ser armazenada em variável de ambiente.

O sistema deverá tratar erros de rede, respostas inválidas, limites de requisição e indisponibilidade temporária.

## RNF11 — Cache

A aplicação deverá avaliar o uso de cache para consultas frequentes ao TMDB e rankings da Home.

## RNF12 — Testes

O projeto deverá conter testes automatizados para as funcionalidades principais, incluindo:

- Cadastro;
- Login;
- Permissões;
- Seguidores;
- Curtidas;
- Deslikes;
- Avaliações;
- Resenhas;
- Playlists;
- Solicitações de filmes.

## RNF13 — Documentação da API

A API deverá ser documentada com endpoints, métodos HTTP, parâmetros, autenticação, códigos de resposta e exemplos.

## RNF14 — Versionamento

O código deverá ser versionado utilizando Git e hospedado em um repositório compartilhado da equipe.

## RNF15 — Manutenibilidade

O projeto deverá seguir uma estrutura organizada, com separação de responsabilidades entre models, serializers, views, services e permissões, quando aplicável.

## RNF16 — Logs

O backend deverá registrar erros técnicos relevantes sem expor senhas, tokens ou dados pessoais desnecessários.

## RNF17 — Interface

A interface deverá ser responsiva e apresentar estados de carregamento, erro, sucesso e ausência de resultados.

## RNF18 — Acessibilidade

A interface deverá utilizar textos legíveis, contraste adequado, labels em formulários e navegação consistente.

---

# 4. Regras de negócio

## RN01 — Usuários

- E-mail e nome de usuário devem ser únicos.
- Um usuário não pode seguir a si mesmo.
- O usuário só pode alterar seus próprios dados.
- Todos os perfis serão públicos.
- A exclusão da conta deverá seguir a política de exclusão definida.

## RN02 — Filmes

- Cada filme deverá possuir um identificador interno.
- Filmes originados do TMDB deverão armazenar o identificador externo.
- O sistema não deverá criar filmes duplicados para o mesmo identificador externo e provedor.
- Filmes precisam existir no catálogo antes de serem associados a avaliações, resenhas, curtidas ou playlists.

## RN03 — Assistidos

- A versão inicial utilizará um registro por usuário e filme.
- O registro de assistido deverá poder ser removido.
- A quantidade de filmes assistidos no perfil deverá ser calculada sem duplicidades.

## RN04 — Curtidas e deslikes

- Um usuário não poderá curtir e dar deslike no mesmo filme simultaneamente.
- Um usuário poderá ter no máximo uma curtida por filme.
- Um usuário poderá ter no máximo um deslike por filme.
- A remoção de uma interação deverá atualizar os contadores apresentados.

## RN05 — Avaliações

- A escala será de 1 a 5 estrelas com números inteiros.
- Cada usuário poderá ter uma avaliação ativa por filme.
- A avaliação poderá ou não exigir que o filme esteja marcado como assistido; essa decisão está pendente.
- A média deverá considerar apenas avaliações válidas.

## RN06 — Resenhas

- Cada usuário poderá possuir uma resenha ativa por filme.
- Apenas o autor poderá editar ou excluir a própria resenha.
- Cada usuário poderá curtir uma resenha uma única vez.
- A versão inicial não terá comentários ou respostas.
- O ranking semanal será baseado em curtidas recebidas no período.

## RN07 — Playlists

- Toda playlist deverá possuir um proprietário.
- Somente o proprietário poderá editar ou excluir a playlist.
- Playlists privadas não aparecerão em buscas públicas.
- O sistema deverá impedir a associação de filmes inexistentes.
- O mesmo filme não poderá aparecer mais de uma vez na mesma playlist.

## RN08 — Solicitação de filmes

- Qualquer usuário autenticado poderá solicitar o cadastro de um filme não encontrado.
- As solicitações deverão possuir status.
- A equipe deverá verificar duplicidades antes de aprovar uma solicitação.
- A aprovação não deverá gerar registros duplicados.
- A equipe poderá registrar observações internas.
- O usuário poderá visualizar o status da própria solicitação, caso essa funcionalidade seja implementada.

---

# 5. Decisões de produto consolidadas

As decisões abaixo são obrigatórias para a primeira versão do Twist e devem ser consideradas na implementação, nos testes, no banco de dados e na API.

| Item | Decisão implementada |
|---|---|
| Escala de estrelas | Avaliações de 1 a 5 estrelas, utilizando somente números inteiros. |
| Avaliação exige filme assistido? | Ainda não definido. A regra deverá ser configurável/decidida antes da implementação final da validação de avaliações. |
| Registro de assistidos | Um único registro por usuário e filme. |
| Curtida e deslike em filmes | Implementar as duas reações. O usuário não poderá manter curtida e deslike simultaneamente para o mesmo filme. |
| Resenhas por filme | Uma única resenha ativa por usuário e filme. |
| Perfil | Todos os perfis serão públicos. |
| Exclusão de conta | Exclusão integral quando tecnicamente possível, respeitando integridade referencial e obrigações legais. |
| Catálogo | Utilizar API externa, com o TMDB como fonte principal e identificador externo armazenado localmente. |
| Ranking semanal | Ranking de resenhas baseado nas curtidas recebidas durante o período semanal. |
| Comentários em resenhas | Não haverá comentários ou respostas na primeira versão; haverá somente curtidas. |
| Chave primária | Inteiro autoincremento (`BigAutoField`) em todas as entidades, em vez de UUID. |
| Login | Autenticação com JWT (`djangorestframework-simplejwt`), aceitando e-mail ou nome de usuário. |
| Logout | Revogação (blacklist) do refresh token via `rest_framework_simplejwt.token_blacklist`; access token expira naturalmente. |
| Foto de perfil | Upload tratado pelo backend, que envia o arquivo para o Supabase Storage (bucket público `avatars`); aceita JPEG, PNG ou WebP, até 5MB. |
| Nome e sobrenome | Passaram a ser campos obrigatórios no cadastro (RF01.01), além dos já previstos. |
| Prefixo das rotas de autenticação | `/api/accounts/`, em vez de `/api/auth/`. |

---

# 6. Modelo de dados sugerido

## Entidades

- `User`
- `Follow`
- `Movie`
- `MovieRequest`
- `MovieLog`
- `Watchlist`
- `MovieReaction`
- `Rating`
- `Review`
- `ReviewLike`
- `Playlist`
- `PlaylistMovie`

## Observações

### User

Deverá utilizar o modelo de usuário personalizado do Django.

Campos sugeridos:

- `id` (inteiro autoincremento);
- `email`;
- `username`;
- `password`;
- `first_name`;
- `last_name`;
- `birth_date`;
- `avatar_url`;
- `bio`;
- `created_at`;
- `updated_at`.

### Movie

Campos sugeridos:

- `id`;
- `tmdb_id`;
- `title`;
- `original_title`;
- `synopsis`;
- `poster_path`;
- `backdrop_path`;
- `release_date`;
- `runtime`;
- `created_at`;
- `updated_at`.

Se futuramente houver mais de um provedor, utilizar também um campo `provider`.

### MovieRequest

Campos sugeridos:

- `id`;
- `requested_by`;
- `title`;
- `original_title`;
- `release_year`;
- `external_url`;
- `description`;
- `status`;
- `admin_notes`;
- `reviewed_by`;
- `reviewed_at`;
- `created_at`;
- `updated_at`.

### MovieReaction

Pode representar a reação do usuário a um filme.

Campos sugeridos:

- `id`;
- `user_id`;
- `movie_id`;
- `reaction_type`;
- `created_at`;
- `updated_at`.

Valores possíveis para `reaction_type`:

- `LIKE`;
- `DISLIKE`.

Deverá existir uma restrição de unicidade para `user_id` e `movie_id`.

### PlaylistMovie

Campos sugeridos:

- `id`;
- `playlist_id`;
- `movie_id`;
- `position`;
- `added_at`.

---

# 6. Endpoints REST sugeridos

## Autenticação

| Método | Endpoint | Descrição |  OK |
|---|---|---|---|
| POST | `/api/accounts/register/` | Cadastro | ✅ |
| POST | `/api/accounts/login/` | Login | ✅ |
| POST | `/api/accounts/token/refresh/` | Renovação do token | ✅ |
| POST | `/api/accounts/logout/` | Logout | ✅ |
| POST | `/api/accounts/password-reset/` | Solicitar recuperação |  |
| POST | `/api/accounts/password-reset/confirm/` | Confirmar recuperação |  |

## Usuários

| Método | Endpoint | Descrição | OK |
|---|---|---|---|
| GET | `/api/users/me/` | Usuário autenticado | ✅ |
| PATCH | `/api/users/me/` | Editar perfil | ✅ |
| DELETE | `/api/users/me/` | Solicitar exclusão | ✅ |
| GET | `/api/users/{username}/` | Perfil público |
| POST | `/api/users/{id}/follow/` | Seguir usuário | ✅ |
| DELETE | `/api/users/{id}/follow/` | Deixar de seguir | ✅ |
| GET | `/api/users/{id}/followers/` | Listar seguidores | ✅ |
| GET | `/api/users/{id}/following/` | Listar seguindo | ✅ |

## Filmes

| Método | Endpoint | Descrição |
|---|---|---|
| GET | `/api/movies/search/` | Buscar filmes no TMDB |
| GET | `/api/movies/{id}/` | Detalhes do filme |
| POST | `/api/movies/{id}/watch/` | Marcar como assistido |
| DELETE | `/api/movies/{id}/watch/` | Remover assistido |
| POST | `/api/movies/{id}/watchlist/` | Adicionar à watchlist |
| DELETE | `/api/movies/{id}/watchlist/` | Remover da watchlist |
| PUT/PATCH | `/api/movies/{id}/rating/` | Criar ou alterar avaliação |
| DELETE | `/api/movies/{id}/rating/` | Remover avaliação |
| PUT | `/api/movies/{id}/reaction/` | Curtir ou dar deslike |
| DELETE | `/api/movies/{id}/reaction/` | Remover reação |
| GET | `/api/movies/{id}/reviews/` | Listar resenhas |

## Resenhas

| Método | Endpoint | Descrição |
|---|---|---|
| POST | `/api/movies/{id}/reviews/` | Criar resenha |
| PATCH | `/api/reviews/{id}/` | Editar resenha |
| DELETE | `/api/reviews/{id}/` | Excluir resenha |
| POST | `/api/reviews/{id}/like/` | Curtir resenha |
| DELETE | `/api/reviews/{id}/like/` | Remover curtida |

## Playlists

| Método | Endpoint | Descrição |
|---|---|---|
| GET | `/api/playlists/` | Listar playlists do usuário |
| POST | `/api/playlists/` | Criar playlist |
| GET | `/api/playlists/{id}/` | Consultar playlist |
| PATCH | `/api/playlists/{id}/` | Editar playlist |
| DELETE | `/api/playlists/{id}/` | Excluir playlist |
| POST | `/api/playlists/{id}/movies/` | Adicionar filme |
| DELETE | `/api/playlists/{id}/movies/{movie_id}/` | Remover filme |

## Solicitações de filmes

| Método | Endpoint | Descrição |
|---|---|---|
| POST | `/api/movie-requests/` | Solicitar cadastro de filme |
| GET | `/api/movie-requests/mine/` | Listar solicitações do usuário |
| GET | `/api/admin/movie-requests/` | Listar solicitações para equipe |
| PATCH | `/api/admin/movie-requests/{id}/` | Atualizar status e observações |
| POST | `/api/admin/movie-requests/{id}/approve/` | Aprovar solicitação |
| POST | `/api/admin/movie-requests/{id}/reject/` | Rejeitar solicitação |

---

# 8. Segurança e permissões

- Utilizar autenticação JWT.
- Não expor a chave do TMDB no frontend.
- Armazenar segredos em variáveis de ambiente.
- Validar permissões em todas as operações de edição e exclusão.
- Aplicar throttling ou limites de requisição em endpoints sensíveis.
- Evitar exposição de informações pessoais desnecessárias.
- Proteger endpoints administrativos com permissões específicas.
- Validar uploads de imagens quanto a formato e tamanho.
- Utilizar proteção contra abuso no envio de solicitações de filmes.
- Registrar ações administrativas relevantes.

---
