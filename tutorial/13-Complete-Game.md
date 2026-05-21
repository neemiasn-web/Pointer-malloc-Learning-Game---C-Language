# 13 - Complete Game

# Jogo dos Ponteiros e malloc em Raylib (C)

```c
// Importa a biblioteca gráfica Raylib
#include "raylib.h"

// Importa funções de memória dinâmica
#include <stdlib.h>

// Importa funções de texto como printf
#include <stdio.h>

// Cria uma estrutura chamada BlocoMemoria
typedef struct BlocoMemoria {

    // Guarda posição X e Y
    Vector2 posicao;

    // Guarda tamanho do bloco
    float tamanho;

    // Define se o bloco está ativo
    bool ativo;

} BlocoMemoria;

// Função principal do programa
int main(void)
{
    // Cria a janela do jogo
    InitWindow(800, 450, "Jogo dos Ponteiros e malloc");

    // Define FPS do jogo
    SetTargetFPS(60);

    // Posição inicial do jogador
    Vector2 jogador = { 400, 225 };

    // Velocidade do jogador
    float velocidade = 4.0f;

    // Quantidade máxima inicial de blocos
    int capacidade = 5;

    // Quantidade atual de blocos
    int quantidade = 5;

    // Pontuação do jogador
    int pontos = 0;

    // malloc:
    // cria um vetor dinâmico na heap
    BlocoMemoria *blocos =
        malloc(capacidade * sizeof(BlocoMemoria));

    // Verifica erro de memória
    if (blocos == NULL)
    {
        // Mostra erro no terminal
        printf("Erro ao alocar memoria!\n");

        // Encerra programa
        return 1;
    }

    // Inicializa os blocos
    for (int i = 0; i < quantidade; i++)
    {
        // Posição X aleatória
        blocos[i].posicao.x =
            GetRandomValue(50, 750);

        // Posição Y aleatória
        blocos[i].posicao.y =
            GetRandomValue(50, 400);

        // Tamanho do bloco
        blocos[i].tamanho = 25;

        // Bloco começa ativo
        blocos[i].ativo = true;
    }

    // Loop principal do jogo
    while (!WindowShouldClose())
    {
        // =========================
        // MOVIMENTO DO JOGADOR
        // =========================

        // Move para direita
        if (IsKeyDown(KEY_RIGHT))
            jogador.x += velocidade;

        // Move para esquerda
        if (IsKeyDown(KEY_LEFT))
            jogador.x -= velocidade;

        // Move para cima
        if (IsKeyDown(KEY_UP))
            jogador.y -= velocidade;

        // Move para baixo
        if (IsKeyDown(KEY_DOWN))
            jogador.y += velocidade;

        // Cria hitbox do jogador
        Rectangle rectJogador =
        {
            jogador.x,
            jogador.y,
            30,
            30
        };

        // Percorre todos os blocos
        for (int i = 0; i < quantidade; i++)
        {
            // Verifica se bloco está ativo
            if (blocos[i].ativo)
            {
                // Cria hitbox do bloco
                Rectangle rectBloco =
                {
                    blocos[i].posicao.x,
                    blocos[i].posicao.y,
                    blocos[i].tamanho,
                    blocos[i].tamanho
                };

                // Detecta colisão
                if (CheckCollisionRecs(rectJogador,
                                       rectBloco))
                {
                    // Desativa bloco
                    blocos[i].ativo = false;

                    // Aumenta pontuação
                    pontos++;
                }
            }
        }

        // ==================================
        // REALOCAÇÃO DINÂMICA DE MEMÓRIA
        // ==================================

        // Se todos os blocos foram coletados
        if (pontos == quantidade)
        {
            // Aumenta capacidade total
            capacidade += 5;

            // Aumenta quantidade atual
            quantidade += 5;

            // realloc:
            // aumenta tamanho do vetor
            BlocoMemoria *novoEndereco =
                realloc(blocos,
                        capacidade *
                        sizeof(BlocoMemoria));

            // Verifica erro de realloc
            if (novoEndereco == NULL)
            {
                // Mostra erro
                printf("Erro ao realocar memoria!\n");

                // Libera memória antiga
                free(blocos);

                // Fecha janela
                CloseWindow();

                // Encerra programa
                return 1;
            }

            // Atualiza ponteiro
            blocos = novoEndereco;

            // Inicializa novos blocos
            for (int i = pontos;
                 i < quantidade;
                 i++)
            {
                // Nova posição X aleatória
                blocos[i].posicao.x =
                    GetRandomValue(50, 750);

                // Nova posição Y aleatória
                blocos[i].posicao.y =
                    GetRandomValue(50, 400);

                // Tamanho do bloco
                blocos[i].tamanho = 25;

                // Ativa bloco
                blocos[i].ativo = true;
            }
        }

        // =========================
        // RENDERIZAÇÃO
        // =========================

        // Inicia desenho do frame
        BeginDrawing();

        // Limpa fundo da tela
        ClearBackground(RAYWHITE);

        // Título do jogo
        DrawText("Jogo dos Ponteiros e malloc",
                 20,
                 20,
                 24,
                 DARKBLUE);

        // Instruções
        DrawText("Use as setas para coletar blocos de memoria",
                 20,
                 55,
                 18,
                 DARKGRAY);

        // Mostra pontuação
        DrawText(TextFormat("Pontos: %d", pontos),
                 20,
                 90,
                 20,
                 BLACK);

        // Mostra quantidade de blocos
        DrawText(TextFormat("Blocos alocados: %d",
                            quantidade),
                 20,
                 115,
                 20,
                 BLACK);

        // Mostra capacidade da memória
        DrawText(TextFormat("Capacidade da memoria: %d",
                            capacidade),
                 20,
                 140,
                 20,
                 BLACK);

        // Desenha jogador
        DrawRectangleV(jogador,
                       (Vector2){30, 30},
                       BLUE);

        // Percorre todos os blocos
        for (int i = 0; i < quantidade; i++)
        {
            // Verifica se bloco está ativo
            if (blocos[i].ativo)
            {
                // Desenha bloco
                DrawRectangleV(
                    blocos[i].posicao,
                    (Vector2)
                    {
                        blocos[i].tamanho,
                        blocos[i].tamanho
                    },
                    GREEN
                );
            }
        }

        // Explicação sobre memória
        DrawText(
            "malloc cria os blocos | realloc aumenta | free libera no final",
            20,
            410,
            18,
            MAROON
        );

        // Finaliza renderização
        EndDrawing();
    }

    // free:
    // libera memória usada pelos blocos
    free(blocos);

    // Fecha janela
    CloseWindow();

    // Encerra programa corretamente
    return 0;
}
```

