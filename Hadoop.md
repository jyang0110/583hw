
# Assiggment 5 report


## Code explanation

# PageRankDriver

```java
	public static void composite(String input, String output, String interim1,
			String interim2, String diff, int reducers) throws Exception {
		/*
		 * TODO 
		 */
		System.out.println("Junyan Yang (10456378)");
		
		int counter = 0;
```

I changed my name here. 

# diffmap
```java
		if (sections.length == 2){ //check tbe length
			context.write(new Text(sections[0].trim()),new Text(sections[1].trim()));
		}
```
I check the length of the input and split node-rank pair then emit them

# diffRed
```java
		int i = 0;
		for (Text val: values){
			String tmp = val.toString();
			ranks[i++] = Double.valueOf(tmp);
			if (i >= 2) break;
		}
		context.write(new Text(String.valueOf(Math.abs(ranks[0] - ranks[1]))), new Text());
```
get values and write values


# finmapper
```java
		String[] sections = line.split("\t"); //split the lines here

		if(sections.length <2) {  //if format is correct
			context.write(new DoubleWritable(0 - Double.valueOf(sections[1])), new Text(sections[0]));
		}
```
check the format of the pairs and write it 

# finReducer
```java
		Iterator<Text> iterator = values.iterator();
		String node;
		while(iterator.hasNext()) {
			node = iterator.next().toString();
			context.write(new Text(node), new Text(String.valueOf(0 - key.get())));
		}
```
loop through the inputs and emit value-rank pair

# initReducer
```java
		int defualtrank = 1;
		Iterator<Text> v = values.iterator();
		while(v.hasNext()) {
			context.write(new Text(key + "+" + defualtrank), v.next());
		}
```
output adjacency list 

# IterMapper
```java
		String[] nr = sections[0].split("\\+"); // split node+rank
		String node = String.valueOf(nr[0]);
		double rank = Double.valueOf(nr[1]);
		String[] adjList = sections[1].split(" ");

		double weight = rank / adjList.length;
		
		for (int i = 0; i < adjList.length; i++){
			context.write(new Text(adjList[i]), new Text(String.valueOf(weight)));
		}
		context.write(new Text(node), new Text("ADJ:" + sections[1]));
```
split value rank pair and output it

# IterReducer

```java
		Iterator<Text> iterator = values.iterator();
		double currentRank = 0; 
		String ajlist = "";
		while(iterator.hasNext()) {
			String line = iterator.next().toString();
			if(!line.startsWith("ADJ")) {
				currentRank += Double.valueOf(line);
			} else {
				ajlist = line.replaceAll("ADJ", "");
			}
		}
		currentRank = 1 - d + currentRank * d;
		context.write(new Text(key + "+" + currentRank), new Text(ajlist));
```
split out rank value and output

## testing
create local file graph.txt 
```bash
1 : 2 3
2 : 4
3 : 1 4 5
5 : 1 4
```
name.txt
```bash
1: v1
2: v2
3: v3
4: v4
5: v5
```
