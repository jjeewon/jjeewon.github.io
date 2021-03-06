---
layout: post
title:  "Flow in Android"
author: ella
categories: [ Android, Coroutine ]
image: assets/images/flow_in_android_title.png
---
๐ค  
# ๊ฐ๋ ๐ Flow in Android  

Flow๋ ์์ฐจ์ ์ผ๋ก ์ฌ๋ฌ ๊ฐ์ emitํ๋ ๋ฐ์ดํฐ์คํธ๋ฆผ์ด๋ฉฐ, ๋จ ํ๋์ ๊ฐ๋ง ๋ฐํํ๋ suspending ํจ์์๋ ๋ค๋ฅด๋ค. flow๋ฅผ ์ฐ๋ฉด DB์์ ์ค์๊ฐ ์๋ฐ์ดํธํ๋ ๊ฐ๋ค์ ์์ ํ  ์ ์๋ค.  
flow๋ ์ฝ๋ฃจํด์ ๊ธฐ๋ฐ์ผ๋ก ๋น๋๋๋ฉฐ, ๋น๋๊ธฐ์์ผ๋ก ๊ณ์ฐํ  ์ ์๋ ๋ฐ์ดํฐ ์คํธ๋ฆผ์ด๋ค. flow์์ emitํ๋ ๊ฐ๋ค์ ๋์ผํ ํ์์ด์ฌ์ผ ํ๋ค. ์๋ฅผ ๋ค์ด์ ```Flow<Int>```๋ Int ๊ฐ๋ง์ ๋ด๋ณด๋ด๋ flow๊ฐ ๋๋ค.  
๊ฐ ์ํ์ค๋ฅผ ์์ฑํ๋ ```Iterator```์ ์ ์ฌํ์ง๋ง, flow๋ suspending ํจ์๋ฅผ ์ด์ฉํ์ฌ ๊ฐ์ ๋น๋๊ธฐ๋ก ๋ง๋ค์ด์ ์ด๋ค. flow๋ฅผ ์ฐ๋ฉด ๊ธฐ๋ณธ ์ค๋ ๋๋ฅผ blockํ์ง ์๊ณ ๋ ๋คํธ์ํฌ ์์ฒญ์ ํด์ ๋ค์ ๊ฐ์ ๋ง๋ค์ด๋ผ ์ ์๋ค.  

## Data Stream์ ์ธ ๊ฐ์ง ์์ : ์์ฐ์ + ์ค๊ฐ์(option) + ์๋น์
<p align="center"><img src="/assets/images/flow_1.png"></p>
* ์์ฐ์(Producer) : ์คํธ๋ฆผ์ ์ถ๊ฐ๋๋ ๋ฐ์ดํฐ๋ฅผ ์์ฐํจ. flow์์ ๋น๋๊ธฐ์ ์ผ๋ก ๋ฐ์ดํฐ๋ฅผ ์์ฐํจ
* ์ค๊ฐ์(Intermediaries, option) : ์คํธ๋ฆผ์ ๋ด๋ณด๋ด๋ ๊ฐ๊ฐ์ ๊ฐ์ ์์ ํ๊ฑฐ๋, ์คํธ๋ฆผ ์์ฒด๋ฅผ ์์ ํจ
* ์๋น์(Consumer) : ์คํธ๋ฆผ์ ๊ฐ์ ์ฌ์ฉํจ

