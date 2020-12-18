
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
# test locally
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
And use the command to run the desired program. 
```bash
hadoop jar PageRank.jar edu.stevens.cs549.hadoop.pagerank.PageRankDriver composite /input /output inter1 inter2 diffdir 10

```
I change my jar file name shorter for easier command

Then got output similar to this
```bash
v4	1.708333333333333
v1	0.8583333333333333
v2	0.575
v3	0.575
v5	0.43333333333333335
```
# test with EMR
First upload files with s3
Then you setup cluster in EMR 
set up the steps then run
setup video is available 

output looks like this
```bash
United_States_postal_abbreviations	13662.8115005250872
United_States	12323.8691079946515
Geographic_coordinate_system	11934.2845297552677 
Biography	8375.8420374471434 
2008	7608.3424234776366
2007	6225.1269737910999
United_Kingdom	4962.2094419805134 
Music_genre	4843.6978424964122 
France	4770.4204330560325
Record_label	4618.266925364475
Biological_classification	4463.1689953240562
England	4004.777736146966
Canada	3770.645114335918 
Personal_name	3593.3883106652829
2006	3555.2705508509173 
Internet_Movie_Database	3337.5717770463823 
India	3236.9415623260545 
Binomial_nomenclature	2943.8165363549141
Germany	2854.4643625263919
Australia	2817.9105111842829 
2005	2755.0642862500629
Japan	2738.5863561278975
Studio_album	2718.814064216729
Village	2615.635419472619
Record_producer	2485.099852834015
Football_(soccer)	2353.706850899679
Politician	2125.971319436932 
Romania	2081.806778455117
English_language	2066.932874975084 
Time_zone	1946.043089990925 
Departments_of_France	1907.073774561458
Wiktionary	1902.205683927874
Geocode	1861.244205536404  
UN/LOCODE	1860.412366015909
2004	1855.809915880652
Television	1849.752611566633
Italy	1811.154093703112
Europe	1784.682031773611
Album	1721.771209240686
Conservation_status	1714.069351391927
Website	1706.811096801126
Animal	1702.005627728579
London	1701.592745259262
IUCN_Red_List	1695.572245405303
Wikimedia_Commons	1686.065818552500
Poland	1626.047082990649
Population_density	1594.162248708802
Public_domain	1559.170958665137
Actor	1472.403175639216
Digital_object_identifier	1468.027942446977
2001	1449.314660155899
Elevation	1359.914311957607
Norway	1307.670141770393
China	1293.801665296237
School	1230.683893187537
```

# test with different reducers
1 master, 4 cores, spent 13 mins.
1 master, 7 cores, spent 7 mins 
1 master, 9 cores, spent 6 mins
1 master, 13 cores, spent 4 mins
Obviously using more reducer spends less time and there is small difference between 9 cores and 13 cores. Howver, i dont see much difference on the result part. So using more reducer may just help on the time cost.
