# Interview Prep — Complete Question Bank with Answers

**Coverage:** DBMS → OOP → Machine Learning → NLP → Federated Learning → Data Science → OS → Computer Networks → Cloud Computing

---

## 1. DBMS (Database Management System)

### Core Questions

**1. What is a DBMS? How is it different from an RDBMS?**
A DBMS is software that stores, retrieves, and manages data (includes file-based, hierarchical, or navigational systems). An RDBMS is a DBMS that specifically stores data in structured tables (rows/columns) with relationships enforced through keys and constraints (e.g., MySQL, PostgreSQL). All RDBMS are DBMS, but not all DBMS are relational.

**2. What is a primary key?**
A column (or set of columns) that uniquely identifies each row in a table. It cannot contain NULLs and must be unique.

**3. Primary key vs candidate key vs super key.**
- **Super key:** any combination of columns that uniquely identifies a row (may have redundant columns).
- **Candidate key:** a minimal super key (no redundant columns).
- **Primary key:** the candidate key chosen to uniquely identify rows.

**4. What is a foreign key?**
A column in one table that references the primary key of another table, enforcing referential integrity between related tables.

**5. What are different types of keys?**
Primary key, candidate key, super key, foreign key, composite key, unique key, alternate key.

**6. What is normalization?**
Organizing data to reduce redundancy and avoid insert/update/delete anomalies by decomposing tables into smaller, related tables.

**7. Explain 1NF, 2NF, 3NF, BCNF with examples.**
- **1NF:** every column holds atomic values, no repeating groups (e.g., split a multi-valued "phone numbers" column into separate rows).
- **2NF:** 1NF + no partial dependency (non-key columns depend on the *whole* composite primary key).
- **3NF:** 2NF + no transitive dependency (non-key columns depend only on the primary key, not on other non-key columns).
- **BCNF:** stricter 3NF — every determinant must be a candidate key.

**8. Why do we normalize databases?**
To eliminate redundancy, prevent anomalies (insert/update/delete), and keep data consistent.

**9. What is denormalization and when would you use it?**
Deliberately introducing redundancy into a normalized schema to improve read performance — common in reporting/data-warehouse systems where read speed matters more than write efficiency.

**10. What is an entity integrity constraint?**
It requires that a table's primary key can never be NULL — every row must be uniquely identifiable.

**11. What is referential integrity?**
A constraint ensuring a foreign key value must match an existing primary key value in the referenced table (or be NULL).

**12. WHERE vs HAVING.**
`WHERE` filters rows *before* grouping/aggregation; `HAVING` filters groups *after* a `GROUP BY`/aggregation.

**13. GROUP BY vs ORDER BY.**
`GROUP BY` aggregates rows into groups based on column values; `ORDER BY` sorts the final result set.

**14. DELETE vs TRUNCATE vs DROP.**
`DELETE` removes rows (supports `WHERE`, is logged, can be rolled back); `TRUNCATE` removes all rows quickly (minimal logging, resets identity, no `WHERE`); `DROP` removes the entire table structure and data.

**15. UNION vs UNION ALL.**
`UNION` merges result sets and removes duplicates; `UNION ALL` merges and keeps duplicates (faster — no dedup step).

**16. What are joins?**
Operations that combine rows from two or more tables based on a related column.

**17. Explain INNER, LEFT, RIGHT, FULL, SELF, CROSS JOIN.**
- **INNER JOIN:** only matching rows in both tables.
- **LEFT JOIN:** all rows from the left table + matches from the right (NULL if no match).
- **RIGHT JOIN:** all rows from the right table + matches from the left.
- **FULL JOIN:** all rows from both tables, matched where possible.
- **SELF JOIN:** a table joined with itself.
- **CROSS JOIN:** Cartesian product of both tables.

**18. What happens if you join tables without a proper join condition?**
It becomes a Cartesian product — every row of table A pairs with every row of table B, producing a huge, mostly meaningless result set.

**19. What is a subquery?**
A query nested inside another query, used to return data the outer query filters or computes on.

**20. Subquery vs JOIN — when would you prefer each?**
JOINs are usually more efficient for combining columns from multiple tables and easier for the optimizer. Subqueries are clearer when you need an aggregated/filtered scalar value (e.g., inside a `WHERE` clause) or when the logic reads better step-by-step.

### Interview-Level SQL

**21. Find the second-highest salary.**
```sql
SELECT MAX(salary) FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);
```

**22. Find the nth-highest salary.**
```sql
-- Using a correlated subquery
SELECT DISTINCT salary FROM employees e1
WHERE (N-1) = (
  SELECT COUNT(DISTINCT salary) FROM employees e2
  WHERE e2.salary > e1.salary
);

-- Using window functions (cleaner)
SELECT salary FROM (
  SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
  FROM employees
) t WHERE rnk = N;
```

**23. Find employees whose salary is greater than their department's average.**
```sql
SELECT e.* FROM employees e
WHERE e.salary > (
  SELECT AVG(salary) FROM employees e2
  WHERE e2.dept_id = e.dept_id
);
```

**24. Find duplicate records.**
```sql
SELECT name, email, COUNT(*)
FROM users
GROUP BY name, email
HAVING COUNT(*) > 1;
```

**25. Find departments having more than 5 employees.**
```sql
SELECT dept_id, COUNT(*) AS cnt
FROM employees
GROUP BY dept_id
HAVING COUNT(*) > 5;
```

**26. Find the highest-paid employee in every department.**
```sql
SELECT * FROM employees e
WHERE salary = (
  SELECT MAX(salary) FROM employees e2
  WHERE e2.dept_id = e.dept_id
);
```

**27. Find customers who have never placed an order.**
```sql
SELECT c.* FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
WHERE o.order_id IS NULL;
```

**28. Find the top 3 products by sales in each category.**
```sql
SELECT * FROM (
  SELECT p.*, RANK() OVER (PARTITION BY category ORDER BY sales DESC) AS rnk
  FROM products p
) t WHERE rnk <= 3;
```

**29. Find the running total of sales.**
```sql
SELECT order_date, sales,
  SUM(sales) OVER (ORDER BY order_date) AS running_total
FROM orders;
```

