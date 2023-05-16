DINING PHILOSPHER -
#include<stdio.h>
#include<stdlib.h>
#include<pthread.h>
#include<semaphore.h>
#include<unistd.h>

sem_t room;
sem_t chopstick[5];

void * philosopher(void *);
void eat(int);
int main()
{
	int i,a[5];
	pthread_t tid[5];
	
	sem_init(&room,0,4);
	
	for(i=0;i<5;i++)
		sem_init(&chopstick[i],0,1);
		
	for(i=0;i<5;i++){
		a[i]=i;
		pthread_create(&tid[i],NULL,philosopher,(void *)&a[i]);
	}
	for(i=0;i<5;i++)
		pthread_join(tid[i],NULL);
}

void * philosopher(void * num)
{
	int phil=*(int *)num;

	sem_wait(&room);
	printf("\nPhilosopher %d has entered room",phil);
	sem_wait(&chopstick[phil]);
	sem_wait(&chopstick[(phil+1)%5]);

	eat(phil);
	sleep(2);
	printf("\nPhilosopher %d has finished eating",phil);

	sem_post(&chopstick[(phil+1)%5]);
	sem_post(&chopstick[phil]);
	sem_post(&room);
}

void eat(int phil)
{
	printf("\nPhilosopher %d is eating",phil);
}

/////////////////////////////////////////////////////////////////////////////////


EXP - Scheduling

Write a program to implement FCFS Scheduling algorithm

#include <stdio.h>
int main()
{
    int pid[15];
    int bt[15];
    int n;
    printf("Enter the number of processes: ");
    scanf("%d",&n);
 
    printf("Enter process id of all the processes: ");
    for(int i=0;i<n;i++)
    {
        scanf("%d",&pid[i]);
    }
 
    printf("Enter burst time of all the processes: ");
    for(int i=0;i<n;i++)
    {
        scanf("%d",&bt[i]);
    }
 
    int i, wt[n];
    wt[0]=0;
 
    //for calculating waiting time of each process
    for(i=1; i<n; i++)
    {
        wt[i]= bt[i-1]+ wt[i-1];
    }
 
    printf("Process ID     Burst Time     Waiting Time     TurnAround Time\n");
    float twt=0.0;
    float tat= 0.0;
    for(i=0; i<n; i++)
    {
        printf("%d\t\t", pid[i]);
        printf("%d\t\t", bt[i]);
        printf("%d\t\t", wt[i]);
 
        //calculating and printing turnaround time of each process
        printf("%d\t\t", bt[i]+wt[i]);
        printf("\n");
 
        //for calculating total waiting time
        twt += wt[i];
 
        //for calculating total turnaround time
        tat += (wt[i]+bt[i]);
    }
    float att,awt;
 
    //for calculating average waiting time
    awt = twt/n;
 
    //for calculating average turnaround time
    att = tat/n;
    printf("Avg. waiting time= %f\n",awt);
    printf("Avg. turnaround time= %f",att);
}

//////////////////////////////////////////////////////////////

READER - WRITER :
#include<semaphore.h>
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<pthread.h>
sem_t x,y;
pthread_t tid;
pthread_t writerthreads[100],readerthreads[100];
int readercount = 0;

void *reader(void* param)
{
    sem_wait(&x);
    readercount++;
    if(readercount==1)
        sem_wait(&y);
    sem_post(&x);
    printf("%d reader is inside\n",readercount);
    usleep(3);
    sem_wait(&x);
    readercount--;
    if(readercount==0)
    {
        sem_post(&y);
    }
    sem_post(&x);
    printf("%d Reader is leaving\n",readercount+1);
    return NULL;
}

void *writer(void* param)
{
    printf("Writer is trying to enter\n");
    sem_wait(&y);
    printf("Writer has entered\n");
    sem_post(&y);
    printf("Writer is leaving\n");
    return NULL;
}

int main()
{
    int n2,i;
    printf("Enter the number of readers:");
    scanf("%d",&n2);
    printf("\n");
    int n1[n2];
    sem_init(&x,0,1);
    sem_init(&y,0,1);
    for(i=0;i<n2;i++)
    {
        pthread_create(&writerthreads[i],NULL,reader,NULL);
        pthread_create(&readerthreads[i],NULL,writer,NULL);
    }
    for(i=0;i<n2;i++)
    {
        pthread_join(writerthreads[i],NULL);
        pthread_join(readerthreads[i],NULL);
    }

}


////////////////////////////////////////////////////////////////

DINING PHILOSPHER -
#include<stdio.h>
#include<stdlib.h>
#include<pthread.h>
#include<semaphore.h>
#include<unistd.h>

sem_t room;
sem_t chopstick[5];

void * philosopher(void *);
void eat(int);
int main()
{
	int i,a[5];
	pthread_t tid[5];
	
	sem_init(&room,0,4);
	
	for(i=0;i<5;i++)
		sem_init(&chopstick[i],0,1);
		
	for(i=0;i<5;i++){
		a[i]=i;
		pthread_create(&tid[i],NULL,philosopher,(void *)&a[i]);
	}
	for(i=0;i<5;i++)
		pthread_join(tid[i],NULL);
}

void * philosopher(void * num)
{
	int phil=*(int *)num;

	sem_wait(&room);
	printf("\nPhilosopher %d has entered room",phil);
	sem_wait(&chopstick[phil]);
	sem_wait(&chopstick[(phil+1)%5]);

	eat(phil);
	sleep(2);
	printf("\nPhilosopher %d has finished eating",phil);

	sem_post(&chopstick[(phil+1)%5]);
	sem_post(&chopstick[phil]);
	sem_post(&room);
}

void eat(int phil)
{
	printf("\nPhilosopher %d is eating",phil);
}


