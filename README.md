# Youtube
Projeto SSIS para extrair Dados da API do Youtube e gravar em Banco de Dados SQL Server, para uso em análises de dados no Power BI.

Link Projeto PowerBI: https://app.powerbi.com/view?r=eyJrIjoiMGIxOTA0MjYtM2ZiMS00MGE2LWIwOTYtYTMwZTM3YjRjMDAxIiwidCI6IjFkOTUyMTVlLTI4Y2EtNDlmMi04NTE3LTU1Mjc5ODc5ZTE5YSJ9

<br />

## Pré-requisitos
Instalação do Visual Studio, SQL Server e Power BI.

<br />

## Youtube
O Youtube disponibiliza diferentes APIs para extrair dados referente a seus canais (Ex. Estatísticas de visualizações e curtidas, listas de vídeos, categorias, entre outros).
No menu "Guia" é possível consultar os primeiros passos necessários para utilização das APIs.
No menu "Referência" é possível consultar as APIs disponíveis, como acessa-las, os parâmetros necessários, a descrição dos dados retornados, e os erros de resposta. Também é possível fazer requisições de teste para verificar os retornos.
Obs. Há um limte de 10 mil requisições por dia.

Link da API: https://developers.google.com/youtube/v3/docs?hl=pt-br

Nesse projeto utilizamos as APIs:
- VideoCategories
- Channels
- PlaylistItems
- Videos

Pré-requisitos para utilização das APIs:
- 1º. Criar uma conta e fazer login.
- 2º. Cadastrar um APP e gerar uma API Key.

<br />

## Orientações sobre o Projeto SSIS
O projeto é composto por 6 pacotes SSIS.

Pacote Categorias Stage:
- Busca as Categorias dos Vídeos através da API VideoCategories, e adiciona a Tabela Categorias no Banco de dados Stage. 
- Variáveis: 
	- apiKey (API Key gerada no Site da Google, usada para acessar as APIs)
	- diretorioDados (Diretório local onde os arquivos JSON serão salvos. Deve ser alterada de acordo com seu diretório local)

Pacote Canal Stage:
- Busca os dados do Canal através da API Channels. Adiciona as informações cadastrais a Tabela Canal (ID, Título, descrição, Data de Criação, Imagem e PlaylistID), as informações estatisticas (Data, Quantidade de visualizações, comentários, inscritos e vídeos) a Tabela Canal_Estatisticas, no Banco de dados Stage. 
- Variáveis: 
	- apiKey (API Key gerada no Site da Google, usada para acessar as APIs)
	- diretorioDados (Diretório local onde os arquivos JSON serão salvos. Deve ser alterada de acordo com seu diretório local)
	- channelID (ID do Canal. Usada para buscar os dados do canal. Precisa ser atualizada manualmente com o ID do canal que deseja buscar os dados)
	- existeDia (Variável lógica. Usada para identificar se já existe dados estatísticos do dia. É atualizada automaticamente, portanto não precisa de alteração manual)

Pacote Videos Stage:
- Busca os IDs dos Vídeos do Canal através da API PlaylistItems, e adiciona a Tabela ID_Videos no Banco de dados Stage. 
- Busca os dados dos Vídeos do Canal através da API Videos. Adiciona as informações cadastrais a Tabela Videos (ID, Título, descrição, Data de Criação, Imagem, Status e Categoria), e as informações estatisticas (Data, Quantidade de visualizações, likes, dislikes, favoritos e comentários) a Tabela Videos_Estatisticas, e as Tags a tabela Videos_Tags, no Banco de dados Stage. 
- Variáveis: 
	- apiKey (API Key gerada no Site da Google, usada para acessar as APIs)
	- diretorioDados (Diretório local onde os arquivos JSON serão salvos. Deve ser alterada de acordo com seu diretório local)
	- channelID (ID do Canal. Usada para buscar os dados do canal. Precisa ser atualizada manualmente com o ID do canal que deseja buscar os dados)
	- existeDia (Variável lógica. Usada para identificar se já existe dados estatísticos do dia. É atualizada automaticamente, portanto não precisa de alteração manual)
	- ID (Número do Vídeo Inicial. Usada para buscar os dados dos Videos. Mante-la igual a 1)
	- MaxID (Número do Vídeo Final. Usada para buscar os dados dos Vídeos. É atualizada automaticamente, portanto não precisa de alteração manual)
	- nextPage (Variável lógica. Usada para identificar se há próxima página de vídeos da playlist. É atualizada automaticamente, portanto não precisa de alteração manual)
	- nextPageID (ID da próxima página de vídeos da playlist. Usada para buscar os IDs dos vídeos da playlist do canal. É atualizada automaticamente, portanto não precisa de alteração manual)
	- playlistID (ID da playlist do canal. É atualizada automaticamente, portanto não precisa de alteração manual)
	- videoID (ID do Vídeo. Usada para buscar os dados dos Vídeos. É atualizada automaticamente, portanto não precisa de alteração manual)


Pacote Categorias DW:
- Busca a Tabela de Categorias no Banco de Dados Stage e replica para o Banco de Dados DW.

Pacote Canal DW:
- Busca a Tabela de Canal e Canal_Estatisticas no Banco de Dados Stage, executa algumas tarefas para preparar os dados para análise no Power BI, e replica para o Banco de Dados DW.

Pacote Videos DW:
- Busca a Tabela de Videos, Videos_Estatisticas e Videos_Tags no Banco de Dados Stage, executa algumas tarefas para preparar os dados para análise no Power BI, e replica para o Banco de Dados DW.
