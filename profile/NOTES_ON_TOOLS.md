# On XTend, the JVM based language used with XText
[XTend](http://xtend-lang.org) has in many ways been overtaken by advances in Java. That means when working with XText based languages you'll be able to use
Java in most cases. There remain, however, 2 use cases where XTend can be helpful. (NOTE: XTend is converted to Java in the `xtend-gen` directory in case 
want to see how it is converted. Never edit or commit to git anything from the `xtend-gen` directory. It will ***always*** be overwritten the next time
the code is compiled.
1. ***Code Gen Templates:*** [XTend Template Expressions](https://www.eclipse.org/xtend/documentation/203_xtend_expressions.html#templates) provide a convenient
way to create code generation templates. They operate very close to WYSIWIG. Whenever you need to do complex text generation, consider using XTend.
1. ***Dynamic Dispatch:*** [XTend Dispatch Methods](https://www.eclipse.org/xtend/documentation/202_xtend_classes_members.html#polymorphic-dispatch) are a 
convenient way to call based on the runtime type of an object. Dispatch methods always pick the method implementation that matches the most specialized 
class. Note that all dispatch methods must be defined in the same class. If you have some defined in a base class and some in a derived class, the derived 
class methods override everything in the base class. This is because there is a generated method (look in the Java code to see it) that actually handles 
the dispatch. There is one per class - and it only knows about the methods defined in that class - not any ancestor classes. That method is the one that
is overridden.

In all other cases, just use Java. And you may want to use Java even in some of these cases.
