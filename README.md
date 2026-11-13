# kaizen-sources

Datos que acompañan a la app Android TV [Kaizen](https://github.com/D4rumanDev/kaizen). Sirven para que quien empiece no tenga que preguntar de nuevo a cada web.

## Qué hay

| Fichero | Para qué |
|---|---|
| `animeav1.json`, `animeflv.json`, `animeflvar.json`, `animegratis.json`, `jkanime.json`, `monoschinos.json`, `verani.json` | Una fuente cada uno: cómo buscar, leer fichas y capítulos de la web (solo selectores y plantillas de URL; no hay secretos). Cada uno lleva su `version`: la app nunca baja de versión. |
| `index.json` | Índice de las fuentes con su versión y el `sha256` de cada fichero. |
| `index.json.sig` | Firma ECDSA P-256 (SHA-256) del `index.json`, en base64. La app solo usa lo que firma la clave pública de `SpecSources.PUBLIC_KEY`. La clave privada **no** está aquí. |
| `enlaces-anilist.json.gz` | Qué ficha de cada web es qué anime de AniList (ver abajo). |
| `enlaces-anilist.sig` | Firma ECDSA de ese fichero (misma clave que `index.json.sig`). La app solo lo usa si la firma vale. |
| `catalogo-unificado.json.gz` | Catálogo unificado (formato 2): cada serie con su nombre, año, nota, el `anilistId` de su anime en AniList (si está emparejada) y las fichas (fuente + slug) que la forman, y su fecha de generación (`createdAt`). Agrupado solo por AniList, sin uniones personales. Solo datos, sin ids locales. |
| `catalogo-unificado.sig` | Su firma (misma clave). La app lo comprueba (firma, reglas de unión, topes y cada campo) y solo aplica uno más nuevo que el último que aplicó. |

Las fuentes son las mismas que lleva la app en `app/src/main/assets/sources/`. Para cambiar una: subir su `version`, regenerar y firmar el índice con `tools/sign-sources.sh` del repositorio de la app y copiar aquí los ficheros.

## `enlaces-anilist.json.gz`

JSON comprimido con gzip:

```json
{ "version": 1, "matcherVersion": 3, "createdAt": "2026-11-05",
  "links": [ { "fuente": "animeav1", "slug": "009-1", "anilistId": 1583, "nivel": "seguro", "puntuacion": 1.03 } ] }
```

- `fuente` y `slug` identifican la ficha en la web (la URL sale de la plantilla `urls.anime` de su fuente); `anilistId` es el anime de AniList; `nivel` es cómo de segura es la decisión (`seguro`, `probable`, `mal` y `datos` por el `idMal` o los datos de la web, `mismo-id`).
- Solo van los enlaces **automáticos y aplicados** (23.039). No van los «por revisar», los «sin resultado» ni nada decidido a mano por el usuario.
- Es una foto: el emparejado sigue mejorando y un enlace automático puede estar mal. No sustituye a la revisión.
- `matcherVersion` es el criterio de emparejado con el que se hizo: la app solo lo usa si coincide con el suyo.
- En una instalación limpia (sin enlaces), la app los guarda al empezar y se ahorra buscar cada ficha en AniList (`AnilistSeed`, apagado hasta que este repositorio sea público).

## Estado

- La descarga de fuentes de la app está **apagada** (`SpecSources.DOWNLOAD_ENABLED = false`). Para usarla hay que hacer este repositorio público (la app lo lee por `raw.githubusercontent.com`) y poner la constante a `true`.
- Los nombres, portadas y datos de series pertenecen a sus webs y a AniList; este repositorio solo guarda el mapa entre ellos.
