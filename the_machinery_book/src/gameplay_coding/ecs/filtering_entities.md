# Filtering Entities

The Machinery knows 2 kind of ways to Tag Entities:

1. using the `Tag Component`
2. using a `Tag Component` to filter the Entity Type's



**Table of Content**

* auto-gen TOC;
{:toc}
## Filtering Entities 

In a Engine (`tm_engine_i`) you can define the `.excluded` filed. This tells the scheduler that this engine shall **not** run on any entity type that contains these components.

Let us assume we have the following entities:

```
#1 Entity:
- Component A
- Component B
- Component C
#2 Entity:
- Component A
- Component B
- Component D
```

Now we have a Engine that shall operate on `(Component A,Component B)` but we do not want it to operate on entities with `Component D` we could just check in our update loop:

```c
{{$include {TM_BOOK_CODE_SNIPPETS}/gameplay_code/ecs_filtering_entities.c:35:54}}
```

or we could define a component mask and use this to filter but both methods are slow. This is because `get_component_by_hash` or `get_component` require us to look up internally the entity + the components and search for them. Its aka a random memory access! 

To avoid all of this we can just tell the engine to ignore all entity types which contain the `component_d` via the `.excluded` fied in the `tm_engine_i`.

```` c
{{$include {TM_BOOK_CODE_SNIPPETS}/gameplay_code/ecs_filtering_entities.c:42:53}}
````

## Filtering Entities by using Tag Components

> **Note**: You can define a `Tag` Component which should not be confused with the `Tag Component`. A tag component is a simple typedef of a `unit64_t` (or something else) or an empty struct in C++ to a component without properties. The function of this component is it to modify the Entity Type / Archetype to group entities together with them.For more information see the [Tagging Entities]({{the_machinery_book}}/gameplay_coding/ecs/tagging_entities.html) Chapter.

You have a Movement / Input System which should always work. At some point you do not want a entity to receive any input. 

*Solution 1*

To solve this issue you could remove the Movement Component but that would be annyoing because you would lose it state, which might be important.

*Better Solution*

First you define the component:

```c
{{$include {TM_BOOK_CODE_SNIPPETS}/gameplay_code/ecs_filtering_entities.c:56:65}}
```

Then you filter in your update for the Input Engine/ Movement Engine any Entity that has a No Movement Tag:

```c
{{$include {TM_BOOK_CODE_SNIPPETS}/gameplay_code/ecs_filtering_entities.c:69:85}}
```

When ever another `engine/system` decides that a entity should not move anymore it just adds a `no_movement_tag_component` to the entity.

```c
{{$include {TM_BOOK_CODE_SNIPPETS}/gameplay_code/ecs_filtering_entities.c:90:107}}
```

As you an see the Movement Engine will now update all other entities in the game which do not have the No Movement Tag.