**30. Find the difference between each employee's salary and department average.**
```sql
SELECT e.*, e.salary - avg_dept.avg_sal AS diff
FROM employees e
JOIN (
  SELECT dept_id, AVG(salary) AS avg_sal
  FROM employees GROUP BY dept_id
) avg_dept ON e.dept_id = avg_dept.dept_id;
```

### Advanced DBMS

**31. What is an index?**
A data structure (commonly a B-tree) storing a sorted reference to table rows, speeding up lookups on indexed columns at the cost of extra storage and slower writes.

**32. Why does an index make searching faster?**
Instead of a full table scan, the database navigates a tree structure in roughly O(log n) to jump directly to matching rows.

**33. When can an index actually hurt performance?**
On write-heavy tables — every INSERT/UPDATE/DELETE must also update the index, and too many indexes increase storage and write latency. Indexes also give little benefit on low-cardinality columns.

**34. Clustered vs non-clustered index.**
A clustered index determines the physical storage order of table data (only one per table); a non-clustered index is a separate structure pointing back to the actual rows (a table can have many).

**35. What is a transaction?**
A sequence of one or more SQL operations executed as a single logical unit of work — either all succeed (commit) or all fail (rollback).

**36. Explain ACID properties.**
- **Atomicity:** transaction is all-or-nothing.
- **Consistency:** the DB moves from one valid state to another.
- **Isolation:** concurrent transactions don't interfere with each other.
- **Durability:** once committed, changes persist even after a crash.

**37. What is concurrency control?**
Techniques (locking, timestamping, MVCC) that manage simultaneous transactions to prevent conflicts and maintain consistency.

**38. What is a deadlock in databases?**
A situation where two or more transactions each hold a lock the other needs, so both wait forever.

**39. What are isolation levels?**
Read Uncommitted, Read Committed, Repeatable Read, Serializable — each trades consistency guarantees against concurrency/performance, controlling dirty reads, non-repeatable reads, and phantom reads.

**40. What is a view?**
A virtual table defined by a stored SQL query; it doesn't store data itself but presents data from underlying tables.

**41. What is a stored procedure?**
A precompiled, reusable block of SQL stored in the database, executable with a single call and able to accept parameters.

**42. What is a trigger?**
A stored procedure that automatically executes in response to an event (INSERT, UPDATE, DELETE) on a table.

### Firestore-Specific

**43. SQL database vs NoSQL database.**
SQL databases are relational, use structured schemas, and enforce ACID. NoSQL databases (document, key-value, column, graph) are schema-flexible, scale horizontally more easily, and often favor availability over strict consistency.

**44. Why would you choose Firestore over MySQL?**
When you need flexible/evolving schemas, automatic horizontal scaling, real-time data sync (listeners), and easy integration with mobile/web apps without managing server infrastructure.

**45. Document vs collection in Firestore.**
A document is a single record (key-value fields, like a JSON object); a collection is a group of documents, similar to a table.

**46. How is Firestore different from a relational database?**
Firestore stores hierarchical, schema-less documents grouped into collections (with subcollections), has no JOINs, and is optimized for horizontal scalability and real-time updates rather than complex relational queries.

**47. What are the limitations of NoSQL?**
Weaker support for complex joins/multi-document transactions, eventual consistency in some systems, less mature ad-hoc analytical query tooling, and potential data duplication.

**48. When should you not use NoSQL?**
When your data is highly relational, requires complex multi-table joins, strict ACID transactions across entities, or a fixed, well-understood schema.

---

## 2. OOP (Object-Oriented Programming)

### Fundamental

**1. What is OOP?**
A programming paradigm that organizes code around objects — bundles of data (attributes) and behavior (methods) — rather than functions and logic alone.

**2. Why do we use OOP?**
It promotes code reuse, modularity, and easier maintenance, and models real-world entities naturally through encapsulation, inheritance, and polymorphism.

**3. What are the four pillars of OOP?**
Encapsulation, Abstraction, Inheritance, Polymorphism.

**4. Explain: Encapsulation / Abstraction / Inheritance / Polymorphism.**
- **Encapsulation:** bundling data and methods together and restricting direct access to internal state.
- **Abstraction:** hiding implementation details, exposing only essential features.
- **Inheritance:** a class (child) acquiring properties/behavior from another class (parent).
- **Polymorphism:** the same interface/method behaving differently depending on the object/context.

**5. Class vs object.**
A class is a blueprint/template defining attributes and methods; an object is a concrete instance of that class with actual values.

**6. Instance variable vs class variable.**
Instance variables are unique to each object (set in `__init__` via `self`); class variables are shared across all instances of the class.

**7. Instance method vs class method vs static method.**
- **Instance method:** takes `self`, operates on instance data.
- **Class method:** takes `cls`, operates on class-level data, decorated `@classmethod`.
- **Static method:** takes neither, behaves like a plain function namespaced in the class, decorated `@staticmethod`.

**8. What is a constructor?**
A special method automatically called when an object is created, used to initialize its attributes (`__init__` in Python).

**9. What is `self` in Python?**
A reference to the current instance of the class, used to access its attributes/methods; must be the first parameter of instance methods.

**10. What does `__init__()` do?**
It's the constructor method, automatically invoked when a new object is instantiated, used to set up initial state.

### Inheritance

**11. What is inheritance?**
A mechanism where a child class derives attributes and methods from a parent class, enabling code reuse.

**12. Types of inheritance.**
Single, multiple, multilevel, hierarchical, hybrid.

**13. Single vs multiple inheritance.**
Single: one child inherits from one parent. Multiple: one child inherits from more than one parent class.

**14. What is multilevel inheritance?**
A chain of inheritance — e.g., class C inherits from B, which inherits from A.

**15. What is hierarchical inheritance?**
Multiple child classes inherit from a single parent class.

**16. What is the diamond problem?**
An ambiguity in multiple inheritance when two parent classes share a common ancestor, making it unclear which parent's method/attribute the child should inherit.

**17. How does Python solve multiple inheritance?**
Through the C3 linearization algorithm, which produces a consistent Method Resolution Order (MRO).

**18. What is MRO?**
Method Resolution Order — the order Python uses to look up methods/attributes across a class hierarchy; viewable via `ClassName.__mro__`.

**19. Explain `super()`.**
A built-in function giving access to a parent class's methods, commonly used to call the parent's `__init__` or extend an overridden method without hardcoding the parent class name.

### Polymorphism

