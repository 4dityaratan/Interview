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
    // Time:  O(n)
// Space: O(h)

/**
 * Definition for a Node.
 * struct Node {
 *     int val;
 *     Node *left;
 *     Node *right;
 *     Node *random;
 *     Node() : val(0), left(nullptr), right(nullptr), random(nullptr) {}
 *     Node(int x) : val(x), left(nullptr), right(nullptr), random(nullptr) {}
 *     Node(int x, Node *left, Node *right, Node *random) : val(x), left(left), right(right), random(random) {}
 * };
 */

class Solution {
public:
    NodeCopy* copyRandomBinaryTree(Node* root) {    
        const auto& merge = [](Node *node) {
            auto copy = new NodeCopy(node->val);
            tie(node->left, copy->left) = pair(reinterpret_cast<Node*>(copy), 
                                               reinterpret_cast<NodeCopy*>(node->left));
            return pair{reinterpret_cast<Node*>(copy->left), copy};
        };
        const auto& clone = [](Node *node) {
            auto copy = reinterpret_cast<NodeCopy*>(node->left);
            node->left->random = node->random ? node->random->left : nullptr;
            node->left->right = node->right ? node->right->left : nullptr;
            return pair{reinterpret_cast<Node*>(copy->left), copy};
        };
        const auto& split = [](Node *node) {
            auto copy = reinterpret_cast<NodeCopy*>(node->left);
            tie(node->left, copy->left) = pair(copy->left ? reinterpret_cast<Node*>(copy->left) : nullptr,
                                               copy->left ? copy->left->left : nullptr);
            return pair{reinterpret_cast<Node*>(node->left), copy};
        };
        iter_dfs(root, merge);
        iter_dfs(root, clone);
        return iter_dfs(root, split);
    }

private:
    NodeCopy *iter_dfs(Node *root, const function<pair<Node*, NodeCopy*>(Node*)>& callback) {
        NodeCopy *result = nullptr;
        vector<Node *> stk = {root};
        while (!stk.empty()) {
            auto node = stk.back(); stk.pop_back();
            if (!node) {
                continue;
            }
            const auto& [left_node, copy] = callback(node);
            if (!result) {
                result = copy;
            }
            stk.emplace_back(node->right);
            stk.emplace_back(left_node);
        }
        return result;
    }
};

// Time:  O(n)
// Space: O(h)
class Solution_Recu {
public:
    NodeCopy* copyRandomBinaryTree(Node* root) {    
        const auto& merge = [](Node *node) {
            auto copy = new NodeCopy(node->val);
            tie(node->left, copy->left) = pair(reinterpret_cast<Node*>(copy), 
                                               reinterpret_cast<NodeCopy*>(node->left));
            return pair{reinterpret_cast<Node*>(copy->left), copy};
        };
        const auto& clone = [](Node *node) {
            auto copy = reinterpret_cast<NodeCopy*>(node->left);
            node->left->random = node->random ? node->random->left : nullptr;
            node->left->right = node->right ? node->right->left : nullptr;
            return pair{reinterpret_cast<Node*>(copy->left), copy};
        };
        const auto& split = [](Node *node) {
            auto copy = reinterpret_cast<NodeCopy*>(node->left);
            tie(node->left, copy->left) = pair(copy->left ? reinterpret_cast<Node*>(copy->left) : nullptr,
                                               copy->left ? copy->left->left : nullptr);
            return pair{reinterpret_cast<Node*>(node->left), copy};
        };
        dfs(root, merge);
        dfs(root, clone);
        return dfs(root, split);
    }

private:
    NodeCopy *dfs(Node *node, const function<pair<Node*, NodeCopy*>(Node*)>& callback) {
        if (!node) {
            return nullptr;
        }
        const auto& [left_node, copy] = callback(node);
        dfs(left_node, callback);
        dfs(node->right, callback);
        return copy;
    }
};

// Time:  O(n)
// Space: O(n)
class Solution2 {
public:
    NodeCopy* copyRandomBinaryTree(Node* root) {
        unordered_map<Node*, NodeCopy*> lookup;
        lookup[nullptr] = nullptr;
        vector<Node *> stk = {root};
        while (!stk.empty()) {
            auto node = stk.back(); stk.pop_back();
            if (!node) {
                continue;
            }
            if (!lookup.count(node)) {
                lookup[node] = new NodeCopy(); 
            }
            if (!lookup.count(node->left)) {
                lookup[node->left] = new NodeCopy(); 
            }
            if (!lookup.count(node->right)) {
                lookup[node->right] = new NodeCopy(); 
            }
            if (!lookup.count(node->random)) {
                lookup[node->random] = new NodeCopy(); 
            }
            lookup[node]->val = node->val;
            lookup[node]->left = lookup[node->left];
            lookup[node]->right = lookup[node->right];
            lookup[node]->random = lookup[node->random];
            stk.emplace_back(node->right);
            stk.emplace_back(node->left);
        }
        return lookup[root];
    }
};

// Time:  O(n)
// Space: O(n)
class Solution2_Recu {
public:
    NodeCopy* copyRandomBinaryTree(Node* root) {
        unordered_map<Node*, NodeCopy*> lookup;
        lookup[nullptr] = nullptr;
        dfs(root, &lookup);
        return lookup[root];
    }

private:
    void dfs(Node *node, unordered_map<Node*, NodeCopy*> *lookup) {
        if (!node) {
            return;
        }
        if (!lookup->count(node)) {
            (*lookup)[node] = new NodeCopy(); 
        }
        if (!lookup->count(node->left)) {
            (*lookup)[node->left] = new NodeCopy(); 
        }
        if (!lookup->count(node->right)) {
            (*lookup)[node->right] = new NodeCopy(); 
        }
        if (!lookup->count(node->random)) {
            (*lookup)[node->random] = new NodeCopy(); 
        }
        (*lookup)[node]->val = node->val;
        (*lookup)[node]->left = (*lookup)[node->left];
        (*lookup)[node]->right = (*lookup)[node->right];
        (*lookup)[node]->random = (*lookup)[node->random];
        dfs(node->left, lookup);
        dfs(node->right, lookup);
    }
};
## Group Sold Products By The Date
Time:  O(nlogn)
Space: O(n)

SELECT sell_date,
       COUNT(DISTINCT(product)) AS num_sold, 
       GROUP_CONCAT(DISTINCT product ORDER BY product ASC SEPARATOR ',') AS products
FROM Activities
GROUP BY sell_date
ORDER BY sell_date ASC;

## Sales by Day of the Week
Time:  O(m + n)
Space: O(n)

SELECT a.item_category AS 'CATEGORY',
       sum(CASE
               WHEN weekday(b.order_date) = 0 THEN b.quantity
               ELSE 0
           END) AS 'MONDAY',
       sum(CASE
               WHEN weekday(b.order_date) = 1 THEN b.quantity
               ELSE 0
           END) AS 'TUESDAY',
       sum(CASE
               WHEN weekday(b.order_date) = 2 THEN b.quantity
               ELSE 0
           END) AS 'WEDNESDAY',
       sum(CASE
               WHEN weekday(b.order_date) = 3 THEN b.quantity
               ELSE 0
           END) AS 'THURSDAY',
       sum(CASE
               WHEN weekday(b.order_date) = 4 THEN b.quantity
               ELSE 0
           END) AS 'FRIDAY',
       sum(CASE
               WHEN weekday(b.order_date) = 5 THEN b.quantity
               ELSE 0
           END) AS 'SATURDAY',
       sum(CASE
               WHEN weekday(b.order_date) = 6 THEN b.quantity
               ELSE 0
           END) AS 'SUNDAY'
FROM items a
LEFT JOIN orders b ON a.item_id = b.item_id
GROUP BY a.item_category
ORDER BY a.item_category;

## Delete N Nodes After M Nodes of a Linked List
        // Time:  O(n)
// Space: O(1)

/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* deleteNodes(ListNode* head, int m, int n) {
        ListNode dummy;
        dummy.next = head;
        head = &dummy;
        while (head) {
            for (int i = 0; i < m; ++i) {
                if (!head->next) {
                    return dummy.next;
                }
                head = head->next;
            }
            auto prev = head;
            for (int i = 0; i < n; ++i) {
                if (!head->next) {
                    prev->next = nullptr;
                    return dummy.next;
                }
                head = head->next;
            }
            prev->next = head->next;
        }
        return dummy.next;
    }
};
## Find All The Lonely Nodes
// Time:  O(n)
// Space: O(h)

/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    vector<int> getLonelyNodes(TreeNode* root) {
        vector<int> result;
        vector<TreeNode *> stk = {root};
        while (!stk.empty()) {
            auto node = stk.back(); stk.pop_back();
            if (!node) {
                continue;
            }
            if (node->left && !node->right) {
                result.emplace_back(node->left->val);
            } else if (node->right && !node->left) {
                result.emplace_back(node->right->val);
            }
            stk.emplace_back(node->right);
            stk.emplace_back(node->left);
        }
        return result;
    }
};

// Time:  O(n)
// Space: O(h)
class Solution2 {
public:
    vector<int> getLonelyNodes(TreeNode* root) {
        vector<int> result;
        dfs(root, &result);
        return result;
    }

private:
    void dfs(TreeNode *node, vector<int> *result) {
        if (!node) {
            return;
        }
        if (node->left && !node->right) {
            result->emplace_back(node->left->val);
        } else if (node->right && !node->left) {
            result->emplace_back(node->right->val);
        }
        dfs(node->left, result);
        dfs(node->right, result);
    }
};
## Calculate Salaries
Time:  O(m + n)
Space: O(m + n)

SELECT s.company_id,
       s.employee_id,
       s.employee_name,
       ROUND(s.salary * t.rate) salary
FROM Salaries s
INNER JOIN
  (SELECT company_id,
          CASE
              WHEN MAX(salary) < 1000 THEN 1.0
              WHEN MAX(salary) <= 10000 THEN 0.76
              ELSE 0.51
          END AS rate
   FROM Salaries
   GROUP BY company_id
   ORDER BY NULL) t ON s.company_id = t.company_id;
## Rectangles Area
Time:  O(n^2)
Space: O(n^2)

SELECT *
FROM
  (SELECT a.id AS P1,
          b.id AS P2,
          abs(a.x_value - b.x_value) * abs(a.y_value - b.y_value) AS area
   FROM Points a
   INNER JOIN Points b ON a.id < b.id
   ORDER BY area DESC, P1, P2) r
WHERE area > 0;
## Active Users
Time:  O(nlogn)
Space: O(n)

SELECT DISTINCT 
       r.id,
       r.name
