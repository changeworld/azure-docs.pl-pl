---
title: Interfejs wiersza polecenia — interfejs API Knowledge Exploration Service
titlesuffix: Azure Cognitive Services
description: Użyj interfejsu wiersza polecenia do tworzenia plików indeksów i gramatyki z danych strukturalnych, a następnie wdróż je jako usługi sieci Web.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/24/2016
ms.author: paulhsu
ms.openlocfilehash: 018552982a8ece3bbbaea2d60e2a6e64f681f822
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385639"
---
# <a name="command-line-interface"></a>Interfejs wiersza polecenia

Interfejs wiersza polecenia Knowledge Exploration Service (KES) zapewnia możliwość tworzenia plików indeksów i gramatyki na podstawie danych ze strukturą oraz wdrażania ich jako usług sieci Web.  Używa składni ogólnej: `kes.exe <command> <required_args> [<optional_args>]`.  Można uruchomić `kes.exe` bez argumentów, aby wyświetlić listę poleceń, lub `kes.exe <command>`, aby wyświetlić listę argumentów dostępnych dla określonego polecenia.  Poniżej znajduje się lista dostępnych poleceń:

* build_index
* build_grammar
* host_service
* deploy_service
* describe_index
* describe_grammar

<a name="build_index-command"></a>

## <a name="build_index-command"></a>Polecenie build_index

Polecenie **build_index** kompiluje binarny plik indeksu z pliku definicji schematu i pliku danych obiektów do indeksowania.  Wynikowy plik indeksu może służyć do analizowania wyrażeń z strukturalnymi zapytaniami lub do generowania interpretacji zapytań języka naturalnego w połączeniu z skompilowanym plikiem gramatyki.

`kes.exe build_index <schemaFile> <dataFile> <indexFile> [options]`

| Parametr      | Opis               |
|----------------|---------------------------|
| `<schemaFile>` | Ścieżka schematu wejściowego |
| `<dataFile>`   | Ścieżka danych wejściowych   |
| `<indexFile>`  | Ścieżka indeksu danych wyjściowych |
| `--description <description>` | Ciąg opisu |
| `--remote <vmSize>`           | Rozmiar maszyny wirtualnej dla kompilacji zdalnej |

Te pliki mogą być określone przez lokalne ścieżki plików lub ścieżki URL do obiektów blob platformy Azure.  Plik schematu opisuje strukturę indeksowanych obiektów oraz operacje, które mają być obsługiwane (zobacz [Format schematu](SchemaFormat.md)).  Plik danych wylicza obiekty i wartości atrybutów do indeksowania (zobacz [Format danych](DataFormat.md)).  Gdy kompilacja zakończy się pomyślnie, plik indeksu wyjściowego zawiera skompresowaną reprezentację danych wejściowych, które obsługują żądane operacje.  

Ciąg opisu może być opcjonalnie określony, aby identyfikować indeks binarny za pomocą polecenia **describe_index** .  

