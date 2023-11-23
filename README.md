# Navegaçao-de-Internet-em-c-
Constrir um programa que simulasse o navegador de Internet, em que devem ser guardados todos os sites, data e hora em que você navegou e dado momento você pode retornar aos sites anteriores um a um por ordem que foram inseridos. Para isso usei duas pilhas uma que guarda os novos sites e outro que guardara os sites que ele retornou.
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>

struct Site {
    char url[100];
    struct tm timestamp;  // Alteração no tipo de timestamp
};

struct Node {
    struct Site data;
    struct Node* next;
};

struct Stack {
    struct Node* top;
};

// Inicializa uma nova pilha
struct Stack* createStack() {
    struct Stack* stack = (struct Stack*)malloc(sizeof(struct Stack));
    stack->top = NULL;
    return stack;
}

// Empilha um site na pilha
void push(struct Stack* stack, struct Site data) {
    struct Node* newNode = (struct Node*)malloc(sizeof(struct Node));
    newNode->data = data;
    newNode->next = stack->top;
    stack->top = newNode;
}

// Desempilha um site da pilha
struct Site pop(struct Stack* stack) {
    if (stack->top == NULL) {
        struct Site emptySite;
        strcpy(emptySite.url, "");
        return emptySite;
    }

    struct Node* temp = stack->top;
    struct Site popped = temp->data;
    stack->top = temp->next;
    free(temp);
    return popped;
}

// Exibe todos os sites visitados
void showAllSites(struct Stack* stack) {
    struct Node* current = stack->top;

    if (current == NULL) {
        printf("Nenhum site foi visitado ainda.\n");
        return;
    }

    printf("Sites visitados:\n");
    while (current != NULL) {
        printf("- URL: %s\n", current->data.url);
        printf("  Data e Hora: %02d-%02d-%04d %02d:%02d:%02d\n",
               current->data.timestamp.tm_mday, current->data.timestamp.tm_mon + 1,
               current->data.timestamp.tm_year + 1900, current->data.timestamp.tm_hour,
               current->data.timestamp.tm_min, current->data.timestamp.tm_sec);
        current = current->next;
    }
}

// Exibe as opções do menu
void showOptions() {
    printf("1. Visitar um novo site\n");
    printf("2. Retornar ao site anterior\n");
    printf("3. Mostrar todos os sites visitados\n");
    printf("4. Sair\n");
}

int main() {
    struct Stack* historico = createStack();    // Pilha para armazenar os novos sites

    int opcao;
    do {
        showOptions();
        printf("Escolha uma opcao: ");
        scanf("%d", &opcao);

        switch (opcao) {
            case 1:
                // Visitar um novo site
                {
                    struct Site novoSite;
                    printf("Digite a URL do novo site: ");
                    scanf("%s", novoSite.url);

                    time_t rawTime;
                    time(&rawTime);
                    novoSite.timestamp = *localtime(&rawTime);  // Obter data e hora local
                    push(historico, novoSite);

                    printf("Site visitado com sucesso!\n");
                }
                break;

            case 2:
                // Retornar ao site anterior
                {
                    struct Site siteAtual = pop(historico);
                    if (strcmp(siteAtual.url, "") != 0) {
                        printf("Voltando ao site anterior: %s\n", siteAtual.url);
                    } else {
                        printf("Nao ha sites anteriores para retornar.\n");
                    }
                }
                break;

            case 3:
                // Mostrar todos os sites visitados
                showAllSites(historico);
                break;

            case 4:
                // Sair
                printf("Saindo do navegador.\n");
                break;

            default:
                printf("Opcao invalida. Tente novamente.\n");
        }
    } while (opcao != 4);

    // Liberar memória alocada para os nós da pilha
    while (historico->top != NULL) {
        pop(historico);
    }
    free(historico);

    return 0;
}