FROM
  (SELECT a_l.id,
          a_l.name,
          @accu := CASE
                       WHEN a_l.name = @prev AND 
                            DATEDIFF(a_l.login_date, @login_date) = 1
                       THEN @accu + 1
                       ELSE 1
                   END AS accu,
          @prev := a_l.name AS prev,
          @login_date := a_l.login_date AS login_date
   FROM (
           (SELECT DISTINCT
                   a.id,
                   a.name,
                   l.login_date
            FROM accounts a
            LEFT JOIN logins l
            ON a.id = l.id
            ORDER BY a.id,
                     a.name,
                     l.login_date) a_l,
           (SELECT @accu := 0,
                   @prev := "",
                   @login_date := "") init
        )
  ) r
WHERE r.accu = 5;
## Apples & Oranges
Time:  O(n)
Space: O(n)

SELECT a.sale_date,
       a.sold_num - b.sold_num AS diff
FROM
  (SELECT sale_date,
          sold_num
   FROM sales
   WHERE fruit = "apples") a
INNER JOIN
  (SELECT sale_date,
          sold_num
   FROM sales
   WHERE fruit = "oranges") b
ON a.sale_date = b.sale_date;

## Evaluate Boolean Expression
    Time:  O(n)
    Space: O(n)

    SELECT left_operand, operator, right_operand,
           CASE WHEN operator = '>' AND v1.value > v2.value THEN 'true'
                WHEN operator = '<' AND v1.value < v2.value THEN 'true'
                WHEN operator = '=' AND v1.value = v2.value THEN 'true'
                ELSE 'false' END AS value
    FROM expressions e
    LEFT JOIN variables v1
    ON e.left_operand = v1.name
    LEFT JOIN variables v2
    ON e.right_operand = v2.name;
    ## Create a Session Bar Chart
    # Time:  O(n)
    # Space: O(1)

    SELECT '[0-5>'  AS bin, 
           Count(1) AS total 
    FROM   sessions 
    WHERE  duration >= 0 
           AND duration < 300 
    UNION ALL
    SELECT '[5-10>' AS bin, 
           Count(1) AS total 
    FROM   sessions 
    WHERE  duration >= 300 
           AND duration < 600 
    UNION ALL 
    SELECT '[10-15>' AS bin, 
           Count(1)  AS total 
    FROM   sessions 
    WHERE  duration >= 600 
           AND duration < 900 
    UNION ALL 
    SELECT '15 or more' AS bin, 
           Count(1)     AS total 
    FROM   sessions 
    WHERE  duration >= 900;

    # Time:  O(n)
    # Space: O(n)
    SELECT
        t2.BIN,
        COUNT(t1.BIN) AS TOTAL
    FROM (
    SELECT
        CASE 
            WHEN duration/60 BETWEEN 0 AND 5 THEN "[0-5>"
            WHEN duration/60 BETWEEN 5 AND 10 THEN "[5-10>"
            WHEN duration/60 BETWEEN 10 AND 15 THEN "[10-15>"
            WHEN duration/60 >= 15 THEN "15 or more" 
            ELSE NULL END AS BIN
    FROM Sessions
    ) t1 
    RIGHT JOIN(
        SELECT "[0-5>"        AS BIN
        UNION ALL
        SELECT "[5-10>"       AS BIN
        UNION ALL
        SELECT "[10-15>"      AS BIN
        UNION ALL
        SELECT "15 or more"   AS BIN
    ) t2
    ON t1.bin = t2.bin
    GROUP BY t2.bin
    ORDER BY NULL;
## NPV Queries
    # Time:  O(n)
    # Space: O(n)

    SELECT a.id, 
           a.year, 
           Ifnull(b.npv, 0) AS npv 
    FROM   queries a 
           LEFT JOIN npv b 
                  ON a.id = b.id 
                     AND a.year = b.year;
## Find the Quiet Students in All Exams
    # Time:  O(m + nlogn)
    # Space: O(m + n)

    SELECT s.student_id, 
           s.student_name 
    FROM   student s 
           INNER JOIN (SELECT a.student_id, 
                              Count(a.exam_id) AS total_exam, 
                              Sum(CASE 
                                    WHEN a.score > min_score 
                                         AND a.score < max_score THEN 1 
                                    ELSE 0 
                                  END)         AS quite_exam 
                       FROM   exam a 
                              INNER JOIN (SELECT exam_id, 
                                                 Min(score) AS min_score, 
                                                 Max(score) AS max_score 
                                          FROM   exam 
                                          GROUP  BY exam_id 
                                          ORDER  BY NULL) b 
                                      ON a.exam_id = b.exam_id 
                       GROUP  BY a.student_id 
                       ORDER  BY NULL) c 
                   ON s.student_id = c.student_id 
    WHERE  c.total_exam = c.quite_exam 
    ORDER  BY s.student_id; 
## Top Travellers
    # Time:  O(m + nlogn) 
    # Space: O(m + n) 

    SELECT name, 
           Ifnull(Sum(distance), 0) AS travelled_distance 
    FROM   users 
           LEFT JOIN rides 
                  ON users.id = rides.user_id 
    GROUP  BY name 
    ORDER  BY travelled_distance DESC, 
              name;
## Customers Who Bought Products A and B but Not C
    # Time:  O(m + n)
    # Space: O(m + n)

    SELECT c.customer_id, 
           c.customer_name 
    FROM   customers AS c 
           INNER JOIN (SELECT customer_id, 
                              SUM(CASE 
                                    WHEN product_name = 'A' THEN 1 
                                    WHEN product_name = 'B' THEN 1 
                                    WHEN product_name = 'C' THEN -1 
                                    ELSE 0 
                                  END) AS total 
                       FROM   (SELECT DISTINCT customer_id, 
                                               product_name 
                               FROM   orders) t 
                       GROUP  BY customer_id 
                       HAVING total = 2) AS o 
                   ON c.customer_id = o.customer_id;
## Capital Gain/Loss
    # Time:  O(n)
    # Space: O(n)

    SELECT stock_name, 
           Sum(CASE 
                 WHEN operation = 'Buy' THEN -price 
                 ELSE price 
               END) AS capital_gain_loss 
    FROM   stocks 
    GROUP  BY stock_name 
    ORDER  BY NULL;
## Total Sales Amount by Year
    # Time:  O(nlogn)
    # Space: O(n)

    SELECT product_id, 
           product_name, 
           report_year, 
           (DATEDIFF( 
               CASE WHEN YEAR(period_end)   > report_year THEN CONCAT(report_year, '-12-31') ELSE period_end   END,
               CASE WHEN YEAR(period_start) < report_year THEN CONCAT(report_year, '-01-01') ELSE period_start END
            ) + 1) * average_daily_sales AS total_amount
    FROM   (SELECT s.product_id,
                   product_name,
                   period_start,
                   period_end,
                   average_daily_sales
            FROM  sales s
            INNER JOIN product p
            ON s.product_id = p.product_id
           ) AS r,
           (SELECT "2018" AS report_year 
            UNION ALL 
            SELECT "2019" 
            UNION ALL 
            SELECT "2020"
           ) AS y
    WHERE  YEAR(period_start) <= report_year AND 
           YEAR(period_end)   >= report_year
    GROUP  BY product_id,
              report_year
    ORDER  BY product_id,
              report_year;


    # Time:  O(nlogn)
    # Space: O(n)
    SELECT r.product_id, 
           product_name, 
           report_year, 
           total_amount 
    FROM   ((SELECT product_id, 
                    '2018'                     AS report_year, 
                    days * average_daily_sales AS total_amount 
             FROM   (SELECT product_id, 
                            average_daily_sales, 
                            DATEDIFF(
                                 CASE WHEN period_end   > '2018-12-31' THEN '2018-12-31' ELSE period_end  END,
                                 CASE WHEN period_start < '2018-01-01' THEN '2018-01-01' ELSE period_start END
                            ) + 1 AS days 
                     FROM   sales s) tmp 
             WHERE  days > 0) 
            UNION ALL
            (SELECT product_id, 
                    '2019'                     AS report_year, 
                    days * average_daily_sales AS total_amount 
             FROM   (SELECT product_id, 
                            average_daily_sales, 
                            DATEDIFF(
                                 CASE WHEN period_end   > '2019-12-31' THEN '2019-12-31' ELSE period_end  END,
                                 CASE WHEN period_start < '2019-01-01' THEN '2019-01-01' ELSE period_start END
                            ) + 1 AS days 
                     FROM   sales s) tmp 
             WHERE  days > 0) 
            UNION ALL
            (SELECT product_id, 
                    '2020'                     AS report_year, 
                    days * average_daily_sales AS total_amount 
             FROM   (SELECT product_id, 
                            average_daily_sales, 
                            DATEDIFF(
                                 CASE WHEN period_end   > '2020-12-31' THEN '2020-12-31' ELSE period_end END,
                                 CASE WHEN period_start < '2020-01-01' THEN '2020-01-01' ELSE period_start END
                            ) + 1 AS days 
                     FROM   sales s) tmp 
             WHERE  days > 0)
           ) r
           INNER JOIN product p
          ON r.product_id = p.product_id
    ORDER  BY r.product_id, 
              report_year ;
    

## Replace Employee ID With The Unique Identifier
    # Time:  O(n)
    # Space: O(n)

    SELECT u.unique_id, e.name
    FROM employees e
    LEFT JOIN employeeuni u
    ON e.id = u.id
    
## Get the Second Most Recent Activity
    # Time:  O(nlogn)
    # Space: O(n)

    SELECT *
    FROM UserActivity
    GROUP BY username
    HAVING COUNT(1) = 1

    UNION ALL

    SELECT a.username,
           a.activity,
           a.startDate,
           a.endDate
    FROM
      (SELECT @accu := (CASE
                            WHEN username = @prev THEN @accu + 1
                            ELSE 1
                        END) AS n,
              @prev := username AS username,
              activity,
              startDate,
              endDate
       FROM
            (SELECT @accu := 0, @prev := 0) AS init,
            UserActivity AS u
       ORDER BY username,
                endDate DESC) AS a
    WHERE n = 2;
## Number of Trusted Contacts of a Customer
    # Time:  O(n + m + l + nlogn), n, m, l are the sizes of invoices, customers,        contacts
    # Space: O(n + m + l)

    SELECT i.invoice_id, 
           c.customer_name, 
           i.price, 
           Ifnull(tmp.c_cnt, 0) AS contacts_cnt, 
           Ifnull(tmp.t_cnt, 0) AS trusted_contacts_cnt 
    FROM   invoices i 
           LEFT JOIN (SELECT co.user_id, 
                             Count(co.user_id)       AS c_cnt, 
                             Count(cu.customer_name) AS t_cnt 
                      FROM   contacts co 
                             LEFT JOIN customers cu 
                                    ON co.contact_name = cu.customer_name 
                                       AND co.contact_email = cu.email 
                      GROUP  BY co.user_id 
                      ORDER  BY NULL) tmp 
                  ON i.user_id = tmp.user_id 
           LEFT JOIN customers c 
                  ON i.user_id = c.customer_id 
    ORDER  BY i.invoice_id;
  
