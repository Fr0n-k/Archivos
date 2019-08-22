/*
 * To change this license header, choose License Headers in Project Properties.
 * To change this template file, choose Tools | Templates
 * and open the template in the editor.
 */

/* 
 * File:   main.c
 * Author: frank
 *
 * Created on 8 de agosto de 2019, 07:11 PM
 */

#include <sys/stat.h>
#include <sys/types.h>
#include <stdio.h>
#include <stdlib.h>
#include <fcntl.h>
#include <time.h>
#include <string.h>
#include <stdbool.h>

typedef struct {
    char Part_Status;
    char Part_Fit;
    int Part_Start;
    int Part_Size;
    int Part_Next;
    char Part_Type;
    char Part_Name[16];
} Particion; //Struct de una particion.

typedef struct {
    int MBR_Tamano;
    char MBR_Fecha_Creacion[20];
    int MBR_Disk_Signature;
    char Disk_Fit;
    Particion MBR_Partitions[4];
} MBR; //Struct del MBR.

typedef struct {
    char Part_Status;
    char Part_Fit;
    int Part_Start;
    int Part_Size;
    int Part_Next;
    char Part_Name[16];
} EBR; //Struct del EBR.

typedef struct {
    char Status;
    char Type;
    char Fit;
    int Start;
    int Size;
    char ID[5];
    char Name[16];
} Particion_Montada;

typedef struct {
    Particion_Montada Particiones[20];
    char Path[300];
    int letra;
} Disco_Montado;

Disco_Montado Memoria_Sistema[26];

int main() {
    Iniciar_Memoria();
    int Validacion_Salida = 0; //Valor que verifica si termino el programa.
    char Cadena_Entrada[500] = ""; // Cadena de entrada.

    while (Validacion_Salida != 1) { //Ciclo que verifica si se llego al comando de salida (exit).
        printf("---------------------------------------------------------------\n");
        printf("---------------- INGRESE EL COMANDO A EJECUTAR ----------------\n");
        printf("---------------------------------------------------------------\n");
        printf("\n");
        fgets(Cadena_Entrada, 500, stdin); //Captura la linea que se esta leyendo.
        Cambiar_Formato(Cadena_Entrada); //Cambia las letras mayusculas a minusculas. 
        Validacion_Salida = Ejecutar_Comando(Cadena_Entrada); //Ejecuta el comando y manda un valor a la validación.
    }
    printf("---------------------------------------------------------------\n");
    printf("----------------------- FIN DEL PROGRAMA ----------------------\n");
    printf("---------------------------------------------------------------\n");
    return 0;
}

void Cambiar_Formato(char Cadena_Entrada[]) {
    int Comilla_Activa = 0; //Variable que cambia si se encuentra una comilla (para no cambiar el formato innecesariamente).
    int Path_Activo = 0; //Variable que cambia si se encuentra una diagonal (para no cambiar el formato innecesariamente).
    int Posicion_Actual = 0; //Caracter actual en el cual esta el contador.

    while (Cadena_Entrada[Posicion_Actual] != NULL) { //Ciclo del recorrido de la cadena de entrada.
        if (Cadena_Entrada[Posicion_Actual] == 34 && Comilla_Activa == 0) { //Activa la bandera de comilla.
            Comilla_Activa = 1;
        } else if (Cadena_Entrada[Posicion_Actual] == 34 && Comilla_Activa == 1) { //Desactiva la bandera de comilla.
            Comilla_Activa = 0;
        }
        if (Cadena_Entrada[Posicion_Actual] == 47 && Path_Activo == 0) { //Activa la bandera de path.
            Path_Activo = 1;
        } else if (Cadena_Entrada[Posicion_Actual] == 32 && Path_Activo == 1) { //Desactiva la bandera de path.
            Path_Activo = 0;
        }

        if (Path_Activo == 1 && Comilla_Activa == 1) {
            Path_Activo == 0;
        }

        if ((Cadena_Entrada[Posicion_Actual] >= 65 && Cadena_Entrada[Posicion_Actual] <= 90) && Path_Activo == 0 && Comilla_Activa == 0) {
            Cadena_Entrada[Posicion_Actual] = Cadena_Entrada[Posicion_Actual] + 32; //Cambia la letra mayúscula por minúscula.
        }
        Posicion_Actual++;
    }
}

int Existencia_Path(char *Path) {
    int Resultado = open(Path, 0); //Abre el archivo dando un resultado numerico.
    if (Resultado < 0) { //Es negativo si existe.
        return 0;
    }
    return 1;
}

int Validar_Extension(char Path[], char Extension[]) {
    int Tamano_Extencion = strlen(Extension); //Tamaño del string de extension
    char Auxiliar[Tamano_Extencion + 1]; //Cadena auxiliar.
    int Posicion_Actual = 0; //Posicion en la cadena de texto.

    for (int i = strlen(Path) - Tamano_Extencion; i < strlen(Path); i++) { //For que empieza en la extencion.
        Auxiliar[Posicion_Actual] = Path[i];
        Posicion_Actual++;
    }
    Auxiliar[Posicion_Actual] = '\0';


    if (strcmp(Auxiliar, Extension) == 0) { //Verifica que los dos string sean iguales.
        return 0;
    }
    return -1;
}

int Generar_Archivo(char Path[]) {
    int Posicion_Actual = 0; //Posicion dentro del path.
    int Estado_Creacion = 0; //Resultado de la creacion.
    char Auxiliar[500] = {0}; //Cadena de texto auxiliar.

    if (Path[0] != 47) {// Verifica que el path empiece con /
        printf("---------------------------------------------------------------\n");
        printf("--------------- ERROR: EL PATH DADO NO ES VALIDO --------------\n");
        printf("---------------------------------------------------------------\n");
        printf("\n");
        return 0;
    }

    while (Path[Posicion_Actual] != NULL) {
        char Concatenacion[] = {Path[Posicion_Actual], '\0'}; //Extrae el caracter del path.
        strcat(Auxiliar, Concatenacion); //Copia el caracter al auxiliar.

        if (Path[Posicion_Actual] == 47) {
            Estado_Creacion = 0;
            if (Existencia_Path(Auxiliar) == 0) {
                Estado_Creacion = mkdir(Auxiliar, S_IRWXU | S_IRWXG | S_IRWXO); //Crea el disco.
            }

            if (Estado_Creacion == -1) { //Verifica que se creo con exito.
                return 0;
            }
        }
        Posicion_Actual++;
    }
    return 1;
}

int Confirmacion() {
    char Cadena_Entrada[500] = "";
    printf("---------------------------------------------------------------\n");
    printf("------------ ¿DESEA REALIZAR ESTA OPERACION? (S/N) ------------\n");
    printf("---------------------------------------------------------------\n");
    printf("\n");
    while (strcmp(Cadena_Entrada, "S\n") != 0 || strcmp(Cadena_Entrada, "s\n") != 0) {
        fgets(Cadena_Entrada, 500, stdin); //Captura la linea que se esta leyendo.
        if (strcmp(Cadena_Entrada, "S\n") == 0 || strcmp(Cadena_Entrada, "s\n") == 0) { //Verifica si se indico que si, cualquier otro valor no es valido.
            return 1;
        } else if (strcmp(Cadena_Entrada, "N\n") == 0 || strcmp(Cadena_Entrada, "n\n") == 0) {
            return 0;
            break;
        } else {
            printf("---------------------------------------------------------------\n");
            printf("----------- ERROR: VALOR INVALIDO, INTENTE DE NUEVO -----------\n");
            printf("---------------------------------------------------------------\n");
            printf("\n");
        }
    }
}

void Iniciar_Memoria() {
    for (int i = 0; i < 26; i++) {
        Memoria_Sistema[i].letra = -1;
        memset(&Memoria_Sistema[i].Path, 0, 300);
        for (int j = 0; j < 10; j++) {
            Memoria_Sistema[i].Particiones[j].Start = -1;
            Memoria_Sistema[i].Particiones[j].Size = -1;
            Memoria_Sistema[i].Particiones[j].Status = 'I';
            Memoria_Sistema[i].Particiones[j].Type = 'p';
            Memoria_Sistema[i].Particiones[j].Fit = 'f';
            memset(&Memoria_Sistema[i].Particiones[j].ID, 0, 5);
            memset(&Memoria_Sistema[i].Particiones[j].Name, 0, 16);
        }
    }
}

