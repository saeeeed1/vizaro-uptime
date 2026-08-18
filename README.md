# vizaro-uptime

**v1zaro.com** saytini muntazam tekshiradi. Jadval har **5 daqiqaga** qo'yilgan,
lekin GitHub uni "eng yaxshi harakat" asosida yuritadi — amalda o'lchangan
mediana **26 daqiqa** («Bilib qo'yish kerak» bo'limiga qarang). Sayt o'chsa —
Telegram'ga xabar yuboradi, qaytganda yana bir marta. Boshqa hech narsa qilmaydi.

GitHub Actions **public repo'da butunlay bepul** — soatlik cheklov yo'q.

---

## 1-qadam. Telegram bot tokenini olish

Agar sizda tayyor bot bo'lsa — bu qadamni o'tkazib yuboring.

1. Telegram'da **@BotFather** ga yozing.
2. `/newbot` — bot nomi va foydalanuvchi nomini kiriting.
3. BotFather sizga token beradi, u shunday ko'rinishda:
   `1234567890:AAxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`

⚠️ **Bu tokenni hech kimga bermang va hech qayerga yozib qo'ymang.** U faqat
GitHub Secrets ichida turishi kerak.

## 2-qadam. Chat ID ni olish

Xabar qayerga kelishini belgilaydi.

1. Botingizga Telegram'da **bir marta** `/start` deb yozing (bu majburiy — bot
   o'zi yozgan odamga javob bera oladi, boshqasiga yo'q).
2. Brauzerda quyidagi manzilni oching (`<TOKEN>` o'rniga o'z tokeningizni qo'ying):

   `https://api.telegram.org/bot<TOKEN>/getUpdates`

3. Javobda `"chat":{"id":123456789` degan joyni toping. **123456789** — sizning
   Chat ID'ingiz.

Agar xabar guruhga kelishini xohlasangiz: botni guruhga qo'shing, guruhda bir
marta yozing va yuqoridagi manzildan guruh ID'sini oling (u **minus** bilan
boshlanadi, masalan `-1001234567890`).

## 3-qadam. GitHub'ga sirlarni qo'shish

1. Bu repo sahifasini oching → yuqoridagi **Settings**.
2. Chap menyuda: **Secrets and variables** → **Actions**.
3. Yashil **New repository secret** tugmasi.
4. Ikki marta takrorlang:

   | Name (aynan shunday yozing) | Secret (qiymat) |
   |---|---|
   | `TELEGRAM_BOT_TOKEN` | 1-qadamdagi token |
   | `TELEGRAM_CHAT_ID` | 2-qadamdagi raqam |

5. **Add secret** bosing.

✅ Shundan keyin monitoring o'zi ishlay boshlaydi — boshqa hech narsa qilish
kerak emas.

⚠️ Sirlarni **Variables** bo'limiga emas, **Secrets** bo'limiga qo'ying.
Variables ochiq ko'rinadi, Secrets esa yashiringan.

## 4-qadam. Issues yoqilganini tekshirish

**Settings** → pastroqda **Features** → **Issues** katagi belgilangan bo'lsin.
Monitoring "sayt hozir o'chiqmi yoki yo'qmi" ni shu yerda eslab qoladi.

---

## Qanday sinash mumkin

### A) Telegram ishlayaptimi (eng oson, xavfsiz)

1. Yuqoridagi **Actions** bo'limi → chapdan **Uptime**.
2. O'ngda **Run workflow** tugmasi.
3. **"Faqat Telegram'ni sinash"** katagini belgilang → **Run workflow**.

Telegram'ga «✅ Sinov xabari» kelishi kerak. Sayt bu rejimda **tekshirilmaydi**
va hech narsa o'zgarmaydi.

### B) Oddiy tekshiruvni qo'lda yugurtirish

