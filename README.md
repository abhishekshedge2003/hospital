#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct Patient {
    int id;
    char name[50];
    int age;
    char disease[50];
};

void addPatient();
void viewPatients();
void searchPatient();
void deletePatient();

int main() {
    int choice;
    while (1) {
        printf("\n--- Hospital Management System ---\n");
        printf("1. Add Patient\n");
        printf("2. View Patients\n");
        printf("3. Search Patient by ID\n");
        printf("4. Delete Patient\n");
        printf("5. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch(choice) {
            case 1: addPatient(); break;
            case 2: viewPatients(); break;
            case 3: searchPatient(); break;
            case 4: deletePatient(); break;
            case 5: exit(0);
            default: printf("Invalid choice. Try again.\n");
        }
    }
    return 0;
}

void addPatient() {
    struct Patient p;
    FILE *fp = fopen("patients.txt", "ab");
    if (fp == NULL) {
        printf("Error opening file!\n");
        return;
    }

    printf("Enter patient ID: ");
    scanf("%d", &p.id);
    printf("Enter name: ");
    scanf(" %[^\n]", p.name);
    printf("Enter age: ");
    scanf("%d", &p.age);
    printf("Enter disease: ");
    scanf(" %[^\n]", p.disease);

    fwrite(&p, sizeof(p), 1, fp);
    fclose(fp);
    printf("Patient added successfully.\n");
}

void viewPatients() {
    struct Patient p;
    FILE *fp = fopen("patients.txt", "rb");
    if (fp == NULL) {
        printf("No records found.\n");
        return;
    }

    printf("\n--- Patient Records ---\n");
    while (fread(&p, sizeof(p), 1, fp)) {
        printf("ID: %d\nName: %s\nAge: %d\nDisease: %s\n\n", p.id, p.name, p.age, p.disease);
    }
    fclose(fp);
}

void searchPatient() {
    int id, found = 0;
    struct Patient p;
    FILE *fp = fopen("patients.txt", "rb");
    if (fp == NULL) {
        printf("No records found.\n");
        return;
    }

    printf("Enter patient ID to search: ");
    scanf("%d", &id);

    while (fread(&p, sizeof(p), 1, fp)) {
        if (p.id == id) {
            printf("\nPatient found:\n");
            printf("ID: %d\nName: %s\nAge: %d\nDisease: %s\n", p.id, p.name, p.age, p.disease);
            found = 1;
            break;
        }
    }
    if (!found)
        printf("Patient with ID %d not found.\n", id);

    fclose(fp);
}

void deletePatient() {
    int id, found = 0;
    struct Patient p;
    FILE *fp = fopen("patients.txt", "rb");
    FILE *temp = fopen("temp.txt", "wb");

    if (fp == NULL || temp == NULL) {
        printf("Error opening file!\n");
        return;
    }

    printf("Enter patient ID to delete: ");
    scanf("%d", &id);

    while (fread(&p, sizeof(p), 1, fp)) {
        if (p.id == id) {
            found = 1;
            continue; // skip writing this record
        }
        fwrite(&p, sizeof(p), 1, temp);
    }

    fclose(fp);
    fclose(temp);

    remove("patients.txt");
    rename("temp.txt", "patients.txt");

    if (found)
        printf("Patient deleted successfully.\n");
    else
        printf("Patient with ID %d not found.\n", id);
}
