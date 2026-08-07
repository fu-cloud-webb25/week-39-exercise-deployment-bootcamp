# Deployment Bootcamp

I dessa övningar ska du använda **Amazon S3** för att driftsätta statiska webbplatser.

Vi börjar med en enkel HTML-fil och bygger sedan vidare till en React-applikation och slutligen en frontend som kommunicerar med ett API.

Målet är att förstå hur S3 kan användas för att hosta statiskt innehåll.

---

# Övning 1 – Driftsätt en enkel HTML-sida

## Steg 1 – Skapa en HTML-fil

Skapa en fil:

```text
index.html
```

Lägg in exempelvis:

```html
<!DOCTYPE html>
<html lang="sv">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Min första S3-sida</title>
</head>
<body>
  <h1>Hello S3!</h1>
  <p>Den här sidan är hostad på AWS S3.</p>
</body>
</html>
```

---

## Steg 2 – Skapa en S3-bucket

Logga in på AWS och gå till:

```text
S3
```

Skapa en ny bucket.

Namnet på en S3-bucket måste vara **globalt unikt**.

Ett exempel skulle kunna vara:

```text
anna-s3-bootcamp-2026
```

eller:

```text
my-super-awesome-static-site-12345
```

---

## Steg 3 – Ladda upp HTML-filen

Öppna din bucket och ladda upp:

```text
index.html
```

---

## Steg 4 – Aktivera Static Website Hosting

Öppna bucketens inställningar och gå till:

```text
Properties
```

Leta upp:

```text
Static website hosting
```

Aktivera funktionen.

Ställ in:

```text
Index document: index.html
```

Spara ändringarna.

---

## Steg 5 – Tillåt publik åtkomst

En webbplats behöver kunna läsa filerna i bucketen.

Du behöver därför konfigurera bucketen så att innehållet kan läsas publikt.

Kontrollera först inställningen:

```text
Block Public Access
```

och konfigurera den så att bucket policy kan ge publik läsåtkomst.

> Var försiktig när du gör resurser publika. I denna övning innehåller bucketen endast filer som är avsedda att vara publika.

---

## Steg 6 – Lägg till en Bucket Policy

Öppna:

```text
Permissions
```

