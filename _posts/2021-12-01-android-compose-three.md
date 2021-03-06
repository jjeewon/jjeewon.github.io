---
layout: post
title:  "Jetpack Compose ์ดํดํ๊ธฐ(3)"
author: ella
categories: [ Android, Compose ]
image: assets/images/compose_title_2.jpeg
---
๐ค  
# ๊ฐ๋ 4๏ธโฃ-3๏ธโฃ Jetpack Compose basics

์ด์  ์ ์ธํ UI์ state, layout, theming๋ค์ ์ด์ฉํ์ฌ ๊ฐ๋ฐํ๋ ๋ฐฉ๋ฒ์ ๋ฐฐ์๋ณด์! ์ด๋ฒ ํฌ์คํธ์์ ์ ์ ์๋ ๊ฒ์ (1) composable๊ณผ modifier์ด ๋ญ์ง, (2) Row์ Column๊ณผ ๊ฐ์ ๊ธฐ๋ณธ UI element๋ค์ด ์ด๋ป๊ฒ ๋์ํ๋์ง, ๊ทธ๋ฆฌ๊ณ  (3) ์ดํ์๋ค๊ฐ state๋ฅผ ๋ถ์ฌํ๋ ๋ฐฉ๋ฒ ์ด๋ค. 

## Compose ๋ฐฐ๊ฒฝ์ง์
Jetpack Compose๋ UI ๊ฐ๋ฐ์ ๋จ์ํ๊ฒ ํ๊ธฐ ์ํด ์ค๊ณ๋ modern toolkit์ด๋ค. Compose๋ reactive ํ๋ก๊ทธ๋๋ฐ ๋ชจ๋ธ๊ณผ conciseness ๊ทธ๋ฆฌ๊ณ  ์ฌ์ฉํ๊ธฐ ์ฌ์ด ์ฝํ๋ฆฐ์ธ์ด์ ์ฅ์ ์ด ๊ฒฐํฉ๋์ด ์๋ค. ๋ํ Composes๋ **๋ฐ์ดํฐ๋ฅผ UI hierarchy๋ก ๋ณํ์ํค๋ ํจ์**๋ค์ ํธ์ถํ์ฌ ๊ฐ๋ฐ์๊ฐ UI๋ฅผ ๋ฌ์ฌํ๊ฒํ๋ค๋ ์ ์์, ์ ์ ์ผ๋ก **declarative**ํ๋ค. ๋ฐ์ดํฐ๊ฐ ๋ณํ๋ฉด, ํ๋ ์์ํฌ๋ UI hierarchy๋ฅผ ์๋ฐ์ดํธํ๋ฉด์ ํด๋น ํจ์๋ค์ ์๋์ผ๋ก ์ฌ์คํํ๋ค.  
Compose ์ฑ์ composable ํจ์๋ค๋ก ๊ตฌ์ฑ๋๋ค. composable ํจ์๋ @Composable ์ด๋ธํ์ด์์ด ๋ถ์ ํจ์์ด๋ฉฐ, ์ด ํจ์์์๋ ๋ค๋ฅธ composable ํจ์๋ฅผ ํธ์ถํ  ์ ์๋ค. composable ํจ์๋ง ์์ผ๋ฉด ๊ฐ๋ฐ์๋ ์๋ก์ด UI ์ปดํฌ๋ํธ๋ฅผ ์์ฑํ  ์ ์๋ค. @Composable ์ด๋ธํ์ด์์ Compose๊ฐ ์ด๋ธํ์ด์์ด ๋ถ์ ํจ์๋ฅผ ์ํฌํธํด์ UI๋ฅผ ์๋ฐ์ดํธํ  ์ ์๊ฒ ํด์ค๋ค. Compose๋ ๋ํ ๊ฐ๋ฐ์๊ฐ ์ฝ๋๋ฅผ ์์ ๋ฉ์ด๋ฆฌ๋ค๋ก ๊ตฌ์กฐํํ  ์ ์๊ฒ ๋์์ค๋ค. ๊ทธ๋ฆฌ๊ณ  composable ํจ์๋ค์ ์งง๊ฒ "composables"๋ผ๊ณ  ๋ถ๋ฅด๋๊น ์์๋์.  
์ฌ์ฌ์ฉํ  ์ ์๋ ์์ composables๋ฅผ ๋ง๋ค๋ฉด, ์ฑ์์ UI element์ ๋ผ์ด๋ธ๋ฌ๋ฆฌ๋ฅผ ๋น๋ํ๊ธฐ๋ ์ฌ์์ง๋ค. ๊ฐ๊ฐ์ element๋ค์ ํ๋ฉด์ ํ ๋ถ๋ถ์ ๋งก์ผ๋ฉฐ, ๊ฐ๋ฐ์๊ฐ ๊ฐ๊ฐ  ๋๋ฆฝ์ ์ผ๋ก ์์ ํ  ์ ์๋ค.

