# C# Events, Delegates, and Lambda
  - Sample repo: https://github.com/DanWahlin/Csharp-Events-Delegates-Lambdas

## Role of Events, Delegates, and Event Handlers
  - Event: notification from end-user or objects
    + Main purpose is provide opportunity for others to listen & act on the event (with its argument)
  - Delegate: is a specialized class based on the `MulticastDelegate` base class
    + Function pointer: Act as the pipeline between an event and an event handler
  - Event Handler: receive & process data from an event (i.e. sender & EventArgs)
    + EventArgs are responsibilve for encapsulating event data

## Create Delegates, Events, and EventArgs
  - Create delegate: as an external class
    + Example: `public delegate int WorkPerformedHandler(int hours, WorkType worktype);`
    + **NOTE**: delegate can be chained (e.g. concat via `+=`)
  - Define an event
    + Example: `public event WorkPerformaedHandler? WorkPerformed;`
  - Raising Event: simply calling the event with its arguments (after null check)
    + Example: `WorkPerformed?.Invoke(8, WorkType.GenerateReports);`
  - Create an EventArgs class
    + custom EventArgs class to encapsulate data that is sent when the event is raised
  - Generic ways (instead of using `delegate`)
    ```
    public event EventHandler<CustomisedEventArgs>? SomeEvent;
    
    // to call
    SomeEvent?.Invoke(this, new CustomisedEventArgs(arg1, arg2))
    ```

## Handling Events
  - Normal way to instantiate delegate: 
    + Example: `worker.WorkPerformed += new EventHandler<CustomisedEventArgs>(some_method);`
    + To reverse the instantiation, use `-=`
  - delegate inference: simplify the delegate instatiation
    + Example: `worker.WorkPerformed += some_method`
    + As long as `some_method` matching the expected signature of event `WorkPerformed`

## Lambdas, Action<T>, and Func<T, TResult>
  - Lambda: short-hand way of define functions matching a delegate signature
  - `Action<T>`: basic delegate, accepts one ore more parameters and return no value
  - `Func<T, TResult>`: accepts one ore more parameters and return a value of type TResult
    + Multiple params, then the last param is the return type
  ==> delegate almost not needed ! (Maybe useful for declaration)