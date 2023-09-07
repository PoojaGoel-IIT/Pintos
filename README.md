# Pintos
POOJA GOEL

22210029
                                                                                                    
                                                                               Assignment 1
                                                                                                     
                                                                          CS 612 - Computer Systems
# Assignment 

Please find the below PDF file for assignment:-

[Pintos](pintos.pdf)

[Assignment](hw1.pdf)


## Question: Word-count:Using Lists to Count Words:

### Step 1:

Defining PINTOS_LIST in word_count_l.c file
```c

 #define PINTOS_LIST
 ```

### Step2: complete functions in word_count_l.c file

```c
void init_words(word_count_list_t* wclist) { /* TODO */
list_init (wclist); 
}

size_t len_words(word_count_list_t* wclist) {
  /* TODO */
  
return list_size(wclist);
  
}

word_count_t* find_word(word_count_list_t* wclist, char* word) {
  /* TODO */
   
  
   struct list_elem *a;
   for(a= list_begin(wclist); a!=list_end(wclist);a=list_next(a))
   {
    struct word_count* b=list_entry(a,struct word_count,elem);
    if(strcmp(b->word,word)==0)
    return(b);
   };
   
  return NULL;
  
}

word_count_t* add_word(word_count_list_t* wclist, char* word) {

  /* TODO */
 word_count_t *a;  
  
a=find_word(wclist,word);
if(a!=NULL)
{
  a->count=a->count+1;
  return a;
}
else{
  struct word_count *new =(struct word_count *)malloc(sizeof(struct word_count));
  new->count=1;
  new->word=word; 
  list_push_back(wclist, &(new->elem));
  return new;
}
  return NULL;

  
}

void fprint_words(word_count_list_t* wclist, FILE* outfile) { /* TODO */
  
struct list_elem *a;
for(a= list_begin(wclist); a!=list_end(wclist);a=list_next(a))
   {
    struct word_count* b=list_entry(a,struct word_count,elem);
    fprintf(outfile,"%6d %6s\n", b->count,b->word);

}
}

static bool less_list(const struct list_elem* ewc1, const struct list_elem* ewc2, void* aux) {
  /* TODO */
struct word_count* a=list_entry(ewc1,struct word_count,elem);
struct word_count* b=list_entry(ewc2,struct word_count,elem);
if(less_count(a,b)|| (!less_count(a,b)&& !less_count(b,a)&& less_word(a,b)))
//if(a->count<b->count)
return true;
  return false;
}

void wordcount_sort(word_count_list_t* wclist,bool less(const word_count_t*, const word_count_t*)) {
  
list_sort(&(wclist->lst), less_list, less);
list_sort(wclist, less_list, less);
}

```

### Step3:

