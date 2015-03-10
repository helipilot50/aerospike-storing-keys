# Storing Keys in Aerospike

##Problem
You want to store a list of Aerospike Key objects in an Aerospike Bin, but you get a Serialization error.

##Solution
Convert the Key information to a Map. Store the map in at list ans then store the List in an Aerospike Bin.

The source code for this solution is available on GitHub, and the README.md 
https://github.com/helipilot50/aerospike-storing-keys.git 


##Discussion

The `work()` method constructs 20 keys and puts them into a list. During construction the `makeMapFromKey()` method converts the key into a Map.

When the Bin is created the factory method `Value.getAsList()` is used to create an Aerospike List with nested Maps.

```java

	public void work() throws Exception {
		List<Map<String, Object>> keys = new ArrayList<Map<String, Object>>();
		for (int i = 0; i < 20; i++){
			Key key = new Key(this.namespace, this.set, "key-to-test-with-"+i);
			keys.add(makeMapFromKey(key));
		}
		
		Key cacheKey = new Key(this.namespace, this.set, "cache-key");
		
		Bin include = new Bin(BIN_INCLUDE, Value.getAsList(keys));
		client.put(new WritePolicy(), cacheKey, include);
	}
	
	private Map<String, Object> makeMapFromKey(Key key){
		Map<String, Object> keyMap = new HashMap<String, Object>(4);
			keyMap.put("namespace", key.namespace);
			keyMap.put("setname", key.setName);
			keyMap.put("userkey", key.userKey);
			keyMap.put("digest",key.digest);
		return keyMap;
	}

```