์๋๋ก์ด๋์์ ์ผ๋ฐ์ ์ผ๋ก repository๋ UI ๋ฐ์ดํฐ์ Producer๋ค. ๊ทธ๋ฆฌ๊ณ  UI๋ ์ต์ข์ ์ผ๋ก ๋ฐ์ดํฐ๋ฅผ ํ์ํ๋ Consumer๋ค. UI ๋ ์ด์ด๋ User Input Event์ Producer์ด๋ฉฐ, ๊ณ์ธต ๊ตฌ์กฐ์ ๋ค๋ฅธ ๋ ์ด์ด๊ฐ input event๋ฅผ ์ฌ์ฉํ๊ธฐ๋ ํ๋ค. Producer์ Consumer ์ฌ์ด์ ์๋ ๋ ์ด์ด๋ ์ผ๋ฐ์ ์ผ๋ก ์ค๊ฐ์์ ์ญํ ์ ํ๋๋ฐ, ๋ค์ ๋ ์ด์ด์ ์๊ตฌ์ฌํญ์ ๋ง์ถฐ์ฃผ๊ธฐ ์ํด์ ๋ฐ์ดํฐ ์คํธ๋ฆผ์ ์์ ํด์ค๋ค.  
## ๐ Flow ๋ง๋ค์ด๋ณด๊ธฐ
Flow๋ฅผ ๋ง๋ค๋ ค๋ฉด ์์ ํฌ์คํํ๋ ๋ด์ฉ์์ ๋ณธ ๋ฐ์ ๊ฐ์ด Flow Builder API๋ฅผ ์จ์ผํ๋ค. flow ๋น๋ ํจ์๋ ```emit``` ํจ์๋ฅผ ์ด์ฉํ์ฌ ์๋ก์ด ๊ฐ์ ๋ฐ์ดํฐ ์คํธ๋ฆผ์ ๋ด๋ณด๋ด๋ **์๋ก์ด flow**๋ฅผ ๋ง๋ค์ด์ค๋ค.  
๋ฐ์ ์ฝ๋์์ ```DataSource```๋ ๊ณ ์ ๋ ๊ฐ๊ฒฉ์ผ๋ก ์ต์  ๋ด์ค๋ฅผ ์๋์ผ๋ก ๊ฐ์ ธ์จ๋ค. suspending ํจ์๋ ๋จ์ผ ๊ฐ์ ๋ฐํํ  ๋ฟ, ์ฌ๋ฌ ๊ฐ์ ๋ฐํํ  ์ ์์ผ๋ฏ๋ก DataSource๊ฐ ์ด๋ฌํ ์๊ตฌ์ฌํญ์ ์ถฉ์กฑํ๋ flow๋ฅผ ๋ง๋ค์ด์ ๋ฐํํ๋ค. ์ด์ ๊ฐ์ ๊ฒฝ์ฐ์์ DataSource๋ **Producer** ์ญํ ์ ํ๋ค.  
``` 
class NewsRemoteDataSource(
    private val newsApi: NewsApi,
    private val refreshIntervalMs: Long = 5000
) {
    val latestNews: Flow<List<ArticleHeadline>> = flow {
        while(true) {
            val latestNews = newsApi.fetchLatestNews()
            emit(latestNews) // flow๋ก request ๊ฒฐ๊ณผ๋ฅผ ๋ด๋ณด๋
            delay(refreshIntervalMs) // refreshIntervalMs๋งํผ ์ฝ๋ฃจํด์ suspendํจ
        }
    }
}

// suspend ํจ์๋ก ๋คํธ์ํฌ ์์ฒญ์ ๋ง๋ค์ด๋์ ์ธํฐํ์ด์ค
interface NewsApi {
    suspend fun fetchLatestNews(): List<ArticleHeadline>
}
```
```flow``` ๋น๋๋ ์ฝ๋ฃจํด ๋ด์์ ์คํ๋๋ฉฐ ์๋์ ๊ฐ์ ์ ํ์ฌํญ์ด ์ ์ฉ๋๋ค.
* flow๋ ์์ฐจ์ ์. Producer๋ ์ฝ๋ฃจํด์ ์์ผ๋ฏ๋ก, suspending ํจ์๋ฅผ ํธ์ถํ๋ฉด producer๋ ์ ์ง ํจ์๊ฐ ๋ฐํ๋  ๋๊น์ง suspend ์ํ๋ก ์ ์ง๋จ. ์์ ์ฝ๋์์ Producer๋ ```fetchLatestNews``` ๋คํธ์ํฌ ์์ฒญ์ด ์๋ฃ๋  ๋๊น์ง ์ ์งํจ. ์๋ฃ๋ ํ์์ผ ๊ฒฐ๊ณผ๋ฅผ ์คํธ๋ฆผ์ ๋ด๋ณด๋ธ๋ค.
* ```flow``` ๋น๋์์ ์์ฐ์๊ฐ ๋ค๋ฅธ ```CoroutineContext```์ ๊ฐ์ ```emit```ํ  ์ ์์. ๋ฐ๋ผ์ ์ ์ฝ๋ฃจํด์ ๋ฐ๋ก ๋ง๋ค๊ฑฐ๋, ```withContext```๋ก ๋ค๋ฅธ ```CoroutineContext```์์ emitํด๋ดค์ ์์ฉ ์์. ์ด๋ฌํ ๊ฒฝ์ฐ์๋ ๋ค๋ฅธ ```callbackFlow```์ ๊ฐ์ flow ๋น๋๋ฅผ ์จ์ผํจ  

