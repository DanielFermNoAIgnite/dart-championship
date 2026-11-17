# NoA Ignite Dart Championship – GitHub Pages

Det här är hela sajten som vanliga filer. Alla med länken ser brackets live utan konto. Bara admins kan ändra.

**Filer**

| Fil | Vad den gör |
| --- | --- |
| `index.html` | Hela sidan (HTML, CSS och JavaScript). |
| `config.js` | Dina två Supabase-värden. Det enda du behöver fylla i. |
| `supabase-setup.sql` | Skapar databasen, säkerheten och turneringen 2026. |

**Hur det hänger ihop:** GitHub Pages lägger ut filerna. Supabase (gratis) sparar turneringarna och sköter admin-inloggningen. Alla kan läsa, men bara användare i tabellen `admins` kan skriva. Det styrs i databasen, inte i sidan, så det går inte att kringgå genom att titta i koden.

---

## Steg 1. Skapa databasen i Supabase (ca 10 min)

1. Gå till supabase.com, skapa ett gratiskonto och välj **New project**. Välj region **North EU (Stockholm)** om den finns, annars en annan EU-region. Spara databaslösenordet, du behöver det inte i sidan men Supabase kräver det.
2. Öppna **SQL Editor → New query**. Klistra in hela innehållet i `supabase-setup.sql` och tryck **Run**. Det ska säga "Success". Nu finns tabellerna, säkerhetsreglerna och 2026-turneringen.
3. Öppna **Authentication → Users → Add user → Create new user**. Skriv din e-post och ett långt lösenord, och bocka i **Auto Confirm User**.
4. Gör användaren till admin. Öppna **SQL Editor** igen och kör (byt ut e-posten):

   ```sql
   insert into public.admins (user_id)
   select id from auth.users where email = 'DIN-EPOST@noaignite.com';
   ```

   Vill du ha fler admins lägger du först till dem som användare (steg 3) och kör sedan samma rad med deras e-post.
5. Valfritt men rekommenderat: **Authentication → Sign In / Providers → Email** och stäng av **Allow new users to sign up**. Då kan ingen annan skapa konton. De skulle ändå inte kunna ändra något utan rad i `admins`.

## Steg 2. Koppla sidan till databasen (2 min)

1. I Supabase: **Project Settings → API** (eller **API Keys**). Kopiera **Project URL** och nyckeln som heter **anon public**. Visar din vy bara en "publishable key", använd den.
2. Öppna `config.js` och ersätt de två värdena.

   Anon-nyckeln är gjord för att ligga publikt i sidan. Skyddet ligger i säkerhetsreglerna från steg 1. Lägg aldrig `service_role`-nyckeln i `config.js`.

## Steg 3. Lägg ut på GitHub Pages (5 min)

1. På github.com: **New repository**. Döp det till t.ex. `dart-championship`. Välj **Public** (se not om integritet nedan).
2. Klicka **uploading an existing file** och dra in `index.html` och `config.js`. Ladda upp `supabase-setup.sql` och `README.md` också om du vill spara dem, men de behövs inte för sajten. Tryck **Commit changes**.
3. Gå till **Settings → Pages**. Under **Build and deployment**, välj **Deploy from a branch**, branch `main` och mapp `/ (root)`. Spara.
4. Efter ungefär en minut visas adressen högst upp på samma sida, ungefär `https://ditt-användarnamn.github.io/dart-championship/`. Det är din live-länk.

## Steg 4. Testa

1. Öppna länken. Du ska se brackets direkt, utan gul notis.
2. Klicka **Admin** i toppen och logga in med e-post och lösenord från steg 1. Knappen blir rosa ("Editing").
3. Klicka på en match, välj en vinnare och spara. Öppna länken i ett annat fönster utan inloggning: ändringen ska synas där inom ett par sekunder.

---

## Vanliga problem