Domyślnie indeks jest tworzony na komputerze lokalnym.  Poza środowiskiem platformy Azure lokalne kompilacje są ograniczone do plików danych zawierających do 10 000 obiektów.  Po określeniu flagi--Remote indeks zostanie utworzony na tymczasowej maszynie wirtualnej platformy Azure o określonym rozmiarze.  Dzięki temu duże indeksy można wydajnie budować przy użyciu maszyn wirtualnych platformy Azure z większą ilością pamięci.  Aby uniknąć stronicowania, które spowalnia proces kompilacji, zalecamy użycie maszyny wirtualnej z 3 razy większą ilością pamięci RAM jako rozmiar pliku danych wejściowych.  Aby uzyskać listę dostępnych rozmiarów maszyn wirtualnych, zobacz [Rozmiary maszyn wirtualnych](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

> [!TIP] 
> Aby przyspieszyć kompilacje, należy posortować obiekty w pliku danych przez zmniejszenie prawdopodobieństwa.

<a name="build_grammar-command"></a>

## <a name="build_grammar-command"></a>Polecenie build_grammar

**Build_grammar** polecenie kompiluje gramatykę określoną w kodzie XML do binarnego pliku gramatyki.  Otrzymany plik gramatyki może być używany w połączeniu z plikiem indeksu do generowania interpretacji zapytań języka naturalnego.

`kes.exe build_grammar <xmlFile> <grammarFile>`

| Parametr       | Opis               |
|-----------------|---------------------------|
| `<xmlFile>`     | Ścieżka danych wejściowych gramatyki XML |
| `<grammarFile>` | Wyjściowa ścieżka gramatyki         |

Te pliki mogą być określone przez lokalne ścieżki plików lub ścieżki URL do obiektów blob platformy Azure.  Specyfikacja gramatyki opisuje zestaw ważonych wyrażeń języka naturalnego i ich interpretacji semantyki (zobacz [Format gramatyki](GrammarFormat.md)).  Gdy kompilacja zakończy się pomyślnie, plik gramatyki wyjściowej zawiera binarną reprezentację specyfikacji gramatyki, aby umożliwić szybkie dekodowanie.

<a name="host_service-command"/>

## <a name="host_service-command"></a>Polecenie host_service

**Host_service** polecenie obsługuje wystąpienie usługi KES na komputerze lokalnym.

`kes.exe host_service <grammarFile> <indexFile> [options]`

| Parametr       | Opis                |
|-----------------|----------------------------|
| `<grammarFile>` | Wejściowa ścieżka gramatyki binarnej         |
| `<indexFile>`   | Ścieżka wejściowego indeksu binarnego           |
| `--port <port>` | Numer portu lokalnego.  Wartość domyślna: 8000 |

Te pliki mogą być określone przez lokalne ścieżki plików lub ścieżki URL do obiektów blob platformy Azure.  Usługa sieci Web będzie hostowana w http://localhost:&lt;p,&gt;/.  Listę obsługiwanych operacji można znaleźć w temacie [interfejsy API sieci Web](WebAPI.md) .

Poza środowiskiem platformy Azure usługi hostowane lokalnie są ograniczone do plików indeksu o rozmiarze do 1 MB, 10 żądań na sekundę i 1000 całkowitej liczby wywołań.  Aby obejść te ograniczenia, uruchom **host_service** wewnątrz maszyny wirtualnej platformy Azure lub Wdróż ją w usłudze w chmurze platformy Azure przy użyciu **deploy_service**.

<a name="deploy_service-command"/>

## <a name="deploy_service-command"></a>Polecenie deploy_service

**Deploy_service** polecenie wdraża wystąpienie usługi KES w usłudze w chmurze platformy Azure.

`kes.exe deploy_service <grammarFile> <indexFile> <serviceName> <vmSize>[options]`

| Parametr       | Opis                  |
|-----------------|------------------------------|
| `<grammarFile>` | Wejściowa ścieżka gramatyki binarnej           |
| `<indexFile>`   | Ścieżka wejściowego indeksu binarnego             |
| `<serviceName>` | Nazwa docelowej usługi w chmurze |
| `<vmSize>`      | Rozmiar maszyny wirtualnej usługi w chmurze     |
| `--slot <slot>` | Gniazdo usługi w chmurze: "przemieszczanie" (domyślnie), "produkcja" |

Te pliki mogą być określone przez lokalne ścieżki plików lub ścieżki URL do obiektów blob platformy Azure.  Nazwa usługi określa wstępnie skonfigurowaną usługę w chmurze platformy Azure (zobacz [jak utworzyć i wdrożyć usługę w chmurze](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md)).  Polecenie spowoduje automatyczne wdrożenie usługi KES w określonej usłudze w chmurze platformy Azure przy użyciu maszyn wirtualnych o określonym rozmiarze.  Aby uniknąć stronicowania, które znacząco zmniejsza wydajność, zalecamy korzystanie z maszyny wirtualnej o pojemności 1 GB więcej pamięci niż rozmiar pliku indeksu wejściowego.  Aby uzyskać listę dostępnych rozmiarów maszyn wirtualnych, zobacz [rozmiary dla Cloud Services](../../../articles/cloud-services/cloud-services-sizes-specs.md).

Domyślnie usługa jest wdrażana w środowisku przejściowym, opcjonalnie przesłonięta za pomocą parametru--slot.  Listę obsługiwanych operacji można znaleźć w temacie [interfejsy API sieci Web](WebAPI.md) .

<a name="describe_index-command"/>

## <a name="describe_index-command"></a>polecenie describe_index

Polecenie **describe_index** wyprowadza informacje o pliku indeksu, w tym schematu i opisie.

`kes.exe describe_index <indexFile>`

| Parametr     | Opis      |
|---------------|------------------|
| `<indexFile>` | Ścieżka indeksu danych wejściowych |

Ten plik może być określony przez lokalną ścieżkę do pliku lub ścieżkę URL do obiektu blob platformy Azure.  Ciąg opisu danych wyjściowych można określić za pomocą parametru--Description polecenia **build_index** .

<a name="describe_grammar-command"/>

## <a name="describe_grammar-command"></a>polecenie describe_grammar

Polecenie **describe_grammar** wyprowadza oryginalną specyfikację gramatyczną używaną do kompilowania gramatyki binarnej.

`kes.exe describe_grammar <grammarFile>`

| Parametr       | Opis      |
|-----------------|------------------|
| `<grammarFile>` | Ścieżka gramatyki wejściowej |

Ten plik może być określony przez lokalną ścieżkę do pliku lub ścieżkę URL do obiektu blob platformy Azure.

