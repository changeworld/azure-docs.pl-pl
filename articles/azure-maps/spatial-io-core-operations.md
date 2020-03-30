---
title: Podstawowe operacje we/wy | Mapy platformy Microsoft Azure
description: Dowiedz się, jak skutecznie odczytywać i zapisywać dane XML i rozdzielane przy użyciu podstawowych bibliotek z przestrzennego modułu We/Wy.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0b8fe1b319dc480879944d28f10645025a8cb38e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371441"
---
# <a name="core-io-operations"></a>Podstawowe operacje we/wy

Oprócz dostarczania narzędzi do odczytywania plików danych przestrzennych, przestrzenny moduł We/Wy udostępnia podstawowe biblioteki bazowe do odczytu i zapisu XML i rozdzielanych danych szybko i wydajnie.

Obszar `atlas.io.core` nazw zawiera dwie klasy niskiego poziomu, które mogą szybko odczytywać i zapisywać dane CSV i XML. Te klasy podstawowe zasilają czytniki danych przestrzennych i moduł i moduł we/wy w module Spatial IO. Możesz ich używać, aby dodać dodatkową obsługę odczytu i zapisu plików CSV lub XML.
 
## <a name="read-delimited-files"></a>Odczytywanie rozdzielonych plików

Klasa `atlas.io.core.CsvReader` odczytuje ciągi, które zawierają rozdzielone zestawy danych. Ta klasa zawiera dwie metody odczytu danych:

- Funkcja `read` odczytuje pełny zestaw danych i zwróci dwuwymiarową tablicę ciągów reprezentujących wszystkie komórki rozdzielonego zestawu danych.
- Funkcja `getNextRow` odczytuje każdy wiersz tekstu w rozdzielanym zestawie danych i zwraca tablicę ciągów reprezentującą wszystkie komórki w tym wierszu zestawu danych. Użytkownik może przetworzyć wiersz i usunąć niepotrzebną pamięć z tego wiersza przed przetworzeniem następnego wiersza. Tak, funkcja jest bardziej wydajne pamięci.

Domyślnie czytelnik użyje znaku przecinka jako ogranicznika. Jednak ogranicznik można zmienić na dowolny `'auto'`pojedynczy znak lub ustawić na . Po ustawieniu `'auto'`na czytnik przeanalizuje pierwszy wiersz tekstu w ciągu. Następnie wybierze najczęstszy znak z poniższej tabeli, aby użyć go jako ogranicznika.

| | |
| :-- | :-- |
| Przecinek | `,` |
| Tab | `\t` |
| Rury | `|` |

Ten czytnik obsługuje również kwalifikatory tekstu, które są używane do obsługi komórek, które zawierają znak ogranicznika. Znak cudzysłowu (`'"'`) jest domyślnym kwalifikatorem tekstu, ale można go zmienić na dowolny pojedynczy znak.

## <a name="write-delimited-files"></a>Zapisywanie rozdzielanych plików

Zapisuje `atlas.io.core.CsvWriter` tablicę obiektów jako ciąg rozdzielany. Dowolny pojedynczy znak może być używany jako ogranicznik lub kwalifikator tekstu. Domyślnym ogranicznikiem`','`jest przecinek ( ) a`'"'`domyślnym kwalifikatorem tekstu jest znak cudzysłowu ( ).

Aby użyć tej klasy, wykonaj poniższe czynności:

- Utwórz wystąpienie klasy i opcjonalnie ustaw niestandardowy ogranicznik lub kwalifikator tekstu.
- Zapisuj dane do `write` klasy `writeRow` za pomocą funkcji lub funkcji. Dla `write` tej funkcji przekaż dwuwymiarową tablicę obiektów reprezentujących wiele wierszy i komórek. Aby użyć `writeRow` tej funkcji, należy przekazać tablicę obiektów reprezentujących wiersz danych z wieloma kolumnami.
- Wywołanie `toString` funkcji, aby pobrać ciąg rozdzielany. 
- Opcjonalnie wywołać `clear` metodę, aby moduł zapisujący wielokrotnego wykorzystania i `delete` zmniejszyć jego alokacji zasobów lub wywołać metodę, aby pozbyć się wystąpienia modułu zapisującego.

