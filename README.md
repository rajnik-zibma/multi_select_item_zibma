# multi_select_item

Multi select view item controller for GridView and ListView in Flutter


![](https://i.imgur.com/y0L5eSi.gif)

## Getting Started
For this to work. You need to write
First define the controller first
```dart
MultiSelectController controller = new MultiSelectController();
```

Then set the controller length from your list length
```dart
//your list here, can be what ever you want.
mainList.add({"key": "1"});
mainList.add({"key": "2"});
mainList.add({"key": "3"});
mainList.add({"key": "4"});

controller.set(mainList.length);
```
Finally you can use it with list builder. For this plugin to work, you need to provide the `MultiSelectItem` widget with `isSelecting` parameter and `onSelected` parameter.
- the `isSelecting` parameter will be used to tell the widget from the controller wether it's in multi selecting mode or not.
- the `onSelected` parameter is a function. The function will be called depending wether it's in multi selecting mode (`isSelecting == true`) or not.
  - if `isSelecting` is true, then the function will be called when user tap on the MultiSelectItem widget
  - if `isSelecting` is false, then the function will be called when user longtap on the MultiSelectItem widget

So in basic, it will look like this:
```dart
ListView.builder(
  itemCount: mainList.length,
  itemBuilder: (context, index) {
    return MultiSelectItem(
      isSelecting: controller.isSelecting,
      
      //the function that will be called when item is long-tapped/tapped
      onSelected: () {
        setState(() {
          controller.toggle(index);
        });
      },
      child: Container(
        child: ListTile(
          title: new Text("Title ${mainList[index]['key']}"),
          subtitle: new Text("Description ${mainList[index]['key']}"),
        ),
        
        //change color based on wether the id is selected or not.
        decoration: controller.isSelected(index)
            ? new BoxDecoration(color: Colors.grey[300])
            : new BoxDecoration(),
      ),
    );
  },
),
```
Now you can multi select and get the selected indexes:
```dart
print(controller.selectedIndexes.toString());
```

## Changing List
If you ever change the list wether adding or removing item in it, don't forget to call `set()` with `setState()` and pass in your list's `length` to update the controller.
```dart
mainList.add({"key": mainList.length + 1});

setState(() {
  controller.set(mainList.length);
});
```
 
## Removing Item from List
Removing item can be tricky. When we select items, it will store the index in `selectedIndexes` list, but it will order by last selected. As such, it will cause error if we just loop through the list and delete one by one. To avoid this, reorder the list from biggest id to smallest id first and then delete one by one.
```dart
var list = controller.selectedIndexes;
list.sort((b, a) =>
    a.compareTo(b)); //reoder from biggest number, so it wont error
list.forEach((element) {
  mainList.removeAt(element);
});

setState(() {
  controller.set(mainList.length);
});
```

## Keep in Selecting Mode
Sometimes, you don't want to quit multi selecting mode even no item is selected. To do this, add and set `disableEditingWhenNoneSelected` to false.
```dart
controller.disableEditingWhenNoneSelected = false;
controller.set(mainList.length);
```

### Enter Selecting Mode Manually
You can enter selecting mode by changing parameter `isSelecting` of the controller to true.\
Don't forget to use `setState()`!
```dart
setState(() {
  controller.isSelecting = true;
});
```

 
 
## MultiSelectItem Parameters
MultiSelectItem is a widget that handles the multi selecting. Wrap this widget inside listbuilder item.
```dart
//child widget to pass
final Widget child;

//variable to tell the MultiSelectItem wether it's in selecting mode or not.
final bool isSelecting;

//function that will be called based on isSelecting variable.
//if isSelecting is true, then the function will be called when user tap on the MultiSelectItem widget
//if isSelecting is false, then the function will be called when user long tap on the MultiSelectItem widget
final VoidCallback onSelected;
```

## MultiSelectController Parameters
MultiSelectController is a controller that hadles the state of the multi selecting.
```dart
//list of selected indexes
List<int> selectedIndexes;

//the state wether it's in multi selecting mode or not
bool isSelecting = false;

//if set to false, it will not get out from multi selecting mode if no item are selected
bool disableEditingWhenNoneSelected = true;
```

## MultiSelectController Functions
| Function    | Return Type | Parameter | Description                                                                             |
|-------------|-------------|-----------|-----------------------------------------------------------------------------------------|
| set         | void        |           | set the controller length                                                               |
| isSelected  | `bool`      | `int i`   | returns if the specified id is being selected or not                                    |
| select      | void        | `int i`   | select specified id                                                                     |
| deselect    | void        | `int i`   | deselect specified id                                                                   |
| toggle      | void        | `int i`   | toggle specified id                                                                     |
| selectAll   | void        |           | select all                                                                              |
| deselectAll | void        |           | deselect all                                                                            |
| toggleAll   | void        |           | select all if not all list item is selected. deselect all if all list item is selected. |
