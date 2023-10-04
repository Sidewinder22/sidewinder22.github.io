---
title: Szkielet aplikacji webowej w JavaScript 
date: 2023-09-25 12:30:04
tags:
- js
categories:
- programowanie
author: Sidewinder22
---

# Wstęp

Cześć!

W tym poście chciałbym pokazać Wam, jak stworzyć szkielet takiej aplikacji webowej i jak połączyć backend z fronted’em.

Do tego projektu wykorzystam następujące narzędzia: [Node.js](https://nodejs.org/en) + [Express](https://expressjs.com/) do backendu, a [React](https://pl.legacy.reactjs.org/) do frontendu.


# Przygotowanie

## Wymagania wstępne

Zanim ruszymy przedstawmy kilka wymagań wstępnych:

- konto na [Githubie](https://github.com/),
- system operacyjny Linux – dla niego podaję komendy powłoki (może być także Windows z zainstalowanym klientem gita i managerem paczek npm lub środowiskiem WSL),
- przeglądarka internetowa – u mnie to będzie Firefox,
- edytor tekstu – w moim przypadku będzie to Visual Studio Code.

## Instalacja npm

Pierwsze co zrobimy, to będzie instalacja managera paczek javascript:

```bash
sudo pacman -S npm    # Arch Linux (pacman based distros)
sudo apt install npm  # Ubuntu (apt-get based distros)
```

## Hostowanie projektu na GitHubie

Do przechowywania kodu źródłowego projektu użyję GitHub’a.
Należy stworzyć tam projekt, a następnie sklonować go na nasz dysk:

```bash
git clone [link-do-stworzonego-projektu]
cd [nazwa-katalogu-projektu]

# np. mój przykładowy projekt:
git clone https://github.com/Sidewinder22/SideFileEditor
cd SideFileEditor
```

# Przygotowanie Backendu

Wchodzimy do sklonowanego z serwisu GitHub katalogu, w którym będzie umieszczony nasz projekt, w tworzymy w nim podkatalog i inicjujemy projekt.

```bash
mkdir backend
cd backend
npm init -y
```

Ostatnia komenda utworzy nam plik `package.json`, w którym będą umieszczone dane konfiguracyjne naszego backendu.

Zainstalujmy framework Express:

    npm install express --save

Do pliku „scripts” znajdującego się w pliku `package.json`, dodajmy komendę do uruchomienia naszego skryptu:

```json
// package.json

...
"start": "node app.js",
...
```

Stwórzmy przykładowy plik `app.js` korzystający z frameworka Express:

```js
// backend/app.js

var express = require('express');
var app = express();
app.get('/', function(request, response) {
    response.send('<h1>Expense Manager</h1>');
});
app.listen(3000, function() {
    console.log('Expense manager backend app listening on the port 3000');
});
```

Spróbujmy uruchomić projekt, używając naszej dodanej komendy `start`:

    npm start

Teraz możemy otworzyć przeglądarkę pod adresem `http://localhost:3000/` i sprawdzić, czy nasz kod faktycznie działa. Jeśli tak jest, to pojawi się strona wraz z nagłówkiem:

![Page with header](/assets/images/app_web_js_sklt/app_web_js_sk-1.png)


# Przygotowanie Frontendu

Teraz zajmiemy się klientem naszej aplikacji webowej.
Zaczniemy od stworzenia nowej aplikacji w Reakcie:

```bash
npx create-react-app frontend
cd frontend
npm start
```

Jeśli wszystko się powiedzie, to ostatnia komenda uruchomi nam nową kartę w przeglądarce, która będzie wyglądała tak:


![Welcome screen from React](/assets/images/app_web_js_sklt/app_web_js_sk-2.png)


# Implementacja

Mamy już działające frontend i backend – na razie oddzielnie. Przejdźmy teraz do kolejnego etapu – spróbujemy je razem połączyć.
Dodajmy do backendu dodatkowy endpoint (`/welcome`):

```js
// backend/app.js

...
app.get('/welcome', function(request, response) {
    console.log('Handling welcome request...');
    response.setHeader('Content-Type', 'application/json');
    response.end(JSON.stringify('{"label": "Welcome from the backend!"}'));
});
...
```

A do frontendu dodamy przycisk, naciśnięcie którego spowoduje wysłanie requestu do serwera i wyświetli zawartość otrzymanego jsona.
Najpierw jednak usuniemy zawartość folderu `src`. Sami stworzymy w nim szkielet aplikacji React.

```js
// frontend/src/App.js

import './index.css';
import React, { Component } from 'react';
class App extends Component {
    constructor(props) {
        super(props);
        this.state = {
            buttonLabel: "Connect",
            data: null
        };
        this.handleButtonClick = this.handleButtonClick.bind(this);
    }
    handleButtonClick() {
        console.log('Handling button click');
        fetch('http://localhost:2999/welcome')
            .then(response => response.json())
            .then(data => {
                console.log('data: %s', data)
                const parsedData = JSON.parse(data)
                const label = parsedData.label;
                this.setState({ data: label})
            })
            .then(this.setState({ buttonLabel: "Connected"}))
            .catch(error => {
                console.error(error);
                
                return { name: "network error", description: ""};
            });
    }
    render() {
        return (
            <div>
                <h1>
                    Expense Manager
                </h1>
                <button
                    className='connect_button'
                    onClick={ this.handleButtonClick }>
                        { this.state.buttonLabel }
                </button>
                <p>
                    <b>Received data</b>: { this.state.data }
                </p>
            </div>
        )
    }
};
export default App;
```

Do tego dodamy krótki plik ze stylami:

```css
/* frontend/src/index.css */

* {
    padding: 0;
    margin: 0;
    box-sizing: border-size;
}
body {
    font-size: 1em;
    margin: 20px;
}
h1 {
    margin: 20px;
}
h2 {
    margin: 20px;
}
.connect_button {
    padding: 5px;
    border-radius: 5px;
    margin: 10px;
}
```

I oczywiście główny plik aplikacji Reacta:

```js
// frontend/src/index.js

import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
ReactDOM.render(
    <App />,
    document.getElementById('root')
); 
```

Teraz nadeszła pora na przetestowanie naszej implementacji. Tak wygląda strona po uruchomieniu:

![Page with header and Connect button](/assets/images/app_web_js_sklt/app_web_js_sk-3.png)

Kliknijmy przycisk `Connect` i zobaczmy, co się stanie.

![Page with header and Connected button](/assets/images/app_web_js_sklt/app_web_js_sk-4.png)

Zmienił się napis na przycisku z `Connect` na `Connected!`
Ale w linii `Received data:` nic się nie pojawiło.
Sprawdźmy, co się stało. W tym celu uruchomimy konsolę deweloperską przeglądarki.
Kliknijmy prawy przycisk myszy na naszej stronie i wybierzmy opcję `Zbadaj`. Na stronie pojawi się widok narzędzi deweloperskich. Wybierzmy Konsolę (Console) i jeszcze raz kliknijmy nasz przycisk `Connect`.

![Page with header and Connected button and errors from console](/assets/images/app_web_js_sklt/app_web_js_sk-5.png)

Brawo! Właśnie trafiliśmy na problem związany z dziedziną cyberbezpieczeństwa: pojawił się błąd związany z [Same Origin Policy (SOP)](https://en.wikipedia.org/wiki/Same-origin_policy).
Jest to zabezpieczenie wbudowane w przeglądarkę. Uniemożliwia ono dwóm aplikacją mającym różne Originy korzystanie z elementów innej aplikacji.


Na Origin składają się trzy elementy: protokół, host (domena i subdomena to dwie różne rzeczy) i port.Zastanówmy się, jakie Originy mamy w naszej aplikacji:

    http://localhost:2999 – frontend
    http://localhost:3000 – backend

Frontend działający pod pierwszym Originem próbuje pobrać dane tekstowe z drugiego Originu – stąd powyższy błąd.

Aby go naprawić użyjemy mechanizmu [CORS (Cross-Origin Resource Sharing)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Umożlwia on wykonywanie zapytań HTTP pomiędzy różnymi Originami.
Zaczniemy od instalacji modułu cors na backendzie (wywołamy ją oczywiście w folderze backend):

    npm install cors

Dodajmy także zmiany w kodzie backendu, które umożliwią nam skorzystanie z tego modułu. Dodamy je na początku pliku, zaraz po definicji numeru portu.

```js
// backend/app.js

...
var cors = require('cors')
app.use(cors({
    origin: 'http://localhost:3000'
}));
...
```

Na powyższym listinigu widać, że dodaliśmy do Origina adres naszej aplikacji frontend.

Teraz uruchomimy jeszcze raz nasz backend za pomocą komedy: `npm start` .
Jeszcze tylko odświeżymy stronę i wciśniemy przycisk – oto efekt:

![Page with Connected button and response from backend](/assets/images/app_web_js_sklt/app_web_js_sk-6.png)

# Podsumowanie

Gratulacje!
Udało nam się odebrać krótki tekst powitalny z naszego backendu. Mamy już więc podstawowy szkielet aplikacji w architekturze klient-serwer.

To jeden z moich początkowych wpisów, mam nadzieję, że komuś z Was się przyda 🙂
Wszystkie uwagi i pytania zgłaszajcie śmiało w komentarzach.

Pozdrawiam,
{\\\_Sidewinder22\_/}