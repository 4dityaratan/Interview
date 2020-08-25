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
// Time:  O(n^2 * m), m is the length of targetPath
// Space: O(n * m)

class Solution {
public:
    vector<int> mostSimilar(int n, vector<vector<int>>& roads, vector<string>& names, vector<string>& targetPath) {
        vector<vector<int>> adj(n);
        for (const auto& road : roads) {
            adj[road[0]].emplace_back(road[1]);
            adj[road[1]].emplace_back(road[0]);
        }

        vector<vector<int>> dp(targetPath.size() + 1, vector<int>(n));
        for (int i = 1; i <= targetPath.size(); ++i) {
            for (int v = 0; v < n; ++v) {
                dp[i][v] = targetPath.size();
                for (const auto& u : adj[v]) {
                    dp[i][v] = min(dp[i][v], dp[i - 1][u]);
                }
                dp[i][v] += int(names[v] != targetPath[i - 1]);
            }
        }

        vector<int> path = {static_cast<int>(distance(cbegin(dp.back()), 
                                                      min_element(cbegin(dp.back()), cend(dp.back()))))};
        for (int i = targetPath.size(); i >= 2; --i) {
            for (const auto& u : adj[path.back()]) {
                if (dp[i - 1][u] + int(names[path.back()] != targetPath[i - 1]) == dp[i][path.back()]) {
                    path.emplace_back(u);
                    break;
                }
            }
        }
        reverse(begin(path), end(path));
        return path;
    }
};

## Fix Product Name Format
         Time:  O(nlogn)
         Space: O(n)

         SELECT LOWER(TRIM(product_name)) product_name,
                substring(sale_date, 1, 7) sale_date,
                count(sale_id) total
         FROM sales
         GROUP BY 1, 2
         ORDER BY 1, 2;

## Guess the Majority in a Hidden Array
// Time:  O(n), n queries
// Space: O(1)

class Solution {
public:
    int guessMajority(ArrayReader &reader) {
        int count_a = 1, count_b = 0, idx_b = -1;
        const auto& value_0_1_2_3 = reader.query(0, 1, 2, 3);
        int value_0_1_2_i = -1;
        for (int i = reader.length() - 1; i >= 4; --i) {
            value_0_1_2_i = reader.query(0, 1, 2, i);
            if (value_0_1_2_i == value_0_1_2_3) {  // nums[i] == nums[3]
                ++count_a;
            } else {
                ++count_b;
                idx_b = i;
            }
        }
        const auto& value_0_1_2_4 = value_0_1_2_i;
        for (int i = 0; i < 3; ++i) {
            vector<int> a_b;
            for (int j = 0; j < 3; ++j) {
                if (j == i) {
                    continue;
                }
                a_b.emplace_back(j);
            }
            const auto& value_a_b_3_4 = reader.query(a_b[0], a_b[1], 3, 4);
            if (value_a_b_3_4 == value_0_1_2_4) {  // nums[i] == nums[3]
                ++count_a;
            } else {
                ++count_b;
                idx_b = i;
            }
        }
        if (count_a == count_b) {
            return -1;
        }
        return count_a > count_b ? 3 : idx_b;
    }
};
         
## Find the Index of the Large Integer
// Time:  O(logn)
// Space: O(1)

class Solution {
public:
    int getIndex(ArrayReader &reader) {
        int left = 0, right = reader.length() - 1;
        while (left < right) {
            const auto& mid = left + (right - left) / 2;
            if (reader.compareSub(left, mid, (right - left + 1) % 2 ? mid : mid + 1, right) >= 0) {
                right = mid;
            }  else {
                left = mid + 1;
            }
        }
        return left;
    }
};

## The Most Recent Three Orders
Time:  O(nlogn)
Space: O(n)

SELECT customer_name,
       customer_id,
       order_id,
       order_date
