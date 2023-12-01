# JS Promise, Callback

## Callback Fonksiyonları

Bir işlem tamamlandığında veya belirli bir olay meydana geldiğinde çağrılacak olan fonksiyonlardır. Callback fonksiyonları genellikle asenkron işlemlerle kullanılır. Örneğin, bir dosyanın okunması veya bir API isteğinin tamamlanması gibi uzun sürebilecek işlemler için callback fonksiyonları kullanılabilir.

```jsx
function islemYap(ilkSayi, ikinciSayi, callback) {
  let sonuc = ilkSayi + ikinciSayi;
  callback(sonuc);
}
function sonucuGoster(sonuc) {
  console.log("İşlem sonucu: " + sonuc);
}

islemYap(5, 3, sonucuGoster);
```

### Callback hell (Callback Cehennemi)

JavaScript kodunun karmaşık ve iç içe geçmiş callback fonksiyonları ile dolu olduğu bir durumu ifade eder. Bu durum, özellikle derinlemesine iç içe geçmiş asenkron işlemlerle uğraşırken kodun okunabilirliğini ve bakımını zorlaştırabilir. Bu nedenle modern JavaScript geliştirme yaklaşımları, Promises ve async/await gibi daha gelişmiş yöntemleri önerir.

Aşağıdaki örnekte box adındaki elemente 500ms aralıklar ile belirli class ‘lar ekliyoruz. Bu iç içe geçmiş callback fonksiyonlarına callback hell denir. Her setStimeout un süresi 500ms olduğunu biliyoruz ve bu nedenle bu işlem sırası ile move, circle, bigger, colors classlarını ekleyecektir. Peki örneğin move class ‘ının ne zaman ekleneceği belirsiz ise ve bizim bu işlemi sırası ile yapmamız gerekir ise ? Bu gibi callback hell durumlarından kurtulmak için Promise gibi dinleyiciler ile bu işlemi yaptırmak daha sağlıklı olacak. Promise ile takip ederek ne zaman ki move class ‘ı eklenir ondan sonra `circle` class ‘ının eklenmesini isteyeceğiz.

```jsx
setTimeout(() => {
    box.classList.add("move")
  setTimeout(() => {
    box.classList.add("circle")
    setTimeout(() => {
      box.classList.add("bigger")
      setTimeout(() => {
        box.classList.add("colors")
      },500)
    },500)
  },500)
},500)
```

## Promise

Söz vermek anlamı taşır. Bir şeye söz verirsin ve bu iki sonuç doğurur. Söz yerine getirilir veya getirilmez. Söz yerine getirilirse `resolve` çalışır, yerine getirilmez ise `reject` çalışır.

![Opera Anlık Görüntü_2023-10-07_105009_www.youtube.com.png](JS%20Promise,%20Callback%20d32eb380f70543d4baa718a152f9984f/Opera_Anlk_Grnt_2023-10-07_105009_www.youtube.com.png)

Bir işlem bittiğinde bir sonraki işlemi yaptırmak için kullanılır. Bir işlemin ne zaman biteceği belirsiz ise ve işlem bittikten sonra başka bir işlemi yaptırmak istiyorsan `Promise` kullanılabilir.

Bir işlem başarılı veya başarısız olduğunda belirli işlemler yaptırmak istiyorsam Promise kullanılır. Örneğin bir listeniz var ve buna yeni bir değer eklemek istiyorsunuz. Ne zaman ki yeni değer eklenirse o zaman ekrandaki listenizi güncellemeniz gerekir ama olası eklenmeme durumunda ekrandaki listeyi güncellemeye gerek yoktur. 

### Promise neden kullanılır ?

Genellikle bir adrese (API) istek gönderirken cevabın gelmesin ne kadar süreceğini bilmeyiz bu nedenle bir adrese istek gönderip gelecek yanıtı `Promise` ile takip ederiz. İstek başarılı bir sonuç döndürür ise `resolve`, başarısız ise `reject` alanı çalışır. Başarılı bir cevap geldiğinde `.then` ile bir işlem yaptırırız başarısız bir cevap geldiğinde `.catch` ile bir işlem yaptırırız.

### Kullanım

