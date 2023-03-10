# Exercici 1

### App

```jsx
import { useState } from "react";
import { data } from "./assets/data";
import { Checkbox } from "./components/Checkbox";

// Defineixo el format del preu.
const getFormattedPrice = (price) => `${price}€ `;

export default function App() {
  // Inicialitzo l'estat de total a 0.
  const [total, setTotal] = useState(0);

  // Creo un nou array amb tots els elements en state FALS (.fill posa totes les caselles en fals). És un array de booleans.
  const [checkedState, setCheckedState] = useState(
    new Array(data.length).fill(false)
  );
  // Duplico l'array checkedState que passa a dir-se newCheckedState. Quan es cridi a onCheckedSelected l'estat de l'ítem (i) clicat passarà a l'estat oposat (de true a false i de false a true). I es canviarà el checkedState amb el nou valor (guardat a newCheckedState).
  function onCheckboxSelected(i) {
    let nextCheckedState = [...checkedState];
    nextCheckedState[i] = !nextCheckedState[i];

    setCheckedState(nextCheckedState);

    // Faig un reduce amb el nou array on: si l'ítem que recorre el reduce és true, retornarà la suma de l'accumulador + el preu de l'ítem indicat (que troba a l'array (data) per posició amb index). I tot això retornarà l'accumulador (que conté la suma dels ítems amb checkbox true).
    const sumPrice = nextCheckedState.reduce((acc, currentValue, index) => {
      if (currentValue === true) {
        return acc + data[index].price;
      }

      return acc;
      // Al reduce després dels arguments i la funció li passo 0 que és el valor inicial de la suma de preus.
    }, 0);
    // Guardo dins de total la sumPrice a través del setTotal.
    setTotal(sumPrice);
  }

  return (
    // Retorno un h3 amb el títol.
    // Faig un map de data passant per props option, price i index.
    // Dins el component Checkbox defineixo què serà l'index, el text, el price i a quina funció s'haurà de cridar quan marquin la casella (quan facin onCheck, cridarem a la funció onCheckedboxSelected). També defineixo a què correspondrà el format del preu. I creo una p per mostrar el preu.
    <div>
      <h3>Què vols fer?</h3>
      <div>
        {data.map(({ option, price }, index) => {
          return (
            <Checkbox
              index={index}
              text={option}
              price={price}
              key={index}
              onCheck={onCheckboxSelected}
              getFormattedPrice={getFormattedPrice}
            />
          );
        })}
        <p> Total: {getFormattedPrice(total)}</p>
      </div>
    </div>
  );
}
```

### Checkbox

```jsx
import React from "react";

// Creo la funció Checkbox i li passo els props de l'index, la info que ha de mostrar (preu i text), la funció que haurà de crear per canviar l'estat (onCheck), l'estat del check (checkedState) i el format del preu (getFormattedPrice).
export function Checkbox({
  index,
  text,
  price,
  onCheck,
  checkedState,
  getFormattedPrice,
}) {
  // A Checkbox defineixo els props i els dono forma (plantilla). A App els cirdo per omplir d'info la plantilla.
  // La funció retorna un ínput de tipus checkbox amb una id = a l'index, la info (que la treu del map fet a App) i la capacitat de fer canvis a través dues funcions (checked) i (onChange).
  // Creo també el "format" o plantilla amb una label que ha de mostrar l'html.
  return (
    <div key={index}>
      <div>
        <input
          type="checkbox"
          id={index}
          name={text}
          value={text}
          checked={checkedState}
          onChange={() => onCheck(index)}
        />
        <label>
          {text} {getFormattedPrice(price)}
        </label>
      </div>
    </div>
  );
}
```

# EXERCICI 2

### App

```jsx
import { useEffect, useState } from "react";
import { data } from "./assets/data";
import { Checkbox } from "./components/Checkbox";
import { FormWeb } from "./components/FormWeb";

const getFormattedPrice = (price) => `${price}€ `;

export default function App() {
  // ESTATS
  const [total, setTotal] = useState(0);
  const [numPages, setNumPages] = useState(1);
  const [numLanguages, setNumLanguages] = useState(1);
  const [checkboxPrice, setCheckboxPrice] = useState(0);

  // ESTAT DESSELECCIONAT DE TOTS ELS ELEMENTS DE L'ARRAY
  const [checkedState, setCheckedState] = useState(
    new Array(data.length).fill(false)
  );

  // EFFECTS (Quan canvia l'estat de que està entre [] es crida a la funció)
  useEffect(() => {
    calculateTotalPrice();
  }, [checkedState, numLanguages, numPages]);

  // SELECCIONA I DESSELECCIONA CHECKBOX
  function onCheckboxSelected(i) {
    let nextCheckedState = [...checkedState];
    nextCheckedState[i] = !nextCheckedState[i];
    // CANVIA L'ESTAT DE LA CHECKBOX
    setCheckedState(nextCheckedState);

    // SUMA EL PREU DELS PRODUCTES SELECCIONATS
    const sumPrice = nextCheckedState.reduce((acc, currentValue, index) => {
      if (currentValue === true) {
        return acc + data[index].price;
      }
      return acc;
    }, 0);
    setCheckboxPrice(sumPrice);
  }
  // CANVIA L'ESTAT DEL NUM DE PÀGINES
  function handlePagesChange(e) {
    setNumPages(e.target.value);
  }
  // CANVIA L'ESTAT DEL NUM D'IDIOMES
  function handleLanguagesChange(e) {
    setNumLanguages(e.target.value);
  }

  // CALCULA EL PREU DE LA WEB AMB DIF. PÀGINES I DIF. IDIOMES
  // CALCULA EL PREU TOTAL (PÀGINES + CHECKBOX) I CANVIA L'ESTAT
  function calculateTotalPrice() {
    const totalWeb = numPages * numLanguages * 30;
    const total = totalWeb + checkboxPrice;

    setTotal(total);
  }

  return (
    <div>
      <h3> Què vols fer? </h3>
      <div>
        {data.map(({ option, price }, index) => {
          return (
            <>
              <Checkbox
                index={index}
                text={option}
                price={price}
                key={index}
                onCheck={onCheckboxSelected}
                getFormattedPrice={getFormattedPrice}
              />
              {checkedState[0] &&
                index === 0 && ( // MOSTRA EL FORMULARI AL MARCAR LA PRIMERA CHECKBOX (checkedState[] vol dir si l'index 0 està check i index: darrera de quin vull que m'ho mostri).
                  <FormWeb
                    numPages={numPages}
                    numLanguages={numLanguages}
                    handlePagesChange={handlePagesChange}
                    handleLanguagesChange={handleLanguagesChange}
                  />
                )}
            </>
          );
        })}
        <p> Preu total: {getFormattedPrice(total)} </p>
      </div>
    </div>
  );
}
```

