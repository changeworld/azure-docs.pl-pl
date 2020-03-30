---
title: Hierarchiczna przestrzeń nazw usługi Azure Data Lake Storage Gen2
description: Opisuje koncepcję hierarchicznego obszaru nazw dla usługi Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 6ce94590a1d0de6941c27d972bdd1c4194080e95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153081"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Hierarchiczna przestrzeń nazw usługi Azure Data Lake Storage Gen2

Kluczowym mechanizmem, który umożliwia platformie Azure Data Lake Storage Gen2 zapewnienie wydajności systemu plików w skali magazynu obiektów i cenach, jest dodanie **hierarchicznego obszaru nazw.** Dzięki temu kolekcja obiektów/plików w obrębie konta ma być zorganizowana w hierarchię katalogów i zagnieżdżonych podkatalogów w taki sam sposób, jak system plików na komputerze. Po włączeniu hierarchicznej przestrzeni nazw konto magazynu staje się w stanie zapewnić skalowalność i opłacalność magazynu obiektów, z semantyką systemu plików, które są znane z aparatów analitycznych i struktur.

## <a name="the-benefits-of-a-hierarchical-namespace"></a>Korzyści z hierarchicznego obszaru nazw

Następujące korzyści są skojarzone z systemami plików, które implementują hierarchiczny obszar nazw za pomocą danych obiektów blob:

- **Manipulowanie katalogami atomowymi:** Obiekt przechowuje przybliżoną hierarchię katalogów, przyjmując konwencję osadzania ukośników (/) w nazwie obiektu w celu oznaczenia segmentów ścieżek. Podczas gdy ta konwencja działa do organizowania obiektów, konwencja nie zapewnia pomocy dla działań, takich jak przenoszenie, zmiana nazwy lub usuwanie katalogów. Bez rzeczywistych katalogów aplikacje muszą przetwarzać potencjalnie miliony pojedynczych obiektów blob, aby osiągnąć zadania na poziomie katalogu. Natomiast hierarchiczna przestrzeń nazw przetwarza te zadania, aktualizując pojedynczy wpis (katalog nadrzędny).

    Ta dramatyczna optymalizacja jest szczególnie istotna dla wielu platform analizy dużych zbiorów danych. Narzędzia takie jak Hive, Spark itp często zapisują dane wyjściowe do lokalizacji tymczasowych, a następnie zmieniają nazwę lokalizacji po zakończeniu zadania. Bez hierarchicznego obszaru nazw ta zmiana nazwy często może trwać dłużej niż sam proces analizy. Mniejsze opóźnienie zadania oznacza niższy całkowity koszt posiadania (TCO) dla obciążeń analitycznych.

- **Znany styl interfejsu:** Systemy plików są dobrze rozumiane zarówno przez programistów, jak i użytkowników. Nie ma potrzeby uczenia się nowego paradygmatu magazynu podczas przenoszenia do chmury, ponieważ interfejs systemu plików udostępniane przez Data Lake Storage Gen2 jest tym samym paradygmatem używanym przez komputery, duże i małe.

Jednym z powodów, dla których magazyny obiektów nie obsługiwały w przeszłości hierarchicznego obszaru nazw, jest hierarchiczne ograniczenie skali obszaru nazw. Jednak hierarchiczna przestrzeń nazw magazynu danych Lake Gen2 skaluje się liniowo i nie obniża pojemności danych ani wydajności.

## <a name="deciding-whether-to-enable-a-hierarchical-namespace"></a>Decydowanie o tym, czy włączyć hierarchiczną przestrzeń nazw

Po włączeniu hierarchicznego obszaru nazw na koncie nie można przywrócić go do płaskiej przestrzeni nazw. W związku z tym należy rozważyć, czy ma sens, aby włączyć hierarchiczną przestrzeń nazw na podstawie charakteru obciążeń magazynu obiektów.

Niektóre obciążenia mogą nie uzyskać żadnych korzyści, włączając hierarchiczną przestrzeń nazw. Przykłady obejmują kopie zapasowe, magazyn obrazu i inne aplikacje, w których organizacja obiektów jest przechowywana oddzielnie od samych obiektów (na przykład: w oddzielnej bazie danych). 

Ponadto podczas gdy obsługa funkcji magazynu obiektów Blob i ekosystemu usługi platformy Azure nadal rośnie, nadal istnieją pewne funkcje i usługi platformy Azure, które nie są jeszcze obsługiwane na kontach, które mają hierarchiczny obszar nazw. Zobacz [Znane problemy](data-lake-storage-known-issues.md). 

Ogólnie rzecz biorąc zaleca się włączenie hierarchicznego obszaru nazw dla obciążeń magazynu, które są przeznaczone dla systemów plików, które manipulują katalogami. Obejmuje to wszystkie obciążenia, które są przede wszystkim do przetwarzania analizy. Zestawy danych, które wymagają wysokiego stopnia organizacji również skorzystają, włączając hierarchiczną przestrzeń nazw.

Przyczyny włączenia hierarchicznego obszaru nazw są określane przez analizę TK. Ogólnie rzecz biorąc, poprawa opóźnienia obciążenia z powodu przyspieszenia magazynu będzie wymagać zasobów obliczeniowych przez krótszy czas. Opóźnienie dla wielu obciążeń może zostać poprawione z powodu niepodzielnego manipulowania katalogami, która jest włączona przez hierarchiczny obszar nazw. W wielu obciążeniach zasób obliczeniowy stanowi > 85% całkowitego kosztu, a więc nawet niewielkie zmniejszenie opóźnienia obciążenia odpowiada znacznej ilości oszczędności całkowitego kosztu posiadania. Nawet w przypadkach, gdy włączenie hierarchicznego obszaru nazw zwiększa koszty magazynowania, koszt posiadania jest nadal obniżany ze względu na zmniejszone koszty obliczeniowe.

Aby przeanalizować różnice w cenach magazynu danych, cenach transakcyjnych i cenach rezerwacji pojemności magazynu między kontami, które mają płaską hierarchiczną przestrzeń nazw a hierarchiczną przestrzenią nazw, zobacz [Ceny usługi Azure Data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="next-steps"></a>Następne kroki

- [Tworzenie konta magazynu](./data-lake-storage-quickstart-create-account.md)
