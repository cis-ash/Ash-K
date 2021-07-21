# How to write code and not die

## Must read

- [Godot style guide](https://docs.godotengine.org/en/stable/getting_started/scripting/gdscript/gdscript_styleguide.html)
- [All you need to know about debugging ](https://youtu.be/-LCBgGK-BAk)

## Godot style guide TLDR

- Use code order specified in documentation (at the bottom of the page there a script template to help you with that)
- Class should be in PascalCase
- Files should be in snake_case (convert class name to snake_case for scripts)
- Variables and function names are in snake_case
- Use single underscore before the name to denote virtual methods, private methods and private variables
- Use past tense in signal names, don't add `on_` unless absolutely necessary

    ```python
    signal door_opened
    signal score_changed
    ```

- Constants are in CONSTANT_CASE, and enum members too.
- Dont make long lines of code, generally they should not be longer then 80 chars
- When separating a long expression into multiple lines, extra lines should be indented twice
- Leave two lines before functions and class definitions
- Use `and` instead of `&&` , `or` instead of `||`
- Comments should start with a space, disabled code should be `ctrl+k`
- Use spaces to make code more readable

    Good:

    ```jsx
    position.x = 5
    position.y = mpos.y + 10
    dict["key"] = 5
    my_array = [4, 5, 6]
    print("foo")
    ```

    Bad:

    ```jsx
    position.x=5
    position.y = mpos.y+10
    dict ["key"] = 5
    myarray = [4,5,6]
    print ("foo")
    ```

    If you do this you die instantly:

    ```jsx
    x        = 100
    y        = 100
    velocity = 500
    ```

- You can use `"` or `'` to avoid escaping quotes, beacuse `print("hello 'world'")` is ok.
- Use explicit zeros on floats
Good:

    ```jsx
    var float_number = 0.234
    var other_float_number = 13.0
    ```

    Bad:

    ```jsx
    var float_number = .234
    var other_float_number = 13.
    ```

- GDscript has decimal, hex and binary leterals and all of them have separators:

    ```python
    var large_number = 1_234_567_890
    var large_hex_number = 0xffff_f8f8_0000
    var large_bin_number = 0b1101_0010_1010
    # Numbers lower than 1000000 generally don't need separators.
    var small_number = 12345
    ```



## Best practices TLDR:

[Best practices full document]([https://docs.godotengine.org/en/latest/tutorials/best_practices/index.html](https://docs.godotengine.org/en/latest/tutorials/best_practices/index.html))

- Nodes should really know only about nodes directly below them, to talk to nodes higher or on the same level use:
    - Signals
    - Make the node call a funcref that other node sets
    - Make the node manipulate a node reference that other node sets.
    Make sure that no reference loops occur (aka two or more nodes reference each other in a loop and none of them can ever be freed), use weakref to break such loops
    - Make the node manipulate a NodePath that other node sets.
    (Personally i found NodePath's to be a bit flaky, but its the only way to export node references in editor)
    - When you want two nodes on the same level talk parent can help them make contact
- Use general OOP principles:
    - [SOLID](https://en.wikipedia.org/wiki/SOLID)
    - [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)
    - [KISS](https://en.wikipedia.org/wiki/KISS_principle)
    - [YAGNI](https://en.wikipedia.org/wiki/You_aren%27t_gonna_need_it)
- Autoloads should be used sparingly (because then can trigger massive hard to find bugs)
- (Its not actually said here but) DONT use preload or load, export PackedScene or [aplicable resource type here] instead, because if you want to move or rename some file, export will update but preaload and load will instead explode.
- Dictionary is a HashMap, Array is a Vector, use type specialized array when speed is key.
- Object is secretly a dictionary too.
- Whatever your project structure is use snake_case for files and folders, otherwise on some platforms shit will explode on export and everyone is gonna have a bad time 💀.
- Always close the editor before running `git pull`! Otherwise, you may lose data if you synchronize files while the editor is open.

## Notes from experience

- `get_parent` is evil, prefer using signals
- Don't use hardcoded paths to nodes, all paths should be in a variable so its easier to change later.
- When commenting code use `ctrl+k` it actually saves time.
- Always use Godot static typing system
- Always explicitly state types for variables
(I died inside more then once from `float_value := 1337`)
- Enums are epic
- Use getters and setters when accessing data from a different script
- No magic numbers (except 0, 1 and 2). Even it is not a good export varible it still probably has a good name.
- Have at least minimal comments
- Give your script a `class_name` and use it as a type in static typing.
- Use `_physics_process` instead of `_process`.
- Multiply stuff by delta time to tie it to realtime instead of frames, and don't forget to check with lower and higher framerates.
- Always test that all code paths actually work.
- Make sure that your scripts are not bloated with functionality, use extra child nodes for handling extra features.
- Every scene should have a folder for itself and for resources that it uses.
- If a function can return an error, always check.
- Prints are your friend, but always have a string telling where this noname float value is printed from. Also its very nice to have a way to disable them without commenting.
- Enable saving logs to disk, sometimes it really helps to have output log from 5 launches ago.
- Godot actually has bugs sometimes:
    - Sometimes delta time in `_process` and `_physics_process` is 0, check at the start of the function and return immediately if its the case, otherwise anything can happen.
    - With some of the scaling options UI elements can explode randomly when changing window sizes, if its the case just `set_rect_position` and `set_rect_size` everything where it belongs
    - Key bindings for Mac are scuffed, but i have the wizardry to make them work (i really should report it and make a pull request).
    - Debugger on some rare occasions fucking dies, in this case try using `print` while praying.
    I remember one time i got this i had a freed node as a key in a dictionary..
- variable names should start with the general thing they refer to, then what they represent in that thing, then if its an attribute of thing they represent - the attribute.
This is done to make autocompletion easier and to avoid remembering variable names by heart
e.g. : jiggle_position_max
- Don't mix Controls and Node2D's, Controls are made for UI.
Some rare exceptions are ok, like having TextureRect instead of Sprite2D.
- Your scripts should "compile" with no warnings, you may suppress manually these types of warnings (ping me to show cases where other types of warnings are useless):
    - return_value_discarded
- Use `pass` only for defining empty functions
- Your scene should be at (0, 0) with (1, 1) scale
- Minimize amount of non (1, 1) scale and non (0, 0) position nodes
- Use pixel snap for positions, some kind of rotation and scale snap is good too
- Be careful with scale, updating it in code after setting it in editor can cause problems

## Script template:

Don't delete unused sections, uncomment needed pieces of code

[new_script.gd](How to write code and not die/new_script.gd)

