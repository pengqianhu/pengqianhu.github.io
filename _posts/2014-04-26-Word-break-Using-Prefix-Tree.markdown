---
layout: post
title:  "Word Break using prefix tree"
date:   2014-04-13 20:33:17
categories: algorithm
---
Given a string and a dictionary, decide whether there exists an valid break of the string based on the dictionary.

The following is a non-recursive solution. Worse case running time is `O(|w|*n + |dict|)` where `|w|` is the length of longest of the word, n is string length and `|dict|` is the number of characters in dictionary. For small dictionary and constant size of length of words, the time complexity is `O(n)`.
{% highlight cpp linenos %}
class Solution {
public:
	struct Node;
	bool wordBreak(string s, unordered_set<string> &dict) {
		if (dict.empty()) return false;
		auto root = initTree(dict);
		return root->match(s, matches);
	}
private:
	set<int> matches;//store position last match
	Node *initTree(unordered_set<string> &dict){
		Node *root = new Node();
		for (auto &item : dict){
			root->insert(item);
		}
		return root;
	}
	struct Node{
		bool hasVal;
		Node *next[26];//assume input contains lower case letter only
		Node(bool hasVal = false) : hasVal(hasVal){
			for (int i = 0; i < 26; ++i){
				this->next[i] = NULL;
			}
		}
		void insert(const string &s){
			auto p = this;
			for (auto i : s){
				if (!p->next[i - 'a']){
					p->next[i - 'a'] = new Node();
				}
				p = p->next[i - 'a'];
			}
			p->hasVal = true;
		}
		//s[0, startIndex - 1](inclusive) has been matched;
		bool match(const string &s, set<int> &matches){
			int startIndex = 0;
			int LEN = s.length();
			matches.clear();
			matches.insert(-1);
			while (!matches.empty()){
				auto p = this;
				startIndex = *matches.begin() + 1;
				matches.erase(matches.begin());
				for (; startIndex < s.size(); ++startIndex){
					auto nextNode = p->next[s[startIndex] - 'a'];
					if (nextNode){
						p = nextNode;
						if (p->hasVal){
							if (startIndex == LEN - 1) return true;							
							if (matches.find(startIndex) == matches.end())
								matches.insert(startIndex);
						}
					}
					else{
						break;
					}
				}
			}
			return false;
		}
	};
};
{% endhighlight %}

For online judge, see [leetcode-word-break].

[leetcode-word-break]: http://oj.leetcode.com/problems/word-break/