//////////////////////////////////////////////////////////


EXP - PIPES 

#include <stdio.h>
#include<unistd.h>
#include<sys/wait.h>
int main()
{
int p[2];
char buff[25];
pipe(p);
if(fork()==0)
{
printf("Child : Writing to pipe \n");
write(p[1],”Welcome”,8);
printf("Child Exiting\n");
}
else
{
wait(NULL);
printf("Parent : Reading from pipe \n");
read(p[0],buff,8);
printf("Pipe content is : %s \n",buff);
}
return 0;
}

#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/wait.h>

int main()
{
    int fd[2];
    pid_t pid;
   
    // Create a pipe
    if (pipe(fd) < 0) {
        perror("pipe");
        exit(EXIT_FAILURE);
    }

    // Fork a child process
    if ((pid = fork()) < 0) {
        perror("fork");
        exit(EXIT_FAILURE);
    }

    if (pid == 0) {
        // Child process - program1

        // Close the input end of the pipe
        close(fd[0]);

        // Redirect the output to the pipe
        dup2(fd[1], STDOUT_FILENO);

        // Execute program1
        if (execlp("program1", "program1", NULL) < 0) {
            perror("execlp");
            exit(EXIT_FAILURE);
        }
    } else {
        // Parent process - program2

        // Close the output end of the pipe
        close(fd[1]);

        // Redirect the input from the pipe
        dup2(fd[0], STDIN_FILENO);

        // Execute program2
        if (execlp("program2", "program2", NULL) < 0) {
            perror("execlp");
            exit(EXIT_FAILURE);
        }

        // Wait for child process to finish
        wait(NULL);
    }

    return 0;
}


///////////////////////////////////////////////////////////////////

EXP - MESSAGE QUEUE AND SHARED MEM

Write a program to send a message (pass through command line arguments) into a
message queue. Send few messages with unique message numbers

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/msg.h>

#define MAX_MSG_LEN 100

int main(int argc, char *argv[]) {
    if (argc < 4) {
        fprintf(stderr, "Usage: %s <key> <msgtype> <msg>\n", argv[0]);
        return 1;
    }

    key_t key = atoi(argv[1]);
    int msgid = msgget(key, IPC_CREAT | 0664);
    if (msgid == -1) {
        perror("msgget");
        return 1;
    }

    int msgtype = atoi(argv[2]);
    if (msgsnd(msgid, argv[3], strlen(argv[3]) + 1, 0) == -1) {
        perror("msgsnd");
        return 1;
    }

    return 0;
}


Write a program to receive a particular message from the message queue. Use message
number to receive the particular message
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/msg.h>

#define MAX_MSG_LEN 100

int main(int argc, char *argv[]) {
    if (argc < 3) {
        fprintf(stderr, "Usage: %s <key> <msgtype>\n", argv[0]);
        return 1;
    }

    key_t key = atoi(argv[1]);
    int msgid = msgget(key, IPC_CREAT | 0664);
    if (msgid == -1) {
        perror("msgget");
        return 1;
    }

    int msgtype = atoi(argv[2]);
    struct {
        long mtype;
        char mtext[MAX_MSG_LEN];
    } msgbuf;

    if (msgrcv(msgid, &msgbuf, MAX_MSG_LEN, msgtype, 0) == -1) {
        perror("msgrcv");
        return 1;
    }

    printf("Received message with type %d: %s\n", msgtype, msgbuf.mtext);

    return 0;
}

Write a program to do the following:
∑ Create two processes, one is for writing into the shared memory (shm_write.c)
and another is for reading from the shared memory (shm_read.c)
∑ In the shared memory, the writing process, creates a shared memory of size 1K
(and flags) and attaches the shared memory
∑ The write process writes the data read from the standard input into the shared
memory. Last byte signifies the end of buffer
∑ Read process would read from the shared memory and write to the standard
output

shm write - 
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/ipc.h>
#include <sys/shm.h>

#define SHM_SIZE 1024

int main()
{
    key_t key = 1234; // unique key for the shared memory segment
    int shmid;
    char *shm, *s;

    // create the shared memory segment
    shmid = shmget(key, SHM_SIZE, IPC_CREAT | 0666);
    if (shmid < 0) {
        perror("shmget");
        exit(1);
    }

    // attach the shared memory segment to our data space
    shm = shmat(shmid, NULL, 0);
    if (shm == (char *) -1) {
        perror("shmat");
        exit(1);
    }

    // write data into the shared memory segment
    printf("Enter a message: ");
    fgets(shm, SHM_SIZE, stdin);
    s = shm + strlen(shm) - 1;
    *s = '\0'; // add a null character to mark the end of the buffer

    printf("Data written to shared memory: %s\n", shm);

    // detach the shared memory segment from our data space
    if (shmdt(shm) == -1) {
        perror("shmdt");
        exit(1);
    }

    return 0;
}

Shm read - 
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/ipc.h>
#include <sys/shm.h>

#define SHM_SIZE 1024

int main()
{
    key_t key = 1234; // unique key for the shared memory segment
    int shmid;
    char *shm, *s;

    // locate the shared memory segment
    shmid = shmget(key, SHM_SIZE, 0666);
    if (shmid < 0) {
        perror("shmget");
        exit(1);
    }

    // attach the shared memory segment to our data space
    shm = shmat(shmid, NULL, 0);
    if (shm == (char *) -1) {
        perror("shmat");
        exit(1);
    }

    // read data from the shared memory segment
    printf("Data read from shared memory: %s\n", shm);

    // detach the shared memory segment from our data space
    if (shmdt(shm) == -1) {
        perror("shmdt");
        exit(1);
    }

    // mark the shared memory segment for removal
    shmctl(shmid, IPC_RMID, NULL);

    return 0;
}