int Ejecutar_Comando(char Cadena_Entrada[]) {
    strcat(Cadena_Entrada, " ");
    int Posicion_Actual = 0;
    int Valor_Parametro = 0;
    int Comando_Encontrado = -1;
    char Auxiliar[1000] = {0};
    //BANDERAS ELEMENTOS ADICIONALES.
    int Existe_Igual = 0;
    int Existe_Comando = 0;
    int Existen_Comillas = -1;
    int Prioridad_OPF = 0;
    //VARIABLES PARA GURDAR DATOS.
    int Valor_Size = 0;
    char Valor_Fit = ' ';
    char Valor_Unit = ' ';
    char Valor_Path[500] = {0};
    char Valor_Type = ' ';
    char Valor_Delete[500] = {0};
    char Valor_Name[500] = {0};
    int Valor_Add = 0;
    char Valor_Id[500] = {0};
    //VARIABLES BANDERA
    int Existe_Size = 0;
    int Existe_Fit = 0;
    int Existe_Unit = 0;
    int Existe_Path = 0;
    int Existe_Type = 0;
    int Existe_Delete = 0;
    int Existe_Name = 0;
    int Existe_Add = 0;
    int Existe_Id = 0;

    while (Cadena_Entrada[Posicion_Actual] != NULL) {
        char Cadena_Auxiliar[] = {Cadena_Entrada[Posicion_Actual], '\0'};
        if (Cadena_Entrada[Posicion_Actual] == '\r') {
            Cadena_Entrada[Posicion_Actual] = ' '; //Quita el salto de linea.
        }

        if (Cadena_Entrada[Posicion_Actual] == 35) { //Se encontro un comentario.
            *Auxiliar = '\0';
            int Posicion_Auxiliar = 0;
            while (Cadena_Entrada[Posicion_Actual] != NULL) { //Sigue el ciclo hasta que termina la linea.
                Auxiliar[Posicion_Auxiliar] = Cadena_Entrada[Posicion_Actual];
                Posicion_Actual++;
                Posicion_Auxiliar++;
            }
            printf("---------------------------------------------------------------\n");
            printf("-------------------- COMENTARIO ENCONTRADO --------------------\n");
            printf("---------------------------------------------------------------\n");
            printf("\n");
            printf("CONTENIDO: %s. \n", Auxiliar); //Imprime el comentario.
            break;
        }

        if (((Cadena_Entrada[Posicion_Actual] == 32 || Cadena_Entrada[Posicion_Actual] == '\n') && Existen_Comillas == -1) || (Existen_Comillas == 1 && Cadena_Entrada[Posicion_Actual] == 34)) {
            if (Existen_Comillas == 1 && Cadena_Entrada[Posicion_Actual] == 34) {
                Existen_Comillas = -1;
            }
            if (Existe_Comando == 1) {
                if (Comando_Encontrado == -1) {
                    if (strcmp(Auxiliar, "exit") == 0) { //Se encontro el comando de salida.
                        Comando_Encontrado = 0;
                    } else if (strcmp(Auxiliar, "mkdisk") == 0) { //Se encontro el comando mkdisk.
                        Comando_Encontrado = 1;
                    } else if (strcmp(Auxiliar, "rmdisk") == 0) { //Se encontro el comando rmdisk.
                        Comando_Encontrado = 2;
                    } else if (strcmp(Auxiliar, "fdisk") == 0) { //Se encontro el comando fdisk.
                        Comando_Encontrado = 3;
                    } else if (strcmp(Auxiliar, "mount") == 0) { //Se encontro el comando mount.
                        Comando_Encontrado = 4;
                    } else if (strcmp(Auxiliar, "unmount") == 0) { //Se encontro el comando unmount.
                        Comando_Encontrado = 5;
                    } else if (strcmp(Auxiliar, "rep") == 0) { //Se encontro el comando de rep.
                        Comando_Encontrado = 6;
                    } else if (strcmp(Auxiliar, "exec") == 0) { //Se encontro el comando de exec.
                        Comando_Encontrado = 7;
                    } else { //Se encontro un valor inválido.
                        printf("---------------------------------------------------------------\n");
                        printf("-------------- ERROR: COMANDO INVALIDO ENCONTRADO -------------\n");
                        printf("---------------------------------------------------------------\n");
                        printf("\n");
                        printf("VALOR ERROR: %s. \n", Auxiliar);
                        return 0;
                    }
                    *Auxiliar = '\0';
                } else if (strcmp(Auxiliar, "") != 0) { //Asigna el valor encontrado al parematro que pertenece
                    if (Valor_Parametro == 1) {//Asignacion al parametro size.
                        if (Existe_Size == 1) {
                            printf("---------------------------------------------------------------\n");
                            printf("------ ERROR: YA HAY UN VALOR ASIGNADO AL PARAMETRO SIZE ------\n");
                            printf("---------------------------------------------------------------\n");
                            printf("\n");
                        } else {
                            Existe_Size = 1;
                            Valor_Size = atoi(Auxiliar);
                        }
                    } else if (Valor_Parametro == 2) {//Asignacion al parametro fit.
                        if (Existe_Fit == 1) {
                            printf("---------------------------------------------------------------\n");
                            printf("------- ERROR: YA HAY UN VALOR ASIGNADO AL PARAMETRO FIT ------\n");
                            printf("---------------------------------------------------------------\n");
                            printf("\n");
                        } else {
                            Existe_Fit = 1;
                            if (strcmp(Auxiliar, "bf") == 0) {
                                Valor_Fit = 'b';
                            } else if (strcmp(Auxiliar, "ff") == 0) {
                                Valor_Fit = 'f';
                            } else if (strcmp(Auxiliar, "wf") == 0) {
                                Valor_Fit = 'w';
                            } else {
                                printf("---------------------------------------------------------------\n");
                                printf("-------- ERROR: VALOR INCORRECTO PARA EL PARAMETRO FIT --------\n");
                                printf("---------------------------------------------------------------\n");
                                printf("\n");
                                printf("VALOR ERROR: %s. \n", Auxiliar);
                            }
                        }
                    } else if (Valor_Parametro == 3) {//Asignacion al parametro unit.
                        if (Existe_Unit == 1) {
                            printf("---------------------------------------------------------------\n");
                            printf("------ ERROR: YA HAY UN VALOR ASIGNADO AL PARAMETRO UNIT ------\n");
                            printf("---------------------------------------------------------------\n");
                            printf("\n");
                        } else {
                            Existe_Unit = 1;
                            if (strcmp(Auxiliar, "b") == 0) {
                                Valor_Unit = 'b';
                            } else if (strcmp(Auxiliar, "k") == 0) {
                                Valor_Unit = 'k';
                            } else if (strcmp(Auxiliar, "m") == 0) {
                                Valor_Unit = 'm';
                            } else {
                                printf("---------------------------------------------------------------\n");
                                printf("------- ERROR: VALOR INCORRECTO PARA EL PARAMETRO UNIT --------\n");
                                printf("---------------------------------------------------------------\n");
                                printf("\n");
                                printf("VALOR ERROR: %s. \n", Auxiliar);
                            }
                        }
                    } else if (Valor_Parametro == 4) {//Asignacion al parametro path.
                        if (Existe_Path == 1) {
                            printf("---------------------------------------------------------------\n");
                            printf("------ ERROR: YA HAY UN VALOR ASIGNADO AL PARAMETRO PATH ------\n");
                            printf("---------------------------------------------------------------\n");
                            printf("\n");
                        } else {
                            Existe_Path = 1;
                            strcpy(Valor_Path, &Auxiliar);
                            *Auxiliar = '\0';
                        }
                    } else if (Valor_Parametro == 5) {//Asignacion al parametro type.
                        if (Existe_Type == 1) {
                            printf("---------------------------------------------------------------\n");
                            printf("------ ERROR: YA HAY UN VALOR ASIGNADO AL PARAMETRO TYPE ------\n");
                            printf("---------------------------------------------------------------\n");
                            printf("\n");
                        } else {
                            Existe_Type = 1;
                            if (strcmp(Auxiliar, "p") == 0) {
                                Valor_Type = 'p';
                            } else if (strcmp(Auxiliar, "e") == 0) {
                                Valor_Type = 'e';
                            } else if (strcmp(Auxiliar, "l") == 0) {
                                Valor_Type = 'l';
                            } else {
                                printf("---------------------------------------------------------------\n");
                                printf("------- ERROR: VALOR INCORRECTO PARA EL PARAMETRO TYPE --------\n");
                                printf("---------------------------------------------------------------\n");
                                printf("\n");
                                printf("VALOR ERROR: %s. \n", Auxiliar);
                            }
                        }
                    } else if (Valor_Parametro == 6) {//Asignacion al parametro delete.
                        if (Existe_Delete == 1) {
                            printf("---------------------------------------------------------------\n");
                            printf("----- ERROR: YA HAY UN VALOR ASIGNADO AL PARAMETRO DELETE -----\n");
                            printf("---------------------------------------------------------------\n");
                            printf("\n");
                        } else {
                            Existe_Delete = 1;
                            if (strcmp(Auxiliar, "fast") == 0) {
                                strcpy(Valor_Delete, &Auxiliar);
                            } else if (strcmp(Auxiliar, "full") == 0) {
                                strcpy(Valor_Delete, &Auxiliar);
                            } else {
                                printf("---------------------------------------------------------------\n");
                                printf("------- ERROR: VALOR INCORRECTO PARA EL PARAMETRO DELETE ------\n");
                                printf("---------------------------------------------------------------\n");
                                printf("\n");
                                printf("VALOR ERROR: %s. \n", Auxiliar);
                            }

                            if (Valor_Delete != 0 && Prioridad_OPF == 0) {
                                Prioridad_OPF = 2;
                            }
                        }
                    } else if (Valor_Parametro == 7) {//Asignacion al parametro name.
                        if (Existe_Name == 1) {
                            printf("---------------------------------------------------------------\n");
                            printf("------ ERROR: YA HAY UN VALOR ASIGNADO AL PARAMETRO NAME ------\n");
                            printf("---------------------------------------------------------------\n");
                            printf("\n");
                        } else {
                            Existe_Name = 1;
                            strcpy(Valor_Name, &Auxiliar);
                            *Auxiliar = '\0';
                        }
                    } else if (Valor_Parametro == 8) {//Asignacion al parametro add.
                        if (Existe_Add == 1) {
                            printf("---------------------------------------------------------------\n");
                            printf("------- ERROR: YA HAY UN VALOR ASIGNADO AL PARAMETRO ADD ------\n");
                            printf("---------------------------------------------------------------\n");
                            printf("\n");
                        } else {
                            Existe_Add = 1;
                            Valor_Add = atoi(Auxiliar);
                            if (Valor_Add != 0 && Prioridad_OPF == 0) {
                                Prioridad_OPF = 1;
                            }
                        }
                    } else if (Valor_Parametro == 9) {//Asignacion al parametro id.
                        if (Existe_Id == 1) {
                            printf("---------------------------------------------------------------\n");
                            printf("-------- ERROR: YA HAY UN VALOR ASIGNADO AL PARAMETRO ID ------\n");
                            printf("---------------------------------------------------------------\n");
                            printf("\n");
                        } else {
                            Existe_Id = 1;
                            strcpy(Valor_Id, &Auxiliar);
                            *Auxiliar = '\0';
                        }
                    } else {
                        Valor_Parametro = 0;
                        printf("---------------------------------------------------------------\n");
                        printf("------------ ERROR: PARÁMETRO INVALIDO ENCONTRADO -------------\n");
                        printf("---------------------------------------------------------------\n");
                        printf("\n");
                        printf("VALOR ERROR: %s. \n", Auxiliar);
                        return 0;
                    }
                    Valor_Parametro = 0;
                    *Auxiliar = '\0';
                }
            }
        } else {
            Existe_Comando = 1;
            if (Cadena_Entrada[Posicion_Actual] == 34) {
                Existen_Comillas = 1;
            } else {
                strcat(Auxiliar, Cadena_Auxiliar);
                Cadena_Auxiliar[0] = '\0';
            }

            if (Existe_Igual == 1) { //Se pasa al valor del parametro encontrado.
                if (strcmp(Auxiliar, "-size==") == 0) {
                    Valor_Parametro = 1;
                } else if (strcmp(Auxiliar, "-fit==") == 0) {
                    Valor_Parametro = 2;
                } else if (strcmp(Auxiliar, "-unit==") == 0) {
                    Valor_Parametro = 3;
                } else if (strcmp(Auxiliar, "-path==") == 0) {
                    Valor_Parametro = 4;
                } else if (strcmp(Auxiliar, "-type==") == 0) {
                    Valor_Parametro = 5;
                } else if (strcmp(Auxiliar, "-delete==") == 0) {
                    Valor_Parametro = 6;
                } else if (strcmp(Auxiliar, "-name==") == 0) {
                    Valor_Parametro = 7;
                } else if (strcmp(Auxiliar, "-add==") == 0) {
                    Valor_Parametro = 8;
                } else if (strcmp(Auxiliar, "-id==") == 0) {
                    Valor_Parametro = 9;
                }

                if (Valor_Parametro != 0) {
                    Existe_Igual = 0;
                    *Auxiliar = '\0';
                }
            } else if (Cadena_Entrada[Posicion_Actual] == 61 && Existe_Igual == 0) { //Verifica que venga el simbolo '='.
                Existe_Igual = 1;
                Posicion_Actual--;
            }
        }
        Posicion_Actual++;
    }

    //Ejecución de comandos encontrados.
    if (Comando_Encontrado == 0) {//Ejecucion del comando exit.
        //Verifica que no tenga parametros.
        if (Existe_Size == 1 || Existe_Fit == 1 || Existe_Unit == 1 || Existe_Path == 1 || Existe_Type == 1 || Existe_Delete == 1 || Existe_Name == 1 || Existe_Add == 1 || Existe_Id == 1) {
            printf("---------------------------------------------------------------\n");
            printf("--  ERROR: HAY PARAMETROS QUE NO PERTENECEN AL COMANDO EXIT  --\n");
            printf("---------------------------------------------------------------\n");
            printf("\n");
        } else {
            printf("---------------------------------------------------------------\n");
            printf("----------------- COMANDO ENCONTRADO: 'EXIT'  -----------------\n");
            printf("---------------------------------------------------------------\n");
            printf("\n");
            return 1;
        }
    } else if (Comando_Encontrado == 1) { //Ejecucion del comando mkdisk.
        //verifica que no tenga parametros de otros comandos.
        if (Existe_Type == 1 || Existe_Delete == 1 || Existe_Name == 1 || Existe_Add == 1 || Existe_Id == 1) {
            printf("---------------------------------------------------------------\n");
            printf("-  ERROR: HAY PARAMETROS QUE NO PERTENECEN AL COMANDO MKDISK  -\n");
            printf("---------------------------------------------------------------\n");
            printf("\n");
        } else {
            if (Existe_Size == 0 || Existe_Path == 0) { //Verifica que existan los parametros obligatorios
                printf("---------------------------------------------------------------\n");
                printf("--  ERROR: FALTAN PARAMETROS OBLIGATORIOS DEL COMANDO MKDISK --\n");
                printf("---------------------------------------------------------------\n");
                printf("\n");

                if (Existe_Size == 0) { //Error por si solo no esta size.
                    printf("VALOR FALTANTE: SIZE. \n");
                }
                if (Existe_Path == 0) {//Error por si solo no esta path.
                    printf("VALOR FALTANTE: PATH. \n");
                }
            } else {
                if (Existe_Fit == 0) {//Por si  no esta fit.
                    Valor_Fit = 'f';
                }
                if (Existe_Unit == 0) {//Por si  no esta unit.
                    Valor_Unit = 'm';
                }
                printf("---------------------------------------------------------------\n");
                printf("---------------- COMANDO ENCONTRADO: 'MKDISK'  ----------------\n");
                printf("---------------------------------------------------------------\n");
                printf("\n");
                printf("VALORES: \n");
                printf("SIZE: %i. \n", Valor_Size);
                printf("FIT: %c. \n", Valor_Fit);
                printf("UNIT: %c. \n", Valor_Unit);
                printf("PATH: %s. \n", Valor_Path);
                printf("\n");

                if (Valor_Unit == 'm') {
                    Valor_Size = Valor_Size * (1024 * 1024);
                } else {
                    Valor_Size = Valor_Size * 1024;
                }

                MKDISK(Valor_Size, Valor_Fit, Valor_Path);
            }
        }

    } else if (Comando_Encontrado == 2) { //Ejecucion del comando rmdisk.
        //verifica que no tenga parametros de otros comandos.
        if (Existe_Size == 1 || Existe_Fit == 1 || Existe_Unit == 1 || Existe_Type == 1 || Existe_Delete == 1 || Existe_Name == 1 || Existe_Add == 1 || Existe_Id == 1) {
            printf("---------------------------------------------------------------\n");
            printf("-  ERROR: HAY PARAMETROS QUE NO PERTENECEN AL COMANDO RMDISK  -\n");
            printf("---------------------------------------------------------------\n");
            printf("\n");
        } else {
            if (Existe_Path == 0) { //Verifica que existan los parametros obligatorios
                printf("---------------------------------------------------------------\n");
                printf("--  ERROR: FALTAN PARAMETROS OBLIGATORIOS DEL COMANDO RKDISK --\n");
                printf("---------------------------------------------------------------\n");
                printf("\n");
                printf("VALOR FALTANTE: PATH. \n");
            } else {
                printf("---------------------------------------------------------------\n");
                printf("---------------- COMANDO ENCONTRADO: 'RMDISK'  ----------------\n");
                printf("---------------------------------------------------------------\n");
                printf("\n");
                printf("VALORES: \n");
                printf("PATH: %s. \n", Valor_Path);
                printf("\n");

                RMDISK(Valor_Path);
            }
        }
    } else if (Comando_Encontrado == 3) {
        //verifica que no tenga parametros de otros comandos.
        if (Existe_Id == 1) {
            printf("---------------------------------------------------------------\n");
            printf("-   ERROR: HAY PARAMETROS QUE NO PERTENECEN AL COMANDO FDISK  -\n");
            printf("---------------------------------------------------------------\n");
            printf("\n");
        } else {
            if ((Existe_Size == 0 && Prioridad_OPF == 0) || Existe_Path == 0 || Existe_Name == 0) { //Verifica que existan los parametros obligatorios
                printf("---------------------------------------------------------------\n");
                printf("--  ERROR: FALTAN PARAMETROS OBLIGATORIOS DEL COMANDO FDISK  --\n");
                printf("---------------------------------------------------------------\n");
                printf("\n");

                if (Existe_Size == 0) { //Error por si solo no esta size.
                    printf("VALOR FALTANTE: SIZE. \n");
                }
                if (Existe_Path == 0) {//Error por si solo no esta path.
                    printf("VALOR FALTANTE: PATH. \n");
                }
                if (Existe_Name == 0) {//Error por si solo no esta name.
                    printf("VALOR FALTANTE: NAME. \n");
                }
            } else {
                if (Existe_Unit == 0) {//Por si  no esta unit.
                    Valor_Unit = 'k';
                }
                if (Existe_Type == 0) {//Por si  no esta type.
                    Valor_Type = 'p';
                }
                if (Existe_Fit == 0) {//Por si  no esta fit.
                    Valor_Fit = 'w';
                }
                if (Existe_Delete == 0) {//Por si  no esta delete.
                    strcpy(Valor_Delete, "N/A");
                }
                printf("---------------------------------------------------------------\n");
                printf("----------------- COMANDO ENCONTRADO: 'FDISK'  ----------------\n");
                printf("---------------------------------------------------------------\n");
                printf("\n");
                printf("VALORES: \n");
                printf("SIZE: %i. \n", Valor_Size);
                printf("UNIT: %c. \n", Valor_Unit);
                printf("PATH: %s. \n", Valor_Path);
                printf("TYPE: %c. \n", Valor_Type);
                printf("FIT: %c. \n", Valor_Fit);
                printf("DELETE: %s. \n", Valor_Delete);
                printf("NAME: %s. \n", Valor_Name);
                printf("ADD: %i. \n", Valor_Add);
                printf("\n");

                if (Valor_Unit == 'k') {
                    Valor_Size = Valor_Size * 1024;
                    Valor_Add = Valor_Add * 1024;
                } else if (Valor_Unit == 'm') {
                    Valor_Size = Valor_Size * (1024 * 1024);
                    Valor_Add = Valor_Add * (1024 * 1024);
                }

                if (Prioridad_OPF != 0) {
                    Valor_Size = 1;
                }

                FDISK(Valor_Path, Valor_Name, Valor_Size, Valor_Type, Valor_Fit, Valor_Delete, Valor_Add, Prioridad_OPF);
            }
        }
    } else if (Comando_Encontrado == 4) {
        //verifica que no tenga parametros de otros comandos.
        if (Existe_Size == 1 || Existe_Fit == 1 || Existe_Unit == 1 || Existe_Type == 1 || Existe_Delete == 1 || Existe_Add == 1 || Existe_Id == 1) {
            printf("---------------------------------------------------------------\n");
            printf("--  ERROR: HAY PARAMETROS QUE NO PERTENECEN AL COMANDO MOUNT --\n");
            printf("---------------------------------------------------------------\n");
            printf("\n");
        } else {
            if (Existe_Path == 0 || Existe_Name == 0) { //Verifica que existan los parametros obligatorios
                printf("---------------------------------------------------------------\n");
                printf("--  ERROR: FALTAN PARAMETROS OBLIGATORIOS DEL COMANDO MOUNT  --\n");
                printf("---------------------------------------------------------------\n");
                printf("\n");

                if (Existe_Path == 0) {//Error por si solo no esta path.
                    printf("VALOR FALTANTE: PATH. \n");
                }
                if (Existe_Name == 0) {//Error por si solo no esta name.
                    printf("VALOR FALTANTE: NAME. \n");
                }
            } else {
                printf("---------------------------------------------------------------\n");
                printf("----------------- COMANDO ENCONTRADO: 'MOUNT'  ----------------\n");
                printf("---------------------------------------------------------------\n");
                printf("\n");
                printf("VALORES: \n");
                printf("PATH: %s. \n", Valor_Path);
                printf("NAME: %s. \n", Valor_Name);
                printf("\n");

                MOUNT(Valor_Path, Valor_Name);
                SHOW_MOUNT();
            }
        }
    } else if (Comando_Encontrado == 5) {
        //verifica que no tenga parametros de otros comandos.
        if (Existe_Size == 1 || Existe_Fit == 1 || Existe_Unit == 1 || Existe_Path == 1 || Existe_Type == 1 || Existe_Delete == 1 || Existe_Name == 1 || Existe_Add == 1) {
            printf("---------------------------------------------------------------\n");
            printf("-  ERROR: HAY PARAMETROS QUE NO PERTENECEN AL COMANDO UNMOUNT -\n");
            printf("---------------------------------------------------------------\n");
            printf("\n");
        } else {
            if (Existe_Id == 0) { //Verifica que existan los parametros obligatorios
                printf("---------------------------------------------------------------\n");
                printf("-  ERROR: FALTAN PARAMETROS OBLIGATORIOS DEL COMANDO UNMOUNT  -\n");
                printf("---------------------------------------------------------------\n");
                printf("\n");
                printf("VALOR FALTANTE: ID. \n");
            } else {
                printf("---------------------------------------------------------------\n");
                printf("---------------- COMANDO ENCONTRADO: 'UNMOUNT'  ---------------\n");
                printf("---------------------------------------------------------------\n");
                printf("\n");
                printf("VALORES: \n");
                printf("ID: %s. \n", Valor_Id);
                printf("\n");
            }

            UNMOUNT(Valor_Id);
            SHOW_MOUNT();
        }
    } else if (Comando_Encontrado == 6) {
        //verifica que no tenga parametros de otros comandos.
        if (Existe_Size == 1 || Existe_Fit == 1 || Existe_Unit == 1 || Existe_Type == 1 || Existe_Delete == 1 || Existe_Add == 1) {
            printf("---------------------------------------------------------------\n");
            printf("---  ERROR: HAY PARAMETROS QUE NO PERTENECEN AL COMANDO REP ---\n");
            printf("---------------------------------------------------------------\n");
            printf("\n");
        } else {
            if (Existe_Name == 0 || Existe_Path == 0 || Existe_Id == 0) { //Verifica que existan los parametros obligatorios
                printf("---------------------------------------------------------------\n");
                printf("---  ERROR: FALTAN PARAMETROS OBLIGATORIOS DEL COMANDO REP  ---\n");
                printf("---------------------------------------------------------------\n");
                printf("\n");

                if (Existe_Name == 0) { //Error por si solo no esta name.
                    printf("VALOR FALTANTE: Name. \n");
                }
                if (Existe_Path == 0) {//Error por si solo no esta path.
                    printf("VALOR FALTANTE: PATH. \n");
                }
                if (Existe_Id == 0) {//Error por si solo no esta id.
                    printf("VALOR FALTANTE: ID. \n");
                }
            } else {
                if (strcmp(Valor_Name, "mbr") != 0 && strcmp(Valor_Name, "disk") != 0) {//Verifica si el name del reporte es valido.
                    printf("---------------------------------------------------------------\n");
                    printf("-------- ERROR: VALOR INCORRECTO PARA EL PARAMETRO NAME -------\n");
                    printf("---------------------------------------------------------------\n");
                    printf("\n");
                    printf("VALOR ERROR: %s. \n", Valor_Name);
                } else {

                    printf("---------------------------------------------------------------\n");
                    printf("------------------ COMANDO ENCONTRADO: 'REP'  -----------------\n");
                    printf("---------------------------------------------------------------\n");
                    printf("\n");
                    printf("VALORES: \n");
                    printf("NAME: %s. \n", Valor_Name);
                    printf("PATH: %s. \n", Valor_Path);
                    printf("ID: %s. \n", Valor_Id);
                    printf("\n");

                    int Disco_Encontrado = -1;
                    char Path_Disco[500] = {0};

                    for (int i = 0; i < 26; i++) {
                        if (Memoria_Sistema[i].letra != -1) {
                            for (int j = 0; j < 10; j++) {
                                if (Memoria_Sistema[i].Particiones[j].Start != -1) {
                                    if (strcmp(Memoria_Sistema[i].Particiones[j].ID, Valor_Id) == 0) {
                                        strcpy(Path_Disco, Memoria_Sistema[i].Path);
                                        Disco_Encontrado = 1;
                                        break;
                                    }
                                }
                            }
                        }
                        if (Disco_Encontrado == 1) {
                            break;
                        }
                    }

                    char Comando_Consola[500] = {0};
                    char Extension_Archivo[100] = {0};
                    for (int i = 0; i < strlen(Valor_Path); i++) {
                        sprintf(Extension_Archivo, "%s%c", Extension_Archivo, Valor_Path[i]);
                        if (Valor_Path[i] == '.') {
                            sprintf(Extension_Archivo, "");
                        }
                    }

                    Generar_Archivo(Valor_Path);
                    char Path_Reporte[] = {"/home/Reportes/Cod_Reporte.dot"};
                    sprintf(Comando_Consola, "dot -T%s %s -o %s", Extension_Archivo, Path_Reporte, Valor_Path);

                    if (REP(Path_Reporte, Path_Disco, Valor_Name) == 1) {
                        if (Existencia_Path(Valor_Path) == 1) {
                            remove(Valor_Path);
                        }
                        system(Comando_Consola);

                        if (Existencia_Path(Valor_Path) == 1) {
                            printf("---------------------------------------------------------------\n");
                            printf("---------------  SE GENERO EL REPORTE CON EXITO  --------------\n");
                            printf("---------------------------------------------------------------\n");
                            printf("\n");
                        } else {
                            printf("---------------------------------------------------------------\n");
                            printf("-----------  ERROR: NO SE PUDO GENERAR EL REPORTE  ------------\n");
                            printf("---------------------------------------------------------------\n");
                            printf("\n");
                        }
                    } else {
                        printf("---------------------------------------------------------------\n");
                        printf("------------ ERROR: NO SE PUDO GENERAR EL REPORTE  ------------\n");
                        printf("---------------------------------------------------------------\n");
                        printf("\n");
                    }
                }
            }
        }
    } else if (Comando_Encontrado == 7) {
        //verifica que no tenga parametros de otros comandos.
        if (Existe_Size == 1 || Existe_Fit == 1 || Existe_Unit == 1 || Existe_Type == 1 || Existe_Delete == 1 || Existe_Name == 1 || Existe_Add == 1 || Existe_Id == 1) {
            printf("---------------------------------------------------------------\n");
            printf("-- ERROR: HAY PARAMETROS QUE NO PERTENECEN AL COMANDO EXEC   --\n");
            printf("---------------------------------------------------------------\n");
            printf("\n");
        } else {
            if (Existe_Path == 0) { //Verifica que existan los parametros obligatorios
                printf("---------------------------------------------------------------\n");
                printf("--- ERROR: FALTAN PARAMETROS OBLIGATORIOS DEL COMANDO EXEC  ---\n");
                printf("---------------------------------------------------------------\n");
                printf("\n");
                printf("VALOR FALTANTE: PATH. \n");
            } else {
                printf("---------------------------------------------------------------\n");
                printf("------------------ COMANDO ENCONTRADO: 'EXEC' -----------------\n");
                printf("---------------------------------------------------------------\n");
                printf("\n");
                printf("VALORES: \n");
                printf("PATH: %s. \n", Valor_Path);
                printf("\n");

                EXEC(Valor_Path);
            }
        }
    }
    return 0;
}

