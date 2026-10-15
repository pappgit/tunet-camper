# Tunet Camper – planlegging

Nettside for planlegging av Tunet Camper: https://pappgit.github.io/tunet-camper/

Siden ligger på GitHub Pages, og dataene lagres i Supabase-prosjektet **TunetScoreboard** (tabellene `camp_sections` og `camp_members`).

## Innlogging
Man logger inn med e-post og passord. Bare e-postadresser som står i `camp_members` får se planen.

## Legge til en ny person
1. Supabase → TunetScoreboard → Authentication → Users → **Add user → Create new user**. Skriv inn e-post og et midlertidig passord, og huk av for **Auto Confirm User**.
2. Legg personen i medlemslisten (Table Editor → `camp_members` → Insert row, eller SQL Editor):
   ```sql
   insert into public.camp_members(email, navn) values ('frida@eksempel.no', 'Frida');
   ```
   E-posten skrives med små bokstaver.
3. Gi personen det midlertidige passordet. Hen bytter det selv via navneknappen øverst til høyre → «Bytt passord».

## Glemt passord
«Glemt passord?» på innloggingssiden sender en e-post med lenke. Supabase sin innebygde e-posttjeneste sender bare noen få e-poster i timen. Går det ikke, kan du sette et nytt passord for personen under Authentication → Users → (personen) → Reset password / Update password.

## Fjerne tilgang
```sql
delete from public.camp_members where email = 'frida@eksempel.no';
```

## Teknisk
- `index.html` er hele appen (ingen byggesteg). Den bruker supabase-js fra jsDelivr.
- Nøkkelen i `index.html` er den offentlige anon-nøkkelen. Den er laget for å være synlig. Tilgangsreglene (RLS) i databasen sørger for at bare medlemmer får lese og skrive.
- Hver del av planen lagres som én rad i `camp_sections`, med versjonsnummer. Lagrer to personer samme del samtidig, får den siste beskjed og kan velge å overskrive eller hente den andres versjon.
