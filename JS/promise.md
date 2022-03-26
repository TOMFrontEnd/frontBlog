為什麼需要Promise?
Promise與回傳函式一樣，同樣是用來處理非同步程式。最常用到非同步程式的情況，都是跟網絡連線有關(例如fetch抓取遠端資料)，或者跟排程有關(例如setTimeout)。

不同的是，Promise改善了非同步的語法結構，比起只用回傳函式，更易閱讀和維護。過往我們要確保非同步函式完成後才執行某個函式，我們會用到回傳函式的方法來完成，可是如果在該回傳函式裏再接一個回傳函式，形成多層的巢狀結構，就會造成回傳地獄(callback hell)的慘況。但用Promise去寫，就會變得較易閱讀。

繼Promise出現的是async/await，也是目前最新的寫法，它是把Promise寫得更簡潔的語法糖，背後操作原理與Promise是一樣。總括來說，Callback function、Promise、async/await其實都是在解決同一個問題：確保非同步函式完成後才執行某個函式。

    function add(a,b){
    return new Promise( (resolve,reject) => {
        window.setTimeout( () => {
            let sum = a + b;
            sum > 0 ? resolve(`${sum}, 成功`) : reject(`${sum}, 失敗`);
        },1000);
    })
    }

    add(10,10)
    .then( success => {
        console.log(success); //20, 成功
        return add(5,10);
    })
    .then( success => {
        console.log(success); //15, 成功
        return add(3,-5);
    })
    .then( success => {
        console.log(success); 
        return add(0,2) //沒有被執行
    })
    .catch( error => {
        console.log(error); //-2, 失敗
    })