## Composables
ํ๋์ composable ํจ์๋ @Composable ์ด๋ธํ์ด์์ ๋ถ์ธ ํจ์์ด๋ค. ์ด ์ด๋ธํ์ด์์ด ๋ถ์ด์์ผ๋ฉด ํด๋น ํจ์์์ ๋ค๋ฅธ @Composable ํจ์๋ฅผ ํธ์ถํ  ์ ์๋ค. ๋ฐ์ ํจ์์์ Greeting ํจ์๊ฐ @Composable ์ด๋ธํ์ด์์ด ๋ถ์ด์๋๊ฑธ ๋ณผ ์ ์๋ค. ์ด ํจ์๋ String์ ์๋ ฅ๋ฐ์์ UI hierarchy์ ํ ์กฐ๊ฐ์ ์์ฑํ๋ค. ํจ์ ์์ ์๋ ```Text```๋ ๋ผ์ด๋ธ๋ฌ๋ฆฌ์์ ์ ๊ณต๋๋ composable ํจ์์ด๋ค.  
```
@Composable
private fun Greeting(name: String) {
   Text(text = "Hello $name!")
}
```

## Compose in Android app
๋งค๋ํ์คํธํ์ผ์ด ๋ช์๋์ด์๋ ๊ฒ์ฒ๋ผ, ์ฌ์ฉ์๊ฐ ์ดํ์ ์ด๋ฉด MainActivity๊ฐ ์์๋๋ค. ๊ฐ๋ฐ์๋ ์ฌ๊ธฐ์ setContent๋ฅผ ์ด์ฉํ์ฌ ๋ ์ด์์์ ์ ์ํ๋ค. ๋ง์ฝ Compose๋ฅผ ์ฌ์ฉํ๋ค๋ฉด ์ ํต์ ์ธ View ์์คํ์์ ์ฌ์ฉํ๋ XML ํ์ผ์ ์ฐ๋ ๋์ ์, Composable ํจ์๋ค์ ํธ์ถํ๊ฒ ๋๋ค.
```
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            BasicsCodelabTheme {
                // A surface container using the 'background' color from the theme
                Surface(color = MaterialTheme.colors.background) {
                    Greeting("Android")
                }
            }
        }
    }
}
```
### **BasicsCodelabTheme**
* Composable ํจ์๋ค์ ํธ์ถํ๋ ๋ฐฉ๋ฒ ์ค ํ๋
* ํ๋ฉด์์ ํ์คํธ๊ฐ ์ด๋ป๊ฒ ๋ณด์ฌ์ง๋์ง ํ์ธํ๊ณ  ์ถ๋ค๋ฉด, ์๋๋ ์ดํฐ๋ ์ค์  ์๋๋ก์ด๋ ํฐ ๋๋ ```Android Studio preview``` ์ด์ฉํ  ์ ์์

```
@Preview(showBackground = true, name = "Text preview")
@Composable
fun DefaultPreview() {
    BasicsCodelabTheme {
        Greeting(name = "Android")
    }
}
```  

<img src="/assets/images/compose_7.png">  

### **Android Studio preview**
* preview๋ฅผ ์ฌ์ฉํ๋ ค๋ฉด @Preview ์ด๋ธํ์ด์์ ๋ถ์ธ (ํ๋ผ๋ฏธํฐ๊ฐ ์๋ Composable function)์ด๋ (default ํ๋ผ๋ฏธํฐ๋ฅผ ๊ฐ์ง ํจ์)๋ฅผ ๋ง๋ค์ด์ ๋น๋ํด์ผ ํจ
* ๊ฐ์ ํ์ผ์์ ์ฌ๋ฌ ๊ฐ์ ํ๋ฆฌ๋ทฐ๋ฅผ ๊ฐ์ง ์ ์๊ณ  ๊ฐ๊ฐ์ ์ด๋ฆ์ ๋ถ์ฌํ  ์ ์์

## Tweaking the UI