---

# O que este jogo ensina

Este projeto integra:

- renderização
- input
- colisão
- structs
- ponteiros
- heap
- malloc
- realloc
- free
- arrays dinâmicos
- gameplay loop

---

# O mais importante desta aula

Agora o aluno consegue visualizar:

```text
malloc cria entidades reais na tela
```

e:

```text
realloc aumenta a memória do jogo dinamicamente
```

Isso torna:
- ponteiros
- heap
- memória dinâmica

muito mais fáceis de entender visualmente.

---

# Fluxo do jogo

```text
malloc()
    ↓
cria blocos
    ↓
jogador coleta
    ↓
colisão detecta
    ↓
pontuação aumenta
    ↓
realloc()
    ↓
mais blocos aparecem
    ↓
free()
    ↓
memória liberada
```

---

# Conceitos profissionais aprendidos

| Conceito | Foi usado |
|---|---|
| Gameplay Loop | ✔ |
| Dynamic Allocation | ✔ |
| Heap Memory | ✔ |
| Collision | ✔ |
| Structs | ✔ |
| Pointers | ✔ |
| realloc | ✔ |
| free | ✔ |
| Dynamic Entities | ✔ |

---

# Curiosidade MUITO importante

Esse padrão é parecido com:
- engines reais
- sistemas ECS
- jogos AAA
- gerenciamento dinâmico de entidades

Você já está entrando em:
- arquitetura de engine
- gerenciamento profissional de memória
- data-oriented programming
