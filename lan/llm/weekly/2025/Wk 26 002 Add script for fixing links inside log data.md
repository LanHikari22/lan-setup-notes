\#lan #llm #diagnostics #logging #st/todo

# 1 Add script for fixing links inside log data

# 2 Problem

I use the `Number Headings` plugin, and I have configured it to automatically number the headings. Because of this, the heading names change frequently, like if you just put a heading on top of them.

If I change the name of a heading, the links in the data also break. There are plugins like Dangling Links that may not be able to help because the links are actually inside data blocks like so:

````json
{
	my_str: "my_link"
}
````

For a concrete example, see

````simple-time-tracker-ex
{"entries":[{"name":"[[Wk 26 000 Add script for opening obsidian note quickly]]","startTime":"2025-06-23T09:33:35.000Z","endTime":"2025-06-23T11:00:00.000Z"}]}
````

I need to be able to fix these links. Ideally in a less flaky way (not depending on naming heuristics).

# 3 Objective

Create a script that can:

* [ ] Fix links within JSON
* [ ] If possible, adds note#link UUID metadata to the file generated with the JSON so that the links are preserved. Otherwise if there is a way to lookup old link names, then metadata is not necessary, and uses that instead.

Optional (Later):

* [ ] Search for all broken links in all notes

Note that the above JSON is generated via templater scripts such as `start_log.js`. The file will be provided.

# 4 Journal

## 4.1 Investigating metadata of links and note names

2025-06-24 Wk 26 Tue - 10:17

(Q1) Is there a plugin or is there a cache of some sort for link UUIDs? Or anything that would be able to do the search.

I have found a plugin before that claims to do something similar.

[<a name="1" />^1](Wk%2026%20002%20Add%20script%20for%20fixing%20links%20inside%20log%20data.md#1) [obsidian-persistent-links](https://github.com/ivan-lednev/obsidian-persistent-links) author states:

 > 
 > If a file got updated in some other way, and you've noticed some broken links, you can use the "Repair links in file" command to fix them. The plugin will search through the metadata cache and try to find a file that contains the block or heading in the link.

Let's try to look into this mechanism, maybe we can also use this "metadata cache".

We can also see that it is a ~~fork~~ of [obsidian-simple-plugin](https://github.com/obsidianmd/obsidian-sample-plugin) which is helpful if we want to create plugins!

Actually not a fork? It says `generated from`, I wonder how they do this in Github.

It seems there is a `use this template` button there! So I can configure the repo for this:
![Pasted image 20250624102808.png](../../../../attachments/Pasted%20image%2020250624102808.png)

The "Sponsor this project" section also can be learned from in [^1](Wk%2026%20002%20Add%20script%20for%20fixing%20links%20inside%20log%20data.md#1) :
![Pasted image 20250624102926.png](../../../../attachments/Pasted%20image%2020250624102926.png)

PR [\#9](https://github.com/ivan-lednev/obsidian-persistent-links/pull/9) indicates that behavior broke with recent obsidian versions. It is open and not merged, so when I tried the plugin and nothing happened, this might be why. This also might mean this solution is too dependent on fluctuating obsidian internals. The PR description:

 > 
 > The internal API "MetadataCache.getBacklinksForFile" seemed to have change its return value from a plain Object to a Map at some point. I'm currently running Obsidian 1.7.4, so I guess 1.7 changed this behaviour.

# 5 References

1. [obsidian-persistent-links](https://github.com/ivan-lednev/obsidian-persistent-links) ^1
1. [obsidian-simple-plugin](https://github.com/obsidianmd/obsidian-sample-plugin) <a name="2" />^2

````mermaid
graph TD

%% Nodes
A1[^1 obsidian-persistent-links]
A2[^2 obsidian-simple-plugin]

%% Settings
classDef note fill:#f9f9a6,stroke:#333,stroke-width:1px,color:#000,font-style:italic;

%% Connections
A2 --> |generates| A1
````