void MKDISK(int Size, char Fit, char Path[]) {
    if (Existencia_Path(Path) == 1) {
        printf("---------------------------------------------------------------\n");
        printf("------------ ERROR: EL PATH DADO YA TIENE UN DISCO -------------\n");
        printf("---------------------------------------------------------------\n");
        printf("\n");
    } else {
        Generar_Archivo(Path); //Genera un archivo plano.
        if (Validar_Extension(Path, ".disk") == 0) { //Verifica que la extension sea la correcta.
            FILE *Disco_Nuevo;
            Disco_Nuevo = fopen(Path, "wb");

            if (Disco_Nuevo == NULL) { //No existe el archivo por algun motivo.
                printf("---------------------------------------------------------------\n");
                printf("-------------- ERROR: NO SE PUDO CREAR EL DISCO ---------------\n");
                printf("---------------------------------------------------------------\n");
                printf("\n");
            } else {
                MBR MBR_Disco; //Estructura del MBR.
                //Asignacion de los valores del MBR para el nuevo disco.
                MBR_Disco.MBR_Disk_Signature = 201504325;
                MBR_Disco.MBR_Tamano = Size;
                MBR_Disco.Disk_Fit = Fit;
                time_t Hora_Actual = time(NULL);
                struct tm *fecha = localtime(&Hora_Actual);
                strftime(MBR_Disco.MBR_Fecha_Creacion, sizeof (MBR_Disco.MBR_Fecha_Creacion) - 1, "%d/%m/%y %H:%M:%S", fecha);

                for (int i = 0; i < 4; i++) { //Asigan particiones vacias para el MBR.
                    MBR_Disco.MBR_Partitions[i].Part_Status = 'I';
                    MBR_Disco.MBR_Partitions[i].Part_Fit = 'f';
                    MBR_Disco.MBR_Partitions[i].Part_Start = -1;
                    MBR_Disco.MBR_Partitions[i].Part_Size = 0;
                    MBR_Disco.MBR_Partitions[i].Part_Next = -1;
                    MBR_Disco.MBR_Partitions[i].Part_Type = 'p';
                    strcpy(MBR_Disco.MBR_Partitions[i].Part_Name, "");
                }
                fwrite(&MBR_Disco, sizeof (MBR_Disco), 1, Disco_Nuevo); //Escribe el MBR en el Path

                int Fin = (Size / 1024);
                char Buffer[1024];
                for (int i = 0; i < 1024; i++) {
                    Buffer[i] = '\0';
                }
                int j = 0;
                while (j != Fin) {
                    fwrite(&Buffer, 1024, 1, Disco_Nuevo);
                    j++;
                }

                fclose(Disco_Nuevo);
                printf("---------------------------------------------------------------\n");
                printf("----------------- SE CREO EL DISCO CON EXITO ------------------\n");
                printf("---------------------------------------------------------------\n");
                printf("\n");
            }
        } else {
            printf("---------------------------------------------------------------\n");
            printf("--------- ERROR: LA EXTENSION DEL DISCO ES INCORRECTA ---------\n");
            printf("---------------------------------------------------------------\n");
            printf("\n");
        }
    }
}

void RMDISK(char Path[]) {
    if (Validar_Extension(Path, ".disk") == 0) {
        if (Confirmacion() == 1) {
            int Eliminacion = remove(Path);
            if (Eliminacion == 0) {
                printf("---------------------------------------------------------------\n");
                printf("---------------- SE ELIMINO EL DISCO CON EXITO ----------------\n");
                printf("---------------------------------------------------------------\n");
                printf("\n");
            } else {
                printf("---------------------------------------------------------------\n");
                printf("------------ ERROR: EL DISCO NO PUDO SER ELIMINADO ------------\n");
                printf("---------------------------------------------------------------\n");
                printf("\n");
            }
        } else {
            printf("---------------------------------------------------------------\n");
            printf("------------- SE CANCELO LA ELIMINACION DEL DISCO -------------\n");
            printf("---------------------------------------------------------------\n");
            printf("\n");
        }
    } else {
        printf("---------------------------------------------------------------\n");
        printf("--------- ERROR: LA EXTENSION DEL DISCO ES INCORRECTA ---------\n");
        printf("---------------------------------------------------------------\n");
        printf("\n");
    }
}

