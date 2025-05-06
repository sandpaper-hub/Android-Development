**Flow**-контейнеры (**FlowRow** и **FlowColumn**) предназначены для потокового размещения содержимого, когда содержимое автоматические помещается на следующую строку или столбец, если в текущей строке/столбце место закончилось.

### FlowColumn

**FlowColumn** располагает элементы сверху вниз, а когда в столбце заканчивается место, переходит к следующему столбцу и продолжает размещать элементы, пока они не закончатся. Этот компонент имеет следующие параметры:

```kotlin
@Composable
@ExperimentalLayoutApi
fun FlowColumn(
    modifier: Modifier = Modifier,
	verticalArrangement: Arrangement.Vertical = Arrangement.Top,
    horizontalArrangement: Arrangement.Horizontal = Arrangement.Start,
    maxItemsInEachColumn: Int = Int.MAX_VALUE,
    maxLines: Int = Int.MAX_VALUE,
    overflow: FlowColumnOverflow = FlowColumnOverflow.Clip,
    content: @Composable FlowColumnScope.() -> Unit
): Unit
```

- `modifier`: применяемые к компоненту функции модификатора.
    
- `horizontalArrangement`: расположение вложенных компонентов по горизонтали.
    
- `verticalArrangement`: расположение вложенных компонентов по вертикали.
    
- `maxItemsInEachColumn`: максимальное количество элементов в столбце
    
- `maxLines`: максимальное количество строк
    
- `overflow`: принцип переноса элементов
    
- `content`: содержимое контейнера

## Простейший пример **FlowColumn**:
```kotlin
@OptIn(ExperimentalLayoutApi::class)  
@Composable  
@Suppress("FunctionName")  
fun FlowColumnExample() {  
    val elements = (1..16).toList()  
    Column(  
        modifier = Modifier  
            .fillMaxWidth()  
            .padding(12.dp)  
            .border(2.dp, MaterialTheme.colorScheme.primaryContainer, RoundedCornerShape(8.dp))  
            .padding(16.dp),  
        horizontalAlignment = Alignment.CenterHorizontally  
    ) {  
        Text("FlowColumn example")  
        FlowColumn(  
            maxItemsInEachColumn = 5  
        ) {  
            elements.forEach { element ->  
                Box(  
                    modifier = Modifier  
                        .size(100.dp, 100.dp)  
                        .padding(8.dp)  
                        .background(  
                            MaterialTheme.colorScheme.primaryContainer,  
                            RoundedCornerShape(4.dp)  
                        ),                    contentAlignment = Alignment.Center  
                ) {  
                    Text("Element $element")  
                }  
            } 
        }    
    }
}
```

Стоит отметить, что **FlowColumn** на момент написания текущей статьи находится в экспериментальном режиме, поэтому перед компонентом, который использует FlowColumn указывается аннотация `@OptIn(ExperimentalLayoutApi::class)`

### FlowRow
**FlowRow** располагает элементы слева направо при левосторонней ориентации или справа налево при правосторонней ориентации, а когда заканчивается место, переходит к следующей строке и продолжает заполнять компоненты, пока они не закончатся. Этот компонент имеет следующие параметры:
```kotlin
@Composable
@ExperimentalLayoutApi
fun FlowRow(
    modifier: Modifier = Modifier,
    horizontalArrangement: Arrangement.Horizontal = Arrangement.Start,
    verticalArrangement: Arrangement.Vertical = Arrangement.Top,
    maxItemsInEachRow: Int = Int.MAX_VALUE,
    maxLines: Int = Int.MAX_VALUE,
    overflow: FlowRowOverflow = FlowRowOverflow.Clip,
    content: @Composable FlowRowScope.() -> Unit
): Unit
```

- `modifier`: применяемые к компоненту функции модификатора.
    
- `horizontalArrangement`: расположение вложенных компонентов по горизонтали.
    
- `verticalArrangement`: расположение вложенных компонентов по вертикали.
    
- `maxItemsInEachRow`: максимальное количество элементов в строке
    
- `maxLines`: максимальное количество строк
    
- `overflow`: принцип переноса элементов
    
- `content`: содержимое контейнера

## Простейший пример **FlowRow**:
```kotlin
@OptIn(ExperimentalLayoutApi::class)  
@Composable  
@Suppress("FunctionName")  
fun FlowRowExample() {  
    val elements = (1..16).toList()  
    Column(  
        modifier = Modifier  
            .fillMaxWidth()  
            .padding(12.dp)  
            .border(2.dp, MaterialTheme.colorScheme.primaryContainer, RoundedCornerShape(8.dp))  
            .padding(16.dp)  
    ) {  
        Text("FlowRow example")  
        FlowRow(maxItemsInEachRow = 4) {  
            elements.forEach { element ->  
                Box(  
                    modifier = Modifier  
                        .size(100.dp, 100.dp)  
                        .padding(8.dp)  
                        .background(  
                            MaterialTheme.colorScheme.primaryContainer,  
                            RoundedCornerShape(4.dp)  
                        ), contentAlignment = Alignment.Center  
                ) {  
                    Text("Element $element")  
                }  
            }       
        }    
    }
}
```

Стоит отметить, что если элементов много, то какая-то часть FlowRow/FlowColumn окажется вне области обзора, соответственно для просмотра невидимой части следует использовать прокрутку. По умолчанию для FlowRow/FlowColumn прокрутка автоматически не создается. Нам самим надо определять прокрутку, например, с помощью модификатора `Modifier.horizontalScroll()` (для FlowColumn) или `verticalScroll()` (для FlowRow)