์ด์  Greeting์ ์ํ ๋ฐฑ๊ทธ๋ผ์ด๋ ์ปฌ๋ฌ๋ฅผ ์ธํํด๋ณด์! ๋ฐฑ๊ทธ๋ผ์ด๋ ์ปฌ๋ฌ๋ฅผ ๋ฐ๊พธ๋ ค๋ฉด ```Surface```๋ก Text composable์ ๊ฐ์ธ์ค์ผ ํ๋ค. ```Surface```๋ color๋ฅผ ๊ฐ์ง ์ ์๋ค. ๋ฐ์ ์์์๋ MaterialTheme.colors.primary ๋ฅผ color๋ก ์ฌ์ฉํ๋ค.
```
@Composable
private fun Greeting(name: String) {
    Surface(color = MaterialTheme.colors.primary) {
        Text (text = "Hello $name!")
    }
}
```  

Surface ๋ธ๋ก ์์ ์๋ ์ปดํฌ๋ํธ๋ค์ ํด๋น ๋ฐฑ๊ทธ๋ผ์ด๋ ์ปฌ๋ฌ์์์ ๊ทธ๋ ค์ง๊ฒ ๋๋ค. 

<img src="/assets/images/compose_8.png">  

โ๏ธ Surface์ MaterialTheme์ Material Design๊ณผ ๊ด๋ จ๋ ๊ฐ๋์. Material Design์ ๊ตฌ๊ธ์ด ๊ฐ๋ฐ์๋ค์ด UI๋ฅผ ์์ฑํ๋ ๊ฒ์ ๋์์ฃผ๋ ค๊ณ  ๋ง๋  ๋์์ธ ์์คํ!

์์ ์ฌ์ง์์ ๋ณด๋ฉด ํ์คํธ๊ฐ ํฐ์์ธ ๊ฒ์ ์ ์ ์๋ค. androidx.compose.material.Surface์ ๊ฐ์ Material ์ปดํฌ๋ํธ๋ค์ ํ์คํธ๋ฅผ ์ํด ์ ์ ํ ์ปฌ๋ฌ๋ฅผ ๊ณจ๋ผ์ฃผ๋ ๊ฒ๊ณผ ๊ฐ์ด ๊ฐ๋ฐ์๊ฐ ์ํ  ๊ฒ ๊ฐ์ ๋ถ๋ถ๋ค์ ์์์ ์ผ์ดํด์ค๋ค. Material์ ๋๋จ์ ์ด๋ผ๊ณ ๋ ํ๋๋ฐ, ์๋ํ๋ฉด ๋๋ถ๋ถ์ ์ดํ์ ํํ ์ฐ์ด๋ ์ข์ ๋ํดํธ๊ฐ๋ค๊ณผ ํจํด๋ค์ ์์์ ์ ๊ณตํ๊ธฐ ๋๋ฌธ์ด๋ค. Compose์์ Material ์ปดํฌ๋ํธ๋ค์ ๋ค๋ฅธ foundational ์ปดํฌ๋ํธ๋ค์ ์์ ๊ตฌ์ถ๋์ด์๋๋ฐ, ์ ์ฐํ๊ฒ ์ฒ๋ฆฌํด์ผํ  ๊ฒฝ์ฐ์๋ ์ฑ ์ปดํฌ๋ํธ๊ฐ ํด๋น foundational ์ปดํฌ๋ํธ์ ์ ๊ทผํ  ์ ์๋ค.  
์์ ์ํฉ์์ ```Surface```๋ ๋ฐฑ๊ทธ๋ผ์ด๋ ์์ด ```primary``` ์ปฌ๋ฌ๋ก ์ธํ๋๋ฉด, ๊ทธ ์์ ์ค๋ ํ์คํธ๋ theme์ ์ ์๋ ๋๋ก ```onPrimary``` ์ปฌ๋ฌ๋ฅผ ์จ์ผํ๋ค๊ณ  ์๋์ผ๋ก ์ธ์ํ๋ค. 

## Modifiers  
```Surface```์ ```Text``` ๊ฐ์ ๋๋ถ๋ถ์ Compose UI element๋ค์ ์ต์์ผ๋ก ```modifier```๋ผ๋ ํ๋ผ๋ฏธํฐ๋ฅผ ๊ฐ์ง๋ค. Modifier๋ UI์๊ฒ ๋ถ๋ชจ ๋ ์ด์์์์ ์ด๋ป๊ฒ lay outํ๊ณ , ๋์คํ๋ ์ดํด์ผ ํ๋์ง๋ฅผ ์๋ ค์ค๋ค.  
์๋ฅผ ๋ค์ด์, padding modifier๋ฅผ ํตํด element๊ฐ ์ฅ์ํ๋ ๊ณต๊ฐ ์ฃผ๋ณ์ amount๋ฅผ ์ ํ  ์ ์๋ค. padding modifier๋ ```Modifier.padding()```์ผ๋ก ์ ์ํ๋ค. ์ด์  ํ๋ฉด์ Text์ padding์ ๋ฃ์ด๋ณด์!
```
import androidx.compose.foundation.layout.padding
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp
...

@Composable
private fun Greeting(name: String) {
    Surface(color = MaterialTheme.colors.primary) {
        Text(text = "Hello $name!", modifier = Modifier.padding(24.dp))
    }
}
```
<img src="/assets/images/compose_9.png">  