### FormWeb

```jsx
import React from "react";
import { Form } from "../styled";

export function FormWeb({
  numPages,
  handlePagesChange,
  numLanguages,
  handleLanguagesChange,
}) {
  return (
    // Form ve de l'styled i és qui dona forma al border.
    <>
      <Form>
        <label>
          Número de pàgines:
          <input
            name="numPages"
            type="number"
            value={numPages} // El value és l'estat (numPages).
            onChange={handlePagesChange} // Quan canvia crida a la funció handlePagesChange.
          />
        </label>

        <br />

        <label>
          Número d'idiomes:
          <input
            name="numLanguages"
            type="number"
            value={numLanguages}
            onChange={handleLanguagesChange}
          />
        </label>
      </Form>
    </>
  );
}
```

# EXERCICI 3

### App

```jsx
import { useEffect, useState } from "react";
import { data } from "./assets/data";
import { Checkbox } from "./components/Checkbox";
import { FormWeb } from "./components/FormWeb";

const getFormattedPrice = (price) => `${price}€ `;

export default function App() {
  // ESTATS
  const [total, setTotal] = useState(0);
  const [checkboxPrice, setCheckboxPrice] = useState(0);
  const [numPages, setNumPages] = useState(1);
  const [numLanguages, setNumLanguages] = useState(1);

  // ESTAT DESSELECCIONAT DE TOTS ELS ELEMENTS DE L'ARRAY
  const [checkedState, setCheckedState] = useState(
    new Array(data.length).fill(false)
  );

  // EFFECTS
  useEffect(() => {
    calculateTotalPrice();
  }, [checkedState, numLanguages, numPages]);

  // SELECCIONA I DESSELECCIONA CHECKBOX
  function onCheckboxSelected(i) {
    let nextCheckedState = [...checkedState];
    nextCheckedState[i] = !nextCheckedState[i];

    // CANVIA L'ESTAT DE LES PÀGINES I ELS IDIOMES A 0 QUAN ES DESSELECCIONA LA CHECKBOX DE WEB
    if (nextCheckedState[0] === false) {
      // Comp.ova que la primera casella (la de la web) està desmarcada-
      setNumLanguages(0); // Canvia l'estat del numPages a 0 perquè no ho sumi al preu.
      setNumPages(0);
    }
    // CANVIA L'ESTAT DE LA CHECKBOX
    setCheckedState(nextCheckedState);

    // SUMA EL PREU DELS PRODUCTES SELECCIONATS
    const sumPrice = nextCheckedState.reduce((acc, currentValue, index) => {
      if (currentValue === true) {
        return acc + data[index].price;
      }
      return acc;
    }, 0);

    // CANVIA L'ESTAT DE CHECKBOXPRICE
    setCheckboxPrice(sumPrice);
  }

  // CALCULA EL PREU DE LA WEB AMB DIF. PÀGINES I DIF. IDIOMES
  // CALCULA EL PREU TOTAL (PÀGINES + CHECKBOX) I CANVIA L'ESTAT
  function calculateTotalPrice() {
    const totalWeb = numPages * numLanguages * 30;
    const total = totalWeb + checkboxPrice;

    setTotal(total);
  }

  return (
    <div>
      <h3> Què vols fer? </h3>
      <div>
        {data.map(({ option, price }, index) => {
          return (
            <>
              <Checkbox
                index={index}
                text={option}
                price={price}
                key={index}
                onCheck={onCheckboxSelected}
                getFormattedPrice={getFormattedPrice}
              />
              {checkedState[0] &&
                index === 0 && ( // MOSTRA EL FORMULARI AL MARCAR LA PRIMERA CHECKBOX
                  <FormWeb
                    numPages={numPages}
                    numLanguages={numLanguages}
                    setNumPages={setNumPages}
                    setNumLanguages={setNumLanguages}
                  />
                )}
            </>
          );
        })}
        <p> Preu total: {getFormattedPrice(total)} </p>
      </div>
    </div>
  );
}
```

### FormWeb

