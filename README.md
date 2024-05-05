#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>
#include <string.h>

struct voiture {
    int id;
    char marque[50];
    char modele[50];
    char type_de_carburant[50];
    int nbr_de_place;
    float prix_location;
    bool disponible;
};

// Fonction pour saisir le nom de l'utilisateur
void saisirNomUtilisateur(char *nom) {
    printf("Bonjour ! Veuillez saisir votre nom : ");
    fgets(nom, 50, stdin);
    // Supprimer le retour à la ligne du nom saisi
    if (nom[strlen(nom) - 1] == '\n') {
        nom[strlen(nom) - 1] = '\0';
    }
}

// Déclaration des fonctions
int rech(int, char*);
void Ajoutervoiture();
void Recherche();
void Suppvoiture();
void Modifiervoiture();
void AfficherTt();
void trierParMarque(struct voiture voitures[], int taille);
void trierParPrix(struct voiture voitures[], int taille);

// Fonction de recherche
int rech(int idrech, char* marqueRech) {
    FILE *F;
    struct voiture voi;
    F = fopen("voiture.txt", "r");
    while (fscanf(F, "%d;%[^;];%[^;];%[^;];%d;%d;%f\n", &voi.id, voi.marque, voi.modele, voi.type_de_carburant, &voi.nbr_de_place, &voi.disponible, &voi.prix_location) != EOF) {
        if (idrech != -1) {
            if (voi.id == idrech) {
                fclose(F);
                return 1;
            }
        } else if (strcmp(voi.marque, marqueRech) == 0) {
            fclose(F);
            return 1;
        }
    }
    fclose(F);
    return -1;
}

// Procédure d'ajout de voiture
void Ajoutervoiture() {
    FILE *F;
    struct voiture voi;
    F = fopen("voiture.txt", "a");
    printf("Entrez l'ID de la nouvelle voiture : ");
    scanf("%d", &voi.id);
    fflush(stdin);
    printf("Entrez la marque : ");
    fgets(voi.marque, 50, stdin);
    if (voi.marque[strlen(voi.marque) - 1] == '\n') {
        voi.marque[strlen(voi.marque) - 1] = '\0';
    }
    printf("Entrez le modèle : ");
    fgets(voi.modele, 50, stdin);
    if (voi.modele[strlen(voi.modele) - 1] == '\n') {
        voi.modele[strlen(voi.modele) - 1] = '\0';
    }
    printf("Entrez le type de carburant : ");
    fgets(voi.type_de_carburant, 50, stdin);
    if (voi.type_de_carburant[strlen(voi.type_de_carburant) - 1] == '\n') {
        voi.type_de_carburant[strlen(voi.type_de_carburant) - 1] = '\0';
    }
    printf("Entrez le nombre de places : ");
    scanf("%d", &voi.nbr_de_place);
    voi.disponible = true;
    printf("Entrez le prix de location : ");
    scanf("%f", &voi.prix_location);
    fprintf(F, "%d;%s;%s;%s;%d;%d;%f\n", voi.id, voi.marque, voi.modele, voi.type_de_carburant, voi.nbr_de_place, voi.disponible, voi.prix_location);
    fclose(F);
}

// Fonction de recherche avancée
void Recherche() {
    int choix;
    printf("1- Rechercher par ID\n");
    printf("2- Rechercher par marque\n");
    printf("Entrez votre choix : ");
    scanf("%d", &choix);
    fflush(stdin);
    
    switch (choix) {
        case 1: {
            int idR;
            struct voiture voi;
            printf("Entrez l'ID de la voiture à rechercher : ");
            scanf("%d", &idR);
            fflush(stdin);
            if (rech(idR, NULL) == 1) {
                printf("\n***** Informations sur la voiture *****\n");
                printf("ID : %d\n", voi.id);
                printf("Marque : %s\n", voi.marque);
                printf("Modèle : %s\n", voi.modele);
                printf("Type de carburant : %s\n", voi.type_de_carburant);
                printf("Nombre de places : %d\n", voi.nbr_de_place);
                printf("Disponible : %s\n", voi.disponible ? "Oui" : "Non");
            } else {
                printf("Aucune voiture avec cet ID n'a été trouvée.\n");
            }
            break;
        }
        case 2: {
            char marqueRech[50];
            struct voiture voi;
            printf("Entrez la marque de la voiture à rechercher : ");
            fgets(marqueRech, 50, stdin);
            if (marqueRech[strlen(marqueRech) - 1] == '\n') {
                marqueRech[strlen(marqueRech) - 1] = '\0';
            }
            fflush(stdin);
            if (rech(-1, marqueRech) == 1) {
                printf("\n***** Informations sur la voiture *****\n");
                printf("ID : %d\n", voi.id);
                printf("Marque : %s\n", voi.marque);
                printf("Modèle : %s\n", voi.modele);
                printf("Type de carburant : %s\n", voi.type_de_carburant);
                printf("Nombre de places : %d\n", voi.nbr_de_place);
                printf("Disponible : %s\n", voi.disponible ? "Oui" : "Non");
            } else {
                printf("Aucune voiture avec cette marque n'a été trouvée.\n");
            }
            break;
        }
        default:
            printf("Choix invalide.\n");
            break;
    }
}

