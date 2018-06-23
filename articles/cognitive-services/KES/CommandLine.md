---
title: Interfejs wiersza polecenia usługi eksploracji wiedzy | Dokumentacja firmy Microsoft
description: Tworzenie indeksu i gramatyki plików z danych strukturalnych za pomocą interfejsu wiersza polecenia KES, a następnie wdrożyć je jako usługi sieci web w usługach kognitywnych firmy Microsoft.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/24/2016
ms.author: paulhsu
ms.openlocfilehash: ffa42ac73b42a8271004d2d45d7a80f3307ef059
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347152"
---
# <a name="command-line-interface"></a>Command Line Interface
Interfejs wiersza polecenia KES zapewnia możliwość tworzenia indeksu i gramatyki plików z danych strukturalnych i wdrażania ich jako usługi sieci web.  Używa ogólna składnia: `kes.exe <command> <required_args> [<optional_args>]`.  Można uruchomić `kes.exe` bez argumentów, aby wyświetlić listę poleceń, lub `kes.exe <command>` Aby wyświetlić listę argumentów, które są dostępne dla określonego polecenia.  Poniżej przedstawiono listę dostępnych poleceń:
* build_index
* build_grammar
* host_service
* deploy_service
* describe_index
* describe_grammar

<a name="build_index-command"></a>
## <a name="buildindex-command"></a>build_index polecenia
**Build_index** polecenie tworzy plik binarny indeksu z pliku definicji schematu i plik danych obiektów, które mają być indeksowane.  Wynikowy plik indeksu można obliczać wyrażeń structured query lub generowanie interpretacji języka naturalnego zapytań w połączeniu z pliku gramatyki skompilowany.

`kes.exe build_index <schemaFile> <dataFile> <indexFile> [options]`

| Parametr      | Opis               |
|----------------|---------------------------|
| `<schemaFile>` | Ścieżka schemat danych wejściowych |
| `<dataFile>`   | Ścieżka danych wejściowych   |
| `<indexFile>`  | Ścieżka wyjściowa indeksu |
| `--description <description>` | Ciąg opisu |
| `--remote <vmSize>`           | Rozmiar maszyny wirtualnej dla kompilacji zdalnej |

Tych plików może zostać określona przez plik lokalny ścieżek lub adres URL do obiektów blob Azure.  Struktura obiektów indeksowany oraz działań, które mają być obsługiwane opisuje plik schematu (zobacz [Format schematu](SchemaFormat.md)).  Plik danych wylicza obiektów i atrybuty do indeksowania (zobacz [Format danych](DataFormat.md)).  Gdy kompilacja zakończy się pomyślnie, dane wyjściowe pliku indeksu zawiera skompresowany reprezentację danych wejściowych, który obsługuje żądanej operacji.  

Ciąg opisu można opcjonalnie określić, aby następnie zidentyfikować binarne indeksu przy użyciu **describe_index** polecenia.  

