#include <stdio.h>
#include <string.h>
#include <stdlib.h>


typedef struct fila {
	int knot;
	struct fila *proximo;
} t_elemento;


typedef struct {
	t_elemento *primeiro;
	t_elemento *ultimo;
} t_fila;

typedef struct {
	char matricula[20];
	char nome[40];
	char lacos[40];
	t_fila * adjacentes;
}t_aluno;



void Enfileirar (int valor, t_fila *f) {
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


void imprimeAdjacentes (t_aluno * aluno) {
	int i=0;
	if(aluno->adjacentes->primeiro == NULL){
		printf("alunos = 0\n");
	}
	else{

		do{

			printf("aluno = %d\n", aluno->adjacentes->primeiro->knot);
			aluno->adjacentes->primeiro = aluno->adjacentes->primeiro->proximo;

		}while(aluno->adjacentes->primeiro != NULL);
	}

}

int main(){


	FILE * fp;
	t_aluno alunos[39];
	char amigos[] = "amigos.txt";
	int  soma=0, j, num;

	
	fp = fopen(amigos, "r");
	int i=0, k=0;	

	for(i=0; i < 39; i++){
		alunos[i].adjacentes = (t_fila*) malloc(sizeof(t_fila)); 
		alunos[i].adjacentes->primeiro = (t_elemento*) malloc(sizeof(t_elemento));
		alunos[i].adjacentes->ultimo = (t_elemento*) malloc(sizeof(t_elemento));
	}

	i=0;
	while((fscanf(fp, "%s | %[^|] | %[^\n]", alunos[i].matricula, alunos[i].nome, alunos[i].lacos ))!= EOF) {
	
		soma =0;
		if(alunos[i].lacos[0] == '-'){
			alunos[i].adjacentes->primeiro = NULL;
		}
		else{	
			
			printf("%s\n",alunos[i].lacos );
			int tamanho = strlen(alunos[i].lacos);
			printf("%d\n",tamanho );
			for(k=0;k<strlen(alunos[i].lacos); k++){ // para salvar em uma lista os alunos adjacentes
					
					printf("%c\n", alunos[i].lacos[k] );
					getchar();
					
				
				if(alunos[i].lacos[k+1] == ' '  ){
					
					if(alunos[i].lacos[k-1] >= 48 && alunos[i].lacos[k-1] <=57 ){
						
						num =(10 * (alunos[i].lacos[k-1] - 48)) + (alunos[i].lacos[k] - 48);
						Enfileirar(num, alunos[i].adjacentes);
					}
					else{
						num = alunos[i].lacos[k] - 48;
						Enfileirar(num, alunos[i].adjacentes);
					
					}

				}
				else{
					if((alunos[i].lacos[k+1]<48 || alunos[i].lacos[k+1] > 57) ){
					
						num =(10 * (alunos[i].lacos[k-1] - 48)) + (alunos[i].lacos[k] - 48);
				
						
						Enfileirar(num, alunos[i].adjacentes);
					}
					else{
						num = alunos[i].lacos[k] -48;
						Enfileirar(num, alunos[i].adjacentes);
					
					}
				}
			}
		}

		i++;
	}
	for(i=0;i<39;i++){
		imprimeAdjacentes(alunos[i]);
	}

	fclose(fp);
	return 0;
}	
