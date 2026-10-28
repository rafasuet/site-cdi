# Site do CDI — FT-Unicamp

![React](https://img.shields.io/badge/React_19-20232A?logo=react&logoColor=61DAFB)
![Vite](https://img.shields.io/badge/Vite-646CFF?logo=vite&logoColor=white)
![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS_4-0F172A?logo=tailwindcss&logoColor=38BDF8)
![React Router](https://img.shields.io/badge/React_Router-CA4245?logo=reactrouter&logoColor=white)

Novo site do **CDI (Centro Acadêmico de Informática da FT-Unicamp, Limeira)**, que reúne em um só lugar o que um aluno de tecnologia precisa no dia a dia: cardápio do RU, próximo ônibus circular, professores, eventos e um guia para ingressantes.

Desenvolvi o site como vice-diretor de projetos do CDI. Ele é a migração de um site estático para **Vite + React + Tailwind**, com conteúdo separado do código e pronto para receber dados de uma API.

![Página inicial do site do CDI](docs/preview.png)

## Destaques

- **5 páginas com rotas reais** (`/`, `/eventos`, `/professores`, `/guia`, `/campus`) via React Router, com tema claro e escuro.
- **Painel do circular em tempo real:** calcula o próximo ônibus pelo horário de Limeira (hook `useAgora`) e mostra o rastreamento ao vivo.
- **Conteúdo desacoplado:** textos, listas e links ficam em `src/data/`, então quem não programa consegue atualizar o site.
- **Pronto para back-end:** o hook `useDados` busca da API e volta para os dados locais se a chamada falhar.

> Esta é a versão pública do projeto. Fotos dos membros, links de convite dos grupos de WhatsApp e documentos internos foram retirados; a página de membros mostra as iniciais.

## Como rodar

Requer Node.js 20.19+ ou 22.12+.

```bash
npm install       # instala as dependências (só na primeira vez)
npm run dev       # servidor de desenvolvimento 
npm run build     # gera a versão final em dist/
npm run preview   # serve a pasta dist/ para conferir o build
```

## Estrutura

```
public/img/          imagens (fotos, logos, mapa, professores…)
src/
  main.jsx           ponto de entrada (React + router)
  App.jsx            layout (Header, páginas, Footer) e rotas
  index.css          Tailwind, cores do tema claro/escuro e classes globais
  pages/             uma página por rota
    Inicio.jsx         /
    Eventos.jsx        /eventos
    Professores.jsx    /professores
    Guia.jsx           /guia
    Campus.jsx         /campus
  components/        um componente por arquivo (Header, Footer, CardapioRU, CircularPainel…)
  data/              conteúdo do site: professores, links, cardápio, circular, membros, FAQ…
  hooks/             useAgora (hora de Limeira) e useDados (dados que podem vir da API)
  services/api.js    cliente da API do back-end
  utils/             funções de horário, circular e rolagem
```

Para mudar textos, links ou listas, edite os arquivos em `src/data/`. Para trocar uma imagem,
substitua o arquivo em `public/img/` ou ajuste o caminho em `src/data/imagens.js`.

## Conectando com o back-end

Sem configuração, o site usa só os dados de `src/data/`. Para buscar da API:

1. Copie `.env.example` para `.env`.
2. Preencha `VITE_API_URL`:
   - `VITE_API_URL=/api`: em desenvolvimento, o Vite repassa `/api/*` para `http://localhost:3000`
     (porta configurável em `vite.config.js`). Em produção, o servidor deve atender `/api` no mesmo domínio.
   - `VITE_API_URL=https://api.exemplo.com`: chama a API direto (ela precisa liberar CORS).
3. Reinicie o `npm run dev` (variáveis do `.env` são lidas na inicialização).

Endpoints usados (todos `GET`, respondendo JSON no mesmo formato dos arquivos de `src/data/`):

| Endpoint       | Formato                                  | Dados locais de reserva      |
| -------------- | ---------------------------------------- | ---------------------------- |
| `/cardapio`    | `{ cafe, dias: [...] }`                  | `CARDAPIO` em `cardapio.js`  |
| `/circular`    | `[{ n, onibus, ft, fca, lateral, aabb, fim }]` | `CIRCULAR` em `circular.js` |
| `/professores` | `[{ nome, foto, email, area, link, rotulo }]` | `PROFESSORES` em `professores.js` |
| `/eventos`     | `[{ nome, descricao, local, data, link, imagem }]` | `PROXIMOS` em `eventos.js` |

Enquanto a resposta não chega, ou se a chamada falhar, a página mostra os dados locais.
Para adicionar um endpoint, crie a função em `src/services/api.js` e o hook em `src/hooks/useDados.js`.

## Publicação

O build gera arquivos estáticos em `dist/`. As páginas usam URLs reais (`/eventos`, `/guia`…),
então o servidor precisa devolver `index.html` para qualquer caminho que não seja um arquivo
(o chamado "SPA fallback"). Netlify, Vercel e o `npm run preview` já fazem isso; em Nginx, use
`try_files $uri /index.html;`.
