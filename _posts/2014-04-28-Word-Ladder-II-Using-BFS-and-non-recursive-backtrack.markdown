---
layout: post
title:  "Word Ladder II using BFS and non-recursive backtracking"
date:   2014-04-28 22:41:10
categories: algorithm
tags: leetcode
---
Given a start string, end string and a dictionary, find all shortest paths to transform start to end. In each step we can transform only one letter. We assume all words are of the same length and contains only lowercase letters.

The problem and my solution are similar to [Word Ladder I]({% post_url 2014-04-27-Word-Ladder-Using-BFS %}) or [leetcode-word-ladder]. The only difference is in addition to finding shortest path, we also record predecessors of each nodes. Note that multiple nodes could be the predecessors of a node if they have the same distances.

The following solution consists of two steps, 1) finding shortest path and predecessors and 2) backtracking. To facilitate backtracking, BFS was running from `end` to `start`.

{% highlight cpp linenos %}
class Solution {
public:
	vector<vector<string>> findLadders(string start, string end, 
		unordered_set<string> &dict) {
		//create graph
		//my own dictionary. key: word; value: distance and list of predecessors
		unordered_map<string, pair<int, vector<string>>> d;
		d.reserve(dict.size());
		for (auto i : dict){
			//distance == -1 means current node is unvisited
			d.emplace(i, make_pair(-1, vector<string>()));
		}
		d.emplace(start, make_pair(-1, vector<string>()));
		d.emplace(end, make_pair(0, vector<string>()));			

		//run BFS
		queue<string> q;
		q.push(end);
		while (!q.empty()){
			auto word = q.front();
			if (word == start) break;//found shortest path, so terminate DFS
			q.pop();
			auto originalWord = word;
			auto newDist = d[word].first + 1;
			for (int i = 0; i < word.length(); ++i){
				auto originalChar = word[i];
				for (int j = 1; j < 26; ++j){
					word[i] = (originalChar - 'a' + j) % 26 + 'a';
					auto neighbor = d.find(word);
					if (neighbor != d.end()){
						if (neighbor->second.first == -1){
							q.push(word);
							neighbor->second.first = newDist;
						}
						if (neighbor->second.first == newDist){
							neighbor->second.second.push_back(originalWord);
						}
					}
				}
				word[i] = originalChar;
			}
		}
		return backtrack_result(start, end, d);
	}
private:
	vector<vector<string>> backtrack_result(string start, string end,
		unordered_map<string, pair<int, vector<string>>> &d){
		vector<vector<string>> result;
		for (auto i : d[start].second){
			result.push_back(vector<string>{start, i});
		}
		if (result.size()){
			while (result.front().back() != end){
				auto currentResultSize = result.size();
				for (int i = 0; i < currentResultSize; ++i){
					const auto &predecessors = d[result[i].back()].second;
					for (int j = 1; j < predecessors.size(); ++j){
						result.push_back(vector<string>(result[i]));
						result.back().push_back(predecessors[j]);
					}
					result[i].push_back(predecessors[0]);
				}
			}
		}
		return result;
	}
};
{% endhighlight %}

For online judge, see [leetcode-word-ladder-ii].

[leetcode-word-ladder]: http://oj.leetcode.com/problems/word-ladder/
[leetcode-word-ladder-ii]: http://oj.leetcode.com/problems/word-ladder-ii/