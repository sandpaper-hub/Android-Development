
borderModifier
```kotlin
val Modifier.borderModifier: Modifier  
    @Composable  
    get() = this  
        .fillMaxWidth()  
        .padding(12.dp)  
        .border(2.dp, MaterialTheme.colorScheme.primaryContainer, RoundedCornerShape(8.dp))  
        .padding(12.dp)  
  
@Composable  
@Suppress("FunctionName")  
fun BorderComponent(content: @Composable () -> Unit) {  
    Column(modifier = Modifier.borderModifier,  
        horizontalAlignment = Alignment.CenterHorizontally) {  
        content()  
    }  
}
```