# Pintos
POOJA GOEL

22210029
                                                                                                    
                                                                               Assignment 1
                                                                                                     
                                                                          CS 612 - Computer Systems
# Assignment 



Question:

Word-count:

Using Lists to Count Words:

Step 1:

Defining PINTOS_LIST in word_count_l.c file
```c

 #define PINTOS_LIST
 ```

Step2: complete functions in word_count_l.c file

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

Step3:

Compiling the code:

               root@DESKTOP-TN1I17B:/mnt/f/Semester 1/Assignment1/assignment1/word-count# make

               gcc -g3 -Wall -std=gnu99 -c word_count_multithread.c -o word_count_multithread.

               gcc -pthread word_count_multithread.o word_helpers.o -o word_count_m

               root@DESKTOP-TN1I17B:/mnt/f/Semester 1/Assignment1/assignment1/word-count# ./lwords gutenberg/alice.txt gutenberg/peter.txt

  ![image](https://github.com/PoojaGoel-IIT/Pintos/assets/112702452/f59888b6-b8c2-4a91-95d5-beb610548adf)
       
Count list is displaying is shown below:

   ![image](https://github.com/PoojaGoel-IIT/Pintos/assets/112702452/c1e72f2f-b5c8-44b8-8159-db6baf5a2cfb)

Using Multiple Threads to Count Words

Creating a new c file :- word_count_multithread.c
File uses pthreads and creates multiple threads to read files.




