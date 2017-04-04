
/*Universidade de Brasilia
Instituto de Ciencias Exatas
Departamento de Ciencia da Computacao

Teroria e aplicação de grafos - 01/2017

Alunos(a): Artur Henrique Brandao de Souza	- 15/0118783 
		   Marcos Paulo Batalha Bispo - 15O154208
							
Versão do compilador: gcc(GCC) 4.8.1

Descricao:	O projeto consiste em escrever um programa em C que lê o arquivo (amigos_tag20171.txt), monta com esses dados um grafo não direcionado, sem pesos, 
usando listas de adjacências, e imprime como saída (tela) em ordem decrescente, os alunos (vértices) com maiores graus no grafo, até o de menor. O programa deve 
encontrar e imprimir o maior clique desse grafo (pelos nomes de todos).
*/
#include <stdio.h>
#include <string.h>
#include <stdlib.h>




typedef struct lista {
	int knot;
	struct lista *proximo;
} t_elemento;
				
typedef struct {
	t_elemento *primeiro;
	t_elemento *ultimo;
} t_lista;

typedef struct {
	char matricula[20];
	char nome[40];
	char lacos[40];
	int grau;
	t_lista * adjacentes;
}t_aluno;

t_aluno alunos[39];
t_lista * cliques[5]; 
int count;


/* Funcoes para manipulacao de listas */
t_lista * CriaLista () {
	t_lista * nova_lista;
	
	nova_lista = (t_lista*) malloc(sizeof(t_lista));
	nova_lista->primeiro = NULL;
	nova_lista->ultimo = NULL;
	
	return nova_lista;
	
}

void InserirFinal (int valor, t_lista *f) {
	t_elemento *novoultimo;	

	novoultimo = (t_elemento*) malloc(sizeof(t_elemento));		
	novoultimo->knot = valor;
	novoultimo->proximo = NULL;									

	if (f->primeiro == NULL) {									
		f->primeiro = novoultimo;
	}
	else {
		f->ultimo->proximo = novoultimo;						
	}
	f->ultimo = novoultimo;										
}


void RemoveInicio (t_lista *l) {
	t_elemento *removido;
									
	removido = l->primeiro;
	if (l->primeiro == l->ultimo) {
		l->ultimo = NULL;
	}	
	l->primeiro = l->primeiro->proximo;							
	free (removido);
	
}

/* ---------------------------------------------- */


t_lista * Interseccao (t_lista * l, int vertice) { /*Funcao para fazer a interseccao entre uma lista e a lista de adjacencia de um vertice.*/
	t_elemento * alvo1, * alvo2;
	t_lista * nova_lista;
	
	nova_lista = CriaLista();

	
	if (alunos[vertice - 1].adjacentes == NULL) {
		return nova_lista;
	}
	alvo1 = alunos[vertice - 1].adjacentes->primeiro;
	while (alvo1!= NULL) {
		alvo2 = l->primeiro;
		while (alvo2 != NULL) {
			if (alvo2->knot == alvo1->knot) {
				InserirFinal(alvo2->knot ,nova_lista);
			}
			alvo2 = alvo2->proximo;
		}
		alvo1 = alvo1->proximo;
	}
	return nova_lista;
}

t_lista * CopiaLista (t_lista * l) { /*Funcao feita para criar uma nova lista r a partir de uma lista r antiga */
	t_lista * nova_lista;
	t_elemento * alvo;
	
	
	
	nova_lista = CriaLista();
	
	alvo = l->primeiro;
	while(alvo != NULL) {
		InserirFinal(alvo->knot, nova_lista);
		alvo = alvo->proximo;
	}
	return nova_lista;
	
}

void BubbleSort(){ /* Algoritmo para ordenar a lista de alunos pelo grau */
	int i, fez_troca=1;
	t_aluno aux;

	while(fez_troca == 1){
		fez_troca =0;
		for(i=0;i<38;i++){/* eh necessario ser a quantidade total(39) - 2 pq senao haverá seg fault;*/
			if(alunos[i].grau < alunos[i+1].grau){
				aux = alunos[i];
				alunos[i] = alunos[i+1];
				alunos[i+1] = aux;
				fez_troca =1;
			}
		}
	}

}