```jsx
import React from "react";
import { Form, Button } from "../styled";
/* import { setNumPages } from '../App' */

export function FormWeb({
  numPages,
  setNumPages,
  handlePagesChange,

  numLanguages,
  setNumLanguages,
  handleLanguagesChange,
}) {
  //PÀGINES
  //Suma pàgina
  const increasePag = () => {
    setNumPages((count) => count + 1);
  };

  //Resta pàgina
  const decreasePag = () => {
    setNumPages((count) => count - 1);
  };

  // Canvia l'estat de la pàgina
  function handlePagesChange(e) {
    setNumPages(e.target.value);
  }

  //SUMA IDIOMA
  const increaseLanguages = () => {
    setNumLanguages((count) => count + 1);
  };

  //RESTA RESTA IDIOMA
  const decreaseLanguages = () => {
    setNumLanguages((count) => count - 1);
  };

  // CANVIA L'ESTAT DEL NUM D'IDIOMES
  function handleLanguagesChange(e) {
    setNumLanguages(e.target.value);
  }

  return (
    <>
      <Form>
        <div>
          Número de pàgines:
          <Button onClick={increasePag}>+</Button>
          <input
            name="numPages"
            type="number"
            value={numPages}
            onChange={handlePagesChange}
          />
          <Button onClick={decreasePag}>-</Button>
        </div>

        <br />

        <div>
          Número d'idiomes:
          <Button onClick={increaseLanguages}>+</Button>
          <input
            name="numLanguages"
            type="number"
            value={numLanguages}
            onChange={handleLanguagesChange}
          />
          <Button onClick={decreaseLanguages}>-</Button>
        </div>
      </Form>
    </>
  );
}
```

# EXERCICI 4

### Budget (antiga App)

```jsx
import { useEffect, useState } from "react";
import { data } from "../assets/data";
import { Checkbox } from "../components/Checkbox";
import { FormWeb } from "../components/FormWeb";
import { manageLocalStorage } from "../useLocalStorage"; // Importo la funció per fer el LOCALSTORAGE

const getFormattedPrice = (price) => `${price}€ `;

export default function Budget() {
  // ESTATS
  const [total, setTotal] = useState(0);
  const [checkboxPrice, setCheckboxPrice] = useState(0);
  const [numPages, setNumPages] = useState(0);
  const [numLanguages, setNumLanguages] = useState(0);

  // ESTAT DESSELECCIONAT DE TOTS ELS ELEMENTS DE L'ARRAY
  const [checkedState, setCheckedState] = useState(
    new Array(data.length).fill(false)
  );

  // RECUPERAR L'ESTAT (LOCALSTORAGE)
  // Crido a la funció del localStorage, `manageLocalStorage` i li passo la key, la variable de l'estat i la funció per canviar l'estat (que he creat abans al fer el useState).
  manageLocalStorage("total", total, setTotal);
  manageLocalStorage("checkboxPrice", checkboxPrice, setCheckboxPrice);
  manageLocalStorage("numPages", numPages, setNumPages);
  manageLocalStorage("numLanguages", numLanguages, setNumLanguages);
  manageLocalStorage("checkedState", checkedState, setCheckedState);

  // EFFECTS
  useEffect(() => {
    calculateTotalPrice();
  }, [checkedState, numLanguages, numPages]);

  // SELECCIONA I DESSELECCIONA CHECKBOX
  function onCheckboxSelected(i) {
    let nextCheckedState = [...checkedState];
    nextCheckedState[i] = !nextCheckedState[i];

    // CANVIA L'ESTAT DE LES PÀGINES I ELS IDIOMES
    // Si el primer checkbox (el de la web) està marcat, ha de posar les pàgines i els idiomes a 1 (perquè es puguin multiplicar entre si i després per 30 (Si està a 0 qualsevol cosa que multipliqui donarà 0 i no es podrà fer el preu total)).
    if (nextCheckedState[0] === true) {
      setNumLanguages(1);
      setNumPages(1);
    }

    if (nextCheckedState[0] === false) {
      setNumLanguages(0);
      setNumPages(0);
    }
    // CANVIA L'ESTAT DE LA CHECKBOX
    setCheckedState(nextCheckedState);

    // SUMA EL PREU DELS PRODUCTES SELECCIONATS
    const sumPrice = nextCheckedState.reduce((acc, currentValue, index) => {
      if (currentValue === true) {
        return acc + data[index].price;
      }
      return acc;
    }, 0);

    // CANVIA L'ESTAT DE CHECKBOXPRICE
    setCheckboxPrice(sumPrice);
  }

  // CALCULA EL PREU DE LA WEB AMB DIF. PÀGINES I DIF. IDIOMES
  // CALCULA EL PREU TOTAL (PÀGINES + CHECKBOX) I CANVIA L'ESTAT
  function calculateTotalPrice() {
    const totalWeb = numPages * numLanguages * 30;
    const total = totalWeb + checkboxPrice;

    setTotal(total);
  }

  return (
    <div>
      <h1>Què vols fer?</h1>
      <div>
        {data.map(({ option, price }, index) => {
          return (
            <>
              <Checkbox
                index={index}
                text={option}
                price={price}
                key={index}
                checked={checkedState[index]} // Quan estigui seleccionat, mostra l'estat (seleccionat) de la checkbox per la qual estigui passant el map (això ho faig amb l'index).
                onCheck={onCheckboxSelected}
                getFormattedPrice={getFormattedPrice}
              />
              {checkedState[0] &&
                index === 0 && ( // MOSTRA EL FORMULARI AL MARCAR LA PRIMERA CHECKBOX
                  <FormWeb
                    numPages={numPages}
                    numLanguages={numLanguages}
                    setNumPages={setNumPages}
                    setNumLanguages={setNumLanguages}
                  />
                )}
            </>
          );
        })}
        <p>
          <b>Preu total: {getFormattedPrice(total)}</b>
        </p>
      </div>
    </div>
  );
}
```

### useLocalStorage (la funció per guardar i recuperar l'estat de les coses)

