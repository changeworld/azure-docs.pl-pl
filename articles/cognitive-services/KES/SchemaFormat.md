---
title: Format schematu w interfejsie API usługi eksploracji wiedzy | Dokumentacja firmy Microsoft
description: Więcej informacji na temat formatu schematu w wiedzy eksploracji usługi (KES) interfejsu API w usługach kognitywnych.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 3009392a5acb12a8f4df3d30a2cbe5e74f2172fc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347101"
---
# <a name="schema-format"></a>Format schematu
Schemat jest określony w pliku JSON, który opisuje Struktura atrybutu obiektów w pliku danych używany do tworzenia indeksu.  Dla każdego atrybutu schematu Określa nazwę, typ danych, opcjonalny operacji i listy synonimów opcjonalne.  Obiekt może być równa 0 lub więcej wartości każdego z atrybutów.  Poniżej znajdują się uproszczony przykład z publikacji academic domeny:

``` json
{
  "attributes":[
    {"name":"Title", "type":"String"},
    {"name":"Year", "type":"Int32"},
    {"name":"Author", "type":"Composite"},
    {"name":"Author.Id", "type":"Int64", "operations":["equals"]},
    {"name":"Author.Name", "type":"String"},
    {"name":"Author.Affiliation", "type":"String"},
    {"name":"Keyword", "type":"String", "synonyms":"Keyword.syn"}
  ]
}
```

W nazwach atrybutów jest uwzględniana wielkość liter identyfikatorów, które zaczynać się literą i zawierać tylko litery (A-Z), cyfry (0-9) i znak podkreślenia (\_).  Atrybut zastrzeżone "logprob" służy do określania prawdopodobieństwa względną logarytm naturalny między obiektami.

## <a name="attribute-type"></a>Typ atrybutu
Poniżej przedstawiono listę atrybutów obsługiwane typy danych:

| Typ | Opis | Operacje | Przykład |
|------|-------------|------------|---------|
| Ciąg | Ciąg (1 – 1024 znaki) | równy, starts_with | "hello world" |
| Int32 | 32-bitowa liczba całkowita | równy, starts_with, is_between | 2016 |
| Int64 | 64-bitowa liczba całkowita | równy, starts_with, is_between | 9876543210 |
| podwójne | Wartość zmiennoprzecinkową podwójnej precyzji | równy, starts_with, is_between | 1.602e-19 |
| Date | Data (1400-01-01 do 9999-12-31) | równy, is_between | "2016-03-14" |
| Identyfikator GUID | Unikatowy identyfikator globalny | równa się | "602DD052-CC47-4B23-A16A-26B52D30C05B" |
| Obiekt blob | Wewnętrznie skompresowane dane nieindeksowanych | *Brak* | "Zwiększenie możliwości dostępnych dla każdej osoby, a każda organizacja na świecie osiągaj więcej" |
| Złożone | Kompozycja wiele atrybutów podrzędne| *Nie dotyczy* | {"Name": "harry shum", "Przynależności": "microsoft"} |

Atrybuty ciągu są używane do reprezentowania wartości ciągów, które mogą pojawić się jako część zapytania użytkownika.  Obsługują one dokładne dopasowanie *jest równe* operacji, jak również *starts_with* operacji scenariusze wykonania zapytania, takie jak Dopasowywanie "micros" za "microsoft".  Bez uwzględniania wielkości liter i rozmyte dopasowywanie do obsługi błędów pisowni będą obsługiwane w przyszłej wersji.

Atrybuty Int32/Int64/podwójny są używane do reprezentowania wartości liczbowych.  *Is_between* operacji włącza obsługę nierówności (lt, le, gt, ge), w czasie wykonywania.  *Starts_with* operacji obsługuje scenariusze wykonania zapytania, takie jak dopasowania "20" z "2016" lub "3". z "3,14".

Atrybuty daty są używane do kodowania wydajnie wartości daty.  *Is_between* operacji włącza obsługę nierówności (lt, le, gt, ge), w czasie wykonywania.
  
Identyfikator GUID atrybuty służą do wydajnie reprezentują wartości identyfikatora GUID z domyślną obsługę *jest równe* operacji.

Atrybuty obiektów blob są używane do efektywne kodowanie obiektów blob potencjalnie dużej ilości danych do środowiska wykonawczego wyszukiwania z odpowiedniego obiektu bez obsługi do żadnej operacji indeksowania na podstawie zawartości obiektu blob wartości.

### <a name="composite-attributes"></a>Atrybuty złożone
Atrybuty złożone są używane do reprezentowania grupowania wartości atrybutów.  Nazwa każdego atrybutu podrzędnego zaczyna się od nazwy atrybutu złożonego, a następnie ".".  Wartości atrybutów złożone są określane jako obiekt JSON zawierający wartości atrybutów zagnieżdżonych.  Złożone atrybutów może mieć wielu wartości obiektu.  Jednak atrybuty złożone nie mogą mieć podrzędne atrybutów, które same atrybuty złożonego.

W powyższym przykładzie publikację academic dzięki temu usługa kwerendy dokumentach "harry shum", gdy jest on w "microsoft".  Bez złożonego atrybutów usługi można tylko zapytania, dla dokumentów w przypadku, gdy autorów jest "harry shum" i jednym autorów jest "Microsoft".  Aby uzyskać więcej informacji, zobacz [złożonego zapytania](SemanticInterpretation.md#composite-function).

## <a name="attribute-operations"></a>Operacje na atrybutach
Domyślnie każdy atrybut został zaindeksowany do obsługi operacji wszystkie dostępne na typ danych atrybutu.  Określonej operacji nie jest wymagana, zestaw indeksowanej operacji można jawnie określić Aby zmniejszyć rozmiar indeksu.  W poniższy fragment kodu z powyższego schematu przykład atrybutu Author.Id został zaindeksowany do obsługi tylko *jest równe* operacji, ale nie dodatkowe *starts_with* i *is_between*  operacje dla atrybutów Int32.
```json
{"name":"Author.Id", "type":"Int32", "operations":["equals"]}
```

Gdy atrybut jest przywoływany wewnątrz gramatyki, *starts_with* operacji należy określić w schemacie w kolejności do wygenerowania zakończeń z częściowa zapytań usługi.  

## <a name="attribute-synonyms"></a>Atrybut synonimy
Często jest to pożądane odwoływanie się do wartość atrybutu określonego ciągu w synonim.  Na przykład użytkownicy mogą odwoływać się do "Microsoft" jako "MSFT" lub "MS".  W takich przypadkach definicja atrybutu można określić nazwę pliku schematu znajduje się w tym samym katalogu co plik schematu.  Każdy wiersz w pliku synonim reprezentuje wpisem synonim w następującym formacie JSON: `["<canonical>", "<synonym>"]`.  W schemacie przykład "AuthorName.syn" to plik JSON zawierający synonim wartości dla atrybutu Author.Name.

`{"name":"Author.Name", "type":"String", "synonyms":"AuthorName.syn"}`


Canonical wartość może mieć wielu synonimy.  Wiele wartości kanonicznej może udostępniać synonim wspólnej.  W takich przypadkach usługa rozpozna niejednoznaczności za pomocą wielu interpretacji.  Poniżej jest przykładowy plik synonimy AuthorName.syn odpowiadający powyższego schematu:
```json
["harry shum","heung-yeung shum"]
["harry shum","h shum"]
["henry shum","h shum"]
...
```
