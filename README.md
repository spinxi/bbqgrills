# BBQ Grills Georgia — Official Online Store

საქართველოში ნახშირის გრილების, მაყალისა და BBQ აქსესუარების თანამედროვე ონლაინ მაღაზია.

## 🚀 ტექნოლოგიური სტეკი
- **Framework:** Vue 3 (Composition API `<script setup>`)
- **Bundler & Dev Server:** Vite 8
- **Styling:** Tailwind CSS 4
- **Icons:** Lucide Icons (`lucide-vue-next`)
- **Fonts:** FiraGO, Noto Sans Georgian, Inter, Outfit

## 📦 პროექტის გაშვება ლოკალურად

```bash
# 1. დამოკიდებულებების დაყენება
npm install

# 2. ლოკალური სერვერის გაშვება (Hot Module Replacement)
npm run dev
# გახსენით ბრაუზერში: http://localhost:5173/

# 3. Production ბილდის აწყობა
npm run build

# 4. Production ბილდის ლოკალური Preview
npm run preview
```

## 🛒 მაღაზიის ფუნქციონალი
- **Online-First E-Commerce:** სრული შეკვეთის სისტემა საიტიდან (Google Forms-ისა და WhatsApp-ის გარეშე).
- **Checkout Form:**
  - სახელი, გვარი
  - ტელეფონის ნომერი
  - პირადი / კომპანიის საიდენტიფიკაციო ნომერი
  - დეტალური მისამართი & ლოკაციის ლინკი
  - ტრანსპორტირების ტარიფები: თვითგატანა (0 ₾), თბილისი (20 ₾, 3 დღე), რეგიონები (35 ₾, 5 დღე)
  - გადახდა: კურიერთან მიღებისას ან TBC გადარიცხვა (`GE81TB7176236080100011`)
- **Black Friday Spotlight:** #5 Model XL Commercial Grill (72x42 სმ, 15 პერსონა, 24 კგ).
- **Multilingual Support:** ქართული (KA), ინგლისური (EN), რუსული (RU).
- **Live Search Modal:** მყისიერი ძიება დასახელებით, ზომებით, მოდელის ნომრებითა და კატეგორიებით (`⌘K`).

## 🌐 უფასო ჰოსტინგზე განთავსება (Domain-Free Deployment)
რადგან მაღაზიას ჯერ არ აქვს საკუთარი დომენი, შეგიძლიათ 1 წუთში ატვირთოთ უფასო ჰოსტინგზე:

### Vercel
1. დააინსტალირეთ Vercel CLI ან შეაერთეთ GitHub რეპოზიტორია:
```bash
npx vercel
```
2. მიიღებთ მზა, უფასო, სწრაფ ბმულს: `https://bbq-grills-georgia.vercel.app`
3. როცა მომავალში შეიძენთ დომენს (მაგ: `bbqgrills.ge`), 2 წუთში მიაბამთ Vercel-ის სამართავ პანელში.

### Netlify
```bash
npx netlify deploy --prod --dir=dist
```
