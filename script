window.addEventListener("load", () => {
    /**
     * Hilfsfunktion zum Umschalten des sichtbaren Inhalts
     *
     * @param {String} id HTML-ID des anzuzeigenden <main>-Elements
     * @param {String} title Neuer Titel für den Browser-Tab
     */
    let swapContent = (id, title) => {
        document.querySelectorAll("main").forEach(mainElement => {
            mainElement.classList.add("hidden");
        })

        let element = document.querySelector(`#${id}`);
        if (element) element.classList.remove("hidden");

        document.title = ` ${title} | Aufgabe: SPA-Router`;
    }

    /**
     * Konfiguration des URL-Routers
     */
    let routes = [
        {
            url: "^home-content$", 
            show: () => swapContent("startseite", "Startseite"),
        },
        {
            url: "/([0-9]+)$",
            show: (matches) => {
                swapContent("details", "Produktdetails");
                loadProductDetails(matches[1]);
            }
        },
        {
            url: "index.html$",
            show: () => swapContent("startseite", "Startseite"),
        },{
            url: "smartphones$",
            show: () => swapContent("kategorien", "kategorien-Ansicht"),
            
        }
    ];
    let router = new Router(routes);
    router.start();
});

"use strict";

/**
 * Diese Klasse stellt einen so genannten Single Page Router dar. Sie überwacht
 * die momentan aktive URL (die sich durch Anklicken eines Links oder durch
 * Setzen von window.location.href in JavaScript jederzeit ändern kann) und
 * sorgt dafür, dass der für die jeweilige URL richtige Inhalt angezeigt wird.
 *
 * Hierzu muss der Klasse bei ihrer Instantiierung eine Liste mit Routen
 * mitgegeben werden, die jeweils einen regulären Ausdrück zur Prüfung der
 * URL sowie eine Funktion zur Anzeige des dazugehörigen Inhalts besitzen.
 */
class Router {
    /**
     * Konstruktor. Im Parameter routes muss eine Liste mit den vorhandenen
     * URL-Routen der App übergeben werden. Die Liste muss folgendes Format
     * haben:
     *
     *      [
     *          {
     *              url: "^/$"              // Regulärer Ausdruck zur URL
     *              show: matches => {...}  // Funktion zur Anzeige des Inhalts
     *          }, {
     *              url: "^/Details/(.*)$"  // Regulärer Ausdruck zur URL
     *              show: matches => {...}  // Funktion zur Anzeige des Inhalts
     *          },
     *          ...
     *      ]
     *
     * @param {List} routes Definition der in der App verfügbaren Seiten
     */
    constructor(routes) {
        this._routes = routes;
        this._started = false;

        window.addEventListener("hashchange", () => this._handleRouting());
    }

    /**
     * Routing starten und erste Route direkt aufrufen.
     */
    start() {
        this._started = true;
        this._handleRouting();
    }

    /**
     * Routing stoppen, so dass der Router nicht mehr aktiv wird, wenn Link
     * angeklickt wird oder sich die URL der Seite sonst irgendwie ändert.
     */
    stop() {
        this._started = false;
    }

    /**
     * Diese Methode wertet die aktuelle URL aus und sorgt dafür, dass der
     * dazu passende Inhalt angezeigt wird. Der Einfachheit halber wird eine
     * so genannte Hash-URL verwendet, bei der die aufzurufende Seite nach
     * einem #-Zeichen stehen muss. Zum Beispiel:
     *
     *   http://localhost:8080/index.html#/Detail/1234
     *
     * Hier beschreibt "/Detail/1234" den Teil der URL mit der darzustellenden
     * Seite. Der Vorteil dieser Technik besteht darin, dass ein Link mit einer
     * solchen URL keine neue Seite vom Server lädt, wenn sich der vordere Teil
     * der URL (alles vor dem #-Zeichen) nicht verändert. Stattdessen wird
     * ein "hashchange"-Ereignis geworfen, auf das hier reagiert werden kann.
     *
     * Auf Basis der History-API und dem "popstate"-Ereignis lässt sich ein
     * noch ausgefeilterer Single Page Router entwickeln, der bei Bedarf auch
     * ohne das #-Zeichen in der URL auskommt. Dies würde jedoch sowohl mehr
     * Quellcode sowie eine spezielle Server-Konfiguration erfordern, um
     * wirklich stabil zu laufen. Denn der Single Page Router kann nur aktiv
     * werden, wenn die Seite schon geladen wurde, so dass insbesondere der
     * erste Aufruf, wenn ein so genannter "Deep Link", der nicht auf die
     * Startseite der Anwendung zeigt, aufgerufen wird, zu einem "404 Not Found"
     * Fehler führen kann. Der Server müsste in diesem Fall so konfiguriert
     * werden, dass er trotzdem die Startseite zurückliefert, auch wenn diese
     * nicht direkt angesprochen wurde. Diesen Aufwand sparen wir uns hier. :-)
     */
    _handleRouting() {
        // Standardmäßig die Startseite anzeigen, wenn keine spezifische Hash-URL vorhanden ist.
        let url = window.location.hash.slice(1) || "home-content";

        // Sonderbehandlung für den Fall, dass die index.html direkt aufgerufen wird.
        if (!window.location.hash && window.location.pathname.endsWith("index.html")) {
            url = "index.html";
        }

        let matches = null;
        let route = this._routes.find(p => matches = url.match(new RegExp(p.url)));

        if (!route) {
            console.error(`Keine Route zur URL ${url} gefunden!`);
            return;
        }

        route.show(matches);
    }

}

