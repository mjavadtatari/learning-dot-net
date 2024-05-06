## Install Vite and Create React app

برای نصب react قبلا از create react app استفاده میشد اما این پکیج دیگه پشتیبانی نمیشه و بجای آن میتوانیم از Vite استفاده کنیم.

برای ساخت قسمت فرانت اند برنامه، درون روت solution مورد نظر یک cmd باز میکنیم و دستور `npm create vite@latest` را اجرا میکنیم.

سایر سوال های هنگام نصب رو به صورت زیر پاسخ میدهیم:

```cmd
√ Project name: ... client
√ Select a framework: » React
√ Select a variant: » TypeScript + SWC
```

در مرحله بعدی با اجرای دستورات زیر، به داخل فولدر client رفته و نیازمندی های پروژه فرانت را نصب میکنیم:

```cmd
cd client
npm install
```

حال میتوانیم با استفاده از دستور `npm run dev` برنامه را اجرا کنیم. برای تنظیم دستی پورت اجرای فرانت هم میتوانیم فایل `vite.config.ts` را به صورت زیر تغییر دهیم:

```ts
export default defineConfig({
  server: {
    port: 3000,
  },
  plugins: [react()],
});
```

## InLine CSS in React Components

می توانیم به صورت زیر CSS Inline داشته باشیم:

```jsx
<h1 style={{ color: "blue" }}></h1>
```

## Loop through Objects

اگر بخواهیم یک لیست یا شی قابل تکرار را در ری اکت نمایش دهیم، به صورت زیر عمل میکنیم:

```jsx
const products = [
  { name: "product1", price: 100.0 },
  { name: "product2", price: 200.0 },
  { name: "product2", price: 200.0 },
];

function App() {
  return (
    <>
      <ul>
        {products.map((item, index) => (
          <li key={index}>
            {item.name} - {item.price}
          </li>
        ))}
      </ul>
    </>
  );
}

export default App;
```

برای حلقه زدن میتوانیم از متد `.map()` استفاده کنیم.
ورودی این متد یک arrow function می باشد.

میتوانیم arrow function را به چند شیوه زیر بنویسیم:

```js
.map(item => ()) // parentheses after arrow means that we wanna return a value

.map(item => { return item }) // curly brackets after arrow means what we wanna run some code and then maybe retuen a value

.map((item, index) => ()) // seceond parameter in parentheses before arrow means index of each elements in iteration
```

## React Hooks: useState

برای مدیریت state ها میتوانیم از هوک `useState` از کتابخانه `react` استفاده کنیم:

```jsx
import { useState } from "react";

function App() {
  const [products, setProducts] = useState([
    { name: "product1", price: 100.0 },
    { name: "product2", price: 200.0 },
  ]);

  function addProduct() {
    // setProducts([...products, { name: "product3", price: 300.0 }]);
    setProducts((prevState) => [
      ...prevState,
      {
        name: "product" + (prevState.length + 1),
        price: prevState.length * 100 + 100,
      },
    ]);
  }

  return (
    <>
      <ul>
        {products.map((item, index) => (
          <li key={index}>
            {item.name} - {item.price}
          </li>
        ))}
      </ul>
      <button onClick={addProduct}>add</button>
    </>
  );
}

export default App;
```

در مثال فوق: شی products را به صورت یک state تعریف کردیم. در هنگام تعریف state ها، دو مقدار نام و متد set را باید برایشان تعیین کنیم.

در تابع addProduct هم از دو روش برای استفاده از متد set استفاده کردیم:

در روش اول نیازی به استفاده از دیتای قبل به صورت جداگانه نداشتیم و صرفا با گذاشتن `[...products]` مقدار های قبلی را نیز به همراه مقدار جدید در متغیر ذخیره کردیم.

اما در روش دوم: با تعریف pervState درون یک arrowFunction این قابلیت را بوجود آوردیم که بتوانیم از مقادیر قبل نیز استفاده کنیم و بر اساس آنها مقدار جدید را بسازیم.

در زمان استفاده trigger هایی مانند `onClick` باید صرفا نام تابع را به آنها پاس بدهیم و نیازی به نوشتن پرانتز ها نداریم. البته میتوان از arrowFunction ها نیز استفاده کرد.

## React.StrictMode

این قابلیت جدید در React v18 اضافه شده و باعث میشه در مود developement هر request که به سرور ارسال میشه، دوبار تکرار بشه و خطاهای احتمالی اون رو بررسی میکنه!

## React Hooks: useEffect

در این قسمت میخواهیم از هوکی به نام `useEffect` استفاده کنیم.

The useEffect hook is used to perform side effects in your functional components, such as fetching data, subscribing to external events, or manually changing the DOM.

کد زیر را به داخل تابع App اضافه میکنیم:

```jsx
useEffect(() => {
  fetch("http://localhost:5000/api/products")
    .then((response) => response.json())
    .then((data) => setProducts(data));
}, []);
```

به عنوان مثال، میخواهیم با استفاده از `fetch` که توصیه نمیشود، api مورد نظر را صدا کنیم. برای این منظور میتوانیم با استفاده از هوک `useEffect` این کار را به سادگی انجام دهیم.