**20. What is polymorphism?**
The ability of different classes to be treated through a common interface, each providing its own implementation of a shared method.

**21. Method overriding vs method overloading.**
**Overriding:** a child class redefines a method already defined in its parent (same signature). **Overloading:** multiple methods with the same name but different parameters (not natively supported in Python).

**22. Does Python support method overloading?**
Not natively — defining multiple methods with the same name simply overwrites the previous one. Similar behavior is achieved via default arguments, `*args`/`**kwargs`, or `functools.singledispatch`.

**23. How can you simulate method overloading in Python?**
Using default parameter values, variable-length arguments (`*args`, `**kwargs`), type-checking inside a single method, or the `singledispatch` decorator.

**24. What is duck typing?**
A dynamic typing style where an object's suitability is determined by the presence of certain methods/attributes rather than its explicit type — "if it walks like a duck and quacks like a duck, it's a duck."

### Encapsulation / Abstraction

**25. Public vs protected vs private members in Python.**
**Public:** no underscore prefix, accessible anywhere. **Protected:** single underscore `_var`, a convention meaning "internal use" (not enforced). **Private:** double underscore `__var`, triggers name mangling to discourage external access.

**26. What does `__variable` actually do?**
It triggers name mangling — Python renames it internally to `_ClassName__variable`, making accidental external access harder (but not impossible).

**27. What is name mangling?**
The process by which Python renames a double-underscore-prefixed attribute to include the class name, avoiding naming conflicts in subclasses and discouraging outside access.

**28. What is abstraction?**
Hiding complex implementation details and exposing only the necessary interface to the user.

**29. How do you implement abstraction in Python?**
Using the `abc` module — define an abstract base class inheriting from `ABC` and mark methods with `@abstractmethod`; subclasses must implement those methods.

**30. Abstract class vs interface.**
An abstract class can mix implemented and abstract methods (partial implementation); an interface (a concept Python simulates rather than has natively) defines only method signatures with no implementation, purely as a contract.

### Code Questions

**31. Create a `BankAccount` class.**
```python
class BankAccount:
    def __init__(self, owner, balance=0):
        self.owner = owner
        self.__balance = balance  # encapsulated

    def get_balance(self):
        return self.__balance
```

**32. Implement deposit/withdraw with encapsulation.**
```python
class BankAccount:
    def __init__(self, balance=0):
        self.__balance = balance

    def deposit(self, amount):
        if amount > 0:
            self.__balance += amount

    def withdraw(self, amount):
        if 0 < amount <= self.__balance:
            self.__balance -= amount
        else:
            raise ValueError("Insufficient funds")

    def get_balance(self):
        return self.__balance
```

**33. Create a parent `Vehicle` class and child `Car`/`Bike`.**
```python
class Vehicle:
    def __init__(self, brand):
        self.brand = brand

    def info(self):
        return f"Vehicle brand: {self.brand}"

class Car(Vehicle):
    def __init__(self, brand, doors):
        super().__init__(brand)
        self.doors = doors

class Bike(Vehicle):
    def __init__(self, brand, has_gear):
        super().__init__(brand)
        self.has_gear = has_gear
```

**34. Demonstrate method overriding.**
```python
class Animal:
    def sound(self):
        return "Some sound"

class Dog(Animal):
    def sound(self):
        return "Bark"

print(Dog().sound())  # Bark
```

**35. Demonstrate multiple inheritance.**
```python
class Flyer:
    def move(self):
        return "Flying"

class Swimmer:
    def move(self):
        return "Swimming"

class Duck(Flyer, Swimmer):
    pass

print(Duck().move())  # "Flying" (MRO checks Flyer first)
```

**36. Write an abstract `Shape` class.**
```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass
```

**37. Implement polymorphism using `area()`.**
```python
class Circle(Shape):
    def __init__(self, r):
        self.r = r
    def area(self):
        return 3.14 * self.r ** 2

class Square(Shape):
    def __init__(self, side):
        self.side = side
    def area(self):
        return self.side ** 2

for shape in [Circle(2), Square(3)]:
    print(shape.area())
```

**38. Explain the output of a code snippet involving inheritance + `super()`.**
```python
class A:
    def __init__(self):
        print("A init")

class B(A):
    def __init__(self):
        super().__init__()
        print("B init")

B()
# Output:
# A init
# B init
```
`super().__init__()` calls the parent constructor first, so "A init" prints before "B init".

**39. Explain the output involving class vs instance variables.**
```python
class Counter:
    count = 0  # class variable
    def __init__(self):
        Counter.count += 1

a = Counter()
b = Counter()
print(Counter.count)  # 2
```
`count` is shared across all instances, so each new object increments the same class-level value.

**40. Implement a class using `@classmethod` and `@staticmethod`.**
```python
class MathUtils:
    factor = 2

    @classmethod
    def scale(cls, x):
        return x * cls.factor

    @staticmethod
    def add(a, b):
        return a + b
```

---

## 3. Machine Learning

### Fundamentals

**1. What is Machine Learning?**
A field of AI where systems learn patterns from data to make predictions or decisions without being explicitly programmed with fixed rules.

**2. Supervised vs unsupervised vs reinforcement learning.**
**Supervised:** learns from labeled data (input-output pairs). **Unsupervised:** finds patterns/structure in unlabeled data (clustering, dimensionality reduction). **Reinforcement:** an agent learns by interacting with an environment and receiving rewards/penalties.

**3. Classification vs regression.**
Classification predicts discrete categories/labels; regression predicts continuous numeric values.

**4. Training vs validation vs test data.**
Training data fits the model; validation data tunes hyperparameters and checks for overfitting during development; test data gives an unbiased final evaluation on unseen data.

**5. Why do we split data?**
To evaluate how well a model generalizes to unseen data, rather than just memorizing the training set.

**6. What is overfitting?**
When a model learns training data too well — including noise — giving high training accuracy but poor generalization.

**7. What is underfitting?**
When a model is too simple to capture the underlying pattern, performing poorly on both training and test data.

**8. How do you prevent overfitting?**
More training data, regularization (L1/L2), dropout, cross-validation, early stopping, simplifying the model, and data augmentation.

**9. What is bias?**
Error from overly simplistic assumptions in the model, causing it to miss relevant patterns (underfitting).