## Activity Participants

    # Time:  O(n) 
    # Space: O(n) 

    SELECT activity 
    FROM   friends 
    GROUP  BY activity 
    HAVING Count(1) NOT IN (SELECT Max(counts) 
                            FROM   (SELECT Count(1) AS counts 
                                    FROM   friends 
                                    GROUP  BY activity 
                                    ORDER  BY NULL) a 
                            UNION ALL 
                            SELECT Min(counts) 
                            FROM   (SELECT Count(1) AS counts 
                                    FROM   friends 
                                    GROUP  BY activity 
                                    ORDER  BY NULL) b) 
    ORDER  BY NULL;
## Students With Invalid Departments
    # Time:  O(n) 
    # Space: O(n) 
    SELECT s.id, 
           s.name 
    FROM   students s 
           LEFT JOIN departments d 
                  ON d.id = s.department_id 
    WHERE  d.id IS NULL; 

    # Time:  O(n) 
    # Space: O(n) 
    SELECT s.id, 
           s.name 
    FROM   students s
    WHERE  NOT EXISTS (SELECT id 
                       FROM   departments d
                       WHERE  d.id = s.department_id); 
                       
## Movie Rating
    # Time:  O(nlogn), n is the number of movie_rating
    # Space: O(n)

    (SELECT b.name AS results 
     FROM   (SELECT a.user_id, 
                    Count(*) AS cnt 
             FROM   movie_rating a 
             GROUP  BY a.user_id) a 
            INNER JOIN users b 
                   ON a.user_id = b.user_id 
     ORDER  BY a.cnt DESC, 
               b.name ASC 
     LIMIT  1) 
    UNION ALL 
    (SELECT b.movie_name 
     FROM   (SELECT a.movie_name, 
                    Avg(a.rating) AS max_rating 
             FROM   (SELECT a.rating,
                            b.title AS movie_name 
                     FROM   movie_rating a 
                            INNER JOIN movies b 
                                    ON a.movie_id = b.movie_id 
                     WHERE  a.created_at BETWEEN '2020-02-01' AND '2020-02-29') a 
             GROUP  BY a.movie_name) b 
     ORDER  BY b.max_rating DESC, 
               b.movie_name ASC 
     LIMIT  1); 
## Number of Transactions per Visit
    # Time:  O(m + n)  
    # Space: O(m + n)  

    SELECT seq.transactions_count AS transactions_count,
           Ifnull(v.visits_count, 0) AS visits_count 
    FROM   (SELECT @count := CAST(@count + 1 AS SIGNED) AS transactions_count 
            FROM   (SELECT @count := -1, @max_count := Ifnull(Max(count), 0)
                    FROM   (SELECT Count(1) AS count 
                            FROM   transactions 
                            GROUP  BY user_id, 
                                      transaction_date 
                            ORDER  BY NULL) AS tmp) AS c
                   CROSS JOIN (SELECT user_id 
                               FROM   visits 
                               UNION ALL 
                               SELECT user_id 
                               FROM   transactions) AS m_n
            WHERE  @count < @max_count
           ) AS seq
           LEFT JOIN (SELECT transactions_count, 
                             Count(1) AS visits_Count 
                      FROM   (SELECT Count(transaction_date) AS transactions_count 
                              FROM   visits AS v 
                                     LEFT JOIN transactions AS t 
                                            ON v.user_id = t.user_id 
                                               AND visit_date = transaction_date 
                              GROUP  BY v.user_id, 
                                        visit_date 
                              ORDER  BY NULL) AS visits_count 
                      GROUP  BY transactions_count) AS v 
                  ON seq.transactions_count = v.transactions_count 
## List the Products Ordered in a Period
    # Time:  O(n)  
    # Space: O(n)  

    SELECT p.product_name, 
           o.unit 
    FROM   (SELECT product_id, 
                   Sum(unit) AS unit 
            FROM   orders 
            WHERE  order_date BETWEEN '2020-02-01' AND '2020-02-29' 
            GROUP  BY product_id 
            HAVING unit >= 100) o 
           INNER JOIN products p 
                   ON o.product_id = p.product_id 
## Ads Performance
    # Time:  O(nlogn)
    # Space: O(n)

    SELECT ad_id,
           CASE
               WHEN clicks + views = 0 THEN 0
               ELSE ROUND(100 * clicks / (clicks + views), 2)
           END ctr
    FROM
      (SELECT ad_id,
              SUM(CASE
                      WHEN action ='Viewed' THEN 1
                      ELSE 0
                  END) views,
              SUM(CASE
                      WHEN action = 'Clicked' THEN 1
                      ELSE 0
                  END) clicks
       FROM Ads
       GROUP BY ad_id) a
    ORDER BY ctr DESC,
             ad_id ASC
## Restaurant Growth
    # Time:  O(n^2)
    # Space: O(n)

    SELECT a.visited_on             AS visited_on, 
           Sum(b.day_sum)           AS amount, 
           Round(Avg(b.day_sum), 2) AS average_amount 
    FROM   (SELECT visited_on, 
                   Sum(amount) AS day_sum 
            FROM   customer 
            GROUP  BY visited_on) a 
           LEFT JOIN (SELECT visited_on, 
                             Sum(amount) AS day_sum 
                      FROM   customer 
                      GROUP  BY visited_on) b 
                  ON Datediff(a.visited_on, b.visited_on) BETWEEN 0 AND 6 
    GROUP  BY a.visited_on 
    HAVING Count(b.visited_on) = 7 
## Running Total for Different Genders
    # Time:  O(nlogn)
    # Space: O(n)

    SELECT gender, 
           day, 
           CASE 
             WHEN gender = 'F' THEN @f_accu := @f_accu + score_points 
             ELSE @m_accu := @m_accu + score_points 
           END total 
    FROM   scores, 
           (SELECT @f_accu := 0, 
                   @m_accu := 0) init 
    ORDER  BY gender, 
              day 
## Find the Team Size
## Check If a String Is a Valid Sequence from Root to Leaves Path in a Binary Tree
    // Time:  O(n)
    // Space: O(w)

    /**
     * Definition for a binary tree node.
     * struct TreeNode {
     *     int val;
     *     TreeNode *left;
     *     TreeNode *right;
     *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
     *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
     *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
     * };
     */
    // bfs solution
    class Solution {
    public:
        bool isValidSequence(TreeNode* root, vector<int>& arr) {
            vector<TreeNode *> q = {root};
            for (int depth = 0; depth < arr.size(); ++depth) {
                vector<TreeNode *> new_q;
                while (!q.empty()) {
                    const auto node = q.back(); q.pop_back();
                    if (!node || node->val != arr[depth]) {
                        continue;
                    }
                    if (depth + 1 == arr.size() && node->left == node->right) {
                        return true;
                    }
                    new_q.emplace_back(node->left);
                    new_q.emplace_back(node->right);            
                }
                q = move(new_q);
            }
            return false;
        }
    };

    // Time:  O(n)
    // Space: O(h)
    // dfs solution with stack
    class Solution2 {
    public:
        bool isValidSequence(TreeNode* root, vector<int>& arr) {
            vector<pair<TreeNode *, int>> s = {{root, 0}};
            while (!s.empty()) {
                const auto [node, depth] = s.back(); s.pop_back();
                if (!node || depth == arr.size() || node->val != arr[depth]) {
                    continue;
                }
                if (depth + 1 == arr.size() && node->left == node->right) {
                    return true;
                }
                s.emplace_back(node->right, depth + 1);   
                s.emplace_back(node->left, depth + 1);
            }
            return false;
        }
    };

    // Time:  O(n)
    // Space: O(h)
    // dfs solution with recursion
    class Solution3 {
    public:
        bool isValidSequence(TreeNode* root, vector<int>& arr) {
            return dfs(root, arr, 0);
        }

    private:
        bool dfs(TreeNode *node, const vector<int>& arr, int depth) {
            if (!node || depth == arr.size() || node->val != arr[depth]) {
                return false;
            }
            if (depth + 1 == arr.size() && node->left == node->right) {
                return true;
            }
            return dfs(node->left, arr, depth + 1) || dfs(node->right, arr, depth + 1);
        }
    };
## Weather Type in Each Country
    # Time:  O(m + n)
    # Space: O(n)

    SELECT c.country_name,
           CASE
               WHEN AVG(w.weather_state * 1.0) <= 15.0 THEN 'Cold'
               WHEN AVG(w.weather_state * 1.0) >= 25.0 THEN 'Hot'
               ELSE 'Warm'
           END AS weather_type
    FROM Countries AS c
    INNER JOIN Weather AS w ON c.country_id = w.country_id
    WHERE w.day BETWEEN '2019-11-01' AND '2019-11-30'
    GROUP BY c.country_id;

## Find the Start and End Number of Continuous Ranges
    # Time:  O(n)
    # Space: O(n)

    SELECT Min(log_id) AS start_id, 
           Max(log_id) AS end_id 
    FROM   (SELECT log_id, 
                   @rank := CASE 
                              WHEN @prev = log_id - 1 THEN @rank 
                              ELSE @rank + 1 
                            end    AS rank, 
                   @prev := log_id AS prev
            FROM   logs AS A, 
                   (SELECT @rank := 0, 
                           @prev :=- 1) AS B) C 
    GROUP  BY C.rank 
    ORDER  BY NULL;
## Students and Examinations
    # Time:  O((m * n) * log(m * n))
    # Space: O(m * n)

    SELECT a.student_id, 
           a.student_name, 
           b.subject_name, 
           Count(c.subject_name) AS attended_exams 
    FROM   students AS a 
           CROSS JOIN subjects AS b 
           LEFT JOIN examinations AS c 
                  ON a.student_id = c.student_id 
                     AND b.subject_name = c.subject_name 
    GROUP  BY a.student_id, 
              b.subject_name
    ORDER  BY a.student_id, 
              b.subject_name;
## Traffic Light Controlled Intersection
// Time:  O(n)
// Space: O(1)

class TrafficLight {
public:
    TrafficLight()
     : light_{1} {
        
    }

    void carArrived(
        int carId,                   // ID of the car
        int roadId,                  // ID of the road the car travels on. Can be 1 (road A) or 2 (road B)
        int direction,               // Direction of the car
        function<void()> turnGreen,  // Use turnGreen() to turn light to green on current road
        function<void()> crossCar    // Use crossCar() to make car cross the intersection
    ) {
        unique_lock<mutex> lock{m_};
        if (light_ != roadId) {
            light_ = roadId;
			turnGreen();
		}
        crossCar();
    }

private:
    mutex m_;
    int light_;
};