och lägg till följande Bucket Policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::<DIN-BUCKET>/*"
    }
  ]
}
```

Ersätt:

```text
<DIN-BUCKET>
```

med namnet på din bucket.

Exempel:

```json
"Resource": "arn:aws:s3:::anna-s3-bootcamp-2026/*"
```

---

## Steg 7 – Öppna webbplatsen

Gå tillbaka till:

```text
Properties
```

och leta upp:

```text
Bucket website endpoint
```

Öppna länken.

Du bör nu kunna se din HTML-sida live i webbläsaren.

Du har nu fått följande flöde att fungera:

```text
index.html
    ↓
S3 Bucket
    ↓
Static Website Hosting
    ↓
Publik webbplats
```

---

# Övning 2 – Driftsätt en React-applikation

Nu ska du göra samma sak med en React-applikation.

## Steg 1 – Välj ett React-projekt

Välj ett React-projekt som du byggt tidigare.

Kontrollera först att projektet fungerar lokalt:

```bash
npm run dev
```

---

## Steg 2 – Skapa en produktionsbuild

Kör:

```bash
npm run build
```

Om du använder Vite kommer det normalt skapa mappen:

```text
dist/
```

Den innehåller den version av applikationen som ska driftsättas.

---

## Steg 3 – Skapa en ny S3-bucket

Skapa en ny bucket för React-applikationen.

Aktivera:

```text
Static website hosting
```

och använd:

```text
Index document: index.html
```

Om din applikation använder client-side routing kan du även använda:

```text
Error document: index.html
```

---

## Steg 4 – Ladda upp projektet

Ladda upp **innehållet** från:

```text
dist/
```

till din S3-bucket.

Det är alltså filerna inuti `dist` som ska ligga i bucketens root.

Exempel:

```text
S3 Bucket
│
├── index.html
├── assets/
│   ├── index.js
│   └── index.css
└── ...
```

Inte:

```text
S3 Bucket
└── dist/
    └── index.html
```

---

## Steg 5 – Konfigurera permissions

Precis som i första övningen behöver bucketen tillåta publik läsåtkomst.

Lägg till en Bucket Policy som tillåter:

```text
s3:GetObject
```

för filerna i bucketen.

---

## Steg 6 – Öppna applikationen

Öppna bucketens:

```text
Website endpoint
```

och kontrollera att React-applikationen fungerar.

---

# React Router och direktlänkar

Om din React-applikation använder exempelvis React Router kan du ha routes som:

```text
/
/movies
/profile
/about
```

När du navigerar mellan sidorna inne i React fungerar detta vanligtvis bra.

Men om du exempelvis öppnar:

```text
/movies
```

direkt i webbläsaren eller uppdaterar sidan kan S3 försöka hitta en faktisk fil på den sökvägen.

Därför kan du i denna övning använda:

```text
Error document: index.html
```

så att React får möjlighet att hantera routingen.

---

# Övning 3 – Driftsätt en frontend till ditt API

Nu ska du koppla ihop din frontend med ett API som du tidigare byggt.

Du kan exempelvis använda:

* Todo API
* Notes API
* Pokémon API
* annat API från tidigare övningar

Målet är att få ett komplett flöde:

```text
React
   ↓
S3
   ↓
API Gateway
   ↓
Lambda
   ↓
DynamoDB
```

---

## Steg 1 – Skapa frontend

Bygg en enkel React-frontend som kommunicerar med ditt API.

Om du använder ett Todo API bör applikationen minst kunna:

```text
GET     Hämta todos
POST    Skapa todo
PUT     Uppdatera todo
DELETE  Ta bort todo
```

Det viktiga i denna övning är **inte designen**.

En enkel fungerande frontend räcker.

---

## Steg 2 – Testa lokalt

Innan du deployar frontend ska du kontrollera att den fungerar lokalt:

```bash
npm run dev
```

Kontrollera att den kan kommunicera med ditt API.

---

## Steg 3 – Bygg applikationen

När allt fungerar:

```bash
npm run build
```

Ladda sedan upp innehållet från:

```text
dist/
```

till en S3-bucket.

---

## Steg 4 – Testa den deployade versionen

Öppna frontendens S3 Website Endpoint.

Testa sedan att:

1. hämta data
2. skapa data
3. uppdatera data
4. ta bort data

---

# CORS

Det är möjligt att ditt API fungerar perfekt i exempelvis Insomnia men inte från din React-applikation.

Om detta händer:

1. öppna Developer Tools i webbläsaren
2. gå till Console eller Network
3. kontrollera om du får ett CORS-fel

Exempel:

```text
Blocked by CORS policy
```

Detta händer eftersom webbläsaren har säkerhetsregler kring vilka domäner som får kommunicera med varandra.

Din frontend och ditt API ligger nu på olika adresser:

```text
S3 Website
      ↓
https://my-site...

API Gateway
      ↓
https://abc123.execute-api...
```

API Gateway behöver därför tillåta requests från frontendens origin.

Konfigurera CORS i ditt API så att frontend-applikationen kan kommunicera med det.

---

# Kontrollera hela flödet

När du är klar ska följande fungera:

```text
Användare
   │
   ▼
React Frontend
   │
   ▼
S3 Static Website
   │
   ▼
API Gateway
   │
   ▼
Lambda
   │
   ▼
DynamoDB
```

Du har nu deployat både:

```text
Frontend
→ S3
```

och:

```text
Backend
→ API Gateway + Lambda + DynamoDB
```

---

# Level Up – Skapa bucketen med Serverless Framework

Hittills har du skapat S3-buckets manuellt i AWS Console.

Som extra utmaning kan du istället låta Serverless Framework skapa en bucket.

Lägg till en S3-resource i:

```text
serverless.yml
```

Exempel:

```yaml
resources:
  Resources:
    WebsiteBucket:
      Type: AWS::S3::Bucket
      Properties:
        BucketName: <unikt-bucket-namn>
```

Deploya:

```bash
serverless deploy
```

Kontrollera sedan i AWS Console att bucketen skapades.

Fundera på skillnaden mellan:

```text
AWS Console
→ skapa resurser manuellt
```

och:

```text
serverless.yml
→ beskriva infrastrukturen
→ serverless deploy
→ AWS skapar resurserna
```

Det senare är ett exempel på:

> **Infrastructure as Code**