```js
import { useEffect } from "react"; // Importo el useEffect
// A la funció i passo una key, un estat i una funció per canviar l'estat.
export const manageLocalStorage = (key, state, setState) => {
  // RECUPERAR LA INFO DEL LOCALSTORAGE (Al obrir la pàgina)
  useEffect(() => {
    // A restoredState es guarda: El que porta (segons la KEY que li passem) el GETITEM des del LOCALSTORAGE.
    const restoredState = localStorage.getItem(key);
    // Si hi ha algo dins de RESTOREDSTATE, ho guarda dins de l'STATE amb el setState. I per guardar-ho ho ha de convertir en no-string (amb el JSON.PARSE)
    if (restoredState) {
      setState(JSON.parse(restoredState));
    }
  }, []); // Els [] es posen per dir que s'ha d'activar quan s'obri la pàgina.

  // GUARDAR LA INFO AL LOCALSTORAGE (Mentre està en funcionament)
  useEffect(
    //Segons la KEY, agafa l'estat (que li ve per PROPS) i el convertex en string per guardar-ho al JSON.
    () => localStorage.setItem(key, JSON.stringify(state)),
    [state] // Quan canvi l'STATE fes que passi el que hi ha a dins del useEffect
  );
};
```

### Checkbox

```jsx
import React from "react";

export function Checkbox({
  index,
  text,
  price,
  onCheck,
  checked,
  getFormattedPrice,
}) {
  return (
    <div key={index}>
      <div>
        <input
          type="checkbox"
          id={index}
          name={text}
          value={text}
          checked={checked} // Li dic que quan estigui marcat (checked), cridi a l'element checked (definit a Budget)
          onChange={() => onCheck(index)}
        />
        <label>
          {text} {getFormattedPrice(price)}
        </label>
      </div>
    </div>
  );
}
```
# EXERCICI 5

### App
```jsx
import React from "react";
import Routes from "./application/routes";

export const App = () => <Routes />; // Ha d'anar a buscar les pàgines a ROUTES

export default App;
```

### Routes
```jsx
import { BrowserRouter, Route, Routes } from "react-router-dom"; // S'ha d'instal·lar react-router
import Welcome from "../pages/Welcome"; // Importo la pàgina de Welcome
import Budget from "../pages/Budget"; // Importo la pàgina de Budget

const Router = () => (
  <BrowserRouter>
    <Routes>
      <Route index element={<Welcome />} /> {// La primera ruta és mostrar el "component" o pàgina WELCOME}
      <Route path="/Budget/" element={<Budget />} /> {// La segona ruta és anar (a través del botó) al "component" o pàgina BUDGET.}
    </Routes>
  </BrowserRouter>
);

export default Router;

```
### Budget (queda igual que a l'exercici anterior)
### Welcome
```jsx
import React from "react";
import { Link } from "react-router-dom"; // Importo el LINK de react-router
import { ButtonStart } from "../styled"; // Importo l'estil del botó de styled

function Welcome() {
  return (
    <main>
      <h1>Pressupost</h1>
      <h2>Personalitza el teu servei</h2>
      <p>
        A continuació trobaràs una aplicació per calcular el pressupost de
        diferents serveis.
      
        <br />
        Pots triar entre les diferents opcions i el <b>preu s'actualitzarà
        automaticament</b> al afegir o treure serveis.
       
        <br />
        Podràs <b>guardar els pressupostos</b> que hagis creat i <b>visualitzar-los</b> a la
        part dreta de la pantalla. <br/> On també tindràs diferents <b>opcions de
        filtrat</b>.
      </p>
      <p><b>Crea el teu pressupost</b></p>
      
      <ul>
        <ButtonStart>
          <Link to="/Budget">Crea el teu pressupost</Link> {//Quan clicki al botó ha d'anar a la pàgina BUDGET}
        </ButtonStart>
      </ul>
    </main>
  );
}
```
# EXERCICI 6

### Styled
Creo el component ButtonInfo i li dono estil
```jsx
export const ButtonInfo = styled.button`
  font-size: 10px;
  font-weight: bold;
  color: white;
  border-radius: 50px;
  background-color: #00337c;
  margin: 8px;
`;
```

### FormWeb
Faig servir el plugin Swall que crea popups. Aquí es pot trobar el Swall https://sweetalert2.github.io/
```jsx
import Swal from "sweetalert2"; // Importo Swal

// POPUP
// Creo una funció perquè mostri el popup amb la info de les pàgines i pels idiomes (amb Swal) i customitzo el Swal (com indica a la documentació de la pàgina de Swal).
  const ExtraInfoPages = () => {
    Swal.fire({
      text: "Aquest component indica el número de planes que tindrà la teva pàgina web",
      showConfirmButton: false, // Per treure-li el botó
      showCloseButton: false,
      customClass: {
        popup: "coustomPopup", // Per donar estils (que es modifiquen al css (index.css))
      },
    });
  };

  const ExtraInfoLanguages = () => {
    Swal.fire({
      text: "Aquest component indica el número d'idiomes que tindrà la teva pàgina web",
      showConfirmButton: false,
      showCloseButton: false,
      customClass: {
        popup: "coustomPopup",
      },
    });
  };

  return (
    <>
      <Form>
        <div>
          Número de pàgines:
          <Button onClick={increasePag}>+</Button>
          <input name="numPages" type="number" value={numPages} />
          <Button onClick={decreasePag}>-</Button>
          <ButtonInfo onClick={ExtraInfoPages}>i</ButtonInfo> // Creo el botó i apunto a la funció perquè mostri el popup
        </div>

        <br />

        <div>
          Número d'idiomes:
          <Button onClick={increaseLanguages}>+</Button>
          <input
            name="numLanguages"
            type="number"
            min="0"
            value={numLanguages}
            readOnly
          />
          <Button onClick={decreaseLanguages}>-</Button>
          <ButtonInfo onClick={ExtraInfoLanguages}>i</ButtonInfo>
        </div>
      </Form>
    </>
  );
}
```

