### 1. Trie

&emsp;&emsp; <img src="../img/trie.png" width=400>

前缀树:

-   边代表字母
-   支持 `insert`、`search`、`startsWith`

??? adcodes "Trie"

    ```cpp
    class Trie {
    private:
        struct TrieNode {
            vector<unique_ptr<TrieNode>> children;
            bool isEnd;
            TrieNode() : children(26), isEnd(false) { }
        };

        unique_ptr<TrieNode> root;

        TrieNode* _searchPrefix(string word) {
            TrieNode* node = root.get();
            for (char c : word) {
                int idx = c - 'a';
                if (node->children[idx] == nullptr) {
                    return nullptr;
                }
                node = node->children[idx].get();
            }
            return node;
        }

    public:
        Trie() : root(new TrieNode()) { }

        void insert(string word) {
            TrieNode* node = root.get();
            for (char c : word) {
                int idx = c - 'a';
                if (node->children[idx] == nullptr) {
                    node->children[idx].reset(new TrieNode());
                }
                node = node->children[idx].get();
            }
            node->isEnd = true;
        }

        bool search(string word) {
            TrieNode* node = _searchPrefix(word);
            return node && node->isEnd;
        }

        bool startsWith(string prefix) {
            return _searchPrefix(prefix);
        }
    };
    ```
