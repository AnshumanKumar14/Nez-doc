# [AI](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI) \([FSM](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/FSM), [Behavior Tree](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/BehaviorTree), [GOAP](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/GOAP), [Utility AI](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/UtilityAI)\)

## [AI](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI)

[Nez](https://github.com/prime31/Nez) includes several different options for setting up AI ranging from a super simple transitionless finite state machine \(FSM\) to extendable behavior trees to ultra-flexible Utility Based AI. You can mix and match them as you see fit.

## [Simple State Machine](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/FSM/SimpleStateMachine.cs)

The fastest and easiest way to get AI up and running. [Simple State Machine](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/FSM/SimpleStateMachine.cs) is a Component subclass that lets you set an `enum` as its generic constraint and it will use that `enum` to control the state machine. The `enum` values each map to a state and can have optional enter/tick/exit methods. The naming conventions for these methods are best shown with an example:

```csharp
enum SomeEnum
{
    Walking,
    Idle
}

public class YourClass : SimpleStateMachine<SomeEnum>()
{
    void OnAddedToEntity()
    {
        initialState = SomeEnum.Idle;
    }

    void Walking_Enter() {}
    void Walking_Tick() {}
    void Walking_Exit() {}

    void Idle_Enter() {}
    void Idle_Tick() {}
    void Idle_Exit() {}
}
```

## [State Machine](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/FSM/StateMachine.cs)

The next step up is [State Machine](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/FSM/StateMachine.cs) which implements the "states as objects" pattern. [State Machine](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/FSM/StateMachine.cs) uses separate classes for each state so it is a better choice for more complex systems.

We start to get into the concept of a **context** with [State Machine](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/FSM/StateMachine.cs). In coding, the context is just the class used to satisfy a generic constraint. in a `List<string>` the _string_ would be the context class, the class that the list operates on. With all of the rest of the AI solutions you get to specify the context class. It could be your `Enemy` class, `Player` class or a helper object that contains any information relevant to your AI \(such as the `Player`, a list of Enemies, navigation information, etc\).

Here is a simple example showing the usage \(with the `State` subclasses omitted for brevity\):

```csharp
// create a state machine that will work with an object of type SomeClass as the focus with an initial state of PatrollingState
var machine = new SKStateMachine<SomeClass>( someClass, new PatrollingState() );

// we can now add any additional states
machine.AddState( new AttackState() );
machine.AddState( new ChaseState() );

// this method would typically be called in an update of an object
machine.Update( Time.deltaTime );

// change states. the state machine will automatically create and cache an instance of the class (in this case ChasingState)
machine.ChangeState<ChasingState>();
```

## [Behavior Trees](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/BehaviorTree)

The de facto standard for composing AI for the last decade. Behavior trees are composed of a tree of nodes. Nodes can make decisions and perform actions based on the state of the world. Nez includes a [BehaviorTreeBuilder](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/BehaviorTree/BehaviorTreeBuilder.cs) class that provides a fluent API for setting up a [behavior tree](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/BehaviorTree/BehaviorTree.cs). The [BehaviorTreeBuilder](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/BehaviorTree/BehaviorTree.cs) is a great way to reduce the barrier of entry to using behavior trees and get up and running quickly.

### [Composites](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/BehaviorTree/Composites)

[Composites](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/BehaviorTree/Composites) are parent nodes in a behavior tree. They house 1 or more children and execute them in different ways.

* [Sequence](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/BehaviorTree/Composites/Sequence.cs): returns failure as soon as one of its children returns failure. If a child returns success it will sequentially run the next child in the next tick of the tree.
* [Selector](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/BehaviorTree/Composites/Selector.cs): returns success as soon as one of its child tasks return success. If a child task returns failure then it will sequentially run the next child in the next tick.
* [Parallel](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/BehaviorTree/Composites/Parallel.cs): runs each child until a child returns failure. It differs from `Sequence` only in that it runs all children every tick
* [ParallelSelector](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/BehaviorTree/Composites/ParallelSelector.cs): like a `Selector` except it will run all children every tick
* [RandomSequence](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/BehaviorTree/Composites/RandomSelector.cs): a [Sequence](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/BehaviorTree/Composites/Sequence.cs) that shuffles its children before executing
* [RandomSelector](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/BehaviorTree/Composites/RandomSelector.cs): a [Selector](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/BehaviorTree/Composites/Selector.cs) that shuffles its children before executing

### [Conditionals](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/BehaviorTree/Conditionals)

[Conditionals](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/BehaviorTree/Conditionals) are binary success/failure nodes. They are identified by the [IConditional](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/BehaviorTree/Conditionals/IConditional.cs) interface. They check some condition of your game world and either return success or failure. These are inherently game specific so [Nez](https://github.com/prime31/Nez) only provides a single generic Conditional out of the box and a helper Conditional that wraps an Action so you can avoid having to make a separate class for each Conditional.

* [RandomProbability](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/BehaviorTree/Conditionals): return success when the random probability is above the specified success probability
* [ExecuteActionConditional](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/BehaviorTree/Conditionals/ExecuteActionConditional.cs): wraps a Func and executes it as the Conditional. Useful for prototyping and to avoid creating separate classes for simple Conditionals.

### [Decorators](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/BehaviorTree/Decorators)

[Decorators](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/BehaviorTree/Decorators) are wrapper tasks that have a single child. They can modify the behavior of the child task in various ways such as inverting the result, running it until failure, etc.

* [AlwaysFail](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/BehaviorTree/Decorators/AlwaysFail.cs): always returns failure regardless of the child result
* [AlwaysSucceed](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/BehaviorTree/Decorators/AlwaysSucceed.cs): always returns success regardless of the child result
* [ConditionalDecorator](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/BehaviorTree/Decorators/ConditionalDecorator.cs): wraps a Conditional and will only run its child if a condition is met
* [Inverter](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/BehaviorTree/Decorators/Inverter.cs): inverts the result of its child
* [Repeater](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/BehaviorTree/Decorators/Repeater.cs): repeats its child task a specified number of times
* [UntilFail](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/BehaviorTree/Decorators/UntilFail.cs): keeps executing its child task until it returns failure
* [UntilSuccess](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/BehaviorTree/Decorators/UntilSuccess.cs): keeps executing its child task until it returns success

### [Actions](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/BehaviorTree/Actions)

[Actions](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/BehaviorTree/Actions) are the leaf nodes of the [behavior tree](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/BehaviorTree/BehaviorTree.cs). This is where stuff happens such as playing an animation, triggering an event, etc.

* [ExecuteAction](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/BehaviorTree/Actions/ExecuteAction.cs): wraps a `Func` and executes it as its action. Useful for prototyping and to avoid creating separate classes for simple `Action`s.
* [WaitAction](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/BehaviorTree/Actions/WaitAction.cs): waits a specified amount of time
* [LogAction](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/BehaviorTree/Actions/LogAction.cs): logs a string to the console. Useful for debugging.
* [BehaviorTreeReference](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/BehaviorTree/Actions/BehaviorTreeReference.cs): runs another BehaviorTree

## [Goal Oriented Action Planning \(GOAP\)](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/GOAP)

[GOAP](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/GOAP) differs quite a bit from the other AI solutions. With GOAP, you provide the planner with a list of the actions that the AI can perform, the current world state and the desired world state \(goal state\). GOAP will then attempt to find a series of actions that will get the AI to the goal state.

GOAP was made popular by the old FPS F.E.A.R. The AI in F.E.A.R. consisted of a GOAP and a state machine with just 3 states: GoTo, Animate, UseSmartObject. Jeff Orkin's [web page](http://alumni.media.mit.edu/~jorkin/goap.html) is a treasure trove of great information.

### [ActionPlanner](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/GOAP/ActionPlanner.cs)

The brains of the operation. You give the [ActionPlanner](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/GOAP/ActionPlanner.cs) all of your Actions, the current world state and your goal state and it will give you back the best possible plan to achieve the goal state.

### [Action](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/GOAP/Action.cs)/[ActionT](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/GOAP/ActionT.cs)

Actions define a list of pre conditions that they require and a list of post conditions that will occur when the [Action](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/GOAP/Action.cs) is performed. [ActionT](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/GOAP/ActionT.cs) is just a subclass of [Action](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/GOAP/Action.cs) with a handy context object of type T.

### [Agent](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/GOAP/Agent.cs)

[Agent](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/GOAP/Agent.cs) is a helper class that encapsulates an AI agent. It keeps a list of available Actions and a reference to the [ActionPlanner](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/GOAP/ActionPlanner.cs). [Agent](https://github.com/prime31/Nez/blob/master/Nez.Portable/AI/GOAP/Agent.cs) is abstract and requires you to define the `GetWorldState` and `GetGoalState` methods. With those in place getting a plan is as simple as calling `agent.Plan()`.

## [Utility Based AI](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/UtilityAI)

Utility Theory for games. The most complex of the AI solutions. Best used in very dynamic environments where its scoring system works best. [Utility based AI](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/UtilityAI) are more appropriate in situations where there are a large number of potentially competing actions the AI can take such as in a RTS. A great overview of utility AI is [available here](http://www.gdcvault.com/play/1012410/Improving-AI-Decision-Modeling-Through).

### [Reasoner](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/UtilityAI/Reasoners)

Selects the best [Consideration](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/UtilityAI/Considerations) from a list of Considerations attached to the [Reasoner](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/UtilityAI/Reasoners). The root of a utility AI.

### [Consideration](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/UtilityAI/Considerations)

Houses a list of [Appraisal](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/UtilityAI/Considerations/Appraisals)s and an [Action](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/UtilityAI/Actions). Calculates a score that represents numerically the utility of its [Action](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/UtilityAI/Actions).

### [Appraisal](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/UtilityAI/Considerations/Appraisals)

One or more [Appraisal](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/UtilityAI/Considerations/Appraisals)s can be added to a [Consideration](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/UtilityAI/Considerations). They calculate and return a score which is used by the [Consideration](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/UtilityAI/Considerations).

### [Action](https://github.com/prime31/Nez/tree/master/Nez.Portable/AI/UtilityAI/Actions)

The action that the AI executes when a specific Consideration is selected.