## All People Report to the Given Manager
    # Time:  O(n)
    # Space: O(n)

    SELECT e1.employee_id 
    FROM   employees e1 
           LEFT JOIN employees e2 
                  ON e1.manager_id = e2.employee_id 
           LEFT JOIN employees e3 
                  ON e2.manager_id = e3.employee_id 
    WHERE  e3.manager_id = 1 
           AND e1.employee_id != 1

## Print Immutable Linked List in Reverse
    // Time:  O(n)
    // Space: O(sqrt(n))

    class Solution {
    public:
        void printLinkedListInReverse(ImmutableListNode* head) {
            int count = 0;
            auto curr = head;
            while (curr) {
                curr = curr->getNext();
                ++count;
            }
            int bucket_count = ceil(sqrt(count));

            vector<ImmutableListNode *> buckets;
            count = 0;
            curr = head;
            while (curr) {
                if (count % bucket_count == 0) {
                    buckets.emplace_back(curr);
                }
                curr = curr->getNext();
                ++count;
            }

            for (int i = buckets.size() - 1; i >= 0; --i) {
                printNodes(buckets[i], bucket_count);
            }
        }

    private:
        void printNodes(ImmutableListNode *head, int count) {
            vector<ImmutableListNode *> nodes;
            while (head && nodes.size() != count) {
                nodes.emplace_back(head);
                head = head->getNext();
            }
            for (int i = nodes.size() - 1; i >= 0; --i) {
                nodes[i]->printValue();
            }
        }
    };

    // Time:  O(n)
    // Space: O(n)
    class Solution2 {
    public:
        void printLinkedListInReverse(ImmutableListNode* head) {
            vector<ImmutableListNode *> nodes;
            while (head) {
                nodes.emplace_back(head);
                head = head->getNext();
            }
            for (int i = nodes.size() - 1; i >= 0; --i) {
                nodes[i]->printValue();
            }
        }
    };

    // Time:  O(n^2)
    // Space: O(1)
    class Solution3 {
    public:
        void printLinkedListInReverse(ImmutableListNode* head) {
            for (ImmutableListNode *tail = nullptr, *curr = nullptr; tail != head; tail = curr) {
                for (curr = head; curr->getNext() != tail; curr = curr->getNext());
                curr->printValue();
            }
        }
    };
## Page Recommendations
    # Time:  O(m + n)
    # Space: O(m)

    SELECT DISTINCT page_id AS recommended_page 
    FROM   likes l2 
    WHERE  NOT EXISTS (SELECT *
                       FROM   likes l1 
                       WHERE  l1.user_id = 1 
                              AND l1.page_id = l2.page_id) 
           AND user_id IN (SELECT user2_id AS friend_id 
                           FROM   friendship 
                           WHERE  user1_id = 1 
                           UNION 
                           SELECT user1_id AS friend_id 
                           FROM   friendship 
                           WHERE  user2_id = 1) 
## Counting Elements
// Time:  O(n)
// Space: O(n)

class Solution {
public:
    int countElements(vector<int>& arr) {
        unordered_set<int> lookup(cbegin(arr), cend(arr));
        return count_if(cbegin(arr), cend(arr),
                        [&lookup](const auto& x) {
                            return lookup.count(x + 1);
                        });
    }
};

// Time:  O(nlogn)
// Space: O(1)
class Solution2 {
public:
    int countElements(vector<int>& arr) {
        sort(begin(arr), end(arr));
        int result = 0, l = 1;
        for (int i = 0; i + 1 < arr.size(); ++i) {
            if (arr[i] == arr[i + 1]) {
                ++l;
                continue;
            }
            if (arr[i] + 1 == arr[i + 1]) {
                result += l;
            }
            l = 1;
        }
        return result;
    }
};
                                           
## Average Selling Price
Time:  O(n)
Space: O(n)

SELECT b.product_id, 
       ROUND(SUM(a.units * b.price) / SUM(a.units), 2) AS average_price 
FROM   UnitsSold AS a 
       INNER JOIN Prices AS b 
               ON a.product_id = b.product_id 
WHERE  a.purchase_date BETWEEN b.start_date AND b.end_date 
GROUP  BY product_id;  

## Web Crawler Multithreaded
// Time:  O(|V| + |E|)
// Space: O(|V|)

/**
 * // This is the HtmlParser's API interface.
 * // You should not implement it, or speculate about its implementation
 * class HtmlParser {
 *   public:
 *     vector<string> getUrls(string url);
 * };
 */
class Solution {
public:
    vector<string> crawl(string startUrl, HtmlParser htmlParser) {
        q_.emplace(startUrl);
        unordered_set<string> lookup = {startUrl};
        vector<thread> workers;
        const auto& worker = [this](HtmlParser *htmlParser, unordered_set<string> *lookup) {
            while (true) {
                string from_url;
                {
                    unique_lock<mutex> lock{m_};
                    cv_.wait(lock, [this]() { return !q_.empty(); });
                    from_url = q_.front(); q_.pop();
                    if (from_url.empty()) {
                        break;
                    }
                    ++working_count_;
                }
                const auto& name = hostname(from_url);
                for (const auto& to_url: htmlParser->getUrls(from_url)) {
                    if (name != hostname(to_url)) {
                        continue;
                    }
                    unique_lock<mutex> lock{m_};
                    if (!lookup->count(to_url)) {
                        lookup->emplace(to_url);
                        q_.emplace(to_url);
                        cv_.notify_all();
                    }
                }
                {
                    unique_lock<mutex> lock{m_};
                    --working_count_;
                    if (q_.empty() && !working_count_) {
                        cv_.notify_all();
                    }
                }
            }
        };
        for (int i = 0; i < NUMBER_OF_WORKERS; ++i) {
            workers.emplace_back(worker, &htmlParser, &lookup);
        }
        {
            unique_lock<mutex> lock{m_};
            cv_.wait(lock, [this]() { return q_.empty() && !working_count_; });
            for (const auto& t : workers) {
                q_.emplace();
            }
            cv_.notify_all();
        }
        for (auto& t : workers) {
            t.join();
        }
        return vector<string>(lookup.cbegin(), lookup.cend());
    }

private:
    string hostname(const string& url) {
        static const string scheme = "http://";
        return url.substr(0, url.find('/', scheme.length()));
    }

    static const int NUMBER_OF_WORKERS = 4;
    queue<string> q_;
    int working_count_ = 0;
    mutex m_;
    condition_variable cv_;
};

// Time:  O(|V| + |E|)
// Space: O(|V|)
class Solution2 {
public:
    vector<string> crawl(string startUrl, HtmlParser htmlParser) {
        q_.emplace(startUrl);
        unordered_set<string> lookup = {startUrl};
        vector<thread> workers;
        for (int i = 0; i < NUMBER_OF_WORKERS; ++i) {
            workers.emplace_back(bind(&Solution2::worker, this, &htmlParser, &lookup));
        }
        {
            unique_lock<mutex> lock{m_};
            cv_.wait(lock, [this]() { return q_.empty() && !working_count_; });
            for (const auto& t : workers) {
                q_.emplace();
            }
            cv_.notify_all();
        }
        for (auto& t : workers) {
            t.join();
        }
        return vector<string>(lookup.cbegin(), lookup.cend());
    }

private:
    void worker(HtmlParser *htmlParser, unordered_set<string> *lookup) {
        while (true) {
            string from_url;
            {
                unique_lock<mutex> lock{m_};
                cv_.wait(lock, [this]() { return !q_.empty(); });
                from_url = q_.front(); q_.pop();
                if (from_url.empty()) {
                    break;
                }
                ++working_count_;
            }
            const auto& name = hostname(from_url);
            for (const auto& to_url: htmlParser->getUrls(from_url)) {
                if (name != hostname(to_url)) {
                    continue;
                }
                unique_lock<mutex> lock{m_};
                if (!lookup->count(to_url)) {
                    lookup->emplace(to_url);
                    q_.emplace(to_url);
                    cv_.notify_all();
                }
            }
            {
                unique_lock<mutex> lock{m_};
                --working_count_;
                if (q_.empty() && !working_count_) {
                    cv_.notify_all();
                }
            }
        }
    }
    
    string hostname(const string& url) {
        static const string scheme = "http://";
        return url.substr(0, url.find('/', scheme.length()));
    }

    static const int NUMBER_OF_WORKERS = 4;
    queue<string> q_;
    int working_count_ = 0;
    mutex m_;
    condition_variable cv_;
};

## Number of Comments per Post
Time:  O(n)
Space: O(n)

SELECT a.sub_id                 AS post_id, 
       Count(DISTINCT b.sub_id) AS number_of_comments 
FROM   submissions a 
       LEFT JOIN submissions b 
              ON a.sub_id = b.parent_id 
WHERE  a.parent_id IS NULL 
GROUP  BY a.sub_id 
ORDER  BY NULL 

## Leftmost Column with at Least a One
// Time:  O(m + n)
// Space: O(1)

class Solution {
public:
    int leftMostColumnWithOne(BinaryMatrix &binaryMatrix) {
        const auto& dim = binaryMatrix.dimensions();
        const auto& m = dim[0], &n = dim[1];
        int r = 0, c = n - 1;
        while (r < m && c >= 0) {
            if (!binaryMatrix.get(r, c)) {
                ++r;
            } else {
                --c;
            }
        }
        return (c + 1 != n) ? c + 1 : -1;
    }
};

## Web Crawler
// Time:  O(|V| + |E|)
// Space: O(|V|)

/**
 * // This is the HtmlParser's API interface.
 * // You should not implement it, or speculate about its implementation
 * class HtmlParser {
 *   public:
 *     vector<string> getUrls(string url);
 * };
 */
class Solution {
public:
    vector<string> crawl(string startUrl, HtmlParser htmlParser) {
        vector<string> result = {startUrl};
        unordered_set<string> lookup(result.cbegin(), result.cend());
        for (int i = 0; i < result.size(); ++i) {
            const auto& from_url = result[i];
            const auto& name = hostname(from_url);
            for (const auto& to_url: htmlParser.getUrls(from_url)) {
                if (!lookup.count(to_url) && name == hostname(to_url)) {
                    result.emplace_back(to_url);
                    lookup.emplace(to_url);
                }
            }
        }
        return result;
    }

private:
    string hostname(const string& url) {
        static const string scheme = "http://";
        return url.substr(0, url.find('/', scheme.length()));
    }
};

## First Unique Number
// Time:  ctor: O(k)
//        add: O(1)
//        showFirstUnique: O(1)
// Space: O(n)

class FirstUnique {
public:
    FirstUnique(vector<int>& nums) {
        for (const auto& num : nums) {
            add(num);
        }
    }
    
    int showFirstUnique() {
        if (q_.size()) {
            return q_.front().first;
        }
        return -1;
    }
    
    void add(int value) {
        if (!dup_.count(value) && !q_.count(value)) {
            q_[value] = true;
            return;
        }
        if (q_.count(value)) {
            q_.pop(value);
            dup_.emplace(value);
        }
    }

private:
    template<typename K, typename V>
    class OrderedDict {
    public:
        bool count(const K& key) const {
            return map_.count(key);
        }
        
