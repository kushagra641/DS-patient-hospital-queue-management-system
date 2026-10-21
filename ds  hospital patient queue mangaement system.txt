#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct Patient {
    int token;
    char name[50];
    struct Patient *next;
};

struct Patient *front = NULL;
struct Patient *rear = NULL;
int tokenCounter = 1;

void registerPatient() {
    struct Patient *n = malloc(sizeof(struct Patient));
    if (n == NULL) {
        printf("Memory allocation failed\n");
        return;
    }
    printf("Enter patient name: ");
    scanf(" %49[^\n]", n->name);
    n->token = tokenCounter++;
    n->next = NULL;

    if (rear == NULL)
        front = rear = n;
    else {
        rear->next = n;
        rear = n;
    }
    printf("Registered. Token number: %d\n", n->token);
}

void servePatient() {
    if (front == NULL) {
        printf("No patients waiting\n");
        return;
    }
    struct Patient *t = front;
    printf("Now serving: Token %d - %s\n", t->token, t->name);
    front = front->next;
    if (front == NULL)
        rear = NULL;
    free(t);
}

void displayQueue() {
    struct Patient *t = front;
    if (t == NULL) {
        printf("Queue is empty\n");
        return;
    }
    while (t != NULL) {
        printf("Token %d - %s\n", t->token, t->name);
        t = t->next;
    }
}

void searchPatient() {
    int tk, pos = 1;
    struct Patient *t = front;
    printf("Enter token to search: ");
    scanf("%d", &tk);

    while (t != NULL) {
        if (t->token == tk) {
            printf("Found: %s (position %d in queue)\n", t->name, pos);
            return;
        }
        t = t->next;
        pos++;
    }
    printf("Patient not found\n");
}

void cancelAppointment() {
    int tk;
    struct Patient *t = front, *p = NULL;
    printf("Enter token to cancel: ");
    scanf("%d", &tk);

    while (t != NULL && t->token != tk) {
        p = t;
        t = t->next;
    }
    if (t == NULL) {
        printf("Patient not found\n");
        return;
    }

    if (p == NULL)
        front = t->next;
    else
        p->next = t->next;

    if (t == rear)
        rear = p;

    free(t);
    printf("Appointment cancelled\n");
}

int main() {
    int ch;
    while (1) {
        printf("\n===== HOSPITAL OPD QUEUE =====\n");
        printf("1. Register Patient\n2. Serve Next Patient\n3. Display Waiting List\n");
        printf("4. Search Patient\n5. Cancel Appointment\n6. Exit\n");
        printf("Enter choice: ");
        if (scanf("%d", &ch) != 1) {
            printf("Invalid input\n");
            while (getchar() != '\n');
            continue;
        }

        if (ch == 1) registerPatient();
        else if (ch == 2) servePatient();
        else if (ch == 3) displayQueue();
        else if (ch == 4) searchPatient();
        else if (ch == 5) cancelAppointment();
        else if (ch == 6) break;
        else printf("Invalid choice\n");
    }
    return 0;
}