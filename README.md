# 🎬 Knowledge Graph de Filmes e Séries (Neo4j)

Este projeto é um estudo prático de modelagem e criação de um **grafo de conhecimento (Knowledge Graph)** utilizando **Neo4j**.  
O objetivo é representar relações entre **usuários**, **filmes**, **séries**, **atores**, **diretores** e **gêneros**, permitindo análises e consultas mais ricas.

---

## 🧠 Objetivo

Construir um grafo que contenha:

### **📌 Entidades (Nodes)**
- `User`
- `Movie`
- `Series`
- `Actor`
- `Director`
- `Genre`

### **🔗 Relacionamentos**
- `WATCHED` (User → Movie/Series, com propriedade `rating`)
- `ACTED_IN` (Actor → Movie/Series)
- `DIRECTED` (Director → Movie/Series)
- `IN_GENRE` (Movie/Series → Genre)

---

## 🧩 Modelo Conceitual (Diagrama)

Representação das relações do grafo:

(User) -- WATCHED {rating} --> (Movie)
(User) -- WATCHED {rating} --> (Series)

(Actor) -- ACTED_IN --> (Movie)
(Actor) -- ACTED_IN --> (Series)

(Director) -- DIRECTED --> (Movie)
(Director) -- DIRECTED --> (Series)

(Movie) -- IN_GENRE --> (Genre)
(Series) -- IN_GENRE --> (Genre)

pgsql
Copiar código

---

## 🛠️ Tecnologias Utilizadas
- **CHAT-GPT**
- **Arrows.app** (modelagem visual)

---

# 🧾 Script Completo (Cypher)

> 📌 Este script cria constraints, popula 10 entidades de cada tipo e cria todos os relacionamentos necessários.

