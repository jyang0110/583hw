
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
