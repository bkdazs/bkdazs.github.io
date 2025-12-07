# Programming & FAANG Interview Prep - Complete Taxonomy & Content Plan

## Overview
A comprehensive curriculum for software engineering interview preparation, covering data structures, algorithms, system design, and behavioral interviews. Designed to take candidates from beginner to FAANG-ready level.

---

## 1. DATA STRUCTURES

### 1.1 Linear Data Structures

#### 1.1.1 Arrays
- Array fundamentals and memory layout
- Static vs dynamic arrays
- Multi-dimensional arrays
- Array manipulation techniques
- Sliding window on arrays
- Two-pointer technique
- Prefix sum arrays
- Difference arrays
- Array rotation algorithms
- Array interview patterns

#### 1.1.2 Strings
- String fundamentals
- String manipulation techniques
- StringBuilder and efficiency
- Pattern matching basics
- KMP algorithm
- Rabin-Karp algorithm
- Z-algorithm
- String hashing
- Anagram problems
- String interview patterns

#### 1.1.3 Linked Lists
- Singly linked list implementation
- Doubly linked list
- Circular linked list
- Fast and slow pointers
- Linked list reversal techniques
- Merge sorted lists
- Cycle detection
- Intersection detection
- LRU Cache implementation
- Linked list interview patterns

#### 1.1.4 Stacks
- Stack fundamentals
- Stack implementation (array vs linked list)
- Monotonic stack
- Min stack / Max stack
- Stack for expression evaluation
- Infix to postfix conversion
- Balanced parentheses
- Next greater element
- Largest rectangle in histogram
- Stack interview patterns

#### 1.1.5 Queues
- Queue fundamentals
- Queue implementation
- Circular queue
- Deque (double-ended queue)
- Priority queue basics
- Monotonic queue
- Sliding window maximum
- BFS with queues
- Queue reconstruction
- Queue interview patterns

### 1.2 Non-Linear Data Structures

#### 1.2.1 Binary Trees
- Binary tree fundamentals
- Tree traversals (inorder, preorder, postorder)
- Level order traversal
- Tree construction from traversals
- Binary tree properties
- Lowest Common Ancestor
- Tree diameter and height
- Path sum problems
- Serialize/deserialize trees
- Binary tree interview patterns

#### 1.2.2 Binary Search Trees
- BST fundamentals and properties
- BST insertion and deletion
- BST search operations
- BST validation
- Inorder successor/predecessor
- BST to sorted array
- Sorted array to BST
- BST floor and ceiling
- Kth smallest/largest in BST
- BST interview patterns

#### 1.2.3 Balanced Trees
- AVL trees explained
- Red-Black trees
- B-trees and B+ trees
- Splay trees
- Tree rotations
- Self-balancing mechanisms
- When to use balanced trees
- Balanced tree implementation
- Real-world applications
- Balanced tree interview patterns

#### 1.2.4 Heaps
- Heap fundamentals
- Min heap vs max heap
- Heap implementation
- Heapify operations
- Heap sort algorithm
- Priority queue with heaps
- Top K problems
- Merge K sorted lists
- Median of stream
- Heap interview patterns

#### 1.2.5 Tries
- Trie fundamentals
- Trie implementation
- Insert and search operations
- Prefix matching
- Autocomplete system
- Word search problems
- Trie vs hash table
- Memory-efficient tries
- Trie applications
- Trie interview patterns

### 1.3 Advanced Data Structures

#### 1.3.1 Graphs
- Graph fundamentals
- Graph representations
- Adjacency list vs matrix
- Directed vs undirected
- Weighted graphs
- Graph traversal (BFS/DFS)
- Connected components
- Cycle detection
- Topological sort
- Graph interview patterns

#### 1.3.2 Hash Tables
- Hash table fundamentals
- Hash function design
- Collision handling
- Open addressing vs chaining
- Load factor and rehashing
- Hash map implementation
- Hash set implementation
- Two sum pattern
- Group anagrams pattern
- Hash table interview patterns