### index.css
Per customitzar el component Popup
```css
.coustomPopup{
  border: 2px solid;
  border-radius: 15px;
  height: auto;
  width: 800px;
  align-items: center;
};
```

# EXERCICI 7 i 10
### Budget
```jsx
import { useEffect, useState } from "react";
import { data } from "../assets/data";
import { Checkbox } from "../components/Checkbox";
import { FormWeb } from "../components/FormWeb";
import { manageLocalStorage } from "../useLocalStorage";
import { ButtonStart } from "../style/styled";
import Swal from "sweetalert2";
import { UserBudget } from "../userBudget";
import { ClientBudget } from "../components/clientBudget";

const getFormattedPrice = (price) => `${price}€ `;

export default function Budget() {
  // ESTATS
  const [total, setTotal] = useState(0);
  const [checkboxPrice, setCheckboxPrice] = useState(0);
  const [numPages, setNumPages] = useState(0);
  const [numLanguages, setNumLanguages] = useState(0);
  const [budgetList, setBudgetList] = useState([]); // Creo l'estat de budgetList
  const [services, setServices] = useState([]);

  // ESTAT DESSELECCIONAT DE TOTS ELS ELEMENTS DE L'ARRAY
  const [checkedState, setCheckedState] = useState(
    new Array(data.length).fill(false)
  );

  // RECUPERAR L'ESTAT (LOCALSTORAGE)
  manageLocalStorage("total", total, setTotal);
  manageLocalStorage("checkboxPrice", checkboxPrice, setCheckboxPrice);
  manageLocalStorage("numPages", numPages, setNumPages);
  manageLocalStorage("numLanguages", numLanguages, setNumLanguages);
  manageLocalStorage("checkedState", checkedState, setCheckedState);
  manageLocalStorage("budgetList", budgetList, setBudgetList); // Guarda l'estat de BudgetList després d'haver guardat el pressupost.
  manageLocalStorage("services", services, setServices);
  
  // EFFECTS
  useEffect(() => {
    calculateTotalPrice();
  }, [checkedState, numLanguages, numPages]);

  // SELECCIONA I DESSELECCIONA CHECKBOX
  function onCheckboxSelected(i) {
    let nextCheckedState = [...checkedState];
    nextCheckedState[i] = !nextCheckedState[i];

    // CANVIA L'ESTAT DE LES PÀGINES I ELS IDIOMES (A 0 quan està desmarcat i a 1 quan està marcat el checkbox)
    if (nextCheckedState[0] === true) {
      setNumLanguages(1);
      setNumPages(1);
    }

    if (nextCheckedState[0] === false) {
      setNumLanguages(0);
      setNumPages(0);
    }
    // CANVIA L'ESTAT DE LA CHECKBOX
    setCheckedState(nextCheckedState);

    // SUMA EL PREU DELS PRODUCTES SELECCIONATS
    const sumPrice = nextCheckedState.reduce((acc, currentValue, index) => {
      if (currentValue === true) {
        return acc + data[index].price;
      }
      return acc;
    }, 0);

    // CANVIA L'ESTAT DE CHECKBOXPRICE
    setCheckboxPrice(sumPrice);
  }

  // CALCULA EL PREU DE LA WEB AMB DIF. PÀGINES I DIF. IDIOMES
  // CALCULA EL PREU TOTAL (PÀGINES + CHECKBOX) I CANVIA L'ESTAT
  function calculateTotalPrice() {
    const totalWeb = numPages * numLanguages * 30;
    const total = totalWeb + checkboxPrice;
    setTotal(total);
  }

  function restartBudget() {
    setTotal(0),
      setCheckboxPrice(0),
      setCheckedState(new Array(data.length).fill(false)),
      setNumPages(0);
    setNumLanguages(0);
  }

  // DEMANAR EL NOM
  function showPopup() {
    // Creo la funció de mostrar popup

    // Mostra popup
    Swal.fire({
      // Amb un plugin mostra el popup
      html:
        "Introdueix les dades per guardar el presupost" +
        '<input id="swal-client" class="swal2-input" placeholder="Escriu el teu nom">' +
        '<input id="swal-title" class="swal2-input" placeholder="Títol del pressupost">',
      showCancelButton: true,
      cancelButtonText: "Cancelar",
      confirmButtonText: "Guardar",
      confirmButtonColor: "#86c8bc",
      preConfirm: () => {
        return [
          document.getElementById("swal-client").value, // Retorno el valor que han escrit a l'input
          document.getElementById("swal-title").value,
        ];
      },

      // Apareix un altre popup per confirmar que s'ha guardat el pressupost
      allowOutsideClick: () => !Swal.isLoading(),
    }).then((result) => {
      const currentTitle = result.value[1]; // Guardo el valor del nom (que han escrit a l'input)
      const currentName = result.value[0];

      if (result.isConfirmed) {
        Swal.fire({
          // Mostra la pantalla de s'ha guardat correctament

          text: "El teu pressupost s'ha guardat correctament",
          confirmButtonColor: "#86c8bc",
          showConfirmButton: false,
          icon: "success",
        });
      }
      // Guardar la data actual i li dona format
      const today = new Date();
      const currentDate = today.toDateString("es-ES");

      // Li passa a la funció saveBudget el tíol, el nom i la data
      saveBudget(currentTitle, currentName, currentDate);
    });
  }
  // GUARDAR EL PRESSUPOST (AMB EL NOM, TÍTOL I DATA)
  function saveBudget(currentTitle, currentName, currentDate) {
    const userBudget = new UserBudget( // Crida a la classe USERBUDGET passant-li el valor dels constructors (que van per ordre segons ho hagi posat a userBudget.js) creant aixi el new UserBudget
      currentTitle,
      currentName,
      currentDate,
      services,
      numPages,
      numLanguages,
      total
    );
    localStorage.setItem("currentTitle", currentTitle); // Guarda el títol al LocalStorage
    localStorage.setItem("currentName", currentName); // Guarda el nom al LocalStorage
    localStorage.setItem("currentDate", currentDate); // Guarda la data al LocalStorage

    const newBudgetList = [...budgetList]; // Fa una còpia de l'array budgetList
    newBudgetList.push(userBudget); // Afegeix el userBudget (creat a la funció amb els constructors) a l'array newBudgetList

    setBudgetList(newBudgetList); // Canvia l'estat de BudgetList per aquest nou, per tant té la info anterior amb el nou budget guardat també
    restartBudget(); // Quan s'hagi guardat crida a la funcio restartBudget que neteja el formulari i les checkbox
  }

  const servicesName = []; // Crea l'array buit (fora de la funció)
  // CONVERTIR L'ARRAY DE CHECKEDSTATE EN STRINGS (treient la info de DATA)
  useEffect(() => {
    checkedState.map((item, index) => {
      if (item === true) servicesName.push(data[index].option); // Si l'item (de l'array checkedState) és tue, agfa la OPTION del DATA corresponant a l`índex d'el true que acaba de verificar i guarda-la a SERVICESNAME
    });
    setServices(servicesName); // Modifica l'estat de SERVICES pel contingut de servicesName
  }, [checkedState]); // Tot això s'ha de fer quan canvii algun checkedState.

  return (
    <main>
      <h1>Què vols fer?</h1>
      <div>
        {data.map(({ option, price }, index) => {
          return (
            <>
              <Checkbox
                index={index}
                text={option}
                price={price}
                key={index}
                checked={checkedState[index]}
                onCheck={onCheckboxSelected}
                getFormattedPrice={getFormattedPrice}
              />
              {checkedState[0] &&
                index === 0 && ( // MOSTRA EL FORMULARI AL MARCAR LA PRIMERA CHECKBOX
                  <FormWeb
                    numPages={numPages}
                    numLanguages={numLanguages}
                    setNumPages={setNumPages}
                    setNumLanguages={setNumLanguages}
                    key={index}
                  />
                )}
            </>
          );
        })}
        <p>
          <b>Preu total: {getFormattedPrice(total)}</b>
        </p>
        <ButtonStart onClick={showPopup}>Guardar pressupost</ButtonStart>
      </div>

      {/* // MOSTRAR PRESSUPOSTOS */}
      <div>
        {budgetList !== [] && // Si l'array budgetList NO està buit: fa un map de l'array agafant tots els valors següents
          budgetList.map(
            (
              {
                currentTitle,
                currentName,
                currentDate,
                services,
                numPages,
                numLanguages,
                total,
              },
              index
            ) => {
              return (
                <>
                  <ClientBudget // Crida al component ClientBudget i li envia la info amb la que ha d'emplenar el component i retornar-ho (fent el map)
                    key={index}
                    currentTitle={currentTitle}
                    currentName={currentName}
                    currentDate={currentDate}
                    services={services}
                    numPages={numPages}
                    numLanguages={numLanguages}
                    total={total}
                  />
                </>
              );
            }
          )}
      </div>
    </main>
  );
}

