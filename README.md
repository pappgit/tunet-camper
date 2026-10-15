# Tunet Camper – planlegging

Nettside for planlegging av Tunet Camper: https://pappgit.github.io/tunet-camper/

Siden ligger på GitHub Pages, og dataene lagres i Supabase-prosjektet **TunetScoreboard** (tabellene `camp_sections` og `camp_members`).

## Innlogging
Man skriver inn e-postadressen sin og får en sekssifret kode på e-post. Det finnes ingen passord. Bare e-postadresser som står i `camp_members` får tilsendt kode og kan se planen.

### Engangsoppsett i Supabase
Authentication → Emails (Email Templates). I malene **Magic Link** og **Confirm signup** bytter du innholdet til:

```html
<h2>Kode til Tunet Camper</h2>
<p>Koden din er: <strong style="font-size:24px;letter-spacing:4px">{{ .Token }}</strong></p>
<p>Skriv den inn på innloggingssiden. Koden er gyldig en kort stund.</p>
```

Emnelinje, for eksempel: `Kode til Tunet Camper`

## Legge til en ny person
Kjør i SQL Editor (e-post med små bokstaver):
```sql
insert into public.camp_members(email, navn) values ('frida@eksempel.no', 'Frida');
```
Personen kan logge inn med en gang. Kontoen opprettes første gang hen ber om kode.

## Fjerne tilgang
```sql
delete from public.camp_members where email = 'frida@eksempel.no';
```

## Teknisk
- `index.html` er hele appen (ingen byggesteg). Den bruker supabase-js fra jsDelivr.
- Nøkkelen i `index.html` er den offentlige anon-nøkkelen. Den er laget for å være synlig. Tilgangsreglene (RLS) i databasen sørger for at bare medlemmer får lese og skrive.
- Hver del av planen lagres som én rad i `camp_sections`, med versjonsnummer. Lagrer to personer samme del samtidig, får den siste beskjed og kan velge å overskrive eller hente den andres versjon.
- Supabase sin innebygde e-posttjeneste har en lav grense for hvor mange e-poster som kan sendes i timen. Innloggingen huskes på enheten, så det trengs sjelden nye koder. Blir det et problem, kan man koble til egen SMTP under Authentication → Emails.