#### 1.3.3 Union-Find (Disjoint Set)
- Union-Find fundamentals
- Basic implementation
- Union by rank
- Path compression
- Applications of Union-Find
- Connected components
- Detect cycle in undirected graph
- Kruskal's algorithm usage
- Accounts merge problem
- Union-Find interview patterns

#### 1.3.4 Segment Trees
- Segment tree fundamentals
- Build operations
- Point updates
- Range queries
- Lazy propagation
- Range update queries
- 2D segment trees
- Persistent segment trees
- Applications and problems
- Segment tree interview patterns

#### 1.3.5 Binary Indexed Trees (Fenwick)
- BIT fundamentals
- Point updates
- Prefix queries
- Range sum queries
- 2D BIT
- BIT vs Segment tree
- Common applications
- Implementation techniques
- Order statistics
- BIT interview patterns

---

## 2. ALGORITHMS

### 2.1 Searching Algorithms

#### 2.1.1 Binary Search
- Binary search fundamentals
- Iterative vs recursive
- Search in sorted array
- First/last occurrence
- Search in rotated array
- Search in 2D matrix
- Peak element finding
- Binary search on answer
- Minimizing maximum
- Binary search interview patterns

#### 2.1.2 Advanced Search
- Ternary search
- Exponential search
- Interpolation search
- Jump search
- Search in infinite array
- Median of two sorted arrays
- Kth element problems
- Search patterns
- Real-world search applications
- Search algorithm comparison

### 2.2 Sorting Algorithms

#### 2.2.1 Basic Sorting
- Bubble sort
- Selection sort
- Insertion sort
- Sorting stability
- Adaptive sorting
- In-place sorting
- Comparison-based sorting
- Lower bound for comparison sort
- When to use basic sorts
- Basic sorting problems

#### 2.2.2 Efficient Sorting
- Merge sort explained
- Quick sort explained
- Heap sort
- Partition algorithms
- Choosing pivot strategies
- Randomized quicksort
- External sorting
- Sorting linked lists
- Sort colors (Dutch flag)
- Efficient sorting interview patterns

#### 2.2.3 Non-Comparison Sorting
- Counting sort
- Radix sort
- Bucket sort
- When to use non-comparison
- Sorting strings efficiently
- Sorting special cases
- Sorting large datasets
- Sorting stability trade-offs
- Custom comparators
- Non-comparison sorting problems

### 2.3 Graph Algorithms

#### 2.3.1 Graph Traversal
- BFS implementation
- DFS implementation
- Iterative vs recursive
- Applications of BFS
- Applications of DFS
- Graph coloring
- Bipartite check
- Number of islands
- Clone graph
- Traversal interview patterns

#### 2.3.2 Shortest Path
- Dijkstra's algorithm
- Bellman-Ford algorithm
- Floyd-Warshall algorithm
- A* algorithm basics
- Shortest path in unweighted graph
- Shortest path with constraints
- Network delay time
- Cheapest flights
- Path with maximum probability
- Shortest path interview patterns

#### 2.3.3 Minimum Spanning Tree
- MST fundamentals
- Kruskal's algorithm
- Prim's algorithm
- MST properties
- Cut property
- Cycle property
- MST applications
- Minimum cost to connect
- Critical and pseudo-critical edges
- MST interview patterns

#### 2.3.4 Advanced Graph
- Strongly connected components
- Tarjan's algorithm
- Kosaraju's algorithm
- Articulation points
- Bridges in graph
- Eulerian path/circuit
- Hamiltonian path
- Traveling salesman intro
- Network flow basics
- Advanced graph problems

### 2.4 Dynamic Programming

#### 2.4.1 DP Fundamentals
- What is dynamic programming
- Overlapping subproblems
- Optimal substructure
- Top-down (memoization)
- Bottom-up (tabulation)
- State definition
- State transition
- Base cases
- Space optimization
- DP problem identification

