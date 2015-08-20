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

U.2) When I forget the `--use rspec` part of the mutant command, I see it does something and even kills one mutant. What's happening here?

U.3) Is it possible to change which mutation operators are being used right now? Suppose I wanted to turn off "> becomes >=", is that possible? Or even something more coarse-grained like turn off all mutations of `if` expressions?

### Developer questions

I walked through an execution of mutant from the binary, tracing my way through the code. I had the following questions:

D.1) What is the zombifier?

D.2) On line 22 of Mutant::CLI, where is the inner `call` method defined? I can't see it in this source file, and running `Mutant::CLI.instance_methods(true).sort` in IRB doesn't show it.

D.3) On line 34 of Mutant::Driver, I've skipped over Parallel.async -- is this an important part of mutant for me to understand? Or can I just assume it's doing some parallel execution of a block?

D.4) Roughly how does the Mutator work? I think I see that it rewrites the AST (probably using a visitor?), but I might be wrong. Suppose I invented a new mutation operator, how would I add it?

D.5) I read that Mutant might in the future support plug-ins for DSLs (e.g., attr_accessor). How far along is this work? This sounds interesting, and I might be able to contribute?

D.6) What tactics does Mutant use to reduce the time taken to kill each mutant?

Everything else looks pretty straightforward, actually. Good times :-) (I've skipped over the Reporter and the Selector classes for now. Are these important for me to understand?)