```cypher
////////////////////////////////////////////////////////////////////////
// 1. CONSTRAINTS
////////////////////////////////////////////////////////////////////////

CREATE CONSTRAINT IF NOT EXISTS
FOR (u:User) REQUIRE u.id IS UNIQUE;

CREATE CONSTRAINT IF NOT EXISTS
FOR (m:Movie) REQUIRE m.id IS UNIQUE;

CREATE CONSTRAINT IF NOT EXISTS
FOR (s:Series) REQUIRE s.id IS UNIQUE;

CREATE CONSTRAINT IF NOT EXISTS
FOR (a:Actor) REQUIRE a.id IS UNIQUE;

CREATE CONSTRAINT IF NOT EXISTS
FOR (d:Director) REQUIRE d.id IS UNIQUE;

CREATE CONSTRAINT IF NOT EXISTS
FOR (g:Genre) REQUIRE g.name IS UNIQUE;

////////////////////////////////////////////////////////////////////////
// 2. NODES
////////////////////////////////////////////////////////////////////////

///////////////////////
// 2.1 Genres (10)
///////////////////////

UNWIND [
  "Action","Drama","Comedy","Sci-Fi","Romance",
  "Fantasy","Thriller","Adventure","Mystery","Horror"
] AS gen
CREATE (:Genre {name: gen});

///////////////////////
// 2.2 Users (10)
///////////////////////

UNWIND [
  {id:"U1",  name:"Ana",      city:"São Paulo", age:25},
  {id:"U2",  name:"Bruno",    city:"Rio",       age:30},
  {id:"U3",  name:"Carla",    city:"Lisboa",    age:27},
  {id:"U4",  name:"Diego",    city:"Curitiba",  age:22},
  {id:"U5",  name:"Eva",      city:"Madrid",    age:29},
  {id:"U6",  name:"Felipe",   city:"Recife",    age:33},
  {id:"U7",  name:"Gabriela", city:"Porto",     age:28},
  {id:"U8",  name:"Hugo",     city:"Porto",     age:24},
  {id:"U9",  name:"Isabel",   city:"São Paulo", age:26},
  {id:"U10", name:"João",     city:"Brasília",  age:35}
] AS row
CREATE (:User {
  id: row.id,
  name: row.name,
  city: row.city,
  age: row.age
});

///////////////////////
// 2.3 Movies (10)
///////////////////////

UNWIND [
  {id:"M1",  title:"Edge of Tomorrow", year:2014},
  {id:"M2",  title:"Silent Echo",      year:2018},
  {id:"M3",  title:"Broken Paths",     year:2020},
  {id:"M4",  title:"Last Horizon",     year:2022},
  {id:"M5",  title:"Laugh Again",      year:2019},
  {id:"M6",  title:"Sky Memories",     year:2017},
  {id:"M7",  title:"Ghost Runner",     year:2021},
  {id:"M8",  title:"Burning Stars",    year:2016},
  {id:"M9",  title:"Red Skies",        year:2023},
  {id:"M10", title:"Nightfall",        year:2015}
] AS row
CREATE (:Movie {
  id: row.id,
  title: row.title,
  year: row.year
});

///////////////////////
// 2.4 Series (10)
///////////////////////

UNWIND [
  {id:"S1",  title:"Cyber Future",  year:2021},
  {id:"S2",  title:"Small Town",    year:2017},
  {id:"S3",  title:"Mystic Road",   year:2019},
  {id:"S4",  title:"Fallen Star",   year:2020},
  {id:"S5",  title:"Deep Shadows",  year:2022},
  {id:"S6",  title:"Iron Hearts",   year:2015},
  {id:"S7",  title:"Crystal Code",  year:2018},
  {id:"S8",  title:"Silver Path",   year:2016},
  {id:"S9",  title:"Kingdom Rise",  year:2023},
  {id:"S10", title:"Nova Terra",    year:2024}
] AS row
CREATE (:Series {
  id: row.id,
  title: row.title,
  year: row.year
});

///////////////////////
// 2.5 Actors (10)
///////////////////////

UNWIND [
  {id:"A1",  name:"Alice Stone"},
  {id:"A2",  name:"Brian Cole"},
  {id:"A3",  name:"Clara Novak"},
  {id:"A4",  name:"Daniel Reed"},
  {id:"A5",  name:"Emma Scott"},
  {id:"A6",  name:"Fernando Lima"},
  {id:"A7",  name:"Gina Turner"},
  {id:"A8",  name:"Henry Wall"},
  {id:"A9",  name:"Isla Voss"},
  {id:"A10", name:"Jake Miller"}
] AS row
CREATE (:Actor {
  id: row.id,
  name: row.name
});

///////////////////////
// 2.6 Directors (10)
///////////////////////

UNWIND [
  {id:"D1",  name:"Nina Torres"},
  {id:"D2",  name:"Oscar Martin"},
  {id:"D3",  name:"Paula Gomes"},
  {id:"D4",  name:"Rafael Costa"},
  {id:"D5",  name:"Sara Lopez"},
  {id:"D6",  name:"Tom Briggs"},
  {id:"D7",  name:"Ursula Ramos"},
  {id:"D8",  name:"Victor Mendes"},
  {id:"D9",  name:"Wendy Clark"},
  {id:"D10", name:"Yuri Sato"}
] AS row
CREATE (:Director {
  id: row.id,
  name: row.name
});

////////////////////////////////////////////////////////////////////////
// 3. RELATIONSHIPS
////////////////////////////////////////////////////////////////////////

///////////////////////
// 3.1 IN_GENRE (Movies)
///////////////////////

UNWIND [
  {id:"M1",  genres:["Action","Sci-Fi"]},
  {id:"M2",  genres:["Drama"]},
  {id:"M3",  genres:["Comedy"]},
  {id:"M4",  genres:["Thriller"]},
  {id:"M5",  genres:["Romance"]},
  {id:"M6",  genres:["Fantasy"]},
  {id:"M7",  genres:["Horror","Mystery"]},
  {id:"M8",  genres:["Adventure"]},
  {id:"M9",  genres:["Drama","Romance"]},
  {id:"M10", genres:["Action","Adventure"]}
] AS row
MATCH (m:Movie {id: row.id})
UNWIND row.genres AS gName
MATCH (g:Genre {name: gName})
CREATE (m)-[:IN_GENRE]->(g);

///////////////////////
// 3.2 IN_GENRE (Series)
///////////////////////

UNWIND [
  {id:"S1",  genres:["Sci-Fi","Drama"]},
  {id:"S2",  genres:["Comedy"]},
  {id:"S3",  genres:["Mystery"]},
  {id:"S4",  genres:["Fantasy"]},
  {id:"S5",  genres:["Action"]},
  {id:"S6",  genres:["Romance"]},
  {id:"S7",  genres:["Thriller"]},
  {id:"S8",  genres:["Adventure"]},
  {id:"S9",  genres:["Horror"]},
  {id:"S10", genres:["Drama","Mystery"]}
] AS row
MATCH (s:Series {id: row.id})
UNWIND row.genres AS gName
MATCH (g:Genre {name: gName})
CREATE (s)-[:IN_GENRE]->(g);

///////////////////////
// 3.3 ACTED_IN
///////////////////////

UNWIND [
  {actorId:"A1",  movies:["M1","M2"],      series:["S1"]},
  {actorId:"A2",  movies:["M3","M4"],      series:["S2","S3"]},
  {actorId:"A3",  movies:["M5"],           series:["S4","S5"]},
  {actorId:"A4",  movies:["M6","M7"],      series:["S6"]},
  {actorId:"A5",  movies:["M8"],           series:["S7","S8"]},
  {actorId:"A6",  movies:["M9","M10"],     series:["S9"]},
  {actorId:"A7",  movies:["M1","M3","M5"], series:["S10"]},
  {actorId:"A8",  movies:["M2","M4"],      series:["S1","S2"]},
  {actorId:"A9",  movies:["M6","M8"],      series:["S3","S4"]},
  {actorId:"A10", movies:["M7","M9"],      series:["S5","S6"]}
] AS row
MATCH (a:Actor {id: row.actorId})
FOREACH (mid IN row.movies |
  MATCH (m:Movie {id: mid})
  CREATE (a)-[:ACTED_IN]->(m)
)
FOREACH (sid IN row.series |
  MATCH (s:Series {id: sid})
  CREATE (a)-[:ACTED_IN]->(s)
);

///////////////////////
// 3.4 DIRECTED
///////////////////////

UNWIND [
  {directorId:"D1",  movies:["M1","M2"], series:["S1"]},
  {directorId:"D2",  movies:["M3"],      series:["S2","S3"]},
  {directorId:"D3",  movies:["M4","M5"], series:["S4"]},
  {directorId:"D4",  movies:["M6"],      series:["S5","S6"]},
  {directorId:"D5",  movies:["M7"],      series:["S7"]},
  {directorId:"D6",  movies:["M8","M9"], series:["S8"]},
  {directorId:"D7",  movies:["M10"],     series:["S9"]},
  {directorId:"D8",  movies:["M1","M3"], series:["S10"]},
  {directorId:"D9",  movies:["M5","M7"], series:[]},
  {directorId:"D10", movies:["M9"],      series:[]}
] AS row
MATCH (d:Director {id: row.directorId})
FOREACH (mid IN row.movies |
  MATCH (m:Movie {id: mid})
  CREATE (d)-[:DIRECTED]->(m)
)
FOREACH (sid IN row.series |
  MATCH (s:Series {id: sid})
  CREATE (d)-[:DIRECTED]->(s)
);

///////////////////////
// 3.5 WATCHED (Users -> Movies)
///////////////////////

UNWIND [
  {userId:"U1",  movieId:"M1",  rating:5},
  {userId:"U1",  movieId:"M3",  rating:4},
  {userId:"U2",  movieId:"M2",  rating:3},
  {userId:"U2",  movieId:"M4",  rating:4},
  {userId:"U3",  movieId:"M5",  rating:5},
  {userId:"U3",  movieId:"M1",  rating:4},
  {userId:"U4",  movieId:"M6",  rating:2},
  {userId:"U5",  movieId:"M7",  rating:5},
  {userId:"U6",  movieId:"M8",  rating:4},
  {userId:"U7",  movieId:"M9",  rating:3},
  {userId:"U8",  movieId:"M10", rating:4},
  {userId:"U9",  movieId:"M2",  rating:2},
  {userId:"U10", movieId:"M3",  rating:3}
] AS row
MATCH (u:User {id: row.userId})
MATCH (m:Movie {id: row.movieId})
CREATE (u)-[:WATCHED {rating: row.rating}]->(m);

///////////////////////
// 3.6 WATCHED (Users -> Series)
///////////////////////

UNWIND [
  {userId:"U1",  seriesId:"S1", rating:5},
  {userId:"U2",  seriesId:"S2", rating:4},
  {userId:"U3",  seriesId:"S3", rating:4},
  {userId:"U4",  seriesId:"S4", rating:3},
  {userId:"U5",  seriesId:"S5", rating:5},
  {userId:"U6",  seriesId:"S6", rating:4},
  {userId:"U7",  seriesId:"S7", rating:5},
  {userId:"U8",  seriesId:"S8", rating:3},
  {userId:"U9",  seriesId:"S9", rating:4},
  {userId:"U10", seriesId:"S10",rating:5}
] AS row
MATCH (u:User {id: row.userId})
MATCH (s:Series {id: row.seriesId})
CREATE (u)-[:WATCHED {rating: row.rating}]->(s);