void FDISK(char Path[], char Name[], int Size, char Type, char Fit, char Delete[], int Add, int Prioridad_Op) {
    if (Existencia_Path(Path) == 1) {
        if (Validar_Extension(Path, ".disk") == 0) {
            MBR MBR_Auxiliar;
            int Encontrado = 0;
            int Disk_Start = 140; //140 por el espacio que ocupa el MBR.
            int Fin_Particion = 0;
            FILE* Disco_Actual;
            Disco_Actual = fopen(Path, "rb+");
            if (Disco_Actual != NULL) {
                int Posicion_Auxiliar = -1;
                fread(&MBR_Auxiliar, sizeof (MBR_Auxiliar), 1, Disco_Actual);
                Fin_Particion = MBR_Auxiliar.MBR_Tamano;
                Particion Particion_Auxiliar;
                int Particion_Encontrada = -1;
                int Extendida_Encontrada = -1;
                int Numero_Primarias = 0;

                for (int i = 0; i < 4; i++) { //Recorrido de las particiones para ver si esta ya existe.
                    if (MBR_Auxiliar.MBR_Partitions[i].Part_Start != -1) {
                        //Verifica si el nombre coincide
                        if (strcmp(Name, MBR_Auxiliar.MBR_Partitions[i].Part_Name) == 0) {
                            Posicion_Auxiliar = i;
                            Particion_Encontrada = 1;
                        }
                    }

                    //Valida si la particion es extendida.
                    if (MBR_Auxiliar.MBR_Partitions[i].Part_Type == 'e') {
                        Extendida_Encontrada = 1;
                        Particion_Auxiliar = MBR_Auxiliar.MBR_Partitions[i];
                    }

                    //Valida si es una particion primaria y que esta si exista.
                    if (MBR_Auxiliar.MBR_Partitions[i].Part_Type == 'p' && MBR_Auxiliar.MBR_Partitions[i].Part_Start != -1) {
                        Numero_Primarias++;
                    }
                }

                //En caso de que no se halla encontrado pero exista una particion extendida
                if (Particion_Encontrada == -1 && Extendida_Encontrada == 1) {
                    int EBR_Encontrado = 0;
                    EBR EBR_Auxiliar; //Primer particion logica.
                    EBR EBR_Siguiente; //Para el resto.
                    fseek(Disco_Actual, Particion_Auxiliar.Part_Start, SEEK_SET);
                    fread(&EBR_Auxiliar, sizeof (EBR_Auxiliar), 1, Disco_Actual);
                    EBR_Siguiente = EBR_Auxiliar;

                    if (EBR_Auxiliar.Part_Start != -1 || EBR_Auxiliar.Part_Next != -1) {
                        if (strcmp(EBR_Auxiliar.Part_Name, Name) == 0) { //Se Busca en la primer particion logica
                            Particion_Encontrada = 2;
                            EBR_Encontrado = 1;
                        } else {
                            while (EBR_Auxiliar.Part_Next != -1) { //Recorre el resto de las particiones Logicas
                                fseek(Disco_Actual, EBR_Auxiliar.Part_Next, SEEK_SET);
                                EBR_Siguiente = EBR_Auxiliar;
                                fread(&EBR_Auxiliar, sizeof (EBR_Auxiliar), 1, Disco_Actual);
                                if (strcmp(EBR_Auxiliar.Part_Name, Name) == 0) { //Se encontro la particion como logica
                                    Particion_Encontrada = 2;
                                    EBR_Encontrado = 1;
                                    break;
                                }
                            }
                        }

                    }
                    
                    if (Type == 'l' || Particion_Encontrada == 2) {
                        if (Particion_Encontrada != -1) {
                            if (Prioridad_Op == 0) {
                                printf("---------------------------------------------------------------\n");
                                printf("------------ ERROR: YA EXISTE ESA PARTICION LOGICA  -----------\n");
                                printf("---------------------------------------------------------------\n");
                                printf("\n");
                            } else if (Prioridad_Op == 1) { //Add
                                if (Confirmacion() == 1) {
                                    int Fin_EBR = EBR_Auxiliar.Part_Next;
                                    if (EBR_Auxiliar.Part_Next == -1) {
                                        Fin_EBR = Particion_Auxiliar.Part_Start + Particion_Auxiliar.Part_Size;
                                    }

                                    int Bit_Fin_EBR = EBR_Auxiliar.Part_Size + Add;
                                    int Antiguo_Size = 0;
                                    if (EBR_Auxiliar.Part_Start + Bit_Fin_EBR < Fin_EBR && Bit_Fin_EBR > 32) {
                                        Antiguo_Size = EBR_Auxiliar.Part_Size;
                                        EBR_Auxiliar.Part_Size = Bit_Fin_EBR;
                                        fseek(Disco_Actual, EBR_Auxiliar.Part_Start, SEEK_SET);
                                        fwrite(&EBR_Auxiliar, sizeof (EBR_Auxiliar), 1, Disco_Actual);
                                        printf("---------------------------------------------------------------\n");
                                        printf("------- SE CAMBIO CON EXITO EL TAMAÑO DE LA PARTICION ---------\n");
                                        printf("---------------------------------------------------------------\n");
                                        printf("\n");
                                        printf("VALOR ANTIGUO: %i .\n", Antiguo_Size);
                                        printf("VALOR NUEVO: %i .\n", Bit_Fin_EBR);
                                    } else {
                                        printf("---------------------------------------------------------------\n");
                                        printf("-- ERROR: NO SE PUEDE USAR EL COMANDO ADD EN ESTA PARTICION ---\n");
                                        printf("---------------------------------------------------------------\n");
                                        printf("\n");
                                    }
                                } else {
                                    printf("---------------------------------------------------------------\n");
                                    printf("-------------------- SE CANCELO LA OPERACION ------------------\n");
                                    printf("---------------------------------------------------------------\n");
                                    printf("\n");
                                }
                            } else if (Prioridad_Op == 2) {
                                int principio = EBR_Auxiliar.Part_Start;
                                int sizeReal = EBR_Auxiliar.Part_Size;
                                if (strcmp(Delete, "full") == 0) {
                                    if (Confirmacion() == 1) {
                                        if (EBR_Auxiliar.Part_Start == EBR_Siguiente.Part_Start) {
                                            principio = principio + 32;
                                            sizeReal = sizeReal - 32;
                                            fseek(Disco_Actual, EBR_Siguiente.Part_Start, SEEK_SET);
                                            EBR_Siguiente.Part_Status = 'I';
                                            EBR_Siguiente.Part_Start = -1;
                                            EBR_Siguiente.Part_Size = -1;
                                            memset(&EBR_Siguiente.Part_Name, 0, sizeof (EBR_Siguiente.Part_Name));
                                            fwrite(&EBR_Siguiente, sizeof (EBR_Siguiente), 1, Disco_Actual);
                                            printf("---------------------------------------------------------------\n");
                                            printf("----- SE ELIMINO LA PARTICION LOGICA DE MANERA COMPLETA -------\n");
                                            printf("---------------------------------------------------------------\n");
                                            printf("\n");
                                        } else {
                                            EBR_Siguiente.Part_Next = EBR_Auxiliar.Part_Next;
                                            fseek(Disco_Actual, EBR_Siguiente.Part_Start, SEEK_SET);
                                            fwrite(&EBR_Siguiente, sizeof (EBR_Siguiente), 1, Disco_Actual);
                                            printf("---------------------------------------------------------------\n");
                                            printf("----- SE ELIMINO LA PARTICION LOGICA DE MANERA COMPLETA -------\n");
                                            printf("---------------------------------------------------------------\n");
                                            printf("\n");
                                        }

                                        fseek(Disco_Actual, principio, SEEK_SET);
                                        int sizeMax = 1024 * 1024;
                                        if (sizeReal > sizeMax) {
                                            char insert[1024 * 1024] = {0};
                                            while (sizeReal > sizeMax) {
                                                sizeReal = sizeReal - sizeMax;
                                                fwrite(insert, sizeof (char), sizeMax, Disco_Actual);
                                            }
                                        }

                                        char ultimoInsert[sizeReal];
                                        for (int i = 0; i < sizeReal; i++) {
                                            ultimoInsert[i] = '\0';
                                        }
                                        fwrite(&ultimoInsert, sizeof (char), sizeReal, Disco_Actual);
                                    } else {
                                        printf("---------------------------------------------------------------\n");
                                        printf("-------------------- SE CANCELO LA OPERACION ------------------\n");
                                        printf("---------------------------------------------------------------\n");
                                        printf("\n");
                                    }
                                } else if (strcmp(Delete, "fast") == 0) {
                                    if (Confirmacion() == 1) {
                                        if (EBR_Auxiliar.Part_Start == EBR_Siguiente.Part_Start) {
                                            fseek(Disco_Actual, EBR_Siguiente.Part_Start, SEEK_SET);
                                            EBR_Siguiente.Part_Status = 'I';
                                            EBR_Siguiente.Part_Start = -1;
                                            EBR_Siguiente.Part_Size = -1;
                                            memset(&EBR_Siguiente.Part_Name, 0, sizeof (EBR_Siguiente.Part_Name));
                                            fwrite(&EBR_Siguiente, sizeof (EBR_Siguiente), 1, Disco_Actual);
                                            printf("---------------------------------------------------------------\n");
                                            printf("------ SE ELIMINO LA PARTICION LOGICA DE MANERA RAPIDA --------\n");
                                            printf("---------------------------------------------------------------\n");
                                            printf("\n");
                                        } else {
                                            EBR_Siguiente.Part_Next = EBR_Auxiliar.Part_Next;
                                            fseek(Disco_Actual, EBR_Siguiente.Part_Start, SEEK_SET);
                                            fwrite(&EBR_Siguiente, sizeof (EBR_Siguiente), 1, Disco_Actual);
                                            printf("---------------------------------------------------------------\n");
                                            printf("------ SE ELIMINO LA PARTICION LOGICA DE MANERA RAPIDA --------\n");
                                            printf("---------------------------------------------------------------\n");
                                            printf("\n");
                                        }
                                    } else {
                                        printf("---------------------------------------------------------------\n");
                                        printf("-------------------- SE CANCELO LA OPERACION ------------------\n");
                                        printf("---------------------------------------------------------------\n");
                                        printf("\n");
                                    }
                                }
                            }
                        } else {
                            EBR Primera_Disponible;
                            EBR Disponilbe_Auxiliar;
                            fseek(Disco_Actual, Particion_Auxiliar.Part_Start, SEEK_SET);
                            fread(&Primera_Disponible, sizeof (Primera_Disponible), 1, Disco_Actual);
                            if (Primera_Disponible.Part_Next != -1 || Primera_Disponible.Part_Start != -1) {
                                char Fit_Extendida = Particion_Auxiliar.Part_Fit;
                                int Exito_Logica = -1;

                                if (Fit_Extendida == 'f') {
                                    while (Primera_Disponible.Part_Next != -1) {
                                        if (Primera_Disponible.Part_Next - (Primera_Disponible.Part_Start + Primera_Disponible.Part_Size) > 0) {
                                            if (Size <= Primera_Disponible.Part_Next - (Primera_Disponible.Part_Start + Primera_Disponible.Part_Size)) {
                                                EBR EBR_Nuevo;
                                                if (Primera_Disponible.Part_Start == -1 && Primera_Disponible.Part_Size == -1) {
                                                    EBR_Nuevo.Part_Start = Particion_Auxiliar.Part_Start;
                                                } else {
                                                    EBR_Nuevo.Part_Start = Primera_Disponible.Part_Size + Primera_Disponible.Part_Start;
                                                }

                                                EBR_Nuevo.Part_Fit = Fit;
                                                EBR_Nuevo.Part_Next = Primera_Disponible.Part_Next;
                                                EBR_Nuevo.Part_Size = Size;
                                                EBR_Nuevo.Part_Status = 'A';
                                                memset(&EBR_Nuevo.Part_Name, 0, sizeof (EBR_Nuevo.Part_Name));
                                                strcpy(&EBR_Nuevo.Part_Name, Name);

                                                if (Primera_Disponible.Part_Start != -1) {
                                                    Primera_Disponible.Part_Next = EBR_Nuevo.Part_Start;
                                                    fseek(Disco_Actual, Primera_Disponible.Part_Start, SEEK_SET);
                                                    fwrite(&Primera_Disponible, sizeof (Primera_Disponible), 1, Disco_Actual);
                                                    fseek(Disco_Actual, EBR_Nuevo.Part_Start, SEEK_SET);
                                                    fwrite(&EBR_Nuevo, sizeof (EBR_Nuevo), 1, Disco_Actual);
                                                    Exito_Logica = 1;
                                                    printf("---------------------------------------------------------------\n");
                                                    printf("----------- SE CREO LA PARTICION LOGICA CON EXITO -------------\n");
                                                    printf("---------------------------------------------------------------\n");
                                                    printf("\n");
                                                    break;
                                                } else {
                                                    fseek(Disco_Actual, EBR_Nuevo.Part_Start, SEEK_SET);
                                                    fwrite(&EBR_Nuevo, sizeof (EBR_Nuevo), 1, Disco_Actual);
                                                    Exito_Logica = 1;
                                                    printf("---------------------------------------------------------------\n");
                                                    printf("----------- SE CREO LA PARTICION LOGICA CON EXITO -------------\n");
                                                    printf("---------------------------------------------------------------\n");
                                                    printf("\n");
                                                    break;
                                                }
                                            }
                                        }

                                        if (Primera_Disponible.Part_Next == -1) {
                                            EBR EBR_Nuevo;
                                            EBR_Nuevo.Part_Start = Primera_Disponible.Part_Size + Primera_Disponible.Part_Start;
                                            EBR_Nuevo.Part_Fit = Fit;
                                            EBR_Nuevo.Part_Next = -1;
                                            EBR_Nuevo.Part_Size = Size;
                                            EBR_Nuevo.Part_Status = 'A';
                                            memset(&EBR_Nuevo.Part_Name, 0, sizeof (EBR_Nuevo.Part_Name));
                                            strcpy(&EBR_Nuevo.Part_Name, Name);
                                            Primera_Disponible.Part_Next = EBR_Nuevo.Part_Start;
                                            fseek(Disco_Actual, Primera_Disponible.Part_Start, SEEK_SET);
                                            fwrite(&Primera_Disponible, sizeof (Primera_Disponible), 1, Disco_Actual);
                                            fseek(Disco_Actual, EBR_Nuevo.Part_Start, SEEK_SET);
                                            fwrite(&EBR_Nuevo, sizeof (EBR_Nuevo), 1, Disco_Actual);
                                            Exito_Logica = 1;
                                            printf("---------------------------------------------------------------\n");
                                            printf("----------- SE CREO LA PARTICION LOGICA CON EXITO -------------\n");
                                            printf("---------------------------------------------------------------\n");
                                            printf("\n");
                                            break;
                                        }

                                        Disponilbe_Auxiliar = Primera_Disponible;
                                        fseek(Disco_Actual, Primera_Disponible.Part_Next, SEEK_SET);
                                        fread(&Primera_Disponible, sizeof (Primera_Disponible), 1, Disco_Actual);
                                    }

                                    if (Primera_Disponible.Part_Next == -1 && Exito_Logica == -1) {
                                        EBR EBR_Nuevo;
                                        EBR_Nuevo.Part_Start = Primera_Disponible.Part_Size + Primera_Disponible.Part_Start;
                                        EBR_Nuevo.Part_Fit = Fit;
                                        EBR_Nuevo.Part_Next = -1;
                                        EBR_Nuevo.Part_Size = Size;
                                        EBR_Nuevo.Part_Status = 'A';
                                        memset(&EBR_Nuevo.Part_Name, 0, sizeof (EBR_Nuevo.Part_Name));
                                        strcpy(&EBR_Nuevo.Part_Name, Name);
                                        Primera_Disponible.Part_Next = EBR_Nuevo.Part_Start;
                                        fseek(Disco_Actual, Primera_Disponible.Part_Start, SEEK_SET);
                                        fwrite(&Primera_Disponible, sizeof (Primera_Disponible), 1, Disco_Actual);
                                        fseek(Disco_Actual, EBR_Nuevo.Part_Start, SEEK_SET);
                                        fwrite(&EBR_Nuevo, sizeof (EBR_Nuevo), 1, Disco_Actual);
                                        printf("---------------------------------------------------------------\n");
                                        printf("----------- SE CREO LA PARTICION LOGICA CON EXITO -------------\n");
                                        printf("---------------------------------------------------------------\n");
                                        printf("\n");
                                    }
                                } else if (Fit_Extendida == 'b') {
                                    EBR Best_Fit;
                                    Best_Fit.Part_Next = 999999999;
                                    Best_Fit.Part_Size = 0;
                                    Best_Fit.Part_Start = 0;
                                    while (Primera_Disponible.Part_Next != -1) {
                                        if (Primera_Disponible.Part_Next - (Primera_Disponible.Part_Start + Primera_Disponible.Part_Size) > 0) {
                                            if (Size <= Primera_Disponible.Part_Next - (Primera_Disponible.Part_Start + Primera_Disponible.Part_Size)) {
                                                if ((Primera_Disponible.Part_Next - Primera_Disponible.Part_Start - Primera_Disponible.Part_Size) < (Best_Fit.Part_Next - Best_Fit.Part_Start - Best_Fit.Part_Size)) {
                                                    Best_Fit = Primera_Disponible;
                                                    Exito_Logica = 1;
                                                }
                                            }
                                        }

                                        if (Primera_Disponible.Part_Next == -1) {
                                            //Insertar al final
                                            if (Size <= Particion_Auxiliar.Part_Start + Particion_Auxiliar.Part_Size - Primera_Disponible.Part_Start - Primera_Disponible.Part_Size) {
                                                //Espacio libre entre la ultima particion y el final de la extendida
                                                if ((Particion_Auxiliar.Part_Start + Particion_Auxiliar.Part_Size - Primera_Disponible.Part_Start - Primera_Disponible.Part_Size) < (Best_Fit.Part_Next - Best_Fit.Part_Start - Best_Fit.Part_Size)) {
                                                    //Ultima particion logica
                                                    Best_Fit = Primera_Disponible;
                                                    Exito_Logica = 1;
                                                }
                                            }
                                        }

                                        Disponilbe_Auxiliar = Primera_Disponible;
                                        fseek(Disco_Actual, Primera_Disponible.Part_Next, SEEK_SET);
                                        fread(&Primera_Disponible, sizeof (Primera_Disponible), 1, Disco_Actual);
                                    }

                                    if (Primera_Disponible.Part_Next == -1) {
                                        if (Size <= Particion_Auxiliar.Part_Start + Particion_Auxiliar.Part_Size - Primera_Disponible.Part_Start - Primera_Disponible.Part_Size) {
                                            if ((Particion_Auxiliar.Part_Start + Particion_Auxiliar.Part_Size - Primera_Disponible.Part_Start - Primera_Disponible.Part_Size) < (Best_Fit.Part_Next - Best_Fit.Part_Start - Best_Fit.Part_Size)) {
                                                Best_Fit = Primera_Disponible;
                                                Exito_Logica = 1;
                                            }
                                        }
                                    }

                                    if (Exito_Logica == 1) {
                                        EBR EBR_Nuevo;
                                        EBR_Nuevo.Part_Start = Best_Fit.Part_Start + Best_Fit.Part_Size;
                                        EBR_Nuevo.Part_Fit = Fit;
                                        EBR_Nuevo.Part_Status = 'A';
                                        EBR_Nuevo.Part_Size = Size;
                                        EBR_Nuevo.Part_Next = Best_Fit.Part_Next;
                                        memset(&EBR_Nuevo.Part_Name, 0, sizeof (EBR_Nuevo.Part_Name));
                                        strcpy(&EBR_Nuevo.Part_Name, Name);
                                        Best_Fit.Part_Next = EBR_Nuevo.Part_Start;
                                        fseek(Disco_Actual, Best_Fit.Part_Start, SEEK_SET);
                                        fwrite(&Best_Fit, sizeof (Best_Fit), 1, Disco_Actual);
                                        fseek(Disco_Actual, EBR_Nuevo.Part_Start, SEEK_SET);
                                        fwrite(&EBR_Nuevo, sizeof (EBR_Nuevo), 1, Disco_Actual);
                                        printf("---------------------------------------------------------------\n");
                                        printf("----------- SE CREO LA PARTICION LOGICA CON EXITO -------------\n");
                                        printf("---------------------------------------------------------------\n");
                                        printf("\n");
                                    }
                                } else if (Fit_Extendida == 'w') {
                                    EBR Worst_Fit;
                                    Worst_Fit.Part_Next = 0;
                                    Worst_Fit.Part_Size = 0;
                                    Worst_Fit.Part_Start = 0;
                                    while (Primera_Disponible.Part_Next != -1) {
                                        if (Primera_Disponible.Part_Next - (Primera_Disponible.Part_Start + Primera_Disponible.Part_Size) > 0) {
                                            if (Size <= Primera_Disponible.Part_Next - (Primera_Disponible.Part_Start + Primera_Disponible.Part_Size)) {
                                                if ((Primera_Disponible.Part_Next - Primera_Disponible.Part_Start - Primera_Disponible.Part_Size) > (Worst_Fit.Part_Next - Worst_Fit.Part_Start - Worst_Fit.Part_Size)) {
                                                    Worst_Fit = Primera_Disponible;
                                                    Exito_Logica = 1;
                                                }
                                            }
                                        }

                                        if (Primera_Disponible.Part_Next == -1) {
                                            if (Size <= Particion_Auxiliar.Part_Start + Particion_Auxiliar.Part_Size - Primera_Disponible.Part_Start - Primera_Disponible.Part_Size) {
                                                if ((Particion_Auxiliar.Part_Start + Particion_Auxiliar.Part_Size - Primera_Disponible.Part_Start - Primera_Disponible.Part_Size) > (Worst_Fit.Part_Next - Worst_Fit.Part_Start - Worst_Fit.Part_Size)) {
                                                    Worst_Fit = Primera_Disponible;
                                                    Exito_Logica = 1;
                                                }
                                            }
                                        }

                                        Disponilbe_Auxiliar = Primera_Disponible;
                                        fseek(Disco_Actual, Primera_Disponible.Part_Next, SEEK_SET);
                                        fread(&Primera_Disponible, sizeof (Primera_Disponible), 1, Disco_Actual);
                                    }

                                    if (Primera_Disponible.Part_Next == -1) {
                                        if (Size <= Particion_Auxiliar.Part_Start + Particion_Auxiliar.Part_Size - Primera_Disponible.Part_Start - Primera_Disponible.Part_Size) {
                                            if ((Particion_Auxiliar.Part_Start + Particion_Auxiliar.Part_Size - Primera_Disponible.Part_Start - Primera_Disponible.Part_Size) > (Worst_Fit.Part_Next - Worst_Fit.Part_Start - Worst_Fit.Part_Size)) {
                                                Worst_Fit = Primera_Disponible;
                                                Exito_Logica = 1;
                                            }
                                        }
                                    }

                                    if (Exito_Logica == 1) {
                                        EBR EBR_Nuevo;
                                        EBR_Nuevo.Part_Start = Worst_Fit.Part_Start + Worst_Fit.Part_Size;
                                        EBR_Nuevo.Part_Fit = Fit;
                                        EBR_Nuevo.Part_Status = 'A';
                                        EBR_Nuevo.Part_Size = Size;
                                        EBR_Nuevo.Part_Next = Worst_Fit.Part_Next;
                                        memset(&EBR_Nuevo.Part_Name, 0, sizeof (EBR_Nuevo.Part_Name));
                                        strcpy(&EBR_Nuevo.Part_Name, Name);
                                        Worst_Fit.Part_Next = EBR_Nuevo.Part_Start;
                                        fseek(Disco_Actual, Worst_Fit.Part_Start, SEEK_SET);
                                        fwrite(&Worst_Fit, sizeof (Worst_Fit), 1, Disco_Actual);
                                        fseek(Disco_Actual, EBR_Nuevo.Part_Start, SEEK_SET);
                                        fwrite(&EBR_Nuevo, sizeof (EBR_Nuevo), 1, Disco_Actual);
                                        printf("---------------------------------------------------------------\n");
                                        printf("----------- SE CREO LA PARTICION LOGICA CON EXITO -------------\n");
                                        printf("---------------------------------------------------------------\n");
                                        printf("\n");
                                    }
                                }
                            } else {
                                int tope_EBR = Particion_Auxiliar.Part_Size;
                                if (Primera_Disponible.Part_Next != -1) {
                                    tope_EBR = Primera_Disponible.Part_Next;
                                }

                                if (Size > tope_EBR) {
                                    printf("---------------------------------------------------------------\n");
                                    printf("---- ERROR: NO HAY ESPACIO PARA CREAR LA PARTICION LOGICA  ----\n");
                                    printf("---------------------------------------------------------------\n");
                                    printf("\n");
                                } else {
                                    EBR EBR_Nuevo;
                                    EBR_Nuevo.Part_Start = Particion_Auxiliar.Part_Start;
                                    EBR_Nuevo.Part_Fit = Fit;
                                    EBR_Nuevo.Part_Next = -1;
                                    EBR_Nuevo.Part_Size = Size;
                                    EBR_Nuevo.Part_Status = 'A';
                                    memset(&EBR_Nuevo.Part_Name, 0, sizeof (EBR_Nuevo.Part_Name));
                                    strcpy(&EBR_Nuevo.Part_Name, Name);
                                    fseek(Disco_Actual, Particion_Auxiliar.Part_Start, SEEK_SET);
                                    fwrite(&EBR_Nuevo, sizeof (EBR_Nuevo), 1, Disco_Actual);
                                    printf("---------------------------------------------------------------\n");
                                    printf("----------- SE CREO LA PARTICION LOGICA CON EXITO -------------\n");
                                    printf("---------------------------------------------------------------\n");
                                    printf("\n");
                                }
                            }
                        }
                    }
                }

                if (Type == 'l' && Extendida_Encontrada != 1) { //Se tiene una particion logica pero no una extendida.
                    printf("---------------------------------------------------------------\n");
                    printf("---------- ERROR: NO EXISTE UNA PARTICION EXTENDIDA -----------\n");
                    printf("---------------------------------------------------------------\n");
                    printf("\n");
                } else if (Particion_Encontrada == -1 && Type != 'l') {//Se quiere crear una particion primaria o extendida
                    if (Size > 0) {
                        char Fit_Disco = MBR_Auxiliar.Disk_Fit;
                        int Posicion_Particion = -1;

                        if (Fit_Disco == 'f') { //Firs Fit
                            for (int i = 0; i < 4; i++) {
                                Posicion_Particion = i;
                                if (MBR_Auxiliar.MBR_Partitions[i].Part_Start == -1) { //Encontro la primara particion libre
                                    break;
                                }

                                if (i == 0) {
                                    int Particion_Disponilbe = MBR_Auxiliar.MBR_Partitions[i].Part_Start - 140;
                                    if (Particion_Disponilbe >= Size) {
                                        Disk_Start = 140;
                                        Encontrado = 1;
                                    }
                                } else {
                                    int Particion_Disponilbe = MBR_Auxiliar.MBR_Partitions[i].Part_Start - MBR_Auxiliar.MBR_Partitions[i - 1].Part_Start - MBR_Auxiliar.MBR_Partitions[i - 1].Part_Size;
                                    if (Particion_Disponilbe >= Size) {
                                        Disk_Start = MBR_Auxiliar.MBR_Partitions[i - 1].Part_Start + MBR_Auxiliar.MBR_Partitions[i - 1].Part_Size;
                                        Encontrado = 1;
                                    }
                                }

                                if (Encontrado == 0) {
                                    Disk_Start = MBR_Auxiliar.MBR_Partitions[i].Part_Start + MBR_Auxiliar.MBR_Partitions[i].Part_Size;
                                }
                            }
                        } else if (Fit_Disco == 'b') {//Best Fit
                            int Posiciones_MBR_Partitions[5] = {0};
                            int Espacios_MBR_Partitions[5] = {0};
                            for (int i = 0; i < 4; i++) {
                                Posicion_Particion = i;
                                if (MBR_Auxiliar.MBR_Partitions[i].Part_Start == -1) {
                                    break;
                                }

                                if (i == 0) {
                                    int disponible = MBR_Auxiliar.MBR_Partitions[i].Part_Start - 140;
                                    Espacios_MBR_Partitions[i] = disponible;
                                    Posiciones_MBR_Partitions[i] = i;
                                    if (MBR_Auxiliar.MBR_Partitions[i + 1].Part_Start == -1) {
                                        Espacios_MBR_Partitions[i + 1] = Fin_Particion - MBR_Auxiliar.MBR_Partitions[i].Part_Start - MBR_Auxiliar.MBR_Partitions[i].Part_Size;
                                        Posiciones_MBR_Partitions[i + 1] = i + 1;
                                    } else {
                                        Espacios_MBR_Partitions[i + 1] = MBR_Auxiliar.MBR_Partitions[i + 1].Part_Start - MBR_Auxiliar.MBR_Partitions[i].Part_Start - MBR_Auxiliar.MBR_Partitions[i].Part_Size;
                                        Posiciones_MBR_Partitions[i + 1] = i + 1;
                                    }
                                } else {
                                    int disponible = MBR_Auxiliar.MBR_Partitions[i].Part_Start - MBR_Auxiliar.MBR_Partitions[i - 1].Part_Start - MBR_Auxiliar.MBR_Partitions[i - 1].Part_Size;
                                    Espacios_MBR_Partitions[i] = disponible;
                                    Posiciones_MBR_Partitions[i] = i;
                                    if (MBR_Auxiliar.MBR_Partitions[i + 1].Part_Start == -1 || i == 3) {
                                        Espacios_MBR_Partitions[i + 1] = Fin_Particion - MBR_Auxiliar.MBR_Partitions[i].Part_Start - MBR_Auxiliar.MBR_Partitions[i].Part_Size;
                                        Posiciones_MBR_Partitions[i + 1] = i + 1;
                                    } else {
                                        Espacios_MBR_Partitions[i + 1] = MBR_Auxiliar.MBR_Partitions[i + 1].Part_Start - MBR_Auxiliar.MBR_Partitions[i].Part_Start - MBR_Auxiliar.MBR_Partitions[i].Part_Size;
                                        Posiciones_MBR_Partitions[i + 1] = i + 1;
                                    }
                                }
                            }

                            int Posicion_Ajuste = -1;
                            int Best_Fit = 999999999;
                            for (int i = 0; i < 5; i++) {
                                if (Espacios_MBR_Partitions[i] < Best_Fit && Espacios_MBR_Partitions[i] >= Size) {
                                    Best_Fit = Espacios_MBR_Partitions[i];
                                    Posicion_Ajuste = i;
                                }
                            }

                            if (Posicion_Ajuste != -1) {
                                if (Posicion_Ajuste == 0) {
                                    Disk_Start = 140;
                                } else if (Posicion_Ajuste == 1) {
                                    Disk_Start = MBR_Auxiliar.MBR_Partitions[0].Part_Start + MBR_Auxiliar.MBR_Partitions[0].Part_Size;
                                } else if (Posicion_Ajuste == 2) {
                                    Disk_Start = MBR_Auxiliar.MBR_Partitions[1].Part_Start + MBR_Auxiliar.MBR_Partitions[1].Part_Size;
                                } else if (Posicion_Ajuste == 3) {
                                    Disk_Start = MBR_Auxiliar.MBR_Partitions[2].Part_Start + MBR_Auxiliar.MBR_Partitions[2].Part_Size;
                                } else { //posAjuste == 4
                                    Disk_Start = MBR_Auxiliar.MBR_Partitions[3].Part_Start + MBR_Auxiliar.MBR_Partitions[3].Part_Size;
                                }
                            } else {
                                if (Posicion_Particion == 0 && MBR_Auxiliar.MBR_Partitions[Posicion_Particion].Part_Start == -1) {
                                    Disk_Start = 140;
                                } else {
                                    if (MBR_Auxiliar.MBR_Partitions[Posicion_Particion].Part_Start == -1) {
                                        Disk_Start = MBR_Auxiliar.MBR_Partitions[Posicion_Particion - 1].Part_Start + MBR_Auxiliar.MBR_Partitions[Posicion_Particion - 1].Part_Size;
                                    } else {
                                        Disk_Start = MBR_Auxiliar.MBR_Partitions[Posicion_Particion].Part_Start + MBR_Auxiliar.MBR_Partitions[Posicion_Particion].Part_Size;
                                    }
                                }
                            }
                        } else if (Fit_Disco == 'w') {//Worst Fit
                            int Posiciones_MBR_Partitions[5] = {0};
                            int Espacios_MBR_Partitions[5] = {0};
                            for (int i = 0; i < 4; i++) {
                                Posicion_Particion = i;
                                if (MBR_Auxiliar.MBR_Partitions[i].Part_Start == -1) {
                                    //Encontro particion vacia
                                    break;
                                }

                                if (i == 0) {
                                    int disponible = MBR_Auxiliar.MBR_Partitions[i].Part_Start - 140;
                                    Espacios_MBR_Partitions[i] = disponible;
                                    Posiciones_MBR_Partitions[i] = i;
                                    if (MBR_Auxiliar.MBR_Partitions[i + 1].Part_Start == -1) {
                                        Espacios_MBR_Partitions[i + 1] = Fin_Particion - MBR_Auxiliar.MBR_Partitions[i].Part_Start - MBR_Auxiliar.MBR_Partitions[i].Part_Size;
                                        Posiciones_MBR_Partitions[i + 1] = i + 1;
                                    } else {
                                        Espacios_MBR_Partitions[i + 1] = MBR_Auxiliar.MBR_Partitions[i + 1].Part_Start - MBR_Auxiliar.MBR_Partitions[i].Part_Start - MBR_Auxiliar.MBR_Partitions[i].Part_Size;
                                        Posiciones_MBR_Partitions[i + 1] = i + 1;
                                    }
                                } else {
                                    int disponible = MBR_Auxiliar.MBR_Partitions[i].Part_Start - MBR_Auxiliar.MBR_Partitions[i - 1].Part_Start - MBR_Auxiliar.MBR_Partitions[i - 1].Part_Size;
                                    Espacios_MBR_Partitions[i] = disponible;
                                    Posiciones_MBR_Partitions[i] = i;
                                    if (MBR_Auxiliar.MBR_Partitions[i + 1].Part_Start == -1 || i == 3) {
                                        Espacios_MBR_Partitions[i + 1] = Fin_Particion - MBR_Auxiliar.MBR_Partitions[i].Part_Start - MBR_Auxiliar.MBR_Partitions[i].Part_Size;
                                        Posiciones_MBR_Partitions[i + 1] = i + 1;
                                    } else {
                                        Espacios_MBR_Partitions[i + 1] = MBR_Auxiliar.MBR_Partitions[i + 1].Part_Start - MBR_Auxiliar.MBR_Partitions[i].Part_Start - MBR_Auxiliar.MBR_Partitions[i].Part_Size;
                                        Posiciones_MBR_Partitions[i + 1] = i + 1;
                                    }
                                }
                            }

                            int Posicion_Ajuste = -1;
                            int Worst_fit = 0;
                            for (int i = 0; i < 5; i++) {
                                if (Espacios_MBR_Partitions[i] > Worst_fit && Espacios_MBR_Partitions[i] >= Size) {
                                    Worst_fit = Espacios_MBR_Partitions[i];
                                    Posicion_Ajuste = i;
                                }
                            }

                            if (Posicion_Ajuste != -1) {
                                if (Posicion_Ajuste == 0) {
                                    Disk_Start = 140;
                                } else if (Posicion_Ajuste == 1) {
                                    Disk_Start = MBR_Auxiliar.MBR_Partitions[0].Part_Start + MBR_Auxiliar.MBR_Partitions[0].Part_Size;
                                } else if (Posicion_Ajuste == 2) {
                                    Disk_Start = MBR_Auxiliar.MBR_Partitions[1].Part_Start + MBR_Auxiliar.MBR_Partitions[1].Part_Size;
                                } else if (Posicion_Ajuste == 3) {
                                    Disk_Start = MBR_Auxiliar.MBR_Partitions[2].Part_Start + MBR_Auxiliar.MBR_Partitions[2].Part_Size;
                                } else {
                                    Disk_Start = MBR_Auxiliar.MBR_Partitions[3].Part_Start + MBR_Auxiliar.MBR_Partitions[3].Part_Size;
                                }
                            } else {
                                if (Posicion_Particion == 0 && MBR_Auxiliar.MBR_Partitions[Posicion_Particion].Part_Start == -1) {
                                    Disk_Start = 140;
                                } else {
                                    if (MBR_Auxiliar.MBR_Partitions[Posicion_Particion].Part_Start == -1) {
                                        Disk_Start = MBR_Auxiliar.MBR_Partitions[Posicion_Particion - 1].Part_Start + MBR_Auxiliar.MBR_Partitions[Posicion_Particion - 1].Part_Size;
                                    } else {
                                        Disk_Start = MBR_Auxiliar.MBR_Partitions[Posicion_Particion].Part_Start + MBR_Auxiliar.MBR_Partitions[Posicion_Particion].Part_Size;
                                    }
                                }
                            }
                        }

                        if (Posicion_Particion == 4) {
                            printf("---------------------------------------------------------------\n");
                            printf("--- ERROR: EL DISCO YA TIENE TODAS SUS PARTICIONES CREADAS ----\n");
                            printf("---------------------------------------------------------------\n");
                            printf("\n");
                        } else {
                            if (Fin_Particion >= Disk_Start + Size) {
                                if ((Numero_Primarias == 4 && Type == 'p') || (Numero_Primarias == 3 && Extendida_Encontrada == 1)) {
                                    printf("---------------------------------------------------------------\n");
                                    printf("--- ERROR: EL DISCO YA TIENE TODAS SUS PARTICIONES CREADAS ----\n");
                                    printf("---------------------------------------------------------------\n");
                                    printf("\n");
                                } else {
                                    if (Extendida_Encontrada == 1 && Type == 'e') {
                                        printf("---------------------------------------------------------------\n");
                                        printf("------ ERROR: EL DISCO YA TIENE UNA PARTICION EXTENDIDA -------\n");
                                        printf("---------------------------------------------------------------\n");
                                        printf("\n");
                                    } else {
                                        MBR_Auxiliar.MBR_Partitions[Posicion_Particion].Part_Fit = Fit;
                                        MBR_Auxiliar.MBR_Partitions[Posicion_Particion].Part_Size = Size;
                                        memset(&MBR_Auxiliar.MBR_Partitions[Posicion_Particion].Part_Name, 0, sizeof (MBR_Auxiliar.MBR_Partitions[Posicion_Particion].Part_Name));
                                        strcpy(&MBR_Auxiliar.MBR_Partitions[Posicion_Particion].Part_Name, Name);
                                        MBR_Auxiliar.MBR_Partitions[Posicion_Particion].Part_Start = Disk_Start;
                                        MBR_Auxiliar.MBR_Partitions[Posicion_Particion].Part_Status = 'A';
                                        MBR_Auxiliar.MBR_Partitions[Posicion_Particion].Part_Type = Type;

                                        if (Type == 'e') {
                                            EBR EBR_Auxiliar;
                                            EBR_Auxiliar.Part_Start = -1;
                                            EBR_Auxiliar.Part_Next = -1;
                                            EBR_Auxiliar.Part_Status = 'I';
                                            fseek(Disco_Actual, Disk_Start, SEEK_SET);
                                            fwrite(&EBR_Auxiliar, sizeof (EBR_Auxiliar), 1, Disco_Actual);
                                        }

                                        printf("---------------------------------------------------------------\n");
                                        printf("--------------- SE CREO LA PARTICION CON EXITO ----------------\n");
                                        printf("---------------------------------------------------------------\n");
                                        printf("\n");
                                    }
                                }
                            } else {
                                printf("---------------------------------------------------------------\n");
                                printf("------- ERROR: EL TAMAÑO DE LA PARTICION ES INSUFICIENTE ------\n");
                                printf("---------------------------------------------------------------\n");
                                printf("\n");
                            }
                        }
                    } else {
                        printf("---------------------------------------------------------------\n");
                        printf("---------- ERROR: EL SIZE NO PUEDE SER 0 O NEGATIVO -----------\n");
                        printf("---------------------------------------------------------------\n");
                        printf("\n");
                    }
                } else if (Particion_Encontrada == 1) {//La particion ya existe.
                    if (Prioridad_Op == 0) { //Se queria crear
                        printf("---------------------------------------------------------------\n");
                        printf("-------- ERROR: LA PARTICION YA EXISTE EN ESTE DISCO ----------\n");
                        printf("---------------------------------------------------------------\n");
                        printf("\n");
                    } else if (Prioridad_Op == 1) { //se queria realizar un add
                        if (Confirmacion() == 1) {
                            int Inicio_Espacio_Libre = 0;
                            int Fin_Espacio_Libre = 0;
                            for (int i = 0; i < 4; i++) {
                                if (MBR_Auxiliar.MBR_Partitions[i].Part_Start + MBR_Auxiliar.MBR_Partitions[i].Part_Size + 1 != MBR_Auxiliar.MBR_Partitions[i + 1].Part_Start) {
                                    if (Inicio_Espacio_Libre == 0) {
                                        Inicio_Espacio_Libre = MBR_Auxiliar.MBR_Partitions[i + 1].Part_Start - MBR_Auxiliar.MBR_Partitions[i].Part_Start + MBR_Auxiliar.MBR_Partitions[i].Part_Size;
                                        Fin_Espacio_Libre = MBR_Auxiliar.MBR_Partitions[i + 1].Part_Start;
                                    } else {
                                        if (Inicio_Espacio_Libre < MBR_Auxiliar.MBR_Partitions[i + 1].Part_Start - MBR_Auxiliar.MBR_Partitions[i].Part_Start + MBR_Auxiliar.MBR_Partitions[i].Part_Size) {
                                            Inicio_Espacio_Libre = MBR_Auxiliar.MBR_Partitions[i + 1].Part_Start - MBR_Auxiliar.MBR_Partitions[i].Part_Start + MBR_Auxiliar.MBR_Partitions[i].Part_Size;
                                            Fin_Espacio_Libre = MBR_Auxiliar.MBR_Partitions[i + 1].Part_Start;
                                        }
                                    }
                                }
                            }
                            if (Add > 0) {
                                if (Fin_Espacio_Libre - Inicio_Espacio_Libre == 0) {
                                    if (MBR_Auxiliar.MBR_Tamano != MBR_Auxiliar.MBR_Partitions[3].Part_Start + MBR_Auxiliar.MBR_Partitions[3].Part_Size) {

                                    } else {
                                        printf("---------------------------------------------------------------\n");
                                        printf("---- ERROR: NO HAY ESPACIO SUFICIENTE PARA REALIZAR EL ADD ----\n");
                                        printf("---------------------------------------------------------------\n");
                                        printf("\n");
                                    }
                                } else {
                                    if ((Fin_Espacio_Libre - Inicio_Espacio_Libre) < Add) {
                                        printf("---------------------------------------------------------------\n");
                                        printf("---- ERROR: NO HAY ESPACIO SUFICIENTE PARA REALIZAR EL ADD ----\n");
                                        printf("---------------------------------------------------------------\n");
                                        printf("\n");
                                    } else {
                                        int Size_Pasado = MBR_Auxiliar.MBR_Partitions[Posicion_Auxiliar].Part_Size;
                                        MBR_Auxiliar.MBR_Partitions[Posicion_Auxiliar].Part_Size = MBR_Auxiliar.MBR_Partitions[Posicion_Auxiliar].Part_Size + Add;
                                        printf("---------------------------------------------------------------\n");
                                        printf("------- SE CAMBIO CON EXITO EL TAMAÑO DE LA PARTICION ---------\n");
                                        printf("---------------------------------------------------------------\n");
                                        printf("\n");
                                        printf("VALOR ANTIGUO: %i .\n", Size_Pasado);
                                        printf("VALOR NUEVO: %i .\n", MBR_Auxiliar.MBR_Partitions[Posicion_Auxiliar].Part_Size);
                                        for (int i = 0; i < 4; i++) {
                                            if (MBR_Auxiliar.MBR_Partitions[i].Part_Start > (Fin_Espacio_Libre)) {
                                                MBR_Auxiliar.MBR_Partitions[i].Part_Start = MBR_Auxiliar.MBR_Partitions[i].Part_Start - (Fin_Espacio_Libre - Inicio_Espacio_Libre);
                                            }
                                        }
                                    }
                                }
                            } else {
                                if (MBR_Auxiliar.MBR_Partitions[Posicion_Auxiliar].Part_Size + Add <= 0) {
                                    printf("---------------------------------------------------------------\n");
                                    printf("-------- ERROR: EL TAMAÑO RESTANTE DEBE SER MAYOR A 0  --------\n");
                                    printf("---------------------------------------------------------------\n");
                                    printf("\n");
                                } else {
                                    int Size_Pasado = MBR_Auxiliar.MBR_Partitions[Posicion_Auxiliar].Part_Size;
                                    MBR_Auxiliar.MBR_Partitions[Posicion_Auxiliar].Part_Size = MBR_Auxiliar.MBR_Partitions[Posicion_Auxiliar].Part_Size + Add;
                                    printf("---------------------------------------------------------------\n");
                                    printf("------- SE CAMBIO CON EXITO EL TAMAÑO DE LA PARTICION ---------\n");
                                    printf("---------------------------------------------------------------\n");
                                    printf("\n");
                                    printf("VALOR ANTIGUO: %i .\n", Size_Pasado);
                                    printf("VALOR NUEVO: %i .\n", MBR_Auxiliar.MBR_Partitions[Posicion_Auxiliar].Part_Size);
                                }
                            }
                        } else {
                            printf("---------------------------------------------------------------\n");
                            printf("-------------------- SE CANCELO LA OPERACION ------------------\n");
                            printf("---------------------------------------------------------------\n");
                            printf("\n");
                        }
                    } else { //Se queria realizar un delete
                        if (strcmp(Delete, "full") == 0) { //full
                            if (Confirmacion() == 1) {
                                fseek(Disco_Actual, MBR_Auxiliar.MBR_Partitions[Posicion_Auxiliar].Part_Start, SEEK_SET);
                                int sizeMax = 1024 * 1024;
                                int sizeExtendida = MBR_Auxiliar.MBR_Partitions[Posicion_Auxiliar].Part_Size;

                                if (sizeExtendida > sizeMax) {
                                    char insert[1024 * 1024] = {0};
                                    while (sizeExtendida > sizeMax) {
                                        sizeExtendida = sizeExtendida - sizeMax;
                                        fwrite(insert, sizeof (char), sizeMax, Disco_Actual);
                                    }
                                }

                                char ultimoInsert[sizeExtendida];
                                for (int i = 0; i < sizeExtendida; i++) {
                                    ultimoInsert[i] = '\0';
                                }

                                fwrite(&ultimoInsert, sizeof (char), sizeExtendida, Disco_Actual);
                                MBR_Auxiliar.MBR_Partitions[Posicion_Auxiliar].Part_Start = -1;
                                MBR_Auxiliar.MBR_Partitions[Posicion_Auxiliar].Part_Status = 'I';
                                memset(&MBR_Auxiliar.MBR_Partitions[Posicion_Auxiliar].Part_Name, 0, sizeof (MBR_Auxiliar.MBR_Partitions[Posicion_Auxiliar].Part_Name));

                                printf("---------------------------------------------------------------\n");
                                printf("--------- SE ELIMINO LA PARTICION DE MANERA COMPLETA ----------\n");
                                printf("---------------------------------------------------------------\n");
                                printf("\n");
                            } else {
                                printf("---------------------------------------------------------------\n");
                                printf("-------------------- SE CANCELO LA OPERACION ------------------\n");
                                printf("---------------------------------------------------------------\n");
                                printf("\n");
                            }
                        } else if (strcmp(Delete, "fast") == 0) {
                            if (Confirmacion() == 1) {
                                MBR_Auxiliar.MBR_Partitions[Posicion_Auxiliar].Part_Start = -1;
                                MBR_Auxiliar.MBR_Partitions[Posicion_Auxiliar].Part_Status = 'I';
                                memset(&MBR_Auxiliar.MBR_Partitions[Posicion_Auxiliar].Part_Name, 0, sizeof (MBR_Auxiliar.MBR_Partitions[Posicion_Auxiliar].Part_Name));

                                printf("---------------------------------------------------------------\n");
                                printf("---------- SE ELIMINO LA PARTICION DE MANERA RAPIDA -----------\n");
                                printf("---------------------------------------------------------------\n");
                                printf("\n");
                            } else {
                                printf("---------------------------------------------------------------\n");
                                printf("-------------------- SE CANCELO LA OPERACION ------------------\n");
                                printf("---------------------------------------------------------------\n");
                                printf("\n");
                            }
                        }
                    }
                }

                //Ciclo que ordena las particiones. (Ordenamiendo tipo Burbuja)
                Particion Particion_Pivote;
                for (int i = 1; i < 4; i++) {
                    for (int j = 0; j < 4 - i; j++) {
                        if (((MBR_Auxiliar.MBR_Partitions[j].Part_Start > MBR_Auxiliar.MBR_Partitions[j + 1].Part_Start) && MBR_Auxiliar.MBR_Partitions[j + 1].Part_Start != -1) || MBR_Auxiliar.MBR_Partitions[j].Part_Start == -1) {
                            Particion_Pivote = MBR_Auxiliar.MBR_Partitions[j + 1];
                            MBR_Auxiliar.MBR_Partitions[j + 1] = MBR_Auxiliar.MBR_Partitions[j];
                            MBR_Auxiliar.MBR_Partitions[j] = Particion_Pivote;
                        }
                    }
                }

                fseek(Disco_Actual, 0, SEEK_SET);
                fwrite(&MBR_Auxiliar, sizeof (MBR_Auxiliar), 1, Disco_Actual); //Guarda los cambios
                fclose(Disco_Actual);
            } else {
                printf("---------------------------------------------------------------\n");
                printf("-------------- ERROR: NO SE PUDO ABRIR EL DISCO ---------------\n");
                printf("---------------------------------------------------------------\n");
                printf("\n");
            }
        } else {
            printf("---------------------------------------------------------------\n");
            printf("--------- ERROR: LA EXTENSION DEL DISCO ES INCORRECTA ---------\n");
            printf("---------------------------------------------------------------\n");
            printf("\n");
        }
    } else {
        printf("---------------------------------------------------------------\n");
        printf("------------------ ERROR: EL DISCO NO EXISTE ------------------\n");
        printf("---------------------------------------------------------------\n");
        printf("\n");
    }
}