        V& operator[](const K& key) {
            if (!map_.count(key)) {
                list_.emplace_front();
                list_.begin()->first = key;
                map_[key] = list_.begin();
            }
            return map_[key]->second;
        }

        void popitem() {
            auto del = list_.front(); list_.pop_front();
            map_.erase(del.first);
        }
        
        pair<K, V> front() {
            return *list_.crbegin();
        }
        
        void pop(const K& key) {
            if (!map_.count(key)) {
                return;
            }
            list_.erase(map_[key]);
            map_.erase(key);
        }
        
        vector<K> keys() const {
            vector<K> result;
            transform(list_.crbegin(), list_.crend(), back_inserter(result),
                      [](const auto& x) {
                          return x.first;
                      });
            return result;
        }
        
        int size() const {
            return list_.size();
        }
    
    private:
        list<pair<K, V>> list_;
        unordered_map<K, typename list<pair<K, V>>::iterator> map_;
    };
    
    OrderedDict<int, bool> q_;
    unordered_set<int> dup_;
};

## Report Contiguous Dates
Time:  O(nlogn)
Space: O(n)

SELECT state     AS period_state, 
       Min(date) AS start_date, 
       Max(date) AS end_date 
FROM   (SELECT state, 
               date, 
               @rank := CASE 
                          WHEN @prev = state THEN @rank 
                          ELSE @rank + 1 
                        end   AS rank, 
               @prev := state AS prev 
        FROM   (SELECT * 
                FROM   (SELECT fail_date AS date, 
                               "failed"  AS state 
                        FROM   failed 
                        UNION ALL 
                        SELECT success_date AS date, 
                               "succeeded"  AS state 
                        FROM   succeeded) a 
                WHERE  date BETWEEN '2019-01-01' AND '2019-12-31' 
                ORDER  BY date ASC) b, 
               (SELECT @rank := 0, 
                       @prev := "unknown") c) d 
GROUP  BY d.rank 
ORDER  BY start_date ASC


## Perform String Shifts
// Time:  O(n + l)
// Space: O(1)

class Solution {
public:
    string stringShift(string s, vector<vector<int>>& shift) {
        int left_shift = 0;
        for (const auto& x : shift) {
            if (!x[0]) {
                left_shift += x[1];
            } else {
                left_shift -= x[1];
            }
        }
        left_shift = ((left_shift) % int(s.length()) + int(s.length())) % int(s.length());
        reverse(begin(s), begin(s) + left_shift);
        reverse(begin(s) + left_shift, end(s));
        reverse(begin(s), end(s));
        return s;
    }
};
    
## Team Scores in Football Tournament
Time:  O(nlogn)
Space: O(n)

SELECT t.team_id,
       t.team_name,
       IFNULL(SUM(m.points), 0) AS num_points
FROM TEAMS t
LEFT JOIN
 (SELECT host_team AS team_id,
         CASE
             WHEN host_goals > guest_goals THEN 3
             WHEN host_goals = guest_goals THEN 1
             ELSE 0
         END AS points
  FROM Matches
  UNION ALL
  SELECT guest_team AS team_id,
         CASE
             WHEN host_goals < guest_goals THEN 3
             WHEN host_goals = guest_goals THEN 1
             ELSE 0
           END AS points
  FROM Matches) m
ON t.team_id = m.team_id
GROUP BY t.team_id
ORDER BY num_points DESC,
         team_id;
         
## Queries Quality and Percentage
Time:  O(n)
Space: O(n)

SELECT query_name,
       ROUND(AVG(rating * 100 / POSITION)) / 100 AS quality,
       ROUND(SUM(rating < 3) * 100 / COUNT(*), 2) AS poor_query_percentage
FROM Queries
GROUP BY query_name
ORDER BY NULL;

## Monthly Transactions II
Time:  O(n)
Space: O(n)

SELECT month,
       country,
       SUM(IF(type = 'approved', 1, 0)) AS approved_count,
       SUM(IF(type = 'approved', amount, 0)) AS approved_amount,
       SUM(IF(type = 'chargeback', 1, 0)) AS chargeback_count,
       SUM(IF(type = 'chargeback', amount, 0)) AS chargeback_amount
FROM (
        (SELECT LEFT(t.trans_date, 7) AS month,
                t.country,
                amount,
                'approved' AS type
         FROM Transactions AS t
         WHERE state = 'approved' )
      UNION ALL
        (SELECT LEFT(c.trans_date, 7) AS month,
                t.country,
                amount,
                'chargeback' AS type
         FROM Transactions AS t
         INNER JOIN Chargebacks AS c ON t.id = c.trans_id)) AS tt
GROUP BY tt.month,
         tt.country;
## Last Person to Fit in the Elevator
    # Time:  O(nlogn) 
    # Space: O(n) 

    SELECT person_name 
    FROM   (SELECT person_name, 
                   @accu := @accu + weight AS accu 
            FROM   (SELECT person_name, weight
                    FROM   Queue 
                    ORDER  BY turn) q, 
                   (SELECT @accu := 0) vars) t 
    WHERE  accu <= 1000 
    ORDER  BY accu DESC 
    LIMIT  1; 
## Tournament Winners
    # Time:  O(m + n + nlogn)
    # Space: P(m + n)

    SELECT group_id, 
           player_id 
    FROM   (SELECT p.group_id, 
                   ps.player_id, 
                   Sum(ps.score) AS score 
            FROM   players p INNER JOIN
                   (SELECT first_player AS player_id, 
                           first_score  AS score 
                    FROM   matches 
                    UNION ALL 
                    SELECT second_player AS player_id,
                           second_score  AS score
                    FROM   matches) ps 
            ON  p.player_id = ps.player_id 
            GROUP  BY ps.player_id 
            ORDER  BY group_id, 
                      score DESC, 
                      player_id) top_scores 
    GROUP  BY group_id
## Monthly Transactions I
    # Time:  O(n)
    # Space: O(n)

    SELECT Date_format(trans_date, '%Y-%m')       AS month, 
           country, 
           Count(id)                              AS trans_count, 
           Count(IF(state = 'approved', 1, NULL)) AS approved_count, 
           SUM(amount)                            AS trans_total_amount, 
           SUM(IF(state = 'approved', amount, 0)) AS approved_total_amount 
    FROM   transactions 
    GROUP  BY Date_format(trans_date, '%Y-%m'), 
              country 
    ORDER BY NULL
## Design Bounded Blocking Queue
    // Time:  O(n)
    // Space: O(1)

    class BoundedBlockingQueue {
    public:    
        BoundedBlockingQueue(int capacity)
          : cap_(capacity) {

        }

        void enqueue(int element) {
            {
                unique_lock<mutex> l(m_);
                cv_.wait(l, [this]() { return q_.size() != cap_; }) ;
                q_.emplace(element);
            }
            cv_.notify_all();
        }

        int dequeue() {
            int element;
            {
                unique_lock<mutex> l(m_);
                cv_.wait(l, [this]() { return !q_.empty(); }) ;
                element = q_.front(); q_.pop();
            }
            cv_.notify_all();
            return element;
        }

        int size() {
            unique_lock<mutex> l(m_);
            return q_.size();
        }

    private:
        mutex m_;
        condition_variable cv_;
        queue<int> q_;
        int cap_;
    };
## Immediate Food Delivery II
    # Time:  O(n), n is the number of delivery
    # Space: O(m), m is the number of customer

    SELECT Round(100 * Sum(order_date = customer_pref_delivery_date) / 
                        Count(DISTINCT customer_id), 2) AS immediate_percentage 
    FROM   delivery 
    WHERE  ( customer_id, order_date ) IN (SELECT customer_id, 
                                                  Min(order_date) 
                                           FROM   delivery 
                                           GROUP  BY customer_id)
## Immediate Food Delivery I
    # Time:  O(n)
    # Space: O(1)

    SELECT Round(100 * Sum(order_date = customer_pref_delivery_date) / Count(*), 2) 
           AS 
           immediate_percentage 
    FROM   delivery;
## Diet Plan Performance
	// Time:  O(n)
	// Space: O(1)

	class Solution {
	public:
	    int dietPlanPerformance(vector<int>& calories, int k, int lower, int upper) {
		int total = accumulate(calories.cbegin(), calories.cbegin() + k, 0);
		int result = int(total > upper) - int(total < lower);
		for (int i = k; i < calories.size(); ++i) {
		    total += calories[i] - calories[i - k];
		    result += int(total > upper) - int(total < lower);
		}
		return result;
	    }
	};
							      
## Product Price at a Given Date
	# Time:  O(mlogn), m is the number of unique product id, n is the number of changed dates
	# Space: O(m)

	SELECT t1.product_id                      AS product_id, 
	       IF(Isnull(t2.price), 10, t2.price) AS price 
	FROM   (SELECT DISTINCT product_id 
		FROM   products) AS t1 
	       left join (SELECT product_id, 
				 new_price AS price 
			  FROM   products 
			  WHERE  ( product_id, change_date ) IN (SELECT product_id, 
									Max(change_date) 
								 FROM   products 
								 WHERE change_date <= '2019-08-16' 
								 GROUP  BY product_id)) 
			 AS t2 
		      ON t1.product_id = t2.product_id 

## Market Analysis II
	# Time:  O(m + n), m is the number of users, n is the number of orders
	# Space: O(m + n)

	SELECT user_id AS seller_id, 
	       IF(item_brand = favorite_brand, 'yes', 'no') AS 2nd_item_fav_brand 
	FROM   (SELECT user_id, 
		       favorite_brand, 
		       (SELECT    item_id
			FROM      orders o 
			WHERE     user_id = o.seller_id 
			ORDER BY order_date limit 1, 1) item_id
		FROM   users) u
		LEFT JOIN items i 
		ON        u.item_id = i.item_id
## Market Analysis I
	# Time:  O(m + n)
	# Space: O(m + n)

	SELECT u.user_id                   AS buyer_id, 
	       u.join_date, 
	       Ifnull(t.orders_in_2019, 0) AS orders_in_2019 
	FROM   users u 
	       LEFT JOIN (SELECT buyer_id        AS user_id, 
				 Count(buyer_id) AS orders_in_2019 
			  FROM   orders 
			  WHERE  Year(order_date) = 2019 
			  GROUP  BY buyer_id)t 
              ON u.user_id = t.user_id
## Article Views II
	# Time:  O(nlogn)
	# Space: O(n)

	SELECT DISTINCT viewer_id AS id 
	FROM   views 
	GROUP  BY viewer_id, 
		  view_date 
	HAVING Count(DISTINCT article_id) > 1 
	ORDER  BY id