void CriaGrafo(){ /* Funcao para construir o grafo a partir do arquivo texto */
	int i =0, num = 0, k =0;
	FILE * fp;
	char amigos[] = "amigos.txt";
	fp = fopen(amigos, "r");
	
	
	while((fscanf(fp, "%s | %[^|] | %[^\n]", alunos[i].matricula, alunos[i].nome, alunos[i].lacos ))!= EOF) {
	
		alunos[i].grau = 0;
	
		if(alunos[i].lacos[0] == '-'){
			alunos[i].adjacentes = NULL;
		}
		else{	
			alunos[i].adjacentes = CriaLista();			
			for(k=0;k<strlen(alunos[i].lacos); k++){ /* para salvar em uma lista os alunos adjacentes*/
			
				
				if(alunos[i].lacos[k+1]<48 || alunos[i].lacos[k+1] > 57 ||alunos[i].lacos[k+1] == ' '  ){
					alunos[i].grau = alunos[i].grau  + 1;
					if(k!=0){


						if(alunos[i].lacos[k-1] >= 48 && alunos[i].lacos[k-1] <=57 ){
							
							num =(10 * (alunos[i].lacos[k-1] - 48)) + (alunos[i].lacos[k] - 48); /*transofrmar o valor da string de tabela ascii para inteiro*/
							InserirFinal(num, alunos[i].adjacentes);	/* pega o valor final e coloca nos adjacentes do aluno referido pelo indice;*/
						}
						else{
						
							num = alunos[i].lacos[k] - 48;		 /* transofrmar o valor da string de tabela ascii para inteiro;*/
							InserirFinal(num, alunos[i].adjacentes);	/* pega o valor final e coloca nos adjacentes do aluno referido pelo indice;*/
						}
					}
					else{
						num = alunos[i].lacos[k] - 48;		 /* transofrmar o valor da string de tabela ascii para inteiro;*/
						InserirFinal(num, alunos[i].adjacentes);	/* pega o valor final e coloca nos adjacentes do aluno referido pelo indice;*/

					}
				}
			}
		}

		i++;
	}	
	fclose(fp);
}


void imprimeAdjacentes ( int i) {

	t_elemento * alvo;

	if(alunos[i].adjacentes == NULL){
		printf("HELLO DARKNESS MY ONLY FRIEND\n");
	}
	else{
		alvo = alunos[i].adjacentes->primeiro;
		while(alvo  != NULL){
		
			printf ("%d->", alvo->knot);
			alvo = alvo->proximo;

		}
		printf ("\\\n");
	}

}

void ImprimeClique() {
	int i, maior_tamanho = -1, tamanho, membro;
	t_elemento * alvo;
	t_elemento * primeiro_maior; 
	
	for (i=0;i<count;i++) { 
		tamanho = 0;
		alvo = cliques[i]->primeiro;
		while(alvo  != NULL){
			alvo = alvo->proximo;
			tamanho++;

		}
		if (tamanho > maior_tamanho) {
			primeiro_maior = cliques[i]->primeiro;
			maior_tamanho = tamanho;
		}
	}
	alvo = primeiro_maior;
	while(alvo  != NULL){
        membro = alvo->knot - 1;
		printf("%s->", alunos[membro].nome);
        alvo = alvo->proximo;

	}
    printf("\n");
	
	
	
}



void LiberaMemoria (t_lista * f) { /* Funcao para liberar a memoria das listas de adjacencia */
	t_elemento * atual, * guarda;
	

	if (f != NULL) {
		atual = f->primeiro;
		while (atual != NULL) {
			guarda = atual->proximo;
			free(atual);
			atual = guarda;
		}
		free(f);
	}

}

void BronKerbosch (t_lista * r, t_lista * x, t_lista * p) { /*Implementacao do algoritmo discutido em sala */
	t_lista *r_novo, *x_novo, *p_novo;
	int vertice;
	
	if (p->primeiro == NULL) {
		if (x->primeiro == NULL) { /* Guarda um clique maximal em um vetor usado para guardar todos os cliques */
			cliques[count] = r;
			count++;
		}
		else {
			LiberaMemoria(r); /* A funcao entrara nessa condicao caso ache um clique nao maximal, que nao precisa ser guardado */
		}
	}
	
	else {
		while (p->primeiro != NULL) {
			r_novo = CopiaLista(r);
			vertice = p->primeiro->knot;
			InserirFinal(vertice, r_novo);
			p_novo = Interseccao(p, vertice);
			x_novo = Interseccao(x, vertice);
			BronKerbosch(r_novo, x_novo, p_novo);
			InserirFinal (vertice,x);
			RemoveInicio (p);
			LiberaMemoria(p_novo);
			LiberaMemoria(x_novo);
		}
	}
	
}



int main(){
	t_lista *r, *x, *p;
	int i=0;	
	
	
	count = 0;
	
	
	
	CriaGrafo();
	
	r = CriaLista();
	x = CriaLista();
	p = CriaLista();
	
	for(i=1;i<40;i++){
		InserirFinal(i,p);
	}
	BronKerbosch(r, x, p);
	ImprimeClique();
	BubbleSort();	
	for(i=0;i<39;i++){
		imprimeAdjacentes(i);
		LiberaMemoria(alunos[i].adjacentes);
	}
	LiberaMemoria(x);
    LiberaMemoria(p);
    LiberaMemoria(r);
	return 0;
}	
 	
