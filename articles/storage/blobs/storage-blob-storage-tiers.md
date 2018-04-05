---
title: Gorący, chłodny i archiwalny magazyn platformy Azure dla obiektów blob | Microsoft Docs
description: Gorący, chłodny i archiwalny magazyn dla kont usługi Azure Storage.
services: storage
documentationcenter: ''
author: kuhussai
manager: jwillis
editor: ''
ms.assetid: eb33ed4f-1b17-4fd6-82e2-8d5372800eef
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/11/2017
ms.author: kuhussai
ms.openlocfilehash: c62f3a92e6199f6467556054c9f58c20b6ceba2c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="azure-blob-storage-hot-cool-and-archive-storage-tiers"></a>Azure Blob Storage: warstwa magazynu gorącego, chłodnego i archiwalnego

## <a name="overview"></a>Omówienie

Usługa Azure Storage oferuje trzy warstwy magazynowania dla obiektów blob, co pozwala na najbardziej ekonomiczne przechowywanie danych w zależności od sposobu ich używania. **Warstwa magazynu gorącego** platformy Azure została zoptymalizowana pod kątem przechowywania danych, do których często uzyskuje się dostęp. **Warstwa magazynowania Chłodna** platformy Azure została zoptymalizowana pod kątem magazynowania danych używanych od czasu do czasu, które są przechowywane co najmniej przez 30 dni. **Warstwa magazynowania Archiwum** platformy Azure została zoptymalizowana pod kątem magazynowania rzadko używanych danych, przechowywanych co najmniej przez 180 dni, co do których obowiązują elastyczne wymagania dotyczące opóźnień (rzędu kilku godzin). Warstwa magazynowania Archiwum jest dostępna tylko na poziomie obiektu blob. Nie jest ona dostępna na poziomie konta magazynu. Dla danych w warstwie magazynu chłodnego nie ma znaczenia nieco niższa dostępność, ale nadal są wymagane wysoka trwałość oraz podobny czas dostępu i parametry przepływności jak w przypadku gorących danych. W przypadku chłodnych danych umowa SLA zapewniająca nieco niższą dostępność i wyższe koszty dostępu w porównaniu z gorącymi danymi to dopuszczalny kompromis w celu uzyskania niższych kosztów magazynowania. Magazyn Archiwum działa w trybie offline i ma najniższe koszty magazynowania, ale także najwyższe koszty dostępu. Na poziomie konta można ustawić tylko warstwy magazynu Gorąca i Chłodna (nie Archiwum). Wszystkie trzy warstwy można ustawić na poziomie obiektu.

Obecnie ilość danych przechowywanych w chmurze rośnie w tempie wykładniczym. Aby zarządzać kosztami zwiększających się potrzeb dotyczących magazynowania, warto zorganizować dane na podstawie atrybutów, takich jak częstotliwość dostępu i planowany okres przechowywania. Pozwoli to na optymalizację kosztów. Dane przechowywane w chmurze mogą być różne pod względem sposobu ich generowania i przetwarzania oraz uzyskiwania do nich dostępu przez cały okres ich istnienia. Do niektórych danych często uzyskuje się dostęp. Są one również często modyfikowane w trakcie całego okresu istnienia. Do niektórych danych często uzyskuje się dostęp na początkowym etapie istnienia, a z czasem już zdecydowanie rzadziej. Niektóre dane pozostają nieużywane w chmurze i dostęp do nich uzyskuje się rzadko (lub w ogóle) po umieszczeniu ich w magazynie.

W przypadku każdego z tych scenariuszy dostępu do danych istnieją korzyści płynące z różnych warstw magazynowania, zoptymalizowanych pod kątem określonego wzorca dostępu. Dzięki warstwom magazynowania Gorąca, Chłodna i Archiwum usługa Azure Blob Storage zaspokaja potrzebę korzystania ze zróżnicowanych warstw magazynowania z oddzielnymi modelami cenowymi.

## <a name="storage-accounts-that-support-tiering"></a>Konta magazynu z obsługą warstw

