# Aplicació web de torneig de futbol

Aquesta és una primera versió independent de l'aplicació, preparada per publicar-se a Vercel i utilitzar Supabase com a base de dades.

## Què inclou

- Consulta pública de classificacions, calendari, resultats i estadístiques.
- Accés privat per a diverses persones administradores.
- Les persones administradores poden crear/substituir equips i grups i introduir o modificar resultats.
- Calendari de tots contra tots dins de cada grup.
- Classificació automàtica: 3 punts per victòria, 1 per empat, 0 per derrota.
- Actualització en temps real per a les persones que tenen el torneig obert.

## 1. Crear el projecte Supabase

1. Ves a https://supabase.com/ i crea un projecte.
2. Obre **SQL Editor** i executa tot el contingut de `supabase/schema.sql`.
3. Ves a **Project Settings → API** (o Connect) i copia:
   - Project URL
   - anon / publishable key
4. Obre `index.html` i substitueix:
   - `YOUR_SUPABASE_URL`
   - `YOUR_SUPABASE_ANON_KEY`
   pels valors del teu projecte. La clau anon/publishable està pensada per ser pública; la seguretat real la controlen les polítiques RLS de la base de dades. **No posis mai la service_role key al codi del navegador.**

## 2. Crear les persones administradores

Per a cada persona administradora:

1. A Supabase, ves a **Authentication → Users** i convida el seu correu electrònic (o crea l'usuari).
2. Quan l'usuari aparegui a la llista, copia el seu UUID.
3. A **SQL Editor**, executa aquesta ordre substituint l'UUID:

```sql
insert into public.tournament_admins (user_id)
values ('UUID_DE_L_USUARI');
```

Repeteix-ho per a totes les persones administradores. Només aquests comptes podran editar equips i resultats. La resta de visitants no necessiten compte.

Recomanació: desactiva el registre públic d'usuaris a Authentication → Settings, si no vols que qualsevol pugui crear-se un compte.

## 3. Publicar a Vercel

1. Crea un compte a https://github.com/ i un repositori nou.
2. Puja `index.html` a l'arrel del repositori. Pots pujar també la carpeta `supabase` per conservar l'esquema.
3. Crea un compte a https://vercel.com/ i tria **Add New → Project**.
4. Importa el repositori de GitHub.
5. Com que és una pàgina estàtica, no cal cap framework ni ordre de build. Deixa Build Command buit i publica.
6. Vercel et donarà una URL pública `*.vercel.app`. Comparteix-la amb tothom.

Cada canvi que facis al repositori de GitHub es publicarà automàticament a Vercel.

## 4. Ús

- El públic entra a l'enllaç i consulta les pestanyes Classificació, Partits i resultats i Estadístiques.
- Les persones administradores premen **Accés organització** i inicien sessió amb el compte convidat.
- A **Gestionar equips** poden establir el nom del torneig, afegir equips, decidir el nombre de grups i generar els enfrontaments.
- A **Partits i resultats** poden introduir o canviar els gols. Els canvis es desen al núvol i es propaguen a les pantalles obertes.

## Notes i limitacions d'aquesta primera versió

- És un torneig principal amb una fase de grups; no inclou encara fase eliminatòria.
- Els equips es reparteixen alternadament entre grups (no hi ha sorteig ni caps de sèrie).
- El calendari genera jornades de tots contra tots per grup. Els partits dins de cada jornada no inclouen encara assignació de camps, hores ni descansos.
- La classificació es desempata per punts, diferència de gols i gols a favor.
- Per seguretat, les operacions d'escriptura estan protegides per Row Level Security i la taula d'administradors no és accessible des del navegador.
- Abans d'un torneig real, prova el flux amb dades fictícies i comprova els permisos amb un compte públic i un d'administrador.
