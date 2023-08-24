# Elysium Game Studio Unreal 5 Coding Conventions

This document summarizes the high-level coding conventions for writing Unreal client code at Elysium Game Studio. They are based on the [DaedalicEntertainment coding conventions](https://github.com/DaedalicEntertainment/unreal-coding-conventions).

The goal is to make it easier to work in similar teams inside and outside the company, as well as have client code blend in with other code of the Unreal API. We are providing a complete summary here in order to allow people to understand the conventions at a glance, instead of having to open multiple documents. Our coding conventions are numbered, which makes it easier to refer to them in code reviews.

## 1. Names

1.1. __DO__ add a prefix to all class and struct names, based on your project name.


## 2. Files

2.1. __DO__ use PascalCase for file names, with the project name prefix, but without the type prefix (e.g. `AHOATCharacter` goes into `HOATCharacter.h`) __IF__ files contain declaration or definition of classes with corresponding name.

2.2. __DO__ define classes with the following structure:

* public constants
* constructors
* destructor
* public `UFUNCTION`s
* public methods (try to keep virtual methods, event handlers and regular functions groups together in their own section)
* operators
* public `UPROPERTY`s
* public fields
* protected constants
* protected `UFUNCTION`s
* protected methods (same consideration as public ones: try to separate virtual from non-virtual ones)
* protected `UPROPERTY`s
* protected fields
* private constants
* private `UFUNCTION`s
* private methods
* private `UPROPERTY`s
* private fields

Within each of these groups, order members by logical groups when appropriate.

## 3. Includes

3.1. __DO_NOT__ add additional dependencies if such can be avoided with forward declarations. If some dependencies in a header file can be avoided by moving some logic to the corresponding .cpp file, it must be done.

3.2. __DO__ prefer using inline forawrd declaration over forward declaration list at the top of the file. It's hard to keep all the list entries relevant. It tends to grow but never shrinks and acquires "dead" forward declarations. The foraward declaration list is still acceptable if the header contains plenty of declared name usages (at least 3 or more). Enums forward declarations are exceptions as they require underlying type specification in forward declarations.

3.3. __DO NOT__ include Unreal Engine's header files. All Unreal Engine's header files must be included in the precompiled header.

## 4. Classes & Structs

4.1. __DO__ use PascalCase for class names, e.g. `AHOATCharacter`.

4.2. __DO__ add type prefixes to class names to distinguish them from variable names. For example, `FSkin` is a type name, and `Skin` is an instance of a `FSkin`. UnrealHeaderTool requires the correct prefixes in most cases, so it's important to provide them.

* Template classes are prefixed by `T`. 
* Classes that inherit from `UObject` are prefixed by `U`. 
* Classes that inherit from `AActor` are prefixed by `A`. 
* Classes that inherit from `SWidget `are prefixed by `S`. 
* Classes that are abstract interfaces are prefixed by `I`. 
* Enums are prefixed by `E`. 
* Most other classes are prefixed by `F`, though some subsystems use other letters. 

4.3. __DO__ prefix boolean variables by `b` (e.g. `bPendingDestruction` or `bHasFadedIn`).

4.4. __DO__ uppercase the first letter of acronyms, only, e.g. `FHOATXmlStreamReader`, not `FHOATXMLStreamReader`.

4.5. __DO__ use `struct`s for data containers, only. They shouldn't contain any logic beyond simple validation or need any destructors.

## 5. Constructors

5.1 __CONSIDER__ defining a deleted copy constructor/copy assignment operator for classes and structs that should not be copied (e.g. ones that contain a lot of data).

## 6. Functions

6.1. __DO__ use PascalCase for functions names.

6.2. __DO__ prefix function parameter names with `Out` if they are passed by reference and the function is expected to write to that value. This makes it obvious that the value passed in this argument will be replaced by the function. If an `Out` parameter is also a boolean, put the `b` before the `Out` prefix, e.g. `bOutResult`.

6.3. __CONSIDER__ writing functions with six parameters or less. For passing more arguments, pass instead a `struct` containing all the parameters (which sensible defaulted values), and/or refactor your function.

6.4. __AVOID__ providing function implementations in header files. Use inline functions judiciously, as they force rebuilds even in files which don't use them. Inlining should only be used for trivial accessors and when profiling shows there is a benefit to doing so. All code and local variables will be expanded out into the calling function and will cause the same build time problems caused by large functions.

6.5. __AVOID__ using BlueprintPure for functions that do non-trivial amount of work. Keep in mind that Pure functions don't cache their results, so it's easy to do a lot of duplicated work by misusing them in Blueprints.

## 7. Variables

7.1. __DO__ use PascalCase for variable names.

7.2. __AVOID__ short or meaningless names (e.g. `A`, `Rbarr`, `Nughdeget`). Single character variable names are only okay for counters and temporaries, where the purpose of the variable is obvious. An exception to this rule are mathematical functions where the meaning of the parameters is "universally" known, such as `Lerp(float A, float B, float T)` or `Sin(float X)`.

7.3. __AVOID__ using negative names for boolean variables.
```
    // Right:
    if (bVisible)

    // Wrong: Double negation is hard to read.
    if (!bInvisible)
```
## 8. if statements decoration

8.1. __DO_NOT__ use curly braces if the body of a conditional statement contains just one line:
```
      // Wrong:
      if (bVisible)
      {
          Hide();
      }

      // Right
      if (bVisible) Hide();

      // Right, if you have a long condition expression
      if (bVisible && bWantsToHide && bCanAffordTo && bStartsAlligned && bIsFullMoon)
          Hide();

      // Right, if the 'true' statement is long
      if (bVisible)
            Hide(GetCoverSubsystem::Get().FindMeANeatPlaceToHidePlease(GetActorLocation()));
```
8.2. __DO__ use curly braces for a single-lined true statement if 'false' block consists from more than 1 line:
```
      // Wrong:
      if (bVisible)
            Hide();
      else
      {
            Unhide();
            SurpriseYourEnemies();
      }

      // Right:
      if (bVisible)
      {
            Hide();
      }
      else
      {
            Unhide();
            SurpriseYourEnemies();
      }

      // Right:
      if (bVisible)
      {
            Unhide();
            SurpriseYourEnemies();
      }
      else
            Hide();
 ```   

## 9. Parentheses

9.1. __DO__ use parentheses to group expressions:
```
      // Right:
      if ((a && b) || c)

      // Wrong: Operator precedence is not immediately clear.
      if (a && b || c)
```

## 10. Control Flow

10.1. __DO__ add a `break` (or `return`) statement at the end of every `case`, or use `[[fallthrough]]`
```
      switch (MyEnumValue)
      {
        case Value1:
            DoSomething();
            break;

        case Value2:
        case Value3:
            DoSomethingElse();
            [[fallthrough]];

        default:
            DefaultHandling();
            break;
      }
```     
10.1.1. __DO__ wrap switch cases in parentheses whenever you declare variables inside them
```
      switch (MyVar)
      {
        case 42:
        {
            int X = 0;
            Foo(X);
            break;
        }
        
        default:
            break;
      }
```      
10.2. __DO NOT__ put `else` after jump statements:
```
      // Right:
      if (ThisOrThat) return;
      SomethingElse();

      // Wrong: Causes unnecessary indentation of the whole else block.
      if (ThisOrThat)
          return;
      else
          SomethingElse();
```
## 11. General Style

11.1. __AVOID__ too many indentation levels. If your function has a lot of indentation, you have 2 options:

* if the high nesting is just due to error checking, consider replacing nesting with early returns/continues:
```
        // Bad
        if (Actor) 
        {
           if (IsAvailable(Actor))
           {
               for (AActor* OtherActor : Others)
               {
                   if (IsAvailable(OtherActor))
                   {
                       // ...
                   }
               }
           }
        }
        
        // Good
        if (!Actor || !IsAvailable(Actor)) return;
        
        for (AActor* OtherActor : Others)
        {
              if (!IsAvailable(OtherActor)) continue;
              // ...
        }
```        
## 12. Language Features

12.1. __AVOID__ using the `auto` keyword except when required by the language (e.g. assigning lambdas) or when assigning long iterator types. If in doubt, for example if using `auto` could make the code less readable, do not use `auto`.
```
      // Bad
      auto* HealthComponent = FindComponentByClass<USOCHealthComponent>();
      
      // Good
      USOCHealthComponent* HealthComponent = FindComponentByClass<USOCHealthComponent>();
      
      TMap<UAVeryVeryLongTypeHere, FAnotherVeryLongTypeThere> Map;
      for (auto it = begin(Map); it != end(Map); ++it) { ... }
```
12.2. __DO__ use `auto*` for auto pointers, to be consistent with references, and to add additional guidance for the reader. Remember that `const auto*` and `const auto` mean different things when the type resolves to a pointer (and you usually want `const auto*`).

12.3. __DO__ use proprietary types, such as `TArray` or `TMap` where possible. This avoids unnecessary and repeated type conversion while interacting with the Unreal Engine APIs.

12.4. __DO__ use native C++ wide string literals L"Like this one" instead of wrapped with TEXT() macros literals

## 13 Casting

13.1. __DO__ use CastChecked<> instead of static_cast or Cast<> when casting to a type that is guaranteed to be correct. (i.e. if you don't need to check whether the object is really of that type AND the object is guaranteed not to be null).

13.2. __DO NOT__ use `dynamic_cast` ever. Use `Cast` instead.

13.3. __DO__ use C-style cast instead of static_cast when cast to a class not derived from UObject.

## 14. Events & Delegates

14.1. __DO__ define two functions when exposing an event to a subclass. The first function should be virtual and its name should begin with `Notify`. The second function should be a `BlueprintImplementableEvent UFUNCTION` and its name should begin with `Receive`. The default implementation of the virtual function should be to call the `BlueprintImplementableEvent` function (see `AActor::NotifyActorBeginOverlap` and `AActor::ReceiveActorBeginOverlap` for example).

14.2. __DO__ call the virtual function before broadcasting the event, if both are defined (see `UPrimitiveComponent::BeginComponentOverlap` for example).

Example:
```
    DECLARE_DYNAMIC_MULTICAST_DELEGATE_ThreeParams(FHoatActorGraphConnectivityChangedSignature, AActor*, Source, AActor*, Target, float, Distance);

    /** Event when the connectivity of an observed source vertex has changed. */
    virtual void NotifyOnConnectivityChanged(AActor* Source, AActor* Target, float Distance);

    /** Event when the connectivity of an observed source vertex has changed. */
    UFUNCTION(BlueprintImplementableEvent, Category = Graph, meta = (DisplayName = "OnConnectivityChanged"))
    void ReceiveOnConnectivityChanged(AActor* Source, AActor* Target, float Distance);

    /** Event when the connectivity of an observed source vertex has changed. */
    UPROPERTY(BlueprintAssignable)
    FHoatActorGraphConnectivityChangedSignature OnConnectivityChanged;


    void ASOCActorGraph::NotifyOnConnectivityChanged(AActor* Source, AActor* Target, float Distance)
    {
        ReceiveOnConnectivityChanged(Source, Target, Distance);
        OnConnectivityChanged.Broadcast(Source, Target, Distance);

        SOC_LOG(hoat, Log, TEXT("%s changed the connectivity of vertex %s: Distance to target %s changed to %f."),
                *GetName(), *Source->GetName(), *Target->GetName(), Distance);
    }
```
## 15. Comments

15.1. __DO__ add a space after `//`.

15.2. __DO__ place the comment on a separate line, not at the end of a line of code.

15.3. __DO__ write API documentation with [Javadoc comments](https://docs.unrealengine.com/latest/INT/Programming/Development/CodingStandard/index.html#exampleformatting).

## 16. Invalid state handling

16.1. We __DO NOT__ check a pointer for null because it’s a pointer and it would be nice to check it for null and return from function just in case.
Every validation of a function parameters or a function return value must be rationalised by the following policy.

Every function is implied to have a range of valid parameters it accepts and values it returns.
Execution of a function with invalid parameters means the program has entered invalid state.
A function returning an unexpected value means the program has entered invalid state.
There are two major reasons why programs can enter invalid state: code logic error and invalid input data.
In general, we __DO NOT__ maintain invalid states. It breeds a lot of code unrelated to game logic and in the end we don’t want our game to continue running if it entered an invalid state. It can severely break gameplay experience or even corrupt save data. If the game has entered an invalid state the further execution must be terminated. However, there are exceptions.
Crash recovery in the Editor during development consumes a lot of time. Therefore we __DO__ maintain invalid states caused by invalid input data in all non-shipping builds. Maintaining invalid state means the session execution must continue and crash should be avoided. The program is still considered being in an invalid state, thus in case of PIE the session should be stopped as soon as possible and the invalid state should be maintained until the PIE session is safely terminated. Maintenance beyond the PIE session safe termination point is not required.
If data is validated before session start, the data is considered always valid and thus the code that works with the data should not maintain invalid input data state. One way to perform data validation is UObject::IsDataValid implementation. If such data validation is present the data is considered to be completely valid. In each particular case we either implement data validation OR invalid state maintenance, not both at the same time.
In the perfect world we don’t want to have partial data validation or partial invalid state maintenance, but in reality full data validation and complete invalid state maintenance is tedious and in most cases is just overkill. Thus, the exact degree of maintenance and validation in each particular case is a state of art and defined by the implementer.

16.2. How do we implement the policy:
The native Unreal Engine tools do not deliver a convenient way to implement the policy, thus we implement our own:

```
UOutData* Func(UInputData* InputData)
{
If (!Assert(InputData)) return nullptr;
	…
}

UOutData* Func(UInputData* InputData)
{
	TArray<int> Values = InputData->GetValues();
      if (!Assert(Values.Num() >= 3, "Optional message. Values Num = %d", Values.Num())) return nullptr;
      …
}
```
The code has the following behaviour:
- If the assertion expression is evaluated to false in a non-shipping build:
      * If a debugger is present, “debug break” will be triggered.
      * A log entry will be generated in the LogOutputDevice category.
      * If it’s a PIE session, a PIE error entry will be generated that will be shown in the end the PIE session.
      * A message dialog will be shown with the error message, asking whether to interrupt the session or continue.
      * If asked to interrupt the session, an interruption request will be posted. The PIE session keeps going until it can safly interrupt the session.
      * The same Assert can fail again only after 1 second after the dialog choice from the previous failure. (Failure spam protection)
- In the shipping build the assertion expression is not evaluated. It’s always considered true.

Difference with Unreal’s ensure:
- ensure evaluates the expression in all builds
- ensure does not generate PIE session error
- ensure has no message dialogs or any form of signals, except a log entry. It’s very easy to miss or ignore
- ensure doesn’t interrupt the execution session

When branching code with Assert, we __DO__ prefer ‘true’ route for invalid state maintenance:
```
void Func(UInputData* InputData)
{
	…
	UObject* ObjA = InputData->GetObjectA();
If (!Assert(ObjA)) return nullptr;  // Preferred
	…
	UObject* ObjB = InputData->GetObjectB();
	If (Assert(ObjB))  // Ineligible
{
…
}
}
```

