# Linux-IPC-Message-Queues
Linux IPC-Message Queues

# AIM:
To write a C program that receives a message from message queue and display them

# DESIGN STEPS:

### Step 1:

Navigate to any Linux environment installed on the system or installed inside a virtual environment like virtual box/vmware or online linux JSLinux (https://bellard.org/jslinux/vm.html?url=alpine-x86.cfg&mem=192) or docker.

### Step 2:

Write the C Program using Linux message queues API 

### Step 3:

Execute the C Program for the desired output. 

# PROGRAM:

## C program that receives a message from message queue and display them
### writer.c
```
// C Program for Message Queue (Writer Process)

#include <stdio.h>
#include <sys/ipc.h>
#include <sys/msg.h>
#include <string.h>
#include <stdlib.h>

// structure for message queue
struct mesg_buffer {
    long mesg_type;
    char mesg_text[100];
} message;

int main()
{
    key_t key;
    int msgid;

    // ftok to generate unique key
    key = ftok("progfile", 65);
    if (key == -1) {
        perror("ftok failed");
        exit(1);
    }

    // msgget creates a message queue and returns identifier
    msgid = msgget(key, 0666 | IPC_CREAT);
    if (msgid == -1) {
        perror("msgget failed");
        exit(1);
    }

    message.mesg_type = 1;

    printf("Write Data: ");
    fgets(message.mesg_text, sizeof(message.mesg_text), stdin);

    // remove newline from fgets
    message.mesg_text[strcspn(message.mesg_text, "\n")] = '\0';

    // msgsnd to send message
    if (msgsnd(msgid, &message, sizeof(message.mesg_text), 0) == -1) {
        perror("msgsnd failed");
        exit(1);
    }

    // display the message
    printf("Data sent is: %s\n", message.mesg_text);

    return 0;
}
```
### reader.c
```
// C Program for Message Queue (Reader Process)

#include <stdio.h>
#include <sys/ipc.h>
#include <sys/msg.h>
#include <stdlib.h>

// structure for message queue
struct mesg_buffer {
    long mesg_type;
    char mesg_text[100];
} message;

int main()
{
    key_t key;
    int msgid;

    // ftok to generate unique key
    key = ftok("progfile", 65);
    if (key == -1) {
        perror("ftok failed");
        exit(1);
    }

    // msgget creates a message queue and returns identifier
    msgid = msgget(key, 0666 | IPC_CREAT);
    if (msgid == -1) {
        perror("msgget failed");
        exit(1);
    }

    // msgrcv to receive message
    if (msgrcv(msgid, &message, sizeof(message.mesg_text), 1, 0) == -1) {
        perror("msgrcv failed");
        exit(1);
    }

    // display the message
    printf("Data Received is : %s\n", message.mesg_text);

    // destroy the message queue
    msgctl(msgid, IPC_RMID, NULL);

    return 0;
}
```



## OUTPUT
<img width="1133" height="501" alt="image" src="https://github.com/user-attachments/assets/4cc1432c-8a47-42b5-b987-02002ba4ef0e" />




# RESULT:
The programs are executed successfully.