// Procédure de suppression de voiture
void Suppvoiture() {
    int idrech;
    char rep;
    printf("\nEntrez l'ID de la voiture à supprimer : ");
    scanf("%d", &idrech);
    fflush(stdin);
    if (rech(idrech, NULL) == 1) {
        printf("\nVoulez-vous vraiment la supprimer ? (O/N) ");
        scanf(" %c", &rep);
        fflush(stdin);
        if (rep == 'o' || rep == 'O') {
            FILE *F, *Fich;
            struct voiture voi;
            F = fopen("voiture.txt", "r");
            Fich = fopen("voitureTemp.txt", "a");
            while (fscanf(F, "%d;%[^;];%[^;];%[^;];%d;%d;%f\n", &voi.id, voi.marque, voi.modele, voi.type_de_carburant, &voi.nbr_de_place, &voi.disponible, &voi.prix_location) != EOF) {
                if (idrech != voi.id) {
                    fprintf(Fich, "%d;%s;%s;%s;%d;%d;%f\n", voi.id, voi.marque, voi.modele, voi.type_de_carburant, voi.nbr_de_place, voi.disponible, voi.prix_location);
                }
            }
            fclose(Fich);
            fclose(F);
            remove("voiture.txt");
            rename("voitureTemp.txt", "voiture.txt");
            printf("Suppression réussie !\n");
        }
    } else {
        printf("\nCet ID n'existe pas !\n");
    }
}

// Procédure de modification de voiture
void Modifiervoiture() {
    FILE *F, *Fich;
    struct voiture voi;
    int num;
    char rep = 'n';
    printf("\nEntrez l'ID de la voiture à modifier : ");
    scanf("%d", &num);
    fflush(stdin);
    if (rech(num, NULL) == 1) {
        printf("\nVoulez-vous vraiment la modifier ? (O/N) ");
        scanf(" %c", &rep);
        fflush(stdin);
        if (rep == 'o' || rep == 'O') {
            F = fopen("voiture.txt", "r");
            Fich = fopen("voitureTemp.txt", "a");
            while (fscanf(F, "%d;%[^;];%[^;];%[^;];%d;%d;%f\n", &voi.id, voi.marque, voi.modele, voi.type_de_carburant, &voi.nbr_de_place, &voi.disponible, &voi.prix_location) != EOF) {
                if (num != voi.id) {
                    fprintf(Fich, "%d;%s;%s;%s;%d;%d;%f\n", voi.id, voi.marque, voi.modele, voi.type_de_carburant, voi.nbr_de_place, voi.disponible, voi.prix_location);
                } else {
                    printf("\nEntrez le nouveau ID : ");
                    scanf("%d", &voi.id);
                    printf("\nEntrez la nouvelle marque : ");
                    fgets(voi.marque, 50, stdin);
                    if (voi.marque[strlen(voi.marque) - 1] == '\n') {
                        voi.marque[strlen(voi.marque) - 1] = '\0';
                    }
                    printf("\nEntrez le nouveau modèle : ");
                    fgets(voi.modele, 50, stdin);
                    if (voi.modele[strlen(voi.modele) - 1] == '\n') {
                        voi.modele[strlen(voi.modele) - 1] = '\0';
                    }
                    printf("\nEntrez le nouveau type de carburant : ");
                    fgets(voi.type_de_carburant, 50, stdin);
                    if (voi.type_de_carburant[strlen(voi.type_de_carburant) - 1] == '\n') {
                        voi.type_de_carburant[strlen(voi.type_de_carburant) - 1] = '\0';
                    }
                    printf("\nEntrez le nouveau nombre de places : ");
                    scanf("%d", &voi.nbr_de_place);
                    printf("\nEntrez le nouveau prix de location : ");
                    scanf("%f", &voi.prix_location);
                    fprintf(Fich, "%d;%s;%s;%s;%d;%d;%f\n", voi.id, voi.marque, voi.modele, voi.type_de_carburant, voi.nbr_de_place, voi.disponible, voi.prix_location);
                }
            }
            fclose(F);
            fclose(Fich);
            remove("voiture.txt");
            rename("voitureTemp.txt", "voiture.txt");
            printf("\nModification réussie !\n");
        } else {
            printf("\nLa modification a été annulée !\n");
        }
    } else {
        printf("\nCet ID n'existe pas !\n");
    }
}

