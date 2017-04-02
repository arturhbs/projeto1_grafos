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
	int grau;
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

void BubbleSort(t_aluno * a){
	int i, fez_troca=1;
	t_aluno aux;

	while(fez_troca == 1){
		fez_troca =0;
		for(i=0;i<38;i++){/* eh necessario ser a quantidade total(39) - 2 pq senao haverá seg fault;*/
			if(a[i].grau < a[i+1].grau){
				aux = a[i];
				a[i] = a[i+1];
				a[i+1] = aux;
				fez_troca =1;
			}
		}
	}

}

void CriaGrafo(t_aluno * alunos){
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
			alunos[i].adjacentes = (t_fila*) malloc(sizeof(t_fila));
			alunos[i].adjacentes->primeiro = NULL;
			alunos[i].adjacentes->ultimo = NULL;			
			for(k=0;k<strlen(alunos[i].lacos); k++){ /* para salvar em uma lista os alunos adjacentes*/
			
				
				if(alunos[i].lacos[k+1]<48 || alunos[i].lacos[k+1] > 57 ||alunos[i].lacos[k+1] == ' '  ){
					alunos[i].grau = alunos[i].grau  + 1;
					if(k!=0){


						if(alunos[i].lacos[k-1] >= 48 && alunos[i].lacos[k-1] <=57 ){
							
							num =(10 * (alunos[i].lacos[k-1] - 48)) + (alunos[i].lacos[k] - 48); /*transofrmar o valor da string de tabela ascii para inteiro*/
							Enfileirar(num, alunos[i].adjacentes);	/* pega o valor final e coloca nos adjacentes do aluno referido pelo indice;*/
						}
						else{
						
							num = alunos[i].lacos[k] - 48;		 /* transofrmar o valor da string de tabela ascii para inteiro;*/
							Enfileirar(num, alunos[i].adjacentes);	/* pega o valor final e coloca nos adjacentes do aluno referido pelo indice;*/
						}
					}
					else{
						num = alunos[i].lacos[k] - 48;		 /* transofrmar o valor da string de tabela ascii para inteiro;*/
						Enfileirar(num, alunos[i].adjacentes);	/* pega o valor final e coloca nos adjacentes do aluno referido pelo indice;*/

					}
				}
			}
		}

		i++;
	}	
	fclose(fp);
}


void imprimeAdjacentes ( t_aluno * f) {

	t_elemento * alvo;

	if(f->adjacentes == NULL){
		printf("HELLO DARKNESS MY ONLY FRIEND\n");
	}
	else{
		alvo = f->adjacentes->primeiro;
		while(alvo  != NULL){
		
			printf ("%d->", alvo->knot);
			alvo = alvo->proximo;

		}
		printf ("\\\n");
	}

}

int main(){
	t_aluno alunos[39];
	int i=0;	
	
	
	CriaGrafo(alunos);
	BubbleSort(alunos);	
	for(i=0;i<39;i++){
		imprimeAdjacentes(&alunos[i]);
	}



	return 0;
}	
 	