## โ๏ธ Stream ์์ ํด๋ณด๊ธฐ
์ค๊ฐ์(Intermediaries)๋ ์ค๊ฐ ์ฐ์ฐ์(```intermediate operator```)๋ฅผ ์ฌ์ฉํจ์ผ๋ก์จ, ๊ฐ์ ์๋นํ์ง ์๊ณ ๋ ๋ฐ์ดํฐ ์คํธ๋ฆผ์ ์์ ํ  ์ ์๋ค. intermediate ์ฐ์ฐ์๋ ๋ฐ์ดํฐ ์คํธ๋ฆผ์ ์ ์ฉ๋๋ ๊ฒฝ์ฐ, ๊ฐ์ด ๋์ค์ ์ฌ์ฉ๋๊ธฐ ์ ๊น์ง๋ ์คํ๋์ง ์๋ work chain์ ์ค์ ํด์ฃผ๋ ํจ์์ด๋ค.  
์๋์ ์ฝ๋์์ Repository ๋ ์ด์ด๋ ์ค๊ฐ ์ฐ์ฐ์ ```map``` ์ฐ์ฐ์๋ฅผ ์ฌ์ฉํ์ฌ ๋ฐ์ดํฐ๊ฐ ```View```์ ํ์๋๋๋ก ๋ณํํ๊ณ  ์๋ค.
   
``` 
class NewsRepository(
    private val newsRemoteDataSource: NewsRemoteDataSource,
    private val userData: UserData
) {
    /**
     * Returns the favorite latest news applying transformations on the flow.
     * These operations are lazy and don't trigger the flow. They just transform
     * the current value emitted by the flow at that point in time.
     */
    val favoriteLatestNews: Flow<List<ArticleHeadline>> =
        newsRemoteDataSource.latestNews
            // Intermediate operation to filter the list of favorite topics
            .map { news -> news.filter { userData.isFavoriteTopic(it) } }
            // Intermediate operation to save the latest news in the cache
            .onEach { news -> saveInCache(news) }
}
```
intermediate ์ฐ์ฐ์๋ ์๊ฐ ๊ฐ๊ฒฉ์ ๋๊ณ  ์์ฐจ์ ์ผ๋ก ์ ์ฉํ  ์ ์์ด์, item๋ค์ flow์ ๋ด๋ณด๋ผ ๋ ๋๋ฆฌ๊ฒ ์คํ๋๋ work chain์ ๊ตฌ์ฑํด์ค๋ค. ์คํธ๋ฆผ์ intermediate ์ฐ์ฐ์๋ฅผ ์ ์ฉํ๋ ๊ฒ๋ง์ผ๋ก๋ flow๊ฐ collect ๋์ง ์๋๋ค.