ورودی اول هوک، باید از نوع `callback` باشد مثل arrowFunction ها!

ورودی دوم هم اختیاری می باشد، ولی `حتما` اگر نمیخواهیم با هر بار render شدن صفحه، این هوک اجرا شود، باید مقدار `[]` براکت خالی را به عنوان مقدار دوم به آن پاس بدهیم.

## .NET Fix CORS

حال برای اینکه ارتباط بین بک اند (localhost:5000) و فرانت اند (localhost:3000) روی یک آدرس نیست، مرورگر ارور CORS را به ما نشان میدهد.

برای رفع آن باید از طریق .net فایل `Program.cs` را به صورت زیر تغییر دهیم:

```c#
// add below code at end of services
builder.Services.AddCors();


// add below code before UseAuthorization()
app.UseCors(opt =>
{
    opt.AllowAnyHeader().AllowAnyMethod().WithOrigins("http://localhost:3000");
});

app.UseAuthorization();
```

## TypeScript vs JavaScript

یکی از تفاوت های جاوا اسکریپت و تایپ اسکریپت در نحوه نمایش ارور هاست. در جاوا اسکریپت اگر خطای نام گذاری یا حتی خطای syntax داشته باشیم، برنامه بدون در نظر گرفتن خطا اجرا می شود ولی قسمت هایی از برنامه به صورت صحیح کار نخواهند کرد.

اما در تایپ اسکریپت اگر خطا در نامگذاری یا syntax باشد، نه تنها برنامه از کار خواهد افتاد و محل خطا را نشان میدهد، بلکه حتی در IDE و در هنگام نوشتن برنامه نیز، IDE سعی میکند محل خطا را به ما نشان دهد.

## Create TypeScript Interface

در این مرحله میخواهیم یکی دیگر از ویژگی های خوب تایپ اسکریپت را بیان کنیم. در تایپ اسکریپت میتوانیم تایپ تعریف کنیم (چیزی شبیه به کلاس)! امااین کار صرفا به این منظور انجام می شود که به برنامه اعلام کنیم چه تایپ هایی داریم و هرکدام از آنها چه نوع پارامتر هایی را دارا هستند.

با انجام این کار، از این به بعد، خود تایپ اسکریپت در برنامه نویسی و استفاده از مدل ها و کار با داده ها به کمک ما می آید و مراقب اشتباهات و خطا های ما خواهد بود.

برای تعریف یک تایپ، ابتدا یک فایل `Product.ts` ایجاد میکنیم و سپس کد های زیر را داخل آن قرار می دهیم:

```ts
export interface Product {
  id: number;
  name: string;
  description: string;
  price: number;
  pictureUrl: string;
  type?: string;
  brand: string;
  quantityInStock?: number;
}
```

`نکته:` برای راحتی کار میتوانیم خروجی JSON از مدل ساخته شده در بک اند را که توسط Swagger ساخته شده را توسط یکی از ابزار های آنلان تبدیل `JSON to TS` به کد های تایپ اسکریپت تبدیل کنیم.

برای مشخص کردن پارامتر های nullable یا optional هم میتوانیم از علامت سوال در مقابل نام آنها استفاده کنیم.

حال کد های قبلی فایل `App.tsx` به صورت زیر تغییر خواهند کرد:

```tsx
import { useEffect, useState } from "react";
import { Product } from "./Product";

function App() {
  const [products, setProducts] = useState<Product[]>([]);

  useEffect(() => {
    fetch("http://localhost:5000/api/products")
      .then((response) => response.json())
      .then((data) => setProducts(data));
  }, []);

  function addProduct() {
    setProducts((prevState) => [
      ...prevState,
      {
        id: prevState.length + 101,
        brand: "new",
        name: "product" + (prevState.length + 1),
        price: prevState.length * 100 + 100,
        description: "asdasdasd",
        pictureUrl: "asdasdasd",
      },
    ]);
  }

  return (
    <>
      <ul>
        {products.map((product) => (
          <li key={product.id}>
            {product.name} - {product.price}
          </li>
        ))}
      </ul>
      <button onClick={addProduct}>add</button>
    </>
  );
}

export default App;
```

## File & Folders Organizations

در این بخش میخواهیم با ساختار فایل ها و فولدر ها آشنا بشیم و اینکه چطوری آنها رو مرتب در کنار هم قرار بدهیم تا در پروژه های بزرگ و شلوغ به مشکل بر نخوریم!

یکی از روش های پیشنهادی برای مرتب کردن پوشه ها و فایل ها به صورت زیر میباشد:

```cmd
client/ (project root dir)
├───src/
│   ├───app/
│   │   ├───layouts/
│   │   │   ├───App.tsx
│   │   │   └───styles.css
│   │   └───models/
│   │       └───product.ts
│   └───features/
└───index.tsx
```

روش دیگر:

```cmd
src/
├── components/
├── pages/
├── features/
│   ├── feature1/
│   ├── feature2/
│   └── feature3/
├── services/
├── styles/
├── utils/
├── App.js
└── index.js
```