// Procédure d'affichage de toutes les voitures
void AfficherTt() {
    FILE *F;
    struct voiture voi;
    F = fopen("voiture.txt", "r");
    printf("\nLa liste des voitures est : \n\n");
    printf("ID\tMarque\tModèle\tType de carburant\tNombre de places\tPrix de location\n");
    while (fscanf(F, "%d;%[^;];%[^;];%[^;];%d;%d;%f\n", &voi.id, voi.marque, voi.modele, voi.type_de_carburant, &voi.nbr_de_place, &voi.disponible, &voi.prix_location) != EOF) {
        printf("%d\t%s\t%s\t%s\t%d\t%.2f\n", voi.id, voi.marque, voi.modele, voi.type_de_carburant, voi.nbr_de_place, voi.prix_location);
    }
    fclose(F);
}

// Fonction de comparaison pour le tri par marque
int comparerMarque(const void *a, const void *b) {
    struct voiture *voitureA = (struct voiture *)a;
    struct voiture *voitureB = (struct voiture *)b;
    return strcmp(voitureA->marque, voitureB->marque);
}

// Fonction de comparaison pour le tri par prix de location par jour
int comparerPrix(const void *a, const void *b) {
    struct voiture *voitureA = (struct voiture *)a;
    struct voiture *voitureB = (struct voiture *)b;
    return (voitureA->prix_location - voitureB->prix_location) < 0 ? -1 : 1;
}

// Procédure pour trier les voitures par marque
void trierParMarque(struct voiture voitures[], int taille) {
    FILE *F;
    struct voiture voi;
    F = fopen("voiture.txt", "r");
    if (F == NULL) {
        printf("Impossible d'ouvrir le fichier voiture.txt.\n");
        return;
    }

    int i = 0;
    while (fscanf(F, "%d;%[^;];%[^;];%[^;];%d;%d;%f\n", &voitures[i].id, voitures[i].marque, voitures[i].modele, voitures[i].type_de_carburant, &voitures[i].nbr_de_place, &voitures[i].disponible, &voitures[i].prix_location) != EOF) {
        i++;
    }
    fclose(F);

    // Trier le tableau de voitures par marque
    qsort(voitures, taille, sizeof(struct voiture), comparerMarque);

    // Afficher les voitures triées par marque
    printf("Les voitures ont été triées par marque.\n");
    for (int j = 0; j < taille; j++) {
        printf("ID : %d, Marque : %s, Modèle : %s\n", voitures[j].id, voitures[j].marque, voitures[j].modele);
    }
}

// Procédure pour trier les voitures par prix de location par jour
void trierParPrix(struct voiture voitures[], int taille) {
    FILE *F;
    struct voiture voi;
    F = fopen("voiture.txt", "r");
    if (F == NULL) {
        printf("Impossible d'ouvrir le fichier voiture.txt.\n");
        return;
    }

    int i = 0;
    while (fscanf(F, "%d;%[^;];%[^;];%[^;];%d;%d;%f\n", &voitures[i].id, voitures[i].marque, voitures[i].modele, voitures[i].type_de_carburant, &voitures[i].nbr_de_place, &voitures[i].disponible, &voitures[i].prix_location) != EOF) {
        i++;
    }
    fclose(F);

    // Trier le tableau de voitures par prix de location
    qsort(voitures, taille, sizeof(struct voiture), comparerPrix);

    // Afficher les voitures triées par prix de location
    printf("Les voitures ont été triées par prix de location.\n");
    for (int j = 0; j < taille; j++) {
        printf("ID : %d, Marque : %s, Prix de location : %.2f\n", voitures[j].id, voitures[j].marque, voitures[j].prix_location);
    }
}

#define MAX_VOITURES 100
struct voiture voitures[MAX_VOITURES];

// Fonction principale
int main() {
    char nomUtilisateur[50];
    saisirNomUtilisateur(nomUtilisateur);
    printf("Bonjour, %s ! Bienvenue dans notre application de gestion de voitures.\n", nomUtilisateur);

    int choix;
    char rep;
    do {
        printf("\n******** Interface de navigation ********\n");
        printf("1- Ajouter une voiture\n");
        printf("2- Rechercher une voiture\n");
        printf("3- Supprimer une voiture\n");
        printf("4- Modifier une voiture\n");
        printf("5- Afficher toutes les voitures\n");
        printf("6- Trier par marque\n");
        printf("7- Trier par prix de location\n");
        printf("8- Quitter\n\n");
        printf("Entrez votre choix : ");
        scanf("%d", &choix);
        fflush(stdin);

        switch (choix) {
            case 1:
                Ajoutervoiture();
                break;
            case 2:
                Recherche();
                break;
            case 3:
                Suppvoiture();
                break;
            case 4:
                Modifiervoiture();
                break;
            case 5:
                AfficherTt();
                break;
            case 6:
                trierParMarque(voitures, MAX_VOITURES); // Appeler la fonction pour trier par marque
                break;
            case 7:
                trierParPrix(voitures, MAX_VOITURES); // Appeler la fonction pour trier par prix de location
                break;
            case 8:
                break;
            default:
                printf("Choix invalide.\n");
                break;
        }

        printf("\nVoulez-vous continuer ? (O/N) ");
        scanf(" %c", &rep);
        fflush(stdin);
    } while (rep == 'o' || rep == 'O');

    return 0;
}
