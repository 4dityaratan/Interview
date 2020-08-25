# Leetcode Premium Question

## Bank Account Summary
## Strings Differ by One Character
## The Most Recent Orders for Each Product
         Time:  O(nlogn)
         Space: O(n)

        SELECT p.product_name,
               p.product_id,
               o.order_id,
               o.order_date
        FROM Products p
        INNER JOIN
          (SELECT product_id,
                  MAX(order_date) AS first_order_date
           FROM Orders
           GROUP BY product_id) tmp
        ON tmp.product_id = p.product_id
        INNER JOIN Orders o
        ON p.product_id = o.product_id AND tmp.first_order_date = o.order_date
        ORDER BY p.product_name,
                 p.product_id,
                 o.order_id;
## The Most Similar Path in a Graph
## Fix Product Name Format
## Guess the Majority in a Hidden Array
## Find the Index of the Large Integer
## The Most Recent Three Orders
## Patients With a Condition
## Diameter of N-Ary Tree
## Find Users With Valid E-Mails
## Move Sub-Tree of N-Ary Tree
## Customer Order Frequency
## Find Root of N-Ary Tree
## Countries You Can Safely Invest In
## Design a File Sharing System
## Friendly Movies Streamed Last Month
## Clone N-ary Tree
## Clone Binary Tree With Random Pointer
## Group Sold Products By The Date
## Sales by Day of the Week
## Delete N Nodes After M Nodes of a Linked List
## Find All The Lonely Nodes
## Calculate Salaries
## Rectangles Area
## Active Users
## Apples & Oranges
## Evaluate Boolean Expression
## Create a Session Bar Chart
## NPV Queries
## Find the Quiet Students in All Exams
## Top Travellers
## Customers Who Bought Products A and B but Not C
## Capital Gain/Loss
## Total Sales Amount by Year
## Replace Employee ID With The Unique Identifier
## Get the Second Most Recent Activity
## Number of Trusted Contacts of a Customer
## Activity Participants
## Students With Invalid Departments
## Movie Rating
## Number of Transactions per Visit
## List the Products Ordered in a Period
## Ads Performance
## Restaurant Growth
## Running Total for Different Genders
## Find the Team Size
## Check If a String Is a Valid Sequence from Root to Leaves Path in a Binary Tree
## Weather Type in Each Country
## Find the Start and End Number of Continuous Ranges
## Students and Examinations
## Traffic Light Controlled Intersection
## All People Report to the Given Manager
## Print Immutable Linked List in Reverse
## Page Recommendations
## Counting Elements
## Average Selling Price
## Web Crawler Multithreaded
## Number of Comments per Post
## Leftmost Column with at Least a One
## Web Crawler
## First Unique Number
## Report Contiguous Dates
## Perform String Shifts
## Team Scores in Football Tournament
## Queries Quality and Percentage
## Monthly Transactions II
## Last Person to Fit in the Elevator
## Tournament Winners
## Monthly Transactions I
## Design Bounded Blocking Queue
## Immediate Food Delivery II
## Immediate Food Delivery I
## Diet Plan Performance
## Product Price at a Given Date
## Market Analysis II
## Market Analysis I
## Article Views II
## Article Views I
## User Activity for the Past 30 Days II
## User Activity for the Past 30 Days I
## Reported Posts II
## Number of Ships in a Rectangle
## User Purchase Platform
## Active Businesses
## Reported Posts
## Highest Grade For Each Student
## Handshakes That Don't Cross
## New Users Daily Count
## Palindrome Removal
## Delete Tree Nodes
## Remove Interval
## Hexspeak
## Unpopular Books
## Game Play Analysis V
## Divide Chocolate
## Synonymous Sentences
## Smallest Common Region
## Encode Number
## Game Play Analysis IV
## Game Play Analysis III
## Game Play Analysis II
## Game Play Analysis I
## Valid Palindrome III
## Tree Diameter
## Design A Leaderboard
## Array Transformation
## Sales Analysis III
## Sales Analysis II
## Sales Analysis I
## Minimum Time to Build Blocks
## Toss Strange Coins
## Meeting Scheduler
## Missing Number In Arithmetic Progression
## Project Employees III
## Project Employees II
## Project Employees I
## Product Sales Analysis III
## Product Sales Analysis II
## Product Sales Analysis I
## Lexicographically Smallest Equivalent String
## Number of Valid Subarrays
## Longest Repeating Substring
## Missing Element in Sorted Array
## All Paths from Source Lead to Destination
## Confusing Number
## Minimize Rounding Error to Meet Target
## Campus Bikes
## Shortest Way to Form String
## Maximum Number of Ones
## Stepping Numbers
## Two Sum BSTs
## Intersection of Three Sorted Arrays
## Optimize Water Distribution in a Village
## Find Smallest Common Element in All Rows
## Minimum Knight Moves
## How Many Apples Can You Put into the Basket
## Actors and Directors Who Cooperated At Least Three Times
## Customers Who Bought All Products
## Shortest Distance to Target Color
## Before and After Puzzle
## Count Substrings with Only One Distinct Letter
## Minimum Cost to Connect Sticks
## Design File System
## String Transforms Into Another String
## Single-Row Keyboard
## Divide Array Into Increasing Sequences
## Analyze User Website Visit Pattern
## Minimum Swaps to Group All 1's Together
## Check If a Number Is Majority Element in a Sorted Array
## Parallel Courses
## Connecting Cities With Minimum Cost
## Path With Maximum Minimum Value
## Largest Unique Number
## Maximum Average Subtree
## Armstrong Number
## Remove Vowels from a String
## Number of Days in a Month
## The Earliest Moment When Everyone Become Friends
## Find K-Length Substrings With No Repeated Characters
## Two Sum Less Than K
## Sum of Digits in the Minimum Number
## Confusing Number II
## Brace Expansion
## Index Pairs of a String
## High Five
## Digit Count in Range
## Campus Bikes II
## Fixed Point
## Robot Room Cleaner
## Insert into a Sorted Circular Linked List
## Similar RGB Color
## Split BST
## Minimize Max Distance to Gas Station
## Search in a Sorted Array of Unknown Size
## Basic Calculator III
## Encode N-ary Tree to Binary Tree
## Serialize and Deserialize N-ary Tree
## Find Anagram Mappings
## Employee Free Time
## Bold Words in String
## Convert Binary Search Tree to Sorted Doubly Linked List
## Pour Water
## IP to CIDR
## Number Of Corner Rectangles
## Closest Leaf in a Binary Tree
## Sentence Similarity II
## Sentence Similarity
## Minimum Window Subsequence
## Max Stack
## Candy Crush
## Number of Distinct Islands II
## Number of Distinct Islands
## Next Closest Time
## K Empty Slots
## Path Sum IV
## Equal Tree Partition
## Remove 9
## Coin Path
## 4 Keys Keyboard
## Design Search Autocomplete System
## Maximum Average Subarray II
## Find the Derangement of An Array
## Design Log Storage System
## Design Excel Sum Formula
## Maximum Distance in Arrays
## Minimum Factorization
## Add Bold Tag in String
## Design Compressed String Iterator
## Biggest Single Number
## Students Report By Geography
## Average Salary: Departments VS Company
## Second Degree Follower
## Shortest Distance in a Line
## Shortest Distance in a Plane
## Triangle Judgement
## Tree Node
## Sales Person
## Consecutive Available Seats
## Friend Requests II: Who Has the Most Friends
## Design In-Memory File System
## Friend Requests I: Overall Acceptance Rate
## Kill Process
## Customer Placing the Largest Number of Orders
## Investments in 2016
## Find Customer Referee
## Count Student Number in Departments
## Find Cumulative Salary of an Employee
## Get Highest Answer Rate Question
## Squirrel Simulation
## Employee Bonus
## Winning Candidate
## Maximum Vacation Days
## Find Median Given Frequency of Numbers
## Managers with at Least 5 Direct Reports
## Median Employee Salary
## Longest Line of Consecutive One in Matrix
## Split Concatenated Strings
## Binary Tree Longest Consecutive Sequence II
## Split Array with Equal Sum
## Boundary of Binary Tree
## Output Contest Matches
## Construct Binary Tree from String
## Lonely Pixel II
## Lonely Pixel I
## Word Abbreviation
## Inorder Successor in BST II
## The Maze II
## The Maze III
## The Maze
## Max Consecutive Ones II
## Find Permutation
## Encode String with Shortest Length
## Convex Polygon
## Optimal Account Balancing
## Sequence Reconstruction
## Ternary Expression Parser
## Word Squares
## Valid Word Square
## Sentence Screen Fitting
## Minimum Unique Word Abbreviation
## Valid Word Abbreviation
## Design Phone Directory
## Range Addition
## Plus One Linked List
## Find Leaves of Binary Tree
## Nested List Weight Sum II
## Design Hit Counter
## Bomb Enemy
## Sort Transformed Array
## Logger Rate Limiter
## Rearrange String k Distance Apart
## Line Reflection
## Design Snake Game
## Android Unlock Patterns
## Design Tic-Tac-Toe
## Moving Average from Data Stream
## Longest Substring with At Most K Distinct Characters
## Nested List Weight Sum
## Largest BST Subtree
## Maximum Size Subarray Sum Equals k
## Number of Connected Components in an Undirected Graph
## Generalized Abbreviation
## Shortest Distance from All Buildings
## Binary Tree Vertical Order Traversal
## Sparse Matrix Multiplication
## Range Sum Query 2D - Mutable
## Number of Islands II
## Smallest Rectangle Enclosing Black Pixels
## Binary Tree Longest Consecutive Sequence
## Best Meeting Point
## Flip Game II
## Flip Game
## Word Pattern II
## Unique Word Abbreviation
## Walls and Gates
## Inorder Successor in BST
## Zigzag Iterator
## Wiggle Sort
## Find the Celebrity
## Paint Fence
## Closest Binary Search Tree Value II
## Encode and Decode Strings
## Closest Binary Search Tree Value
## Alien Dictionary
## Palindrome Permutation II
## Palindrome Permutation
## Paint House II
## Graph Valid Tree
## 3Sum Smaller
## Paint House
## Verify Preorder Sequence in Binary Search Tree
## Factor Combinations
## Meeting Rooms II
## Meeting Rooms
## Flatten 2D Vector
## Count Univalue Subtrees
## Group Shifted Strings
## Strobogrammatic Number III
## Strobogrammatic Number II
## Strobogrammatic Number
## Shortest Word Distance III
## Shortest Word Distance II
## Shortest Word Distance
## Reverse Words in a String II
## Two Sum III - Data structure design
## Missing Ranges
## One Edit Distance
## Longest Substring with At Most Two Distinct Characters
## Read N Characters Given Read4 II - Call multiple times
## Read N Characters Given Read4
## Binary Tree Upside Down