Compiling the code:

               root@DESKTOP-TN1I17B:/mnt/f/Semester 1/Assignment1/assignment1/word-count# make

               gcc -g3 -Wall -std=gnu99 -c word_count_multithread.c -o word_count_multithread.

               gcc -pthread word_count_multithread.o word_helpers.o -o word_count_m

               root@DESKTOP-TN1I17B:/mnt/f/Semester 1/Assignment1/assignment1/word-count# ./lwords gutenberg/alice.txt gutenberg/peter.txt

  ![image](https://github.com/PoojaGoel-IIT/Pintos/assets/112702452/f59888b6-b8c2-4a91-95d5-beb610548adf)
       
Count list is displaying is shown below:

   ![image](https://github.com/PoojaGoel-IIT/Pintos/assets/112702452/c1e72f2f-b5c8-44b8-8159-db6baf5a2cfb)

## Using Multiple Threads to Count Words

Creating a new c file :- word_count_multithread.c

File uses pthreads and creates multiple threads to read files.

```c
/*this file is creating multiple threads to read file and updating shared word_count_list_t* w*/
#define PINTOS_LIST 
#define PTHREADS

#include<pthread.h>

#include "word_count.h"
#include "word_helpers.h"
#include "list.c"
#include "debug.c"
#include "word_count_l.c"

word_count_list_t* w; // defining global word_count_list_t
pthread_t thread[10]; //creating thread array globally

void* threadWordCount(void* arg)
{
    FILE* f = fopen((char *) arg, "r");
    count_words(w, f);
    fclose(f);
    return NULL;
}

int main(int argc, char *argv[]){
    w = (word_count_list_t*)malloc(sizeof(word_count_list_t));
    init_words(w);
    pthread_mutex_init(&w->lock, NULL);  // initializing mutex 

    for(int i=0; i<argc; ++i){
      pthread_create(&thread[i], NULL, threadWordCount, argv[i]);
    }

    for(int i=0; i<argc; ++i){
      pthread_join(thread[i], NULL);
    }

    wordcount_sort(w,NULL); // sorting updated w list 
    fprint_words(w, stdout);

    exit(0);
}   
```
### Step:

Updating all functions in word_count_l.c

```c
#define PINTOS_LIST
#ifndef PINTOS_LIST
//#error "PINTOS_LIST must be #define'd when compiling word_count_l.c"
//#define PINTOS_LIST // defining PINTOS_LIST for the compilation
#define PINTOS_LIST
//#define PTHREADS
#endif

#include "word_count.h"
#include "list.h"
#include "word_helpers.h"

void init_words(word_count_list_t* wclist) { /* TODO */
#ifdef PINTOS_LIST
#ifdef PTHREADS

   list_init (&wclist->lst);
   
#else
list_init (wclist); 
#endif
#endif
}

size_t len_words(word_count_list_t* wclist) {
  /* TODO */
  
#ifdef PINTOS_LIST
#ifdef PTHREADS
  return list_size(&(wclist->lst));
#else
return list_size(wclist);
#endif
#endif

  
}

word_count_t* find_word(word_count_list_t* wclist, char* word) {
  /* TODO */
   #ifdef PINTOS_LIST
   #ifdef PTHREADS
   struct list_elem *a;
   for(a= list_begin(&(wclist->lst)); a!=list_end(&(wclist->lst));a=list_next(a))
   {
    struct word_count* b=list_entry(a,struct word_count,elem);
    if(strcmp(b->word,word)==0)
    return(b);
   };
   return NULL;
  #else
   struct list_elem *a;
   for(a= list_begin(wclist); a!=list_end(wclist);a=list_next(a))
   {
    struct word_count* b=list_entry(a,struct word_count,elem);
    if(strcmp(b->word,word)==0)
    return(b);
   };
   
  return NULL;
  #endif
  #endif
}

word_count_t* add_word(word_count_list_t* wclist, char* word) {
  /* TODO */
 word_count_t *a;  
  #ifdef PINTOS_LIST
  #ifdef PTHREADS
  a=find_word(wclist,word);
if(a!=NULL)
{ pthread_mutex_lock(&(wclist->lock));
  a->count=a->count+1;
  pthread_mutex_unlock(&(wclist->lock));
  return a;
}
else{
  struct word_count *new =(struct word_count *)malloc(sizeof(struct word_count));
  new->count=1;
  new->word=word; 
  pthread_mutex_lock(&(wclist->lock));
  list_push_back(&(wclist->lst),&(new->elem));
  pthread_mutex_unlock(&(wclist->lock));
  return new;
}
  return NULL;
#else
a=find_word(wclist,word);
if(a!=NULL)
{
  a->count=a->count+1;
  return a;
}
else{
  struct word_count *new =(struct word_count *)malloc(sizeof(struct word_count));
  new->count=1;
  new->word=word; 
  list_push_back(wclist, &(new->elem));
  return new;
}
  return NULL;

  #endif
  #endif
}

void fprint_words(word_count_list_t* wclist, FILE* outfile) { /* TODO */
  
struct list_elem *a;
#ifdef PINTOS_LIST
#ifdef PTHREADS

   for(a= list_begin(&(wclist->lst)); a!=list_end(&(wclist->lst));a=list_next(a))
   {
    struct word_count* b=list_entry(a,struct word_count,elem);
    fprintf(outfile,"%6d %20s\n", b->count,b->word);

}
#else
for(a= list_begin(wclist); a!=list_end(wclist);a=list_next(a))
   {
    struct word_count* b=list_entry(a,struct word_count,elem);
    fprintf(outfile,"%6d %6s\n", b->count,b->word);

}
#endif
#endif
}

static bool less_list(const struct list_elem* ewc1, const struct list_elem* ewc2, void* aux) {
  /* TODO */
struct word_count* a=list_entry(ewc1,struct word_count,elem);
struct word_count* b=list_entry(ewc2,struct word_count,elem);
if(less_count(a,b)|| (!less_count(a,b)&& !less_count(b,a)&& less_word(a,b)))
//if(a->count<b->count)
return true;
  return false;
}

void wordcount_sort(word_count_list_t* wclist,bool less(const word_count_t*, const word_count_t*)) {
  
#ifdef PINTOS_LIST
#ifdef PTHREADS

list_sort(&(wclist->lst), less_list, less);
#else
list_sort(wclist, less_list, less);
#endif
#endif
}

```


### Step:

Perform changes in make file accordingly

### Step:

Compiling file

![image](https://github.com/PoojaGoel-IIT/Pintos/assets/112702452/4e24cc3e-c340-4c3e-b783-ffd4443d2e72)


![image](https://github.com/PoojaGoel-IIT/Pintos/assets/112702452/b4ac54e0-aebc-40cd-968a-5c58f1b2332c)

# Question:
Shell 

```c

int cmd_cwd(struct tokens* tokens); //defining cmd_cwd function 
int cmd_cd(struct tokens* tokens);// defining cmd_cwd function 

{cmd_cwd, "cwd", "prints the current working directory to standard output,"},
    {cmd_cd, "cd", "takes one argument, a directory path, and changes the current working directory to that directory."}

int cmd_cd(unused struct tokens* tokens) 
{ 
  char*filename; //defining charater pointer
  filename=tokens_get_token(tokens,1);
  if(filename==NULL)
  {
fprintf(stderr,"please specify a directory.\n");
  }
else if(chdir(filename)!=0)
fprintf(stderr,"%s not found.\n",filename);
return 1;
  
}

int cmd_cwd(unused struct tokens* tokens) // cmd_cwd function uses getcwd function to read 
{
  char cwd[PATH_MAX];
   if (getcwd(cwd, sizeof(cwd)) != NULL) {
       printf("Current working directory: %s\n", cwd);
   } else {
       perror("getcwd() error");
       return 1;
   }
   return 0;
}
```

### step 2
Compiling 

![image](https://github.com/PoojaGoel-IIT/Pintos/assets/112702452/5cb9b092-64b8-4244-a0aa-736b38329519)






