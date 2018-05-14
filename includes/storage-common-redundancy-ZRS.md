---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: jeking
ms.custom: include file
ms.openlocfilehash: 0347d6ca951b75c385b138487f58b85a809c6805
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
---
Strefy magazyn geograficznie nadmiarowy (ZRS) synchronicznie replikuje dane między magazynu klastrów trzech (3) w pojedynczym regionie. Każdy klaster magazyn jest fizycznie oddzielona od innych i znajduje się w jej własnej strefie dostępności (AZ). Każdej strefy dostępności i klaster ZRS, to jest autonomiczny z oddzielnych narzędzi i możliwości sieciowych.

Przechowywanie danych w ramach konta ZRS zapewnia, że będzie można uzyskać dostępu i zarządzać danymi w przypadku, gdy strefa jest niedostępny. Magazyn ZRS zapewnia doskonałą wydajność i bardzo małych opóźnień. W rzeczywistości ma taką samą ZRS [wartości docelowe skalowalności](../articles/storage/common/storage-scalability-targets.md) jako LRS.

W scenariuszach wymagających wysoki poziom spójności, silne trwałości i wysokiej dostępności, nawet w przypadku awarii lub klęski żywiołowej renderuje centrum danych zonal niedostępne, należy wziąć pod uwagę ZRS. Magazyn ZRS zapewnia trwałość obiektów magazynu co najmniej % 99.9999999999 (12 na 9) w danym roku.