document.addEventListener('DOMContentLoaded', function() {
    // Fügt Event-Listener für alle Produkte hinzu
    let products = document.querySelectorAll('.product');
    products.forEach(product => {
        product.addEventListener('click', function() {
            let productId = this.getAttribute('data-productid');
            loadProductDetails(productId);
        });
    });

    // Fügt Event-Listener zum Home-Link hinzu, um die Suchergebnisse zu clearen
    let homeLink = document.querySelector('li a[href="#home-content"]');
    homeLink.addEventListener('click', clearSearchResults);
    
    // Suchfunktion
    let searchButton = document.querySelector('.search-button');
    searchButton.addEventListener('click', function() {
        // Contact und About Us Elemente ausblenden
        let aboutTextDiv = document.querySelector('.AboutText');
        if (aboutTextDiv) aboutTextDiv.style.display = 'none';

        let contactFormDiv = document.querySelector('.contact-form');
        if (contactFormDiv) contactFormDiv.style.display = 'none';

        // Wert aus dem Suchfeld holen
        let searchValue = document.querySelector('.search-box').value;
        
        // Überprüfen, ob ein Suchwert eingegeben wurde
        if (searchValue) {
            // Den .search-results-Bereich initial verstecken
            document.querySelector('.search-results').style.display = 'none';

            // API-URL mit Suchwert dynamisch erstellen
            let apiUrl = `https://dummyjson.com/products/search?q=${searchValue}`;
            
            // API aufrufen
            fetch(apiUrl)
            .then(res => {
                if (!res.ok) {
                    throw new Error('Netzwerkantwort war nicht ok');
                }
                return res.json();
            })
            .then(data => {
                console.log("Daten von der API erhalten:", data);
                displayResults(data);
            })
            .catch(error => {
                console.error("Es gab einen Fehler beim Abrufen der Daten:", error);
            });
        } else {
            alert('Bitte geben Sie einen Suchwert ein.');
        }
    })
});


function clearSearchResults() {
    let resultsDiv = document.querySelector('.search-results');
    if (resultsDiv) {
        resultsDiv.innerHTML = '';
    }
    let searchInput = document.querySelector('.search-box'); // Die Klasse entspricht dem Suchfeld in Ihrer Suchleiste
    if (searchInput) {
        searchInput.value = '';
    }
}

function loadProductDetails(productId) {
    fetch(`https://dummyjson.com/products/${productId}`)
    .then(res => {
        if (!res.ok) {
            throw new Error('Netzwerkantwort war nicht ok');
        }
        return res.json();
    })
   .then(data => {
        let detailsDiv = document.getElementById('details-ansicht');
        detailsDiv.innerHTML = `
            <h1>${data.title}</h1>
            <p>${data.description}</p>
            <p>ID: ${data.id}</p>
            <p>Preis: ${data.price} €</p>
            <p>Rabatt: ${data.discountPercentage}%</p>
            <p>Bewertung: ${data.rating}</p>
            <p>Lagerbestand: ${data.stock}</p>
            <p>Marke: ${data.brand}</p>
            <p><a href="#/${data.category}" class="category-link">Kategorie</a>: ${data.category}</p>
            `;
        fetchCarts(productId);
        // Stellen Sie sicher, dass die Detailansicht sichtbar ist
        document.getElementById('details').classList.remove('hidden');
    })
    .catch(error => {
        console.error("Es gab einen Fehler beim Abrufen der Daten:", error);
    });
}







