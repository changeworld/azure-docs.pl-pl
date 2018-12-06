---
title: Namespace hierarchiczne usługi Azure Data Lake Storage Gen2 (wersja zapoznawcza)
description: Omówienie pojęć dotyczących hierarchicznej przestrzeni nazw dla usługi Azure Data Lake Gen2 — wersja zapoznawcza
services: storage
author: jamesbak
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 13483333c2135f858191f62b255e2887c0e61f01
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52975305"
---
# <a name="azure-data-lake-storage-gen2-preview-hierarchical-namespace"></a>Azure Data Lake Gen2 — wersja zapoznawcza hierarchiczne — przestrzeń nazw

Mechanizm klucza, który umożliwia usługi Azure Data Lake Gen2 — wersja zapoznawcza zapewnienie wydajności systemu plików obiektów magazynu oraz ceny, to dodanie **hierarchicznej przestrzeni nazw**. Umożliwia to zbiór obiektów/plików, w ramach konta, które mają być organizowane w hierarchii katalogów i podkatalogów zagnieżdżonych w taki sam sposób, że system plików na komputerze jest zorganizowana. W hierarchicznej przestrzeni nazw, włączone konto magazynu staje się możliwość zapewnienia skalowalności i ekonomiczności magazynie obiektów przy użyciu semantyki systemu plików, które są znane analytics aparatów i struktur.

## <a name="the-benefits-of-the-hierarchical-namespace"></a>Korzyści wynikające z hierarchicznej przestrzeni nazw

Następujące korzyści są skojarzone z systemami plików, które implementują hierarchicznej przestrzeni nazw za pośrednictwem danych obiektów blob:

- **Manipulowanie Atomic katalogu:** magazyny obiektów w przybliżeniu hierarchii katalogów przez przyjęcie Konwencji osadzania ukośnikami (/) w nazwie obiektu do określenia segmentów ścieżki. Gdy ta Konwencja działa w przypadku organizowania obiektów, Konwencji zapewnia nie pomocy dla akcji, takich jak przenoszenie, zmiana nazwy lub usuwanie katalogów. Bez rzeczywistego katalogów aplikacji musi przetworzyć potencjalnie milionów poszczególne obiekty BLOB do osiągnięcia poziomie katalogu zadania. Z kolei hierarchicznej przestrzeni nazw przetwarza te zadania, aktualizując pojedynczy wpis (katalog nadrzędny).

    Optymalizacja znaczne jest szczególnie istotne dla wielu platform analizy danych big data. Narzędzia, takie jak Hive, Spark, itp. często zapisywać dane wyjściowe do tymczasowej lokalizacji, a następnie zmień nazwę lokalizacji podczas kończenia zadania. Bez hierarchicznej przestrzeni nazw to zmiana nazwy często może trwać dłużej, niż analizami przetworzyć sam. Mniejsze opóźnienia i zadania jest równa niższy całkowity koszt posiadania (TCO) dla obciążeń analitycznych.

- **Styl interfejsu znanych:** systemy plików są dobrze zrozumiałe przez programistów i użytkowników. Istnieje nie musisz uczyć się nowego modelu magazynu po przeniesieniu do chmury, interfejsu systemu plików, które są udostępniane przez Data Lake Storage Gen2 jest tego samego modelu, używany przez komputery, małych i dużych.

Jest jednym z powodów, że magazyny obiektów nie ma w przeszłości obsługiwane hierarchiczne przestrzenie nazw, że hierarchiczne przestrzenie nazw ograniczone skali. Jednak Data Lake Storage Gen2 hierarchicznej przestrzeni nazw jest skalowana liniowo i nie zmniejsza pojemnością danych lub wydajnością.

## <a name="when-to-enable-the-hierarchical-namespace"></a>Kiedy należy włączyć hierarchicznej przestrzeni nazw

Włączanie hierarchicznej przestrzeni nazw jest zalecane dla obciążeń magazynowania, które są przeznaczone dla systemów plików, które manipulowania katalogów. Dotyczy to wszystkich obciążeń, które są przeznaczone głównie dla przetwarzania analizy. Zestawy danych, które wymagają wysokiego stopnia organizacji również będą mogli korzystać, włączając hierarchicznej przestrzeni nazw.

Ze względu na włączenie hierarchicznej przestrzeni nazw są określone przez analizę całkowitego kosztu posiadania. Ogólnie rzecz biorąc ulepszenia obciążenia opóźnienia z powodu przyspieszenie magazynu będzie wymagać zasoby obliczeniowe dla mniej czasu. Opóźnienie dla wielu zadań mogą być ulepszane z powodu atomic katalog został zmanipulowany, można włączyć, konfigurując hierarchicznej przestrzeni nazw. W wielu obciążeń zasobów obliczeniowych reprezentuje > 85% całkowitego kosztu, a nawet niewielkie zmniejszenie obciążenia opóźnienia jest równa znacznej ilości całkowitego kosztu posiadania oszczędności. Nawet w przypadkach, w którym Włączanie hierarchicznej przestrzeni nazw zwiększa koszty magazynowania całkowitego kosztu posiadania nadal jest obniżona z powodu koszty operacji obliczeniowych mniejsze.

## <a name="when-to-disable-the-hierarchical-namespace"></a>Gdy wyłączenie hierarchicznej przestrzeni nazw

Niektórych obciążeń magazynu obiektów nie może uzyskać korzyści przez włączenie hierarchicznej przestrzeni nazw. Te obciążenia przykłady kopii zapasowych, magazyn obrazów i innych aplikacji, w którym organizacja obiektu były przechowywane osobno od samych obiektach (*np.*, w oddzielnej bazy danych).


## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie konta magazynu](./data-lake-storage-quickstart-create-account.md)