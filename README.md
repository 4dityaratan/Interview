# Leetcode Premium Question

##534. Design TinyURL
        // Time:  O(1)
        // Space: O(n)

        class Solution {
        public:
            Solution() : gen_((random_device())()) {
            }

            // Encodes a URL to a shortened URL.
            string encode(string longUrl) {
                string key = getRand();
                while (lookup_.count(key)) {
                    key = getRand();
                }
                lookup_[key] = longUrl;
                return "http://tinyurl.com/" + key;
            }

            // Decodes a shortened URL to its original URL.
            string decode(string shortUrl) {
                return lookup_[shortUrl.substr(tiny_url.length())];
            }

        private:
            string getRand() {
                string random;
                for (int i = 0; i < random_length; ++i) {
                    random += alphabet_[uniform_int_distribution<int>{0, alphabet_.length() - 1}(gen_)];
                }
                return random;
            }

            const int random_length = 6;
            const string tiny_url = "http://tinyurl.com/";
            const string alphabet_ = "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ";
            default_random_engine gen_;
            unordered_map<string, string> lookup_;
        };

##1 TWO SUM
        // Time:  O(n)
        // Space: O(n)

        class Solution {
        public:
            vector<int> twoSum(vector<int>& nums, int target) {
                unordered_map<int, int> lookup;
                for (int i = 0; i < nums.size(); ++i) {
                    if (lookup.count(target - nums[i])) {
                        return {lookup[target - nums[i]], i};
                    }
                    lookup[nums[i]] = i;
                }
                return {};
            }
        };
