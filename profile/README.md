# Epistimis is about Privacy First Design

We supply tools to developers to enable them to design privacy in from the very beginning.  These tools enable developers, product managers, CISOs, inside 
counsel and outside counsel to all work together to reduce risk of violating privacy, discrimination and other laws.

As part of our commitment to quality and the Open Source Community, we want to give back. So the foundation of our tools is open sourced here in 4 repos:

The foundation of Epistimis' tools is built on standards originally developed for the US Military 
(Yes, we are military grade!) Those standards were originally one (FACE), now split into 2 parts:
* UDDL (Universal Data Definition Language) - specification available [here](https://publications.opengroup.org/standards/face/c198)
* FACE (Future Airborne Capability Environment) - specification available [here](https://publications.opengroup.org/standards/face/c207)

These can be downloaded for free after you create an OpenGroup login.

These specifications and the training that goes with them are still in active development. As additional documents are published, we will post links 
here referencing the ones you'll need to know.

For now, the parts that matter for our purposes are:
UDDL - the entire document:
* [UDDL](https://github.com/Epistimis/UDDL) implements Chapters 5,7, and 8 of that spec.
* [UDDL Query Language](https://github.com/Epistimis/UDDL-Query-Language) implements Chapter 6 of that spec.

To properly understand those implementations, you'll want to read chapters 2 & 3 of the spec as well.

FACE - Appendix J
* [FACE](https://github.com/Epistimis/FACE) implements Meta-Packages face.uop & face.integration (J.2, J.5, J.6, J.7)
* [FACE Template Language](https://github.com/Epistimis/FACE-Template-Language) implements the Data Architecture Template Specification (J.3, J.4)


The IDL Bindings described in the FACE Spec Appendix J.8 and Appendix K apply to both UDDL and FACE. Read the legend at the beginning of 
the appendix and then search by the classname (as used in the FACE/UDDL specs, not the classname as implemented in Epistimis' code) to 
find relevant binding rules.

Note that the FACE Reference Implementation Guide should also be referenced. When it is published, a link to it will be provided here.

## Epistimis modifications
Epistimis code is based on these specs but is 'unofficial'. It does not use the namespace/packaging from the original spec which results in 
some minor modifications to OCL. It does not use the official .ecore model though the generated model should be compatible with it. In addition, 
enumerations have been extended to support hierarchies (because this makes simple taxonomies easy to implement). Other than that, it should be functionally equivalent.

The UDDL Query and FACE Template grammars are the same as the spec - those are isolated in `com.epistimis.uddl.query.*` and `com.epistimis.face.template.*` respectively.
The syntax used in the `com.epistimis.uddl.*` and `com.epistimis.face.*` projects is Epistimis` alone - it is not part of the 'official' 
UDDL spec.  The syntax choices in these projects are based on the philosophy outlined in the Notation Overview below.

# Motivational Overview - Why These Tools?
*Learning to program is an unnatural act. It requires the novice to codify their notions of specification and process, to acquire an understanding of abstractions for which they may have no prior referents, and to express these concepts in a formal style of language they have never previously encountered. In other words, it is like trying to alter their belief system by quoting theoretical philosophy to them in hieroglyphics. (McIver & Conway)*

If learning to program is hard, learning to model is both paradoxically easier and harder. It is easier because much of the detail required by programming languages need not be included. Harder because, done properly, it is no less formal and thus requires the modeler understand the problem well enough to know what can be left out.

Modeling is often associated with pictures. Many companies with vested interests have deliberately conflated these two over the years. However, modeling is solely about identifying appropriate abstractions and recording them in a way that will help both communicate those concepts to others and, ideally, be useful in creating the systems that actually employ these concepts (by, for example, generating software from the model).  How the model is represented visually is independent of its content so long as the visual representation can capture and/or express the appropriate concepts accurately. Further, because the content is independent of its representation, it is possible to have multiple representations of the same information, with each available for use where most appropriate. The most obvious example is this: typing text when notating a simple concept such as A -> B is typically much faster than drawing a diagram to represent the same information. On the other hand, complex relationships are often easier to understand if we can see a picture tying related objects together with visual cues. Ideally, you should be able to select the format you use for recording information independently of the information to be recorded and independently of the presentation format used to communicate. Epistimis Modeling Toolkit (EMT) is the tool that lets you do that.

EMT is a collection of tools that give you, the user, the ability to create FACE conformant models in a textual syntax that is easy to learn and understand and fast to use. You can also create models graphically, or create them textually and view them graphically.  In addition, the tool supports code generation and the ability to exchange work with other XMI compliant tools like Enterprise Architect. 

# Notation Overview
## Syntactic Principles
1. Predictability is highly desirable because it is easier for people to remember fewer mental rules. This means:
* Use familiar paradigms where possible - but only if used in the same way. E.g., '{' and '}' are used for scoping just as they are in languages 
like C++ and Java. If there is a reason to use something 'non-standard', it will be explained.
* Where you can't mimic existing paradigms, do what is 'natural' - notation should be easy to figure out or, barring that, easy to understand once explained. 
* Be consistent in terminology and organization. For example, wherever there is a 'realizes' attribute it is placed immediately before the attribute set. 

2. Be succinct - every keystroke counts. Succinctness trumps predictability when the result is still easy to understand. 
E.g. most of the keywords chosen are abbreviations of their names in the UDDL spec.

  * The most obvious case of this is the use of '{' and '}' These are used only when the object has attributes beyond Element attributes or 
  Characteristic attributes and 'realizes'. They may not be used in some of these remaining cases if there is a more succinct way of 
  representing the information that is still consistent in some sense. 

  * In the same way, ';' is typically used to denote the end of an object definition. It won't be used for things like CDM, LDM, PDM 
  because these are just named collections of other objects. Similarly, for objects that are always composed into something else, ';' will 
  typically not be used since the object is clearly nested inside another. There may be cases where it is necessary for the parser to 
  determine where one object ends.

  * Lists of composed sub-objects: because the objects are composed in, we want to avoid using ';' (that can get confusing to the reader). 
  However, because we have a list and each sub-object can have multiple attributes (e.g. constraints), we need to make sure they are clearly separated. 
  But the separation when used in a list need not be present when used individually. Further, the notation used for separation must not be notation 
  that could appear in the content of the object - otherwise, the parser may get confused when reading the model text. In these cases, we use 
  '{' / '}'  as part of the list specification.

Collections that have multiplicity of 0...* should be made optional as a whole but, if specified, required to contain at least 1 element.  

## Epistimis' Implementation
Epistimis implements the above goals as follows:
<!--
The Notation Specifics table lists grammar labels and the specific character sequences they convert to. Every time you see one of the labels in a grammar rule below, that means you should type the notation character sequence for that label. For pairs of labels (e.g. LIST_START and LIST_END), the notation cell for that concept shows the two character sequences separated by a space. Using these labels in the subsequent grammar rules is meant to ensure that the appropriate concept is communicated in the grammar rules *and* that the rules implement the concepts in a consistent way. 

-->
1. Epistimis uses XText grammars as the basis for tooling. Per the [XText grammar doc](https://www.eclipse.org/Xtext/documentation/301_grammarlanguage.html#syntax): 
* Parentheses are used in grammar rules for grouping. 
* A vertical bar ‘|’ inside the parentheses means that the things on either side of the vertical bar are alternatives. 
* The closing parenthesis may be followed by either ‘?’,’\*’, or ‘+’. Each of these indicates 
how many times what is inside the parentheses can be entered: ‘?’ means 0 or 1 times; ‘\*’ means 0 or more times; ‘+’ means 1 or more times. 
If none of these is specified, it means the preceding grammar element must be present exactly 1 time. 

For more details, see the [XText grammar doc](https://www.eclipse.org/Xtext/documentation/301_grammarlanguage.html#syntax)

To insure consistency in the rules, the following conventions are used:
1. All optional lists (a list that can contain 0 or more elements) are themselves optional. If you aren’t going to put anything in the list, you 
don’t add the '[' and ']' either. This minimizes visual clutter. 

1. Cross references between parts of a model are created by using the name/ rolename of the referenced model element. The use of ‘ID’ in Elements 
and rolename in Characteristics indicates where the name / rolename is defined for a model element.  Italics in rules indicate where to 
include a cross reference. You can either type in the name of the referent or use CTRL-Spacebar to bring up a list of choices from which to select.
If the ID is insufficient to uniquely identity the referent, fully or partially qualified names should be used. Fully qualified names are simply the names of all the enclosing elements in sequence from outermost to the referent, separated by ‘.’. A partially qualified name simply includes a subsequence of those names that does not extend all the way back to the outermost element. If you use CTRL-Space to select a referent from a list of choices, the tool will typically insert partially qualified names that start with the closest common ancestor between the referent and the reference.

The UDDL and FACE textual syntax mimics the syntax of C/C++/Java or the UNIX command line where practical. For example:

* Type information, when specified, is placed before the name of the typed attribute.

* Structures are enclosed in ‘{‘ and ‘}’.

* Structure and attribute definitions end with ‘;’ where doing so would be visually helpful. In order to avoid including the ‘;’ in a comment, 
it may appear on a subsequent line by itself

* Ranges are enclosed in ‘[‘ and ‘]’ when the endpoints are included and ‘(‘and ‘)’ when they are not. 

* Array bounds are enclosed in ‘[‘ and ‘]’

* ‘/’ is used as a path separator


# Getting started

See the [Getting Started Guide](GETTING-STARTED.md) for info on setting up an Eclipse development environment. It will take you all the way to a working demo.

# Contributing
See [Contributing](CONTRIBUTING.md) for info on how to contribute. See [Code of Conduct](CODE_OF_CONDUCT.md) for community standards.


# Security and Bug Reports
For most bugs, create a GitHub issue. If you believe you have found a security issue, see [Security](SECURITY.md) for reporting instructions.

# User Doc
We need user Doc! A draft will be provided soon in Markdown and linked here. Stay tuned!

<!--

**Here are some ideas to get you started:**

🙋‍♀️ A short introduction - what is your organization all about?
🌈 Contribution guidelines - how can the community get involved?
👩‍💻 Useful resources - where can the community find your docs? Is there anything else the community should know?
🍿 Fun facts - what does your team eat for breakfast?
🧙 Remember, you can do mighty things with the power of [Markdown](https://docs.github.com/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)
-->