- **Gul notis: "not connected to its database yet"**: `config.js` har fortfarande platshållarna, eller så har GitHub inte hunnit publicera din ändring. Vänta en minut och ladda om med Cmd+Shift+R.
- **Gul notis: "could not be loaded"**: öppna **Technical details** i notisen. Är det "Invalid API key" har du kopierat fel nyckel. Är det "relation does not exist" har SQL-filen inte körts klart.
- **"Could not sign in"**: kontrollera e-post och lösenord, och att användaren är *confirmed* i Supabase.
- **Du är inloggad men får "not an admin"**: raden i `admins` saknas. Kör SQL-raden från steg 1.4 med rätt e-post.
- **"Could not save: your account is not an admin"**: samma sak, eller så har inloggningen gått ut. Logga in igen.
- **Gratis Supabase-projekt pausas efter en veckas inaktivitet.** Logga in i Supabase och klicka **Restore** om sidan slutar ladda.

## Integritet

En GitHub Pages-sida är öppen för alla som har länken, och spelarnas namn ligger i databasen som alla kan läsa. Det är avsiktligt, så att kollegor kan se brackets utan konto. Är det olämpligt att namnen är publika, använd förnamn eller initialer, eller lägg sidan bakom företagets inloggning i stället (t.ex. Salesforce Experience Cloud eller en intern SharePoint).

## Lägga till nästa års turnering

Logga in som admin, klicka **New tournament**, välj år och antal spelare (4–64) och klistra in namnen. Fliken dyker upp bredvid 2026. Namn, vinnare och resultat sparas i Supabase.

## Ändra spelare manuellt

Logga in som admin och klicka **Edit players**. Där ser du alla platser i brackets med ett namnfält per spelare. Skriv eller ändra namn direkt, eller klistra in en lista (ett namn per rad) och klicka **Fill slots from list**. Du kan också ändra **Number of players** för att lägga till eller ta bort spelare. Brackets byggs då om, namnen läggs in i ordning och alla resultat nollställs. Det snabbaste för ett enskilt namn är att klicka direkt på namnet i brackets, så öppnas redigeringsfältet.

## Highscore (tre pilar)

Sektionen **Highscore** visar bästa resultat med tre pilar (0–180) och vem som håller det, helt skilt från brackets. Logga in som admin och klicka **Edit highscore** på kortet, eller öppna **Tournament settings** och ändra fälten **Record holder** och **Score**. Lämna båda tomma för att ta bort rekordet. Varje år (flik) har sin egen highscore. För 2026 visas Tobias Håkansson med 138 p tills du ändrar det.

## Dashboard (TV / platta / mobil i landskap)

I mappen `dashboard/` finns en andra sida som är gjord för att visas på en TV, en platta eller en mobil som hålls på tvären. Adressen blir:

`https://DITT-ANVÄNDARNAMN.github.io/dart-championship/dashboard/`

Den roterar automatiskt mellan tre vyer: **full bracket** (25 s), **nästa matcher** (14 s) och **highscore + mästare** (12 s). "Nästa matcher" visar de matcher där båda spelarna redan är klara, och hoppas över när turneringen är avgjord. Sidan är bara för visning, kräver ingen inloggning och uppdateras live från Supabase (kontrolleras även var 30:e sekund). Den använder samma `config.js` som huvudsidan, så ingen extra inställning behövs.

Styrning: tryck på **höger/vänster del** av skärmen (eller piltangenterna) för att byta vy, **mitten** eller mellanslag pausar, och knapparna uppe till höger pausar och går till helskärm (tangent F). Skärmen hålls vaken där webbläsaren tillåter det. Med flera turneringar byter du år genom att trycka på årtalet, eller lägg till `#2027` sist i adressen. Visas "Offline copy" i stället för "Live" når sidan inte databasen och visar den inbyggda 2026-kopian.

Tips för TV: öppna adressen i webbläsaren, tryck F för helskärm och låt den stå.
