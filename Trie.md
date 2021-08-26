# Trie (Prefix Tree)

wiki: https://zh.wikipedia.org/wiki/Trie

![](https://i.imgur.com/CM56YAE.jpg)

字典樹，用於字串匹配快速搜尋。也適合配合DFS進行搜尋。

## Code
### leetcode problem 208: Implement Trie (Prefix Tree)

Implement the Trie class:

1. Trie() Initializes the trie object.
2. void insert(String word) Inserts the string word into the trie.
3. boolean search(String word) Returns true if the string word is in the trie (i.e., was inserted before), and false otherwise.
4. boolean startsWith(String prefix) Returns true if there is a previously inserted string word that has the prefix prefix, and false otherwise.
 

Example 1:

Input:
["Trie", "insert", "search", "search", "startsWith", "insert", "search"]
[[], ["apple"], ["apple"], ["app"], ["app"], ["app"], ["app"]]
Output:
[null, null, true, false, true, null, true]

```
class Trie {
public:
    /** Initialize your data structure here. */
    struct TrieNode{
        char c = '0';
        bool leaf = 0;
        vector<TrieNode*> child;
    };
    
    TrieNode* root;
    
    Trie() {
        root = new TrieNode();
    }
    
    /** Inserts a word into the trie. */
    void insert(string word) {
        TrieNode* curr = root;
        
        for(int i=0;i<word.size();++i){
            bool f = 0;
            int idx = -1;
            for(int j=0;j<curr->child.size();++j){
                if(curr->child[j]->c == word[i]){
                    f = 1;
                    idx = j;
                    break;
                }
            }
            if(f){
                curr = curr->child[idx];
            }else{
                TrieNode* new_node = new TrieNode();
                new_node->c = word[i];
                curr->child.push_back(new_node);
                curr = new_node;
            }
        }
        curr->leaf = 1;
    }
    
    /** Returns if the word is in the trie. */
    bool search(string word) {
        TrieNode* curr = root;
        
        for(int i=0;i<word.size();++i){
            bool f = 0;
            int idx = -1;
            for(int j=0;j<curr->child.size();++j){
                if(curr->child[j]->c == word[i]){
                    f = 1;
                    idx = j;
                    break;
                }
            }
            if(f){
                curr = curr->child[idx];
            }else{
                return false;
            }
        }
        if(curr->leaf){
            return true;
        }
        return false;
    }
    
    /** Returns if there is any word in the trie that starts with the given prefix. */
    bool startsWith(string prefix) {
        TrieNode* curr = root;
        
        for(int i=0;i<prefix.size();++i){
            bool f = 0;
            int idx = -1;
            for(int j=0;j<curr->child.size();++j){
                if(curr->child[j]->c == prefix[i]){
                    f = 1;
                    idx = j;
                    break;
                }
            }
            if(f){
                curr = curr->child[idx];
            }else{
                return false;
            }
        }
        return true;
    }
};

/**
 * Your Trie object will be instantiated and called as such:
 * Trie* obj = new Trie();
 * obj->insert(word);
 * bool param_2 = obj->search(word);
 * bool param_3 = obj->startsWith(prefix);
 */
```


### leetcode problem 212: Word Search II

棋盤搜索: DFS配合Trie

```
class Solution {
public:
    struct TrieNode{
        char c = '0';
        bool leaf = 0;
        vector<TrieNode*> child;
    };
    
    TrieNode* root;
    /** Inserts a word into the trie. */
    void insert(string word) {
        TrieNode* curr = root;
        
        for(int i=0;i<word.size();++i){
            bool f = 0;
            int idx = -1;
            for(int j=0;j<curr->child.size();++j){
                if(curr->child[j]->c == word[i]){
                    f = 1;
                    idx = j;
                    break;
                }
            }
            if(f){
                curr = curr->child[idx];
            }else{
                TrieNode* new_node = new TrieNode();
                new_node->c = word[i];
                curr->child.push_back(new_node);
                curr = new_node;
            }
        }
        curr->leaf = 1;
    }
    
    int m;
    int n;
    vector<string> ans;
    
    void dfs(vector<vector<char>> &b, int pos_m, int pos_n, TrieNode* node, string s){
        // out of range
        if(pos_m >= m || pos_m < 0 || pos_n >= n || pos_n < 0){
            return ;
        }
        
        char curr = b[pos_m][pos_n];
        TrieNode* next_node = nullptr;
        for(int i=0;i<node->child.size();++i){
            if(node->child[i]->c == curr){
                next_node = node->child[i];
                break;
            }
        }
        if(next_node == nullptr){
            return ;
        }
        s += curr;
        if(next_node->leaf){
            ans.push_back(s);
            next_node->leaf = 0;
        }
        
        b[pos_m][pos_n] = '.';
        // up || down || left || right
        dfs(b, pos_m-1, pos_n, next_node, s);
        dfs(b, pos_m+1, pos_n, next_node, s);
        dfs(b, pos_m, pos_n-1, next_node, s);
        dfs(b, pos_m, pos_n+1, next_node, s);
        b[pos_m][pos_n] = curr;
        
    }
    
    vector<string> findWords(vector<vector<char>>& board, vector<string>& words) {
        root = new TrieNode();
        for(int i=0;i<words.size();++i){
            insert(words[i]);
        }
        
        m = board.size();
        n = board[0].size();
        for(int i=0;i<m;++i){
            for(int j=0;j<n;++j){
                dfs(board, i, j, root, "");
            }
        }
        
        return ans;
    }
};
```