void MOUNT(char Path[], char Name[]) {
    if (Existencia_Path(Path) == 1 && Validar_Extension(Path, ".disk") == 0) {
        int Posicion_Disco = -1;
        int Existencia_Montura = -1;

        for (int i = 0; i < 26; i++) {
            if (strcmp(Memoria_Sistema[i].Path, Path) == 0) { //Verifica si el nuevo path ya tiene alguna particion montada
                Existencia_Montura = 1;
                Posicion_Disco = i;
                break;
            }

            if (Memoria_Sistema[i].letra == -1) { //encontro un espacio libre
                Posicion_Disco = i;
                break;
            }
        }

        if (Existencia_Montura == 1) {
            FILE *Disco_Actual;
            Disco_Actual = fopen(Path, "rb+");

            if (Disco_Actual != NULL) { //Abre el archivo del path (if en caso de que el path tenga formato correcto pero este mal escrito)
                int Posicion_Vacia = -1;
                int Encontro_Particion = -1;
                for (int i = 0; i < 20; i++) {
                    if (strcmp(Memoria_Sistema[Posicion_Disco].Particiones[i].Name, Name) == 0) { //Mira si ya hay una particion montada con ese nombre.
                        Encontro_Particion = 1;
                        break;
                    }

                    if (Memoria_Sistema[Posicion_Disco].Particiones[i].Start == -1) { //Encuentra una posicion vacia.
                        Posicion_Vacia = i;
                        break;
                    }
                }

                MBR MBR_Auxiliar;
                fread(&MBR_Auxiliar, sizeof (MBR_Auxiliar), 1, Disco_Actual);
                Particion Particion_Disco;
                int Posicion_Particion = -1;
                int Posicion_Extendida = -1;

                for (int i = 0; i < 4; i++) {
                    if (MBR_Auxiliar.MBR_Partitions[i].Part_Start != -1) {
                        if (strcmp(MBR_Auxiliar.MBR_Partitions[i].Part_Name, Name) == 0) { //Encuentra la particion en el archivo binario
                            Particion_Disco = MBR_Auxiliar.MBR_Partitions[i];
                            Posicion_Particion = i;
                            break;
                        }

                        if (MBR_Auxiliar.MBR_Partitions[i].Part_Type == 'e') { //Verifica si es extendida
                            Posicion_Extendida = i;
                        }
                    }
                }

                if (Encontro_Particion != 1) { //No se ha montado la particion.
                    if (Posicion_Particion != -1) { //se encontro la particion en el disco.
                        if (Particion_Disco.Part_Type != 'l') { //La particion no es logica.
                            if (Posicion_Vacia < 20) { //Aun hay espacio para montar.
                                Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Vacia].Status = Particion_Disco.Part_Status;
                                Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Vacia].Type = Particion_Disco.Part_Type;
                                Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Vacia].Fit = Particion_Disco.Part_Fit;
                                Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Vacia].Start = Particion_Disco.Part_Start;
                                Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Vacia].Size = Particion_Disco.Part_Size;
                                Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Vacia].ID[0] = 'v';
                                Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Vacia].ID[1] = 'd';
                                Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Vacia].ID[2] = (char) Memoria_Sistema[Posicion_Disco].letra;
                                Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Vacia].ID[3] = (char) 48 + Posicion_Vacia + 1;
                                Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Vacia].ID[4] = '\0';
                                strcat(&Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Vacia].Name, &Particion_Disco.Part_Name);
                                printf("---------------------------------------------------------------\n");
                                printf("--------------- SE MONTO LA PARTICION CON EXITO ---------------\n");
                                printf("---------------------------------------------------------------\n");
                                printf("\n");
                            } else {
                                printf("---------------------------------------------------------------\n");
                                printf("------------ ERROR: NO SE PUDO MONTAR LA PARTICION ------------\n");
                                printf("---------------------------------------------------------------\n");
                                printf("\n");
                            }
                        } else {
                            printf("---------------------------------------------------------------\n");
                            printf("------------ ERROR: NO SE PUDO MONTAR LA PARTICION ------------\n");
                            printf("---------------------------------------------------------------\n");
                            printf("\n");
                        }
                    } else {
                        if (Posicion_Extendida != -1) {
                            EBR EBR_Auxiliar;
                            int Existe_Logica = -1;
                            fseek(Disco_Actual, MBR_Auxiliar.MBR_Partitions[Posicion_Extendida].Part_Start, SEEK_SET);
                            fread(&EBR_Auxiliar, sizeof (EBR_Auxiliar), 1, Disco_Actual);

                            if (strcmp(EBR_Auxiliar.Part_Name, Name) == 0) { //Intenta encontrar en la primera posicion.
                                Existe_Logica = 1;
                            }

                            while (EBR_Auxiliar.Part_Next != -1) { //Busca hasta que ya no existan mas particiones logicas.
                                if (strcmp(EBR_Auxiliar.Part_Name, Name) == 0) {
                                    Existe_Logica = 1;
                                    break;
                                }
                                fseek(Disco_Actual, EBR_Auxiliar.Part_Next, SEEK_SET);
                                fread(&EBR_Auxiliar, sizeof (EBR_Auxiliar), 1, Disco_Actual);
                            }

                            if (Existe_Logica == 1) {
                                Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Vacia].Status = EBR_Auxiliar.Part_Status;
                                Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Vacia].Type = 'l';
                                Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Vacia].Fit = EBR_Auxiliar.Part_Fit;
                                Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Vacia].Start = EBR_Auxiliar.Part_Start;
                                Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Vacia].Size = EBR_Auxiliar.Part_Size;
                                Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Vacia].ID[0] = 'v';
                                Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Vacia].ID[1] = 'd';
                                Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Vacia].ID[2] = (char) Memoria_Sistema[Posicion_Disco].letra;
                                Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Vacia].ID[3] = (char) 48 + Posicion_Vacia + 1;
                                Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Vacia].ID[4] = '\0';
                                strcat(&Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Vacia].Name, &EBR_Auxiliar.Part_Name);
                                printf("---------------------------------------------------------------\n");
                                printf("--------------- SE MONTO LA PARTICION CON EXITO ---------------\n");
                                printf("---------------------------------------------------------------\n");
                                printf("\n");
                            } else {
                                printf("---------------------------------------------------------------\n");
                                printf("------------ ERROR: NO SE PUDO MONTAR LA PARTICION ------------\n");
                                printf("---------------------------------------------------------------\n");
                                printf("\n");
                            }
                        } else {
                            printf("---------------------------------------------------------------\n");
                            printf("------------ ERROR: NO SE PUDO MONTAR LA PARTICION ------------\n");
                            printf("---------------------------------------------------------------\n");
                            printf("\n");
                        }
                    }
                } else {
                    printf("---------------------------------------------------------------\n");
                    printf("-------------- ERROR: LA PARTICION YA ESTA MONTADA ------------\n");
                    printf("---------------------------------------------------------------\n");
                    printf("\n");
                }
            } else {
                printf("---------------------------------------------------------------\n");
                printf("------------- ERROR: NO SE PUDO INGRESAR AL DISCO -------------\n");
                printf("---------------------------------------------------------------\n");
                printf("\n");
            }
        } else { //Mismo proceso pero se agrega un nuevo disco al sistema.
            FILE *Disco_Actual;
            Disco_Actual = fopen(Path, "rb+");
            if (Disco_Actual != NULL) {
                int Posicion_Particion = -1;
                for (int i = 0; i < 10; i++) {
                    if (Memoria_Sistema[Posicion_Disco].Particiones[i].Start == -1) {
                        Posicion_Particion = i;
                        break;
                    }
                }

                MBR MBR_Auxiliar;
                fread(&MBR_Auxiliar, sizeof (MBR_Auxiliar), 1, Disco_Actual);
                Particion Particion_Actual;
                int Existe_Particion = -1;
                int Existe_Extendida = -1;
                for (int i = 0; i < 4; i++) {
                    if (MBR_Auxiliar.MBR_Partitions[i].Part_Start != -1) {
                        if (strcmp(MBR_Auxiliar.MBR_Partitions[i].Part_Name, Name) == 0) {
                            Particion_Actual = MBR_Auxiliar.MBR_Partitions[i];
                            Existe_Particion = i;
                            break;
                        }

                        if (MBR_Auxiliar.MBR_Partitions[i].Part_Type == 'e') {
                            Existe_Extendida = i;
                        }
                    }
                }

                if (Existe_Particion != -1) {
                    if (Particion_Actual.Part_Type != 'l') {
                        if (Posicion_Particion < 20) {
                            Memoria_Sistema[Posicion_Disco].letra = 97 + Posicion_Disco;
                            strcpy(&Memoria_Sistema[Posicion_Disco].Path, Path);
                            Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Particion].Status = Particion_Actual.Part_Status;
                            Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Particion].Type = Particion_Actual.Part_Type;
                            Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Particion].Fit = Particion_Actual.Part_Fit;
                            Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Particion].Start = Particion_Actual.Part_Start;
                            Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Particion].Size = Particion_Actual.Part_Size;
                            Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Particion].ID[0] = 'v';
                            Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Particion].ID[1] = 'd';
                            Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Particion].ID[2] = (char) Memoria_Sistema[Posicion_Disco].letra;
                            Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Particion].ID[3] = (char) 48 + Posicion_Particion + 1;
                            Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Particion].ID[4] = '\0';
                            strcat(&Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Particion].Name, &Particion_Actual.Part_Name);
                            printf("---------------------------------------------------------------\n");
                            printf("--------------- SE MONTO LA PARTICION CON EXITO ---------------\n");
                            printf("---------------------------------------------------------------\n");
                            printf("\n");
                        } else {
                            printf("---------------------------------------------------------------\n");
                            printf("------------ ERROR: NO SE PUDO MONTAR LA PARTICION ------------\n");
                            printf("---------------------------------------------------------------\n");
                            printf("\n");
                        }
                    } else {
                        printf("---------------------------------------------------------------\n");
                        printf("------------ ERROR: NO SE PUDO MONTAR LA PARTICION ------------\n");
                        printf("---------------------------------------------------------------\n");
                        printf("\n");
                    }
                } else {
                    if (Existe_Extendida != -1) {
                        EBR EBR_Auxiliar;
                        int Existe_Logica = -1;
                        fseek(Disco_Actual, MBR_Auxiliar.MBR_Partitions[Existe_Extendida].Part_Start, SEEK_SET);
                        fread(&EBR_Auxiliar, sizeof (EBR_Auxiliar), 1, Disco_Actual);
                        if (strcmp(EBR_Auxiliar.Part_Name, Name) == 0) {
                            Existe_Logica = 1;
                        }

                        while (EBR_Auxiliar.Part_Next != -1) {
                            if (strcmp(EBR_Auxiliar.Part_Name, Name) == 0) {
                                Existe_Logica = 1;
                                break;
                            }
                            fseek(Disco_Actual, EBR_Auxiliar.Part_Next, SEEK_SET);
                            fread(&EBR_Auxiliar, sizeof (EBR_Auxiliar), 1, Disco_Actual);
                        }

                        if (Existe_Logica == 1) {
                            Memoria_Sistema[Posicion_Disco].letra = 97 + Posicion_Disco;
                            strcpy(&Memoria_Sistema[Posicion_Disco].Path, Path);
                            Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Particion].Status = EBR_Auxiliar.Part_Status;
                            Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Particion].Type = 'l';
                            Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Particion].Fit = EBR_Auxiliar.Part_Fit;
                            Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Particion].Start = EBR_Auxiliar.Part_Start;
                            Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Particion].Size = EBR_Auxiliar.Part_Size;
                            Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Particion].ID[0] = 'v';
                            Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Particion].ID[1] = 'd';
                            Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Particion].ID[2] = (char) Memoria_Sistema[Posicion_Disco].letra;
                            Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Particion].ID[3] = (char) 48 + Posicion_Particion + 1;
                            Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Particion].ID[4] = '\0';
                            strcat(&Memoria_Sistema[Posicion_Disco].Particiones[Posicion_Particion].Name, &EBR_Auxiliar.Part_Name);
                            printf("---------------------------------------------------------------\n");
                            printf("--------------- SE MONTO LA PARTICION CON EXITO ---------------\n");
                            printf("---------------------------------------------------------------\n");
                            printf("\n");
                        } else {
                            printf("---------------------------------------------------------------\n");
                            printf("------------ ERROR: NO SE PUDO MONTAR LA PARTICION ------------\n");
                            printf("---------------------------------------------------------------\n");
                            printf("\n");
                        }
                    } else {
                        printf("---------------------------------------------------------------\n");
                        printf("------------ ERROR: NO SE PUDO MONTAR LA PARTICION ------------\n");
                        printf("---------------------------------------------------------------\n");
                        printf("\n");
                    }
                }
            } else {
                printf("---------------------------------------------------------------\n");
                printf("------------- ERROR: NO SE PUDO INGRESAR AL DISCO -------------\n");
                printf("---------------------------------------------------------------\n");
                printf("\n");
            }
        }
    } else {
        printf("---------------------------------------------------------------\n");
        printf("----------- ERROR: EL DISCO SELECCIONADO NO EXISTE ------------\n");
        printf("---------------------------------------------------------------\n");
        printf("\n");
    }
}