## Reusing composables

UI์๋ค๊ฐ ์ปดํฌ๋ํธ๋ฅผ ๋ง์ด ๋ฃ์ผ๋ฉด ๋ฃ์์๋ก, nesting ๋ ๋ฒจ์ด ๋์์ง๋ค. ํจ์๊ฐ ์์ฒญ ์ปค์ง๊ฒ ๋๋ฉด, ๊ฐ๋์ฑ์ด ๋จ์ด์ง๊ฒ ๋๋ค. ๋ฐ๋ผ์ ์ฌ์ฌ์ฉํ  ์ ์๋ ์์ componenet๋ค์ ๋ง๋ค์ด์ UI element๋ค์ ๋ผ์ด๋ธ๋ฌ๋ฆฌ๋ฅผ ๋น๋ํ๋ ๊ฒ์ด ์ข๋ค. MyApp์ด๋ผ๋ Composable ํจ์๋ฅผ ๋ง๋ค์ด๋ณด์.
```
import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.padding
import androidx.compose.material.MaterialTheme
import androidx.compose.material.Surface
import androidx.compose.material.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import com.codelab.basicstep1.ui.theme.BasicsCodelabTheme

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            BasicsCodelabTheme {
                MyApp()
            }
        }
    }
}

@Composable
private fun MyApp() {
    Surface(color = MaterialTheme.colors.background) {
        Greeting("Android")
    }
}

@Composable
private fun Greeting(name: String) {
    Surface(color = MaterialTheme.colors.primary) {
        Text(text = "Hello $name!", modifier = Modifier.padding(24.dp))
    }
}

@Preview(showBackground = true)
@Composable
private fun DefaultPreview() {
    BasicsCodelabTheme {
        MyApp()
    }
}
```
MyApp์ด๋ผ๋ composable ํจ์๊ฐ onCreate ์ฝ๋ฐฑ์ด๋ preview์์ ์ฌ์ฌ์ฉ๋์ด ์ฝ๋ ์ค๋ณต ์์ด ๊น๋ํ๊ฒ ์ ๋ฆฌ๋๋ ๊ฒ์ ๋ณผ ์ ์๋ค. 

## column & row ๋ง๋ค์ด๋ณด๊ธฐ
Compose์๋ Column, Row, Box๋ผ๋ ์ธ ๊ฐ์ง ๊ธฐ๋ณธ ๋ ์ด์์ element๊ฐ ์๋ค.  
<img src="/assets/images/compose_10.png">  
Column, Row, Box๋ Composable content๋ฅผ ๊ฐ์ง๋ Composable ํจ์๋ผ์ ์์๋ค๊ฐ ์์ดํ๋ค์ ๋ฃ์ ์ ์๋ค. ์๋ฅผ ๋ค์ด์, Column ์์ ์๋ ์์๋ค์ '์์ง'์ผ๋ก ๋ฐฐ์น๋๋ค.
```  
Column {
    Text("First row")
    Text("Second row")
}
```

์ด๋ฌํ Column์ ์ด์ฉํ์ฌ Greeting์ ๋ฐ๊ฟ๋ณด๋ฉด ๋ค์๊ณผ ๊ฐ์ด ๋๋ค. 
```
import androidx.compose.foundation.layout.Column
...

@Composable
private fun Greeting(name: String) {
    Surface(color = MaterialTheme.colors.primary) {
        Column(modifier = Modifier.padding(24.dp)) {
            Text(text = "Hello,")
            Text(text = name)
        }
    }
}
```