FROM
  (SELECT @accu := (CASE
                        WHEN co.customer_id = @prev THEN @accu + 1
                        ELSE 1
                    END) AS n,
          @prev := co.customer_id AS customer_id,
          co.name AS customer_name,
          co.order_id AS order_id,
          co.order_date AS order_date
   FROM
     (SELECT @accu := 0, @prev := 0) AS init,
     (SELECT c.name, c.customer_id, o.order_id, o.order_date FROM
      Customers c
      INNER JOIN Orders o
      ON c.customer_id = o.customer_id
      ORDER BY c.name ASC, c.customer_id ASC, o.order_date DESC) AS co
  ) AS tmp
WHERE n < 4;

## Patients With a Condition
Time:  O(n)
Space: O(n)

SELECT * 
FROM Patients AS p
WHERE p.conditions REGEXP '^DIAB1| DIAB1';


## Diameter of N-Ary Tree
// Time:  O(n)
// Space: O(h)

class Solution {
public:
    int diameter(Node* root) {
        return iter_dfs(root).first;
    }

private:
    pair<int, int> iter_dfs(Node *root) {
        using RET = pair<int, int>;
        RET result;
        vector<tuple<int, Node *, shared_ptr<RET>, RET *>> stk = {{1, root, nullptr, &result}};
        while (!stk.empty()) {
            const auto [step, node, ret2, ret] = stk.back(); stk.pop_back();
            if (step == 1) {
                for (int i = node->children.size() - 1; i >= 0; --i) {
                    const auto& ret2 = make_shared<RET>();
                    stk.emplace_back(2, nullptr, ret2, ret);
                    stk.emplace_back(1, node->children[i], nullptr, ret2.get());
                }
            } else {
                ret->first = max(ret->first, max(ret2->first, ret->second + ret2->second + 1));
                ret->second = max(ret->second, ret2->second + 1);
            }
        }
        return result;
    }
};

// Time:  O(n)
// Space: O(h)
class Solution2 {
public:
    int diameter(Node* root) {
        return dfs(root).first;
    }

private:
    pair<int, int> dfs(Node *node) {
        int max_dia = 0, max_depth = 0;
        for (const auto& child : node->children) {
            const auto& [child_max_dia, child_max_depth] = dfs(child);
            max_dia = max({max_dia, child_max_dia, max_depth + child_max_depth + 1});
            max_depth = max(max_depth, child_max_depth + 1);
        }
        return {max_dia, max_depth};
    }
};


## Find Users With Valid E-Mails  
Time:  O(n)
Space: O(n)

SELECT * 
FROM   users AS u 
WHERE  u.mail REGEXP '^[a-zA-Z][a-zA-Z0-9._-]*@leetcode.com$'; 

## Move Sub-Tree of N-Ary Tree
// Time:  O(n)
// Space: O(h)

/*
// Definition for a Node.
class Node {
public:
    int val;
    vector<Node*> children;
    Node() {}
    Node(int _val) {
        val = _val;
    }
    Node(int _val, vector<Node*> _children) {
        val = _val;
        children = _children;
    }
};
*/

// one pass solution without recursion
class Solution {
public:
    Node* moveSubTree(Node* root, Node* p, Node* q) {
        unordered_map<Node *, Node *> lookup;
        const auto& is_ancestor = iter_find_parents(root, nullptr, p, q, false, &lookup);
        if (lookup.count(p) && lookup[p] == q) {
            return root;
        }
        q->children.emplace_back(p);
        if (!is_ancestor) {
            lookup[p]->children.erase(find(begin(lookup[p]->children), end(lookup[p]->children), p));
        } else {
            lookup[q]->children.erase(find(begin(lookup[q]->children), end(lookup[q]->children), q));
            if (p == root) {
                root = q;
            } else {
                *find(begin(lookup[p]->children), end(lookup[p]->children), p) = q;
            }
        }
        return root;
    }

private:
    bool iter_find_parents(Node *node, Node *parent, Node *p, Node *q,
                           bool is_ancestor,
                           unordered_map<Node *, Node *> *lookup) {
        vector<tuple<int, Node *, Node *, bool, int>> stk = {tuple(1, node, parent, is_ancestor, -1)};
        while (!stk.empty()) {
            const auto [step, node, parent, is_ancestor, i] = stk.back(); stk.pop_back();
            if (step == 1) {
                if (node == p || node == q) {
                    (*lookup)[node] = parent;
                    if (lookup->size() == 2) {
                        return is_ancestor;
                    }
                }
                stk.emplace_back(2, node, parent, is_ancestor, node->children.size() - 1);
            } else {
                if (i < 0) {
                    continue;
                }
                stk.emplace_back(2, node, parent, is_ancestor, i - 1);
                stk.emplace_back(1, node->children[i], node, is_ancestor || node == p, -1);
            }
        }
        assert(false);
        return false;
    }
};

