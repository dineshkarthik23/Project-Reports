# Interview Prep — Full Answer Guide

A complete set of answers to every question in your prep list, organized by section and priority tier.

---

## 🔴 S-TIER

## 🐍 Python

**What are the key features of Python?**
Interpreted and dynamically typed, with clean/readable syntax. High-level, so it manages memory automatically (garbage collection). Supports multiple paradigms — OOP, functional, procedural. Huge standard library plus a massive third-party ecosystem (PyPI). Portable across platforms and easy to integrate with other languages.

**List vs tuple — differences and when would you use each?**
Lists are mutable, tuples are immutable. Tuples are slightly faster and can be used as dictionary keys or set members (since they're hashable); lists can't. Use a tuple for fixed data that shouldn't change (e.g., coordinates, a return value with a fixed shape); use a list when you need to add, remove, or modify elements.

**Set vs list vs tuple vs dictionary.**
- **List** — ordered, mutable, allows duplicates, indexed by position.
- **Tuple** — ordered, immutable, allows duplicates.
- **Set** — unordered, mutable, no duplicates, very fast membership checks (O(1) average).
- **Dictionary** — key-value pairs, insertion-ordered (3.7+), mutable, keys must be unique.

**Mutable vs immutable objects in Python.**
Mutable objects (list, dict, set) can be changed in place after creation. Immutable objects (int, float, str, tuple, frozenset) cannot — any "change" creates a new object. This matters for function arguments, hashing, and default parameter pitfalls.

**What is the difference between == and is?**
`==` checks value equality (do these objects contain the same data). `is` checks identity (are these the exact same object in memory).

**Shallow copy vs deep copy.**
`copy.copy()` (shallow) creates a new outer object, but nested objects inside it are still shared references with the original. `copy.deepcopy()` recursively copies every nested object too, so the copy is fully independent.

**What are *args and **kwargs?**
`*args` collects any extra positional arguments into a tuple; `**kwargs` collects extra keyword arguments into a dict. They let a function accept a flexible, variable number of arguments.

**What are list/dictionary/set comprehensions?**
A concise one-line syntax for building a collection from an iterable, optionally with a filter. E.g. `[x**2 for x in range(10)]`, `{k: v for k, v in items}`, `{x for x in data if x > 0}`. They're generally more readable and faster than an equivalent loop with `.append()`.

**What is a lambda function?**
An anonymous, single-expression function defined with the `lambda` keyword, e.g. `lambda x: x + 1`. Used for short, throwaway functions — commonly as an argument to `map`, `filter`, or `sorted(key=...)`.

**map(), filter(), and reduce() — when would you use them?**
`map()` applies a function to every item in an iterable and returns transformed values. `filter()` keeps only the items for which a function returns `True`. `reduce()` (from `functools`) cumulatively combines items into a single value (e.g., a running product or sum). Use `map`/`filter` for transformations/selections, `reduce` for aggregation.

**What is exception handling? Explain try, except, else, finally.**
A mechanism to handle runtime errors gracefully instead of crashing. `try` contains code that might raise an error; `except` catches and handles a specific exception type; `else` runs only if no exception occurred; `finally` always runs, whether or not an exception occurred — typically used for cleanup (closing files/connections).

**What are iterators and generators?**
An iterator is any object implementing `__iter__()` and `__next__()`, producing values one at a time and remembering its state. A generator is a simpler way to create an iterator — a function that uses `yield` instead of `return`, automatically producing an iterator without you writing the `__next__` logic yourself.

**Generator vs normal function returning a list.**
A normal function computes the entire list and returns it all at once, using memory proportional to its size. A generator yields one value at a time, lazily, so it can represent very large or infinite sequences using almost no memory — at the cost of only being iterable once.

**What is a decorator?**
A function that wraps another function to add or modify its behavior without changing its source code, applied using `@decorator_name` syntax above a function definition. Common uses: logging, timing, caching, access control.

**How does Python manage memory?**
Python allocates objects on a private heap. Each object has a reference count; when it drops to zero, the memory is freed immediately. A separate cyclic garbage collector periodically detects and cleans up reference cycles (objects referencing each other) that reference counting alone can't catch. Small-object allocation is optimized internally via `pymalloc`.

**Follow-ups:**

*What is garbage collection in Python?* The automatic process of reclaiming memory from objects that are no longer reachable/referenced by the program, so the programmer doesn't need to manually free memory.

*What is reference counting?* Every object tracks how many references point to it. When that count hits zero, Python immediately deallocates the object's memory.

*What is the GIL?* The Global Interpreter Lock — a mutex in CPython that allows only one thread to execute Python bytecode at a time. It simplifies memory management but prevents true parallel execution of CPU-bound Python threads.

*Multithreading vs multiprocessing in Python.* Threads share the same memory space and are good for I/O-bound tasks (waiting on network/disk), but are limited by the GIL for CPU-bound work. Processes have separate memory and truly run in parallel across CPU cores, making them better for CPU-bound tasks, at the cost of higher memory use and inter-process communication overhead.

*Why can Python be slower than languages such as C++/Go?* Python is interpreted (bytecode run by a virtual machine) rather than compiled to native machine code. Dynamic typing adds runtime type-checking overhead, and the GIL limits parallelism. These trade some raw speed for flexibility and developer productivity.

---

## 🗄️ SQL

**What is the difference between WHERE and HAVING?**
`WHERE` filters individual rows *before* grouping/aggregation happens. `HAVING` filters *groups* after `GROUP BY`, and can reference aggregate functions (e.g. `HAVING COUNT(*) > 5`), which `WHERE` cannot do.

**Explain all types of SQL joins.**
- **INNER JOIN** — only rows matching in both tables.
- **LEFT JOIN** — all rows from the left table, matched right-table columns or NULL.
- **RIGHT JOIN** — all rows from the right table, matched left-table columns or NULL.
- **FULL OUTER JOIN** — all rows from both tables, NULL where there's no match on either side.
- **CROSS JOIN** — Cartesian product of both tables (every row paired with every row).
- **SELF JOIN** — a table joined to itself, useful for hierarchical/comparative data.

**INNER JOIN vs LEFT JOIN.**
INNER JOIN returns only matching rows from both tables. LEFT JOIN returns every row from the left table regardless of a match, filling unmatched right-side columns with NULL.

**GROUP BY vs ORDER BY.**
`GROUP BY` collapses rows sharing the same value(s) into groups, usually paired with aggregate functions (SUM, COUNT, AVG). `ORDER BY` simply sorts the final result set — it doesn't change the number of rows.

**DELETE vs TRUNCATE vs DROP.**
`DELETE` removes rows (optionally filtered with `WHERE`), is logged row-by-row, fires triggers, and can be rolled back. `TRUNCATE` removes all rows at once, resets auto-increment counters, is minimally logged, and can't use `WHERE`. `DROP` removes the entire table (structure and data) permanently.

**UNION vs UNION ALL.**
`UNION` combines two result sets and removes duplicate rows (requires an extra sort/dedup step). `UNION ALL` combines them and keeps duplicates, so it's faster.

**What is a subquery?**
A query nested inside another query — used inside `SELECT`, `WHERE`, or `FROM` clauses to compute an intermediate result the outer query then uses.

**What is a correlated subquery?**
A subquery that references a column from the outer query, so it's re-evaluated once for every row processed by the outer query (as opposed to a regular subquery, which runs once).

**What is a primary key vs foreign key?**
A primary key uniquely identifies each row in a table (must be unique and not null). A foreign key is a column in one table that references a primary key in another table, enforcing a relationship between the two.

**What is normalization?**
The process of organizing tables to reduce data redundancy and avoid update/insert/delete anomalies, by splitting data into related tables and defining relationships (progressing through 1NF, 2NF, 3NF, etc.).

**What are indexes and why are they used?**
Data structures (typically B-trees) built on one or more columns to speed up data lookups, at the cost of extra storage and slightly slower writes (since indexes must also be updated).

**What are ACID properties?**
**A**tomicity (a transaction fully completes or fully fails), **C**onsistency (a transaction moves the database from one valid state to another), **I**solation (concurrent transactions don't interfere with each other), **D**urability (once committed, changes survive system failures).

**What is a transaction?**
A sequence of one or more operations executed as a single logical unit of work — either it all commits successfully, or it's rolled back entirely if any part fails.

**Find the second-highest salary.**
```sql
SELECT MAX(salary) AS second_highest
FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);
```

**Find the highest-paid employee in each department.**
```sql
SELECT department_id, employee_id, salary
FROM (
  SELECT department_id, employee_id, salary,
         RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rnk
  FROM employees
) ranked
WHERE rnk = 1;
```

### 🔥 Query questions

**Find duplicate records.**
```sql
SELECT col, COUNT(*)
FROM my_table
GROUP BY col
HAVING COUNT(*) > 1;
```

**Find employees earning more than the average salary.**
```sql
SELECT * FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

**Find customers who have never placed an order.**
```sql
SELECT c.*
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
WHERE o.order_id IS NULL;
```

**Find the top 3 salaries in each department.**
```sql
SELECT * FROM (
  SELECT *, DENSE_RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rnk
  FROM employees
) t
WHERE rnk <= 3;
```

**Find the second-highest distinct salary.**
```sql
SELECT DISTINCT salary
FROM employees
ORDER BY salary DESC
LIMIT 1 OFFSET 1;
```

**Find departments having more than 5 employees.**
```sql
SELECT department_id, COUNT(*) AS emp_count
FROM employees
GROUP BY department_id
HAVING COUNT(*) > 5;
```

**Calculate a running total using a window function.**
```sql
SELECT employee_id, salary,
       SUM(salary) OVER (ORDER BY employee_id) AS running_total
FROM employees;
```

**Find the rank of employees based on salary.**
```sql
SELECT employee_id, salary,
       RANK() OVER (ORDER BY salary DESC) AS salary_rank
FROM employees;
```

**Difference between RANK(), DENSE_RANK(), and ROW_NUMBER().**
`RANK()` gives tied rows the same rank, then skips the next rank(s) (1,1,3). `DENSE_RANK()` also ties rows equally but never skips (1,1,2). `ROW_NUMBER()` gives every row a unique sequential number regardless of ties (1,2,3).

**Find the most frequently purchased product.**
```sql
SELECT product_id, COUNT(*) AS times_purchased
FROM order_items
GROUP BY product_id
ORDER BY times_purchased DESC
LIMIT 1;
```

---

## 🤖 TensorFlow / Keras / ML

**What is TensorFlow?**
An open-source library (originally by Google) for numerical computation and machine learning, representing computations as graphs of tensors flowing through operations. It supports building, training, and deploying models across CPUs, GPUs, and TPUs.

**What is Keras?**
A high-level neural network API, now built into TensorFlow as `tf.keras`, designed to make building and training models fast and simple with a readable, user-friendly syntax.

**TensorFlow vs Keras.**
TensorFlow is the full, lower-level framework handling graph execution, automatic differentiation, and deployment. Keras sits on top of TensorFlow as a high-level API for quickly defining and training models without dealing with low-level graph details.

**What is a tensor?**
A multi-dimensional array — a generalization of scalars (0D), vectors (1D), and matrices (2D) to any number of dimensions. It's the core data structure used to represent inputs, weights, and outputs in ML frameworks.

**What is a neural network?**
A computational model loosely inspired by the brain, made of layers of interconnected nodes ("neurons"). Each connection has a weight; the network learns by adjusting these weights to map inputs to desired outputs.

**Explain the basic architecture of a neural network.**
An **input layer** receives the raw features. One or more **hidden layers** apply weighted sums and activation functions to learn increasingly abstract representations. An **output layer** produces the final prediction (e.g., a class probability or a numeric value).

**What is an activation function?**
A function applied to a neuron's weighted output that introduces non-linearity, letting the network learn complex, non-linear patterns. Without one, stacking layers would collapse into a single linear transformation.

**ReLU vs sigmoid vs softmax.**
ReLU outputs `max(0, x)` — fast to compute, helps avoid vanishing gradients, and is the default for hidden layers. Sigmoid squashes values into (0,1), used for binary classification outputs. Softmax converts a vector of logits into a probability distribution across classes, used for multi-class classification outputs.

**What is forward propagation?**
The process of passing input data through the network layer by layer to compute the final output/prediction.

**What is backpropagation?**
The algorithm that computes how much each weight contributed to the prediction error, by propagating the loss gradient backward through the network using the chain rule — this is what makes learning possible.

**What is gradient descent?**
An optimization algorithm that repeatedly updates model weights in the direction that most reduces the loss function, based on the gradient computed via backpropagation.

**What is an epoch?**
One complete pass of the entire training dataset through the model.

**Batch size vs epoch vs iteration.**
Batch size is the number of training samples processed before the model's weights are updated. An epoch is one full pass through the whole dataset. An iteration is one weight update, i.e., processing one batch — so `iterations per epoch = dataset_size / batch_size`.

**What is a loss function?**
A function that measures how far the model's predictions are from the true values (e.g., Mean Squared Error for regression, Cross-Entropy for classification). Training aims to minimize it.

**What is an optimizer?**
The algorithm that updates model weights using the gradients computed from the loss (e.g., SGD, Adam, RMSprop), controlling how the model actually learns.

### 🔥 Keras-specific

**What is Sequential in Keras?** A simple API for building a model as a linear stack of layers, one after another.

**Sequential vs Functional API.** Sequential only supports a single input, single output, linear layer stack. The Functional API supports more complex architectures — multiple inputs/outputs, shared layers, branching and merging paths.

**What does model.compile() do?** Configures the model for training by specifying the optimizer, loss function, and evaluation metrics.

**What does model.fit() do?** Trains the model on the given data for a specified number of epochs, updating weights via backpropagation.

**What does model.evaluate() do?** Computes the loss and specified metrics on a given (typically held-out) dataset, without updating weights.

**What does model.predict() do?** Runs the trained model on new input data and returns predictions.

**What are callbacks?** Objects you can attach to `model.fit()` that run custom logic at specific points during training (e.g., end of each epoch) — used for logging, saving checkpoints, or adjusting training dynamically.

**What is EarlyStopping?** A callback that monitors a chosen metric (e.g., validation loss) and stops training once it stops improving, to prevent overfitting and wasted compute.

**What is Dropout?** A regularization technique that randomly zeroes out a fraction of neurons during each training step, forcing the network not to over-rely on any single neuron, which reduces overfitting.

**What is Batch Normalization?** A technique that normalizes a layer's inputs across each mini-batch (mean 0, variance 1), which stabilizes and speeds up training and can improve generalization.

---

## 📊 NumPy + Pandas

### NumPy

**What is NumPy and why is it used?**
A library for efficient numerical computing in Python, providing fast, memory-efficient n-dimensional arrays and vectorized operations — much faster than working with plain Python lists for numeric work.

**Python list vs NumPy array.**
NumPy arrays are homogeneous (single data type), stored contiguously in memory, and support vectorized operations that run in optimized C code. Python lists are heterogeneous, more flexible, but much slower for numeric computation.

**What is vectorization?**
Performing an operation on an entire array at once (e.g., `a + b`) instead of looping element-by-element in Python — this pushes the loop down into fast, compiled C code.

**What is broadcasting?**
NumPy's rule set for applying operations between arrays of different but compatible shapes by implicitly "stretching" the smaller array, without actually copying data.

**What is the difference between reshape() and resize()?**
`reshape()` returns a new view of the array with a different shape (total element count must match) and doesn't modify the original unless reassigned. `resize()` modifies the array in place and can change the total number of elements (padding with zeros or truncating).

**np.array() vs Python list.**
`np.array()` converts a list into a fixed-type NumPy array supporting fast vectorized math; a plain Python list stays a flexible, general-purpose but slower container.

**What is the difference between axis=0 and axis=1?**
`axis=0` operates down the rows (i.e., column-wise, collapsing rows). `axis=1` operates across the columns (i.e., row-wise, collapsing columns).

**What are NumPy views vs copies?**
A view (e.g., from slicing) shares the same underlying memory as the original array — changing one changes the other. A copy (e.g., via `.copy()`) is a fully independent array in new memory.

### Pandas

**Series vs DataFrame.**
A Series is a one-dimensional labeled array (like a single column). A DataFrame is a two-dimensional labeled table made up of multiple Series as columns.

**loc vs iloc.**
`loc` selects rows/columns by label (index name). `iloc` selects by integer position, regardless of the label.

**How do you handle missing values in Pandas?**
Detect them with `isnull()`/`isna()`. Remove rows/columns with `dropna()`, or impute them with `fillna()` using a constant, mean, median, mode, or forward/backward fill.

**merge() vs concat() vs join().**
`merge()` combines DataFrames based on common column(s)/keys, like a SQL join. `concat()` stacks DataFrames along an axis (rows or columns) without key-based matching. `join()` combines DataFrames based on their index.

**How does groupby() work?**
It follows a split-apply-combine pattern: splits the data into groups based on column value(s), applies an aggregation/transformation function to each group, then combines the results back into a single structure.

**How do you detect and remove duplicates?**
`df.duplicated()` flags duplicate rows (returns a boolean Series); `df.drop_duplicates()` removes them.

**How do you filter rows based on multiple conditions?**
Use boolean masking with `&` (and) / `|` (or), each condition in parentheses: `df[(df.a > 5) & (df.b == 'x')]`.

### 🔥 Practical Pandas

**How would you find the top 5 customers by total spending?**
```python
df.groupby('customer')['amount'].sum().sort_values(ascending=False).head(5)
```

**How would you calculate the average salary by department?**
```python
df.groupby('department')['salary'].mean()
```

**How would you identify missing values column-wise?**
```python
df.isnull().sum()
```

**How would you replace missing values with mean/median?**
```python
df['col'] = df['col'].fillna(df['col'].mean())   # or .median()
```

**How would you combine two DataFrames?**
```python
pd.concat([df1, df2])                      # stack rows
pd.merge(df1, df2, on='key', how='inner')  # join on a key
```

**How would you sort a DataFrame by multiple columns?**
```python
df.sort_values(['col1', 'col2'], ascending=[True, False])
```

**How would you create a pivot table?**
```python
df.pivot_table(values='sales', index='region', columns='product', aggfunc='sum')
```

**apply() vs vectorized operations.**
`apply()` runs a Python function element/row/column-wise — flexible but slower since it can't use optimized C loops. Vectorized operations (built-in Pandas/NumPy methods) operate on the whole array at once and are much faster; prefer them whenever possible.

**How would you find outliers in a DataFrame?**
Use the IQR method (flag values below `Q1 - 1.5*IQR` or above `Q3 + 1.5*IQR`) or a z-score threshold (e.g., `|z| > 3`).

**How would you remove duplicate rows based on selected columns?**
```python
df.drop_duplicates(subset=['col1', 'col2'])
```

---

## 🧠 NLP

**What is NLP?**
A field of AI focused on enabling computers to understand, interpret, and generate human language — spanning tasks like text classification, translation, and language generation.

**What is tokenization?**
The process of splitting text into smaller units — tokens — which can be words, subwords, or sentences, depending on the tokenizer used.

**What is a vocabulary?**
The set of unique tokens a model recognizes, typically built by scanning the training corpus and assigning each unique token an index.

**What are stop words?**
Very common words (e.g., "the," "is," "and") that carry little unique meaning and are often removed during preprocessing to reduce noise — though modern deep learning models sometimes keep them since context matters.

**Stemming vs lemmatization.**
Stemming crudely chops word endings using fixed rules, which can produce non-real words (e.g., "studies" → "studi"). Lemmatization uses vocabulary and grammatical analysis to return the proper dictionary base form (e.g., "studies" → "study"), so it's slower but more linguistically correct.

**What are word embeddings?**
Dense, lower-dimensional vector representations of words (e.g., Word2Vec, GloVe) that capture semantic relationships — similar words end up close together in vector space.

**One-hot encoding vs word embeddings.**
One-hot encoding represents each word as a sparse binary vector with no notion of similarity between words. Embeddings are dense, much lower-dimensional, learned representations that capture semantic meaning and relationships.

**What is an RNN?**
A Recurrent Neural Network — an architecture with loops that maintain a hidden state across time steps, letting it process sequential data (like text) by "remembering" earlier inputs.

**RNN vs LSTM.**
Plain RNNs struggle with long sequences due to vanishing gradients, making it hard to retain long-term dependencies. LSTM (Long Short-Term Memory) networks add gates (input, forget, output) and a separate cell state that let them selectively retain or discard information over much longer sequences.

**How does next-word prediction work?**
The model takes a sequence of preceding words (encoded as embeddings), processes them through recurrent/attention layers to build a context representation, then outputs a probability distribution over the entire vocabulary (usually via softmax) indicating the most likely next word.

### Project-related

**How did you preprocess text in your project?**
Typical pipeline: lowercase the text, strip punctuation/special characters, tokenize into words, optionally remove stop words, and map tokens to integer indices using the vocabulary.

**How did you create the vocabulary?**
Collect all unique tokens across the training corpus, optionally sort by frequency, and assign each a unique integer index — reserving special indices for unknown/out-of-vocabulary and padding tokens.

**How did you convert words into numerical representations?**
Map each token to its vocabulary index, then feed those indices into an `Embedding` layer, which learns a dense vector representation for each word during training.

**Why do you need padding?**
Neural network layers process inputs in fixed-size batches/tensors, but real sentences vary in length — padding shorter sequences to a common length lets them be batched together.

**What would you use as the output layer for next-word prediction?**
A Dense layer with a number of units equal to the vocabulary size, using a softmax activation to output a probability distribution over all possible next words.

**Why is softmax appropriate for next-word prediction?**
Because predicting the next word is a multi-class classification problem over the vocabulary, and softmax converts raw output scores (logits) into a proper probability distribution that sums to 1.

**What loss function would you use?**
Categorical cross-entropy (or sparse categorical cross-entropy if labels are given as integer indices rather than one-hot vectors).

**How would you evaluate a next-word prediction model?**
Common approaches: accuracy on held-out data, perplexity (how confidently/correctly the model predicts the true sequence), and qualitative review of generated text samples.

**What problems occur when sequences are very long?**
Vanishing/exploding gradients, higher memory and compute cost, and difficulty retaining information from far earlier in the sequence.

**Why might an LSTM perform better than a basic RNN?**
Its gating mechanism and separate cell state let it selectively preserve important information over long ranges, avoiding the vanishing-gradient problem that limits how far back a plain RNN can "remember."

---

## 💾 MySQL + Firestore

**MySQL vs Firestore.**
MySQL is a relational (SQL) database — structured schema, tables, strong support for joins and transactions, strict consistency. Firestore is a NoSQL document database (Firebase/Google Cloud) storing flexible JSON-like documents in collections, built for real-time sync, offline support, and easy horizontal scaling.

**SQL vs NoSQL.**
SQL databases are relational, schema-enforced, queried with structured SQL, and strong on complex relationships/ACID transactions. NoSQL databases (document, key-value, graph, column-family) are schema-flexible and prioritize scalability and flexibility, often at the cost of strict consistency.

**When would you choose MySQL over Firestore?**
When data is highly relational, requires complex joins/multi-table transactions, needs a strict, well-defined schema, and demands strong consistency (e.g., financial or inventory systems).

**When would you choose Firestore over MySQL?**
When you need real-time data sync, offline support, a flexible/evolving schema, and simple horizontal scalability (e.g., a mobile chat app or collaborative tool).

**What is a relational database?**
A database that stores data in structured tables of rows and columns, with relationships enforced via keys, and queried using SQL.

**What is a document database?**
A NoSQL database that stores data as flexible, semi-structured documents (often JSON-like), grouped into collections, without requiring a fixed schema across documents.

**What is a collection in Firestore?**
A container that groups related documents together — conceptually similar to a table in SQL, but without an enforced schema.

**How are relationships represented in Firestore?**
Typically by storing a referenced document's ID inside another document, by nesting related data directly, or by using subcollections — since Firestore has no native join operation.

**What are the advantages and disadvantages of denormalization in Firestore?**
Advantages: faster reads (related data is already co-located, fewer round trips), which fits Firestore's document-read model well. Disadvantages: data duplication and the risk that duplicated copies fall out of sync when the source data changes.

**How would you design a database for an application with users, products, and orders?**
In SQL: `Users`, `Products`, and `Orders` tables, with `Orders` referencing `user_id`, and an `OrderItems` junction table linking orders to products (many-to-many) with quantity/price. In Firestore: separate `users`, `products`, and `orders` collections, where each order document stores the user reference and an array/subcollection of ordered items with product references and quantities.

### 🔥 Scenario questions

**You have highly relational financial data. MySQL or Firestore?**
MySQL — financial data needs strong consistency, ACID transactions, and often complex multi-table joins/reporting that relational databases handle best.

**You need real-time synchronization for a mobile application. Which would you choose?**
Firestore — its real-time listeners push data changes to clients instantly, and it has built-in offline support for mobile apps.

**Your application has millions of records. What database considerations would you make?**
Proper indexing on frequently queried columns, partitioning/sharding for scale, read replicas or caching layers (e.g., Redis) to offload read traffic, and choosing the database type based on access patterns (heavy joins → relational; simple key lookups at scale → NoSQL).

**How would you improve a slow MySQL query?**
Add appropriate indexes, avoid `SELECT *`, use `EXPLAIN` to inspect the query plan, simplify unnecessary subqueries/joins, ensure WHERE clauses can use indexes (avoid functions on indexed columns), and consider caching or denormalizing for read-heavy cases.

**How would you secure database access from a client application?**
Never connect directly from the client to the database — route access through a backend API. Use parameterized queries/prepared statements to prevent SQL injection, enforce authentication and authorization, apply least-privilege database accounts, and encrypt data in transit.

---

## 🟠 JavaScript

**var vs let vs const.**
`var` is function-scoped, hoisted, and can be redeclared/reassigned. `let` is block-scoped and can be reassigned but not redeclared in the same scope. `const` is block-scoped and cannot be reassigned after initialization (though objects/arrays it holds can still be mutated internally).

**== vs ===.**
`==` compares values after type coercion (e.g., `"5" == 5` is true). `===` compares both value and type with no coercion (`"5" === 5` is false).

**What is hoisting?**
JavaScript's behavior of moving variable and function declarations to the top of their scope before execution. `var` declarations are hoisted and initialized as `undefined`; `let`/`const` are hoisted but stay uninitialized until their declaration line (the "temporal dead zone").

**What is a closure?**
A function that retains access to variables from its enclosing (outer) scope even after that outer function has finished executing.

**What is a callback function?**
A function passed as an argument to another function, to be invoked later — often once an asynchronous operation (like a network request) completes.

**What are promises?**
Objects representing the eventual result of an asynchronous operation, with three states — pending, fulfilled, or rejected — handled via `.then()` for success and `.catch()` for errors.

**async/await vs promises.**
`async`/`await` is syntactic sugar over promises that lets asynchronous code read like synchronous code, avoiding long `.then()` chains and generally improving readability and error handling (via `try`/`catch`).

**What is the JavaScript event loop?**
The mechanism that lets single-threaded JavaScript handle asynchronous work: it continuously checks whether the call stack is empty, and if so, pulls the next callback from the task/microtask queues to execute.

**What is the difference between synchronous and asynchronous JavaScript?**
Synchronous code executes line by line, blocking further execution until each step finishes. Asynchronous code lets long-running operations (network calls, timers) run in the background without blocking, with results delivered later via callbacks, promises, or async/await.

**What are arrow functions and how do they differ from regular functions?**
Arrow functions have concise syntax (`(x) => x + 1`), don't bind their own `this` (they inherit it from the enclosing scope), can't be used as constructors, and don't have their own `arguments` object.

### Follow-ups

**What is destructuring?** Syntax for unpacking values from arrays or properties from objects into individual variables in one line, e.g. `const { a, b } = obj;`.

**Spread vs rest operator.** Both use `...`, but spread *expands* an iterable into individual elements (e.g., `[...arr1, ...arr2]`), while rest *collects* multiple elements into a single array/object, typically in function parameters (`function f(...args) {}`).

**What are higher-order functions?** Functions that take other functions as arguments and/or return a function — e.g., `map`, `filter`, `reduce`.

**map() vs filter() vs reduce().** `map()` transforms each element into a new array of the same length. `filter()` keeps only elements matching a condition. `reduce()` folds all elements down into a single accumulated value.

**What is JSON?** JavaScript Object Notation — a lightweight, text-based format for representing structured data as key-value pairs, widely used for data interchange between client and server.

**How does JavaScript handle objects?** Objects are reference types stored in memory; a variable holding an object actually holds a reference to it, so assigning it to another variable or passing it to a function shares the same underlying object.

**What is this?** A keyword referring to the execution context of a function — its value depends on how the function was called (as a method, a plain function, with `new`, or via `call`/`apply`/`bind`); arrow functions instead inherit `this` from their surrounding scope.

**What is the DOM?** The Document Object Model — a tree-structured, in-memory representation of an HTML/XML document that JavaScript can read, traverse, and modify to update what's displayed.

**What is event bubbling?** When an event fires on an element, it also propagates upward and triggers the same event on each ancestor element in the DOM tree, unless stopped with `stopPropagation()`.

**What is localStorage vs sessionStorage?** Both store key-value string data in the browser. `localStorage` persists indefinitely across sessions/tabs until explicitly cleared. `sessionStorage` only persists for the lifetime of that specific tab/session.

---

## 🟠 React.js

**What is React?**
A JavaScript library for building user interfaces out of small, reusable, composable components, using a declarative style — you describe what the UI should look like for a given state, and React handles updating the DOM.

**Why use React?**
Reusable components, an efficient Virtual DOM for fast updates, a huge ecosystem and community, a declarative programming model that's easier to reason about than manual DOM manipulation, and strong tooling/testing support.

**What is a component?**
An independent, reusable building block of UI, written as a function (or class) that returns JSX describing what should be rendered.

**Functional component vs class component.**
Functional components are plain JavaScript functions that use Hooks to manage state and lifecycle. Class components extend `React.Component` and use `this.state` plus lifecycle methods (`componentDidMount`, etc.). Functional components with Hooks are now the standard approach.

**What are props?**
Read-only data passed from a parent component into a child component, used to configure or customize how the child renders.

**What is state?**
Data managed internally within a component that can change over time; updating it causes React to re-render the component.

**Props vs state.**
Props come from outside and are immutable within the receiving component. State is managed internally by the component itself and can be changed (via `setState`/`useState`), triggering a re-render.

**What is the Virtual DOM?**
A lightweight, in-memory copy of the real DOM. React compares ("diffs") the new virtual DOM against the previous version and only applies the minimal necessary changes to the real DOM, which is much faster than re-rendering everything.

**What is useState()?**
A Hook that adds local state to a functional component, returning a pair: the current state value and a setter function to update it.

**What is useEffect()?**
A Hook for running side effects in functional components — such as data fetching, subscriptions, or manual DOM changes — executed after the component renders.

### Follow-ups

**What is the dependency array in useEffect()?** The second argument to `useEffect` — an array of values the effect depends on. The effect re-runs only when one of these values changes between renders; an empty array means "run once, on mount."

**What causes a React component to re-render?** A change in its own state, a change in props passed from its parent, its parent re-rendering, or a change in a Context value it subscribes to.

**What is conditional rendering?** Rendering different UI depending on a condition — using `if` statements, ternary operators, or short-circuit `&&` directly inside JSX.

**What are controlled components?** Form elements (inputs, selects) whose value is driven entirely by React state via `value` and `onChange`, rather than by the DOM's own internal state.

**What is prop drilling?** Passing props down through several layers of components that don't actually need them, purely to get data to a deeply nested child — often a sign that Context or state management would help.

**How can you share state between components?** Lift the state up to their closest common ancestor and pass it down as props, or use the Context API (or a state library like Redux/Zustand) for state needed more broadly.

**What are keys in React lists?** Unique identifiers assigned to items rendered in a list, so React can efficiently track which items changed, were added, or were removed between renders.

**Why should you not use array index as a key in some cases?** If the list can be reordered, filtered, or have items inserted/removed, index-based keys can cause React to mismatch elements to the wrong data, leading to bugs like incorrect state showing up on the wrong row.

**What is lifting state up?** Moving state from a child component to its closest common ancestor, so multiple sibling components can access and update the same shared data via props.

**How would you call a REST API from React?** Use `fetch` or a library like `axios`, typically inside a `useEffect` hook to trigger the call on mount, storing the result in state via `useState` so the component re-renders with the fetched data.

---

## 🟠 Go

**Why Go?**
Designed for simplicity and fast compilation, with built-in concurrency (goroutines/channels), a solid standard library, static typing with automatic garbage collection, and performance close to C/C++ — good for building fast, reliable network services.

**What are goroutines?**
Lightweight, independently executing functions managed by the Go runtime rather than the OS. They start with a very small stack that grows as needed, so you can spawn thousands of them cheaply.

**Goroutine vs thread.**
Goroutines are much lighter weight (a few KB of stack, growable) and are scheduled by Go's own runtime scheduler across OS threads. Native OS threads are heavier and managed directly by the operating system, with higher context-switching overhead.

**What are channels?**
Typed conduits used to send and receive values between goroutines, providing a safe way to synchronize and communicate without explicit locks.

**How does Go handle concurrency?**
Through goroutines (concurrent execution) and channels (communication between them), following the philosophy "don't communicate by sharing memory; share memory by communicating."

**Array vs slice in Go.**
Arrays have a fixed size known at compile time. Slices are dynamically-sized, flexible views over an underlying array, and are what's actually used in most Go code.

**What is a struct?**
A composite type that groups related fields together under one name — similar to a class's data, though in Go, methods are attached separately via receiver functions rather than defined inside the type itself.

**What are pointers in Go?**
Variables that store the memory address of another variable. `&x` gets the address of `x`, and `*p` dereferences a pointer `p` to access/modify the value it points to.

### Project-related

**Why did you use Go for the cache simulator backend?**
A strong framing: Go's performance and built-in concurrency made it well-suited to handling multiple simultaneous cache operations, it compiles to a single fast binary that's simple to deploy, and its standard library makes building a lightweight REST API straightforward.

**How did your Go REST API communicate with React?**
The Go backend exposes HTTP endpoints (e.g., via `net/http` or a router like Gin), and the React frontend calls those endpoints using `fetch`/`axios`, exchanging data as JSON in request/response bodies.

**What is a REST API?**
An architectural style for networked applications that uses stateless HTTP requests and standard methods (GET/POST/PUT/PATCH/DELETE) to operate on resources identified by URLs.

**GET vs POST vs PUT vs PATCH vs DELETE.**
GET retrieves a resource. POST creates a new resource. PUT replaces a resource entirely. PATCH partially updates a resource. DELETE removes a resource.

**What are HTTP status codes?**
Three-digit codes indicating the outcome of an HTTP request: 2xx = success, 3xx = redirection, 4xx = client error, 5xx = server error.

**How would you handle errors in Go?**
Go doesn't use exceptions for normal error handling — functions return an explicit `error` value alongside their result, and the caller checks `if err != nil` immediately after the call to decide how to handle it.

---

## 🟡 Matplotlib

**What is Matplotlib used for?**
A Python library for creating static, animated, and interactive visualizations — line charts, bar charts, histograms, scatter plots, and more — commonly used for exploring and presenting data.

**What is the difference between a line chart, bar chart and scatter plot?**
A line chart shows trends over continuous/ordered data (like time). A bar chart compares values across discrete categories. A scatter plot shows individual data points to reveal the relationship or correlation between two numeric variables.

**When would you use a histogram?**
When you want to see the distribution/frequency of a single continuous numeric variable, by grouping values into bins.

**How would you visualize the distribution of a variable?**
A histogram, a box plot (shows quartiles/outliers), or a KDE (density) plot.

**How would you visualize the relationship between two numerical variables?**
A scatter plot — optionally with a trend/regression line overlaid, or by computing a correlation coefficient alongside it.

---

## 🟡 Flutter

**What is Flutter?**
Google's open-source UI toolkit for building natively compiled apps for mobile, web, and desktop from a single Dart codebase.

**What is a widget?**
The fundamental building block of a Flutter UI — everything from layout containers to text to buttons is a widget, composed together in a tree.

**StatelessWidget vs StatefulWidget.**
A `StatelessWidget` is immutable — it doesn't hold state that changes over time. A `StatefulWidget` can hold mutable state and rebuild itself whenever that state changes via `setState()`.

**What is setState()?**
A method that tells the framework a `StatefulWidget`'s internal state has changed, triggering the widget to rebuild and reflect the new state.

**What is the widget tree?**
The hierarchical structure describing how widgets are nested inside one another to compose the full UI.

**How does navigation work in Flutter?**
Through the `Navigator` widget, which maintains a stack of routes (screens). You push a new screen with `Navigator.push()` and return to the previous one with `Navigator.pop()`.

**How do you make an API call in Flutter?**
Typically using the `http` package (or `dio`) to send an async request, `await` the response, decode the JSON body, and update state to display the fetched data.

**How would you manage application state?**
For simple local state, `setState()` is enough. For state shared across many widgets, use `Provider`, `Riverpod`, `BLoC`, or `Redux`, depending on the app's complexity.

---

## 🟡 Git + GitHub

**What is Git?**
A distributed version control system that tracks changes to files over time, enabling branching, history, and collaboration without needing a central server for every operation.

**Git vs GitHub.**
Git is the version control tool itself, running locally on your machine. GitHub is a cloud platform for hosting Git repositories, adding collaboration features like pull requests, issues, and code review on top of Git.

**git clone vs git pull.**
`git clone` creates a full local copy of a remote repository the first time. `git pull` fetches and merges new changes from the remote into an already-cloned local repository.

**git fetch vs git pull.**
`git fetch` downloads new commits from the remote but doesn't merge them into your current branch. `git pull` does a fetch followed immediately by a merge (or rebase).

**What is a branch?**
An independent, parallel line of development within a repository, letting you work on a feature or fix in isolation from the main codebase.

**How do you create and switch branches?**
```bash
git branch feature-x         # create
git checkout feature-x       # switch
git checkout -b feature-x    # create and switch in one step
```

**What is a merge conflict?**
It happens when Git can't automatically reconcile changes made to the same lines of a file across two branches being merged, requiring you to manually decide which changes to keep.

**How do you resolve a merge conflict?**
Open the flagged files, find the conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`), manually edit to keep the correct combination of changes, remove the markers, then `git add` the resolved files and commit.

**git merge vs git rebase.**
`git merge` combines two branches by creating a new merge commit, preserving the full, non-linear history. `git rebase` replays your branch's commits on top of another branch, producing a cleaner, linear history — but it rewrites commit history, so it's riskier on shared branches.

**What is a commit?**
A saved snapshot of your staged changes, along with a message describing what changed and why.

### Practical

**Explain your normal Git workflow.**
Pull the latest changes from main, create a feature branch, make and commit changes incrementally with clear messages, push the branch, open a pull request for review, address feedback, then merge into main once approved.

**What is .gitignore?**
A file listing patterns for files/directories Git should never track — e.g., build output, `node_modules`, environment files with secrets.

**What is git stash?**
Temporarily saves your uncommitted changes so you can switch branches or contexts cleanly, then reapply them later with `git stash pop`.

**What is git reset?**
Moves the current branch pointer to a different (usually earlier) commit. Depending on the mode — `--soft`, `--mixed`, or `--hard` — it can also affect the staging area and working directory.

**What is git revert?**
Creates a *new* commit that undoes the changes of a specified previous commit, without rewriting history — the safe way to undo something on a shared/public branch.

**How would you undo the last commit?**
```bash
git reset --soft HEAD~1   # undo commit, keep changes staged
git revert HEAD           # safe undo via a new commit (for shared branches)
```

**How would you work with another developer on the same project?**
Use separate feature branches, push and pull regularly to stay in sync, communicate about who's touching which files, and resolve conflicts through pull requests and code review rather than pushing straight to main.

---

## 🟡 Linux

**What is Linux?**
An open-source, Unix-like operating system kernel — and the family of distributions built around it — widely used for servers, development environments, and embedded systems due to its stability, performance, and flexibility.

**What is the difference between a process and a service?**
A process is any running instance of a program. A service (daemon) is a special long-running background process, usually managed by the system's init system (e.g., `systemd`), that provides ongoing functionality like a web or database server.

**What does chmod do?**
Changes the read/write/execute permissions on a file or directory, for the owner, group, and others.

**What does ps do?**
Displays information about currently running processes (PID, status, resource usage, etc.).

**What does grep do?**
Searches text (files or piped input) for lines matching a given pattern or regular expression.

**What does sudo do?**
Runs a command with elevated (superuser/root) privileges.

**How do you find a file from the terminal?**
```bash
find /path -name "filename"
# or, if the locate database is available:
locate filename
```

**How do you inspect running processes?**
```bash
ps aux     # snapshot of all running processes
top        # live, interactive view (or htop for a nicer UI)
```

**Commands worth knowing:** `ls` (list files), `cd` (change directory), `pwd` (print working directory), `mkdir` (create directory), `rm` (remove files), `cp` (copy), `mv` (move/rename), `cat` (print file contents), `head`/`tail` (show first/last lines of a file), `grep` (search text), `chmod` (change permissions), `ps` (list processes), `kill` (terminate a process by PID).

---

## 🟡 Postman + REST APIs

**What is an API?**
A set of rules and protocols that lets different software applications communicate and exchange data with each other.

**What is a REST API?**
An API that follows REST architectural principles — stateless requests, resources identified by URLs, and standard HTTP methods used to act on those resources.

**GET vs POST vs PUT vs PATCH vs DELETE.**
Same as in the Go section: GET reads, POST creates, PUT fully replaces, PATCH partially updates, DELETE removes.

**What are HTTP status codes?**
Codes indicating the outcome of a request — 2xx success, 3xx redirect, 4xx client error, 5xx server error.

**What are request headers?**
Metadata sent along with an HTTP request (e.g., `Content-Type`, `Authorization`) that tells the server how to interpret or handle the request.

**What is a request body?**
The data payload sent with a request — commonly JSON — used with methods like POST/PUT/PATCH to send data to the server.

**What is JSON?**
JavaScript Object Notation — a lightweight, human-readable, text-based format for structured data, widely used for API request/response bodies.

**How do you test an API using Postman?**
Create a request by setting the method, URL, headers, and body as needed, send it, and inspect the response status/headers/body. You can also write test assertions in Postman's scripting panel and group related requests into collections for repeatable/automated testing.

### Follow-ups

**Authentication vs authorization.** Authentication verifies *who* a user is (identity — e.g., logging in). Authorization determines *what* an authenticated user is allowed to do (permissions).

**What is an API endpoint?** A specific URL where a particular API operation can be accessed (e.g., `/users/123`).

**What is an HTTP method?** The verb in an HTTP request (GET, POST, PUT, PATCH, DELETE, etc.) indicating what action to perform on the resource.

**What happens when an API returns 404?** It means the server couldn't find any resource matching the requested URL.

**400 vs 401 vs 403.** 400 = Bad Request (malformed syntax or invalid input data). 401 = Unauthorized (authentication is missing or failed). 403 = Forbidden (the client is authenticated but not permitted to access this resource).

**500 vs 503.** 500 = Internal Server Error (a generic, unhandled failure on the server side). 503 = Service Unavailable (the server is temporarily overloaded, down for maintenance, or otherwise unable to handle the request).

**How would you test an API for invalid input?** Send requests with missing required fields, wrong data types, out-of-range values, and malformed JSON, then confirm the API responds with clear, appropriate error codes/messages rather than crashing or returning a misleading success.

---

## 🟡 VS Code

**What extensions/tools do you commonly use in VS Code?**
A good answer names what you actually use — for example: the Python extension for linting/IntelliSense, Prettier/ESLint for formatting and code quality, GitLens for richer Git history/blame info, and a REST client extension for testing APIs directly in the editor. Tailor this to your own setup and be ready to explain *why* you use each one.

**How do you debug Python/JavaScript code in VS Code?**
Set breakpoints by clicking next to the line numbers, open the Run & Debug panel, choose or create a launch configuration for your language/runtime, then start debugging to step through code line by line and inspect variables in the debug console.

**How do you manage and run a project from VS Code?**
Use the integrated terminal to run build/start commands, the Explorer panel to navigate and manage files, `.vscode/settings.json` and `launch.json` for project-specific configuration, and the built-in Source Control panel for staging, committing, and pushing changes with Git.

---

## 🟡 Certifications

**What did you learn from the IBM SkillsBuild — Getting Started with Data certification?**
This course typically covers foundational data literacy: what data is and why it matters, the data lifecycle (collection, storage, processing, analysis), basic concepts of data analysis and visualization, and an introduction to how data-driven decisions are made in business. When answering, personalize this with one specific concept you found most useful and, ideally, tie it to something concrete you did in a project (e.g., "the module on data visualization principles directly shaped how I presented results in my NLP project").

**What topics were covered in the certification?**
List the actual modules/topics from your certificate if you remember them (data types, data cleaning, basic statistics, visualization tools, etc.) — interviewers respond well to specifics rather than generic descriptions.

**What was the most useful concept you learned?**
Pick one genuinely useful idea and explain briefly *why* it mattered to you, ideally with an example of applying it.

**What did you learn from Introduction to Software Engineering?**
This course typically covers the software development lifecycle (SDLC), Agile/Scrum basics, requirements gathering, software design principles, and testing/quality fundamentals. Personalize with a specific practice (e.g., version control discipline, writing clearer requirements, or basic testing habits) you've since applied.

**How have you applied something from these certifications in your projects?**
Give one concrete example — e.g., "I applied the data-cleaning process from the data certification when preparing text data for my NLP project" or "I used Agile-style incremental development, breaking my CacheSphere project into small testable pieces." Concrete, personal examples always land better than restating course content.

---

*Tip: for the certification questions and any "tell me about your project" follow-ups, practice saying your answers out loud once — they're the questions most likely to trip you up if you've only read them silently.*