Aby uzyskać więcej informacji na temat stref dostępności, zobacz [Przegląd stref dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="support-coverage-and-regional-availability"></a>Regionalne dostępności i pomocy technicznej
Magazyn ZRS jest obecnie obsługuje [ **standardowe, ogólnego przeznaczenia v2 (GPv2)** ](../articles/storage/common/storage-account-options.md#general-purpose-v2) kont typu. Magazyn ZRS jest dostępna dla blokowych obiektów blob, z systemem innym niż dysk stronicowe obiekty BLOB, plików, tabel i kolejek. Ponadto wszystkie Twoje [analityka magazynu](../articles/storage/common/storage-analytics.md) dzienniki i [metryki magazynu](../articles/storage/common/storage-enable-and-view-metrics.md)

Magazyn ZRS jest **ogólnie dostępna** w następujących regionach:

- Wschodnie stany USA 2
- Środkowe stany USA
- Europa Północna
- Europa Zachodnia
- Francja Środkowa
- Azja Południowo-Wschodnia

Firma Microsoft będzie nadal występować włączyć ZRS w różnych regionach platformy Azure i spowoduje zaktualizowanie tej listy, jeśli ma to miejsce. Firma Microsoft będzie także wprowadzać takie powiadomienia za pośrednictwem standardowych kanałów takich jak [aktualizacje usługi Azure](https://azure.microsoft.com/updates/) strony i wiadomości e-mail powiadomienia do właścicieli subskrypcji platformy Azure i administratorów.

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>Co się stanie, gdy strefa jest niedostępny?

Dane pozostaną elastyczne, jeśli strefa jest niedostępny. Firma Microsoft zaleca, nadal należy uwzględnić wskazówki dotyczące obsługi, takie jak wdrażanie zasad ponawiania z wykładniczej wycofania wystąpienia błędu przejściowego. Gdy strefa jest niedostępny, Azure zobowiązuje się sieci aktualizacji, na przykład DNS repointing. Te aktualizacje mogą wpływać na aplikację, jeśli przed ukończył uzyskują dostęp do danych.

Magazyn ZRS nie może chronić dane przed regionalnej awarii, gdzie trwale dotyczy wielu strefach. Zamiast tego ZRS zapewnia odporność danych w przypadku niedostępności danych czasowych. Do ochrony przed regionalnej awarii, firma Microsoft zaleca używanie [magazyn geograficznie nadmiarowy (GRS): replikacji między regionalne usługi Azure Storage](../articles/storage/common/storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>Konwertowanie na ZRS replikacji
Obecnie jest bardzo prosta do zmiany między LRS, GRS i RA-GRS - korzystać z portalu lub interfejsu API. Z ZRS jednak nie jest jako utrudnione ponieważ spowodowałoby to przeniesienie danych fizycznych z sygnatury pojedynczy magazyn wiele sygnatur w obrębie regionu. Tak masz dwie opcje podstawowego — ręcznie skopiowania/przeniesienia danych do nowego konta ZRS z istniejącego konta lub żądania migracji na żywo. Zdecydowanie zaleca się dokonać ręcznej migracji, ponieważ nie możemy zagwarantować prawidłowych w przypadku migracji na żywo zostanie zakończony; istnieje wiele czynników, które bezpośrednio i pośredni wpływ na ukończenie zadania migracji. 

Aby przeprowadzić migrację ręcznie, masz różne opcje:
- Użyj istniejących narzędzi takich jak narzędzia AzCopy, magazynu zestawu SDK, niezawodne narzędzi innych firm, np.
- Jeśli znasz Hadoop lub HDInsight, możesz dołączyć zarówno źródłowego i docelowego (ZRS) konta do klastra, a także umożliwia ekran podobny do narzędzia DistCp ogromną skalę parallelize proces kopiowania danych
- Tworzenie własnych narzędzi wykorzystaniu jednej wersji zestawu SDK magazynu

Jak wspomniano wcześniej, zdecydowanie zaleca się przejść tras w ramach migracji, ponieważ zapewnia większą elastyczność niż migracji na żywo. Pełną kontrolę nad po wystąpieniu migracji są również.

Jeśli jednak ręcznej migracji spowoduje przestój aplikacji, a nie do przyjęcia, który na użytkownika końcowego, udostępniamy opcji migracji na żywo. Migracja na żywo jest migracji w miejscu, które umożliwia kontynuowanie przy użyciu istniejącego konta magazynu podczas migracji danych między sygnatury magazynu źródłowego i docelowego. Podczas migracji będą nadal mają ten sam poziom trwałość i dostępność, tak jak zwykle.

Migracja na żywo pochodzą jednak z pewnymi ograniczeniami. Są one wymienione poniżej.

- Gdy żądanie migracji na żywo będzie można rozwiązać natychmiast, nie możemy zagwarantować podczas migracji będzie faktycznie ukończenia. Jeśli potrzebujesz danych będzie ZRS w określonym czasie, należy wykonać ręcznej migracji. Ogólnie rzecz biorąc, większej ilości danych należy na koncie tym dłużej potrwa do migracji danych. 
- Użytkownik może tylko migracja na żywo z konta z replikacji LRS i GRS. Jeśli masz RA-GRS następnie należy zmienić na jednym z tych typów replikacji przed kontynuowaniem. Ten krok pośredniczące zapewnia, że pomocniczy tylko do odczytu punktu końcowego, który udostępnia RA-GRS są usuwane podczas są gotowe.
- Twoje konto musi być niepusta.
- Obsługiwane są tylko wewnątrz regionu migracje. Jeśli chcesz przeprowadzić migrację danych do konta ZRS znajduje się w regionie inne niż konto źródłowego, należy wykonać ręcznej migracji.
- Standardowe typy kont magazynu tylko. Nie można migrować z konta magazynu Premium.

Żądania są kierowane za pośrednictwem portalu Azure obsługi migracji na żywo. Za portalu należy wybrać konto magazynu, który ma zostać przekonwertowany na ZRS.
1. Kliknij przycisk **nowe żądanie pomocy technicznej**
2. Sprawdź podstawy. Kliknij przycisk **Dalej**. 
3. Na **Problem** sekcji 
    - Pozostaw ważność jako — jest.
    - Typ problemu = **migracji danych**
    - Kategoria = **Migrowanie do ZRS w obrębie regionu**
    - Tytuł = **ZRS konta migracji** (lub coś opisową)
    - Szczegóły = I przeprowadzić migrację do ZRS z [LRS, GRS] w regionie ___. 
4. Kliknij przycisk **Dalej**.
5. Sprawdź poprawność w bloku informacje kontaktowe informacje kontaktowe.
6. Kliknij przycisk **przesłać**.

Pomocy technicznej będzie kontaktu użytkownik. Ta osoba będzie dostępna do pomocy, które mogą wymagać. 

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>Klasycznym ZRS: Starszych opcji nadmiarowości blokowych obiektów blob
> [!NOTE]
> Amortyzacja i wymagane migracji na 31 marca 2021 planuje się kontami ZRS klasycznego. Firma Microsoft będzie wysyłać szczegółowe klientom ZRS klasycznego przed wycofywanie. Firma Microsoft planuje umożliwiają proces automatycznej migracji z klasycznym ZRS ZRS w przyszłości.

>[!NOTE]
> Po ZRS [ogólnie dostępna](#support-coverage-and-regional-availability) w regionie, nie można utworzyć konto klasycznego ZRS z portalu, w tym samym regionie. Jednak można tworzyć jedną za pomocą innych środków, takich jak Microsoft PowerShell i interfejsu wiersza polecenia Azure, oznacza to, aż ZRS klasycznego jest przestarzały.

Magazyn ZRS klasycznego asynchronicznie replikuje dane między centrami danych w ramach jednej do dwóch regionach. Replika może być niedostępna, chyba że firma Microsoft zainicjuje przejście w tryb failover do regionu pomocniczego. Klasycznym ZRS jest dostępna tylko w przypadku **blokowe obiekty BLOB** w [V1 ogólnego przeznaczenia (GPv1)](../articles/storage/common/storage-account-options.md#general-purpose-v1) kont magazynu. Nie można przekonwertować konta klasycznego magazynu ZRS na konto magazynu LRS lub GRS ani odwrotnie, a ponadto konto klasycznego magazynu ZRS nie ma metryk ani funkcji rejestrowania.

Nie można przekonwertować ZRS klasycznych kont, do lub z LRS, GRS lub RA-GRS. Magazyn ZRS klasycznych kont nie obsługują również metryki lub rejestrowania.

Z konta LRS, ZRS klasycznego, GRS lub RA-GRS lub ręcznego przeprowadzania migracji danych konta ZRS, użyj narzędzia AzCopy, Eksploratora usługi Storage platformy Azure, programu Azure PowerShell lub wiersza polecenia platformy Azure. Można również tworzyć własne rozwiązania migracji z jednym z biblioteki klienta magazynu Azure.
