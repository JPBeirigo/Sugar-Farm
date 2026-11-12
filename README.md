# 🐜 Sugar Clicker

Jogo *idle / incremental* no navegador, com visual cartoon leve. 
Neste jogo simples você comanda uma colônia de formigas que coleta e acumula açúcar de um enorme cubo central, o jogo começa no clique manual e evolui para uma reação em cadeia automatizada, com formigas e formigueiros coletando açúcar automaticamente.

> Projeto de portfólio: arquitetura limpa em React, componentes separados, lógica de jogo isolada e persistência local.

## Tecnologias

| Área         | Escolha                                                                       |
| ------------ | ----------------------------------------------------------------------------- |
| Interface    | React 18 (hooks)                                                              |
| Build        | Vite 5 + `vite-plugin-singlefile`                                             |
| Estilo       | CSS Modules, com fonte Fredoka (Google Fonts)                                 |
| Persistência | `localStorage`                                                                |

---

## Como se joga

1. **Clique no cubo** para ganhar açúcar.
2. **Compre melhorias** na loja para aumentar o ganho por clique e gerar açúcar sozinho.
3. **Evolua as formigas** para multiplicar o poder de clique.
4. Fique de olho nos **eventos aleatórios**, que dão bônus temporários.

### Fórmulas

```text
Preço atual     = ⌈ Preço base × 1,15 ^ quantidade possuída ⌉
Poder de clique = 1 + (formigas × poder de cada formiga)
Poder de cada formiga = produto dos multiplicadores das evoluções compradas
Ganho por clique      = Poder de clique × multiplicadores de eventos
Açúcar por segundo    = soma das melhorias passivas × multiplicadores de eventos
```

### Melhorias da loja

| Melhoria | Tipo | Custo inicial | Efeito |
| --- | --- | --- | --- |
| 🐜 Formiga | Clique | 20 | +1 por clique (× poder de cada formiga) |
| 👑 Formiga Rainha | Passiva | 100 | +1 açúcar/s |
| 🕳️ Formigueiro | Passiva | 1.000 | +10 açúcar/s |
| 🐄 Pulgão | Passiva | 5.000 | +50 açúcar/s |
| 🏰 Super-Colônia | Passiva | 25.000 | +250 açúcar/s |


### Eventos especiais

Um evento aleatório acontece a cada **40 a 90 segundos**.

| Evento | Efeito |
| --- | --- |
| 🍬 Doce no chão! | Toda a produção (cliques e passivo) em dobro por 60 s |
| 🍦 Chuva de sorvete | Cliques valem o triplo por 30 s (o cubo derrete) |
| ✨ Piquenique na grama | Uma formiga dourada cruza a tela. Clicando a tempo, você ganha 30 s de produção passiva (sem produção ainda, ganha o equivalente a 10 cliques) |

Os bônus de Doce no chão e Chuva de sorvete se acumulam nos cliques (×2 × ×3).

---

## Extras de experiência

- **Feedback no clique:** o cubo "amassa" e sobem números flutuantes de açúcar.
- **Indicador de produção:** sob o ganho por clique aparece o açúcar/s atual, já com os eventos.
- **Números legíveis:** valores grandes são abreviados (K, M, B, T...).

## Persistência

O progresso é salvo automaticamente no `localStorage` a cada 3 segundos e ao fechar a aba. O botão **Reiniciar** no cabeçalho apaga o progresso.

| Chave                   | Conteúdo                                            |
| ----------------------- | --------------------------------------------------- |
| `sugar-clicker-save-v1` | Açúcar, itens comprados, evoluções e eventos ativos |

---

## Estrutura do projeto

```text
src/
├── components/
│   ├── AntBackground/    # Canvas com formigas, formigueiro e açúcar no fundo
│   ├── ClickerArea/      # Cubo de açúcar, animação de clique e números flutuantes
│   ├── EventBanner/      # Avisos de eventos ativos e a formiga dourada (GoldenAnt)
│   ├── Header/           # Açúcar total, açúcar/s, música, efeitos e reiniciar
│   └── Shop/             # Loja (Shop) e item reutilizável (UpgradeCard)
├── hooks/
│   ├── useGameSave.js    # Estado do jogo + salvamento no localStorage
│   ├── useCozyMusic.js   # Controle da música (preferências, gesto inicial, aba oculta)
│   └── useSfx.js         # Preferência dos efeitos sonoros
├── utils/
│   ├── gameLogic.js      # Dados das melhorias/eventos e fórmulas de preço e produção
│   ├── cozyMusic.js      # Sintetizador e sequenciador da trilha
│   └── sfx.js            # Efeitos sonoros sintetizados
├── App.jsx               # Estado global, game loop e eventos aleatórios
└── main.jsx
```

### Decisões de arquitetura

- **Lógica separada da interface:** preços, produção, multiplicadores e formatação ficam em `gameLogic.js`, em funções puras fáceis de testar.
- **Game loop por tempo real:** a cada 100 ms o `App` soma a produção proporcional ao tempo decorrido, então a produção não depende da velocidade da aba.
- **Efeitos por timestamp:** os eventos guardam o instante em que terminam, e por isso expiram sozinhos e sobrevivem a um recarregamento da página.
- **Áudio sem arquivos:** música e efeitos são sintetizados com Web Audio, sem assets nem direitos autorais, e o bundle continua em um único arquivo.
- **Fila de formigas sem colisão:** todas andam no mesmo sentido e na mesma velocidade numa pista fechada, e uma formiga nova só sai do formigueiro quando há espaço na fila.

## Próximos passos possíveis

- Ilustrações no lugar dos emojis.
- Progresso offline, calculando o que foi produzido com o jogo fechado.
- Testes unitários para `gameLogic.js`.
- Sons para o jogo.
- Mais melhorias e conquistas.
