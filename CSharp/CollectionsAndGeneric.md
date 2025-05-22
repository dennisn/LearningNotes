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
  - Co-variant vs. contra-variant vs. in-variant:
    + Co-variant: 
      - type parameter is used only as a return type of interface methods
      - type parameter is not used as a generic constraint for the interface methods
      - Can assign subtype to super-type (i.e. more specific template can be used as more general template)
      ```
      interface ICovariant<out R> {}
      class Sample<R> : ICovariant<R> { }
      
      ICovariant<Object> iobj = new Sample<Object>();
      ICovariant<String> istr = new Sample<String>();

      // You can assign istr to iobj because
      // the ICovariant interface is covariant.
      iobj = istr;
      ```
    + Contr-variant: reverse of variant
      ```
      interface IContravariant<in A> {}
      class Sample<A> : IContravariant<A> {}
      
      IContravariant<Object> iobj = new Sample<Object>();
      IContravariant<String> istr = new Sample<String>();

      // You can assign iobj to istr because
      // the IContravariant interface is contravariant.
      istr = iobj;
      ```
    + In-variant: Doesn't allow assignment to subtype or supertype
      - NOTE: the type instance can still be of subtype, only the generic instance can't be assigned to generic subtype/supertype
  - Generic type inference : mechanism where a compiler automatically determines the specific types used with a generic type or method
    + Simplify coding and enhance readability
    + Can be used to totally hide all trace of generics in consuming code