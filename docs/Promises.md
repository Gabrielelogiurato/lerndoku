# JavaScript Promises

In dieser Lerndoku schreibe ich die Grundlagen von Promises auf für mein ÜK und für's weitere Programmieren.

## Was ist ein Promise

Es ist ein Objekt welches einen Zukünftigen Wert darstellt wie Zb im ÜK gelernt ein Ergebnis einer Asynchronen Operation. Sie hat immer drei Zustände:

- Pending "Das ist wenn der Vorgang noch läuft"
- fulfilled "wenn der Vorgang ERfolgreich war"
- rejected " Der Vorgamg ist fehlgeschlagen"

  Man sollte .then für Erfolge verwenden und .catch für Fehler.
Hier ein Beipsiel:

```
promise
  .then(result => console.log("Ergebnis:", result))
  .catch(error => console.error("Fehler:", error));
```

Man kann auch mehrere .then benutzen um sachen zu verbinden:

```
fetch("https://blablabla")
  .then(res => res.json())
  .then(data => console.log(data.title))
  .catch(err => console.error(err));

```

Dann gibt es auch die lesbarere und kürzere Version die macht mach mit async und await:

```
async function ladePost() {
  try {
    const res = await fetch("https://blablabla");
    const data = await res.json();
    console.log(data.title);
  } catch (err) {
    console.error(err);
  }
}

```


Man kann auch SetTimeout benutzen um etwas zu verzögern:

```
console.log("Start");

setTimeout(() => {
  console.log("Das kommt nach 2 Sekunden");
}, 2000);

console.log("Ende");

```

# async ist glechzeitig und sync ist alles nacheinander
