---
title: Format schematu — interfejs API usługi Eksploracja wiedzy
titlesuffix: Azure Cognitive Services
description: Więcej informacji na temat format schematu w wiedzy Exploration Service (KES) interfejsu API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 51a812762659bcc67762b82e9c120772065aab53
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814417"
---
# <a name="schema-format"></a>Format schematu

Schemat jest określona w pliku JSON, który opisuje strukturę atrybutów obiektów w pliku danych używanego do utworzenia indeksu.  Dla każdego atrybutu schemat określa nazwę, typ danych, opcjonalny operacji i listy opcjonalnych synonimów.  0 lub więcej wartości każdego z atrybutów, może być obiekt.  Poniżej przedstawiono uproszczony przykład z domeny akademickich publikacji:

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

W nazwach atrybutów jest rozróżniana wielkość liter identyfikatorów, które zaczynają się literą i zawierać tylko litery (A – Z), cyfry (0 – 9) i podkreślenie (\_).  Atrybut zarezerwowanych "logprob" jest używany do określenia prawdopodobieństw względne logarytmu naturalnego między obiektami.

## <a name="attribute-type"></a>Typ atrybutu

Poniżej przedstawiono listę typów danych obsługiwanych atrybutu:

| Typ | Opis | Operacje | Przykład |
|------|-------------|------------|---------|
| `String` | Ciąg (1 – 1024 znaki) | Equals, starts_with | "hello world" |
| `Int32` | 32-bitowa liczba całkowita ze znakiem | równa się, starts_with, is_between | 2016 |
| `Int64` | 64-bitowa liczba całkowita ze znakiem | równa się, starts_with, is_between | 9876543210 |
| `Double` | Wartość zmiennoprzecinkowa o podwójnej precyzji | równa się, starts_with, is_between | 1.602e-19 |
| `Date` | Data (1400-01-01 do 9999-12-31) | Equals, is_between | '2016-03-14' |
| `Guid` | Unikatowy identyfikator globalny | równa się | "602DD052-CC47-4B23-A16A-26B52D30C05B" |
| `Blob` | Wewnętrznie skompresowanych danych nieindeksowaną | *Brak* | "Zwiększenie możliwości dostępnych dla każdej osoby i każdej organizacji, na świecie, aby mogli osiągnąć więcej" |
| `Composite` | Kompozycja wiele atrybutów podrzędnych| *Nie dotyczy* | {"Name": "harry shum", "Przynależności": "microsoft"} |

Atrybuty ciągu są używane do reprezentowania wartości ciągów, które mogą się pojawić jako część zapytania użytkownika.  Obsługują one dokładne dopasowanie *jest równa* operacji, jak również *starts_with* operacji scenariuszach uzupełniania zapytań, na przykład dopasowania "micros" z "microsoft".  Bez uwzględniania wielkości liter i rozmyte dopasowywanie do obsługi błędów pisowni będą obsługiwane w przyszłych wydaniach.

Atrybuty Int32/Int64/podwójnej precyzji są używane do reprezentowania wartości liczbowych.  *Is_between* operacji umożliwia obsługę nierówności (lt, le, gt, ge) w czasie wykonywania.  *Starts_with* operacja obsługuje scenariuszach uzupełniania zapytań, na przykład dopasowywania "20" z "2016" lub "3". za pomocą "3,14".

Atrybuty daty są używane do efektywne kodowanie wartości daty.  *Is_between* operacji umożliwia obsługę nierówności (lt, le, gt, ge) w czasie wykonywania.
  
Identyfikator GUID atrybuty są używane do wydajnego reprezentują wartości identyfikatora GUID domyślnie obsługuje *jest równa* operacji.

Atrybuty obiektów blob są używane do efektywne kodowanie potencjalnie dużych ilości danych obiektów blob, do środowiska uruchomieniowego wyszukiwania z odpowiedniego obiektu, bez obsługi dla każdej operacji indeksowania, na podstawie zawartości wartości obiektu blob.

### <a name="composite-attributes"></a>Atrybuty złożone

Złożone atrybuty są używane do reprezentowania grupowania wartości atrybutów.  Nazwa każdego z atrybutów podrzędnych rozpoczyna się od nazwy atrybutu złożonego, a następnie ".".  Wartości dla atrybutów złożone są określane jako obiekt JSON, zawierającą wartości atrybutów zagnieżdżonych.  Złożone atrybutów może mieć wiele wartości obiektu.  Jednak złożone atrybutów nie może mieć podrzędnej atrybutów, które same atrybuty złożone są.

W powyższym przykładzie akademickich publikacji dzięki temu usługa do wykonywania zapytań dla dokumentów przez "harry shum" podczas, gdy jest on na "microsoft".  Bez złożonego atrybutów usługi można badać tylko dokumentów, gdzie jest on autorzy "harry shum" i jednym autorów wynosi "microsoft".  Aby uzyskać więcej informacji, zobacz [złożonego zapytania](SemanticInterpretation.md#composite-function).

## <a name="attribute-operations"></a>Operacje na atrybutach

Domyślnie każdy atrybut są indeksowane, aby obsługiwać wszystkie operacje dostępne na typ danych atrybutu.  Określoną operacją nie jest wymagane, zestaw indeksowanej operacji można jawnie określone, aby zmniejszyć rozmiar indeksu.  W poniższym fragmencie kodu z powyższego przykładu schematu atrybut Author.Id są indeksowane, aby obsługiwać tylko *jest równa* operacja, ale nie dodatkowe *starts_with* i *is_between*  operacje dla atrybutów typu Int32.
```json
{"name":"Author.Id", "type":"Int32", "operations":["equals"]}
```

Gdy atrybut odwołuje się do wewnątrz gramatyki *starts_with* operacji musi być określona w schemacie w kolejności do generowania uzupełnienia z częściowego zapytań usługi.  

## <a name="attribute-synonyms"></a>Synonimy — atrybut

Często zaleca się wartość atrybutu określonego ciągu według synonimu.  Na przykład użytkownicy mogą odwoływać się do "Microsoft", "MSFT" lub "MS".  W takich przypadkach definicja atrybutu można określić nazwę pliku schematu znajduje się w tym samym katalogu co plik schematu.  Każdy wiersz w pliku synonim reprezentuje wpis synonimów w następującym formacie JSON: `["<canonical>", "<synonym>"]`.  W schemacie przykład "AuthorName.syn" to plik JSON, który zawiera wartości synonim dla atrybutu Author.Name.

`{"name":"Author.Name", "type":"String", "synonyms":"AuthorName.syn"}`


Canonical wartość może mieć wiele synonimów.  Wiele wartości canonical może udostępniać typowe synonimów.  W takiej sytuacji usługa rozpozna niejednoznaczność poprzez wiele interpretacji.  Poniżej jest przykładowy plik synonimy AuthorName.syn odpowiadający powyższego schematu:
```json
["harry shum","heung-yeung shum"]
["harry shum","h shum"]
["henry shum","h shum"]
...
```