Xuddi shunday, lekin katakni **belgilamang**. Sayt ishlayotgan bo'lsa hech qanday
xabar kelmaydi (bu normal — monitoring faqat muammo bo'lganda yozadi).

### C) Haqiqiy uzilishni sinash

Buni sayt bilan sinab ko'rish shart emas. Ishonch hosil qilish uchun:
`uptime.yml` faylidagi `SITE_URL` ni vaqtincha mavjud bo'lmagan manzilga
o'zgartiring (masalan `https://v1zaro.com/yoq-sahifa-test/`), commit qiling,
xabar kelganini ko'ring, keyin qaytaring. Xabar kelgach — Issues bo'limida
qizil issue paydo bo'lganini ham ko'rasiz.

---

## Nima aniq tekshiriladi

Har ishga tushganda **ikki narsa**:

1. **Bosh sahifa** — HTTP 200 qaytarishi **yetarli emas**. Sahifa ichida login
   formasi borligi ham tekshiriladi. Sabab: nginx xato sahifasi, bo'sh javob yoki
   "texnik ishlar" sahifasi ham 200 qaytarishi mumkin.
2. **`/api/bootstrap`** — ilova qatlami. Bu tekshiruvsiz shunday holat sezilmay
   qoladi: nginx tirik, sayt ochiladi, lekin Python xizmati o'lgan va hech narsa
   ishlamaydi. Javob haqiqiy JSON ekani va `serverTime` borligi tekshiriladi.

Ikkalasidan biri yiqilsa — sayt "o'chiq" deb hisoblanadi.

## Yolg'on trevoga bo'lmasligi uchun

Bir marta javob bermaslik — uzilish emas. Shuning uchun har tekshiruvda
**3 marta urinadi**, oralarida **20 soniya**. Xabar faqat uchalasi ham
muvaffaqiyatsiz bo'lganda ketadi (jami ~1 daqiqa).

Bu deploy paytidagi qisqa restart, tarmoqdagi bir lahzalik uzilish va
provayder blipini filtrlaydi.

## Takroriy xabar kelmasligi

Sayt 2 soat o'chiq tursa ham **24 ta emas, 2 ta** xabar keladi:

- 🔴 o'chganda — bir marta
- ✅ qaytganda — bir marta

Buning uchun holat **GitHub Issue** sifatida saqlanadi: ochiq issue = "hozir
o'chiq". Keyingi tekshiruvlar ochiq issue'ni ko'rib, jim turadi. Sayt qaytganda
issue avtomatik yopiladi. Yon foyda: **Issues bo'limida uzilishlar tarixi** o'zi
yig'ilib boradi — qachon o'chgan, qancha turgan.

---

## Bilib qo'yish kerak

- **Cron aniq 5 daqiqa emas — amalda ancha siyrak.** GitHub jadval bo'yicha ishga
  tushirishni "eng yaxshi harakat" asosida bajaradi. `uptime.yml` da e'lon
  qilingani — har **5 daqiqa**; 2026-08-18 da o'lchangani (136 ta jadvalli run,
  15–18 avgust): **mediana 26 daqiqa**, o'rtacha 30, eng uzun tanaffus **109
  daqiqa**, 1 soatdan uzun tanaffus 6 marta. Ya'ni uzilish odatda yarim soatda,
  yomon holatda ~1,5 soatda seziladi. Buni sozlab bo'lmaydi — bu GitHub'ning
  bepul jadvali; haqiqatan 5 daqiqa kerak bo'lsa, tashqi xizmat kerak.
- **Sayt o'chganda GitHub xat yubormaydi** — bu ataylab. Aks holda har 5
  daqiqada bitta xat kelib, pochtani to'ldirib yuborardi. Xabar kanali —
  Telegram. GitHub xati faqat **monitoringning o'zi buzilganda** keladi (bu esa
  aynan bilish kerak bo'lgan narsa).
- **Issue'lar ochiq (public) ko'rinadi.** Repo public bo'lgani uchun "sayt
  o'chgan edi" ma'lumoti hammaga ko'rinadi. Sayt o'chgani baribir tashqaridan
  bilinadi, shuning uchun bu yangi ma'lumot oshkor qilmaydi. Bezovta qilsa —
  repo'ni private qilish mumkin, lekin unda Actions bepul emas (oyiga 2000
  daqiqa; bu esa har 5 daqiqalik tekshiruvga yetmaydi).
- **60 kun qoidasi.** GitHub public repo'da 60 kun commit bo'lmasa cron'ni
  o'chirib qo'yadi. Shuning uchun `keepalive.yml` oyiga bir marta bo'sh commit
  qiladi. Uni o'chirsangiz — GitHub'ning ogohlantirish xatini o'tkazib
  yubormang.

## Sirlar xavfsizligi

- Kodda hech qanday token yo'q — faqat `${{ secrets.* }}` havolasi.
- Token log'ga chiqmaydi: hech qayerda `echo` qilinmaydi va `set -x` yoqilmagan.
  Bundan tashqari GitHub sir qiymatlarini log'da avtomatik `***` bilan yopadi.
- Xato bo'lganda Telegram javobi ko'rsatiladi — u javobda token **yo'q**, faqat
  xato tavsifi bor.
- Sayt manzili ochiq turishi normal — sayt baribir public.