**10. What is variance?**
Error from the model being too sensitive to small fluctuations in training data, causing overfitting.

**11. Explain the bias-variance tradeoff.**
As model complexity increases, bias decreases but variance increases (and vice versa). The goal is the sweet spot minimizing total error on unseen data.

### Evaluation

**12. Confusion matrix.**
A table showing True Positives, True Negatives, False Positives, and False Negatives — the basis for computing accuracy, precision, recall, etc.

**13. Accuracy.**
(TP + TN) / Total — the fraction of correct predictions.

**14. Precision.**
TP / (TP + FP) — of all predicted positives, how many were actually positive.

**15. Recall.**
TP / (TP + FN) — of all actual positives, how many were correctly identified.

**16. F1-score.**
The harmonic mean of precision and recall: 2 × (Precision × Recall) / (Precision + Recall) — useful when you need a balance between the two.

**17. Precision vs recall — when is each more important?**
High precision matters when false positives are costly (e.g., spam filters flagging real email). High recall matters when false negatives are costly (e.g., cancer detection, fraud detection).

**18. ROC curve.**
A plot of True Positive Rate vs False Positive Rate at various classification thresholds, showing the tradeoff between sensitivity and specificity.

**19. AUC.**
Area Under the ROC Curve — a single number (0 to 1) summarizing a classifier's ability to distinguish classes; 0.5 = random guessing, 1.0 = perfect.

**20. MAE vs MSE vs RMSE.**
**MAE:** average absolute error (robust to outliers). **MSE:** average squared error (penalizes large errors more). **RMSE:** square root of MSE (same units as the target, still penalizes large errors).

**21. What is R²?**
The coefficient of determination — the proportion of variance in the target explained by the model; higher (closer to 1) is better fit.

### Scenario Questions

**22. Your model has 99% accuracy but performs badly. Why?**
Likely class imbalance — if 99% of data belongs to one class, always predicting that class gives 99% accuracy while completely failing on the minority class.

**23. Dataset has 95% negative and 5% positive samples. What problem exists?**
Class imbalance — the model can hit high accuracy by mostly ignoring the minority class, making accuracy a misleading metric here.

**24. How would you handle class imbalance?**
Resampling (oversample minority via SMOTE, undersample majority), class weighting in the loss function, better metrics (precision/recall/F1/AUC instead of accuracy), or collecting more minority-class data.

**25. Your training accuracy is 99%, validation accuracy is 70%. What happened?**
The model is overfitting — it memorized the training data but doesn't generalize. Fix with regularization, more data, a simpler model, or dropout.

**26. Both training and validation accuracy are poor. What would you do?**
This indicates underfitting — increase model complexity, add better features, train longer, or reduce regularization.

**27. Your model performs well on training data but poorly on unseen data. Explain.**
Classic overfitting — the model captured noise/specific patterns in the training set that don't generalize. Address with regularization, cross-validation, or more diverse training data.

### Algorithms

**28. Explain Linear Regression.**
A model that fits a line (y = mx + b, generalized to multiple features) to predict a continuous output by minimizing the sum of squared errors.

**29. Explain Logistic Regression.**
A classification algorithm that models the probability of a binary outcome using the sigmoid function applied to a linear combination of features.

**30. Decision Tree.**
A tree-structured model that splits data based on feature values (using Gini impurity or information gain) to reach a decision/prediction at the leaves.

**31. Random Forest.**
An ensemble of many decision trees trained on random subsets of data/features, with predictions aggregated (voting/averaging) to reduce overfitting and improve accuracy.

**32. KNN.**
K-Nearest Neighbors — classifies/predicts a new point based on the majority class (or average value) of its K closest points in the training data.

**33. K-Means.**
An unsupervised clustering algorithm that partitions data into K clusters by iteratively assigning points to the nearest centroid and updating centroids.

**34. Naive Bayes.**
A probabilistic classifier based on Bayes' theorem, assuming features are conditionally independent given the class — fast and effective for text classification.

**35. SVM.**
Support Vector Machine — finds the optimal hyperplane maximizing the margin between classes; handles non-linear boundaries via the kernel trick.

**36. Gradient Descent.**
An optimization algorithm that iteratively adjusts model parameters in the direction of steepest descent of the loss function to minimize error.

**37. What is a learning rate?**
A hyperparameter controlling the step size taken during each gradient descent update.

**38. What happens if learning rate is too high?**
The model may overshoot the minimum, causing loss to oscillate or diverge instead of converging.

**39. What happens if it is too low?**
Training becomes very slow, and the model may get stuck or take excessively long to converge.

### Preprocessing

**40. Why do we normalize/standardize data?**
To bring features to a similar scale so no single feature dominates due to magnitude, and to help gradient-based algorithms converge faster.

**41. Normalization vs standardization.**
Normalization rescales values to a fixed range (e.g., 0–1). Standardization rescales to zero mean and unit variance (z-score).

**42. How do you handle missing values?**
Remove rows/columns with excessive missing data, impute with mean/median/mode, use forward/backward fill for time series, or use model-based imputation.

**43. How do you handle categorical variables?**
Encode them numerically — one-hot encoding for nominal categories, label/ordinal encoding for ordered categories, or embeddings for high-cardinality categories.

**44. One-hot encoding vs label encoding.**
One-hot encoding creates a binary column per category (no implied order); label encoding assigns each category an integer (implies an order, which can mislead models on nominal data).

**45. What is feature engineering?**
Creating, transforming, or combining raw data into features that better represent the underlying problem to the model.

**46. What is feature selection?**
Choosing the most relevant subset of features to reduce dimensionality, improve performance, and reduce overfitting/training time.

**47. What is data leakage?**
When information from outside the training data (often from the target or future data) inadvertently influences the model, producing unrealistically good performance that doesn't hold in production.

---

## 4. NLP (Natural Language Processing)

**1. What is NLP?**
A field of AI focused on enabling computers to understand, interpret, and generate human language.

**2. What is tokenization?**
Splitting text into smaller units (tokens) — words, subwords, or characters — for processing.

**3. Word tokenization vs character tokenization.**
Word tokenization splits text into words; character tokenization splits into individual characters. Word-level captures meaning more directly but has a larger vocabulary; character-level has a small vocabulary but longer sequences.

