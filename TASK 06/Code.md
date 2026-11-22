## C Program – Video Storage Simulation

```c
#include <stdio.h>

#define VIDEOS 10
#define CACHE_SIZE 3

int main(void)
{
    int access[VIDEOS] =
    {
        1, 2, 3, 1, 4, 2, 5, 1, 3, 2
    };

    int cache[CACHE_SIZE] = {-1, -1, -1};
    int total_reads = 0;
    int cache_hits = 0;
    int cache_misses = 0;

    printf("VIDEO STORAGE SIMULATION\n");
    printf("------------------------\n");

    for (int i = 0; i < VIDEOS; i++)
    {
        int found = 0;

        for (int j = 0; j < CACHE_SIZE; j++)
        {
            if (cache[j] == access[i])
            {
                found = 1;
                break;
            }
        }

        if (found)
        {
            cache_hits++;
            printf("Video %d: CACHE HIT\n", access[i]);
        }
        else
        {
            cache_misses++;
            total_reads++;

            cache[i % CACHE_SIZE] = access[i];

            printf("Video %d: CACHE MISS - DISK READ\n",
                   access[i]);
        }
    }

    printf("\nTOTAL REQUESTS: %d\n", VIDEOS);
    printf("CACHE HITS: %d\n", cache_hits);
    printf("CACHE MISSES: %d\n", cache_misses);
    printf("DISK READS: %d\n", total_reads);

    printf("CACHE HIT RATIO: %.2f%%\n",
           (double)cache_hits / VIDEOS * 100);

    return 0;
}
```

## Compilation

```bash
gcc -std=c11 -Wall -Wextra video_storage.c -o video_storage
```

## Execution

```bash
./video_storage
```


