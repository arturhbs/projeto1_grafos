#include <stdio.h>
#include <string.h>
#include <stdlib.h>

typedef struct {
	char matricula[20];
	char nome[40];
	char lacos[40];
	/*t_fila * adjacentes;*/
}t_aluno;


typedef struct fila {
	t_aluno *knot;
	struct fila *proximo;
} t_elemento;


typedef struct {
	t_elemento *primeiro;
	t_elemento *ultimo;
} t_fila;

typedef struct {
	t_aluno * aluno;
	t_fila * adjacentes;
}t_filaluno;

void Enfileirar (int *valor, t_fila *f) {
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

int main(){
	FILE * fp;
	t_filaluno alunos[41];
	char amigos[] = "amigos.txt";
	int vet[39], soma=0, j, num;

	
	fp = fopen(amigos, "r");
	int i=0, k;
	
	for(i=0; i < 39; i++){
		vet[i] =0; 
	}
	i=0;
	while((fscanf(fp, "%s | %[^|] | %[^\n]", alunos[i].aluno.matricula, alunos[i].aluno.nome, alunos[i].aluno.lacos ))!= EOF) {
		soma =0;
		if(alunos[i].aluno.lacos[0] == '-'){
			vet[i] = 0;
		}
		else{	
			for(j=0;j<=strlen(alunos[i].aluno.lacos); j++){ // será para somar a quantidade de amigos presentes .
				if(alunos[i].aluno.lacos[j] == ' '  ){
					soma ++;					
				}
				
			}	
			soma++; /*para adicionar o ultimo valor , já que no final será \n e não um espaço*/
			while(alunos[i].aluno.lacos[k] != '\n'){ // para salvar em uma lista os alunos adjacentes
				if(alunos[i].aluno.lacos[k+1] == ' ' || alunos[i].aluno.lacos[k+1] == '\n' ){
					if(alunos[i].aluno.lacos[k-1] >= 48 && alunos[i].aluno.lacos[k-1] <=57 ){
						num = 10 * alunos[i].aluno.lacos[k-1] + alunos[i].aluno.lacos[k];
						Enfileirar(num, alunos[i].adjacentes);
					}
					else{
						num = alunos[i].aluno.lacos[k];
						Enfileirar(num, alunos[i].adjacentes);
					}
				}
			}
		}
		vet[i] = soma;



		i++;
	}

	return 0;
}	