```

### ClientBudget
```jsx
import React from "react";
import { Budget } from "../style/styled";

export function ClientBudget({ // Creo el component ClientBudget amb totes les dades d'un pressupost (li venen per props)
  currentTitle,
  currentName,
  currentDate,
  services,
  numPages,
  numLanguages,
  total,
}) {
    const formattedDate = String(currentDate.toLocaleDateString("es-ES")); // Dono format a la data 

  return (
    <div>
      <Budget> {/* // Faig servir el component creat a styled */}
        <label> {/* {// Creo la "plantilla de com s'ha de veure i omplir (automaticament) un pressupost"} */}
          <h2>Títol: {currentTitle}</h2>
          <p>Nom: {currentName}</p>
          <p>Data: {formattedDate}</p>
          <p>Serveis: {services} </p>
          <p>
            Pàgines: {numPages}
            <br />
            Idiomes: {numLanguages}
          </p>
          <p>Preu final: {total} €</p>
        </label>
      </Budget>
    </div>
  );
}
```
### userBudget
```js
export class UserBudget {
  // Crea la classe (la fàbrica)
  // Defineix quins "atributs" ha de tenir l'objecte que es crei a la fàbirica/classe. Defineix la plantilla. (Ha de tenir un títol, un client...)
  currentTitle;
  currentName;
  currentDate;
  services;
  numPages;
  numLanguages;
  total;

  constructor(
    currentTitle,
    currentName,
    currentDate,
    services,
    numPages,
    numLanguages,
    total
  ) {
    // Dic quins valors ha de "demanar" per poder construir l'objecte
    this.currentTitle = currentTitle;
    this.currentName = currentName; // this fa referència a l'objecte concret que s'està creant en aquell moment. El CLIENT "d'aquest" OBJECTE ha de ser igual al CLIENT que t'han passat al cridar la funció.
    this.currentDate = currentDate;
    this.services = services;
    this.numPages = numPages;
    this.numLanguages = numLanguages;
    this.total = total;
  }
}

```
# EXERCICI 8 i 9
### Budget
```jsx
import { useEffect, useState } from "react";
import { data } from "../assets/data";
import { Checkbox } from "../components/Checkbox";
import { FormWeb } from "../components/FormWeb";
import { manageLocalStorage } from "../useLocalStorage";
import { ButtonStart } from "../style/styled";
import Swal from "sweetalert2";
import { UserBudget } from "../userBudget";
import { ClientBudget } from "../components/clientBudget";
import { Filters } from "../components/Filters";

