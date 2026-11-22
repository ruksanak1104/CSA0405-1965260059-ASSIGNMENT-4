## Simulation C Program

```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define CYLINDERS 200
#define REQUESTS 10000

int distance(int a, int b)
{
    return abs(a - b);
}

void simulate(int n, int method)
{
    int req[REQUESTS];
    int done[REQUESTS] = {0};
    int head = 53;
    long long total = 0;
    int max_wait = 0;

    for (int i = 0; i < n; i++)
        req[i] = rand() % CYLINDERS;

    for (int i = 0; i < n; i++)
    {
        int selected = -1;

        if (method == 0)
            selected = i;
        else
        {
            int best = CYLINDERS + 1;

            for (int j = 0; j < n; j++)
            {
                if (!done[j])
                {
                    int d = distance(head, req[j]);

                    if (d < best)
                    {
                        best = d;
                        selected = j;
                    }
                }
            }
        }

        if (selected < 0)
            break;

        int movement = distance(head, req[selected]);

        total += movement;

        if (movement > max_wait)
            max_wait = movement;

        head = req[selected];
        done[selected] = 1;
    }

    printf("%-12s %10lld %15.2f %15d\n",
           method == 0 ? "FCFS" : "SSTF",
           total,
           (double)total / n,
           max_wait);
}

int main(void)
{
    srand(42);

    int loads[] = {1000, 5000, 10000};

    for (int i = 0; i < 3; i++)
    {
        printf("\nWorkload: %d requests\n", loads[i]);

        printf("%-12s %10s %15s %15s\n",
               "Algorithm", "Total", "Average", "Max movement");

        simulate(loads[i], 0);
        simulate(loads[i], 1);
    }

    return 0;
}
```

## Compilation

```bash
gcc -std=c11 -Wall -Wextra disk_simulation.c -o disk_simulation
```

## Execution

```bash
./disk_simulation
```