## Compose์ ์ฝํ๋ฆฐ
Composable ํจ์๋ ์ฝํ๋ฆฐ์์์ ๋ค๋ฅธ ํจ์๋ค๊ณผ ๊ฐ์ด ์ฌ์ฉ๋  ์ ์๋ค. ๋ฐ๋ผ์ ๊ฐ๋ฐ์๋ ์ฝํ๋ฆฐ์ผ๋ก UI๊ฐ ๋ณด์ฌ์ง๋๋ฐ์ ์ํฅ์ ์ฃผ๋ statement๋ค์ ์ถ๊ฐํ  ์ ์๊ธฐ ๋๋ฌธ์, UI๋ฅผ ๋ ๊ฐ๋ ฅํ๊ฒ ๋น๋ฉํ  ์ ์๋ค. ์๋ฅผ ๋ค์ด, Column ์์์ for๋ฌธ์ ์ด์ฉํ์ฌ element๋ค์ ์ถ๊ฐํ  ์ ์๋ค. 
```
@Composable
fun MyApp(names: List<String> = listOf("World", "Compose")) {
    Column {
        for (name in names) {
            Greeting(name = name)
        }
    }
}
```
<img src="/assets/images/compose_11.png">  
์์ง composable์ size constraint๋ฅผ ์ฃผ์ง์์๊ธฐ ๋๋ฌธ์, ๊ฐ๊ฐ์ row๊ฐ ์ต์ํ์ ๊ณต๊ฐ์ ์ฐจ์งํ์ฌ์ ์์ ์๋ row๋ ์๋์ ์๋ row๋ ๊ฐ๋ก๊ฐ ๋ง์ง ์๋ค. Preview์์ ํฐ์ ๋ณดํต width์ธ 320dp๋ก ํ๋ผ๋ฏธํฐ๋ฅผ ์ถ๊ฐํด๋ณด์.
```
@Preview(showBackground = true, widthDp = 320)
@Composable
fun DefaultPreview() {
    BasicsCodelabTheme {
        MyApp()
    }
}
```
Modifier๋ Compose์์ ์ ๋ฐ์ ์ผ๋ก ์ฌ์ฉ๋๋ฏ๋ก modifier๋ฅผ ์ด์ฉํ ์ฐ์ต์ ๋ ํด๋ณด์. ๋ค์๊ณผ ๊ฐ์ด ```fillMaxWidth```์ ```padding``` modifier๋ฅผ ์ด์ฉํ์ฌ ์ฝ๋๋ฅผ ์์ฑํ๋ค.
```
@Composable
fun MyApp(names: List<String> = listOf("World", "Compose")) {
    Column(modifier = Modifier.padding(vertical = 4.dp)) {
        for (name in names) {
            Greeting(name = name)
        }
    }
}

@Composable
private fun Greeting(name: String) {
    Surface(
        color = MaterialTheme.colors.primary,
        modifier = Modifier.padding(vertical = 4.dp, horizontal = 8.dp)
    ) {
        Column(modifier = Modifier.fillMaxWidth().padding(24.dp)) {
            Text(text = "Hello, ")
            Text(text = name)
        }
    }
}
```

* Modifier๋ ์ค๋ฒ๋ก๋๋  ์ ์์ผ๋ฏ๋ก, ํจ๋ฉ์ ๋ง๋๋๋ฐ ๋ค๋ฅผ ๋ฐฉ์์ ์ธ ์ ์๋ค.
* modifier๋ฅผ ์ฌ๋ฌ๊ฐ ์ถ๊ฐํ๊ณ  ์ถ๋ค๋ฉด, ๊ทธ๋ฅ chainํ๋ฉด ๋๋ค.(์ฐ๋ฌ์ ์ฐ๋ฉด ๋จ!)

## ๋ฒํผ ์ถ๊ฐํด๋ณด๊ธฐ
์ด์  Greeting์ ํ์ฅํด์ ํด๋ฆญํ  ์ ์๋ element์ธ button์ ์ถ๊ฐํด๋ณด์.  
<img src="/assets/images/compose_11.png">
Button์ ๋ง์ง๋ง argument๋ก composable์ ์ทจํ๋ composable์ด๋ค. trailing lambda๋ ๊ดํธ ๋ฐ์ผ๋ก ์ด๋ํ  ์ ์๊ธฐ ๋๋ฌธ์, button์ ์์์ผ๋ก ์ด๋ค ๋ด์ฉ์ด๋ผ๋ ์ถ๊ฐํ  ์ ์๋ค. ๋ฐ์ ์์์๋ ์์์ผ๋ก Text๋ฅผ ์ถ๊ฐํ๋ค.
```
Button(
    onClick = { } // You'll learn about this callback later
) {
    Text("Show less")
}
```
์์ ๊ทธ๋ฆผ๊ณผ ๊ฐ์ด row์ ๋งจ ๋์ ๋ฒํผ์ด ์ค๊ฒ ํด์ผ ํ๋๋ฐ, composable์๋ alignEnd modifier๊ฐ ์๋ค. ๊ทธ ๋์ ์ ์ฒ์๋ถ๋ถ์ ์๋ composable์๋ค๊ฐ ```weight```์ ์ค ์ ์๋ค. ```weight``` modifier๋ weight๊ฐ ์๋ inflexibleํ element๋ฅผ ์์ผ๋ก ๋ฐ์ด๋ฒ๋ฆฌ๊ณ ์  element๊ฐ ์ฐจ์งํ  ์ ์๋ ๊ณต๊ฐ์ ์ฑ์ฐ๊ฒ ํด์ค๋ค. 
```
import androidx.compose.material.Button
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.remember
...

@Composable
private fun Greeting(name: String) {

    Surface(
        color = MaterialTheme.colors.primary,
        modifier = Modifier.padding(vertical = 4.dp, horizontal = 8.dp)
    ) {
        Row(modifier = Modifier.padding(24.dp)) {
            Column(modifier = Modifier.weight(1f)) {
                Text(text = "Hello, ")
                Text(text = name)
            }
            OutlinedButton(
                onClick = { /* TODO */ }
            ) {
                Text("Show more")
            }
        }
    }
}
```