## Article Views I

	# Time:  O(nlogn)
	# Space: O(n)

	SELECT DISTINCT author_id AS id 
	FROM   views 
	WHERE  author_id = viewer_id 
	ORDER  BY id
## User Activity for the Past 30 Days II
	# Time:  O(n)
	# Space: O(n)

	SELECT Round(Ifnull(Count(DISTINCT session_id) / Count(DISTINCT user_id), 0), 2) 
	       AS 
	       average_sessions_per_user 
	FROM   activity 
	WHERE  Datediff("2019-07-27", activity_date) < 30 
## User Activity for the Past 30 Days I
	# Time:  O(n)
	# Space: O(n)

	SELECT activity_date           AS day, 
	       Count(DISTINCT user_id) AS active_users 
	FROM   activity 
	GROUP  BY activity_date 
	HAVING Datediff("2019-07-27", activity_date) < 30 
	ORDER  BY NULL
	
## Reported Posts II
	# Time:  O(m + n)
	# Space: O(n)

	SELECT ROUND(AVG(removal_percent), 2) average_daily_percent
	FROM
	  (SELECT a.action_date,
		  COUNT(DISTINCT r.post_id) / COUNT(DISTINCT a.post_id) * 100 removal_percent
	   FROM Actions a
	   LEFT JOIN Removals r ON a.post_id = r.post_id
	   WHERE a.extra = 'spam'
	   GROUP BY a.action_date
	   ORDER BY NULL) tmp
## Number of Ships in a Rectangle
	// Time:  O(s * log(m * n)), s is the max number of ships, which is 10 in this problem
	// Space: O(log(m * n))

	/**
	 * // This is Sea's API interface.
	 * // You should not implement it, or speculate about its implementation
	 * class Sea {
	 *   public:
	 *     bool hasShips(vector<int> topRight, vector<int> bottomLeft);
	 * };
	 */

	class Solution {
	public:
	    int countShips(Sea sea, vector<int> topRight, vector<int> bottomLeft) {
		int result = 0;
		if (topRight[0] >= bottomLeft[0] &&
		    topRight[1] >= bottomLeft[1] &&
		    sea.hasShips(topRight, bottomLeft)) {
		    if (topRight == bottomLeft) {
			return 1;
		    }
		    const auto& mid_x = (topRight[0] + bottomLeft[0]) / 2;
		    const auto& mid_y = (topRight[1] + bottomLeft[1]) / 2;
		    result += countShips(sea, topRight, {mid_x + 1, mid_y + 1});
		    result += countShips(sea, {mid_x, topRight[1]}, {bottomLeft[0], mid_y + 1});
		    result += countShips(sea, {topRight[0], mid_y}, {mid_x + 1, bottomLeft[1]});
		    result += countShips(sea, {mid_x, mid_y}, bottomLeft);
		}
		return result;
	    }
	};
## User Purchase Platform
	# Time:  O(n)
	# Space: O(n)

	SELECT t1.spend_date, 
	       'both'                       AS platform, 
	       Sum(Ifnull(t.sum_amount, 0)) AS total_amount, 
	       Count(t.user_id)             AS total_users 
	FROM   (SELECT spend_date, 
		       user_id, 
		       Sum(amount) AS sum_amount 
		FROM   spending 
		GROUP  BY spend_date, 
			  user_id 
		HAVING Count(platform) = 2) AS t 
	       RIGHT JOIN (SELECT DISTINCT spend_date 
			   FROM   spending) AS t1
		       ON t.spend_date = t1.spend_date 
	GROUP  BY t1.spend_date 
	UNION 
	SELECT t2.spend_date, 
	       'mobile'                 AS platform, 
	       Sum(Ifnull(t.amount, 0)) AS total_amount, 
	       Count(t.user_id)         AS total_users 
	FROM   (SELECT spend_date, 
		       user_id, 
		       platform, 
		       amount 
		FROM   spending 
		GROUP  BY spend_date, 
			  user_id 
		HAVING Count(platform) < 2) AS t 
	       RIGHT JOIN (SELECT DISTINCT spend_date 
			   FROM   spending) AS t2 
		       ON t.spend_date = t2.spend_date 
			  AND t.platform = 'mobile' 
	GROUP  BY t2.spend_date 
	UNION 
	SELECT t3.spend_date, 
	       'desktop'                AS platform, 
	       Sum(Ifnull(t.amount, 0)) AS total_amount, 
	       Count(t.user_id)         AS total_users 
	FROM   (SELECT spend_date, 
		       user_id, 
		       platform, 
		       amount 
		FROM   spending 
		GROUP  BY spend_date, 
			  user_id 
		HAVING Count(platform) < 2) AS t 
	       RIGHT JOIN (SELECT DISTINCT spend_date 
			   FROM   spending) AS t3
		       ON t.spend_date = t3.spend_date 
			  AND t.platform = 'desktop' 
	GROUP  BY t3.spend_date
## Active Businesses
	# Time:  O(n)
	# Space: O(n)

	SELECT business_id
	FROM EVENTS
	JOIN
	  (SELECT event_type,
		  avg(occurences) AS average
	   FROM EVENTS
	   GROUP BY event_type) AS TEMP ON Events.event_type = temp.event_type
	AND Events.occurences > temp.average
	GROUP BY business_id
	HAVING count(DISTINCT Events.event_type) > 1
	ORDER BY NULL
## Reported Posts
	# Time:  O(n)
	# Space: O(n)

	SELECT extra                   AS report_reason, 
	       Count(DISTINCT post_id) AS report_count 
	FROM   actions 
	WHERE  action = 'report' 
	       AND action_date = '2019-07-04' 
	GROUP  BY extra
	ORDER  BY NULL
## Highest Grade For Each Student
	# Time:  O(nlogn)
	# Space: O(n)

	SELECT student_id, 
	       Min(course_id) AS course_id, 
	       grade 
	FROM   enrollments 
	WHERE  ( student_id, grade ) IN (SELECT student_id, 
						Max(grade) 
					 FROM   enrollments 
					 GROUP  BY student_id 
					 ORDER  BY NULL) 
	GROUP  BY student_id 
	ORDER  BY student_id
## Handshakes That Don't Cross
	// Time:  O(n)
	// Space: O(1)

	class Solution {
	public:
	    int numberOfWays(int num_people) {
		static const int MOD = 1e9 + 7;
		int n = num_people / 2;
		return 1ULL * nCr(2 * n, n, MOD) * inv(n + 1, MOD) % MOD;  // Catalan number
	    }

	private:
	    int nCr(int n, int k, int m) {
		if (n - k < k) {
		    return nCr(n, n - k, m);
		}
		uint64_t result = 1;
		for (int i = 1; i <= k; ++i) {
		    result = (result * (n - k + i) % m) * inv(i, m) % m;
		}
		return result;
	    }

	    int inv(int x, int m) {  // Euler's Theorem
		return pow(x, m - 2, m);
	    }

	    int pow(uint64_t a, int b, int m) {  // O(logMOD) = O(1)
		a %= m;
		uint64_t result = 1;
		while (b) {
		    if (b & 1) {
			result = (result * a) % m;
		    }
		    a = (a * a) % m;
		    b >>= 1;
		}
		return result;
	    }
	};

	// Time:  O(n^2)
	// Space: O(n)
	class Solution2 {
	public:
	    int numberOfWays(int num_people) {
		static const int MOD = 1e9 + 7;
		vector<uint64_t> dp(num_people / 2 + 1);
		dp[0] = 1ULL;
		for (int k = 1; k <= num_people / 2; ++k) {
		    for (int i = 0; i < k; ++i) {
			dp[k] = (dp[k] + dp[i] * dp[k - 1 - i]) % MOD;
		    }
		}
		return dp[num_people / 2];
	    }
	};
## New Users Daily Count
	# Time:  O(n)
	# Space: O(n)

	SELECT login_date, 
	       Count(user_id) AS user_count 
	FROM   (SELECT user_id, 
		       Min(activity_date) AS login_date 
		FROM   traffic 
		WHERE  activity = 'login' 
		GROUP  BY user_id
		ORDER BY NULL) p 
	WHERE  Datediff('2019-06-30', login_date) <= 90 
	GROUP BY login_date
	ORDER BY NULL
## Palindrome Removal
	// Time:  O(n^3)
	// Space: O(n^2)

	class Solution {
	public:
	    int minimumMoves(vector<int>& arr) {
		vector<vector<int>> dp(arr.size() + 1, vector<int>(arr.size()));
		for (int l = 1; l <= arr.size(); ++l) {
		    for (int i = 0; i + l - 1 < arr.size(); ++i) {
			int j = i + l - 1;
			if (l == 1) {
			    dp[i][j] = 1;
			} else {
			    dp[i][j] = 1 + dp[i + 1][j];
			    if (arr[i] == arr[i + 1]) {
				dp[i][j] = min(dp[i][j], 1 + dp[i + 2][j]);
			    }
			    for (int k = i + 2; k <= j; ++k) {
				if (arr[i] == arr[k]) {
				    dp[i][j] = min(dp[i][j], dp[i + 1][k - 1] + dp[k + 1][j]);
				}
			    }
			}
		    }
		}
		return dp[0][arr.size() - 1]; 
	    }
	};
## Delete Tree Nodes
	// Time:  O(n)
	// Space: O(n)

	class Solution {
	public:
	    int deleteTreeNodes(int nodes, vector<int>& parent, vector<int>& value) {
		unordered_map<int, vector<int>> children;
		for (int i = 1; i < parent.size(); ++i) {
		    children[parent[i]].emplace_back(i);
		}
		return dfs(value, &children, 0).second;
	    }

	private:
	    pair<int, int> dfs(const vector<int>& value, 
			       unordered_map<int, vector<int>> *children,
			       int x) {
		int total = value[x], count = 1;
		for (const auto& y : (*children)[x]) {
		    const auto& [t, c] = dfs(value, children, y);
		    total += t;
		    count += t ? c : 0;
		}
		return {total, total ? count : 0};
	    }
	};


	// Time:  O(n)
	// Space: O(n)
	class Solution2 {
	public:
	    int deleteTreeNodes(int nodes, vector<int>& parent, vector<int>& value) {
		// assuming parent[i] < i for all i > 0
		vector<int> result(nodes, 1);
		for (int i = nodes - 1; i >= 1; --i) {
		    value[parent[i]] += value[i];
		    result[parent[i]] += value[i] ? result[i] : 0;
		}
		return result[0];
	    }
	};
## Remove Interval
	// Time:  O(n)
	// Space: O(1)

	class Solution {
	public:
	    vector<vector<int>> removeInterval(vector<vector<int>>& intervals, vector<int>& toBeRemoved) {
		vector<vector<int>> result;
		for (const auto& interval : intervals) {
		    vector<pair<int, int>> tmp = {{interval[0], min(toBeRemoved[0], interval[1])},
						  {max(interval[0], toBeRemoved[1]), interval[1]}};
		    for (const auto& [x, y] : tmp) {
			if (x < y) {
			    result.push_back({x, y});
			}
		    }
		}
		return result;
	    }
	};