## ๐ฅ flow์์ collectํด๋ณด๊ธฐ
ํฐ๋ฏธ๋ ์ฐ์ฐ์(```terminal operator```)๋ฅผ ์ฌ์ฉํ๋ฉด, '๊ฐ๋ค์ ์์ ํ๊ธฐ ์ํด listening'์ ์์ํ๋ flow๋ฅผ ํธ๋ฆฌ๊ฑฐํด์ค๋ค. ```collect```์ ์ฐ๋ฉด emitํ  ๋ ์คํธ๋ฆผ์ ๋ชจ๋  ๊ฐ๋ค์ ๊ฐ์ ธ์ฌ ์ ์๋ค.  
```collect```๋ suspending ํจ์์ด๋ฏ๋ก, ์ฝ๋ฃจํด ๋ด์์ ์คํ๋์ด์ผ ํ๋ค. ์๋ก์ด ๊ฐ์์ ํธ์ถ๋๋ ๋งค๊ฐ๋ณ์์๋ ๋๋ค๋ฅผ ์ด๋ค. collect๋ suspending ํจ์๋ผ์ collect๋ฅผ ํธ์ถํ๋ ์ฝ๋ฃจํด์ flow๊ฐ ์ข๋ฃ๋  ๋๊น์ง ์ ์ง๋  ์ ์๋ค.  
์์ ์ฝ๋์์ repository์ ๋ฐ์ดํฐ๋ฅผ ์ฌ์ฉํ๋ ```ViewModel```์ ๊ฐ๋จํ ๊ตฌํํด๋ณด๋ฉด ๋ค์๊ณผ ๊ฐ๋ค. 
```
class LatestNewsViewModel(
    private val newsRepository: NewsRepository
) : ViewModel() {

    init {
        viewModelScope.launch {
            // collect๋ฅผ ํตํด flow๋ฅผ ํธ๋ฆฌ๊ฑฐํ์ฌ element๋ค์ ์๋นํ๋ค.
            newsRepository.favoriteLatestNews.collect { favoriteNews ->
                // ์ฌ๊ธฐ์ ์ต์  favorite news์ ๋ํ View๋ฅผ ์๋ฐ์ดํธํ๋ฉด ๋จ
            }
        }
    }
}
```
flow๋ฅผ collectํ๋ฉด ์ผ์ ํ ์๊ฐ ๊ฐ๊ฒฉ์ผ๋ก ์ต์  ๋ด์ค๋ฅผ ์๋ก๊ณ ์นจํ์ฌ ๋คํธ์ํฌ ์์ฒญ์ ๊ฒฐ๊ณผ๋ฅผ emitํ๋ producer๊ฐ ํธ๋ฆฌ๊ฑฐ๋๋ค. producer๋ while(true) ๋ฃจํ๋ก ํญ์ ํ์ฑ ์ํ๊ฐ ์ ์ง๋๋ฏ๋ก Viewmodel์ด ์ญ์ ๋์ด ```viewModelScope```๊ฐ ์ทจ์๋๋ฉด ๋ฐ์ดํฐ ์คํธ๋ฆผ์ด ์ข๋ฃ๋๋ค.  
## โ ๏ธ flow์ collect๊ฐ ์ค์ง๋๋ ๊ฒฝ์ฐ?
* collect๋ ์ฝ๋ฃจํด์ด ์ทจ์๋ ๊ฒฝ์ฐ, flow์ collect๊ฐ ์ค์ง๋๊ณ  ๊ธฐ๋ณธ producer๋ ์ค์ง๋จ
* producer๊ฐ ํญ๋ชฉ emit์ ๋ชจ๋ ์๋ฃํ ๊ฒฝ์ฐ, ๋ฐ์ดํฐ ์คํธ๋ฆผ์ด ์ข๋ฃ๋๊ณ  collect๋ฅผ ํธ์ถํ ์ฝ๋ฃจํด์ด ๋ค์ ์คํํ๊ธฐ ์์ํจ  
๋ค๋ฅธ intermediate ์ฐ์ฐ์๊ฐ ์ง์ ๋์ง ์์ผ๋ฉด, flow๋ ```cold```ํ๊ณ  ```lazy```ํ๋ค. ์ฆ, flow์์ terminal ์ฐ์ฐ์๊ฐ ํธ์ถ๋  ๋๋ง๋ค, producer ์ฝ๋๊ฐ ์คํ๋๋ค. ์ฌ๋ฌ consumer๊ฐ ๋์์ collectํ๋ ๊ฒฝ์ฐ์ flow๋ฅผ ์ต์ ํ์ํค๊ณ  ๊ณต์ ํ๊ฒ ํ๋ ค๋ฉด ```shareIn```์ฐ์ฐ์๋ฅผ ์ฌ์ฉํ๋ฉด ๋๋ค. 

