---
title: Format gramatyki w interfejsie API usługi eksploracji wiedzy | Dokumentacja firmy Microsoft
description: Więcej informacji na temat formatu gramatyki w wiedzy eksploracji usługi (KES) interfejsu API w usługach kognitywnych.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 27202379b8c36696a380049336229cac040b0108
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347205"
---
# <a name="grammar-format"></a>Format gramatyki
Gramatyka jest plik XML, który określa zestaw ważoną języka naturalnego zapytań, które usługa może interpretować oraz jak te zapytania języka naturalnego przetłumaczyć wyrażenia zapytania semantycznego.  Składnia gramatyki jest oparta na [SRGS](http://www.w3.org/TR/speech-grammar/), standard W3C dla gramatyk rozpoznawania mowy, z rozszerzeniami do obsługi integracji indeks danych i funkcji semantycznego.

Poniżej opisano poszczególne elementy składni, które mogą być używane w gramatyce.  Zobacz [w tym przykładzie](#example) dla pełnej gramatyki, prezentującą użycie tych elementów w kontekście.

### <a name="grammar-element"></a>Gramatyka — Element 
`grammar` Element jest elementem najwyższego poziomu w specyfikacji gramatyki XML.  Wymagane `root` atrybut określa nazwę reguły głównego, która określa punkt początkowy gramatyki.

```xml
<grammar root="GetPapers">
```

### <a name="import-element"></a>Import — Element
`import` Element importuje definicji schematu z pliku zewnętrznego, aby włączyć atrybut odwołania. Element musi być elementem podrzędnym elementu najwyższego poziomu `grammar` element i wyświetlana przed jakimkolwiek `attrref` elementów. Wymagane `schema` atrybut określa nazwę pliku schematu znajduje się w tym samym katalogu co plik gramatyki XML. Wymagane `name` element Określa schemat alias, który kolejnych `attrref` elementy użyć podczas odwoływania się do atrybuty zdefiniowane w tym schemacie.

```xml
  <import schema="academic.schema" name="academic"/>
```

### <a name="rule-element"></a>Reguła elementu
`rule` Element definiuje reguła gramatyki, strukturalnych jednostki, która określa zestaw wyrażeń zapytania, które można zinterpretować systemu.  Element musi być elementem podrzędnym elementu najwyższego poziomu `grammar` elementu.  Wymagane `id` atrybut określa nazwę reguły, który jest wywoływany przez `grammar` lub `ruleref` elementów.

A `rule` element definiuje zestaw prawne rozszerzenia.  Tokeny tekst dopasowywania zapytanie wejściowe bezpośrednio.  `item` elementy Określ powtórzeń i zmień interpretacji prawdopodobieństwa.  `one-of` elementy wskazują opcje alternatywne.  `ruleref` elementy Włącz konstrukcji rozwinięcia bardziej złożone są prostsze podsieć.  `attrref` elementy Zezwalaj odpowiedników wartości atrybutów z indeksu.  `tag` elementy Określ semantykę interpretacji i można zmieniać prawdopodobieństwo interpretacji.

```xml
<rule id="GetPapers">...</rule>
```

### <a name="example-element"></a>przykład elementu
Opcjonalny `example` element określa przykład wyrażeń, które mogą być akceptowane przez zawierający `rule` definicji.  To może służyć do dokumentacji i/lub testowanie automatyczne.

```xml
<example>papers about machine learning by michael jordan</example>
```

### <a name="item-element"></a>element — Element
`item` Element grupy sekwencji konstrukcji gramatyki.  Może służyć wskazująca powtórzeń sekwencji rozszerzenia lub określić alternatywne w połączeniu z `one-of` elementu.

Gdy `item` element nie jest elementem podrzędnym `one-of` elementu, może określać powtórzenie sekwencji zamknięte przez przypisanie `repeat` atrybutu wartość licznika.  Wartość liczby "*n*" (gdzie *n* jest liczbą całkowitą) wskazuje, że sekwencja musi wystąpić dokładnie *n* razy.  Wartość liczby "*m*-*n*" umożliwia sekwencji pomiędzy *m* i *n* razy włącznie.  Wartość liczby "*m*-" Określa, że sekwencja musi występować co najmniej *m* razy.  Opcjonalny `repeat-logprob` atrybut może służyć do zmiany prawdopodobieństwa interpretacji powtórzeniami dodatkowe poza minimum.

```xml
<item repeat="1-" repeat-logprob="-10">...</item>
```

Gdy `item` elementy są wyświetlane jako elementy podrzędne `one-of` elementu definiują zestaw rozszerzeń alternatyw.  To użycie opcjonalny `logprob` atrybut określa prawdopodobieństwo względną dziennika między różne opcje.  Podane prawdopodobieństwo *p* od 0 do 1, można obliczyć odpowiedniego prawdopodobieństwo dziennika logowania (*p*), gdzie jest log() funkcja logarytm naturalny.  Jeśli nie zostanie określony, `logprob` wartość domyślna to 0, która nie zmienia prawdopodobieństwo interpretacji.  Należy pamiętać, że prawdopodobieństwo dziennika zawsze zmiennoprzecinkowe wartość ujemną lub 0.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="one-of-element"></a>jeden-elementu
`one-of` Element Określa alternatywną rozszerzenia między jedną z podrzędnych `item` elementów.  Tylko `item` elementy mogą wystąpić wewnątrz `one-of` elementu.  Względne prawdopodobieństwa spośród różnych opcji można określić za pomocą `logprob` atrybutu w każdego elementu podrzędnego `item`.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="ruleref-element"></a>Odwołanie ruleref — Element
`ruleref` Element określa nieprawidłowy rozszerzenia za pomocą odwołań do innego `rule` elementu.  Za pośrednictwem `ruleref` elementów, bardziej złożonych wyrażeń mogą być wbudowane w regułach prostsze.  Wymagane `uri` atrybut wskazuje nazwę przywoływana `rule` przy użyciu składni "#*rulename*".  Aby przechwycić semantycznego dane wyjściowe reguły z odwołaniami, Użyj opcjonalnego `name` atrybutu, aby określić nazwę zmiennej, do której przypisano semantycznego danych wyjściowych.
 
```xml
<ruleref uri="#GetPaperYear" name="year"/>
```

### <a name="attrref-element"></a>attrref — Element
`attrref` Element odwołuje się do atrybutu indeksu, umożliwiając porównanie z wartości atrybutów obserwowanych w indeksie.  Wymagane `uri` atrybut określa nazwę schematu indeksu i nazwa atrybutu przy użyciu składni "*NazwaSchematu*#*attrName*".  Musi być poprzedzających `import` element, który importuje schematu o nazwie *NazwaSchematu*.  Nazwa atrybutu jest nazwa atrybutu zdefiniowana w schemacie odpowiednie.

Oprócz dopasowania danych wejściowych użytkownika, `attrref` element zwraca również wartość obiektu structured query jako dane wyjściowe wybierające podzbioru obiektów w indeksie dopasowania wartości wejściowej.  Użyj opcjonalnego `name` atrybutu, aby określić nazwę zmiennej, na którym powinny być przechowywane dane wyjściowe obiektu zapytania.  Obiekt kwerendy może składać się z innymi obiektami zapytania, aby utworzyć więcej złożonych wyrażeń.  Zobacz [interpretacji semantyki](SemanticInterpretation.md) szczegółowe informacje.  

```xml
<attrref uri="academic#Keyword" name="keyword"/>
```

#### <a name="query-completion"></a>Ukończenie zapytań 
Do obsługi zapytań zakończeń przy interpretowaniu kwerend częściowe użytkownika, każdy atrybut przywoływanego musi zawierać "starts_with" jako operacji w definicji schematu.  Podany prefiks zapytania użytkownika, `attrref` pasuje do wszystkich wartości w indeksie kończące prefiks i uzyskanie wartości pełną jako osobne interpretacja gramatyki.  

Przykłady:
* Dopasowywanie `<attrref uri="academic#Keyword" name="keyword"/>` przed zapytanie prefiksu "dat" generuje interpretacja dla dokumentów o "baza danych", interpretacja dla dokumentów o "wyszukiwania danych" itp.
* Dopasowywanie `<attrref uri="academic#Year" name="year"/>` przed zapytanie prefiksu "200" generuje interpretacja dla dokumentów "2000" interpretacja dokumentów "2001" itp.

#### <a name="matching-operations"></a>Dopasowywanie operacji
Oprócz dokładnego dopasowania, wybierz typy atrybutów również prefiks pomocy technicznej i nierówności odpowiada za pomocą opcjonalnego `op` atrybutu.  Jeśli żaden obiekt w indeksie ma nieprawidłową wartość odpowiada, ścieżka gramatyki jest zablokowana i usługa nie będzie już generował żadnych interpretacje Przechodzenie za pośrednictwem tej ścieżki gramatyki.   `op` Atrybutu ustawienia domyślne "eq".

```xml
in <attrref uri="academic#Year" name="year"/>
before <attrref uri="academic#Year" op="lt" name="year"/
```

W poniższej tabeli wymieniono obsługiwane `op` wartości dla każdego typu atrybutu.  Ich użycie wymaga odpowiedniej operacji indeksu do uwzględnienia w definicji atrybutu schematu.

| Typ atrybutu | Wartość OP | Opis | Operacja indeksu
|----|----|----|----|
| Ciąg | eq | Dokładne dopasowanie ciągu | równa się |
| Ciąg | starts_with | Dopasowanie prefiksu ciągu | starts_with |
| Int32, Int64, o podwójnej precyzji | eq |  Dopasowanie dokładne numeryczne | równa się |
| Int32, Int64, o podwójnej precyzji | lt, le, gt, ge | Dopasowanie liczbowych nierówności (<, < =, >, > =) | is_between |
| Int32, Int64, o podwójnej precyzji | starts_with | Dopasowanie prefiksu wartości w notacji dziesiętnej | starts_with |

Przykłady:
* `<attrref uri="academic#Year" op="lt" name="year"/>` Dopasowuje ciąg wejściowy "2000" i zwraca wszystkie dokumenty opublikowane przed roku 2000, wyłącznie.
* `<attrref uri="academic#Year" op="lt" name="year"/>` Ciąg wejściowy "20" nie jest zgodny, ponieważ indeks opublikowane przed rokiem 20 nie zawiera żadnych dokumentów.
* `<attrref uri="academic#Keyword" op="starts_with" name="keyword"/>` Ciąg wejściowy "dat" i zwraca dokumenty pojedynczego interpretacji o "baza danych", "wyszukiwania danych" itp.  Jest to przypadek użycia rzadko.
* `<attrref uri="academic#Year" op="starts_with" name="year"/>` Dopasowuje ciąg wejściowy "20" i zwraca w dokumenty pojedynczego interpretacji publikowane w 200 299, 2000 2999, itp.  Jest to przypadek użycia rzadko.

### <a name="tag-element"></a>Tag — Element
`tag` Element określa, jak ścieżka do gramatyki jest interpretowane.  Zawiera ona sekwencji instrukcji zakończona średnikiem.  Instrukcja może być przypisanie literałem lub zmienną do innej zmiennej.  On również przypisać dane wyjściowe funkcji 0 lub więcej parametrów do zmiennej.  Każdy parametr funkcji można określić za pomocą literałem lub zmienną.  Jeśli funkcja nie zwraca żadnych danych wyjściowych, przypisanie zostanie pominięty.  Zakres zmiennej jest lokalny dla zawierającej regule.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Każdy `rule` w gramatyce ma uprzednio zdefiniowanej zmiennej o nazwie "out" reprezentujący produkt wyjściowy semantycznego reguły.  Wartość jest obliczana przez ocenę każdego semantycznego instrukcje przechodzić przez ścieżkę za pośrednictwem `rule` dopasowanie użytkownika zapytanie danych wejściowych.  Wartość przypisaną do zmiennej "out" na końcu oceny jest semantycznego dane wyjściowe reguły.  Dane wyjściowe semantycznego interpretowanie użytkownika zapytanie gramatyki jest semantycznego danych wyjściowych reguł głównego.

Niektóre instrukcje mogą zmienić prawdopodobieństwo ścieżki interpretacji dzięki zastosowaniu przesunięcia prawdopodobieństwo dodatku dziennika.  Niektóre instrukcje może odrzucić interpretacji całkowicie, jeśli określone warunki nie są spełnione.

Aby uzyskać listę obsługiwanych funkcji semantyczne, zobacz [semantycznego funkcji](SemanticInterpretation.md#semantic-functions).

## <a name="interpretation-probability"></a>Interpretacja prawdopodobieństwo
Prawdopodobieństwo ścieżka interpretacji przez gramatyki jest prawdopodobieństwo zbiorczą dziennika wszystkich `<item>` elementy i funkcje semantycznego napotkano wzdłuż sposób.  Opisuje względną prawdopodobieństwo dopasowania określonej sekwencji wejściowych.

Podane prawdopodobieństwo *p* od 0 do 1, można obliczyć odpowiedniego prawdopodobieństwo dziennika logowania (*p*), gdzie log() jest funkcja logarytm naturalny.  Za pomocą dziennika prawdopodobieństwa umożliwi systemowi gromadzone prawdopodobieństwo wspólne ścieżki interpretacji przez dodanie proste.  Unika się również niedomiar wspólne dla tych obliczeń prawdopodobieństwa wspólnych.  Należy pamiętać, że zgodnie z projektem prawdopodobieństwo dziennika zawsze zmiennoprzecinkowe wartość ujemną lub 0, gdy większe wartości wskazują prawdopodobieństwo wyższy.

<a name="example"></a>
## <a name="example"></a>Przykład
Poniżej przedstawiono przykład XML z domeny academic publikacji, który demonstruje różnych elementów gramatyki:

```xml
<grammar root="GetPapers">

  <!-- Import academic data schema-->
  <import schema="academic.schema" name="academic"/>
  
  <!-- Define root rule-->
  <rule id="GetPapers">
    <example>papers about machine learning by michael jordan</example>
    
    papers
    <tag>
      yearOnce = false;
      isBeyondEndOfQuery = false;
      query = All();
    </tag>
  
    <item repeat="1-" repeat-logprob="-10">
      <!-- Do not complete additional attributes beyond end of query -->
      <tag>AssertEquals(isBeyondEndOfQuery, false);</tag>
        
      <one-of>
        <!-- about <keyword> -->
        <item logprob="-0.5">
          about <attrref uri="academic#Keyword" name="keyword"/>
          <tag>query = And(query, keyword);</tag>
        </item>
        
        <!-- by <authorName> [while at <authorAffiliation>] -->
        <item logprob="-1">
          by <attrref uri="academic#Author.Name" name="authorName"/>
          <tag>authorQuery = authorName;</tag>
          <item repeat="0-1" repeat-logprob="-1.5">
            while at <attrref uri="academic#Author.Affiliation" name="authorAffiliation"/>
            <tag>authorQuery = And(authorQuery, authorAffiliation);</tag>
          </item>
          <tag>
            authorQuery = Composite(authorQuery);
            query = And(query, authorQuery);
          </tag>
        </item>
        
        <!-- written (in|before|after) <year> -->
        <item logprob="-1.5">
          <!-- Allow this grammar path to be traversed only once -->
          <tag>
            AssertEquals(yearOnce, false);
            yearOnce = true;
          </tag>
          <ruleref uri="#GetPaperYear" name="year"/>
          <tag>query = And(query, year);</tag>
        </item>
      </one-of>

      <!-- Determine if current parse position is beyond end of query -->
      <tag>isBeyondEndOfQuery = GetVariable("IsBeyondEndOfQuery", "system");</tag>
    </item>
    <tag>out = query;</tag>
  </rule>
  
  <rule id="GetPaperYear">
    <tag>year = All();</tag>
    written
    <one-of>
      <item>
        in <attrref uri="academic#Year" name="year"/>
      </item>
      <item>
        before
        <one-of>
          <item>[year]</item>
          <item><attrref uri="academic#Year" op="lt" name="year"/></item>
        </one-of>
      </item>
      <item>
        after
        <one-of>
          <item>[year]</item>
          <item><attrref uri="academic#Year" op="gt" name="year"/></item>
        </one-of>
      </item>
    </one-of>
    <tag>out = year;</tag>
  </rule>
</grammar>
```
