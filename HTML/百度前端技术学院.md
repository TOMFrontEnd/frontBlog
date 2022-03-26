[JavaScript基本功修練：Day22 - 回傳函式與立即函式(IIFE)](https://ithelp.ithome.com.tw/articles/10250445) 

# CALL BACK AND IIFE

在學習高階函式和非同步概念時，不時會聽到回傳函式(callback function)這個字，但一直都不太理解當中的意思。此外，關於函式這個課題，還有一款函式叫做立即函式(IIFE)。因此，今天這篇文章就來一併看看這兩個函式到底是什麼意思。

為什麼需要回傳函式
JavaScript的執行次序是從頭到尾去執行。但有些情況下，我們想要確保當某個函式被執行完後，或者某個事件被觸發後，我們才去執行另一個函式。可是，JavaScript不管怎樣還是會從頭到尾執行，這樣的方式會破壞了我們的計劃，使本來要等某函式完成，或某事件被觸發，才會執行的函式，都一併被執行了。

然而，回傳函式卻能解決上面的問題，它確保會等到某函式完成後，或某事件被觸發後，才會執行另一個函式。

題外話，說到這裏，我們很容易會聯想起同步和非同步的概念，甚至把非同步與回傳函式混為一談。可是，回傳函式不一定是非同步，它也可以是同步，這要看你是如何呼叫回傳函式，以下例子參考自這裏：

function func(x){
    console.log(x)
}

//回傳函式，但它是被同步執行
function func1(num, callback){
    callback(num)
}

//回傳函式，但它是被非同步執行
function func2(num, callback){
    setTimeout(callback, 1000, num)
}

func1(10,func)
func2(10,func)
func1 ：func1的確是回傳函式的做法，但它是同步執行，同步的意思是做完一個接一個，而這裏是先執行fun1，再回傳函式，執行次序是一個接一個。

func2：在執行fun2時，我們用setTimeout回傳函式，即是在相隔某段時間後才執行該被回傳的函式，這時該函式會被放到工作佇例(event queue)暫緩執行，待同步執行的函式都被執行好了，才會去執行工作佇例裏的函式，即是非同步的函式。

上面提及的同步與非同步的概念會在另一篇文章再詳細解釋，這時候只要記得回傳函式不一定是非同步就行。

建立回傳函式
回到重點，回傳函式就是把函式當成參數，傳入另一個函式裏後再呼叫它。

setTimeout、setInterval計時器例子
function message(text){
    console.log(text)
}

window.setTimeout(message, 1000, 'Hello World!')
setTimeout是瀏覽器的window物件內置的一個方法，像計時器一樣，在某個時間後才會執行你所傳進去的那個函式。這個例子就是把message當作參數，傳進去setTimeout這個方法裏，再隔1秒(1000毫秒)執行。

當然也可以寫行這樣：

window.setTimeout(function(){
    console.log('Hello World!')
}, 1000)
另一個運作原理相似的語法是setInterval：

window.setInterval(function(){
    console.log('Hello World!')
}, 1000)
不同的是，setInterval會不斷回傳函式，這個例子就是每隔1秒就顯示Hello World!，但是setTimeout就只會回傳一次函式。

監聽事件例子
簡單例子。監聽click事件，當button被按下時，就會回傳一個匿名函式：

<button id="myButton">button</button>
const btn = document.getElementById('myButton');
//按下button後回傳函式
btn.addEventListener('click',function(){
    console.log('You clicked the button')
})
陣列的高階函式例子
const arr = [1,2,3,4]

//forEach方法
arr.forEach( function(num){
    console.log(num) //1,2,3,4
})

//reduce方法
const result = arr.reduce( function(a,b){
    return a + b
})

console.log(result) //10
我們常常用到的高階函式，例如forEach、reduce、map等等，它們的運作同樣是回傳函式。在這個例子中，這些回傳的函式都是屬於同步執行。

回傳地獄問題(Callback Hell)
使用回傳函式時，要注意不要過度依賴回傳函式，造成多層的回傳函式，這樣會大大加重閱讀和維護的難度。以下簡單示範什麼是多層的回傳函式，例子參考自這裏：

function callback(num,cb){
    console.log(num) 
    cb(num)
}

callback(10, function(num1){  //10
    var x = num1 + 10
        callback(x, function(num2){ //20
            var y = num2 + 10
            callback(y, function(num3){ //30 
                console.log(num3 + 10) //40 
                console.log('end of hell')
            })
        })
    })
這裏所做的事就是：

//step: 
// 1. 呼叫callback > 帶 10 和 function 進去 > 顯示10 > 呼叫function > num1 變成 10 > x = 20 (10+10)
// 2. 呼叫callback > 帶 x 和 function 進去 > 顯示20 > 呼叫function > num2 變成 20 > y = 30 (20+10)
// 3. 呼叫callback > 帶 y 和 function 進去 > 顯示30 > 呼叫function > num3 變成 30 > 顯示 40 (30+10) > 顯示 'end of hell'
// 看似只呼叫了3次函式，但過程一共呼叫了6次函式
嗯...這樣跳來跳去的做法根本是違反人類思考的習慣QQ。如果途中還要出現錯誤，除錯時就更加是一場惡夢了。針對這個情況，現時會建議用promise、async/await這些語法處理，讓程式碼更易閱讀和維護，這些語法會在另一篇文章再解釋。

立即函式(IIFE)
立即函式(Immediately Invoked Function Expression)，按字面理解就是該函式會立即被執行。簡單講就是會被立即執行的匿名函式。它的寫法有兩個重點：

用()包著函式
在函式後補上一個()(裏面可帶參數)，來立即執行該函式
例子如下：

(function(){
    console.log('IIFE!')
})();
我們可以利用IIFE，把立即執行後得出來的值，賦予給變數：

const x = (function(){
    return 'IIFE!'
})();

console.log(x) //IIFE!
立即函式的用處
避免使用被汙染的全域變數
其中一個常見的情況是用立即函式來避免使用到被汙染的全域變數，例如以下例子：

//連續吐出5個5
for(var i=0; i<5; i++){
    setTimeout(function(){
        console.log(i)}
    ,1000)
}

//分別吐出0,1,2,3,4,5
for(var i=0; i<5; i++){
    (function(num){
        setTimeout(function(){
            console.log(num)}
        ,1000)
    })(i)
}
在ES6的let出現前，這個例子我們會用立即函式的方式，避免跑完整個迴圈後的i，會汙染到全域的i，令之後setTimeout裏的函式不斷吐出已經被汙染的i(即是5)。原因是每跑一次迴圈，立即函式會把當下的i數值傳進setTimeout的函式，使它之後吐出來的i不會是全域的i。

避免大量宣告函式，佔據大量記憶體空間
當我們宣告函式，該函式會被加至全域window物件中，如果我們宣告大量函式，就會佔據大量記憶體位置，拖慢瀏覽器的運作(當物件不再是全域時，才會釋放記憶體空間)。

但是，如果只是單純一個立即函式，而它不是儲存到全域變數，那麼它就不屬於全域物件，從而減低對記憶體空間的影響。

避免命名衝突和意外呼叫函式
此外，以上情況也會使之後命名函式時會更容易造成名字上的衝突，也可能會意外呼叫某個函式。然而，當我們使用立即函式，就能避免以上的情況，因為它是立即被執行掉，之後不能再被呼叫。

語意更精確
同時，在語意上也更精確。如果宣告一個函式，但我們只會使用它一次，或者只用作初始化變數，這個情況用立即函式就會比一般函式更為合適，當我們閱讀程式碼時，就更快能了解該函式的用處是什麼了。

ES6的const和let，取代立即函式避免汙染全域的優點
在ES6的const和let出現前，不少開發者都會用立即函式來避免汙染到全域變數(就像上面曾提及的for迴圈吐出0-5的例子)。可是，ES6新增了const和let，當中採用區塊作用域(大括號{}內)來介定變數的存活範圍，可以說是完全取代了立即函式在這方面的優勢了。

總結
回傳函式：

把函式當成參數，傳入另一個函式裏後再呼叫它
回傳函式可以是同步，也可以是非同步
注意回傳地獄問題
立即函式：

一個會立即執行的匿名函式
好處：避免使用被汙染的全域變數、命名衝突、意外呼叫函式，變得更語意化
let和const取代了立即函式在避免汙染到全域變數上的優勢