Domyślnie ten indeks jest oparty na komputerze lokalnym.  Poza środowiskiem platformy Azure lokalne kompilacje są ograniczone do maksymalnie 10 000 obiektów zawierających pliki danych.  Gdy zdalnego określono flagę, indeks zostanie utworzona na tymczasowo utworzony maszyny Wirtualnej platformy Azure o określonym rozmiarze.  Dzięki temu dużych indeksów wydajnie przy użyciu maszyn wirtualnych platformy Azure z większą ilością pamięci.  Aby uniknąć stronicowania, który spowalnia proces kompilacji, zaleca się przy użyciu maszyny Wirtualnej z 3 razy ilość pamięci RAM jako rozmiar pliku danych wejściowych.  Aby uzyskać listę dostępnych rozmiarów maszyny Wirtualnej, zobacz [rozmiary maszyn wirtualnych](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

> [!TIP] 
> Dla kompilacji szybsze presort obiektów w pliku danych, zmniejszając prawdopodobieństwo.

<a name="build_grammar-command"></a>
## <a name="buildgrammar-command"></a>build_grammar polecenia
**Build_grammar** polecenie kompiluje gramatyki, określonym w kodzie XML w pliku binarnego gramatyki.  Wynikowy plik gramatyki można w połączeniu z plikiem indeksu pliku do generowania interpretacji języka naturalnego zapytań.

`kes.exe build_grammar <xmlFile> <grammarFile>`

| Parametr       | Opis               |
|-----------------|---------------------------|
| `<xmlFile>`     | Ścieżka specyfikacji gramatyki XML wejściowa |
| `<grammarFile>` | Ścieżka gramatyki skompilowanych danych wyjściowych         |

Tych plików może zostać określona przez plik lokalny ścieżek lub adres URL do obiektów blob Azure.  Specyfikacja gramatyki opisano zestaw wyrażeń języka naturalnego ważoną i ich interpretacji semantyki (zobacz [formatu gramatyki](GrammarFormat.md)).  Gdy kompilacja zakończy się pomyślnie, dane wyjściowe pliku gramatyki zawiera to binarna reprezentacja specyfikacji gramatyki, umożliwiające szybkie dekodowania.

<a name="host_service-command"/>
## <a name="hostservice-command"></a>host_service polecenia
**Host_service** polecenia znajduje się wystąpienie usługi KES na komputerze lokalnym.

`kes.exe host_service <grammarFile> <indexFile> [options]`

| Parametr       | Opis                |
|-----------------|----------------------------|
| `<grammarFile>` | Ścieżka wejściowych gramatyka binarna         |
| `<indexFile>`   | Ścieżka wejściowych Indeks binarny           |
| `--port <port>` | Numer portu lokalnego.  Domyślne: 8000 |

Tych plików może zostać określona przez plik lokalny ścieżek lub adres URL do obiektów blob Azure.  Usługi sieci web będzie hostowany pod http://localhost:&lt; port&gt;/.  Zobacz [interfejsów API sieci Web](WebAPI.md) listę obsługiwanych operacji.

Poza platformy Azure środowiska, lokalnie hostowanych usług są ograniczone do indeksu plików maksymalnie 1 MB, rozmiar, 10 żądań na sekundę i 1000 całkowita liczba wywołań.  Aby wyeliminować te ograniczenia, uruchom **host_service** wewnątrz maszyny Wirtualnej platformy Azure lub wdrożyć usługi chmury Azure przy użyciu **deploy_service**.

<a name="deploy_service-command"/>
## <a name="deployservice-command"></a>deploy_service polecenia
**Deploy_service** polecenia wdraża wystąpienia usługi KES do usługi w chmurze Azure.

`kes.exe deploy_service <grammarFile> <indexFile> <serviceName> <vmSize>[options]`

| Parametr       | Opis                  |
|-----------------|------------------------------|
| `<grammarFile>` | Ścieżka wejściowych gramatyka binarna           |
| `<indexFile>`   | Ścieżka wejściowych Indeks binarny             |
| `<serviceName>` | Nazwa docelowej usługi w chmurze |
| `<vmSize>`      | Rozmiar maszyny Wirtualnej usługi w chmurze     |
| `--slot <slot>` | Gniazdo usługi chmury: "tymczasowości" (ustawienie domyślne), "produkcyjnych" |

Tych plików może zostać określona przez plik lokalny ścieżek lub adres URL do obiektów blob Azure.  Nazwa usługi określa wstępnie skonfigurowane usługi w chmurze Azure (zobacz [sposobu tworzenia i wdrażania usługi w chmurze](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md)).  Polecenie zostaną automatycznie wdrożone usługi KES usługą określonej chmury Azure przy użyciu maszyn wirtualnych o określonym rozmiarze.  Aby uniknąć stronicowania, który znacznie zmniejsza wydajność, firma Microsoft zaleca używanie maszyny Wirtualnej z 1 GB pamięci RAM niż rozmiar pliku wejściowego indeksu.  Aby uzyskać listę dostępnych rozmiarów maszyny Wirtualnej, zobacz [rozmiary dla usług w chmurze](../../../articles/cloud-services/cloud-services-sizes-specs.md).

Domyślnie wdrażania usługi do środowiska pomostowego, opcjonalnie zastąpiony przez parametr miejsca.  Zobacz [interfejsów API sieci Web](WebAPI.md) listę obsługiwanych operacji.

<a name="describe_index-command"/>
## <a name="describeindex-command"></a>polecenie describe_index
**Describe_index** polecenie wyświetla informacje o pliku indeksu, w tym schematu i opis.

`kes.exe describe_index <indexFile>`

| Parametr     | Opis      |
|---------------|------------------|
| `<indexFile>` | Ścieżka wejściowych indeksu |

Ten plik może zostać określony przez ścieżkę do pliku lokalnego lub ścieżkę URL do obiektów blob platformy Azure.  Ciąg opisu wyjściowego można określić za pomocą — Opis parametru **build_index** polecenia.

<a name="describe_grammar-command"/>
## <a name="describegrammar-command"></a>polecenie describe_grammar
**Describe_grammar** pierwotnej specyfikacji gramatyki użytą do skompilowania gramatyka binarna danych wyjściowych polecenia.

`kes.exe describe_grammar <grammarFile>`

| Parametr       | Opis      |
|-----------------|------------------|
| `<grammarFile>` | Ścieżka wejściowych gramatyki |

Ten plik może zostać określony przez ścieżkę do pliku lokalnego lub ścieżkę URL do obiektów blob platformy Azure.

