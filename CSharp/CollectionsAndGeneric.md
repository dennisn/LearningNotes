# Collections And Generic

  - Reason: some algorithms/concepts, such as "sort", or "collection", can be applied to more than one types
    + For statistical-typed language, object type needs to be declared at compile time (for verification ?)
	+ Generic comes in to allow re-used of above agorithms: implementations against a yet-unknown type T with specified constraints
  - Trick to delay sorting of list: created the unsorted list, then assign sorted list as a lambda where sorting function is called
    ```
	public SortListObject(IEnumerable<T> data, IComparer<T> comparer)
	{
	  var unsorted = new List<T>(data);
	  this.SortedData = new(() => SortData(unsorted, comparer));
	}
	
	private static List<T> SortData(List<T> data, IComparer<T> comparer)
	{
	  data.Sort(comparer);
	  return data;
	}
	```
  -