Dane magazynu obiektów można dzielić między warstwy Gorąca, Chłodna lub Archiwum tylko na kontach usługi Blob Storage lub ogólnego przeznaczenia w wersji 2 (GPv2). Konta ogólnego przeznaczenia w wersji 1 (GPv1) nie obsługują warstw. Klienci mogą jednak łatwo przekształcać istniejące konta GPv1 lub konta usługi Blob Storage w konta GPv2 w ramach prostego procesu uruchamianego jednym kliknięciem w witrynie Azure Portal. Konto GPv2 oferuje nową strukturę cen obiektów blob, plików i kolejek oraz dostęp do różnych nowych funkcji magazynu. Co więcej, w przyszłości niektóre nowe funkcje i rabaty cenowe będą oferowane tylko w przypadku kont GPv2. W związku z tym klienci powinni zastanowić się nad korzystaniem z konta GPv2, ale podjąć taką decyzję mogą tylko po zapoznaniu się z cenami wszystkich usług, ponieważ niektóre obciążenia mogą być droższe na kontach GPv2 niż na kontach GPv1. Aby dowiedzieć się więcej, zobacz [Opcje kont usługi Azure Storage](../common/storage-account-options.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Kona usługi Blob Storage i GPv2 ujawniają atrybut **Warstwa dostępu** na poziomie konta, co pozwala na określanie domyślnej warstwy magazynowania jako Gorąca lub Chłodna dla dowolnego obiektu blob na koncie magazynu, które nie ma warstwy ustawionej na poziomie obiektu. W przypadku obiektów z warstwą ustawioną na poziomie obiektu warstwa konta nie będzie stosowana. Warstwę Archiwum można stosować tylko na poziomie obiektu. W dowolnym momencie można przełączać się między tymi warstwami magazynowania.

## <a name="hot-access-tier"></a>Warstwa dostępu Gorąca

Koszty magazynowania w warstwie Gorąca są wyższe niż w warstwach Chłodna i Archiwum, ale ma ona najniższe koszty dostępu. Przykładowe scenariusze użycia dotyczące warstwy magazynu gorącego obejmują:

* Dane, które są często używane lub przewiduje się do nich częsty dostęp (odczyt i zapis danych).
* Dane, które są przygotowywane do przetwarzania i ewentualnej migracji do warstwy magazynu chłodnego.

## <a name="cool-access-tier"></a>Warstwa dostępu Chłodna

Warstwa magazynowania Chłodna ma niższe koszty magazynowania i wyższe koszty dostępu w porównaniu do warstwy Gorąca. Ta warstwa jest przeznaczona dla danych, które pozostaną w warstwie Chłodna przez co najmniej 30 dni. Przykładowe scenariusze użycia dotyczące warstwy magazynu chłodnego obejmują:

* Krótkoterminowe kopie zapasowe i zestawy danych odzyskiwania po awarii.
* Starszą zawartość nośników, która nie jest już często wyświetlana, ale oczekiwane jest, że będzie ona natychmiast dostępna, gdy będzie to wymagane.
* Duże zbiory danych, które muszą być przechowywane w sposób ekonomiczny, podczas gdy na potrzeby przyszłego przetwarzania zbierana jest większa ilość danych (*np.* długoterminowe magazynowanie danych naukowych lub nieprzetworzonych danych telemetrycznych z zakładu produkcyjnego).

## <a name="archive-access-tier"></a>Warstwa dostępu Archiwum

W warstwie Archiwum koszt przechowywania jest najniższy, a koszt pobierania danych jest wyższy niż w przypadku magazynu w warstwie Gorąca i Chłodna. Ta warstwa jest przeznaczona dla danych, które można pobierać z opóźnieniem kilku godzin i które pozostaną w warstwie Archiwum przez co najmniej 180 dni.

Gdy obiekt blob znajduje się w magazynie w warstwie Archiwum, jest on w trybie offline i nie można go odczytać (z wyjątkiem metadanych, które są w trybie online i dostępne), skopiować, zastąpić ani zmodyfikować. Nie można również tworzyć migawek takiego obiektu blob. Można jednak używać istniejących operacji do usuwania lub wyświetlania listy obiektów blob, pobierania ich właściwości/metadanych albo zmiany warstwy obiektu blob.

Przykładowe scenariusze użycia archiwalnej warstwy magazynowania obejmują:

* Długoterminowe kopie zapasowe, dodatkowe kopie zapasowej i archiwalne zestawy danych
* Oryginalne (nieprzetworzone) dane, które muszą zostać zachowane, nawet po przetworzeniu ich do ostatecznej użytecznej postaci (*np.* nieprzetworzone pliki multimedialne po transkodowaniu do innych formatów).
* Dane zgodności i dane archiwalne, które muszą być przechowywane przez długi czas, i do których bardzo rzadko uzyskuje się dostęp (*np.* zapisy z kamer monitorujących, stare zdjęcia rentgenowskie lub zdjęcia z rezonansu magnetycznego dla organizacji opieki zdrowotnej, nagrania audio i zapisy rozmów telefonicznych z klientami dla firm z branży usług finansowych).

### <a name="blob-rehydration"></a>Ponowne wypełnianie obiektów blob
Aby odczytać dane w magazynie archiwalnym, należy najpierw zmienić warstwę obiektu blob na gorącą lub chłodną. Proces ten, określany jako „ponowne wypełnianie” (ang. rehydration), może potrwać do 15 godzin. W celu uzyskania optymalnej wydajności zaleca się korzystanie z dużych rozmiarów obiektów blob. Ponowne wypełnianie kilku małych obiektów blob równocześnie może spowodować dalsze przedłużenie czasu.

Podczas ponownego wypełniania można sprawdzać właściwość obiektu blob **Stan archiwum**, aby upewnić się, że warstwa została zmieniona. Właściwość ta ma wartość „rehydrate-pending-to-hot” (ponowne wypełnianie w celu przejścia do warstwy gorącej) lub „rehydrate-pending-to-cool” (ponowne wypełnianie w celu przejścia do warstwy chłodnej) w zależności od warstwy docelowej. Po zakończeniu tego procesu właściwość obiektu blob „stan archiwum” jest usuwana, a wartość właściwości **Warstwa dostępu** odpowiada nowej warstwie Gorąca lub Chłodna.  

## <a name="blob-level-tiering"></a>Obsługa warstw na poziomie obiektów blob

Funkcja obsługi warstw na poziomie obiektów blob umożliwia zmianę warstwy danych na poziomie obiektu przy użyciu jednej operacji o nazwie [Ustawianie warstwy obiektu blob](/rest/api/storageservices/set-blob-tier). W odpowiedzi na zmiany wzorców użycia można łatwo zmieniać warstwy dostępu do obiektu blob (wybierając warstwę gorącą, chłodną lub archiwalną) bez przenoszenia danych między kontami. Wszystkie zmiany warstw są stosowane natychmiast z wyjątkiem ponownego wypełniania obiektu blob z archiwum (ten proces może potrwać kilka godzin). Czas ostatniej zmiany warstwy obiektu blob jest uwidaczniany za pomocą właściwości obiektu blob **Czas zmiany warstwy dostępu**. Jeśli obiekt blob znajduje się w warstwie Archiwum, nie można go zastąpić. W związku z tym przekazanie tego samego obiektu blob nie jest dozwolone w tym scenariuszu. Zastąpienie obiektu blob jest możliwe w warstwach Gorąca i Chłodna. W takim przypadku nowy obiekt blob dziedziczy warstwę starego, zastąpionego obiektu blob.

W obrębie jednego konta jest możliwe współistnienie obiektów blob należących do wszystkich trzech warstw magazynowania. Obiekt blob bez jawnie przypisanej warstwy korzysta z warstwy zgodnie z ustawieniem warstwy dostępu konta. Jeśli warstwa dostępu jest wnioskowana z konta, właściwość obiektu blob **Wywnioskowana warstwa dostępu** ma wartość „prawda”, a właściwość obiektu blob **Warstwa dostępu** jest zgodna z warstwą konta. W witrynie Azure Portal właściwość Wywnioskowana warstwa dostępu jest wyświetlana z warstwą dostępu obiektu blob, np. Gorąca (wywnioskowana) lub Chłodna (wywnioskowana).

> [!NOTE]
> Magazyn Archiwum i funkcja obsługi warstw na poziomie obiektów blob obsługują tylko blokowe obiekty blob. Nie można także zmienić warstwy blokowego obiektu blob, który ma migawki.

### <a name="blob-level-tiering-billing"></a>Rozliczanie obsługi warstw na poziomie obiektów blob

Gdy obiekt blob jest przenoszony do chłodniejszej warstwy (Gorąca -> Chłodna, Gorąca -> Archiwum lub Chłodna -> Archiwum), operacja jest rozliczana jako zapis w warstwie docelowej i naliczane są opłaty za operacje zapisu (za 10 000 operacji) i za zapis danych (za GB) zgodnie z cennikiem warstwy docelowej. Gdy obiekt blob jest przenoszony do cieplejszej warstwy (Archiwum -> Chłodna, Archiwum -> Gorąca lub Chłodna -> Gorąca), operacja jest rozliczana jako odczyt z warstwy źródłowej i naliczane są opłaty za operacje odczytu (za 10 000 operacji) i za pobieranie danych (za GB) zgodnie z cennikiem warstwy źródłowej.

W przypadku przełączania warstwy konta z Gorąca na Chłodna opłaty zostaną naliczone za operacje zapisu (za 10 000 operacji) dla wszystkich obiektów blob bez ustawionej warstwy tylko na kontach GPv2. Za tę operację wykonywaną na kontach usługi Blob Storage nie ma opłat. Zostanie naliczona opłata za operacje odczytu (za 10 000 operacji) i pobieranie danych (za GB), jeśli przełączasz konta magazynu usługi Blob Storage lub GPv2 z warstwy Chłodna na Gorąca. Mogą również obowiązywać opłaty za wczesne usunięcie dla dowolnego obiektu przeniesionego z warstwy Chłodna lub Archiwum.

### <a name="cool-and-archive-early-deletion"></a>Opłaty za wcześniejsze usunięcie w warstwach Chłodna i Archiwum

Oprócz opłaty miesięcznej za GB, każdy obiekt blob przenoszony do warstwy Chłodna (tylko konta GPv2) jest objęty okresem wcześniejszego usunięcia z warstwy Chłodna przez 30 dni, a każdy obiekt przenoszony do warstwy Archiwum jest objęty okresem wcześniejszego usunięcia z warstwy Chłodna przez 180 dni. Ta opłata jest naliczana proporcjonalnie. Jeśli na przykład obiekt blob zostanie przeniesiony do warstwy Archiwum, a następnie usunięty lub przeniesiony do warstwy Gorąca po 45 dniach, zostanie naliczona opłata za wczesne usunięcie odpowiadająca 135 (180 minus 45) dniom przechowywania tego obiektu blob w archiwum.

## <a name="comparison-of-the-storage-tiers"></a>Porównanie warstw magazynowania

W poniższej tabeli przedstawiono porównanie warstw magazynowania Gorąca, Chłodna i Archiwum.

| | **Warstwa magazynu gorącego** | **Warstwa magazynu chłodnego** | **Warstwa magazynowania Archiwum**
| ---- | ----- | ----- | ----- |
| **Dostępność** | 99,9% | 99% | Nie dotyczy |
| **Dostępność** <br> **(odczyty RA-GRS)**| 99,99% | 99,9% | Nie dotyczy |
| **Opłaty za użycie** | Wyższe koszty magazynowania, niższe koszty dostępu i transakcji | Niższe koszty magazynowania, wyższe koszty dostępu i transakcji | Najniższe koszty magazynowania, najwyższe koszty dostępu i transakcji |
| **Minimalny rozmiar obiektu** | Nie dotyczy | Nie dotyczy | Nie dotyczy |
| **Minimalny czas magazynowania** | Nie dotyczy | 30 dni (tylko GPv2) | 180 dni
| **Opóźnienie** <br> **(czas do pierwszego bajtu)** | milisekundy | milisekundy | mniej niż 15 godz.
| **Cele dotyczące skalowalności i wydajności** | Takie same jak w przypadku kont magazynu ogólnego przeznaczenia | Takie same jak w przypadku kont magazynu ogólnego przeznaczenia | Takie same jak w przypadku kont magazynu ogólnego przeznaczenia |

> [!NOTE]
> Konta usługi Blob Storage obsługują te same cele wydajności i skalowalności co konta magazynu ogólnego przeznaczenia. Aby uzyskać więcej informacji, zobacz [Azure Storage Scalability and Performance Targets](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Cele dotyczące skalowalności i wydajności usługi Magazyn Azure).

## <a name="quickstart-scenarios"></a>Scenariusze typu Szybki start

W tej sekcji przedstawiono następujące scenariusze obejmujące użycie witryny Azure Portal:

* Jak zmienić domyślną warstwę dostępu konta GPv2 lub usługi Blob Storage.
* Jak zmienić warstwę obiektu blob na koncie GPv2 lub usługi Blob Storage.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Zmienianie domyślnej warstwy dostępu konta GPv2 lub usługi Blob Storage

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Aby przejść do konta magazynu, zaznacz pozycję Wszystkie zasoby, a następnie wybierz konto magazynu.

3. W bloku Ustawienia kliknij pozycję **Konfiguracja**, aby wyświetlić i/lub zmienić konfigurację konta.

4. Wybierz odpowiednią dla Twoich potrzeb warstwę magazynu: ustaw pozycję **Warstwa dostępu** na wartość **Chłodna** lub **Gorąca**.

5. Kliknij pozycję Zapisz w górnej części bloku.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Zmienianie warstwy obiektu blob na koncie GPv2 lub usługi Blob Storage

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Aby przejść do obiektu blob na koncie magazynu, wybierz pozycję Wszystkie zasoby, wybierz konto magazynu, wybierz kontener, a następnie wybierz obiekt blob.

3. W bloku Właściwości obiektu blob kliknij menu rozwijane **Warstwa dostępu**, aby wybrać warstwę magazynowania **Gorąca**, **Chłodna** lub **Archiwum**.

5. Kliknij pozycję Zapisz w górnej części bloku.

## <a name="faq"></a>Często zadawane pytania

**Czy używać konta usługi Blob Storage lub GPv2, jeśli chcę obsługiwać warstwy danych?**

Zalecamy korzystanie z kont GPv2 zamiast kont usługi Blob Storage na potrzeby obsługi warstw. Konta GPv2 obsługują wszystkie funkcje, które obsługują konta usługi Blob Storage, i wiele innych. Ceny kont usługi Blob Storage i GPv2 są niemal identyczne, ale niektóre nowe funkcje i rabaty cenowe będą dostępne tylko na kontach GPv2. Konta GPv1 nie obsługują warstw.

Struktury cen kont GPv1 i GPv2 są różne, a klienci powinni dokładnie je sprawdzić przed podjęciem decyzji o użyciu kont GPv2. Istniejące konto usługi Blob Storage lub GPv1 można łatwo przekonwertować na konto GPv2 w ramach prostego procesu uruchamianego jednym kliknięciem. Aby dowiedzieć się więcej, zobacz [Opcje kont usługi Azure Storage](../common/storage-account-options.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

**Czy mogę przechowywać obiekty we wszystkich trzech warstwach (Gorąca, Chłodna i Archiwum) na tym samym koncie?**

Tak. Atrybut **Warstwa dostępu** ustawiany na poziomie konta to domyślna warstwa mająca zastosowanie do wszystkich obiektów na tym koncie bez jawnie ustawionej warstwy. Jednak obsługa warstw na poziomie obiektów blob umożliwia ustawienie warstwy dostępu na poziomie obiektu niezależnie od ustawienia warstwy dostępu konta. Obiekty blob mogą istnieć w ramach tego samego konta w jednej z trzech warstw magazynowania (Gorąca, Chłodna lub Archiwum).

**Czy mogę zmienić domyślną warstwę magazynowania na moim koncie usługi Blob Storage lub koncie magazynu GPv2?**

Tak, zmiana domyślnej warstwy magazynowania jest możliwa przez ustawienie atrybutu **Warstwa dostępu** dla konta magazynu. Zmiana warstwy magazynowania będzie miała zastosowanie do wszystkich obiektów przechowywanych na koncie, które nie mają jawnie ustawionej warstwy. Zmiana warstwy magazynowania z Gorąca na Chłodna spowoduje naliczenie opłat za operacje zapisu (za 10 000 operacji) dla wszystkich obiektów blob bez ustawionej warstwy tylko na kontach GPv2. Natomiast zmiana warstwy z Chłodna na Gorąca spowoduje naliczenie opłat za operacje odczytu (za 10 000 operacji) i pobieranie danych (za GB) dla wszystkich obiektów blob na kontach usługi Blob Storage i GPv2.

**Czy mogę ustawić domyślną warstwę dostępu do konta na Archiwum?**

Nie. Tylko warstwę magazynowania Gorąca lub Chłodna można ustawić jako domyślną warstwę dostępu do konta. Archiwum można ustawić tylko na poziomie obiektu.

**W którym regionach są dostępne warstwy magazynowania Gorąca, Chłodna i Archiwum?**

Warstwy magazynowania Gorąca i Chłodna oraz obsługa warstw na poziomie obiektu blob są dostępne we wszystkich regionach. Magazyn w warstwie Archiwum będzie początkowo dostępny tylko w wybranych regionach. Pełną listę można znaleźć w temacie [Dostępność produktów platformy Azure według regionów](https://azure.microsoft.com/regions/services/).

**Czy obiekty blob w chłodnej warstwie magazynowania działają inaczej niż obiekty blob w gorącej warstwie magazynowania?**

Obiekty blob w gorącej warstwie magazynowania mają takie samo opóźnienie jak obiekty blob na kontach GPv1, GPv2 i usługi Blob Storage. Obiekty blob w chłodnej warstwie magazynowania mają podobne opóźnienie (w milisekundach) jak obiekty blob na kontach GPv1, GPv2 i usługi Blob Storage. Obiekty blob w warstwie magazynowania Archiwum mają kilka godzin opóźnienia na kontach GPv1, GPv2 i usługi Blob Storage.

Obiekty blob w chłodnej warstwie magazynowania będą miały nieco niższy poziom dostępności usług (umowa SLA) niż obiekty blob przechowywane w gorącej warstwie magazynowania. Aby uzyskać więcej szczegółów, zobacz [Magazyn — umowa SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Czy operacje w warstwach Gorąca, Chłodna i Archiwum są takie same?**

Tak. Wszystkie operacje w warstwach Gorąca i Chłodna są w 100% spójne. Wszystkie prawidłowe operacje w warstwie Archiwum, w tym usuwanie, tworzenie listy, pobieranie właściwości/metadanych obiektów blob i ustawianie warstwy obiektu blob, są w 100% spójne z warstwami Gorąca i Chłodna. Obiektu blob znajdującego się w warstwie Archiwum nie można odczytywać ani modyfikować.

**Kiedy podczas ponownego wypełniania obiektu blob z warstwy Archiwum do warstwy Gorąca lub Chłodna będę wiedzieć, że ten proces został ukończony?**

Podczas ponownego wypełniania można używać operacji pobierania właściwości obiektu blob, aby sondować atrybut **Stan archiwum** w celu potwierdzenia ukończenia zmiany warstwy. Właściwość ta ma wartość „rehydrate-pending-to-hot” (ponowne wypełnianie w celu przejścia do warstwy gorącej) lub „rehydrate-pending-to-cool” (ponowne wypełnianie w celu przejścia do warstwy chłodnej) w zależności od warstwy docelowej. Po zakończeniu tego procesu właściwość obiektu blob „stan archiwum” jest usuwana, a wartość właściwości **Warstwa dostępu** odpowiada nowej warstwie Gorąca lub Chłodna.  

**Kiedy po ustawieniu warstwy obiektu blob rozpocznie się naliczanie opłat przy użyciu odpowiedniej stawki?**

Opłaty za każdy obiekt blob są zawsze naliczane zgodnie z warstwą określoną przez właściwość obiektu blob **Warstwa dostępu**. W przypadku ustawiania nowej warstwy obiektu blob właściwość **Warstwa dostępu** będzie natychmiast odzwierciedlać nową warstwę dla wszystkich przejść, z wyjątkiem ponownego wypełniania obiektu blob z warstwy Archiwum do warstwy Gorąca lub Chłodna, które może potrwać kilka godzin. W takim przypadku opłaty będą nadal naliczane przy użyciu stawek warstwy Archiwum do momentu ukończenia ponownego wypełniania, gdy właściwość **Warstwa dostępu** zacznie odzwierciedlać nową warstwę. Dopiero wtedy opłaty będą naliczane przy użyciu stawek nowej warstwy Gorąca lub Chłodna.

**Jak mogę ustalić, czy będę płacić za wczesne usunięcie w przypadku usunięcia lub przeniesienia obiektu blob z warstwy Chłodna lub Archiwum?**

Proporcjonalne opłaty za wczesne usunięcie będą dotyczyć wszystkich obiektów blob usuniętych lub przeniesionych z warstwy Chłodna (tylko konta GPv2) lub Archiwum przed upływem odpowiednio 30 lub 180 dni. Aby określić, jak długo obiekt blob znajdował się w warstwie Chłodna lub Archiwum, można sprawdzić właściwość **Czas zmiany warstwy dostępu**, która udostępnia znacznik czasu ostatniej zmiany warstwy. Więcej szczegółów można znaleźć w sekcji dotyczącej [wczesnego usunięcia z warstwy Chłodna i Archiwum](#cool-and-archive-early-deletion).

**Które narzędzia i zestawy SDK platformy Azure obsługują warstwy na poziomie obiektów blob i magazyn w warstwie Archiwum?**

Następujące rozwiązania: witryna Azure Portal, program PowerShell, narzędzia interfejsu wiersza polecenia oraz biblioteki klienta środowisk .NET, Java, Python i Node.js obsługują warstwy na poziomie obiektów blob i magazyn w warstwie Archiwum.  

**Ile danych można przechowywać w warstwach Gorąca, Chłodna i Archiwum?**

Limity magazynu danych i inne są ustawiane na poziomie konta, a nie na poziomie warstwy magazynowania. Dlatego można wybrać opcję użycia wszystkich limitów w jednej warstwie lub we wszystkich trzech warstwach. Aby uzyskać więcej informacji, zobacz [Azure Storage Scalability and Performance Targets](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Cele dotyczące skalowalności i wydajności usługi Magazyn Azure).

## <a name="next-steps"></a>Następne kroki

### <a name="evaluate-hot-cool-and-archive-in-gpv2-blob-storage-accounts"></a>Ocena warstw Gorąca, Chłodna i Archiwum na kontach GPv2 i usługi Blob Storage

[Sprawdzanie dostępności warstw Gorąca, Chłodna i Archiwum według regionu](https://azure.microsoft.com/regions/#services)

[Ocena użycia bieżących kont magazynu przez włączenie metryk usługi Azure Storage](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Sprawdzanie cen warstw Gorąca, Chłodna i Archiwum na kontach usługi Blob Storage i GPv2 według regionu](https://azure.microsoft.com/pricing/details/storage/)

[Sprawdzanie ceny transferu danych](https://azure.microsoft.com/pricing/details/data-transfers/)
