### User questions

U.1) When I run `mutant --include lib --require calculator --use rspec "Calculator*"`, I receive the output shown below. I *think* I'm right in saying that the one living mutant can never be killed: it's an equivalent way to implement my specification. Is there an extra spec I can write to kill this mutant? If not, is there any way to tell mutant not to show me this one when I next run `mutant ...`?

```diff
Mutations:       43
Kills:           42
Alive:           1
evil:Calculator::Max#run:/Users/louis/Code/mutant-playground/lib/calculator/max.rb:3:9bb72
@@ -1,8 +1,8 @@
 def run(left, right)
   max = left
-  if (right > left)
+  if (right >= left)
     max = right
   end
   max
 end
```

*Markus*:
* Equivalent mutants don't arise very often in Ruby because mostly we can use library code.
* When they do arise, he typically isolates them into their own method and then exempts those methods from mutation testing
* Mutant enforces a hierachy of mutation operators sometimes so, for example, if > can be mutated to >= then >= cannot be mutated to >. Therefore one way to fix the example above is to use the >= equivalent, and Mutant won't generate the > version.


U.2) When I forget the `--use rspec` part of the mutant command, I see it does something and even kills one mutant. What's happening here?



U.3) Is it possible to change which mutation operators are being used right now? Suppose I wanted to turn off "> becomes >=", is that possible? Or even something more coarse-grained like turn off all mutations of `if` expressions?

### Developer questions

I walked through an execution of mutant from the binary, tracing my way through the code. I had the following questions:

D.1) What is the zombifier?

*Markus*: Infrastructure needed for mutant to be applied to itself.

D.2) On line 22 of Mutant::CLI, where is the inner `call` method defined? I can't see it in this source file, and running `Mutant::CLI.instance_methods(true).sort` in IRB doesn't show it.

*Markus*: It's defined indirectly via the Procto library (see the include statement).

D.3) On line 34 of Mutant::Driver, I've skipped over Parallel.async -- is this an important part of mutant for me to understand? Or can I just assume it's doing some parallel execution of a block?

*Markus*: Just skip it for now.

D.4) Roughly how does the Mutator work? I think I see that it rewrites the AST (probably using a visitor?), but I might be wrong. Suppose I invented a new mutation operator, how would I add it?

*Markus*: It uses a more maintainabile form of visitor (called an emitter) that allows visiting logic to be decomposed into node-specific classes. A lot of duplication has been extracted from these classes so the code is very concise: studying the extracted methods is key to understanding how it works.

D.5) I read that Mutant might in the future support plug-ins for DSLs (e.g., attr_accessor). How far along is this work? This sounds interesting, and I might be able to contribute?

*Markus*: For the specific case of `attr_accessor` or `validate` from AR this is very challenging because Mutant loads the namespaces under test once, and these macros are expanded at class evaluation time. Mutants are normally created prior to this step, so being able to mutate these macro-style DSLs will involve adding significant new infrastructure to Mutant to perform boot-time mutation.

D.6) What tactics does Mutant use to reduce the time taken to kill each mutant?

*Markus* Parallel exeuction. Plus for each mutant in Foo#bar, Mutant first executes the specs Foo#bar's specs only. If and ONLY if there are no such specs then Foo's specs only. If and ONLY if there an such specs than all specs are run for this mutant.

Everything else looks pretty straightforward, actually. Good times :-) (I've skipped over the Reporter and the Selector classes for now. Are these important for me to understand?)