Yeni bir promise tanımladım ve resolve, reject değerlerini alır. İçeride resolve, reject ‘i çağırdım. Herhangi bir şart girmediğim işlem başarılır kabul edilir ve resolve çalıştırılır. resolve çalıştığı için aşağıdaki .then bloğumun içerisindeki işlem yaptırılır.

```jsx
const promise = new Promise(function(resolve, reject) {
    resolve()
    reject()
})

promise
  .then(function(){
    console.log("okey")
  })
  .catch(function(){
    console.log("error")
  })
```

Resolve ve reject ‘e istediğimiz değeri parametre olarak gönderip .then ve .catch bloklarından bu değere ulaşabiliriz.

```jsx
const promise = new Promise(function(resolve, reject) {
  
  const myPerson = {
    name: "Kaira",
    surname: "Katarina",
    age: 21
  }
  
    resolve(myPerson)
    reject()
})

promise
  .then(function(res){
    console.log(res)
  })
  .catch(function(){
    console.log("error")
  })
```

Promise kullanırken belirlenene şartlara göre resolve veya reject çalışacağını belirtmemiz gerekir. Zaten mantığı budur. Bir işlem başarılı ise resolve, başarısız ise reject döndürmemiz gerekir ve başarılı veya başarısız durumuna göre işlem yaptırabiliriz.

```jsx
const promise = new Promise(function(resolve, reject) {

  const check = false;

  if(check){
    resolve()
  } else reject()
})

promise
  .then(function(){
    console.log("success")
  })
  .catch(function(){
    console.log("error")
  })
```

.finally ise promise işlemi ister başarılı isterse başarısız olsun her iki durumda da çalışır. Yani aşağıda check true olduğu için  resolve çalışır, promise resolve olduğu için .then bloğunun içerisindeki işlem yapılır ekrana success yazdırılır son olarak finally bloğu çalışır ve işlem tamamlandı yazdırılır.

```jsx
const promise = new Promise(function(resolve, reject) {

  const check = true;

  if(check){
    resolve()
  } else reject()
})

promise
  .then(function(){
    console.log("success")
  })
  .catch(function(){
    console.log("error")
  })
  .finally(function() {
    console.log("işlem tamamlandı.")
  })
```

API ile çalışırken isteğin ne zaman sonuçlanacağı belirsizdir. Bu nedenle aşağıda temsilen 1500ms bir süre tanımladık fakat bu sürenin ne zaman tamamlanacağı bilmeseydik ve isteğin tamamlanmasının ardından bir işlem yaptırmak isteseydik işte Promise i tam bu mantıkta kullanabiliriz.

```jsx
// boş bir array tanımladık
const arr = [];

// addArr adında bir fonksiyon tanımladık ve value adında bir parametre alır.
// bu fonksiyon geriye bir promise döndürür.
// setTimeout ile 1500ms sonra işlemin gerçekleşeceğini söylerim
// mevcut array in uzunluğunu alırım, şuan için boş yani 0.
// array in içine gelen parametreyi eklerim. Array in uzunluğu artık 1.
// eğer array in içine bir değer eklendi ise uzunluğu arttığı için
// bu işlemi başarılı olmuş kabul ederim ve resolve çalıştırırım.
// eğer uzunluk hala 0 ise ekleme yapılmamıştır ve reject çalışır.

const addArr = (value) => {
	return new Promise((resolve, reject) => {
    setTimeout(() => {
			const count = arr.length;
			arr.push(value)

			if(arr.length > count){
        resolve()
      } else reject("İşlem başarısız");

    },1500)
  })
}

// yukarıda oluşturduğum fonksiyona bir parametre gönderirim ve 1500ms
// sonra o parametreyi array e eklemeye çalışır.
// eklendiğinde resolve çalışır ve resolve çalışır ise .then ile bu işlemin
// başarılı olduğunu yazdırırım
// eğer işlem başarısız olursa reject çalışır bunu da .catch ile yakalarım
// ve ekrana yazdırırım.
// Eğer işlem başarılı olsun veya olmasın her türlü bir şeyi çalıştırmak
// istiyorsam o zaman .finally ile bunu yaparım.
addArr(newObj)
.then(() => console.log("işlem başarılı"))
.catch(mess => console.log(mess))
.finally(() => console.log("promise tamamlandı."))

// bu işlemin sonucunda array 'e eleman ekleneceği için ekrana
// işlem başarılı yazısı yazdırırlır.
```