// Time:  O(n)
// Space: O(h)
// one pass solution with recursion
class Solution_Recu {
public:
    Node* moveSubTree(Node* root, Node* p, Node* q) {
        unordered_map<Node *, Node *> lookup;
        const auto& [_, is_ancestor] = find_parents(root, nullptr, p, q, false, &lookup);
        if (lookup.count(p) && lookup[p] == q) {
            return root;
        }
        q->children.emplace_back(p);
        if (!is_ancestor) {
            lookup[p]->children.erase(find(begin(lookup[p]->children), end(lookup[p]->children), p));
        } else {
            lookup[q]->children.erase(find(begin(lookup[q]->children), end(lookup[q]->children), q));
            if (p == root) {
                root = q;
            } else {
                *find(begin(lookup[p]->children), end(lookup[p]->children), p) = q;
            }
        }
        return root;
    }

private:
    pair<bool, bool> find_parents(Node *node, Node *parent, Node *p, Node *q,
                                  bool is_ancestor,
                                  unordered_map<Node *, Node *> *lookup) {
        if (node == p || node == q) {
            (*lookup)[node] = parent;
            if (lookup->size() == 2) {
                return {true, is_ancestor};
            }
        }
        for (const auto& child : node->children) {
            const auto& [found, result] = find_parents(child, node, p, q, is_ancestor || node == p, lookup);
            if (found) {
                return {true, result};
            }
        }
        return {false, false};
    }
};

// Time:  O(n)
// Space: O(h)
// two pass solution without recursion
class Solution2 {
public:
    Node* moveSubTree(Node* root, Node* p, Node* q) {
        unordered_map<Node *, Node *> lookup;
        iter_find_parents(root, nullptr, p, q, &lookup);
        if (lookup.count(p) && lookup[p] == q) {
            return root;
        }
        q->children.emplace_back(p);
        if (!iter_is_ancestor(p, q)) {
            lookup[p]->children.erase(find(begin(lookup[p]->children), end(lookup[p]->children), p));
        } else {
            lookup[q]->children.erase(find(begin(lookup[q]->children), end(lookup[q]->children), q));
            if (p == root) {
                root = q;
            } else {
                *find(begin(lookup[p]->children), end(lookup[p]->children), p) = q;
            }
        }
        return root;
    }

private:
    void iter_find_parents(Node *node, Node *parent, Node *p, Node *q,
                           unordered_map<Node *, Node *> *lookup) {
        vector<tuple<int, Node *, Node *, int>> stk = {tuple(1, node, parent, -1)};
        while (!stk.empty()) {
            const auto [step, node, parent, i] = stk.back(); stk.pop_back();
            if (step == 1) {
                if (node == p || node == q) {
                    (*lookup)[node] = parent;
                    if (lookup->size() == 2) {
                        return;
                    }
                }
                stk.emplace_back(2, node, parent, node->children.size() - 1);
            } else {
                if (i < 0) {
                    continue;
                }
                stk.emplace_back(2, node, parent, i - 1);
                stk.emplace_back(1, node->children[i], node, -1);
            }
        }
    }
    
    bool iter_is_ancestor(Node *node, Node *q) {
        vector<tuple<int, Node *, int>> stk = {tuple(1, node, -1)};
        while (!stk.empty()) {
            const auto [step, node, i] = stk.back(); stk.pop_back();
            if (step == 1) {
                stk.emplace_back(2, node, node->children.size() - 1);
            } else {
                if (i < 0) {
                    continue;
                }
                if (node->children[i] == q) {
                    return true;
                }
                stk.emplace_back(2, node, i - 1);
                stk.emplace_back(1, node->children[i], -1);
            }
        }
        return false;
    }
};