void UNMOUNT(char Id[]) {
    if (Confirmacion() == 1) {
        int Existe_Montura = -1;
        for (int i = 0; i < 26; i++) {
            if (Memoria_Sistema[i].letra != -1) {
                int Cantidad_Monturas = 0;
                for (int j = 0; j < 20; j++) {
                    if (strcmp(Memoria_Sistema[i].Particiones[j].ID, Id) == 0) {
                        memset(&Memoria_Sistema[i].Particiones[j].Name, 0, 16);
                        memset(&Memoria_Sistema[i].Particiones[j].ID, 0, 5);
                        Memoria_Sistema[i].Particiones[j].Status = 'I';
                        Memoria_Sistema[i].Particiones[j].Type = 'p';
                        Memoria_Sistema[i].Particiones[j].Fit = 'f';
                        Memoria_Sistema[i].Particiones[j].Start = -1;
                        Memoria_Sistema[i].Particiones[j].Size = -1;
                        Existe_Montura = 1;
                        printf("---------------------------------------------------------------\n");
                        printf("------------- SE DESMONTO LA PARTICION CON EXITO --------------\n");
                        printf("---------------------------------------------------------------\n");
                        printf("\n");
                        break;
                    }
                }

                for (int j = 0; j < 20; j++) {
                    if (Memoria_Sistema[i].Particiones[j].Start != -1) {
                        Cantidad_Monturas++;
                    }
                }

                if (Cantidad_Monturas == 0) {
                    memset(&Memoria_Sistema[i].Path, 0, 300);
                    Memoria_Sistema[i].letra = -1;
                }

                if (Existe_Montura == 1) {
                    break;
                }
            }
        }

        if (Existe_Montura == -1) {
            printf("---------------------------------------------------------------\n");
            printf("------ ERROR: LA PARTICION NO ESTA MONTADA EN EL SISTEMA ------\n");
            printf("---------------------------------------------------------------\n");
            printf("\n");
        }

        Disco_Montado Auxiliar;
        for (int i = 1; i < 26; i++) {
            for (int j = 0; j < 26 - i; j++) {
                if (((Memoria_Sistema[j].letra > Memoria_Sistema[j + 1].letra) && Memoria_Sistema[j + 1].letra != -1) || (Memoria_Sistema[j].letra == -1)) {
                    Auxiliar = Memoria_Sistema[j + 1];
                    Memoria_Sistema[j + 1] = Memoria_Sistema[j];
                    Memoria_Sistema[j] = Auxiliar;
                }
            }
        }

        for (int i = 0; i < 26; i++) {
            if (Memoria_Sistema[i].letra != -1) {
                Memoria_Sistema[i].letra = 97 + i;
                for (int j = 0; j < 10; j++) {
                    if (Memoria_Sistema[i].Particiones[j].Start != -1) {
                        memset(&Memoria_Sistema[i].Particiones[j].ID, 0, 5);
                        Memoria_Sistema[i].Particiones[j].ID[0] = 'v';
                        Memoria_Sistema[i].Particiones[j].ID[1] = 'd';
                        Memoria_Sistema[i].Particiones[j].ID[2] = (char) Memoria_Sistema[i].letra;
                        Memoria_Sistema[i].Particiones[j].ID[3] = (char) 48 + j + 1;
                        Memoria_Sistema[i].Particiones[j].ID[4] = '\0';
                    }
                }
            }
        }

        Particion_Montada Pivote;
        for (int i = 0; i < 26; i++) {
            if (Memoria_Sistema[i].letra != -1) {
                for (int x = 1; x < 10; x++) {
                    for (int y = 0; y < 10 - x; y++) {
                        if (Memoria_Sistema[i].Particiones[y].Start == -1) {
                            Pivote = Memoria_Sistema[i].Particiones[y + 1];
                            Memoria_Sistema[i].Particiones[y + 1] = Memoria_Sistema[i].Particiones[y];
                            Memoria_Sistema[i].Particiones[y] = Pivote;
                        }
                    }
                }
            }
        }

        for (int i = 0; i < 26; i++) {
            if (Memoria_Sistema[i].letra != -1) {
                for (int j = 0; j < 10; j++) {
                    if (Memoria_Sistema[i].Particiones[j].Start != -1) {
                        memset(&Memoria_Sistema[i].Particiones[j].ID, 0, 5);
                        Memoria_Sistema[i].Particiones[j].ID[0] = 'v';
                        Memoria_Sistema[i].Particiones[j].ID[1] = 'd';
                        Memoria_Sistema[i].Particiones[j].ID[2] = (char) Memoria_Sistema[i].letra;
                        Memoria_Sistema[i].Particiones[j].ID[3] = (char) 48 + j + 1;
                        Memoria_Sistema[i].Particiones[j].ID[4] = '\0';
                    }
                }
            }
        }
    } else {
        printf("---------------------------------------------------------------\n");
        printf("---------------- SE CANCELO EL COMANDO UNMOUNT ----------------\n");
        printf("---------------------------------------------------------------\n");
        printf("\n");
    }
}

