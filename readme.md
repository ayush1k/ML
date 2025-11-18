+-----------------------------------------------------------+
|                   1. INPUT LAYER                          |
|                                                           |
|   [ Raw Text Files ]       [ Rules Config (rules.json) ]  |
|        (.txt)                          |                  |
+-----------+----------------------------+------------------+
|                            |
v                            v
+-----------------------------------------------------------+
|              2. PARALLEL PROCESSING ENGINE                |
|                                                           |
|  [ text_loader.py ] -> [ text_breaker.py ]                |
|        (Read)             (Clean & Chunk)                 |
|                                |                          |
|                                v                          |
|              [ parallel_break_loader2.py ]                |
|                     (Task Manager)                        |
|                                |                          |
|           +--------------------+--------------------+     |
|           |                    |                    |     |
|      [ Worker 1 ]         [ Worker 2 ]         [ Worker 3]|
|    (checker2.py)        (checker2.py)        (checker2.py)|
+-----------+--------------------+--------------------+-----+
|                    |                    |
+-----------+--------+--------------------+
|
v
+-----------------------------------------------------------+
|             3. STORAGE & DEDUPLICATION                    |
|                                                           |
|               [ storage2.py (Manager) ]                   |
|                          |                                |
|                < Duplicate Check? >----(Yes)---> [ Skip ] |
|                          |                                |
|                        (No)                               |
|                          |                                |
|                          v                                |
|            [   SQLite Database (checks.db)   ]            |
+--------------------------+--------------------------------+
|
v
+--------------------------+--------------------------------+
|                  4. FEEDBACK LOOP                         |
|                                                           |
|           [ storage_improver2.py ]                        |
|       (Analyzes DB -> Suggests New Rules)                 |
+-----------------------------------------------------------+