##  ๐ค Unexpected exception ์บ์นํ๊ธฐ
ํ์ฌ ๋ผ์ด๋ธ๋ฌ๋ฆฌ์์ producer๋ฅผ ๊ตฌํํ๋ ๊ฒฝ์ฐ์ unexpected exception์ด ๋ฐ์ํ  ์ ์๋ค. ์ด๋ฌํ exception์ ์ฒ๋ฆฌํ๋ ค๋ฉด intermediate ์ฐ์ฐ์์ธ ```catch```๋ฅผ ์จ์ผ ํ๋ค. 
```
class LatestNewsViewModel(
    private val newsRepository: NewsRepository
) : ViewModel() {

    init {
        viewModelScope.launch {
            newsRepository.favoriteLatestNews
                // ์ค๊ฐ ์ฐ์ฐ์์ธ catch operator. ๋ง์ฝ์ exception์ด throw๋๋ฉด 
                // catchํ๊ณ  UI๋ฅผ ์๋ฐ์ดํธ ํจ
                .catch { exception -> notifyError(exception) }
                .collect { favoriteNews ->
                    // ์ฌ๊ธฐ์ ์ต์  favorite news์ ๋ํ View๋ฅผ ์๋ฐ์ดํธํ๋ฉด ๋จ
                }
        }
    }
}
```
catch๋ฅผ ์ฐ์ง ์์๋ ์ด์ ์ ์ฝ๋์์๋ exception์ด ๋ฐ์ํ์ ๋, ์ element๊ฐ ์์ ๋์ง ์์ผ๋ฏ๋ก ```collect``` ๋๋ค๊ฐ ํธ์ถ๋์ง ์๋๋ค. ```catch```๋ฅผ ํตํด flow์ ํญ๋ชฉ์ emitํ  ์ ์๋ค. ๋ฐ์ ์ฝ๋์์ repository ๋ ์ด์ด๋ ์บ์๋ ๊ฐ์ emitํ๊ณ  ์๋ค.

```
class NewsRepository(...) {
    val favoriteLatestNews: Flow<List<ArticleHeadline>> =
        newsRemoteDataSource.latestNews
            .map { news -> news.filter { userData.isFavoriteTopic(it) } }
            .onEach { news -> saveInCache(news) }
            // If an error happens, emit the last cached values
            .catch { exception -> emit(lastCachedNews()) }
}
```
์์ ์ฝ๋์์ exception์ด ๋ฐ์ํ๋ฉด ```collect``` ๋๋ค๊ฐ ํธ์ถ๋์ด์ exception์ผ๋ก ์ธํ ์ ํญ๋ชฉ์ด ์คํธ๋ฆผ์ ์ ์ก๋๋ค.