> [!Note]
> Liczba zapisanych kolumn będzie ograniczona do liczby komórek w pierwszym wierszu danych przekazanych do modułu zapisującego.

## <a name="read-xml-files"></a>Odczytywanie plików XML

Klasa `atlas.io.core.SimpleXmlReader` jest szybsza podczas analizowania `DOMParser`plików XML niż . Jednak `atlas.io.core.SimpleXmlReader` klasa wymaga plików XML, aby być dobrze sformatowany. Pliki XML, które nie są dobrze sformatowane, na przykład brakujące tagi zamykające, prawdopodobnie spowoduje błąd.

Poniższy kod pokazuje, jak `SimpleXmlReader` używać klasy do analizowania ciągu XML w obiekcie JSON i serializacji go w żądanym formacie.

```javascript
//Create an instance of the SimpleXmlReader and parse an XML string into a JSON object.
var xmlDoc = new atlas.io.core.SimpleXmlReader().parse(xmlStringToParse);

//Verify that the root XML tag name of the document is the file type your code is designed to parse.
if (xmlDoc && xmlDoc.root && xmlDoc.root.tagName && xmlDoc.root.tagName === '<Your desired root XML tag name>') {

    var node = xmlDoc.root;

    //Loop through the child node tree to navigate through the parsed XML object.
    for (var i = 0, len = node.childNodes.length; i < len; i++) {
        childNode = node.childNodes[i];

        switch (childNode.tagName) {
            //Look for tag names, parse and serialized as desired.
        }
    }
}
```

## <a name="write-xml-files"></a>Pisanie plików XML

Klasa `atlas.io.core.SimpleXmlWriter` zapisuje dobrze sformatowany kod XML w sposób efektywny pamięci.

Poniższy kod pokazuje, jak `SimpleXmlWriter` używać klasy do generowania dobrze sformatowanego ciągu XML.

```javascript
//Create an instance of the SimpleXmlWriter class.
var writer = new atlas.io.core.SimpleXmlWriter();

//Start writing the document. All write functions return a reference to the writer, making it easy to chain the function calls to reduce the code size.
writer.writeStartDocument(true)
    //Specify the root XML tag name, in this case 'root'
    .writeStartElement('root', {
        //Attributes to add to the root XML tag.
        'version': '1.0',
        'xmlns': 'http://www.example.com',
         //Example of a namespace.
        'xmlns:abc': 'http://www.example.com/abc'
    });

//Start writing an element that has the namespace abc and add other XML elements as children.
writer.writeStartElement('abc:parent');

//Write a simple XML element like <title>Azure Maps is awesome!</title>
writer.writeElement('title', 'Azure Maps is awesome!');

//Close the element that we have been writing children to.
writer.writeEndElement();

//Finish writing the document by closing the root tag and the document.
writer.writeEndElement().writeEndDocument();

//Get the generated XML string from the writer.
var xmlString = writer.toString();
```

Wygenerowany kod XML z powyższego kodu będzie wyglądać następująco.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<root version="1.0" xmlns="http://www.example.com" xmlns:abc="http://www.example.com/abc">
    <abc:parent>
        <title>Azure Maps is awesome!</title>
    </abc:parent>
</root>
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [CsvReader (CsvReader)](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

> [!div class="nextstepaction"]
> [CsvWriter (Pisarz csv)](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

> [!div class="nextstepaction"]
> [SimpleXmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

> [!div class="nextstepaction"]
> [ProstyXmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

Aby uzyskać więcej przykładów kodu do dodania do map, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Szczegóły obsługiwanego formatu danych](spatial-io-supported-data-format-details.md)