#### 2.4.2 1D Dynamic Programming
- Fibonacci variations
- Climbing stairs
- House robber problem
- Maximum subarray (Kadane's)
- Coin change problem
- Perfect squares
- Word break problem
- Decode ways
- Jump game series
- 1D DP interview patterns

#### 2.4.3 2D Dynamic Programming
- Grid-based DP
- Unique paths
- Minimum path sum
- Edit distance
- Longest common subsequence
- Longest increasing subsequence
- Matrix chain multiplication
- Palindrome partitioning
- Interleaving strings
- 2D DP interview patterns

#### 2.4.4 Advanced DP
- DP on trees
- DP with bitmask
- Digit DP
- DP on intervals
- DP optimization techniques
- Knuth optimization
- Divide and conquer optimization
- Convex hull optimization
- SOS DP (Sum over Subsets)
- Advanced DP interview patterns

### 2.5 Greedy Algorithms

#### 2.5.1 Greedy Fundamentals
- Greedy approach explained
- When greedy works
- Proving greedy correctness
- Exchange argument
- Greedy vs DP
- Activity selection
- Fractional knapsack
- Huffman coding
- Greedy staying ahead
- Greedy fundamentals patterns

#### 2.5.2 Interval Problems
- Interval scheduling
- Meeting rooms problem
- Non-overlapping intervals
- Merge intervals
- Insert interval
- Minimum arrows
- Interval intersection
- Employee free time
- Task scheduler
- Interval interview patterns

#### 2.5.3 Other Greedy Problems
- Jump game greedy
- Gas station
- Candy distribution
- Partition labels
- Queue reconstruction
- Minimum platforms
- Job sequencing
- Fractional items
- Greedy string problems
- Greedy interview patterns

### 2.6 Recursion & Backtracking

#### 2.6.1 Recursion Fundamentals
- Recursion basics
- Base case and recursive case
- Call stack understanding
- Recursion vs iteration
- Tail recursion
- Recursion tree analysis
- Common recursion patterns
- Recursion pitfalls
- Debugging recursion
- Recursion interview patterns

#### 2.6.2 Backtracking
- Backtracking fundamentals
- N-Queens problem
- Sudoku solver
- Permutations
- Combinations
- Subsets generation
- Word search
- Palindrome partitioning
- Letter combinations
- Backtracking interview patterns

#### 2.6.3 Advanced Recursion
- Divide and conquer
- Merge sort analysis
- Quick sort analysis
- Master theorem
- Binary search recursion
- Tree recursion patterns
- Graph recursion (DFS)
- Memoization with recursion
- Recursion to iteration
- Advanced recursion patterns

### 2.7 Bit Manipulation

#### 2.7.1 Bit Basics
- Binary number system
- Bitwise operators (AND, OR, XOR)
- Bit shifts
- Two's complement
- Checking bits
- Setting and clearing bits
- Toggling bits
- Counting set bits
- Power of two check
- Bit basics patterns

#### 2.7.2 Bit Manipulation Problems
- Single number problems
- Missing number
- Reverse bits
- Hamming distance
- Bitwise AND of range
- Maximum XOR
- Subsets using bits
- Gray code
- UTF-8 validation
- Bit manipulation interview patterns

---

## 3. PROBLEM-SOLVING PATTERNS

### 3.1 Two Pointers

#### 3.1.1 Two Pointer Techniques
- Same direction pointers
- Opposite direction pointers
- Fast and slow pointers
- Two arrays pointers
- Partitioning pointers
- Remove duplicates
- Container with most water
- Trapping rain water
- Three sum problem
- Two pointer interview patterns

### 3.2 Sliding Window

#### 3.2.1 Sliding Window Techniques
- Fixed size window
- Variable size window
- Shrinking window
- Maximum sum subarray
- Minimum window substring
- Longest substring without repeat
- Longest repeating character
- Permutation in string
- Window with K distinct
- Sliding window interview patterns

### 3.3 Divide and Conquer

#### 3.3.1 Divide and Conquer Problems
- Merge sort pattern
- Quick sort pattern
- Binary search as D&C
- Maximum subarray
- Closest pair of points
- Karatsuba multiplication
- Strassen's matrix multiplication
- Count inversions
- Median of medians
- D&C interview patterns

### 3.4 Advanced Patterns

#### 3.4.1 Monotonic Stack/Queue
- Monotonic increasing stack
- Monotonic decreasing stack
- Next greater element
- Previous smaller element
- Daily temperatures
- Stock span problem
- Largest rectangle
- Maximal rectangle
- Sum of minimums
- Monotonic patterns

#### 3.4.2 Top K Elements
- Using heaps for Top K
- Quick select algorithm
- Kth largest element
- K closest points
- Top K frequent elements
- K closest in sorted array
- Find median
- Merge K sorted
- Smallest range
- Top K interview patterns

#### 3.4.3 Merge Intervals
- Interval basics
- Merge overlapping
- Insert interval
- Interval intersection
- Meeting rooms series
- Minimum intervals
- Employee free time
- Add bold tags
- Range module
- Interval interview patterns

---

## 4. SYSTEM DESIGN

### 4.1 System Design Fundamentals

#### 4.1.1 Design Basics
- System design interview format
- Requirements gathering
- Functional vs non-functional
- Back-of-envelope estimation
- System design template
- Trade-offs in design
- Design principles (SOLID)
- Design patterns overview
- API design basics
- System design preparation

#### 4.1.2 Scalability Concepts
- Horizontal vs vertical scaling
- Load balancing
- Caching strategies
- Database sharding
- Replication strategies
- CDN usage
- Microservices basics
- Stateless design
- Async processing
- Scalability patterns

#### 4.1.3 Database Design
- SQL vs NoSQL
- Database normalization
- Indexing strategies
- Query optimization
- ACID properties
- CAP theorem
- Consistency patterns
- Database partitioning
- Database replication
- Database selection criteria

### 4.2 Building Blocks

#### 4.2.1 Load Balancers
- Load balancer types
- Round robin
- Weighted round robin
- Least connections
- IP hash
- Layer 4 vs Layer 7
- Health checks
- Session persistence
- Global load balancing
- Load balancer implementation

#### 4.2.2 Caching
- Cache fundamentals
- Cache patterns
- Write-through vs write-back
- Cache eviction policies
- Distributed caching
- Redis deep dive
- Memcached overview
- Cache invalidation
- Cache consistency
- Caching interview patterns

#### 4.2.3 Message Queues
- Queue fundamentals
- Pub/sub model
- Apache Kafka basics
- RabbitMQ overview
- Event-driven architecture
- Queue vs stream
- Message ordering
- Message delivery guarantees
- Dead letter queues
- Message queue design patterns

#### 4.2.4 Storage Systems
- Block vs file vs object storage
- Distributed file systems
- HDFS basics
- Amazon S3 design
- Blob storage
- Data lake concepts
- Storage tiering
- Data replication
- Backup strategies
- Storage system patterns

### 4.3 System Design Case Studies

#### 4.3.1 URL Shortener
- Requirements analysis
- High-level design
- Database schema
- Short URL generation
- Collision handling
- Caching strategy
- Analytics features
- Rate limiting
- Security considerations
- URL shortener trade-offs

#### 4.3.2 Twitter/Social Feed
- Requirements gathering
- Timeline generation
- Fan-out strategies
- Tweet storage
- Media handling
- Search functionality
- Notification system
- Trending topics
- Rate limiting
- Twitter design trade-offs

#### 4.3.3 YouTube/Video Streaming
- Requirements analysis
- Video upload pipeline
- Video processing
- Streaming protocols
- Content delivery
- Recommendation system
- Comments and interactions
- Analytics pipeline
- Monetization system
- Video platform trade-offs

#### 4.3.4 WhatsApp/Messaging
- Requirements analysis
- Message delivery
- Real-time communication
- Group messaging
- Media sharing
- End-to-end encryption
- Status/stories feature
- Push notifications
- Offline support
- Messaging system trade-offs

#### 4.3.5 Uber/Ride Sharing
- Requirements analysis
- Location tracking
- Matching algorithm
- ETA calculation
- Dynamic pricing
- Payment system
- Rating system
- Map integration
- Notification system
- Ride sharing trade-offs

#### 4.3.6 E-commerce (Amazon)
- Requirements analysis
- Product catalog
- Search functionality
- Shopping cart
- Order management
- Payment processing
- Inventory management
- Recommendation engine
- Review system
- E-commerce trade-offs

### 4.4 Advanced System Design

#### 4.4.1 Distributed Systems
- Distributed consensus
- Raft algorithm basics
- Paxos overview
- Distributed transactions
- Two-phase commit
- Saga pattern
- Event sourcing
- CQRS pattern
- Eventual consistency
- Distributed system patterns

#### 4.4.2 Rate Limiting
- Rate limiting algorithms
- Token bucket
- Leaky bucket
- Fixed window
- Sliding window
- Rate limiting strategies
- Distributed rate limiting
- API throttling
- Rate limit headers
- Rate limiting implementation

#### 4.4.3 Notification System
- Notification types
- Push notification architecture
- Email notification design
- SMS gateway integration
- Notification preferences
- Delivery guarantees
- Retry mechanisms
- Notification templates
- Analytics and tracking
- Notification system design

#### 4.4.4 Search System
- Search architecture
- Inverted index
- Elasticsearch basics
- Full-text search
- Search ranking
- Autocomplete design
- Fuzzy search
- Search relevance
- Search personalization
- Search system design

---

## 5. OBJECT-ORIENTED DESIGN

### 5.1 OOP Fundamentals

#### 5.1.1 OOP Concepts
- Classes and objects
- Encapsulation
- Inheritance
- Polymorphism
- Abstraction
- Composition vs inheritance
- Interface design
- Access modifiers
- Static vs instance
- OOP interview questions

#### 5.1.2 SOLID Principles
- Single responsibility principle
- Open/closed principle
- Liskov substitution principle
- Interface segregation principle
- Dependency inversion principle
- SOLID in practice
- SOLID violations
- Refactoring to SOLID
- SOLID trade-offs
- SOLID interview patterns

### 5.2 Design Patterns

#### 5.2.1 Creational Patterns
- Singleton pattern
- Factory method pattern
- Abstract factory pattern
- Builder pattern
- Prototype pattern
- When to use each
- Pattern combinations
- Anti-patterns
- Real-world examples
- Creational pattern problems

#### 5.2.2 Structural Patterns
- Adapter pattern
- Bridge pattern
- Composite pattern
- Decorator pattern
- Facade pattern
- Flyweight pattern
- Proxy pattern
- When to use each
- Pattern combinations
- Structural pattern problems

#### 5.2.3 Behavioral Patterns
- Observer pattern
- Strategy pattern
- Command pattern
- State pattern
- Template method
- Chain of responsibility
- Iterator pattern
- Mediator pattern
- Visitor pattern
- Behavioral pattern problems

### 5.3 OOD Case Studies

#### 5.3.1 Parking Lot
- Requirements gathering
- Class diagram
- Vehicle hierarchy
- Parking spot types
- Parking logic
- Payment system
- Ticket system
- Capacity management
- Extensions and variations
- Parking lot implementation

#### 5.3.2 Library Management
- Requirements analysis
- Book management
- Member management
- Borrowing system
- Fine calculation
- Reservation system
- Search functionality
- Notification system
- Reporting
- Library system implementation

#### 5.3.3 Elevator System
- Requirements analysis
- Elevator state machine
- Request handling
- Scheduling algorithms
- Multiple elevators
- Priority handling
- Capacity management
- Emergency handling
- Maintenance mode
- Elevator implementation

#### 5.3.4 Chess Game
- Requirements analysis
- Board representation
- Piece hierarchy
- Move validation
- Check and checkmate
- Castling and en passant
- Game state management
- Move history
- Undo functionality
- Chess implementation

#### 5.3.5 Movie Ticket Booking
- Requirements analysis
- Theater management
- Show scheduling
- Seat selection
- Booking workflow
- Payment integration
- Cancellation handling
- Notifications
- Discounts and offers
- Booking system implementation

---

## 6. BEHAVIORAL INTERVIEWS

### 6.1 Behavioral Fundamentals

#### 6.1.1 STAR Method
- Situation component
- Task component
- Action component
- Result component
- STAR variations
- Crafting STAR stories
- Story bank preparation
- Quantifying results
- Common mistakes
- STAR practice techniques

#### 6.1.2 Leadership Principles (Amazon)
- Customer obsession
- Ownership
- Invent and simplify
- Are right, a lot
- Learn and be curious
- Hire and develop the best
- Insist on highest standards
- Think big
- Bias for action
- Frugality, Earn trust, Dive deep, Have backbone, Deliver results

### 6.2 Common Questions

#### 6.2.1 Conflict & Challenges
- Disagreement with teammate
- Conflict with manager
- Difficult stakeholder
- Tight deadline handling
- Project failure recovery
- Resource constraints
- Technical disagreement
- Priority conflicts
- Cross-team challenges
- Conflict resolution patterns

#### 6.2.2 Leadership & Teamwork
- Leading a project
- Mentoring others
- Team motivation
- Building consensus
- Delegation experience
- Giving feedback
- Receiving feedback
- Cross-functional work
- Remote collaboration
- Leadership story patterns

#### 6.2.3 Problem-Solving
- Complex problem solved
- Ambiguous situation
- Data-driven decision
- Quick decision making
- Long-term vs short-term
- Risk assessment
- Innovation examples
- Process improvement
- Learning from mistakes
- Problem-solving patterns

#### 6.2.4 Personal Growth
- Biggest strength
- Areas of improvement
- Career goals
- Learning something new
- Handling failure
- Stepping out of comfort zone
- Feedback received
- Professional development
- Work-life balance
- Growth story patterns

### 6.3 Company-Specific Prep

#### 6.3.1 FAANG Specifics
- Google interview process
- Amazon interview process
- Meta interview process
- Apple interview process
- Microsoft interview process
- Netflix interview process
- Company culture fit
- Values alignment
- Research strategies
- Company-specific patterns

---

## 7. LANGUAGE-SPECIFIC PREP

### 7.1 Python

#### 7.1.1 Python for Interviews
- Python syntax essentials
- List operations
- Dictionary operations
- Set operations
- String manipulation
- Built-in functions
- Lambda and functional
- List comprehensions
- Common libraries
- Python interview tips

#### 7.1.2 Python Data Structures
- Python lists internals
- Python dicts internals
- Collections module
- heapq module
- bisect module
- deque usage
- defaultdict
- Counter class
- OrderedDict
- Python DS implementation

### 7.2 Java

#### 7.2.1 Java for Interviews
- Java syntax essentials
- Arrays in Java
- ArrayList vs LinkedList
- HashMap internals
- TreeMap usage
- Comparable vs Comparator
- Lambda expressions
- Stream API basics
- Exception handling
- Java interview tips

#### 7.2.2 Java Collections
- Collection framework overview
- List implementations
- Set implementations
- Map implementations
- Queue implementations
- Priority Queue
- Custom comparators
- Thread-safe collections
- Choosing right collection
- Java collections interview

### 7.3 C++

#### 7.3.1 C++ for Interviews
- C++ syntax essentials
- STL overview
- Vector operations
- Map and unordered_map
- Set and unordered_set
- Pair and tuple
- Iterator usage
- Algorithm library
- Memory management
- C++ interview tips

---

## 8. RDBMS & SQL

### 8.1 SQL Fundamentals

#### 8.1.1 SQL Basics
- Introduction to relational databases
- Tables, rows, and columns
- Data types in SQL
- CREATE TABLE statements
- INSERT, UPDATE, DELETE
- SELECT statement basics
- WHERE clause filtering
- ORDER BY and LIMIT
- NULL handling
- SQL syntax across databases

#### 8.1.2 Filtering & Sorting
- Comparison operators
- BETWEEN and IN operators
- LIKE and pattern matching
- Regular expressions in SQL
- Multiple WHERE conditions
- AND, OR, NOT operators
- ORDER BY multiple columns
- ASC and DESC sorting
- LIMIT and OFFSET pagination
- DISTINCT keyword

#### 8.1.3 Aggregate Functions
- COUNT function
- SUM function
- AVG function
- MIN and MAX functions
- GROUP BY clause
- HAVING clause
- Grouping sets
- ROLLUP and CUBE
- Aggregate with DISTINCT
- Common aggregation patterns

### 8.2 Joins & Relationships

#### 8.2.1 Join Types
- INNER JOIN explained
- LEFT JOIN (LEFT OUTER)
- RIGHT JOIN (RIGHT OUTER)
- FULL OUTER JOIN
- CROSS JOIN
- Self joins
- Multiple table joins
- Join conditions and ON clause
- Join vs WHERE filtering
- Join performance considerations

#### 8.2.2 Subqueries
- Subquery fundamentals
- Scalar subqueries
- Row subqueries
- Table subqueries
- Correlated subqueries
- EXISTS and NOT EXISTS
- IN with subqueries
- ANY and ALL operators
- Subquery in SELECT clause
- Subquery optimization

#### 8.2.3 Set Operations
- UNION operator
- UNION ALL
- INTERSECT operator
- EXCEPT (MINUS) operator
- Combining multiple sets
- Set operation ordering
- NULL handling in sets
- Performance of set operations
- When to use each operation
- Set operations interview patterns

### 8.3 Advanced SQL

#### 8.3.1 Window Functions
- Window function introduction
- ROW_NUMBER function
- RANK and DENSE_RANK
- NTILE function
- LAG and LEAD functions
- FIRST_VALUE and LAST_VALUE
- SUM/AVG/COUNT over windows
- PARTITION BY clause
- ORDER BY in windows
- Window frames (ROWS/RANGE)

#### 8.3.2 Common Table Expressions
- CTE syntax and basics
- Multiple CTEs
- Recursive CTEs
- Hierarchical data queries
- Tree traversal with CTEs
- CTE vs subqueries
- CTE performance
- CTE best practices
- Complex CTE patterns
- CTE interview problems

#### 8.3.3 Advanced Techniques
- CASE expressions
- COALESCE and NULLIF
- String functions
- Date and time functions
- Conditional aggregation
- Pivoting data (PIVOT)
- Unpivoting data
- JSON functions in SQL
- Working with arrays
- Advanced function patterns

### 8.4 Database Design

#### 8.4.1 Schema Design
- Entity-Relationship modeling
- Primary keys
- Foreign keys
- Unique constraints
- Check constraints
- Default values
- Normalization (1NF, 2NF, 3NF)
- Denormalization trade-offs
- Many-to-many relationships
- Schema design best practices

#### 8.4.2 Indexing
- Index fundamentals
- B-tree indexes
- Hash indexes
- Composite indexes
- Covering indexes
- Index selectivity
- When to create indexes
- Index maintenance
- Index impact on writes
- Index optimization strategies

#### 8.4.3 Query Optimization
- EXPLAIN and query plans
- Table scans vs index scans
- Join algorithms (nested loop, hash, merge)
- Query rewriting techniques
- Statistics and cardinality
- Parameterized queries
- Query hints
- Avoiding common pitfalls
- Batch processing patterns
- Performance tuning workflow

### 8.5 SQL Interview Patterns

#### 8.5.1 Classic Interview Problems
- Second highest salary
- Nth highest salary
- Duplicate emails
- Customers who never order
- Department top earners
- Consecutive numbers
- Rising temperature
- Exchange seats
- Rank scores
- Human traffic of stadium

#### 8.5.2 Advanced Interview Problems
- Trips and users analysis
- Median employee salary
- Find median given frequency
- Game play analysis series
- Retention and churn queries
- Moving averages
- Running totals
- Gap and island problems
- Sessionization queries
- Complex reporting queries

---

## 9. LEETCODE PATTERNS

### 9.1 Easy Pattern Problems

#### 9.1.1 Array Easy Patterns
- Two sum
- Best time to buy stock
- Contains duplicate
- Maximum subarray
- Move zeroes
- Plus one
- Merge sorted array
- Remove duplicates
- Single number
- Missing number

#### 9.1.2 String Easy Patterns
- Valid palindrome
- Reverse string
- First unique character
- Valid anagram
- Longest common prefix
- Implement strStr
- Count and say
- String to integer
- Roman to integer
- Palindrome number

### 9.2 Medium Pattern Problems

#### 9.2.1 Array Medium Patterns
- Three sum
- Container with most water
- Product except self
- Find peak element
- Search in rotated array
- Next permutation
- Rotate image
- Spiral matrix
- Set matrix zeroes
- Subarray sum equals K

#### 9.2.2 DP Medium Patterns
- Longest increasing subsequence
- Coin change
- Word break
- House robber II
- Decode ways
- Unique paths
- Jump game
- Maximum product subarray
- Palindromic substrings
- Partition equal subset

### 9.3 Hard Pattern Problems

#### 9.3.1 Graph Hard Patterns
- Word ladder
- Alien dictionary
- Course schedule II
- Critical connections
- Minimum height trees
- Reconstruct itinerary
- Swim in rising water
- Shortest path visiting all
- Making large island
- Bus routes

#### 9.3.2 DP Hard Patterns
- Edit distance
- Regular expression matching
- Wildcard matching
- Longest valid parentheses
- Burst balloons
- Palindrome partitioning II
- Distinct subsequences
- Interleaving string
- Scramble string
- Dungeon game

---

## 10. MOCK INTERVIEW PREP

### 10.1 Interview Strategies

#### 10.1.1 Coding Interview Tips
- Problem clarification
- Example walkthrough
- Brute force first
- Optimization approach
- Code organization
- Testing your code
- Time complexity analysis
- Space complexity analysis
- Communication during coding
- Handling hints

#### 10.1.2 System Design Tips
- Requirement gathering
- High-level design first
- Component breakdown
- Deep dive selection
- Trade-off discussion
- Scalability considerations
- Handling follow-ups
- Whiteboarding tips
- Time management
- Common mistakes

### 10.2 Practice Resources

#### 10.2.1 Practice Plan
- Weekly practice schedule
- Topic rotation
- Timed practice sessions
- Mock interview frequency
- Review and reflection
- Weak area focus
- Company-specific prep
- Final week preparation
- Day before interview
- Interview day routine

---

## ARTICLE COUNT SUMMARY

| Section | Subsections | Articles (10 per subsection) |
|---------|-------------|------------------------------|
| 1. Data Structures | 14 | 140 |
| 2. Algorithms | 20 | 200 |
| 3. Problem-Solving Patterns | 7 | 70 |
| 4. System Design | 17 | 170 |
| 5. Object-Oriented Design | 12 | 120 |
| 6. Behavioral Interviews | 8 | 80 |
| 7. Language-Specific | 5 | 50 |
| 8. RDBMS & SQL | 12 | 120 |
| 9. LeetCode Patterns | 6 | 60 |
| 10. Mock Interview Prep | 3 | 30 |
| **TOTAL** | **104** | **1040** |

---

## CONTENT PRIORITIES

### Phase 1 (High Priority)
- Data Structures Fundamentals
- Binary Search & Sorting
- Dynamic Programming
- Two Pointers & Sliding Window
- System Design Basics

### Phase 2 (Medium Priority)
- Graph Algorithms
- Advanced Data Structures
- OOD Case Studies
- Behavioral Questions
- LeetCode Patterns

### Phase 3 (Lower Priority)
- Advanced Algorithms
- Language-Specific Deep Dives
- Advanced System Design
- Mock Interview Resources

---

## CROSS-REFERENCES

This taxonomy connects with:
- **AI/ML**: ML system design, optimization algorithms, graph algorithms for GNNs
- **Finance**: Trading system design, financial algorithms, quantitative analysis
- **Blockchain**: Smart contract patterns, consensus algorithms, cryptographic algorithms
