---
layout: post
title:  "Word Ladder using BFS"
date:   2014-04-27 20:48:29
categories: algorithm
tags: leetcode
---
Given a start string, end string and a dictionary, find the shortest steps to transform start to end. In each step we can transform only one letter. We assume all words are of the same length and contains only lowercase letters.

My first thought was to scan dictionary to find all possible pair of words who are neighbour and use BFS. That approach proved to be slow (`O(n^2)` where n is the size of dictionary) and could not pass [online judge](http://oj.leetcode.com/problems/word-ladder/).

After some frustration, I took another approach, we can generate all possible neighbors of a word and find whether it was in the dictionary. It only took `26*|w|` (w is word length) to generate all possible neighbors of a word. Since each word in dictionary will only be accessed once, the time complexity is `O(w*n)`, which is better if length of word is constant.

The following solution was based on that approach as well as BFS.
{% highlight cpp linenos %}
class Solution {
	bool areNeighbor(const string &s1, const string &s2) const{
		if (s1.size() == 0 || s1.size() != s2.size()) return false;
		int differentChar = 0;
		for (int i = 0; i < s1.size(); ++i){
			differentChar += s1[i] != s2[i];
			if (differentChar >1) return false;
		}
		return differentChar == 1;
	}
public:
	int ladderLength(string start, string end, unordered_set<string> &d) {
		if (start == end) return 0;
		queue<string> q;
		//distance to start string, corresponding to element in q
		queue<int> dist;
		q.push(start);
		dist.push(1);
		while (!q.empty()){
			auto newDist = dist.front() + 1;
			auto word = q.front();
			dist.pop();
			q.pop();
			if (areNeighbor(end, word)) return newDist;
			//find all possible neigbhors
			int wordLen = word.length();
			for (int i = 0; i < wordLen; ++i){
				auto original = word[i];
				for (int j = 1; j < 26; ++j){
					word[i] = (original - 'a' + j) % 26 + 'a';
					auto target = d.find(word);
					if (target != d.end()){
						//erase from dictionary so that won't access it again
						d.erase(target);
						q.push(word);
						dist.push(newDist);
					}
				}
				word[i] = original;
			}
		}
		return 0;
	}
};
{% endhighlight %}

For online judge, see [leetcode-word-ladder].

[leetcode-word-ladder]: http://oj.leetcode.com/problems/word-ladder/