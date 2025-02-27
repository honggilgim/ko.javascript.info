
# FormData

This chapter is about sending HTML forms: with or without files, with additional fields and so on.

[FormData](https://xhr.spec.whatwg.org/#interface-formdata) objects can help with that. As you might have guessed, it's the object to represent HTML form data.

생성자는 다음과 같습니다.
```js
let formData = new FormData([form]);
```

HTML�뿉 `form` �슂�냼媛� �엳�뒗 寃쎌슦, �쐞��� 媛숈�� 肄붾뱶瑜� �옉�꽦�븯硫� �빐�떦 �뤌 �슂�냼�쓽 �븘�뱶 �쟾泥닿�� �옄�룞 諛섏쁺�맗�땲�떎.

The special thing about `FormData` is that network methods, such as `fetch`, can accept a `FormData` object as a body. It's encoded and sent out with `Content-Type: multipart/form-data`.

From the server point of view, that looks like a usual form submission.

## 媛꾨떒�븳 �뤌 �쟾�넚�븯湲�

�븘二� 媛꾨떒�븳 �뤌�쓣 �쟾�넚�븳�떎怨� 媛��젙�빐遊낆떆�떎.

蹂댁떆�떎�떆�뵾 �븘二� 吏㏃�� 肄붾뱶濡쒕룄 �쟾�넚 泥섎━媛� 媛��뒫�빀�땲�떎.

```html run autorun
<form id="formElem">
  <input type="text" name="name" value="John">
  <input type="text" name="surname" value="Lee">
  <input type="submit">
</form>

<script>
  formElem.onsubmit = async (e) => {
    e.preventDefault();

    let response = await fetch('/article/formdata/post/user', {
      method: 'POST',
*!*
      body: new FormData(formElem)
*/!*
    });

    let result = await response.json();

    alert(result.message);
  };
</script>
```

�슂泥��쓣 諛쏆븘 泥섎━�븯�뒗 �꽌踰� 痢� 肄붾뱶�뒗 �뒠�넗由ъ뼹 踰붿쐞瑜� �꽆�뼱�꽌�꽌 異붽���븯吏� �븡�븯�뒗�뜲, �꽌踰꾨뒗 POST �슂泥��쓣 諛쏆븘 '����옣 �꽦怨�'�씠�씪�뒗 �쓳�떟�쓣 蹂대궡以��떎怨� �젙�룄留� �븣怨� 怨꾩떆硫� �맗�땲�떎.

## FormData 硫붿꽌�뱶

`FormData`�뿉 �냽�븯�뒗 �븘�뱶�뒗 �븘�옒��� 媛숈�� 硫붿꽌�뱶濡� �닔�젙�븷 �닔 �엳�뒿�땲�떎.

- `formData.append(name, value)` - add a form field with the given `name` and `value`,
- `formData.append(name, blob, fileName)` - add a field as if it were `<input type="file">`, the third argument `fileName` sets file name (not form field name), as it were a name of the file in user's filesystem,
- `formData.delete(name)` - remove the field with the given `name`,
- `formData.get(name)` - `name`�뿉 �빐�떦�븯�뒗 �븘�뱶�쓽 媛믪쓣 媛��졇�샂
- `formData.has(name)` - if there exists a field with the given `name`, returns `true`, otherwise `false`

A form is technically allowed to have many fields with the same `name`, so multiple calls to `append` add more same-named fields.

`append` 메서드 이외에 필드 추가 시 사용할 수 있는 메서드로 `set`도 있습니다. `set`이 `append` 메서드와 다른 점은 `set`은 `name`과 동일한 이름을 가진 필드를 모두 제거하고 새로운 필드 하나를 추가한다는 데 있습니다. 따라서 `set` 메서드를 쓰면 `name`을 가진 필드가 단 한 개만 있게끔 보장할 수 있습니다. 이 외에 다른 기능은 `append` 메서드와 동일합니다.

- `formData.set(name, value)`,
- `formData.set(name, blob, fileName)`.

참고로 폼 데이터 필드에 반복 작업을 할땐 `for..of` 루프를 사용할 수 있습니다.

```js run
let formData = new FormData();
formData.append('key1', 'value1');
formData.append('key2', 'value2');

// key/value �뙇�씠 �떞湲� 由ъ뒪�듃
for(let [name, value] of formData) {
  alert(`${name} = ${value}`); // key1=value1, then key2=value2
}
```

## Sending a form with a file

The form is always sent as `Content-Type: multipart/form-data`, this encoding allows to send files. So, `<input type="file">` fields are sent also, similar to a usual form submission.

�뙆�씪�씠 �엳�뒗 �뤌 �삁�떆瑜� �궡�렣遊낆떆�떎.

```html run autorun
<form id="formElem">
  <input type="text" name="firstName" value="John">
  Picture: <input type="file" name="picture" accept="image/*">
  <input type="submit">
</form>

<script>
  formElem.onsubmit = async (e) => {
    e.preventDefault();

    let response = await fetch('/article/formdata/post/user-avatar', {
      method: 'POST',
*!*
      body: new FormData(formElem)
*/!*
    });

    let result = await response.json();

    alert(result.message);
  };
</script>
```

## Blob �뜲�씠�꽣媛� �엳�뒗 �뤌 �쟾�넚�븯湲�

<info:fetch> 梨뺥꽣�뿉�꽌 �궡�렣蹂� 諛붿�� 媛숈씠 �씠誘몄�� 媛숈�� �룞�쟻�쑝濡� �깮�꽦�맂 諛붿씠�꼫由� �뙆�씪��� `Blob` 媛앹껜瑜� �궗�슜�빐 �돺寃� �쟾�넚�븷 �닔 �엳�뒿�땲�떎. �씠�븣 `Blob` 媛앹껜�뒗 `fetch` 硫붿꽌�뱶�쓽 `body` 留ㅺ컻蹂��닔�뿉 諛붾줈 �꽆寃⑥쨪 �닔 �엳二�.

In practice though, it's often convenient to send an image not separately, but as a part of the form, with additional fields, such as "name" and other metadata.

�꽌踰� �엯�옣�뿉�꽌�룄 �썝�떆 諛붿씠�꼫由� �뜲�씠�꽣瑜� 諛쏅뒗 寃껊낫�떎 multipart-encoded �뤌�쓣 諛쏅뒗寃� 醫� �뜑 �쟻�빀�븯二�.

This example submits an image from `<canvas>`, along with some other fields, as a form, using `FormData`:

```html run autorun height="90"
<body style="margin:0">
  <canvas id="canvasElem" width="100" height="80" style="border:1px solid"></canvas>

  <input type="button" value="�씠誘몄�� �쟾�넚" onclick="submit()">

  <script>
    canvasElem.onmousemove = function(e) {
      let ctx = canvasElem.getContext('2d');
      ctx.lineTo(e.clientX, e.clientY);
      ctx.stroke();
    };

    async function submit() {
      let imageBlob = await new Promise(resolve => canvasElem.toBlob(resolve, 'image/png'));

*!*
      let formData = new FormData();
      formData.append("firstName", "Bora");
      formData.append("image", imageBlob, "image.png");
*/!*    

      let response = await fetch('/article/formdata/post/image-form', {
        method: 'POST',
        body: formData
      });
      let result = await response.json();
      alert(result.message);
    }

  </script>
</body>
```

Please note how the image `Blob` is added:

```js
formData.append("image", imageBlob, "image.png");
```

�씠 肄붾뱶�뒗 �뤌�뿉 `<input type="file" name="image">` �깭洹멸�� �엳怨�, �궗�슜�옄 湲곌린�쓽 �뙆�씪 �떆�뒪�뀥�뿉�꽌 �뙆�씪紐낆씠 `"image.png"`(3踰덉㎏ �씤�닔 李멸퀬)�씤 `imageBlob` �뜲�씠�꽣(2踰덉㎏ �씤�닔 李멸퀬)瑜� 異붽���븳 寃껉낵 �룞�씪�븳 �슚怨쇰�� 以띾땲�떎.

요청을 받은 서버는 일반 폼과 동일하게 폼 데이터와 파일을 읽고 처리합니다.

## Summary

[FormData](https://xhr.spec.whatwg.org/#interface-formdata) 媛앹껜�뒗 `fetch` �벑�쓽 �꽕�듃�썙�겕 硫붿꽌�뱶瑜� �넻�빐 HTML �뤌�쓣 蹂대궡�뒗�뜲 �궗�슜�맗�땲�떎.

`FormData` 객체는 HTML 폼(`form`)을 직접 넘겨 `new FormData(form)`으로 만들 수도 있고, HTML 폼 없이 다음과 같은 메서드로 필드를 추가해 만들 수도 있습니다.

- `formData.append(name, value)`
- `formData.append(name, blob, fileName)`
- `formData.set(name, value)`
- `formData.set(name, blob, fileName)`

硫붿꽌�뱶瑜� �궗�슜�븷 �븣 二쇱쓽�븷 �젏 2媛�吏�媛� �엳�뒿�땲�떎.

1. The `set` method removes fields with the same name, `append` doesn't. That's the only difference between them.
2. �뙆�씪�쓣 蹂대궪 �븧 �꽭 踰덉㎏ �씤�닔媛� �븘�슂�븳�뜲 �씠 �씤�닔�뒗 �궗�슜�옄 �뙆�씪 �떆�뒪�뀥�뿉�꽌 吏��젙�븳 �뙆�씪紐낃낵 �룞�씪�븯寃� 吏��젙�맗�땲�떎.
Other methods are:

- `formData.delete(name)`
- `formData.get(name)`
- `formData.has(name)`

�떎猷� �궡�슜��� �뿬湲곌퉴吏��엯�땲�떎!
