---
title: Interfejs wiersza polecenia — interfejs API usługi Eksploracja wiedzy
titlesuffix: Azure Cognitive Services
description: Tworzenie indeksu i gramatyki plików z danymi strukturalnymi przy użyciu interfejsu wiersza polecenia, a następnie wdrożyć je jako usługi sieci web.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/24/2016
ms.author: paulhsu
ms.openlocfilehash: 018552982a8ece3bbbaea2d60e2a6e64f681f822
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60815143"
---
# <a name="command-line-interface"></a>Command Line Interface

Interfejs wiersza polecenia usługi Eksploracja wiedzy (KES) umożliwia tworzenie indeksu i gramatyki plików z danymi strukturalnymi i wdrażanie ich jako usług sieci web.  Używa ona ogólnych składni: `kes.exe <command> <required_args> [<optional_args>]`.  Możesz uruchomić `kes.exe` bez argumentów, aby wyświetlić listę poleceń, lub `kes.exe <command>` do wyświetlania listy argumentów, które są dostępne dla określonego polecenia.  Poniżej przedstawiono listę dostępnych poleceń:

* build_index
* build_grammar
* host_service
* deploy_service
* describe_index
* describe_grammar

<a name="build_index-command"></a>

## <a name="buildindex-command"></a>build_index polecenia

**Build_index** polecenie powoduje utworzenie pliku binarnego indeksu z pliku definicji schematu i plik danych obiektów, które mają być indeksowane.  Wynikowy plik indeksu może służyć do oceny wyrażeń zapytań ze strukturą lub do generowania interpretacji zapytań w języku naturalnym w połączeniu z plikiem gramatyki skompilowany.

`kes.exe build_index <schemaFile> <dataFile> <indexFile> [options]`

| Parametr      | Opis               |
|----------------|---------------------------|
| `<schemaFile>` | Ścieżka schemat danych wejściowych |
| `<dataFile>`   | Ścieżka danych wejściowych   |
| `<indexFile>`  | Ścieżka indeksu danych wyjściowych |
| `--description <description>` | Ciąg opisu |
| `--remote <vmSize>`           | Rozmiar maszyny Wirtualnej na potrzeby zdalnego kompilowania |

Te pliki mogą być określone przez lokalne ścieżki do plików lub ścieżki adresu URL do obiektów blob platformy Azure.  Plik schematu opisujący strukturę obiektów indeksowane, a także operacje są obsługiwane (zobacz [Format schematu](SchemaFormat.md)).  Plik danych wylicza obiektów i wartości atrybutów, które mają być indeksowane (zobacz [Format danych](DataFormat.md)).  Po pomyślnym zakończeniu kompilacji, plik wyjściowy Indeks zawiera reprezentację skompresowanych danych wejściowych, który obsługuje żądanej operacji.  

Ciąg opisu można opcjonalnie określić, aby później zidentyfikować binarnego indeksu przy użyciu **describe_index** polecenia.  

