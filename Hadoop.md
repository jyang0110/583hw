
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