**4. What is vocabulary?**
The set of all unique tokens (words/subwords) the model recognizes, each typically mapped to an integer index.

**5. What is an embedding?**
A dense, low-dimensional vector representation of a word/token that captures semantic meaning, learned so similar words have similar vectors.

**6. One-hot encoding vs word embeddings.**
One-hot vectors are sparse, high-dimensional, and carry no semantic relationship between words. Embeddings are dense, lower-dimensional, and capture semantic similarity (e.g., "king" and "queen" sit close in vector space).

**7. What is an RNN?**
Recurrent Neural Network — a network with loops that maintain a hidden state across time steps, allowing it to process sequential data.

**8. Why were RNNs introduced?**
To handle sequential/temporal data (text, time series) where order matters, which feedforward networks can't model since they have no memory of previous inputs.

**9. What is the vanishing-gradient problem?**
During backpropagation through many time steps, gradients can shrink exponentially, making it hard for RNNs to learn long-range dependencies.

**10. What is an LSTM?**
Long Short-Term Memory — an RNN variant with gating mechanisms (input, forget, output gates) that control what information to keep or discard, retaining long-range dependencies better than vanilla RNNs.

**11. LSTM vs RNN.**
LSTMs use gates and a cell state to selectively remember/forget information over long sequences, mitigating the vanishing gradient problem plain RNNs suffer from.

**12. What is sequence prediction?**
Predicting the next element(s) in a sequence based on previous elements — e.g., predicting the next word given prior words.

**13. How does next-word prediction work?**
The model takes a sequence of preceding words (as embeddings), processes them through a sequential model (RNN/LSTM/Transformer), and outputs a probability distribution over the vocabulary for the most likely next word.

**14. Given "I love machine", how would you predict the next word?**
Tokenize and convert the sequence to embeddings, pad/format to the model's expected input length, pass it through the trained model, and take the word with the highest probability from the softmax output over the vocabulary (likely "learning").

**15. What is padding?**
Adding filler tokens (usually zeros) so all sequences in a batch have the same length, since neural networks require fixed-size inputs.

**16. Why do sequences need the same length?**
Because operations are vectorized/batched — matrix operations require uniform input dimensions across a batch.

**17. What is an `<UNK>` token?**
A placeholder token representing words not present in the model's vocabulary (out-of-vocabulary words).

**18. What loss function would you use for next-word prediction?**
Categorical cross-entropy (or sparse categorical cross-entropy), since it's a multi-class classification problem over the vocabulary.

**19. Why is softmax used in the output layer?**
It converts raw output scores (logits) into a probability distribution over all vocabulary words summing to 1, letting you pick the most probable next word.

**20. How would you evaluate a next-word prediction model?**
Using perplexity (lower is better), top-1/top-k accuracy, and qualitative review of generated text.

---

## 5. Federated Learning

**1. What is Federated Learning?**
A decentralized machine learning approach where a model is trained across multiple devices/clients holding local data, without the raw data ever leaving the device — only model updates are shared with a central server.

**2. Why use Federated Learning instead of centralized ML?**
It preserves user privacy (raw data stays on-device), avoids transferring large/sensitive datasets to a server, and enables learning from data that's legally/ethically sensitive to centralize.

**3. Explain the architecture of Federated Learning.**
A central server maintains a global model. It sends the current model to selected clients; each client trains locally on its own data; clients send back only model updates (weights/gradients); the server aggregates these (e.g., via FedAvg) to improve the global model, and the cycle repeats.

**4. What is a client?**
A device or node (e.g., a phone) that holds local data and performs local training on a copy of the global model.

**5. What is a server?**
The central coordinator that distributes the global model to clients, collects their updates, and aggregates them into an improved global model.

**6. What happens during one round of training?**
The server selects a subset of clients, sends them the current global model, each client trains it locally for a few epochs on its own data, clients send updated weights back, and the server aggregates them into the next global model version.