// Time:  O(n)
// Space: O(h)
// two pass solution with recursion
class Solution2_Recu {
public:
    Node* moveSubTree(Node* root, Node* p, Node* q) {
        unordered_map<Node *, Node *> lookup;
        find_parents(root, nullptr, p, q, &lookup);
        if (lookup.count(p) && lookup[p] == q) {
            return root;
        }
        q->children.emplace_back(p);
        if (!is_ancestor(p, q)) {
            lookup[p]->children.erase(find(begin(lookup[p]->children), end(lookup[p]->children), p));
        } else {
            lookup[q]->children.erase(find(begin(lookup[q]->children), end(lookup[q]->children), q));
            if (p == root) {
                root = q;
            } else {
                *find(begin(lookup[p]->children), end(lookup[p]->children), p) = q;
            }
        }
        return root;
    }

private:
    bool find_parents(Node *node, Node *parent, Node *p, Node *q, unordered_map<Node *, Node *> *lookup) {
        if (node == p || node == q) {
            (*lookup)[node] = parent;
            if (lookup->size() == 2) {
                return true;
            }
        }
        for (const auto& child : node->children) {
            if (find_parents(child, node, p, q, lookup)) {
                return true;
            }
        }
        return false;
    }

    bool is_ancestor(Node *node, Node *q) {
        for (const auto& child : node->children) {
            if (child == q || is_ancestor(child, q)) {
                return true;
            }
        }
        return false;
    }
};


## Customer Order Frequency
Time:  O(n)
Space: O(n)

SELECT a.customer_id,
       a.name
FROM Customers AS a
INNER JOIN
  (SELECT *
   FROM Orders
   WHERE order_date BETWEEN "2020-06-01" AND "2020-07-31" ) AS b
ON a.customer_id = b.customer_id
INNER JOIN Product AS c
ON b.product_id = c.product_id
GROUP BY a.customer_id
HAVING SUM(CASE
               WHEN LEFT(b.order_date, 7) = "2020-06" THEN c.price * b.quantity
               ELSE 0
           END) >= 100
AND    SUM(CASE
               WHEN LEFT(b.order_date, 7) = "2020-07" THEN c.price * b.quantity
               ELSE 0
           END) >= 100
ORDER BY NULL;


## Find Root of N-Ary Tree
// Time:  O(n)
// Space: O(1)

class Solution {
public:
    Node* findRoot(vector<Node*> tree) {
        uint64_t root = 0;
        for (const auto& node : tree) {
            root ^= reinterpret_cast<uint64_t>(node);
            for (const auto& child : node->children) {
                root ^= reinterpret_cast<uint64_t>(child);
            }
        }
        return reinterpret_cast<Node *>(root);
    }
};


## Countries You Can Safely Invest In
Time:  O(n)
Space: O(n)

SELECT co.name AS country
FROM person p
INNER JOIN country co ON SUBSTRING(phone_number, 1, 3) = country_code
INNER JOIN calls c ON (p.id = c.caller_id OR p.id = c.callee_id)
GROUP BY co.name
HAVING AVG(duration) > (SELECT AVG(duration) as avg_duration FROM calls)
ORDER BY NULL;

## Design a File Sharing System
// Time:  ctor:    O(1)
//        join:    O(logu + c), u is the number of total joined users
//        leave:   O(logu + c), c is the number of chunks
//        request: O(u)
// Space: O(u * c)

// "u ~= n" solution, n is the average number of users who own the chunk
class FileSharing {
public:
    FileSharing(int m) {
        
    }
    
    int join(vector<int> ownedChunks) {
        int userID = users_.size() + 1;
        if (!min_heap_.empty()) {
            userID = min_heap_.top();
            min_heap_.pop();
        } else {
            users_.emplace_back();
        }
        for (const auto& chunk : ownedChunks) {
            users_[userID - 1].emplace(chunk);
        }
        lookup_.emplace(userID);
        return userID;
    }
    
