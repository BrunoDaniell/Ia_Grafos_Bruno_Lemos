{
  "nbformat": 4,
  "nbformat_minor": 0,
  "metadata": {
    "colab": {
      "provenance": []
    },
    "kernelspec": {
      "name": "python3",
      "display_name": "Python 3"
    },
    "language_info": {
      "name": "python"
    }
  },
  "cells": [
    {
      "cell_type": "markdown",
      "source": [
        "**Lista de Exercícios N2**\n",
        "\n",
        "Tema: Grafos e Algoritmos de Busca (Dijkstra, A*, em-ordem, pré-ordem e pós-ordem)\n",
        "\n",
        "Disciplina: Inteligência Artificial\\\n",
        "Ambiente: Google Colab\\\n",
        "Entrega: via repositório individual no GitHub"
      ],
      "metadata": {
        "id": "51-tDsdpFH9B"
      }
    },
    {
      "cell_type": "markdown",
      "source": [
        "**Nome completo:**\\\n",
        "**Matricula:**"
      ],
      "metadata": {
        "id": "-02FfQpMKNEb"
      }
    },
    {
      "cell_type": "markdown",
      "source": [
        "**Regras Gerais**\n",
        "\n",
        "- Trabalho individual.\n",
        "\n",
        "- Linguagem: Python 3 (Google Colab).\n",
        "\n",
        "- É permitido usar: heapq, numpy, matplotlib, dataclasses.\n",
        "\n",
        "- Proibido usar funções prontas de shortest path (networkx.shortest_path, scipy.sparse.csgraph.dijkstra, etc.).\n",
        "\n",
        "O Notebook (.ipynb) deve conter:\n",
        "\n",
        "- Identificação (nome, turma, link do GitHub)\n",
        "\n",
        "- Código, testes e reflexões\n",
        "\n",
        "- Seções organizadas conforme o roteiro abaixo."
      ],
      "metadata": {
        "id": "iJ41FayHF8rV"
      }
    },
    {
      "cell_type": "markdown",
      "source": [
        "**Parte A — Dijkstra (Caminho Mínimo em Grafos Ponderados Positivos)**\n",
        "\n",
        "> Imagine que você está projetando um sistema de navegação para ambulâncias em uma cidade. Cada interseção é representada como um nó e cada rua como uma aresta ponderada com o tempo médio de deslocamento. Você precisa encontrar a rota mais rápida entre o hospital e o local de atendimento, garantindo que o caminho tenha custo mínimo e seja correto mesmo em grandes redes urbanas.\n",
        "\n",
        "**Atividades**\n",
        "\n",
        "- Implemente o algoritmo de Dijkstra, retornando o custo mínimo (dist) e os predecessores (parent).\n",
        "\n",
        "- Crie uma função reconstruct_path(parent, target) que reconstrua o trajeto.\n",
        "\n",
        "Teste o algoritmo em um grafo de exemplo.\n",
        "\n",
        "**Explique (Questões Discursivas):**\n",
        "\n",
        "1. Por que Dijkstra exige arestas não negativas?\n",
        "\n",
        "2. Qual a complexidade do algoritmo com lista de adjacência e heapq?\n",
        "\n",
        "💡 Dica: Compare seu resultado com um mapa simples — se mudar o peso de uma rua, a rota muda?"
      ],
      "metadata": {
        "id": "XKYf0iFXGaV1"
      }
    },
    {
      "cell_type": "code",
      "source": [
        "# import heapq

def dijkstra(grafo, origem):
    dist = {no: float('inf') for no in grafo}
    parent = {no: None for no in grafo}
    dist[origem] = 0

    fila = [(0, origem)]  # (custo, nó)

    while fila:
        custo_atual, no_atual = heapq.heappop(fila)

        if custo_atual > dist[no_atual]:
            continue

        for vizinho, peso in grafo[no_atual]:
            novo_custo = custo_atual + peso
            if novo_custo < dist[vizinho]:
                dist[vizinho] = novo_custo
                parent[vizinho] = no_atual
                heapq.heappush(fila, (novo_custo, vizinho))

    return dist, parent


def reconstruct_path(parent, destino):
    caminho = []
    while destino is not None:
        caminho.append(destino)
        destino = parent[destino]
    caminho.reverse()
    return caminho


# Grafo de exemplo (representando ruas e tempos médios)
grafo = {
    'Hospital': [('A', 4), ('B', 2)],
    'A': [('C', 3)],
    'B': [('A', 1), ('C', 5)],
    'C': [('Destino', 2)],
    'Destino': []
}

# Executa o algoritmo
dist, parent = dijkstra(grafo, 'Hospital')

print("🚑 Distâncias mínimas até cada ponto:")
print(dist)

print("\n🧭 Predecessores de cada ponto:")
print(parent)

caminho = reconstruct_path(parent, 'Destino')
print("\n✅ Melhor rota encontrada:")
print(" ➜ ".join(caminho))
"
      ],
      "metadata": {
        "id": "_7qVHP30FFF5"
      },
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [],
      "metadata": {
        "id": "vKwE6Qo2IBFc"
      },
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "markdown",
      "source": [
        "**Parte B — A-Star (Busca Informada com Heurística Admissível)**\n",
        "\n",
        "> Agora, considere um robô autônomo que deve se deslocar por um labirinto 2D, evitando obstáculos e chegando ao destino no menor tempo possível.\n",
        "Diferente do Dijkstra, o robô pode usar uma heurística (como a distância ao alvo) para priorizar rotas promissoras, economizando tempo de busca.\n",
        "\n",
        "**Atividades**\n",
        "\n",
        "1. Gere um grid 20x20 com ~15% de obstáculos aleatórios.\n",
        "\n",
        "2. Implemente a função heuristic(a, b) (distância Manhattan).\n",
        "\n",
        "3. Desenvolva o algoritmo a_star(grid, start, goal, h) e teste-o.\n",
        "\n",
        "**Explique (Questões Discursivas):**\n",
        "\n",
        "1. A* vs Dijkstra: qual expande menos nós?\n",
        "\n",
        "2. Por que a heurística Manhattan é admissível nesse caso?\n",
        "\n",
        "💡 Cenário real: O A* é amplamente usado em robôs aspiradores, drones e jogos. Seu desafio é aplicar o mesmo raciocínio."
      ],
      "metadata": {
        "id": "9vCEA95GGx_6"
      }
    },
    {
      "cell_type": "code",
      "source": [
        "# import heapq
import random

# -----------------------------
# 1️⃣ Gera um grid 20x20 com 15% de obstáculos
# -----------------------------
def gerar_grid(tamanho=20, prob_obstaculo=0.15):
    grid = []
    for _ in range(tamanho):
        linha = [0 if random.random() > prob_obstaculo else 1 for _ in range(tamanho)]
        grid.append(linha)
    return grid

# -----------------------------
# 2️⃣ Função heurística (distância Manhattan)
# -----------------------------
def heuristic(a, b):
    # Distância Manhattan = |x1 - x2| + |y1 - y2|
    return abs(a[0] - b[0]) + abs(a[1] - b[1])

# -----------------------------
# 3️⃣ Algoritmo A* (Busca Informada)
# -----------------------------
def a_star(grid, start, goal, h):
    tamanho = len(grid)
    movimentos = [(0,1), (0,-1), (1,0), (-1,0)]  # 4 direções
    open_set = []
    heapq.heappush(open_set, (0, start))

    g_score = {start: 0}
    parent = {start: None}

    while open_set:
        _, atual = heapq.heappop(open_set)

        if atual == goal:
            # Reconstrói o caminho
            caminho = []
            while atual is not None:
                caminho.append(atual)
                atual = parent[atual]
            caminho.reverse()
            return caminho, g_score[goal]

        for dx, dy in movimentos:
            vizinho = (atual[0] + dx, atual[1] + dy)
            # Verifica limites do grid e obstáculos
            if (0 <= vizinho[0] < tamanho and 0 <= vizinho[1] < tamanho and grid[vizinho[0]][vizinho[1]] == 0):
                novo_g = g_score[atual] + 1
                if vizinho not in g_score or novo_g < g_score[vizinho]:
                    g_score[vizinho] = novo_g
                    f_score = novo_g + h(vizinho, goal)
                    heapq.heappush(open_set, (f_score, vizinho))
                    parent[vizinho] = atual
    return None, float('inf')

# -----------------------------
# 4️⃣ Teste do algoritmo
# -----------------------------
grid = gerar_grid()
start = (0, 0)
goal = (19, 19)

# Garante que início e fim não sejam obstáculos
grid[start[0]][start[1]] = 0
grid[goal[0]][goal[1]] = 0

caminho, custo = a_star(grid, start, goal, heuristic)

# Exibe resultado
if caminho:
    print(f"✅ Caminho encontrado com custo {custo}")
else:
    print("❌ Nenhum caminho encontrado.")

# Exibe visualmente o grid
for i in range(len(grid)):
    linha = ""
    for j in range(len(grid[i])):
        if (i, j) == start:
            linha += "S "  # Start
        elif (i, j) == goal:
            linha += "G "  # Goal
        elif (i, j) in caminho:
            linha += "• "  # Caminho
        elif grid[i][j] == 1:
            linha += "⬛ "  # Obstáculo
        else:
            linha += "⬜ "
    print(linha)"
      ],
      "metadata": {
        "id": "XMIxqCl3FEt6"
      },
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [],
      "metadata": {
        "id": "zQ1WgmxRFB8N"
      },
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "markdown",
      "source": [
        "**Parte C — Árvores Binárias e Percursos (DFS em-ordem, pré-ordem e pós-ordem)**\n",
        "\n",
        "> Você está desenvolvendo um sistema de recomendação que organiza produtos em uma árvore binária de busca (BST), conforme o preço. Cada nó é um produto e a travessia da árvore pode ser usada para: 1. Ordenar produtos (em-ordem); 2. Clonar a estrutura (pré-ordem); 3. Calcular totais ou liberar memória (pós-ordem);\n",
        "\n",
        "**Atividades**\n",
        "\n",
        "1. Crie uma BST com os valores: [50, 30, 70, 20, 40, 60, 80, 35, 45].\n",
        "\n",
        "Implemente os percursos:\n",
        "\n",
        "1. in_order(root)\n",
        "\n",
        "2. pre_order(root)\n",
        "\n",
        "3. post_order(root)\n",
        "\n",
        "Teste se as saídas correspondem às travessias esperadas.\n",
        "\n",
        "**Explique (Questões Discursivas):**\n",
        "\n",
        "1. Em que situação cada tipo de percurso é mais indicado?"
      ],
      "metadata": {
        "id": "qJ1Rbf22IASJ"
      }
    },
    {
      "cell_type": "code",
      "source": [
        "## # Definição do nó da árvore binária
class Node:
    def __init__(self, valor):
        self.valor = valor
        self.esq = None
        self.dir = None

# Função para inserir um novo nó na BST
def inserir(root, valor):
    if root is None:
        return Node(valor)
    if valor < root.valor:
        root.esq = inserir(root.esq, valor)
    else:
        root.dir = inserir(root.dir, valor)
    return root

# Percurso em-ordem (in-order): Esquerda → Raiz → Direita
def in_order(root):
    if root:
        in_order(root.esq)
        print(root.valor, end=' ')
        in_order(root.dir)

# Percurso pré-ordem (pre-order): Raiz → Esquerda → Direita
def pre_order(root):
    if root:
        print(root.valor, end=' ')
        pre_order(root.esq)
        pre_order(root.dir)

# Percurso pós-ordem (post-order): Esquerda → Direita → Raiz
def post_order(root):
    if root:
        post_order(root.esq)
        post_order(root.dir)
        print(root.valor, end=' ')

# ---- Teste ----
valores = [50, 30, 70, 20, 40, 60, 80, 35, 45]

root = None
for v in valores:
    root = inserir(root, v)

print("Percurso em-ordem (ordenado):")
in_order(root)
print("\n")

print("Percurso pré-ordem:")
pre_order(root)
print("\n")

print("Percurso pós-ordem:")
post_order(root)
print("\n")
"
      ],
      "metadata": {
        "id": "jxol5GNCIrM9"
      },
      "execution_count": 2,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [],
      "metadata": {
        "id": "6eP76jYWItEK"
      },
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "markdown",
      "source": [
        "**Parte D — Reflexões (Respostas Curtas)**\n",
        "\n",
        "Responda de forma argumentativa (5–10 linhas cada):\n",
        "\n",
        "1. Quando não é vantajoso usar A*, mesmo tendo uma heurística?\n",
        "\n",
        "2. Diferencie corretude e otimalidade nos algoritmos estudados.\n",
        "\n",
        "3. Dê um exemplo do mundo real onde cada tipo de percurso (em, pré, pós) é essencial.\n",
        "\n",
        "4. Como heurísticas inconsistentes podem afetar o resultado do A*?\n",
        "\n",
        "💬 Sugestão: use exemplos de mapas, jogos, sistemas de busca ou árvores sintáticas."
      ],
      "metadata": {
        "id": "5bocfC3rIuwu"
      }
    },
    {
      "cell_type": "markdown",
      "source": [
        "1 - Quando não é vantajoso usar A*, mesmo tendo uma heurística?

O A* deixa de ser vantajoso quando o problema é muito grande ou a heurística não ajuda de verdade a guiar a busca. Nesses casos, ele acaba explorando quase todos os caminhos possíveis, ficando tão pesado quanto o Dijkstra. Além disso, em ambientes que mudam constantemente (como mapas dinâmicos em jogos), é caro recalcular o A* várias vezes. Ou seja, se a heurística for fraca ou o cenário mudar rápido demais, o custo do A* não compensa o benefício.

2 -  Diferencie corretude e otimalidade nos algoritmos estudados.

A corretude significa que o algoritmo sempre encontra uma solução válida, se ela existir. Já a otimalidade quer dizer que essa solução é a melhor possível, levando em conta o custo ou o desempenho. Por exemplo, o algoritmo de busca em largura é correto e ótimo em grafos com custos iguais, enquanto o A* só é ótimo se sua heurística for admissível. Então, dá pra dizer que um algoritmo pode ser correto sem ser o mais eficiente — mas não o contrário.

3 - Dê um exemplo do mundo real onde cada tipo de percurso (em, pré, pós) é essencial.

Em-ordem (in-order): usado em lojas online ou sistemas de recomendação para exibir produtos em ordem de preço ou nome.

Pré-ordem (pre-order): útil quando queremos copiar ou exportar a estrutura de uma árvore, como no backup de pastas ou na leitura de árvores sintáticas em compiladores.

Pós-ordem (post-order): aplicado em cálculos que dependem dos “filhos” antes do “pai”, como somar valores em uma árvore contábil ou liberar memória em estruturas de dados.

4 - Como heurísticas inconsistentes podem afetar o resultado do A*?

Quando a heurística é inconsistente, ela pode “enganar” o A*, fazendo o algoritmo acreditar que certos caminhos são mais curtos do que realmente são. Isso faz com que alguns nós precisem ser revisitados, aumentando o tempo de execução e, em alguns casos, prejudicando a otimalidade do resultado. Em contextos práticos, como em jogos ou navegação de robôs, isso pode gerar rotas estranhas, com voltas desnecessárias ou movimentos fora do esperado."
      ],
      "metadata": {
        "id": "8W_afmXGI6h3"
      }
    },
    {
      "cell_type": "code",
      "source": [],
      "metadata": {
        "id": "Vo_E3Z3AI40h"
      },
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "markdown",
      "source": [
        "**Entrega**\n",
        "\n",
        "Crie um repositório público chamado ia-grafos-seu-nome.\n",
        "\n",
        "Envie para o repositório o arquivo ia_grafos_buscas.ipynb.\n",
        "\n",
        "Submeta o link do repositório no ambiente da disciplina **Portal Digital Fametro.**."
      ],
      "metadata": {
        "id": "vyWiRE5TJEUf"
      }
    },
    {
      "cell_type": "markdown",
      "source": [
        "**Integridade Acadêmica**\n",
        "\n",
        "1. O trabalho é individual.\n",
        "2. Discussões conceituais são permitidas, mas o código deve ser inteiramente autoral.\n",
        "3. Verificações de similaridade serão aplicadas a todas as submissões.\n",
        "4. Busque e estude os algoritmos através de pesquisas na internet, livros, slides da disciplina."
      ],
      "metadata": {
        "id": "xP1stx7fJcCV"
      }
    },
    {
      "cell_type": "code",
      "source": [],
      "metadata": {
        "id": "uxmWR_KlJXDC"
      },
      "execution_count": null,
      "outputs": []
    }
  ]
}
