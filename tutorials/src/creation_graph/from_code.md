# Calling creation graphs from code

In this tutorial we will create a very simple component that uses a [creation graph]({{the_machinery_book}}/creation_graphs/concept.html) to render to the viewport. The creation graph used for this example can be seen in the image below.

The goal of this creation graph is to create an image output that we can copy to the viewport. In this example the image is created by the creation graph and the viewport UV is rendered onto it using and unlit pass. Notice that no geometry has to be defined as we use the `Construct Quad` node in clip space, this will procedurally encompass the entire viewport.

**Contents**
* auto-gen TOC;
{:toc}

![](https://www.dropbox.com/s/k4y8wlwx7y8vll3/tm_tut_creation_graphs_from_code.png?dl=1)

## Using the creation graph API
The component itself is very simple, it only has a single property which is our creation graph asset.

```c
{{$include {TM_BOOK_CODE_SNIPPETS}/creation_graph/calling_creation_graph_from_code.c:146:147}}
```

However multiple fields are defined in the runtime component struct, all of these are dependent on our creation graph.

```c
{{$include {TM_BOOK_CODE_SNIPPETS}/creation_graph/calling_creation_graph_from_code.c:37:52}}
```

In the example we only call the creation graph once (during the initialization phase). The workflow is as follows. 
The creation graph subobject is added by The Truth, so we don’t have to do any UI or linking code for it. 
In the initialize function we instantiate this creation graph asset with a default context. 
This updates our image output node and all the nodes it is dependent upon. 

```c
{{$include {TM_BOOK_CODE_SNIPPETS}/creation_graph/calling_creation_graph_from_code.c:69:87}}
{{$include {TM_BOOK_CODE_SNIPPETS}/creation_graph/calling_creation_graph_from_code.c:100}}
```

Next we query all the image output nodes from the graph, and pick the first one. This information we get from the output node is enough to copy our image to the viewport. 

```c
{{$include {TM_BOOK_CODE_SNIPPETS}/creation_graph/calling_creation_graph_from_code.c:89:99}}
```

To do this we register it to the viewports render graph using `register_gpu_image` and then pass it to the `debug_visualization_resources` for easy rendering to the screen.

```c
{{$include {TM_BOOK_CODE_SNIPPETS}/creation_graph/calling_creation_graph_from_code.c:109:135}}
```

## Remarks
Note that this is a very simple example of the creation graph, we don’t update it every frame so it will only render once. This makes use of the `Time` node useless in this example. Note as well that we are not triggering any wires, this also means that the `Init event` node will never be called by the component. 

Note as well that all destruction code has been omitted from the code sample to shorten it. In a production implementation the creation graph instance and the component should be destroyed.

## Full Code
```c
{{$include {TM_BOOK_CODE_SNIPPETS}/creation_graph/calling_creation_graph_from_code.c}}
```