Domyślnie indeks jest oparty na komputerze lokalnym.  Poza środowiskiem platformy Azure lokalnych kompilacji są ograniczone do plików danych, zawierające do 10 000 obiektów.  Podczas zdalnego flaga zostanie określona, indeks przyniesie tymczasowo utworzonej maszyny wirtualnej platformy Azure o określonym rozmiarze.  Dzięki temu dużych indeksów efektywnie przy użyciu maszyn wirtualnych platformy Azure przy użyciu większej ilości pamięci.  Aby uniknąć stronicowania, który spowalnia proces kompilacji, zaleca się używania maszyny Wirtualnej z 3 razy ilość pamięci RAM jako dane wejściowe rozmiar pliku.  Aby uzyskać listę dostępnych rozmiarów maszyn wirtualnych, zobacz [Rozmiary maszyn wirtualnych](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

> [!TIP] 
> Aby przyspieszyć kompilowanie presort obiektów w pliku danych, zmniejszając prawdopodobieństwo.

<a name="build_grammar-command"></a>

## <a name="buildgrammar-command"></a>build_grammar polecenia

**Build_grammar** polecenie kompiluje gramatyki, określone w pliku XML do pliku binarnego gramatyki.  Wynikowy plik gramatyki może służyć w połączeniu z plikiem indeksu do generowania interpretacji zapytań w języku naturalnym.

`kes.exe build_grammar <xmlFile> <grammarFile>`

| Parametr       | Opis               |
|-----------------|---------------------------|
| `<xmlFile>`     | Ścieżka wejściowa specyfikacji gramatyki w XML |
| `<grammarFile>` | Ścieżka gramatyki skompilowanych danych wyjściowych         |

Te pliki mogą być określone przez lokalne ścieżki do plików lub ścieżki adresu URL do obiektów blob platformy Azure.  Specyfikacja gramatyki opisuje zestaw wyrażeń ważona języka naturalnego i ich interpretacji semantyczne (zobacz [Format gramatyki](GrammarFormat.md)).  Po pomyślnym zakończeniu kompilacji, plik wyjściowy gramatyki zawiera reprezentacja binarna specyfikacji gramatyki umożliwiające szybkie dekodowania.

<a name="host_service-command"/>

## <a name="hostservice-command"></a>host_service polecenia

**Host_service** polecenie znajduje się wystąpienie usługi KES na komputerze lokalnym.

`kes.exe host_service <grammarFile> <indexFile> [options]`

| Parametr       | Opis                |
|-----------------|----------------------------|
| `<grammarFile>` | Ścieżka gramatyki binarnych danych wejściowych         |
| `<indexFile>`   | Ścieżka danych wejściowych binarnego indeksu           |
| `--port <port>` | Numer portu lokalnego.  Wartość domyślna: 8000 |

Te pliki mogą być określone przez lokalne ścieżki do plików lub ścieżki adresu URL do obiektów blob platformy Azure.  Usługa sieci web będzie hostowany pod http://localhost:&lt ; jest to port&gt; /.  Zobacz [interfejsów API sieci Web](WebAPI.md) Aby uzyskać listę obsługiwanych operacji.

Spoza platformy Azure środowiska, lokalnie hostowanych usług są ograniczone do indeksowania plików w rozmiarze, 10 żądań na sekundę i 1000 łączna liczba wywołań do 1 MB.  Aby wyeliminować te ograniczenia, uruchom **host_service** wewnątrz maszyny Wirtualnej platformy Azure, lub wdrożyć na usługi platformy Azure w chmurze przy użyciu **deploy_service**.

<a name="deploy_service-command"/>

## <a name="deployservice-command"></a>deploy_service polecenia

**Deploy_service** polecenie wdraża wystąpienie usługi KES w usłudze w chmurze platformy Azure.

`kes.exe deploy_service <grammarFile> <indexFile> <serviceName> <vmSize>[options]`

| Parametr       | Opis                  |
|-----------------|------------------------------|
| `<grammarFile>` | Ścieżka gramatyki binarnych danych wejściowych           |
| `<indexFile>`   | Ścieżka danych wejściowych binarnego indeksu             |
| `<serviceName>` | Nazwa docelowej usługi w chmurze |
| `<vmSize>`      | Rozmiar maszyny Wirtualnej usługi w chmurze     |
| `--slot <slot>` | Miejsce usługi w chmurze: "staging" (wartość domyślna), "produkcyjne" |

Te pliki mogą być określone przez lokalne ścieżki do plików lub ścieżki adresu URL do obiektów blob platformy Azure.  Nazwa usługi określa, że usługa wstępnie skonfigurowanej chmury platformy Azure (zobacz [jak utworzyć i wdrożyć usługę w chmurze](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md)).  Polecenie automatycznie wdroży usługi KES usługą określonej chmury platformy Azure przy użyciu maszyn wirtualnych o określonym rozmiarze.  Aby uniknąć stronicowania, co znacznie obniża wydajność, zaleca się używania maszyny Wirtualnej z 1 GB więcej pamięci RAM niż rozmiar pliku wejściowego indeksu.  Aby uzyskać listę dostępnych rozmiarów maszyn wirtualnych, zobacz [rozmiary usług Cloud Services](../../../articles/cloud-services/cloud-services-sizes-specs.md).

Domyślnie usługa jest wdrażana do środowiska pomostowego, opcjonalnie zastąpione za pomocą parametru--miejsca.  Zobacz [interfejsów API sieci Web](WebAPI.md) Aby uzyskać listę obsługiwanych operacji.

<a name="describe_index-command"/>

## <a name="describeindex-command"></a>polecenie describe_index

**Describe_index** polecenie wyświetla informacje o pliku indeksu, w tym schemacie i opis.

`kes.exe describe_index <indexFile>`

| Parametr     | Opis      |
|---------------|------------------|
| `<indexFile>` | Ścieżka danych wejściowych indeksu |

Ten plik może być określone przez lokalną ścieżką pliku lub ścieżkę URL do obiektu blob platformy Azure.  Ciąg opisu danych wyjściowych można określić za pomocą parametru opis **build_index** polecenia.

<a name="describe_grammar-command"/>

## <a name="describegrammar-command"></a>polecenie describe_grammar

**Describe_grammar** polecenie zwraca pierwotną specyfikację gramatyki, używany do tworzenia binarne gramatyki.

`kes.exe describe_grammar <grammarFile>`

| Parametr       | Opis      |
|-----------------|------------------|
| `<grammarFile>` | Ścieżka danych wejściowych gramatyki |

Ten plik może być określone przez lokalną ścieżką pliku lub ścieżkę URL do obiektu blob platformy Azure.

