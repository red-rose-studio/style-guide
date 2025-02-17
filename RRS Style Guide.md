# Red Rose Style Guides
The following are guidelines we will be using on programming projects at Red Rose Studio in order to maintain clarity and consistency across codebases. These are subject to change as needed. Feel free to message [@sierra0451](https://discordapp.com/users/933474287380230154) on Discord or email [thesierra0451@gmail.com](mailto:thesierra0451@gmail.com) if you have any questions!

## General
### Naming rules
Files:
* File names and directories should be in pascal case: `Assets/Scripts/LowerFolder/MyClass.cs` or `Assets/Sprites/Player/PlayerSprites.png`.
	* What this means is, essentially, that the first letter of each word should be capitalized, and there should be no spaces.
	* For clarification purposes, this also means that acronyms should only have the first letter capitalized (e.g. prefer `Id` over `ID`).

### Organization
#### All games
* HUD assets should go in a subfolder under `Assets/Sprites`.
	* Same goes for sprites in 2D games.
#### 3D games
* All models should go into a subfolder under `Assets/Models`.
* Decals and level textures should go into a subfolder under `Assets/Textures`, preferably one named after the level itself if only used in that level.

## C#
Most of this part of the style guide is pulled straight from [Google's C# style guide](https://google.github.io/styleguide/csharp-style.html), though they are *not* exactly the same. Some bits are pulled from [Microsoft's framework design guidelines](https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/) as well.

When referring to a "serialized field", we're describing a field with the `[SerializeField]` attribute.
### Formatting rules
#### Naming rules
Code:
* Classes, methods, enumerations, properties, and namespaces should use pascal case: `ExampleClass`.
* Public and serialized fields, local variables, and parameters should use camel case: `exampleParameter`.
* Non-serialized private, protected, internal, and protected internal fields should use camel case, with a preceding underscore: `_exampleField`.
* `const`, `static`, `readonly`, and other such modifiers will not affect the naming convention.
* Acronyms should only have their first letter capitalized: ex. `EntityId` rather than ~~`EntityID`~~.
* Interface names start with a capital I: `IInterface`.
* When possible, avoid abbreviations in identifier names: e.g. `GetWindow` rather than `GetWin`.
* Do NOT use Hungarian notation (i.e. prefer `bool CanDash` over `bool bCanDash`). We're not working in Unreal Engine.

Files:
* Where applicable, the file name should match the name of the script's main class: `MyClass.cs`.
* Generally, prefer one core class per script, especially when said class derives from `MonoBehaviour`.

#### Organization
* Modifiers should follow this order: `public protected internal private new abstract virtual override sealed static readonly extern unsafe volatile async`.
* `using` directives go at the top of the script, just below the header. Import order is in alphabetical order, except for `System` namespaces, which go first (and should themselves be in alphabetical order).
* Unity event methods should follow this order: `Awake Start FixedUpdate Update LateUpdate`.
* Class member ordering:
	* Group class members in following order:
		* Nested classes, enums, delegates, and events
		* Static, const, and readonly fields
		* Fields and properties
		* Constructors and finalizers
		* Unity event methods (`Awake`, `Start`, etc.)
		* Custom methods
		* Unity collision handling methods (`OnTriggerEnter`, `OnCollisionEnter`, etc.)
		* Unity debugging methods (`OnDrawGizmos`, `OnDrawGizmosSelected`, etc.)
		* `OnEnable`, `OnDisable`, `OnDestroy`
	* Within each group:
		* (fields only) Serialized fields
		* Public
		* Internal
		* Protected internal
		* Protected
		* Private
	* Where possible, group interface implementations together.
* All C# code written for any Red Rose Studio project should be declared under the namespace `RedRoseStudio.[ProjectName]`, replacing [ProjectName] with the name of the project (e.g. `RedRoseStudio.ProjectAlice`).

#### Whitespace
* One statement maximum per line.
* One assignment per statement maximum, with the exception of local variables in methods.
* Tab indentation, 4 spaces.
* Line break before opening brace.
* Line break between closing brace and `else`, and between `else` and the next opening brace.
* Prefer no braces with one-line sections.
* One space after `if`, `for`, `while`, etc., and after commas.
* No space after an opening parenthesis or before a closing parenthesis.
* No space between unary operators and their operands, e.g. `counter++` or `timer--`. One space between operands and all other operators.

### Coding Guidelines
#### Constants
* When possible, prefer `const` variables and fields.
* If not possible, `readonly` can be a suitable alternative.
* Prefer named constants to magic numbers.

#### Property styles
* For single line read-only or write-only properties, prefer expression body properties (`=>`) when possible.
* For everything else, use `{ get; set; }` syntax, following same brace formatting rules from above.

#### Expression body syntax
Example:
```cs
int SomeProperty => _someProperty;
```
* Judiciously use expression body syntax in lambdas and properties.
* Don't use on method definitions.

#### Structs and classes
* Structs are very different from classes:
	* Structs are **always** passed and returned by value (i.e. don't use `ref` or `in`).
	* As such, assigning a value to it or to a member of it won't modify the original value.
* Almost always use a class.
* Consider a struct if the type can be treated like other value types—as an example, if instances of the type are typically short-lived or commonly embedded in other objects. Some examples would be `Quaternion`, `Vector3`, and `Bounds`.

#### Lambdas and methods
* If a lambda is non-trivial (e.g. more than a couple statements, excluding declarations), or is reused in multiple places, it should probably be a named method.

#### Field initializers
* Field initializers are generally encouraged.

#### Array vs List
* Generally, prefer `List<>` over arrays for public variables, properties, and return types.

#### Folders and file locations
* Be consistent with the project.
* Prefer a flat structure when possible.
* Reorganizing the file structure shouldn't be done without approval by the rest of the team.

#### String interpolation vs `String.Format()` vs `String.Concat` vs `operator+`
* In general, use whatever is most readable, particularly for log messages.
* Be aware that chained `operator+` concatenations will be slower and can cause significant memory churn. Largely avoid using this in methods that are called frequently.

#### `using` Directives
* Generally, don't alias long typenames with `using`. Often this is a sign that a `Tuple<>` needs to be turned into a class.
	* e.g. `using RecordList = List<Tuple<int, float>>` should probably be a named class instead.
* Be aware that `using` statements are only file scoped and so of limited use. Type aliases will not be available for external users.

#### Object Initializer Syntax
Example:
```csharp
var x = new SomeClass
{
    Property1 = value1,
    Property2 = value2,
};
```
* This syntax is fine for "plain old data" types.
* Avoid this for classes or structs that have constructors.
* If splitting across multiple lines, indent one block level.

#### Calling delegates
* When calling a delegate, use `Invoke()` and use the null conditional operator—e.g. `SomeDelegate?.Invoke()`. This clearly marks the call at the callsite as "a delegate that is being called". The null check is concise and robust against threading race conditions.

#### `var` keyword
* Use of `var` is encouraged if it aids readability by avoiding type names that are noisy, obvious, and/or unimportant.
* Encouraged:
	* When the type is obvious: `var apple = new Apple();` or `var request = Factory.Create<HttpRequest>();`
	* For transient variables that are only passed directly to other methods: `var item = GetItem(); ProcessItem(item);`
* Discouraged:
	* When working with basic types: `var success = true;`
	* When working with compiler-resolved built-in numeric types: `var number = 12 * ReturnsFloat();`
	* When users would clearly benefit from knowing the type: `var listOfItems = GetList();`

#### Attributes
* For fields:
  * Attributes should appear on the line above the field, property, or method.
  * Each attribute should be in its own separate line and square brackets, making adding and removing attributes and searching for specific attributes easier.

#### Argument Naming
When the meaning of a function argument is non-obvious, consider one of the following remedies:
* If the argument is a literal constant (i.e. `5`, `0.2f`, or `false`), and the same constant is used in multiple function calls in a way that tacitly assumes they're the same, use a named constant to make that constraint explicit, and to guarantee that it holds.
* Consider changing the function signature to replace a `bool` argument with an `enum` argument. This will make the argument values self-describing.
* Replace large or complex nested expressions with named variables.
* Consider using named arguments to clarify argument meanings at the call site.
* For functions with several configuration options, consider defining a single class or struct holding all the options and pass an instance of that. This approach has several advantages. Options are referenced by name at the call site, which clarifies their meaning; and also reduces function argument count, which makes function calls easier to read and write. As an added benefit, call sites don't need to be changed when another option is added.

As an example:
```csharp
// Bad example... what are these arguments?
DecimalNumber product = CalculateProduct(values, 7, false, null);
```
vs.
```csharp
// Good example!
ProductOptions options = new ProductOptions();
options.PrecisionDetails = 7;
options.UseCache = CacheUsage.DontUseCache;
DecimalNumber product = CalculateProduct(values, options, completionDelegate: null);
```