## Hexspeak
// Time:  O(n)
// Space: O(1)

class Solution {
public:
    string toHexspeak(string num) {
        unordered_map<int, char> lookup = {{0, 'O'}, {1,'I'}};
        for (int i = 0; i < 6; ++i) {
            lookup[10 + i] = 'A' + i;
        }
        string result;
        uint64_t n = stoul(num), r = 0;
        while (n) {
            r = n % 16;
            n /= 16;
            if (!lookup.count(r)) {
                return "ERROR";
            }
            result.push_back(lookup[r]);
        }
        reverse(result.begin(), result.end());
        return result;
    }
};

// Time:  O(n)
// Space: O(n)
class Solution2 {
public:
    string toHexspeak(string num) {
        stringstream ss;
        ss << hex << uppercase  << stoul(num);
        string result(ss.str());
        for (auto i = 0; i < result.length(); ++i) {
            if ('2' <= result[i] && result[i] <= '9') {
                return "ERROR";
            }
            if (result[i] == '0') {
                result[i] = 'O';
            }
            if (result[i] == '1') {
                result[i] = 'I';
            }
        }
        return result;
    }
};
## Unpopular Books
	# Time:  O(m + n)
	# Space: O(n)

	SELECT b.book_id, 
	       b.NAME 
	FROM   books AS b 
	       LEFT JOIN orders AS o 
		      ON b.book_id = o.book_id 
			 AND dispatch_date BETWEEN '2018-06-23' AND '2019-6-23' 
	WHERE  Datediff('2019-06-23', b.available_from) > 30 
	GROUP BY book_id 
	HAVING Sum(IFNULL(o.quantity, 0)) < 10 ORDER  BY NULL 
## Game Play Analysis V
	# Time:  O(n^2)
	# Space: O(n)

	SELECT install_dt, 
	       Count(player_id)                             AS installs, 
	       Round(Count(next_day) / Count(player_id), 2) AS Day1_retention 
	FROM   (SELECT a.player_id, 
		       a.install_dt, 
		       b.event_date AS next_day 
		FROM   (SELECT player_id, 
			       Min(event_date) AS install_dt 
			FROM   activity 
			GROUP  BY player_id) AS a 
		       LEFT JOIN activity AS b 
			      ON Datediff(b.event_date, a.install_dt) = 1
				 AND a.player_id = b.player_id ) AS t 
	GROUP  BY install_dt; 
## Divide Chocolate
	// Time:  O(nlogn)
	// Space: O(1)

	class Solution {
	public:
	    int maximizeSweetness(vector<int>& sweetness, int K) {
		int left = *min_element(sweetness.cbegin(), sweetness.cend());
		int right = accumulate(sweetness.cbegin(), sweetness.cend(), 0) / (K + 1);
		while (left <= right) {
		    const auto& mid = left + (right - left) / 2;
		    if (!check(sweetness, K, mid)) {
			right = mid - 1;
		    } else {
			left = mid + 1;
		    }
		}
		return right;
	    }

	private:
	    bool check(const vector<int>& sweetness, int K, int x) {
		int curr = 0, cuts = 0;
		for (const auto& s : sweetness) {
		    curr += s;
		    if (curr >= x) {
			++cuts;
			curr = 0;
		    }
		}
		return cuts >= K + 1;
	    }
	};
## Synonymous Sentences
// Time:  O(p*l * log(p*l)), p is the production of all number of synonyms
//                         , l is the length of a word
// Space: O(p*l)

class Solution {
public:
    vector<string> generateSentences(vector<vector<string>>& synonyms, string text) {
        unordered_map<string, int> lookup;
        unordered_map<int, string> inv_lookup;
        for (const auto& synonym : synonyms) {
            assign_id(synonym[0], &lookup, &inv_lookup);
            assign_id(synonym[1], &lookup, &inv_lookup);
        }
        UnionFind union_find(lookup.size());
        for (const auto& synonym : synonyms) {
            union_find.union_set(lookup[synonym[0]], lookup[synonym[1]]);
        }
        unordered_map<int, vector<int>> groups;
        for (int i = 0; i < union_find.set().size(); ++i) {
            groups[union_find.find_set(i)].emplace_back(i);
        }
        vector<vector<string>> result;
        for (const auto& w : split(text, ' ')) {
            if (!lookup.count(w)) {
                result.push_back({w});
                continue;
            }
            result.emplace_back();
            for (const auto& x : groups[union_find.find_set(lookup[w])]) {
                result.back().emplace_back(inv_lookup[x]);
            }
            sort(result.back().begin(), result.back().end());
        }
        return product(result);
    }

private:
    void assign_id(const string& x,
                   unordered_map<string, int> *lookup,
                   unordered_map<int, string> *inv_lookup) {
        if (lookup->count(x)) {
            return;
        }
        (*lookup)[x] = lookup->size();
        (*inv_lookup)[(*lookup)[x]] = x;
    }
    
    vector<string> split(const string& s, const char delim) const {
        vector<string> tokens;
        stringstream ss(s);
        string token;
        while (getline(ss, token, delim)) {
            if (!token.empty()) {
                tokens.emplace_back(token);
            }
        }
        return tokens;
    }
    
    vector<string> product(const vector<vector<string>>& options) const {
        vector<string> result;
        int total = 1;
        for (const auto& opt : options) {
            total *= opt.size();
        }
        for (int i = 0; i < total; ++i) {
            vector<string> tmp;
            int curr = i;
            for (int j = options.size() - 1; j >= 0; --j) {
                tmp.emplace_back(options[j][curr % options[j].size()]);
                curr /= options[j].size();
            }
            reverse(tmp.begin(), tmp.end());
            result.emplace_back(join(tmp, " "));
        }
        return result;
    }
    
    string join(const vector<string>& strings, const string& delim) const {
        if (strings.empty()) {
            return "";
        }
        ostringstream imploded;
        copy(strings.begin(), prev(strings.end()), ostream_iterator<string>(imploded, delim.c_str()));
        return imploded.str() + *prev(strings.end());
    }
    
    class UnionFind {
        public:
            UnionFind(const int n) : set_(n) {
                iota(set_.begin(), set_.end(), 0);
            }

            int find_set(const int x) {
               if (set_[x] != x) {
                   set_[x] = find_set(set_[x]);  // Path compression.
               }
               return set_[x];
            }

            bool union_set(const int x, const int y) {
                int x_root = find_set(x), y_root = find_set(y);
                if (x_root == y_root) {
                    return false;
                }
                set_[min(x_root, y_root)] = max(x_root, y_root);
                return true;
            }
        
            vector<int> set() const {
                return set_;
            }

        private:
            vector<int> set_;
    };
};
## Smallest Common Region
  
	// Time:  O(m * n)
	// Space: O(n)

	class Solution {
	public:
	    string findSmallestRegion(vector<vector<string>>& regions, string region1, string region2) {
		unordered_map<string, string> parents;
		for (const auto& region : regions) {
		    for (int i = 1; i < region.size(); ++i) {
			parents[region[i]] = region[0];
		    }
		}
		unordered_set<string> lookup = {region1};
		while (parents.count(region1)) {
		    region1 = parents[region1];
		    lookup.emplace(region1);
		}
		while (!lookup.count(region2)) {
		    region2 = parents[region2];
		}
		return region2;
	    }
	};


## Encode Number
// Time:  O(logn)
// Space: O(1)

class Solution {
public:
    string encode(int num) {
        string result;
        while (num) {
            result.push_back((num % 2) ? '0' : '1');
            num = (num - 1) / 2;
        }
        reverse(result.begin(), result.end());
        return result;
    }
};
## Game Play Analysis IV
	# Time:  O(n)
	# Space: O(n)

	SELECT Round(Count(NULLIF(a.event_date, NULL)) / Count(*), 2) fraction 
	FROM   activity a 
	       RIGHT JOIN (SELECT player_id, 
				  Min(event_date) event_date 
			   FROM   activity 
			   GROUP  BY player_id 
			   ORDER  BY NULL) b 
		       ON Datediff(a.event_date, b.event_date) = 1 
			  AND a.player_id = b.player_id
## Game Play Analysis III
	# Time:  O(nlogn)
	# Space: O(n)

	SELECT player_id, 
	       event_date, 
	       @accum := games_played + ( @prev = ( @prev := player_id ) ) * @accum 
	       games_played_so_far 
	FROM   activity, 
	       (SELECT @accum := 0, 
		       @prev := -1) init 
	ORDER  BY player_id, 
		  event_date 
## Game Play Analysis II
	# Time:  O(n)
	# Space: O(n)

	SELECT player_id, 
	       device_id 
	FROM   activity 
	WHERE  ( player_id, event_date ) IN (SELECT player_id, 
						    Min(event_date) 
					     FROM   activity 
					     GROUP  BY player_id 
					     ORDER  BY NULL) 
## Game Play Analysis I
	# Time:  O(n)
	# Space: O(n)

	SELECT player_id, 
	       Min(event_date) first_login 
	FROM   activity 
	GROUP  BY player_id 
	ORDER  BY NULL
## Valid Palindrome III
// Time:  O(n^2)
// Space: O(n)

class Solution {
public:
    bool isValidPalindrome(string s, int k) {
        if (s == string(s.rbegin(), s.rend())) {  // optional, to optimize special case
            return true;
        }
        
        vector<vector<int>> dp(2, vector<int>(s.size(), 1));
        for (int i = s.length() - 2; i >= 0; --i) {
            for (int j = i + 1; j < s.length(); ++j) {
                if (s[i] == s[j]) {
                    dp[i % 2][j] = (i + 1 <= j - 1) ? 2 + dp[(i + 1) % 2][j - 1] : 2;
                } else {
                    dp[i % 2][j] = max(dp[(i + 1) % 2][j], dp[i % 2][j - 1]);
                }
            }
        }
        return s.length() <= k +dp[0][s.length() - 1];
    }
};
## Tree Diameter
	// Time:  O(|V| + |E|)
	// Space: O(|E|)

	class Solution {
	private:
		template <typename T>
	    struct PairHash {
		size_t operator()(const pair<T, T>& p) const {
		    size_t seed = 0;
		    seed ^= std::hash<T>{}(p.first)  + 0x9e3779b9 + (seed<<6) + (seed>>2);
		    seed ^= std::hash<T>{}(p.second) + 0x9e3779b9 + (seed<<6) + (seed>>2);
		    return seed;
		}
	    };

	public:
	    int treeDiameter(vector<vector<int>>& edges) {
		unordered_map<int, unordered_set<int>> graph;
		int length = 0;
		for (const auto& edge : edges) {
		    graph[edge[0]].emplace(edge[1]);
		    graph[edge[1]].emplace(edge[0]);
		}
		unordered_set<pair<int, int>, PairHash<int>> curr_level;
		for (const auto& [u, neighbors] : graph) {
		    if (neighbors.size() == 1) {
			curr_level.emplace(-1, u);
		    }
		}
		while (!curr_level.empty()) {
		    unordered_set<pair<int, int>, PairHash<int>> new_level;
		    for (const auto& [prev, u] : curr_level) {
			for (const auto& v : graph[u]) {
			    if (v != prev) {
				new_level.emplace(u, v);
			    }
			}
		    }
		    curr_level = move(new_level);
		    ++length;
		}
		return max(length - 1, 0);
	    }
	};
