---
title: Format gramatyki — interfejs API usługi Eksploracja wiedzy
titlesuffix: Azure Cognitive Services
description: Więcej informacji na temat format gramatyki w wiedzy Exploration Service (KES) interfejsu API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 844bd9a88c52fd398fc66c71e59da513c0d7d90d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814861"
---
# <a name="grammar-format"></a>Format gramatyki

Gramatyka jest plik XML, który określa zestaw ważona zapytań w języku naturalnym, których usługa może interpretować, a także jak tych zapytań w języku naturalnym są tłumaczone na wyrażeniach zapytań semantycznych.  Składnia gramatyki opiera się na [SRGS](https://www.w3.org/TR/speech-grammar/), to standard W3C gramatyki rozpoznawania mowy, za pomocą rozszerzeń do obsługi integracji indeksu danych i funkcji semantycznego.

Poniżej opisano poszczególne elementy składni, które mogą być używane w gramatyki.  Zobacz [w tym przykładzie](#example) dla pełną gramatyki, który demonstruje użycie tych elementów w kontekście.

### <a name="grammar-element"></a>Gramatyka — Element

`grammar` Element jest elementem najwyższego poziomu w gramatyce specyfikacji XML.  Wymagane `root` atrybut określa nazwę reguły głównej, która określa punkt początkowy gramatyki.

```xml
<grammar root="GetPapers">
```

### <a name="import-element"></a>Import — Element

`import` Element importuje definicję schematu z zewnętrznego pliku, aby włączyć atrybut odwołania. Element musi być obiektem podrzędnym obiektu najwyższego poziomu `grammar` elementu i pojawiają się przed każdą `attrref` elementów. Wymagane `schema` atrybut określa nazwę pliku schematu znajduje się w tym samym katalogu co plik XML gramatyki. Wymagane `name` element Określa schemat alias oznacza kolejne `attrref` elementy używać podczas odwoływania się do atrybutów zdefiniowanych w tym schemacie.

```xml
  <import schema="academic.schema" name="academic"/>
```

### <a name="rule-element"></a>Reguła elementu

`rule` Element definiuje reguły gramatyki, strukturalnych jednostki, która określa zestaw wyrażeń zapytania, które może interpretować systemu.  Element musi być obiektem podrzędnym obiektu najwyższego poziomu `grammar` elementu.  Wymagane `id` atrybut określa nazwę reguły, które jest przywoływane z `grammar` lub `ruleref` elementów.

A `rule` element definiuje zestaw rozszerzeń prawnych.  Tokeny tekstu dopasowywania zapytanie wejściowe bezpośrednio.  `item` elementy Określ powtórzeń i zmienia interpretację prawdopodobieństwa.  `one-of` elementy wskazują opcje alternatywne.  `ruleref` elementy Włącz konstruowania bardziej złożonych rozszerzenia z tymi prostsze.  `attrref` elementy umożliwiają dopasowań w odniesieniu do wartości atrybutu z indeksu.  `tag` elementy Określ semantykę interpretacji i może zmienić prawdopodobieństwo interpretacji.

```xml
<rule id="GetPapers">...</rule>
```

### <a name="example-element"></a>przykład elementu

Opcjonalny `example` element określa przykład fraz, które mogą być akceptowane przez zawierający `rule` definicji.  To może służyć do dokumentacji i/lub testowania automatycznego.

```xml
<example>papers about machine learning by michael jordan</example>
```

### <a name="item-element"></a>element — Element

`item` Element grupy sekwencji konstrukcji gramatyki.  Może służyć do wskazania powtórzeń sekwencji rozszerzenia lub określić alternatywne w połączeniu z `one-of` elementu.

Podczas `item` element nie jest elementem podrzędnym `one-of` elementu, można określić powtórzenie sekwencji ujęty, przypisując `repeat` atrybutu wartość licznika.  Wartość liczby "*n*" (gdzie *n* jest liczbą całkowitą) wskazuje, że sekwencja musi wystąpić dokładnie *n* razy.  Wartość liczby "*m*-*n*" umożliwia sekwencji występować między *m* i *n* razy (włącznie).  Wartość liczby "*m*—" Określa, że sekwencja musi występować przynajmniej *m* razy.  Opcjonalny `repeat-logprob` atrybut może służyć do zmiany interpretacji prawdopodobieństwo powtórzeniami dodatkowe poza minimum.

```xml
<item repeat="1-" repeat-logprob="-10">...</item>
```

Gdy `item` elementy są wyświetlane jako elementy podrzędne `one-of` elementu, definiują zestaw rozszerzeń alternatyw.  To wykorzystania opcjonalnego `logprob` atrybut określa prawdopodobieństwo względne dziennika między różnymi możliwościami.  Biorąc pod uwagę prawdopodobieństwo *p* od 0 do 1 odpowiedniego prawdopodobieństwo, że dziennik może zostać obliczony jako dziennik (*p*), gdzie log() jest funkcja logarytmu naturalnego.  Jeśli nie zostanie określony, `logprob` wartość domyślna to 0, która nie zmienia prawdopodobieństwo interpretacji.  Należy pamiętać, że prawdopodobieństwo dziennika zawsze ujemną wartość zmiennoprzecinkowa lub 0.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="one-of-element"></a>jeden — element

`one-of` Element Określa alternatywną rozszerzenia wśród jednego elementu podrzędnego `item` elementów.  Tylko `item` elementów może występować wewnątrz `one-of` elementu.  Względna prawdopodobieństwa wśród różnych opcji można określić za pomocą `logprob` atrybutu w każdego elementu podrzędnego `item`.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="ruleref-element"></a>ruleref Element

`ruleref` Element określa prawidłowe rozszerzenia za pomocą odwołania do innego `rule` elementu.  Za pośrednictwem `ruleref` elementów, mogą być wbudowane bardziej złożonych wyrażeń z reguł prostsze.  Wymagane `uri` atrybut wskazuje nazwę występujących w odwołaniu `rule` przy użyciu składni "#*rulename*".  Aby przechwycić semantycznego dane wyjściowe reguły z odwołaniami, Użyj opcjonalnego `name` atrybutu, aby określić nazwę zmiennej, do której przypisany jest semantycznego danych wyjściowych.
 
```xml
<ruleref uri="#GetPaperYear" name="year"/>
```

### <a name="attrref-element"></a>attrref — Element

`attrref` Element odwołuje się do atrybutu indeksu, umożliwiając dopasowywania w odniesieniu do wartości atrybutu zaobserwowane w indeksie.  Wymagane `uri` atrybut określa nazwę schematu indeksu i nazwy atrybutu przy użyciu składni "*schemaName*#*attrName*".  Musi być pewnym `import` element, który Importuje schemat o nazwie *schemaName*.  Nazwa atrybutu jest nazwa atrybutu zdefiniowanej w schemacie odpowiednie.

Oprócz dopasowania danych wejściowych użytkownika, `attrref` element zwraca również wartość obiektu zapytań ze strukturą jako dane wyjściowe, który wybierze podzbiór obiektów w indeks pasujących wartości wejściowej.  Użyj opcjonalnego `name` atrybutu, aby określić nazwę zmiennej, w którym powinny być przechowywane dane wyjściowe obiektu zapytania.  Obiekt zapytania może składać się z innymi obiektami zapytanie w celu utworzenia więcej złożonych wyrażeń.  Zobacz [interpretacja semantyczna](SemanticInterpretation.md) Aby uzyskać szczegółowe informacje.  

```xml
<attrref uri="academic#Keyword" name="keyword"/>
```

#### <a name="query-completion"></a>Ukończenie zapytań

Aby zapewnić obsługę uzupełnienia zapytań przy interpretowaniu zapytania częściowego użytkowników, każdy atrybut odwołania musi zawierać "starts_with" jako operacji w definicji schematu.  Podany prefiks zapytania użytkownika, `attrref` pasuje do wszystkich wartości w indeksie, kończące prefiks i uzyskanie każdej wartości ukończone jako osobne interpretacji gramatyki.  

Przykłady:
* Dopasowywanie `<attrref uri="academic#Keyword" name="keyword"/>` względem zapytania prefiksu "dat" generuje interpretacja dla dokumentów dotyczących "baza danych", interpretacja dla dokumentów o "wyszukiwania danych" itp.
* Dopasowywanie `<attrref uri="academic#Year" name="year"/>` względem zapytania prefiksu "200" generuje interpretacja dla dokumentów w "2000" interpretacja dokumentów "2001" itp.

#### <a name="matching-operations"></a>Pasujących operacji

Oprócz dokładnego dopasowania, wybierz typy atrybutów również prefiks pomocy technicznej i nierówności odpowiada za pomocą opcjonalnego `op` atrybutu.  Jeśli żaden obiekt w indeksie ma wartość, która jest zgodna, powoduje to zablokowanie ścieżki gramatyki i usługa nie zostanie wygenerowany wszelkie interpretacji przechodzenie przez tę ścieżkę gramatyki.   `op` Atrybutu wartości domyślnych "eq".

```xml
in <attrref uri="academic#Year" name="year"/>
before <attrref uri="academic#Year" op="lt" name="year"/
```

W poniższej tabeli wymieniono obsługiwane `op` wartości dla każdego typu atrybutu.  Ich użycie wymaga odpowiednia operacja indeksu mają zostać uwzględnione w definicji atrybutów schematu.

| Typ atrybutu | Wartość operacji | Opis | Operacja indeksu
|----|----|----|----|
| String | eq | Dokładne dopasowanie ciągu | równa się |
| String | starts_with | Dopasowanie prefiksu w ciągu | starts_with |
| Podwójne typu Int32, Int64, | eq |  Dopasowanie dokładne liczbowe | równa się |
| Podwójne typu Int32, Int64, | lt, le, gt, ge | Dopasowanie liczbowych nierówności (<, < =, >, > =) | is_between |
| Podwójne typu Int32, Int64, | starts_with | Dopasowanie prefiksu wartości w notacji dziesiętnej | starts_with |

Przykłady:
* `<attrref uri="academic#Year" op="lt" name="year"/>` pasuje do ciągu wejściowego "2000" i zwraca wszystkie dokumenty opublikowane przed roku 2000, wyłącznie.
* `<attrref uri="academic#Year" op="lt" name="year"/>` nie pasuje do ciągu wejściowego "20", ponieważ istnieją nie dokumenty w indeksie opublikowane przed rokiem 20.
* `<attrref uri="academic#Keyword" op="starts_with" name="keyword"/>` pasuje do ciągu wejściowego "dat" i zwraca dokumenty pojedynczego interpretacji, o "baza danych", "wyszukiwania danych" itp.  Jest to przypadek użycia rzadkie.
* `<attrref uri="academic#Year" op="starts_with" name="year"/>` Dopasowuje ciąg wejściowy "20" i zwraca w dokumenty pojedynczego interpretacji publikowane w 200 299, 2000 2999, itp.  Jest to przypadek użycia rzadkie.

### <a name="tag-element"></a>tag elementu

`tag` Element określa, jak ścieżka za pośrednictwem gramatyki ma być interpretowany.  Zawiera ona sekwencja zakończona średnikiem instrukcji.  Instrukcja może być przypisanie literałem lub zmienną do innej zmiennej.  On również przypisać dane wyjściowe funkcji z atrybutem 0 lub większą liczbę parametrów do zmiennej.  Każdy parametr funkcji może być określona za pomocą literałem lub zmienną.  Jeśli funkcja nie zwraca żadnych danych wyjściowych, przypisanie zostanie pominięty.  Zakres zmiennej jest lokalny zawierającej regule.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Każdy `rule` w gramatyce ma uprzednio zdefiniowanej zmiennej o nazwie "out" reprezentujący produkt wyjściowy semantycznego reguły.  Jego wartość jest obliczana w wyniku obliczenia każdej instrukcji semantycznego przesunięta przy użyciu ścieżki za pośrednictwem `rule` dopasowanie użytkownika zapytania danych wejściowych.  Wartość przypisana do zmiennej "out" na końcu oceny jest semantycznego dane wyjściowe reguły.  Interpretowanie kwerenda użytkownika na gramatyki semantycznego danych wyjściowych znajduje się semantycznego dane wyjściowe reguły głównego.

Niektóre instrukcje mogą zmienić prawdopodobieństwo, że ścieżka interpretacji, wprowadzając przesunięcie prawdopodobieństwo dziennika dodatku.  Niektóre instrukcje może odrzucić interpretacji całkowicie, jeśli określone warunki nie są spełnione.

Aby uzyskać listę obsługiwanych funkcji semantyczne, zobacz [semantycznego funkcji](SemanticInterpretation.md#semantic-functions).

## <a name="interpretation-probability"></a>Prawdopodobieństwo interpretacja

Prawdopodobieństwo ścieżką interpretacji za pośrednictwem gramatyki jest zbiorcza dziennika prawdopodobieństwo, że wszystkie `<item>` elementy i funkcje semantycznego napotkano po drodze.  Opisano w nim względnego prawdopodobieństwa wystąpienia określonej sekwencji wejściowych do dopasowania.

Biorąc pod uwagę prawdopodobieństwo *p* od 0 do 1 odpowiedniego prawdopodobieństwo, że dziennik może zostać obliczony jako dziennik (*p*), gdzie log() jest funkcja logarytmu naturalnego.  Za pomocą dziennika prawdopodobieństwa umożliwia systemowi accumulate prawdopodobieństwo wspólnego drogę interpretacji przez proste dodanie.  Unika również zmiennoprzecinkowych niedopełnienie wspólne dla takich obliczeń prawdopodobieństwa wspólnych.  Należy pamiętać, że zgodnie z projektem, prawdopodobieństwo, że dziennik ma zawsze wartość ujemną wartość zmiennoprzecinkowa lub 0, gdzie większe wartości wskazują prawdopodobieństwo.

## <a name="example"></a>Przykład

Poniżej przedstawiono przykładowy kod XML z domeny publikacji akademickich, który demonstruje różne elementy gramatyki:

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
