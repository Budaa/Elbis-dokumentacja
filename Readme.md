# Dokumentacja

## Instalacja skryptu:

Poniższy skrypt proszę wkleić do pliku `formularz.php` pomiędzy tagami `<head></head>`

```javascript
<script type="text/javascript">
    var bruttoHTMLNode;
    var nettoHTMLNode;
    window.textPrzedCenaNetto = 'Cena Netto:';
    window.textPrzedCenaBrutto = 'Cena Brutto:';
      document.addEventListener('DOMContentLoaded', function() {
        var ID_ELEMENTU_WYSWIETLAJACEGO_AKTUALNA_WARTOSC_ZAMOWNIEA_NETTO =
          'wartosc-zamowienia-netto';
        var ID_ELEMENTU_WYSWIETLAJACEGO_AKTUALNA_WARTOSC_ZAMOWNIEA_BRUTTO =
          'wartosc-zamowienia-brutto';
        // HTML NODEs
        bruttoHTMLNode = document.getElementById(
          ID_ELEMENTU_WYSWIETLAJACEGO_AKTUALNA_WARTOSC_ZAMOWNIEA_BRUTTO
        );
        nettoHTMLNode = document.getElementById(
          ID_ELEMENTU_WYSWIETLAJACEGO_AKTUALNA_WARTOSC_ZAMOWNIEA_NETTO
        );
        bruttoHTMLNode.innerHTML = window.textPrzedCenaBrutto;
        nettoHTMLNode.innerHTML = window.textPrzedCenaNetto;

      });
        var totalBrutto;
        var totalNetto;
        // Konfiguracja
        var ID_FORMULARZA = 'formularz-zamowienia';

        var WALUTA = 'zl';
        // Jezli produkt nie ma podaej stawki VAT, ponizsza zostanie doliczona.
        var PODSTAWOWA_STAWKA_VAT = '23';


        function roundToSecondDecimal(value) {
          return Math.round(value * 100) / 100;
        }

        function calculatePriceWithVat(price, vat) {
          return Number(price) * (Number(vat) / 100) + Number(price);
        }

        function updatePrices() {
          var totalBrutto = 0;
          var totalNetto = 0;
          var form = document.getElementById(ID_FORMULARZA) || [];
          var elements = form.elements;
          var selectedInputs = [];
          for (var i = 0; i < elements.length; i++) {
            var isTextInput =
              elements[i].localName === 'input' && elements[i].type === 'text';
            var hasValue = !!elements[i].value;

            if (isTextInput && hasValue) {
              selectedInputs.push(elements[i]);
            }
          }

          selectedInputs.forEach(function(input) {
            var amount = input.value && Number(input.value);
            var price = input.attributes.cena && input.attributes.cena.value;
            var vat =
              (input.attributes.vat && input.attributes.vat.value) ||
              PODSTAWOWA_STAWKA_VAT;

            if (price) {
              totalBrutto += (calculatePriceWithVat(price, vat) * amount);
              totalNetto += (Number(price) * Number(amount));
            }
          });
          /*
            Te wartosci odpowiedzialne sa za wysietlanie tekstu znajdujacego sie w polach
            ID_ELEMENTU_WYSWIETLAJACEGO_AKTUALNA_WARTOSC_ZAMOWNIEA_NETTO
            i
            ID_ELEMENTU_WYSWIETLAJACEGO_AKTUALNA_WARTOSC_ZAMOWNIEA_BRUTTO
          */
          bruttoHTMLNode.innerHTML = `${window.textPrzedCenaBrutto} ${totalBrutto.toFixed(2)} ${WALUTA}`;
          nettoHTMLNode.innerHTML = `${window.textPrzedCenaNetto} ${totalNetto.toFixed(2)} ${WALUTA}`;
        }
    </script>
```

## ID tagu Form

Zeby nasz skypt dzialal musimy ustalic ID naszego formularza. 
Do tagu `<form>` nalezy dopisac id
```html
<form id="formularz-zamowienia" .../>

```

---

## Wyświetlanie ceny:

Skrypt wyświetli podliczona wartość zamówienia w elementach HTML z wartością id '"wartosc-zamowienia-netto"' oraz '"wartosc-zamowienia-netto"';

### Przykład:

Wartość netto zamówienia zostanie wstrzyknięta pomiędzy tagi `<span>`.

```html
<p id="wartosc-zamowienia-netto"></p>
<p id="wartosc-zamowienia-brutton"></p>
```

### Uwaga!

> Skrypt podmieni wszystko co znajduje sie w Tagu z ID 'wartosc-zamowienia-netto' i 'wartosc-zamowienia-brutto'. Aby zmienić tekst wyświetlany przed ceną, należy dodać go poza tag z tymi ID albo edytować zmienne 'bruttoHTMLNode.innerHTML' lub 'nettoHTMLNode.innerHTML', znajdujące się w skrypcie.

---

## Dodawanie funkcji podliczania do pola input:

Aby dane pole 'input' podliczało wpisaną wartość, należy dodać do niego poniższe wartości:

```html
<input
  class="formularz"
  type="text"
  name="wk�adki�FROTTE�BAMBUS�KOKOS�COCCINE�pc37�37_38���ilo��p."
  cena="5.22"
  vat="12"
  onchange="updatePrices()"
/>
```

| Argument |                                Rola                                | Wymagane |
| -------- | :----------------------------------------------------------------: | :------: |
| cena     |                        cena produktu Netto                         |   Tak    |
| vat      |                         Vat (domyślnie 23)                         |   Nie    |
| onchange | funkcja wywolywana, kiedy użytkownik zmieni wartość elementu input |   Tak    |