๐ง Compose์๋ Material Design Buttons spec์ ๋ฐ๋ผ Button์ด ์ฌ๋ฌ ์ข๋ฅ๊ฐ ์๋ค. ```Button```, ```OutlinedButton```, ```TextButton```์ด ํด๋นํ๋ค. ์์ ์ฝ๋์์๋ Button์ content๋ก Text๋ฅผ ๊ฐ์ธ๋ ```OutlinedButton```์ ์ฌ์ฉํ๋ค.

## State in Compose
์ด๋ฒ์๋ ํ๋ฉด์ interaction์ ๋ฃ์ด๋ณด์. ์ง๊ธ๊น์ง๋ staticํ ๋ ์ด์์์ ๋ง๋ค์์ง๋ง, ์ด๋ฒ์๋ ์๋์ ๊ฐ์ด ์ ์ ์ ํ๋์ ๋ฐ์ํ๊ฒ ํด๋ณด์.
![compose_13](https://user-images.githubusercontent.com/18653295/144225394-0970c73f-7cb9-4e99-b345-f0611d934aae.gif)  
ํด๋ฆญํ  ์ ์๋ ๋ฒํผ์ ๋ง๋๋ ๋ฐฉ๋ฒ๊ณผ ์ด๋ป๊ฒ item ํฌ๊ธฐ๋ฅผ ์ฌ์กฐ์ ํ๋์ง์ ๋ํด ์์๋ณด๊ธฐ ์ ์, ๊ฐ๊ฐ์ ์์ดํ์ด ํผ์ณ์ก๋์ง ์๋์ง๋ฅผ ์๋ ค์ฃผ๋ '์์ดํ์ ```state```'๋ฅผ ์ด๋๊ฐ์๋ค๊ฐ ์ ์ฅํด๋์ผ ํ๋ค. greeting๋ง๋ค ์ด ```state``` ๊ฐ๋ค์ ํ๋์ฉ ๊ฐ์ง๊ณ  ์์ด์ผ ํ๋ฏ๋ก, Greeting composable ํจ์์ ```state``` ๊ฐ๋ค์ด ์์ด์ผ ํ๋ค. ๋ฐ์ ์์ ์์ boolean์ธ ```expanded```๊ฐ ์ด๋ป๊ฒ ์ฐ์ด๊ณ  ์๋์ง๋ฅผ ํ์ธํด๋ณด์.(์๋ชป๋ ์์ด์ง๋ง) 
```
@Composable
private fun Greeting(name: String) {
    var expanded = false // Don't do this!

    Surface(
        color = MaterialTheme.colors.primary,
        modifier = Modifier.padding(vertical = 4.dp, horizontal = 8.dp)
    ) {
        Row(modifier = Modifier.padding(24.dp)) {
            Column(modifier = Modifier.weight(1f)) {
                Text(text = "Hello, ")
                Text(text = name)
            }
            OutlinedButton(
                onClick = { expanded = !expanded }
            ) {
                Text(if (expanded) "Show less" else "Show more")
            }
        }
    }
}
```
์์ ์ฝ๋์์ Button์ onClick ์ก์๊ณผ ๋์ ์ธ button text๊ฐ ์ถ๊ฐ๋ ๊ฒ์ ๋ณผ ์ ์๋ค. ํ์ง๋ง, ์์ ์ฝ๋๋ ์ ๋๋ก ์๋ํ์ง ์์ ๊ฒ์ด๋ค. ์๋ํ๋ฉด ```expanded``` ๋ณ์์ ๋ค๋ฅธ ๊ฐ์ ์ค์ ํ๋ค๊ณ  ํด์ Compose๊ฐ state change๋ก ๋ฐ์๋ค์ด์ง ์์์, ์๋ฌด ๋ณํ๊ฐ ์ผ์ด๋์ง ์์ ๊ฒ์ด๊ธฐ ๋๋ฌธ์ด๋ค.  
์ฆ, expanded ๋ณ์๋ฅผ ๋ณํ์์ผ๋ ```recomposition```์ด ๋์ง ์๋ ์ด์ ๋ Compose๊ฐ ์ถ์ ํ๊ณ  ์์ง ์์์์ด๋ค. ๋ํ Greeting์ด ํธ์ถ๋  ๋๋ง๋ค, expanded ๋ณ์๋ false๋ก ๋ฆฌ์๋  ๊ฒ์ด๋ค.  
๋ฐ๋ผ์ ์ํ๋๋๋ก ์๋ํ๊ฒ ํ๊ธฐ ์ํด์ composable์ **internal state**๋ฅผ ์ถ๊ฐํ๋ ค๋ฉด, ```mutableStateOf``` ํจ์๋ฅผ ์จ์ผ ํ๋ค. mutableStateof ํจ์๋ Compose๊ฐ ```State```๋ฅผ ์ฝ๋ ํจ์๋ฅผ recomposeํ๊ฒ ํด์ค๋ค. 

โ๏ธ State์ MutableState : ์ด๋ ํ value๋ฅผ ๊ฐ์ง๊ณ  ์๋ค๊ฐ, ๊ทธ value์ ๋ณํ๊ฐ ์ผ์ด๋ฌ์ ๋ UI ์๋ฐ์ดํธ๋ฅผ trigger(=recompositions)ํด์ฃผ๋ ์ธํฐํ์ด์ค์!
```
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.remember
...

@Composable
fun Greeting() {
    val expanded = remember { mutableStateOf(false) }
    ...
}
```
์ฌ๊ธฐ์ ```remember```๋ **mutable state๋ฅผ ๊ธฐ์ต**ํ๊ณ  ์๊ฒ ํด์ฃผ๋ ์ญํ ์ ํ๋ค. ๋ง์ฝ์ remember ์์ด ๊ทธ๋ฅ ```val expanded = mutableStateOf(false)```๋ผ๊ณ  ํด๋ฒ๋ฆฌ๋ฉด recomposition์ด ์ผ์ด๋  ๋๋ง๋ค false๋ก ์๋ก์ด mutable state๋ฅผ ๋ฆฌ์ํ๋ฏ๋ก ๊ผญ remember๋ฅผ ์จ์ค์ผํ๋ค.  
์ฆ, ```remember```๋ **recomposition์ผ๋ก๋ถํฐ guard**ํ๋ ์ญํ ์ ํ์ฌ ํด๋น state๊ฐ reset๋์ง ์๊ฒ ํด์ค๋ค. 

โ ๏ธ ํด๋์ค์์ internal state๋ private variable ๊ฐ์ ๋๋์ด๋ค. ๋ฐ๋ผ์ ๊ฐ์ composable ํจ์๋ผ๋ ํ๋ฉด์์ ๋ค๋ฅธ ๋ถ๋ถ์์ ์ฐ์ด๋ฉด, UI element๋ ๋ฌ๋ผ์ง๊บผ๊ณ  ๊ทธ ์์์์ state๋ ๊ฐ๊ฐ ๋ค๋ฅธ state๋ผ๋ ๊ฒ์ ์ฃผ์ํ์.  
composable ํจ์๋ ์๋์ ์ผ๋ก state์ subscribe๋๋ค. ๋ง์ฝ์ state๊ฐ ๋ณํํ๋ฉด, ์ด ํ๋๋ฅผ ์ฝ๋ composable ํจ์๋ ๋ฐ๋ ๋ด์ฉ์ ์๋ฐ์ดํธํ๊ธฐ ์ํด recompose๋๋ค. 

## Mutating state and reacting to state changes
**state๋ฅผ ๋ณํ**์ํค๊ธฐ ์ํด Button์ **onClick**์ด๋ผ๋ ํ๋ผ๋ฏธํฐ๋ฅผ ๊ฐ์ง๋ค. onClick์ value๊ฐ ์๋๋ผ **funtion**์ ์ทจํ๋ค. on click ์ก์์ ์ ์ํ๊ธฐ ์ํด์ **๋๋ค ํํ์**์ ์ฌ์ฉํ  ์ ์๋ค. ์๋ฅผ ๋ค์ด, ํ ๊ธ๋๋ expanded state์ ๋ฐ๋ผ ๋ค๋ฅธ ๊ฐ์ ํ์คํธ๋ก ๋ณด์ฌ์ฃผ๋ ์ฝ๋๊ฐ ์๋ค๊ณ  ํด๋ณด์. 
```
OutlinedButton(
                onClick = { expanded.value = !expanded.value },
            ) {
                Text(if (expanded.value) "Show less" else "Show more")
            }
 ```
 ์ด ์ฝ๋๋ฅผ ์คํํ๋ฉด, ๋ฒํผ์ด ํด๋ฆญ๋์์ ๋ button ์์ ์๋ text๊ฐ recomposition๋๋ฉด์ expanded๊ฐ ํ ๊ธ๋๋ ๊ฒ์ ๋ณผ ์ ์๋ค. ๊ฐ๊ฐ์ Greeting์ ์๋ก ๋ค๋ฅธ UI element์ ์ํ๊ธฐ ๋๋ฌธ์, ์๊ธฐ ์์ ๋ง์ expanded state๋ฅผ ๊ฐ์ง๋ค.
 ```
 @Composable
private fun Greeting(name: String) {
    var expanded = remember { mutableStateOf(false) } 

    Surface(
        color = MaterialTheme.colors.primary,
        modifier = Modifier.padding(vertical = 4.dp, horizontal = 8.dp)
    ) {
        Row(modifier = Modifier.padding(24.dp)) {
            Column(modifier = Modifier.weight(1f)) {
                Text(text = "Hello, ")
                Text(text = name)
            }
            OutlinedButton(
                onClick = { expanded.value = !expanded.value }
            ) {
                Text(if (expanded.value) "Show less" else "Show more")
            }
        }
    }
}
```
 
 ![compose_14](https://user-images.githubusercontent.com/18653295/144246424-8d265ef4-7009-498b-ba92-57053b0296f6.gif)  
   
 
## Expanding the item  
 ์ด์  ํ์ํ  ๋ ์์ดํ์ ํ์ฅ์ํค๋ ์ฝ๋๋ฅผ ์์ฑํด๋ณด์. state์ ๋ฐ๋ผ ๋ฌ๋ผ์ง๋ ๋ณ์(extraPadding)๋ฅผ ์ถ๊ฐํ๋ค. 
```
 @Composable
private fun Greeting(name: String) {

    val expanded = remember { mutableStateOf(false) }

    val extraPadding = if (expanded.value) 48.dp else 0.dp
...
```
์์ ์ฝ๋์์ extraPadding์ expanded์๋ ๋ค๋ฅด๊ฒ remember๋ฅผ ์ฐ์ง ์๊ณ  ์๋ค. ์๋ํ๋ฉด extraPadding์ state์ ์์กดํ๊ณ  ๋จ์ ๊ณ์ฐ๋ง ํ๋ ๋ณ์์ด๊ธฐ ๋๋ฌธ์ด๋ค.  
์ด์  Column์ ์๋ก์ด padding modifier๋ฅผ ์ ์ฉํ์ฌ ์ฝ๋๋ฅผ ์์ฑํด๋ณด์.
```
@Composable
private fun Greeting(name: String) {

    val expanded = remember { mutableStateOf(false) }

    val extraPadding = if (expanded.value) 48.dp else 0.dp

    Surface(
        color = MaterialTheme.colors.primary,
        modifier = Modifier.padding(vertical = 4.dp, horizontal = 8.dp)
    ) {
        Row(modifier = Modifier.padding(24.dp)) {
            Column(modifier = Modifier
                .weight(1f)
                .padding(bottom = extraPadding)
            ) {
                Text(text = "Hello, ")
                Text(text = name)
            }
            OutlinedButton(
                onClick = { expanded.value = !expanded.value }
            ) {
                Text(if (expanded.value) "Show less" else "Show more")
            }
        }
    }
}
```
![compose_15](https://user-images.githubusercontent.com/18653295/144247368-585d943a-5554-4e13-a073-67d25ff8fa2c.gif)  
์์ ์ฝ๋๋ฅผ ์๋ฎฌ๋ ์ดํฐ์์ ์คํํด๋ณด๋ฉด, ๋ค์๊ณผ ๊ฐ์ด ์์ดํ์ ๊ฐ๊ฐ ํ์ฅ๋๋ ๊ฒ์ ํ์ธํด๋ณผ ์ ์๋ค.

[์ฐธ๊ณ  ์ฌ์ดํธ]  
[Android ๊ณต์ ๋ฌธ์ - Jetpack Compose basics](https://developer.android.com/codelabs/jetpack-compose-basics?continue=https%3A%2F%2Fdeveloper.android.com%2Fcourses%2Fpathways%2Fcompose%23codelab-https%3A%2F%2Fdeveloper.android.com%2Fcodelabs%2Fjetpack-compose-basics#7)  