function loadCategories() {
    fetch('https://dummyjson.com/products/categories')
      .then(res => {
        if (!res.ok) {
          throw new Error('Netzwerkantwort war nicht ok');
        }
        return res.json();
      })
      .then(categories => {
        const categoriesListDiv = document.getElementById('categories-list');
        if (categoriesListDiv) {
          // Erstelle eine Liste für die Kategorien
          let categoriesHtml = '<ul>';
          categories.forEach(category => {
            // Füge jede Kategorie als Listenelement hinzu, nur als Text
            categoriesHtml += `<li>${category}</li>`;
          });
          categoriesHtml += '</ul>';
          
          // Setze die innerHTML des div-Elements mit der erstellten Liste
          categoriesListDiv.innerHTML = categoriesHtml;
        }
      })
      .catch(error => {
        console.error("Es gab einen Fehler beim Abrufen der Kategorien:", error);
      });
  }
  
  // Aufruf der Funktion beim Laden der Seite oder zu einem anderen Zeitpunkt
  loadCategories();


function displayResults(data) {
    let resultsDiv = document.querySelector('.search-results');
    resultsDiv.innerHTML = '';
    console.log(data);

    if (data && data.products && data.products.length > 0) {
        data.products.forEach(item => {
            console.log(item.title);
            if (item && item.title) {
                let itemLink = document.createElement('a');
                itemLink.href = `#/${item.id}`;
                itemLink.innerText = item.title;
                itemLink.style.display = 'block';
                itemLink.style.marginBottom = '10px'; // Optional: fügt einen Abstand zwischen den Ergebnissen hinzu
    
                // Hinzufügen des Event-Listeners zum Link
                itemLink.addEventListener('click', function(event) {
                    
    
                    // Hier rufst du deine Funktion auf, z.B.:
                    const itemid = item.id;
                    showProductDetails(itemid);
    
                    
                });
    
                resultsDiv.appendChild(itemLink);
            } else {
                console.log("Produkt ohne Titel gefunden:", item);
            }
        });
        resultsDiv.style.display = 'block';
    } else {
        resultsDiv.innerText = 'Keine Ergebnisse gefunden';
        resultsDiv.style.display = 'block';
    }

    
}

function showProductDetails(itemid) {
    // Verwende die bestehende Funktion, um die Produktdetails zu laden
    loadProductDetails(itemid);
    
    // Stelle sicher, dass das Detailfenster angezeigt wird
    let detailsDiv = document.getElementById('details');
    detailsDiv.classList.remove('hidden');
}

// Funktion, um alle Warenkörbe zu holen
function fetchCarts(prID) {
  fetch('https://dummyjson.com/carts')
    .then(res => res.json())
    .then(data => {
        console.log(data);
        let cartsWithProduct = data.carts.filter((cart) => 
            cart.products.some((product) => product.id === prID)
        ); 
        
        console.log(cartsWithProduct);// Nach Erhalt der Daten, filtern Sie die Warenkörbe, um diejenigen zu finden, die das Produkt enthalten

        // Anzeigen der Warenkörbe auf der Produktdetailseite
        displayCartsOnProductDetail(cartsWithProduct);
    })
    .catch(error => {
      console.error('Error fetching carts:', error);
    });
}

// Funktion, um gefilterte Warenkörbe auf der Produktdetailseite anzuzeigen
function displayCartsOnProductDetail(carts) {
  const detailsContainer = document.getElementById('details-ansicht'); // Container-Element auf Ihrer Webseite
  carts.forEach(cart => {
    // Erstellen Sie Elemente für jeden Warenkorb und fügen Sie diese zum Container hinzu
    const cartElement = document.createElement('div');
    cartElement.innerHTML = `
    
      <h2>Warenkorb ${cart.id}</h2>
      <p>Summe (mit Rabatt): ${cart.discountedTotal}€</p>
      <p>Originalsumme: ${cart.total}€</p>
      <p>Anzahl der Produkte: ${cart.totalProducts}</p>
      <p>Gesamtmenge: ${cart.totalQuantity} Stück</p>
      <p>Benutzer-ID: ${cart.userId}</p>                    `;
    detailsContainer.appendChild(cartElement);
  });
}

// Starten Sie die Funktion, um die Warenkörbe zu holen, wenn die Produktdetailseite geladen wird