**7. What is FedAvg?**
Federated Averaging — the standard aggregation algorithm that combines client updates via a weighted average (typically weighted by each client's number of local samples).

**8. Why don't clients send raw data?**
To preserve privacy and reduce bandwidth/storage costs — sending model updates instead of raw data keeps sensitive user data on-device.

**9. What exactly does a client send to the server?**
The updated model parameters (weights) or gradients from local training, not the underlying data.

**10. What are model updates?**
The changes in model weights (or the new weights themselves) produced after a client trains the global model on its local data.

**11. Why is encryption required?**
Model updates can still leak information about underlying data (e.g., via gradient inversion attacks), so encryption (or secure aggregation/differential privacy) protects updates in transit and during aggregation.

**12. What are the limitations of Federated Learning?**
Communication overhead, slower convergence than centralized training, challenges with non-IID/heterogeneous client data, unreliable client availability, and the need for careful privacy/security mechanisms.

**13. What happens if clients have different amounts of data?**
Aggregation must account for this (e.g., FedAvg weights each client's update by its sample count); otherwise clients with little data could disproportionately sway the global model.

**14. What happens if client data distributions are different?**
This is the non-IID problem — it can slow convergence, bias the global model, and cause client updates to conflict during aggregation.

**15. What is non-IID data?**
Data that isn't independently and identically distributed across clients — e.g., each user's phone has a different vocabulary/writing style, unlike a shuffled, uniformly-sampled centralized dataset.

**16. Why is communication a bottleneck?**
Because model weights (potentially millions of parameters) must be transmitted between server and many clients every round, over often slow/unreliable networks (mobile data, limited bandwidth).

**17. What happens if a client has low battery/CPU/network?**
The client may fail to complete local training in time, drop out of the round, or degrade user experience if forced — so systems typically check device conditions before selecting a client for training.

**18. Why did you check CPU, RAM, battery and network availability?**
To ensure training only runs when it won't harm the user's device experience or fail mid-round — e.g., only training when the device is idle, charging, and on Wi-Fi.

**19. Why do all clients need a common vocabulary?**
Because the model's input/output layers are fixed to a specific vocabulary size and word-to-index mapping; different vocabularies across clients would make model updates incompatible and impossible to aggregate meaningfully.

**20. Explain your entire project architecture.**
*(This is personal to your project — walk through: on-device text data flow → local model (e.g., an LSTM for next-word prediction) → the client-side training loop → device conditions checked before training (battery/CPU/network) → what gets sent to the server (weight updates) → the aggregation method (FedAvg) → how the global model gets redistributed → any privacy measures used (encryption/differential privacy). Rehearse this out loud — it's the single most likely deep-dive question.)*

---

## 6. Data Science

### Concepts

**1. What is Data Science?**
An interdisciplinary field using statistics, programming, and domain knowledge to extract insights and build predictive models from data.

**2. Data Science vs Data Analytics vs ML.**
Data Analytics analyzes historical data to find trends and support decisions. Data Science is broader — analytics plus building predictive models, often using ML. Machine Learning is a subset of tools/techniques used within data science to build predictive models.

**3. What is EDA?**
Exploratory Data Analysis — analyzing datasets to summarize their main characteristics, often via visualizations, before formal modeling.

**4. What steps do you follow in EDA?**
Understand data structure/types, check for missing values and duplicates, examine distributions and outliers, study correlations between variables, and visualize patterns.

**5. How do you detect outliers?**
Box plots, z-scores (|z| > 3), the IQR method (values beyond 1.5×IQR from Q1/Q3), or scatter plots.

**6. How do you handle outliers?**
Remove them if they're errors, cap/clip them (winsorization), transform the data (log transform), or keep them if they're genuine and important to the analysis.

**7. Mean vs median — when would you use each?**
Mean is the average, sensitive to outliers/skew. Median is the middle value, robust to outliers — preferred for skewed distributions (e.g., income data).

**8. What is standard deviation?**
A measure of how spread out data values are from the mean — the square root of variance.

**9. What is variance?**
The average of squared differences from the mean, measuring data spread.

**10. What is correlation?**
A statistical measure (ranging -1 to 1) indicating the strength and direction of a linear relationship between two variables.

**11. Correlation vs causation.**
Correlation means two variables move together; causation means one directly causes change in the other. Correlation does not imply causation — there could be a confounding variable or coincidence.

**12. What is covariance?**
A measure of how two variables vary together (direction of relationship), but unlike correlation, it isn't normalized, making magnitude hard to interpret directly.

**13. What is a distribution?**
A function describing how the values of a variable are spread across its range (e.g., normal, uniform, binomial).

**14. Normal distribution.**
A symmetric, bell-shaped distribution defined by its mean and standard deviation, where most values cluster near the mean.

**15. Skewness.**
A measure of asymmetry in a distribution — positive skew has a longer right tail, negative skew has a longer left tail.

**16. What is sampling?**
Selecting a subset of data (a sample) from a larger population to make inferences about that population without examining every member.

**17. Population vs sample.**
Population is the entire set of items/individuals of interest; a sample is a subset used for analysis.

**18. What is a confidence interval?**
A range of values, derived from sample data, likely to contain the true population parameter at a given confidence level (e.g., 95%).

**19. What is hypothesis testing?**
A statistical method for testing an assumption (null hypothesis) about a population using sample data, to decide whether to reject or fail to reject it.

**20. What is a p-value?**
The probability of observing results as extreme as (or more extreme than) the actual results, assuming the null hypothesis is true. A small p-value (typically < 0.05) suggests rejecting the null hypothesis.

### Pandas

**21. Series vs DataFrame.**
A Series is a one-dimensional labeled array; a DataFrame is a two-dimensional labeled table made up of multiple Series (columns).

**22. `loc` vs `iloc`.**
`loc` selects rows/columns by label (index name); `iloc` selects by integer position.

**23. `merge()` vs `concat()`.**
`merge()` combines DataFrames based on common columns/keys (like SQL joins); `concat()` stacks DataFrames along an axis (rows or columns) without key-based matching.

**24. `groupby()`.**
Splits data into groups based on column values, allowing aggregate operations (sum, mean, count) to be applied per group.

**25. `apply()`.**
Applies a function along an axis (rows or columns) of a DataFrame, or to each element of a Series.

**26. Handling missing values.**
`df.isnull()` to detect, `df.dropna()` to remove, `df.fillna()` to impute with a value, mean/median, or interpolation.

**27. Removing duplicates.**
`df.drop_duplicates()` removes exact duplicate rows (optionally based on a subset of columns).

**28. Filtering rows.**
Boolean indexing, e.g., `df[df['col'] > value]`, or `df.query('col > value')`.

**29. Sorting.**
`df.sort_values('col')` sorts by column values; `df.sort_index()` sorts by index.

**30. Pivot tables.**
`df.pivot_table()` reshapes data by aggregating values across specified rows and columns, similar to Excel pivot tables.

### Scenario

**31. You receive a dataset with 30% missing values. What do you do?**
Investigate whether missingness is random or systematic, then decide: drop columns/rows if missingness is very high and uninformative, impute with mean/median/mode or model-based methods, or use algorithms that handle missing values natively — and document how the choice affects downstream results.

**32. How would you identify suspicious data?**
Check for outliers, inconsistent formats, impossible values (e.g., negative age), duplicate records, and mismatches against expected ranges or business rules.

**33. How would you determine which features are useful?**
Correlation with the target, feature importance from tree-based models, statistical tests (chi-square, ANOVA), domain knowledge, and dimensionality reduction (PCA).

**34. How would you explain an analytical result to a non-technical person?**
Avoid jargon, use analogies and visualizations, lead with the business implication ("what this means for us") before the statistical details.

---

## 7. Operating Systems

### Must Know

**1. What is an Operating System?**
System software that manages hardware resources and provides services for application programs, acting as an intermediary between users/programs and hardware.

**2. Kernel vs OS.**
The kernel is the core component that directly manages hardware (memory, CPU, devices); the OS is the complete package including the kernel plus user interfaces, utilities, and system services.

**3. Process vs program.**
A program is a static set of instructions stored on disk; a process is a program in execution — an active entity with its own memory, state, and resources.

**4. Process vs thread.**
A process is an independent execution unit with its own memory space; a thread is a lightweight unit of execution within a process that shares memory with other threads in that process.

**5. Why are threads faster than processes?**
Threads share the same memory space, so creating/switching between them avoids the overhead of allocating separate memory and context — inter-thread communication is also cheaper than inter-process communication.

**6. What is context switching?**
Saving the state of a currently running process/thread and loading the state of another, so the CPU can switch between them.

**7. What is multitasking?**
The OS's ability to execute multiple tasks (processes) seemingly simultaneously by rapidly switching the CPU between them.

**8. What is multiprocessing?**
Using multiple CPUs/cores to execute multiple processes truly in parallel.

**9. What is multithreading?**
Running multiple threads within a single process concurrently, sharing the same memory space.

**10. What is a PCB?**
Process Control Block — a data structure the OS uses to store all information about a process (process ID, state, registers, memory pointers, scheduling info) needed to manage and resume it.

### Scheduling

**11. FCFS.**
First Come First Served — processes execute in arrival order; simple but can cause long waiting times (convoy effect).

**12. SJF.**
Shortest Job First — the process with the smallest execution time is scheduled next; minimizes average waiting time but can starve longer jobs.

**13. Round Robin.**
Each process gets a fixed time slice (quantum) in cyclic order; fair and good for time-sharing systems, but performance depends heavily on quantum size.

**14. Priority scheduling.**
Processes execute based on assigned priority; higher priority runs first, which can starve low-priority processes without aging.

**15. Preemptive vs non-preemptive scheduling.**
Preemptive: the OS can interrupt a running process to give the CPU to another (e.g., Round Robin). Non-preemptive: once started, a process runs to completion or until it voluntarily yields (e.g., FCFS).

**16. What is starvation?**
A process waits indefinitely because the scheduler continually favors other (e.g., higher-priority) processes.

**17. What is aging?**
A technique to prevent starvation by gradually increasing the priority of processes that have waited a long time.

### Memory

**18. Stack vs heap.**
Stack stores function call frames and local variables, follows LIFO order with automatic memory management; heap stores dynamically allocated memory that must be managed manually or via garbage collection, persisting until explicitly freed.

**19. What is virtual memory?**
An abstraction giving each process the illusion of a large, contiguous address space, backed by a combination of physical RAM and disk storage, managed via paging/segmentation.

**20. What is paging?**
A memory management scheme dividing physical memory into fixed-size frames and logical memory into same-sized pages, mapping pages to frames to avoid external fragmentation.

**21. What is segmentation?**
A memory management scheme dividing a program into variable-sized logical segments (code, data, stack), each mapped separately to physical memory.

**22. What is a page fault?**
An event that occurs when a program accesses a page not currently loaded in physical memory, requiring the OS to fetch it from disk.

**23. What is thrashing?**
A state where the system spends most of its time swapping pages in and out of memory rather than executing actual processes, causing severe performance degradation — usually due to over-committed memory.

### Synchronization

**24. What is a race condition?**
A situation where multiple threads/processes access shared data concurrently, and the outcome depends on unpredictable timing of execution, potentially causing incorrect results.

**25. What is a critical section?**
A part of code accessing shared resources, which must be executed by only one thread/process at a time to avoid race conditions.

**26. What is a mutex?**
A locking mechanism ensuring only one thread can access a critical section at a time (mutual exclusion).

**27. What is a semaphore?**
A signaling mechanism (an integer counter) controlling access to a resource by multiple processes/threads — can allow more than one accessor (counting semaphore) or just one (binary semaphore).

**28. Mutex vs semaphore.**
A mutex is a locking mechanism owned by the thread that locks it (binary, ownership-based); a semaphore is a signaling mechanism that can allow multiple threads and isn't tied to ownership by a single thread.

### Deadlocks

**29. What is deadlock?**
A state where two or more processes each wait for a resource held by another, so none can proceed.

**30. Four necessary conditions for deadlock.**
Mutual exclusion, hold and wait, no preemption, circular wait — all four must hold simultaneously for deadlock to occur.

**31. Deadlock prevention vs avoidance.**
Prevention eliminates one of the four necessary conditions structurally (e.g., disallowing hold-and-wait). Avoidance allows the conditions but carefully allocates resources at runtime (e.g., Banker's algorithm) to ensure the system never enters an unsafe state.

**32. What is Banker's algorithm?**
A deadlock-avoidance algorithm that simulates resource allocation before granting a request, only proceeding if the resulting state is "safe" (some order exists in which all processes can complete).

---

## 8. Computer Networks

### Core

**1. What is a computer network?**
A collection of interconnected devices that can communicate and share resources with each other.

**2. LAN vs WAN.**
LAN (Local Area Network) covers a small area like a building/campus; WAN (Wide Area Network) spans large geographic areas, connecting multiple LANs (e.g., the internet).

**3. OSI model.**
A 7-layer conceptual framework describing how data is transmitted over a network: Physical, Data Link, Network, Transport, Session, Presentation, Application.

**4. TCP/IP model.**
A 4-layer practical networking model: Network Interface (Link), Internet, Transport, Application — the model the actual internet is built on.

**5. Explain all 7 OSI layers.**
- **Physical:** transmits raw bits over a medium (cables, signals).
- **Data Link:** framing, MAC addressing, error detection on a local link.
- **Network:** logical addressing (IP) and routing between networks.
- **Transport:** end-to-end delivery, reliability (TCP/UDP).
- **Session:** manages sessions/connections between applications.
- **Presentation:** data translation, encryption, compression.
- **Application:** user-facing protocols (HTTP, FTP, SMTP).

**6. TCP vs UDP.**
TCP is connection-oriented, reliable, ordered, with error checking and flow control (slower). UDP is connectionless, faster, with no guarantee of delivery or order (used for streaming, gaming, DNS).

**7. Why is TCP reliable?**
It uses acknowledgments, sequence numbers, retransmission of lost packets, flow control, and a three-way handshake to establish a verified connection before sending data.

**8. What is the TCP three-way handshake?**
Client sends SYN → server responds with SYN-ACK → client responds with ACK — after which data transfer begins.

**9. What happens when you type `google.com` into a browser?**
The browser checks its cache, then DNS resolves the domain to an IP address, a TCP connection is established (three-way handshake, plus TLS handshake for HTTPS), the browser sends an HTTP request, the server responds with the page content, and the browser renders it.

**10. What is DNS?**
Domain Name System — a distributed system that translates human-readable domain names (like google.com) into IP addresses.

**11. What is DHCP?**
Dynamic Host Configuration Protocol — automatically assigns IP addresses and network configuration to devices on a network.

**12. HTTP vs HTTPS.**
HTTP transmits data in plaintext; HTTPS encrypts communication using TLS/SSL, providing confidentiality, integrity, and authentication.

**13. What is an IP address?**
A unique numerical identifier assigned to each device on a network, used to route data to the correct destination.

**14. IPv4 vs IPv6.**
IPv4 uses 32-bit addresses (~4.3 billion addresses, format like 192.168.1.1); IPv6 uses 128-bit addresses (a vastly larger address space, format like 2001:0db8::1), designed to solve IPv4 exhaustion.

**15. Public vs private IP.**
Public IPs are globally unique and routable on the internet; private IPs (e.g., 192.168.x.x) are used within local networks and aren't routable on the public internet (translated via NAT).

### Interview Scenarios

**16. What happens when you enter a URL?**
Same flow as Q9: DNS resolution → TCP/TLS handshake → HTTP request sent → server processes and responds → browser renders the response.

**17. How does DNS resolve a domain?**
The browser/OS checks its cache; if not found, it queries a recursive resolver, which queries root servers → TLD servers → authoritative name servers in sequence until it gets the IP address, then caches it.

**18. Why does HTTPS use encryption?**
To protect data confidentiality and integrity in transit, and to authenticate the server (via certificates), preventing eavesdropping and man-in-the-middle attacks.

**19. What is a port?**
A numerical identifier (0–65535) distinguishing different services/applications on the same device (e.g., port 80 for HTTP, 443 for HTTPS).

**20. What is a socket?**
An endpoint for communication, defined by an IP address and port number, used by applications to send/receive data over a network.

**21. What is NAT?**
Network Address Translation — maps private IP addresses within a local network to a single public IP address (and vice versa) for internet communication, conserving public IP addresses.

**22. What is a firewall?**
A security system that monitors and filters incoming/outgoing network traffic based on defined rules, blocking unauthorized access.

**23. Router vs switch.**
A router connects different networks and routes data between them based on IP addresses; a switch connects devices within the same network and forwards data based on MAC addresses.

**24. Hub vs switch.**
A hub broadcasts incoming data to all connected devices (no intelligence, causes collisions); a switch intelligently forwards data only to the intended recipient device based on MAC address.

**25. What is packet loss?**
When one or more data packets fail to reach their destination, often due to network congestion, hardware issues, or errors.

**26. What is latency?**
The time delay between sending a request and receiving a response (or data starting to transfer) over a network.

**27. What is bandwidth?**
The maximum rate of data transfer across a network path, typically measured in bits per second.

---

## 9. Cloud Computing

**1. What is Cloud Computing?**
The delivery of computing resources (servers, storage, databases, networking, software) over the internet on-demand, typically with pay-as-you-go pricing.

**2. Why use cloud computing?**
Cost savings (no upfront hardware), scalability, flexibility, faster deployment, reduced maintenance burden, and global accessibility.

**3. IaaS vs PaaS vs SaaS.**
**IaaS:** raw computing resources (VMs, storage) — e.g., AWS EC2. **PaaS:** a platform to build/deploy apps without managing infrastructure — e.g., Google App Engine. **SaaS:** fully managed software delivered over the internet — e.g., Gmail, Google Docs.

**4. Public vs private vs hybrid cloud.**
**Public:** shared infrastructure owned by a third-party provider (AWS, GCP, Azure). **Private:** dedicated infrastructure for a single organization. **Hybrid:** a combination of both, allowing data/apps to move between them.

**5. What is virtualization?**
Technology creating virtual (software-based) versions of physical resources like servers, storage, or networks, allowing multiple isolated environments to run on shared hardware.

**6. VM vs container.**
A VM virtualizes an entire machine including its own OS (heavier, more isolated); a container virtualizes at the OS level, sharing the host OS kernel while isolating the app and its dependencies (lighter, faster to start).

**7. What is scalability?**
The ability of a system to handle increased load by adding resources.

**8. What is elasticity?**
The ability of a system to automatically scale resources up or down based on real-time demand.

**9. Horizontal vs vertical scaling.**
Horizontal scaling adds more machines/instances to distribute load; vertical scaling adds more power (CPU/RAM) to an existing machine.

**10. What is load balancing?**
Distributing incoming network traffic across multiple servers so no single server is overwhelmed, improving reliability and performance.

**11. What is serverless computing?**
A cloud model where the provider automatically manages server provisioning/scaling, and developers only write and deploy functions that run in response to events (e.g., AWS Lambda), paying only for actual execution time.

**12. What is cloud storage?**
A service letting users store data on remote servers accessed via the internet, managed and maintained by a cloud provider (e.g., Google Cloud Storage, AWS S3).

**13. What is cloud database?**
A database service hosted and managed on cloud infrastructure, accessible over the internet, often with built-in scaling, backups, and high availability (e.g., Firestore, Amazon RDS).

**14. What are the advantages/disadvantages of cloud computing?**
**Advantages:** cost efficiency, scalability, accessibility, reduced maintenance. **Disadvantages:** dependency on internet connectivity, potential security/privacy concerns, vendor lock-in, and less control over infrastructure.

**15. What is availability?**
The proportion of time a system is operational and accessible when needed.

**16. What is fault tolerance?**
A system's ability to continue operating correctly even when some of its components fail.

**17. What is high availability?**
Designing systems (often via redundancy, failover, and load balancing) to minimize downtime and ensure a very high percentage of uptime (e.g., 99.99%).

---

## Study Priority (If Time Is Limited)

| Priority | Subject | Depth |
|---|---|---|
| 🔴 1 | DBMS + SQL | Very deep |
| 🔴 2 | OOP (Python) | Very deep |
| 🔴 3 | Machine Learning | Very deep |
| 🔴 4 | NLP + Federated Learning | Project deep-dive |
| 🟠 5 | Data Science | Medium-deep |
| 🟠 6 | Operating Systems | Medium |
| 🟠 7 | Computer Networks | Medium |
| 🟡 8 | Cloud Computing | Basic |

**Focus order within each subject:**
- **DBMS:** JOIN → GROUP BY/HAVING → subqueries → normalization → keys → indexing → ACID → transactions → SQL vs NoSQL
- **OOP:** 4 pillars → inheritance → overriding → MRO → `super()` → class/instance variables → class/static methods → abstraction
- **Federated Learning:** be ready to walk through your **entire project architecture** end-to-end — this is the single most likely follow-up given it's on your resume.