    void leave(int userID) {
        if (!lookup_.count(userID)) {
            return;
        }
        lookup_.erase(userID);
        users_[userID - 1].clear();
        min_heap_.emplace(userID);
    }
    
    vector<int> request(int userID, int chunkID) {
        vector<int> result;
        for (int i = 0; i < users_.size(); ++i) {
            if (users_[i].count(chunkID)) {
                result.emplace_back(i + 1);
            }
        }
        if (!result.empty()) {
            users_[userID - 1].emplace(chunkID);
        }
        return result;
    }

private:
    vector<unordered_set<int>> users_;
    unordered_set<int> lookup_;
    priority_queue<int, vector<int>, greater<int>> min_heap_;
};

// Time:  ctor:    O(1)
//        join:    O(logu + c), u is the number of total joined users
//        leave:   O(logu + c), c is the number of chunks
//        request: O(nlogn)   , n is the average number of users who own the chunk
// Space: O(u * c + m)
// "u >> n" solution
class FileSharing2 {
public:
    FileSharing2(int m) {
        
    }
    
    int join(vector<int> ownedChunks) {
        int userID = users_.size() + 1;
        if (!min_heap_.empty()) {
            userID = min_heap_.top();
            min_heap_.pop();
        } else {
            users_.emplace_back();
        }
        for (const auto& chunk : ownedChunks) {
            users_[userID - 1].emplace(chunk);
            chunks_[chunk].emplace(userID);
        }
        lookup_.emplace(userID);
        return userID;
    }
    
    void leave(int userID) {
        if (!lookup_.count(userID)) {
            return;
        }
        lookup_.erase(userID);
        for (const auto& chunk : users_[userID - 1]) {
            chunks_[chunk].erase(userID);
        }
        users_[userID - 1].clear();
        min_heap_.emplace(userID);
    }
    
    vector<int> request(int userID, int chunkID) {
        vector<int> result(cbegin(chunks_[chunkID]), cend(chunks_[chunkID]));
        sort(begin(result), end(result));
        if (!result.empty()) {
            users_[userID - 1].emplace(chunkID);
            chunks_[chunkID].emplace(userID);
        }
        return result;
    }

private:
    vector<unordered_set<int>> users_;
    unordered_set<int> lookup_;
    unordered_map<int, unordered_set<int>> chunks_;
    priority_queue<int, vector<int>, greater<int>> min_heap_;
};

## Friendly Movies Streamed Last Month
Time:  O(n)
Space: O(n)

SELECT DISTINCT title
FROM content ctt
INNER JOIN TVProgram tv
ON ctt.content_id = tv.content_id
WHERE content_type = 'Movies'
AND Kids_content = 'Y'
AND program_date BETWEEN '2020-06-01' AND '2020-06-30';

## Clone N-ary Tree
// Time:  O(n)
// Space: O(h)

class Solution {
public:
    Node* cloneTree(Node* root) {
        using RET = Node*;
        RET result{};
        vector<tuple<int, RET, shared_ptr<RET>, RET *>> stk = {{1, root, nullptr, &result}};
        while (!stk.empty()) {
            const auto [step, node, ret1, ret] = stk.back(); stk.pop_back();
            if (step == 1) {
                if (!node) {
                    continue;
                }
                *ret = new Node(node->val);
                for (int i = node->children.size() - 1; i >= 0; --i) {
                    const auto& ret1 = make_shared<RET>();
                    stk.emplace_back(2, nullptr, ret1, ret);
                    stk.emplace_back(1, node->children[i], nullptr, ret1.get());
                }
            } else {
                (*ret)->children.emplace_back(*ret1);
            }
        }
        return result;
    }
};

// Time:  O(n)
// Space: O(h)
class Solution2 {
public:
    Node* cloneTree(Node* root) {
        return dfs(root);
    }

private:
    Node *dfs(Node *node) {
        if (!node) {
            return nullptr;
        }
        auto copy = new Node(node->val);
        for (const auto& child : node->children) {
            copy->children.emplace_back(dfs(child));
        }
        return copy;
    }
};

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