void SHOW_MOUNT() {
    printf("---------------------------------------------------------------\n");
    printf("---------------- DISCOS MONTADOS EN EL SISTEMA ----------------\n");
    printf("---------------------------------------------------------------\n");
    printf("\n");
    for (int i = 0; i < 26; i++) {
        if (Memoria_Sistema[i].letra != -1) {
            printf("DISCO '%c' : PATH: '%s' \n", Memoria_Sistema[i].letra, Memoria_Sistema[i].Path);
            for (int j = 0; j < 20; j++) {
                if (Memoria_Sistema[i].Particiones[j].Start != -1) {
                    printf("PARTICION '%s' - NOMBRE: '%s' \n", Memoria_Sistema[i].Particiones[j].ID, Memoria_Sistema[i].Particiones[j].Name);
                }
            }
        }
    }
}

int REP(char Path_Reporte[], char Path_Disco[], char Id[]) {
    if (Existencia_Path(Path_Disco) == 1) {
        Generar_Archivo(Path_Reporte);
        FILE *FRep;
        FRep = fopen(Path_Reporte, "w");
        if (FRep != NULL) {
            fclose(FRep);
            if (strcmp(Id, "mbr") == 0) {
                return RepMBR(Path_Reporte, Path_Disco);
            } else if (strcmp(Id, "disk") == 0) {
                return RepDISK(Path_Reporte, Path_Disco);
            } else {
                return -1;
            }
        } else {
            printf("---------------------------------------------------------------\n");
            printf("--------------- ERROR: NO SE GENERO EL REPORTE ----------------\n");
            printf("---------------------------------------------------------------\n");
            printf("\n");
            return -1;
        }
    } else {
        printf("---------------------------------------------------------------\n");
        printf("-------  ERROR: NO SE PUDO ABRIR EL ARCHIVO DEL REPORTE -------\n");
        printf("---------------------------------------------------------------\n");
        printf("\n");
        return -1;
    }
}