## ๋ค๋ฅธ CoroutineContext์์ ์คํํ๊ธฐ
๊ธฐ๋ณธ์ ์ผ๋ก ```flow``` ๋น๋์ producer๋ collectํ๋ ์ฝ๋ฃจํด์ **CoroutineContext**์์ ์คํ๋๋ค. ๋ค๋ฅธ **CoroutineContext**์์ ๊ฐ์ emitํ  ์๊ฐ ์์ผ๋ฉฐ, ๊ทธ๋ด ๊ฒฝ์ฐ ๋์์ด ์ด์ํ๊ฒ ๋์๊ฐ ์๋ ์๋ค. ์๋ฅผ ๋ค์ด์, ์์ ์์ ์์ repository ๋ ์ด์ด๋ viewModelScope๊ฐ ์ฌ์ฉํ๋ Dispatchers.Main์์ ์์์ ์คํํ๋ฉด ์๋๋ค.  
flow์ **CoroutineContext**๋ฅผ ๋ณ๊ฒฝํ๋ ค๋ฉด intermediate ์ฐ์ฐ์์ธ ```flowOn```๋ฅผ ์จ์ผํ๋ค. ```flowOn```์ upstream flow์ **CoroutineContext**๋ฅผ ๋ณ๊ฒฝํด์ค๋ค. ์ฆ, ```flowOn``` ์ ์ ์๋ producer์ intermediate ์ฐ์ฐ์์ ์ ์ฉ๋๋ค. downstream flow(flowOn ์ดํ์ intermediate ์ฐ์ฐ์์ producer)๋ ```flowOn```์ ์ํฅ์ ๋ฐ์ง ์์ผ๋ฉฐ, flow์์ collectํ๋๋ฐ ์ฌ์ฉํ๋ **CoroutineContext**์์ ์คํ๋๋ค. ```flowOn``` ์ฐ์ฐ์๊ฐ ์ฌ๋ฌ๊ฐ๊ฐ ์๋ค๋ฉด, ๊ฐ ์ฐ์ฐ์๋ ํ์ฌ ์์น์์ upstream์ ๋ณ๊ฒฝํ๋ค.  
```
class NewsRepository(
    private val newsRemoteDataSource: NewsRemoteDataSource,
    private val userData: UserData,
    private val defaultDispatcher: CoroutineDispatcher
) {
    val favoriteLatestNews: Flow<List<ArticleHeadline>> =
        newsRemoteDataSource.latestNews
            .map { news -> // defaultDispatcher์์ ์คํ๋จ
                news.filter { userData.isFavoriteTopic(it) }
            }
            .onEach { news -> // defaultDispatcher์์ ์คํ๋จ
                saveInCache(news)
            }
            // flowOn์ด upstream flow โ ์ ์ํฅ์ ๋ฏธ์นจ
            .flowOn(defaultDispatcher)
            // downstream flow โ ๋ ์ํฅ x
            .catch { exception -> // consumer์ context์์ ์คํ๋จ
                emit(lastCachedNews())
            }
}  
```
์์ ์ฝ๋์์ ```onEach```์ ```map``` ์ฐ์ฐ์๋ defaultDispatcher๋ฅผ ์ฌ์ฉํ๋๋ฐ ๋ฐํด, ```catch``` ์ฐ์ฐ์์ ์๋น์๋ viewModelScope์ ์ฌ์ฉ๋๋ Dispatchers.Main์์ ์คํ๋๋ค.  
DataSource ๋ ์ด์ด๋ I/O ์์์ ์ํํ๋ฏ๋ก, I/O ์์์ ์ต์ ํ๋ ๋์คํจ์ฒ์ธ IO Dispatcher๋ฅผ ์ฌ์ฉํด์ผ ํ๋ค. 
```
class NewsRemoteDataSource(
    ...,
    private val ioDispatcher: CoroutineDispatcher
) {
    val latestNews: Flow<List<ArticleHeadline>> = flow {
        // Executes on the IO dispatcher
        ...
    }
        .flowOn(ioDispatcher)
}
```

## Jetpack ๋ผ์ด๋ธ๋ฌ๋ฆฌ์ flow
Flow๋ ```Jetpack``` ๋ผ์ด๋ธ๋ฌ๋ฆฌ์ ํตํฉ๋๋ฉฐ **์ค์๊ฐ์ผ๋ก ๋ฐ์ดํธ๋ฅผ ์๋ฐ์ดํธ**ํด์ผ ํ๊ฑฐ๋, **๋ฌด์ ํ ๋ฐ์ดํฐ ์คํธ๋ฆผ**์ด ํ์ํ  ๋ ๋งค์ฐ ์ ํฉํ๋ค.  
```Flow with Room```์ ์ฌ์ฉํ๋ฉด DB ๋ณ๊ฒฝ ์๋ฆผ์ ๋ฐ์ ์ ์๋ค. DAO๋ฅผ ์ฌ์ฉํ๋ ๊ฒฝ์ฐ์ ์ค์๊ฐ ์๋ฐ์ดํธ๋ฅผ ๋ฐ์ผ๋ ค๋ฉด, Flow ํ์์ ๋ฐํํ๋ฉด ๋๋ค.
```
@Dao
abstract class ExampleDao {
    @Query("SELECT * FROM Example")
    abstract fun getExamples(): Flow<List<Example>>
}
```
์ด๋ ๊ฒ ์ ์ํด๋์ผ๋ฉด ```Example``` ํ์ด๋ธ์ด ๋ณ๊ฒฝ๋  ๋๋ง๋ค DB์ ์ํญ๋ชฉ์ด ํฌํจ๋ ์ ๋ชฉ๋ก์ด emit๋๋ค. 

