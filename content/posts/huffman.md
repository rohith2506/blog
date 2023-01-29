---
title: "Huffman Coding"
date: 2022-08-13T16:00:00+01:00
tags: ["programming", "c++"]
---

Sometimes it's just fun to solve some programming challenges to keep your problem solving skills sharp. I do this time to time for two reasons. One being to stay on top of my basic fundamentals and the other is to get better the language I am implementing the solution in. I have been exposed to professional C++ in the HFT space and my love for the language has rekindled again. So, I am gonna use my current sabbatical to become better at the language and also build some fun stuff all along


The first problem I thought which would be quite fun to implement was huffman coding. This is like the basic compression mechanism we have and it's pretty simple to implement. For the curious, Here's the [link](https://en.wikipedia.org/wiki/Huffman_coding)

```
#include <iostream>
#include <string>
#include <map>
#include <vector>
#include <queue>

struct TreeNode {
	int value;
	std::string key;
	TreeNode* left;
	TreeNode* right;
};

class compare {
	public:
	bool operator()(TreeNode* a, TreeNode* b) {
		return a->value > b->value;
	}
};

class HuffmanCoding {
private:
    std::map<char, int> frequency_map;
public:
    HuffmanCoding() {
    }

    TreeNode* merge(TreeNode* left, TreeNode* right) {
        TreeNode* new_node = new TreeNode();
        new_node->value = left->value + right->value;
        new_node->key = left->key + "_" + right->key;
        new_node->left = left;
        new_node->right = right;
        return new_node;
    }

    void populate_frequency(std::string input) {
        for (auto ch: input) {
            auto it = frequency_map.find(ch);
            if (it == frequency_map.end()) 
                frequency_map[ch] = 1;
            else
                frequency_map[ch] += 1;
        }
    }

    std::string encode_value_of_char(TreeNode* root_node, std::string key) {
        std::queue<std::pair<TreeNode*, std::string>> q;

        std::string result = "";
        q.push(std::make_pair(root_node, ""));
        while (!q.empty()) {
            auto current = q.front();
            q.pop();

            TreeNode* current_node = current.first;
            std::string current_result = current.second;

            if (current_node->key == key) {
                result = current_result;
                break;
            }

            if (current_node->left != nullptr)
                q.push(std::make_pair(current_node->left, current_result + '0'));
            if (current_node->right != nullptr)
                q.push(std::make_pair(current_node->right, current_result + '1'));
        }
                
        return result;
    }

    std::pair<TreeNode*, std::string> encode(std::string input) {

        // populate frequency 
        populate_frequency(input);

        // construct treenodes for each item in the frequency map
        std::priority_queue<TreeNode*, std::vector<TreeNode*>, compare> pq;
        for (auto it = frequency_map.begin(); it != frequency_map.end(); it++) {
            TreeNode* tree_node = new TreeNode();
            tree_node->value = it->second;
            tree_node->key = std::string(1, it->first);
            tree_node->left = nullptr;
            tree_node->right = nullptr;
            pq.push(tree_node);
        }

        // Use a priority queue to generate the top node 
        TreeNode* top_node = nullptr;
        while (!pq.empty()) {
            if (pq.size() == 1) {
                top_node = pq.top();
                pq.pop();
                break;
            }
            else {
                TreeNode* top1 = pq.top();
                pq.pop();
                TreeNode* top2 = pq.top();
                pq.pop();
                TreeNode* new_node = merge(top1, top2);
                pq.push(new_node);
            }
        }
        
        // Do the encoding
        std::string output;
        TreeNode* root_node = top_node;
        for (auto ch : input) {
            std::string encoded_value = encode_value_of_char(top_node, std::string(1, ch));
            output += encoded_value;
        }

        return make_pair(root_node, output);
    }


    std::string decode(TreeNode* root_node, const std::string& encoded_output) {
        std::string decoded_input = "";
        TreeNode* current_node = root_node;
        for (int index = 0; index < encoded_output.size(); index++) {
            if (encoded_output[index] == '0') {
                current_node = current_node->left;
            }
            else {
                current_node = current_node->right;
            }
            if (current_node->left == nullptr && current_node->right == nullptr) {
                decoded_input += current_node->key;
                current_node = root_node;
            }
        }
        return decoded_input;
    }
};

int main() {
    std::string input = "aaabcdd";
    HuffmanCoding* huffman_coding = new HuffmanCoding();
    auto [root_node, encoded_input] = huffman_coding->encode(input);
    std::cout << "encoded input: " << encoded_input << std::endl;

    std::string original = huffman_coding->decode(root_node, encoded_input);
    std::cout << "Original: " << original << std::endl;

    return 0;
}
```