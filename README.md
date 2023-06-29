# :books: Modifier extension drag-and-drop #

[![](https://jitpack.io/v/JGomez-Dev/drag-and-drop-compose-library.svg)](https://jitpack.io/#JGomez-Dev/drag-and-drop-compose-library)

![ezgif com-video-to-gif (1)](https://github.com/JGomez-Dev/drag-and-drop-compose-library/assets/49919880/9c19eb25-ff6c-4a73-95c1-3e2a6ff6dbc9)

This project is a library that provides an extension of the Modifier class in Jetpack Compose, allowing you to easily add drag and drop behavior to list items in your applications. With this library, you can enhance the user experience by enabling users to drag items from a list and place them in a new position.

### 📁  Example Repo
https://github.com/JGomez-Dev/drag-and-drop-jetpack-compose

### :control_knobs: How to Use ###

To be able to use it, we will need to include the following in the Gradle file of our module where we want to use it:

```kotlin
implementation ("com.github.JGomez-Dev:drag-and-drop-compose-library:<version>")
```

In the settings.gradle:
```kotlin
pluginManagement {
    repositories {
        ...
        maven { url = uri("https://jitpack.io") }
    }
}

dependencyResolutionManagement {
    repositories {
        ...
        maven { url = uri("https://jitpack.io") }
    }
}
```

## :sparkles: Features
- Drag and drop items within a list.
- Smooth animation and visual effects during drag and drop.
- Reorder items in the list based on user interactions.

## :tada: Usage
To use this drag and drop example in your project, follow these steps:

1. Add the People data class to your project. The People class represents an item in the list and contains a name property.
2. Copy the BasicExample and HomeExample composable functions to your project.
3. Modify the code as needed to fit your project requirements.
4. Use the ExampleList composable function to display the list of items with drag and drop functionality. Pass the necessary parameters, such as the list of people, slide states, and update functions.
5. Make sure you have the necessary dependencies and setup for Compose and Material Design in your project.

## :computer: Code

Example
Here's an example usage of the drag and drop functionality:
```kotlin

val peopleList = mutableListOf(
    People(name = "Martin Jenkins"),
    People(name = "Jacquelyn Keith"),
    People(name = "Lavern Chen")
)

@Composable
fun App() {
    HomeExample()
}

@ExperimentalAnimationApi
@Composable
fun HomeExample() {
     val slideStates = remember {
        mutableStateMapOf<People, SlideState>()
            .apply {
                peopleList.map { example ->
                    example to SlideState.NONE
                }.toMap().also {
                    putAll(it)
                }
            }
    }
    Scaffold(
        topBar = {
            TopAppBar(
                title = {
                    Text(text = "Drag and Drop In Compose")
                }
            )
        }
    ) { innerPadding ->
        ExampleList(
            modifier = Modifier.padding(innerPadding),
            peopleList = peopleList,
            slideStates = slideStates,
            updateSlideState = { people, slideState ->
                // Handle slide state updates here
            },
            updateItemPosition = { currentIndex, destinationIndex ->
                // Handle item position updates here
            }
        )
    }
}

@ExperimentalAnimationApi
@Composable
fun ExampleList(
    modifier: Modifier,
    peopleList: MutableList<People>,
    slideStates: Map<People, SlideState>,
    updateSlideState: (people: People, slideState: SlideState) -> Unit,
    updateItemPosition: (currentIndex: Int, destinationIndex: Int) -> Unit
) {
    val lazyListState = rememberLazyListState()
    LazyColumn(
        state = lazyListState,
        modifier = modifier.padding(top = dimensionResource(id = R.dimen.list_top_padding))
    ) {
        items(peopleList.size) { index ->
            val example = peopleList.getOrNull(index)
            if (example != null) {
                key(example) {
                    val slideState = slideStates[example] ?: SlideState.NONE
                    BasicExample(
                        peoble = example,
                        peopleList = peopleList,
                        slideState = slideState,
                        updateSlideState = updateSlideState,
                        updateItemPosition = updateItemPosition
                    )
                }
            }
        }
    }
}

@Composable
fun BasicExample(
    peoble: People,
    peopleList: MutableList<People>,
    slideState: SlideState,
    updateSlideState: (shoesArticle: People, slideState: SlideState) -> Unit,
    updateItemPosition: (currentIndex: Int, destinationIndex: Int) -> Unit
) {
    val itemHeightDp = dimensionResource(id = R.dimen.image_size)

    val verticalTranslation by animateIntAsState(
        targetValue = when (slideState) {
            SlideState.UP -> -itemHeight
            SlideState.DOWN -> itemHeight
            else -> 0
        },
        label = "",
    )
    val isDragged = remember { mutableStateOf(false) }
    val zIndex = if (isDragged.value) 1.0f else 0.0f
    val rotation = if (isDragged.value) -5.0f else 0.0f
    val elevation = if (isDragged.value) 8.dp else 0.dp

    val currentIndex = remember { mutableStateOf(0) }
    val destinationIndex = remember { mutableStateOf(0) }
    val isPlaced = remember { mutableStateOf(false) }

    with(LocalDensity.current) {
        itemHeight = itemHeightDp.toPx().toInt()
    }

    LaunchedEffect(isPlaced.value) {
        if (isPlaced.value) {
            launch {
                if (currentIndex.value != destinationIndex.value) {
                    updateItemPosition(currentIndex.value, destinationIndex.value)
                }
                isPlaced.value = false
            }
        }
    }
    Column {
        Box(
            Modifier
                .dragAndDrop(
                    peoble,
                    peopleList,
                    itemHeight,
                    updateSlideState,
                    isDraggedAfterLongPress = false,
                    { isDragged.value = true },
                    { cIndex, dIndex ->
                        isDragged.value = false
                        isPlaced.value = true
                        currentIndex.value = cIndex
                        destinationIndex.value = dIndex
                    }
                )
                .padding(horizontal = 16.dp)
                .offset { IntOffset(0, verticalTranslation) }
                .zIndex(zIndex)
                .rotate(rotation)

        ) {
            Column(
                modifier = Modifier
                    .shadow(elevation, RoundedCornerShape(8.dp))
                    .clip(RoundedCornerShape(8.dp))
                    .align(Alignment.CenterStart)
                    .fillMaxWidth()

            ) {
                Row(horizontalArrangement = Arrangement.SpaceBetween) {
                    Text(text = peoble.name)
                    Image(
                        modifier = Modifier
                            .size(itemHeightDp),
                        painter = painterResource(id = R.mipmap.ic_climbig_blue),
                        contentDescription = ""
                    )
                }
            }
        }
    }
}

```
## 🙌 Contributing
Contributions are welcome! If you find any issues or have suggestions for improvements, please create a pull request or open an issue in this repository.

## :pencil: License

MIT License

Copyright (c) 2023 Javier Gómez

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