## ์ฝ๋ฐฑ ๊ธฐ๋ฐ API๋ฅผ flow๋ก ๋ณํํ๊ธฐ
```callbackFlow```๋ ์ฝ๋ฐฑ ๊ธฐ๋ฐ API๋ฅผ flow๋ก ๋ณํํ  ์ ์๋ flow ๋น๋์ด๋ค. ์๋ฅผ ๋ค์ด์ Firebase Firestore Android API๋ ์ฝ๋ฐฑ์ ์ฌ์ฉํ๋๋ฐ, ์ด API๋ฅผ flow๋ก ๋ณํํ๊ณ , Firebase DB ์๋ฐ์ดํธ๋ฅผ ์์  ๋๊ธฐํ๋ ค๋ฉด ์๋์ ๊ฐ์ ์ฝ๋๋ฅผ ์ด์ฉํ๋ฉด ๋๋ค. 
```
class FirestoreUserEventsDataSource(
    private val firestore: FirebaseFirestore
) {
    // Method to get user events from the Firestore database
    fun getUserEvents(): Flow<UserEvents> = callbackFlow {

        // Reference to use in Firestore
        var eventsCollection: CollectionReference? = null
        try {
            eventsCollection = FirebaseFirestore.getInstance()
                .collection("collection")
                .document("app")
        } catch (e: Throwable) {
            // If Firebase cannot be initialized, close the stream of data
            // flow consumers will stop collecting and the coroutine will resume
            close(e)
        }

        // Registers callback to firestore, which will be called on new events
        val subscription = eventsCollection?.addSnapshotListener { snapshot, _ ->
            if (snapshot == null) { return@addSnapshotListener }
            // Sends events to the flow! Consumers will get the new events
            try {
                offer(snapshot.getEvents())
            } catch (e: Throwable) {
                // Event couldn't be sent to the flow
            }
        }

        // The callback inside awaitClose will be executed when the flow is
        // either closed or cancelled.
        // In this case, remove the callback from Firestore
        awaitClose { subscription?.remove() }
    }
}
```
```flow``` ๋น๋์๋ ๋ฌ๋ฆฌ ```callbackFlow```์์๋ ```send``` ํจ์๋ฅผ ์ฌ์ฉํ๋ฉด ๋ค๋ฅธ ```CoroutineContext```์์ ๊ฐ์ emit ํ  ์ ์๋ค. ๊ทธ๋ฆฌ๊ณ  ```offer``` ํจ์๋ฅผ ์ฌ์ฉํ๋ฉด ๊ฐ์ ์ฝ๋ฃจํด์ ์ธ๋ถ๋ก emitํ  ์ ์๋ค.  
```callbackFlow```๋ ๊ฐ๋์ blocking ```queue```์ ๋งค์ฐ ์ ์ฌํ channel์ ์ฌ์ฉํ๋ค. channel์ capacity(๋ฒํผ๋ง์ด ๊ฐ๋ฅํ element์ ์ต๋ ๊ฐ์)๋ก ๊ตฌ์ฑ๋๋ค. ```callbackFlow```์์ ์์ฑ๋ ์ฑ๋์ ๊ธฐ๋ณธ capacity๋ element 64๊ฐ์ด๋ค. ์ ์ฒด ์ฑ๋์ ์๋ก์ด element๋ฅผ ์ถ๊ฐํ๋ ๊ฒฝ์ฐ (1) ```send```๋ ์๋ก์ด element๋ฅผ ์ํ ๊ณต๊ฐ์ด ์๊ธธ ๋๊น์ง producer๋ฅผ ์ ์งํ๋ค. ๋ฐ๋ฉด์ (2) ```offer```๋ ์ฑ๋์ element๋ฅผ ์ถ๊ฐํ์ง ์๊ณ  ์ฆ์ ```false```๋ฅผ ๋ฐํํด๋ฒ๋ฆฐ๋ค. 
  


[์ฐธ๊ณ  ์ฌ์ดํธ]  
[์๋๋ก์ด๋ ๊ณต์ ๋ฌธ์ - Flow](https://developer.android.com/kotlin/flow)  
