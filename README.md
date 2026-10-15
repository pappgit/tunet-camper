# Tunet Camper – planlegging

Nettside for planlegging av Tunet Camper. Siden ligger på GitHub Pages, og dataene lagres i Supabase-prosjektet **TunetScoreboard** (tabellene `camp_sections` og `camp_members`).

## Tilgang
Bare e-postadresser som står i tabellen `camp_members` kan se og endre planen. Andre som logger inn, får beskjeden «Ingen tilgang».

### Legge til en ny person
1. Supabase → TunetScoreboard → Authentication → Users → **Add user → Create new user**. Skriv inn e-post og et midlertidig passord, og huk av for **Auto Confirm User**.
2. Legg e-posten inn i medlemslisten (SQL Editor):
   ```sql
   insert into public.camp_members(email, navn) values ('frida@eksempel.no', 'Frida');
   ```
   E-posten må skrives med små bokstaver.
3. Personen logger inn og bytter passord via navneknappen øverst til høyre.

### Fjerne tilgang
```sql
delete from public.camp_members where email = 'frida@eksempel.no';
```

### «Glemt passord»
Legg inn adressen til siden under Authentication → URL Configuration → **Redirect URLs**, for eksempel `https://BRUKERNAVN.github.io/tunet-camper/`.

## Teknisk
- `index.html` er hele appen (ingen byggesteg). Den bruker supabase-js fra jsDelivr.
- Nøkkelen i `index.html` er den offentlige anon-nøkkelen. Den er laget for å være synlig. Tilgangsreglene (RLS) i databasen sørger for at bare medlemmer får lese og skrive.
- Hver del av planen lagres som én rad i `camp_sections`, med versjonsnummer. Lagrer to personer samme del samtidig, får den siste beskjed og kan velge å overskrive eller hente den andres versjon.
