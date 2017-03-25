#include <stdio.h>
#include <string.h>

typedef struct aluno{
	char matricula[20];
	char nome[40];
	char lacos[40];
	/*t_fila * adjacentes;*/
}t_aluno;

/*typedef struct fila {
	t_aluno *knot;
	struct fila *proximo;
} t_elemento;


typedef struct {
	t_elemento *primeiro;
	t_elemento *ultimo;
} t_fila;

void Enfileirar (t_aluno *valor, t_fila *f) {
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
*/
int main(){
	FILE * fp;
	t_aluno alunos[41];
	char amigos[] = "amigos.txt";
	int vet[39], soma=0;

	
	fp = fopen(amigos, "r");
	int i=0, j;
	
	for(i=0; i < 39; i++){
		vet[i] =0; 
	}
	i=0;
	while((fscanf(fp, "%s | %[^|] | %[^\n]", alunos[i].matricula, alunos[i].nome, alunos[i].lacos ))!= EOF) {
		soma =0;
		if(alunos[i].lacos[0] == '-'){
			vet[i] = 0;
		}
		else{	
			for(j=0;j<=strlen(alunos[i].lacos); j++){
				if(alunos[i].lacos[j] == ' '  ){
					soma ++;
				}
				
			}	
			soma++; /*para adicionar o ultimo valor , já que no final será \n e não um espaço*/
		}
		vet[i] = soma;

		i++;	}

	for(i=0; i < 39; i++){
		printf("vet[%d] = %d\n",i+1,  vet[i]);
		vet[i] =0;
	}		

	return 0;
}	