## Design A Leaderboard
	// Time:  ctor:  O(1)
	//        add:   O(1)
	//        top:   O(n)
	//        reset: O(1)
	// Space: O(n)

	class Leaderboard {
	public:
	    Leaderboard() {

	    }

	    void addScore(int playerId, int score) {
		lookup_[playerId] += score;
	    }

	    int top(int K) {
		vector<int> scores;
		for (const auto& [k, v] : lookup_) {
		    scores.emplace_back(v);
		}
		nth_element(scores.begin(), scores.begin() + K, scores.end(), greater<int>());
		return accumulate(scores.cbegin(), scores.cbegin() + K, 0);
	    }

	    void reset(int playerId) {
		lookup_[playerId] = 0;
	    }

	private:
	    unordered_map<int, int> lookup_;
	};
## Array Transformation
  
	// Time:  O(n^2)
	// Space: O(n)

	class Solution {
	public:
	    vector<int> transformArray(vector<int>& arr) {    
		while (is_changable(arr)) {
		    auto new_arr = arr;
		    for (int i = 1; i + 1 < arr.size(); ++i) {
			new_arr[i] += int(arr[i - 1] > arr[i] && arr[i] < arr[i + 1]);
			new_arr[i] -= int(arr[i - 1] < arr[i] && arr[i] > arr[i + 1]);
		    }
		    arr = move(new_arr);
		}
		return arr;
	    }

	private:
	    bool is_changable(const vector<int>& arr) {
		for (int i = 1; i + 1 < arr.size(); ++i) {
		    if ((arr[i - 1] > arr[i] && arr[i] < arr[i + 1]) ||
			(arr[i - 1] < arr[i] && arr[i] > arr[i + 1])) {
			return true;
		    }
		}
		return false;
	    }
	};
## Sales Analysis III
	# Time:  O(m + n)
	# Space: O(m + n)

	SELECT product_id, 
	       product_name 
	FROM   product 
	WHERE  product_id NOT IN (SELECT product_id 
				  FROM   sales 
				  WHERE  sale_date NOT BETWEEN 
					 '2019-01-01' AND '2019-03-31'); 
## Sales Analysis II
	# Time:  O(m + n)
	# Space: O(m + n)

	SELECT DISTINCT buyer_id 
	FROM   sales 
	       INNER JOIN product 
		 ON sales.product_id = product.product_id 
	WHERE  product.product_name = "s8" 
	       AND buyer_id NOT IN (SELECT DISTINCT buyer_id 
				    FROM   sales 
					   INNER JOIN product 
					     ON sales.product_id = product.product_id 
				    WHERE  product.product_name = "iphone");
## Sales Analysis I
	# Time:  O(n)
	# Space: O(n)

	SELECT seller_id 
	FROM   sales 
	GROUP  BY seller_id 
	HAVING Sum(price) = (SELECT Sum(price) 
			     FROM   sales 
			     GROUP  BY seller_id 
			     ORDER  BY Sum(price) DESC 
			     LIMIT  1) 
	ORDER  BY NULL
## Minimum Time to Build Blocks
	// Time:  O(nlogn)
	// Space: O(n)

	class Solution {
	public:
	    int minBuildTime(vector<int>& blocks, int split) {
		priority_queue<int, vector<int>, greater<int>> min_heap(blocks.cbegin(), blocks.cend());
		while (min_heap.size() != 1) {
		    min_heap.pop();
		    const auto y = min_heap.top(); min_heap.pop();
		    min_heap.emplace(y + split);
		}
		return min_heap.top();
	    }
	};
## Toss Strange Coins
	// Time:  O(n^2)
	// Space: O(n)

	class Solution {
	public:
	    double probabilityOfHeads(vector<double>& prob, int target) {
		vector<double> dp(target + 1);
		dp[0] = 1.0;
		for (const auto& p : prob) {
		    for (int i = target; i >= 0; --i) {
			dp[i] = ((i >= 1) ? dp[i - 1] : 0.0) * p + dp[i] * (1 - p);
		    }
		}
		return dp[target];
	    }
	};
## Meeting Scheduler
	// Time:  O(n) ~ O(nlogn)
	// Space: O(n)

	class Solution {
	public:
	    vector<int> minAvailableDuration(vector<vector<int>>& slots1, vector<vector<int>>& slots2, int duration) {
		vector<pair<int, int>> min_heap;
		for (const auto& slot : slots1) {
		    if (slot[1] - slot[0] >= duration) {
			min_heap.emplace_back(slot[0], slot[1]);
		    }
		}
		for (const auto& slot : slots2) {
		    if (slot[1] - slot[0] >= duration) {
			min_heap.emplace_back(slot[0], slot[1]);
		    }
		}
		make_heap(min_heap.begin(), min_heap.end(), greater<>());  // Time: O(n)
		while (min_heap.size() > 1) {
		    pop_heap(min_heap.begin(), min_heap.end(), greater<>());  // Time: O(logn)
		    const auto left = min_heap.back();
		    min_heap.pop_back();
		    pop_heap(min_heap.begin(), min_heap.end(), greater<>());
		    const auto right = min_heap.back();
		    min_heap.pop_back();
		    min_heap.emplace_back(right);
		    push_heap(min_heap.begin(), min_heap.end(), greater<>());
		    if (left.second - right.first >= duration) {
			return {right.first, right.first + duration};
		    }
		}
		return {};
	    }
	};

	// Time:  O(nlogn)
	// Space: O(n)
	class Solution2 {
	public:
	    vector<int> minAvailableDuration(vector<vector<int>>& slots1, vector<vector<int>>& slots2, int duration) {
		priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> min_heap;
		for (const auto& slot : slots1) {
		    if (slot[1] - slot[0] >= duration) {
			min_heap.emplace(slot[0], slot[1]);
		    }
		}
		for (const auto& slot : slots2) {
		    if (slot[1] - slot[0] >= duration) {
			min_heap.emplace(slot[0], slot[1]);
		    }
		}
		while (min_heap.size() > 1) {
		    const auto left = min_heap.top();
		    min_heap.pop();
		    const auto right = min_heap.top();
		    if (left.second - right.first >= duration) {
			return {right.first, right.first + duration};
		    }
		}
		return {};
	    }
	};

	// Time:  O(nlogn)
	// Space: O(n)
	class Solution3 {
	public:
	    vector<int> minAvailableDuration(vector<vector<int>>& slots1, vector<vector<int>>& slots2, int duration) {
		sort(slots1.begin(), slots1.end());
		sort(slots2.begin(), slots2.end());
		int i = 0, j = 0;
		while (i < slots1.size() && j < slots2.size()) {
		    const auto& left = max(slots1[i][0], slots2[j][0]);
		    const auto& right = min(slots1[i][1], slots2[j][1]);
		    if (left + duration <= right) {
			return {left, left + duration};
		    }
		    if (slots1[i][1] < slots2[j][1]) {
			++i;
		    } else {
			++j;
		    }
		}
		return {};
	    }
	};
## Missing Number In Arithmetic Progression
	// Time:  O(logn)
	// Space: O(1)

	class Solution {
	public:
	    int missingNumber(vector<int>& arr) {
		const auto& d = (arr.back() - arr[0]) / static_cast<int>(arr.size());
		int left = 0, right = arr.size() - 1;
		while (left <= right) {
		    const auto& mid = left + (right - left) / 2;
		    if (check(arr, d, mid)) {
			right = mid - 1;
		    } else {
			left = mid + 1;
		    }
		}
		return arr[0] + d * left;
	    }

	private:
	    bool check(const vector<int>& arr, int d, int x) {
		return arr[x] != arr[0] + d * x;
	    }
	};

	// Time:  O(logn)
	// Space: O(1)
	class Solution2 {
	public:
	    int missingNumber(vector<int>& arr) {
		return (*min_element(arr.cbegin(), arr.cend()) +
			*max_element(arr.cbegin(), arr.cend())) *
		       (arr.size() + 1) / 2 -
		       accumulate(arr.cbegin(), arr.cend(), 0);
	    }
	};
## Project Employees III
	# Time:  O((m + n)^2)
	# Space: O(m + n)

	SELECT project_id, 
	       p1.employee_id 
	FROM   project AS p1 
	       INNER JOIN employee AS e1 
		       ON p1.employee_id = e1.employee_id 
	WHERE  ( project_id, experience_years ) IN (SELECT project_id, 
							   Max(experience_years) 
						    FROM   project AS p2 
							   INNER JOIN employee AS e2 
								   ON p2.employee_id = 
								      e2.employee_id 
						    GROUP  BY project_id 
						    ORDER  BY NULL)
## Project Employees II
	# Time:  O(n)
	# Space: O(n)

	SELECT project_id 
	FROM   project 
	GROUP  BY project_id 
	HAVING Count(employee_id) = (SELECT Count(employee_id) 
				     FROM   project 
				     GROUP  BY project_id 
				     ORDER  BY Count(employee_id) DESC 
				     LIMIT  1) 
	ORDER  BY NULL 
## Project Employees I
	# Time:  O(m + n) 
	# Space: O(m + n) 

	SELECT project_id, 
	       Round(Avg(experience_years), 2) AS average_years 
	FROM   project AS p 
	       INNER JOIN employee AS e 
		       ON p.employee_id = e.employee_id 
	GROUP  BY project_id 
	ORDER  BY NULL
## Product Sales Analysis III
	# Time:  O(n) 
	# Space: O(n) 

	SELECT product_id, 
	       Min(year) AS first_year, 
	       quantity, 
	       price 
	FROM   sales 
	GROUP  BY product_id 
	ORDER  BY NULL 
## Product Sales Analysis II
	# Time:  O(n)
	# Space: O(n)

	SELECT product_id, 
	       Sum(quantity) AS total_quantity 
	FROM   sales 
	GROUP  BY product_id 
	ORDER  BY NULL
## Product Sales Analysis I
	# Time:  O(m + n)
	# Space: O(m + n)

	SELECT p.product_name, 
	       s.year, 
	       s.price 
	FROM   sales AS s 
	       INNER JOIN product AS p 
		       ON s.product_id = p.product_id
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