const getFormattedPrice = (price) => `${price}€ `;

export default function Budget() {
  // ESTATS
  const [total, setTotal] = useState(0);
  const [checkboxPrice, setCheckboxPrice] = useState(0);
  const [numPages, setNumPages] = useState(0);
  const [numLanguages, setNumLanguages] = useState(0);
  const [budgetList, setBudgetList] = useState([]);
  const [filteredBudget, setFilteredBudget] = useState([]) // Faig un state de filteredBudget. Que serà l'array que es mostrarà per pantalla
  const [services, setServices] = useState([]);
  const [search, setSearch] = useState("");


  // ESTAT DESSELECCIONAT DE TOTS ELS ELEMENTS DE L'ARRAY
  const [checkedState, setCheckedState] = useState(
    new Array(data.length).fill(false)
  );

  // RECUPERAR L'ESTAT (LOCALSTORAGE)
  manageLocalStorage("total", total, setTotal);
  manageLocalStorage("checkboxPrice", checkboxPrice, setCheckboxPrice);
  manageLocalStorage("numPages", numPages, setNumPages);
  manageLocalStorage("numLanguages", numLanguages, setNumLanguages);
  manageLocalStorage("checkedState", checkedState, setCheckedState);
  manageLocalStorage("budgetList", budgetList, setBudgetList);
  manageLocalStorage("services", services, setServices);

  // EFFECTS
  useEffect(() => {
    calculateTotalPrice();
  }, [checkedState, numLanguages, numPages]);


  useEffect(() => { // Omplo FilteredBudget amb el contingut de budgetList (perquè budgetList no es sobreescrigui al filtrar)
  setFilteredBudget(budgetList)
  }, [budgetList]) // Això ha de passar cada cop que es modifiqui budgetList (quan s'inicia i quan s'afegeix un pressupost)
  


  // SELECCIONA I DESSELECCIONA CHECKBOX
  function onCheckboxSelected(i) {
    let nextCheckedState = [...checkedState];
    nextCheckedState[i] = !nextCheckedState[i];

    // CANVIA L'ESTAT DE LES PÀGINES I ELS IDIOMES (A 0 quan està desmarcat i a 1 quan està marcat el checkbox)
    if (nextCheckedState[0] === true) {
      setNumLanguages(1);
      setNumPages(1);
    }

    if (nextCheckedState[0] === false) {
      setNumLanguages(0);
      setNumPages(0);
    }
    // CANVIA L'ESTAT DE LA CHECKBOX
    setCheckedState(nextCheckedState);

    // SUMA EL PREU DELS PRODUCTES SELECCIONATS
    const sumPrice = nextCheckedState.reduce((acc, currentValue, index) => {
      if (currentValue === true) {
        return acc + data[index].price;
      }
      return acc;
    }, 0);

    // CANVIA L'ESTAT DE CHECKBOXPRICE
    setCheckboxPrice(sumPrice);
  }

  // CALCULA EL PREU DE LA WEB AMB DIF. PÀGINES I DIF. IDIOMES
  // CALCULA EL PREU TOTAL (PÀGINES + CHECKBOX) I CANVIA L'ESTAT
  function calculateTotalPrice() {
    const totalWeb = numPages * numLanguages * 30;
    const total = totalWeb + checkboxPrice;
    setTotal(total);
  }

  // ESBORRA EL FORMULARI
  function restartBudget() {
    setTotal(0),
      setCheckboxPrice(0),
      setCheckedState(new Array(data.length).fill(false)),
      setNumPages(0);
    setNumLanguages(0);
  }

  // DEMANA EL NOM I EL TÍTOL
  function showPopup() {
    Swal.fire({
      // DEMANAR NOM I TÍTOL
      html:
        "Introdueix les dades per guardar el presupost" +
        '<input id="swal-client" class="swal2-input" placeholder="Escriu el teu nom">' +
        '<input id="swal-title" class="swal2-input" placeholder="Títol del pressupost">',
      showCancelButton: true,
      cancelButtonText: "Cancelar",
      confirmButtonText: "Guardar",
      confirmButtonColor: "#86c8bc",
      preConfirm: () => {
        return [
          document.getElementById("swal-client").value,
          document.getElementById("swal-title").value,
        ];
      },

      // GUARDAR NOM I TÍTOL
      allowOutsideClick: () => !Swal.isLoading(),
    }).then((result) => {
      const currentTitle = result.value[1];
      const currentName = result.value[0];

      if (result.isConfirmed) {
        // MOSTRAR POPUP DE S'HA GUARDAT BÉ
        Swal.fire({
          text: "El teu pressupost s'ha guardat correctament",
          confirmButtonColor: "#86c8bc",
          showConfirmButton: false,
          icon: "success",
        });
      }
      // GUARDA LA DATA ACTUAL
      const today = new Date();
      const currentDate = today;

      // PASSA LA INFO OBTINGUDA A LA FUNCIÓ SAVEBUDGET
      saveBudget(currentTitle, currentName, currentDate);
    });
  }
  // GUARDAR EL PRESSUPOST (AMB EL NOM, TÍTOL I DATA)
  function saveBudget(currentTitle, currentName, currentDate) {
    const userBudget = new UserBudget(
      currentTitle,
      currentName,
      currentDate,
      services,
      numPages,
      numLanguages,
      total
    );
    localStorage.setItem("currentTitle", currentTitle);
    localStorage.setItem("currentName", currentName);
    localStorage.setItem(
      "currentDate",
      currentDate.toLocaleDateString("es-ES")
    );

    const newBudgetList = [...budgetList];
    newBudgetList.push(userBudget);
    setBudgetList(newBudgetList);
    restartBudget();
  }

  const servicesName = [];
  // CONVERTEIX L'ARRAY DE CHECKEDSTATE EN STRINGS (treient la info de DATA)
  useEffect(() => {
    checkedState.map((item, index) => {
      if (item === true) servicesName.push(data[index].option);
    });
    setServices(servicesName);
  }, [checkedState]);


// CERCADOR PER TÍTOL
const copyBudgetList = [...budgetList] // Clona la budgetList a una variable

console.log("budgetList", budgetList);
console.log("filteredBudget", filteredBudget);
console.log("search", search)

  return (
    <main>
      <h1>Què vols fer?</h1>
      <div>
        {data.map(({ option, price }, index) => {
          return (
            <>
              <Checkbox
                index={index}
                text={option}
                price={price}
                key={index}
                checked={checkedState[index]}
                onCheck={onCheckboxSelected}
                getFormattedPrice={getFormattedPrice}
              />
              {checkedState[0] && index === 0 && (
                // MOSTRA EL FORMULARI AL MARCAR LA PRIMERA CHECKBOX
                <FormWeb
                  numPages={numPages}
                  numLanguages={numLanguages}
                  setNumPages={setNumPages}
                  setNumLanguages={setNumLanguages}
                  key={index}
                />
              )}
            </>
          );
        })}
        <p>
          <b>Preu total: {getFormattedPrice(total)}</b>
        </p>
        <ButtonStart onClick={showPopup}>Guardar pressupost</ButtonStart>
      </div>
  
      {/* // FILTRAR ELS PRESSUPOSTOS */}
      <Filters // Li passo els props que necessitarà el component Filter (tant els valors com els setValue (funcions per modificar el valor))
        key={"buttonsFilter"} 
        budgetList={budgetList} 
        setBudgetList={setBudgetList}
        search={search}
        setSearch={setSearch}
        filteredBudget={filteredBudget}
        setFilteredBudget={setFilteredBudget}
        copyBudgetList={copyBudgetList}
        />

      {/* MOSTRAR PRESSUPOSTOS */}
      <div>
        {filteredBudget !== [] && // Li dic que faci el map de la filterBudget en comptes de la budgetList perquè mostri el que hi ha adins només (filtrat o no)
          filteredBudget.map(
            (
              {
                currentTitle,
                currentName,
                currentDate,
                services,
                numPages,
                numLanguages,
                total,
              },
              index
            ) => {
              return (
                <>
                  <ClientBudget
                    key={index}
                    currentTitle={currentTitle}
                    currentName={currentName}
                    currentDate={currentDate}
                    services={services}
                    numPages={numPages}
                    numLanguages={numLanguages}
                    total={total}
                  />
                </>
              );
            }
          )}
      </div>
    </main>
  );
}
```

### Filters
```jsx
import React from "react";
import { Button, Input } from "../style/styled";