int RepMBR(char Destino[], char Disco[]) {
    if (Existencia_Path(Destino) == 1) {
        remove(Destino);
    }

    int Exito_Reporte = 0;
    FILE *Reporte_Actual;
    Reporte_Actual = fopen(Destino, "w");
    if (Reporte_Actual != NULL) {
        FILE *Disco_Actual;
        Disco_Actual = fopen(Disco, "rb+");
        if (Disco_Actual != NULL) {
            char Cuerpo_Reporte[9999] = {0};
            int Numero_EBR = 0;
            MBR MBR_Actual;
            fread(&MBR_Actual, sizeof (MBR_Actual), 1, Disco_Actual);
            sprintf(Cuerpo_Reporte, "digraph D {\nnode[shape=plaintext] rankdir=LR;\n  nodoUnico[label=< <table><tr><td colspan=\"3\">\"%s\"</td></tr>\n", Disco);
            fputs(Cuerpo_Reporte, Reporte_Actual);
            sprintf(Cuerpo_Reporte, "<tr><td colspan=\"2\">Size (Bytes)</td><td> %i </td></tr>\n", MBR_Actual.MBR_Tamano);
            fputs(Cuerpo_Reporte, Reporte_Actual);
            sprintf(Cuerpo_Reporte, "<tr><td colspan=\"2\">ID Disco</td><td> %i </td></tr>\n", MBR_Actual.MBR_Disk_Signature);
            fputs(Cuerpo_Reporte, Reporte_Actual);
            sprintf(Cuerpo_Reporte, "<tr><td colspan=\"2\">Fecha Creacion</td><td> %s </td></tr>\n", MBR_Actual.MBR_Fecha_Creacion);
            fputs(Cuerpo_Reporte, Reporte_Actual);
            sprintf(Cuerpo_Reporte, "<tr><td colspan=\"2\">Fit Disco</td><td> %cf </td></tr>\n", MBR_Actual.Disk_Fit);
            fputs(Cuerpo_Reporte, Reporte_Actual);
            for (int i = 0; i < 4; i++) {
                if (MBR_Actual.MBR_Partitions[i].Part_Start == -1) {
                    break;
                }
                sprintf(Cuerpo_Reporte, "<tr><td colspan=\"3\">Particion  %i</td></tr>\n", i + 1);
                fputs(Cuerpo_Reporte, Reporte_Actual);
                sprintf(Cuerpo_Reporte, "<tr><td colspan=\"2\">Status </td><td> %c </td></tr>\n", MBR_Actual.MBR_Partitions[i].Part_Status);
                fputs(Cuerpo_Reporte, Reporte_Actual);
                sprintf(Cuerpo_Reporte, "<tr><td colspan=\"2\">Type </td><td> %c </td></tr>\n", MBR_Actual.MBR_Partitions[i].Part_Type);
                fputs(Cuerpo_Reporte, Reporte_Actual);
                sprintf(Cuerpo_Reporte, "<tr><td colspan=\"2\">Fit </td><td> %c </td></tr>\n", MBR_Actual.MBR_Partitions[i].Part_Fit);
                fputs(Cuerpo_Reporte, Reporte_Actual);
                sprintf(Cuerpo_Reporte, "<tr><td colspan=\"2\">Start (Bytes)</td><td> %i </td></tr>\n", MBR_Actual.MBR_Partitions[i].Part_Start);
                fputs(Cuerpo_Reporte, Reporte_Actual);
                sprintf(Cuerpo_Reporte, "<tr><td colspan=\"2\">Size (Bytes)</td><td> %i </td></tr>\n", MBR_Actual.MBR_Partitions[i].Part_Size);
                fputs(Cuerpo_Reporte, Reporte_Actual);
                sprintf(Cuerpo_Reporte, "<tr><td colspan=\"2\">Name </td><td> %s </td></tr>\n", MBR_Actual.MBR_Partitions[i].Part_Name);
                fputs(Cuerpo_Reporte, Reporte_Actual);
                if (MBR_Actual.MBR_Partitions[i].Part_Type == 'e' && MBR_Actual.MBR_Partitions[i].Part_Start != -1) {
                    EBR EBR_Actual;
                    fseek(Disco_Actual, MBR_Actual.MBR_Partitions[i].Part_Start, SEEK_SET);
                    fread(&EBR_Actual, sizeof (EBR_Actual), 1, Disco_Actual);
                    if (EBR_Actual.Part_Start != -1 || EBR_Actual.Part_Next != -1) {
                        sprintf(Cuerpo_Reporte, "<tr><td></td><td colspan=\"2\">EBR #%i</td></tr>\n", Numero_EBR + 1);
                        Numero_EBR++;
                        fputs(Cuerpo_Reporte, Reporte_Actual);
                        if (!(EBR_Actual.Part_Status == 'I' || EBR_Actual.Part_Status == 'A')) {
                            sprintf(Cuerpo_Reporte, "<tr><td width=\"20%\"></td><td>Estado</td><td> %c</td></tr>\n", 'X');
                        } else {
                            sprintf(Cuerpo_Reporte, "<tr><td width=\"20%\"></td><td>Estado</td><td> %c</td></tr>\n", EBR_Actual.Part_Status);
                        }
                        fputs(Cuerpo_Reporte, Reporte_Actual);
                        if (!(EBR_Actual.Part_Fit == 'f' || EBR_Actual.Part_Fit == 'b' || EBR_Actual.Part_Fit == 'w')) {
                            sprintf(Cuerpo_Reporte, "<tr><td width=\"20%\"></td><td>Fit</td><td> %c </td></tr>\n", 'X');
                        } else {
                            sprintf(Cuerpo_Reporte, "<tr><td width=\"20%\"></td><td>Fit</td><td> %c </td></tr>\n", EBR_Actual.Part_Fit);
                        }
                        fputs(Cuerpo_Reporte, Reporte_Actual);
                        sprintf(Cuerpo_Reporte, "<tr><td width=\"20%\"></td><td>Inicio (Bytes) </td><td> %i</td></tr>\n", EBR_Actual.Part_Start);
                        fputs(Cuerpo_Reporte, Reporte_Actual);
                        sprintf(Cuerpo_Reporte, "<tr><td width=\"20%\"></td><td>Size (Bytes)</td><td> %i</td></tr>\n", EBR_Actual.Part_Size);
                        fputs(Cuerpo_Reporte, Reporte_Actual);
                        sprintf(Cuerpo_Reporte, "<tr><td width=\"20%\"></td><td>Siguiente </td><td> %i</td></tr>\n", EBR_Actual.Part_Next);
                        fputs(Cuerpo_Reporte, Reporte_Actual);
                        sprintf(Cuerpo_Reporte, "<tr><td width=\"20%\"></td><td>Nombre</td><td> %s</td></tr>\n", EBR_Actual.Part_Name);
                        fputs(Cuerpo_Reporte, Reporte_Actual);
                        while (EBR_Actual.Part_Next != -1 && EBR_Actual.Part_Start != 0) {
                            fseek(Disco_Actual, EBR_Actual.Part_Next, SEEK_SET);
                            fread(&EBR_Actual, sizeof (EBR_Actual), 1, Disco_Actual);
                            sprintf(Cuerpo_Reporte, "<tr><td></td><td colspan=\"2\">EBR #%i</td></tr>\n", Numero_EBR + 1);
                            Numero_EBR++;
                            fputs(Cuerpo_Reporte, Reporte_Actual);
                            sprintf(Cuerpo_Reporte, "<tr><td width=\"20%\"></td><td>Estado</td><td> %c</td></tr>\n", EBR_Actual.Part_Status);
                            fputs(Cuerpo_Reporte, Reporte_Actual);
                            sprintf(Cuerpo_Reporte, "<tr><td width=\"20%\"></td><td>Fit</td><td> %c </td></tr>\n", EBR_Actual.Part_Fit);
                            fputs(Cuerpo_Reporte, Reporte_Actual);
                            sprintf(Cuerpo_Reporte, "<tr><td width=\"20%\"></td><td>Inicio (Bytes)</td><td> %i</td></tr>\n", EBR_Actual.Part_Start);
                            fputs(Cuerpo_Reporte, Reporte_Actual);
                            sprintf(Cuerpo_Reporte, "<tr><td width=\"20%\"></td><td>Size (Bytes)</td><td> %i</td></tr>\n", EBR_Actual.Part_Size);
                            fputs(Cuerpo_Reporte, Reporte_Actual);
                            sprintf(Cuerpo_Reporte, "<tr><td width=\"20%\"></td><td>Siguiente </td><td> %i</td></tr>\n", EBR_Actual.Part_Next);
                            fputs(Cuerpo_Reporte, Reporte_Actual);
                            sprintf(Cuerpo_Reporte, "<tr><td width=\"20%\"></td><td>Nombre Particion</td><td> %s</td></tr>\n", EBR_Actual.Part_Name);
                            fputs(Cuerpo_Reporte, Reporte_Actual);
                        }
                    }
                }
            }
            fclose(Disco_Actual);
            Exito_Reporte = 1;
            printf("---------------------------------------------------------------\n");
            printf("-------------  SE GENERO CON EXITO EL REPORTE MBR -------------\n");
            printf("---------------------------------------------------------------\n");
            printf("\n");
        } else {
            printf("---------------------------------------------------------------\n");
            printf("---------- ERROR: NO SE ENCONTRO EL DISCO SLECCIONADO ---------\n");
            printf("---------------------------------------------------------------\n");
            printf("\n");
        }
    } else {
        printf("---------------------------------------------------------------\n");
        printf("-------- ERROR: NO SE PUDO ABRIR EL ARCHIVO DEL REPORTE -------\n");
        printf("---------------------------------------------------------------\n");
        printf("\n");
        return -1;
    }
    if (Exito_Reporte == 1) {
        fputs("</table> >  ];}", Reporte_Actual);
    }
    fclose(Reporte_Actual);
    return Exito_Reporte;
}

int RepDISK(char Destino[], char Disco[]) {
    if (Existencia_Path(Destino) == 1) {
        remove(Destino);
    }

    int Exito_Reporte = 0;
    int Numero_EBR = 0;
    char Cuerpo_Reporte[9999] = {0};
    FILE *Archivo_Reporte;
    Archivo_Reporte = fopen(Destino, "w");
    if (Archivo_Reporte != NULL) {
        char Contenido_Reporte[1024] = {0};
        MBR MBR_Actual;
        FILE *Archivo_Disco;
        Archivo_Disco = fopen(Disco, "rb+");
        if (Archivo_Disco != NULL) {
            fread(&MBR_Actual, sizeof (MBR_Actual), 1, Archivo_Disco);
            fseek(Archivo_Disco, 0, SEEK_SET);
            float Size_MBR = MBR_Actual.MBR_Tamano / 100;
            int SD = 140;
            int Pos = 0;
            sprintf(Contenido_Reporte, "digraph D {\n  node [shape=plaintext] rankdir=LR;\n  nodoUnico[label=< <table><tr><td rowspan=\"2\">MBR<br/>Nombre: %s<br/>Creacion: %s<br/>Size: %i</td>\n", Disco, MBR_Actual.MBR_Fecha_Creacion, MBR_Actual.MBR_Tamano);
            fputs(Contenido_Reporte, Archivo_Reporte);
            for (int i = 0; i < 4; i++) {
                Pos = i;
                if (MBR_Actual.MBR_Partitions[i].Part_Start == -1) {
                    break;
                }
                if (MBR_Actual.MBR_Partitions[i].Part_Start - SD > 0) {
                    sprintf(Contenido_Reporte, "<td rowspan=\"2\">Libre<br/>%4.2f%</td>\n", (MBR_Actual.MBR_Partitions[i].Part_Start - SD) / Size_MBR);
                    fputs(Contenido_Reporte, Archivo_Reporte);
                }
                int Total = 0;
                if (MBR_Actual.MBR_Partitions[i].Part_Type == 'e' && MBR_Actual.MBR_Partitions[i].Part_Start != -1) {
                    EBR EBR_Actual;
                    fseek(Archivo_Disco, MBR_Actual.MBR_Partitions[i].Part_Start, SEEK_SET);
                    fread(&EBR_Actual, sizeof (EBR_Actual), 1, Archivo_Disco);
                    if (EBR_Actual.Part_Start == -1) {
                        sprintf(Cuerpo_Reporte, "%s<td>EBR</td><td>Libre<br/>%4.2f%</td>\n", Cuerpo_Reporte, (EBR_Actual.Part_Size / Size_MBR));
                    } else {
                        sprintf(Cuerpo_Reporte, "%s<td>EBR</td><td>Logica<br/>Nombre: %s<br/>%4.2f%</td>\n", Cuerpo_Reporte, EBR_Actual.Part_Name, (EBR_Actual.Part_Size / Size_MBR));
                    }
                    if (EBR_Actual.Part_Next - EBR_Actual.Part_Start - EBR_Actual.Part_Size > 0) {
                        Total++;
                        sprintf(Cuerpo_Reporte, "%s<td>Libre<br/>%4.2f%</td>\n", Cuerpo_Reporte, ((EBR_Actual.Part_Next - EBR_Actual.Part_Start - EBR_Actual.Part_Size) / Size_MBR));
                    }
                    Numero_EBR++;
                    while (EBR_Actual.Part_Next != -1 && EBR_Actual.Part_Start != 0) {
                        fseek(Archivo_Disco, EBR_Actual.Part_Next, SEEK_SET);
                        fread(&EBR_Actual, sizeof (EBR_Actual), 1, Archivo_Disco);
                        sprintf(Cuerpo_Reporte, "%s<td>EBR</td><td>Logica<br/>Nombre: %s<br/>%4.2f%</td>\n", Cuerpo_Reporte, EBR_Actual.Part_Name, (EBR_Actual.Part_Size / Size_MBR));
                        Numero_EBR++;
                        if (EBR_Actual.Part_Next - EBR_Actual.Part_Start - EBR_Actual.Part_Size > 0) {
                            Total++;
                            sprintf(Cuerpo_Reporte, "%s<td>Libre<br/>%4.2f%</td>\n", Cuerpo_Reporte, ((EBR_Actual.Part_Next - EBR_Actual.Part_Start - EBR_Actual.Part_Size) / Size_MBR));
                        }
                    }
                    float Posicion_Final = MBR_Actual.MBR_Partitions[i].Part_Start + MBR_Actual.MBR_Partitions[i].Part_Size - EBR_Actual.Part_Start - EBR_Actual.Part_Size;
                    if (Posicion_Final > 0) {
                        sprintf(Cuerpo_Reporte, "%s<td>Libre<br/>%4.2f%</td>  \n", Cuerpo_Reporte, (Posicion_Final / Size_MBR));
                        sprintf(Contenido_Reporte, "<td colspan=\"%d\">Extendida<br/>Nombre: %s<br/>%4.2f%</td> \n", Numero_EBR * 2 + 1 + Total, MBR_Actual.MBR_Partitions[i].Part_Name, MBR_Actual.MBR_Partitions[i].Part_Size / Size_MBR);
                    } else {
                        sprintf(Contenido_Reporte, "<td colspan=\"%d\">Extendida<br/>Nombre: %s<br/>%4.2f%</td> \n", Numero_EBR * 2 + Total, MBR_Actual.MBR_Partitions[i].Part_Name, MBR_Actual.MBR_Partitions[i].Part_Size / Size_MBR);
                    }
                    fputs(Contenido_Reporte, Archivo_Reporte);
                } else {
                    sprintf(Contenido_Reporte, "<td rowspan=\"2\">Primaria<br/>Nombre: %s<br/>%4.2f%</td>  \n", MBR_Actual.MBR_Partitions[i].Part_Name, (MBR_Actual.MBR_Partitions[i].Part_Size / Size_MBR));
                    fputs(Contenido_Reporte, Archivo_Reporte);
                }
                SD = MBR_Actual.MBR_Partitions[i].Part_Start + MBR_Actual.MBR_Partitions[i].Part_Size;
            }
            if (Pos > 0) {
                if (Pos == 3) {
                    float Posicion_Final = MBR_Actual.MBR_Tamano - MBR_Actual.MBR_Partitions[Pos].Part_Start - MBR_Actual.MBR_Partitions[Pos].Part_Size;
                    if (Posicion_Final > 140) {
                        sprintf(Contenido_Reporte, "<td rowspan=\"2\">Libre<br/>%4.2f%</td>\n", (Posicion_Final / Size_MBR));
                        fputs(Contenido_Reporte, Archivo_Reporte);
                    }
                } else {
                    float Posicion_Final = MBR_Actual.MBR_Tamano - MBR_Actual.MBR_Partitions[Pos - 1].Part_Start - MBR_Actual.MBR_Partitions[Pos - 1].Part_Size;
                    if (Posicion_Final > 140) {
                        sprintf(Contenido_Reporte, "<td rowspan=\"2\">Libre<br/>%4.2f%</td>\n", (Posicion_Final / Size_MBR));
                        fputs(Contenido_Reporte, Archivo_Reporte);
                    }
                }
            } else {
                sprintf(Contenido_Reporte, "<td rowspan=\"2\">Libre<br/>%4.2f%</td>\n", ((MBR_Actual.MBR_Tamano - 140) / Size_MBR));
            }
            fclose(Archivo_Disco);
            Exito_Reporte = 1;
            printf("---------------------------------------------------------------\n");
            printf("-------------  SE GENERO CON EXITO EL REPORTE DISK ------------\n");
            printf("---------------------------------------------------------------\n");
            printf("\n");
        } else {
            printf("---------------------------------------------------------------\n");
            printf("-------  ERROR: NO SE PUDO ABRIR EL DISCO SELECCIONADO  -------\n");
            printf("---------------------------------------------------------------\n");
            printf("\n");
            return false;
        }
    } else {
        printf("---------------------------------------------------------------\n");
        printf("-------  ERROR: NO SE PUDO ABRIR EL ARCHIVO DEL REPORTE -------\n");
        printf("---------------------------------------------------------------\n");
        printf("\n");
        return -1;
    }
    if (Numero_EBR != 0 && Exito_Reporte == 1) {
        fputs("</tr><tr>", Archivo_Reporte);
        fputs(Cuerpo_Reporte, Archivo_Reporte);
    }
    if (Exito_Reporte == 1) {
        fputs("</tr></table> >  ];}", Archivo_Reporte);
    }
    fclose(Archivo_Reporte);
    return Exito_Reporte;
}

void EXEC(char Path[]) {
    char Contenido[1000] = {0};
    if (Existencia_Path(Path) == 1) {
        if (Validar_Extension(Path, ".sh") == 0) {
            FILE *Script;
            Script = fopen(Path, "r");
            if (Script != NULL) {
                while (!feof(Script)) {
                    fgets(Contenido, 1000, Script);
                    printf("%s", Contenido);
                    sprintf(Contenido, "%s \n", Contenido);
                    Cambiar_Formato(Contenido);
                    Ejecutar_Comando(Contenido);
                }
            } else {
                printf("---------------------------------------------------------------\n");
                printf("-------- ERROR: NO SE PUDO ABRIR EL ARCHIVO DE ENTRADA  -------\n");
                printf("---------------------------------------------------------------\n");
                printf("\n");
            }
        } else {
            printf("---------------------------------------------------------------\n");
            printf("-------- ERROR: LA EXTENSION DEL ARCHIVO ES INCORRECTA  -------\n");
            printf("---------------------------------------------------------------\n");
            printf("\n");
        }
    } else {
        printf("---------------------------------------------------------------\n");
        printf("-------- ERROR: NO SE ENCONTRO EL ARCHIVO DE ENTRADA  ---------\n");
        printf("---------------------------------------------------------------\n");
        printf("\n");
    }
}