// ORDERAR ELS PRESSUPOSTOS
export const Filters = ({
  setBudgetList,
  budgetList,
  search,
  setSearch,
  setFilteredBudget,
  copyBudgetList,
}) => {
  // Canvia l'estat del budgetList pel resultat de la funció que toqui
  const changeBudget = (change) => {
    setBudgetList(change);
  };

  const orderAlphabetically = () => { // Endreço alfabèticament amb un map agafant comparant els títols
    const orderByTitle = budgetList
      .map((e) => e)
      .sort((a, b) => {
        a.currentTitle - b.currentTitle;
        if (a.currentTitle > b.currentTitle) return 1;
        else return -1;
      });
    changeBudget(orderByTitle); // Crido a la funció de canvi d'estat amb el resultat objtingut del map
  };

  // Per ordre alfabètic
  const orderDate = () => { // El mateix que abans però amb la data
    const orderByDate = budgetList
      .map((e) => e)
      .sort((a, b) => {
        a.currentDate - b.currentDate;
        if (a.currentDate < b.currentDate) return 1;
        else return -1;
      });
    changeBudget(orderByDate);
  };

  // Restaurar l'ordre
  const reorder = () => { // El mateix que amb la data però a la inversa (perquè ja ho estava guardant posant els nous a sota)
    const restartOrder = budgetList
      .map((e) => e)
      .sort((a, b) => {
        a.currentDate - b.currentDate;
        if (a.currentDate > b.currentDate) return 1;
        else return -1;
      });
    changeBudget(restartOrder);
  };

  // Cercar per títol
  // CERCADOR PER TÍTOL
  const handleChangeSearch = (e) => { // Creo la funció que agafa el valor que l'user escrigui a l'input
    setSearch(e.target.value);

    const filterByTitle = copyBudgetList.filter((e) => { // Guardo a dins de filterByTitle un filter de copyBudgetList.
      if (e.currentTitle.toUpperCase().includes(search.toUpperCase())) { // Si el títol en majúscules coincideix amb algun dels títols (passat a majúscules dels budgets) retorna true
        return true;
      }
      return false;
    });
    setFilteredBudget(filterByTitle); // Canvio l'estat de filteredBudget pel resultat d'aquesta funció

    if (search === "" || search === " ") { // Si el contingut de l'input (search) està buit o té un espai, guarda a dins de filteredBudget la llista sencera (budgetList)
      setFilteredBudget(budgetList);
    }
  };

  return (
    <>
      
      <div>
        <Button onClick={orderAlphabetically}>Ordre alfabètic</Button>
        <Button onClick={orderDate}>Ordre cronològic</Button>
        <Button onClick={reorder}>Restaurar ordre</Button>


        <Input // Mostro l'input (creat en styled) i li passo el valor, el que ha de fer quan canvii, etc.
          value={search}
          onChange={handleChangeSearch}
          type="text"
          placeholder="Cerca un pressupost per títol"
        ></Input>
      </div>
    </>
  );
};
```