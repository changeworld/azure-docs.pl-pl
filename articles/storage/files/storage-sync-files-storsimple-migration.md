---
title: Migrowanie z StorSimple do Azure File Sync
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: fauhse
ms.subservice: files
description: StorSimple jest produktem końcowym, a Azure File Sync to rozwiązanie do migracji do programu. Dowiedz się więcej na temat koncepcji migracji i uzyskaj dostęp do AzureFiles@microsoft.com na potrzeby niestandardowej pomocy dotyczącej migracji.
ms.openlocfilehash: edad4d1c6be2c39dbf8150b6ab8979ae3924fb53
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666665"
---
# <a name="storsimple-migration-to-azure-file-sync"></a>StorSimple migrację do Azure File Sync

StorSimple to wycofany produkt firmy Microsoft. Rozszerzona pomoc techniczna dla tego produktu i jego usługi w chmurze będzie trwać do końca 2022.
Ważne jest, aby zacząć planowanie migracji od StorSimple od razu.

Azure File Sync do programu można migrować domyślne i strategiczne usługi platformy Azure StorSimple. Jest to ogólnie dostępna usługa platformy Azure z zestawem funkcji StorSimple.

## <a name="full-cloud-side-migration-with-limited-downtime"></a>Pełna migracja po stronie chmury z ograniczoną ilością przestojów
W tym artykule opisano koncepcję rozpoczęcia migracji.
Należy pamiętać, że klienci, którzy chcą przeprowadzić migrację z StorSimple i na Azure File Sync nie muszą samodzielnie wykonywać.

> [!IMPORTANT]
> Firma Microsoft dokłada starań, aby pomóc klientom w migracji. AzureFiles@microsoft.com e-mail dla niestandardowego planu migracji oraz pomoc podczas migracji.

## <a name="migration-approach"></a>Podejście do migracji
Migracja do Azure File Sync rozpocznie się po stronie chmury z minimalnym wpływem na środowisko lokalne i ograniczone przestoje.
Poniższe pojęcie koncepcji dotyczy urządzeń z serii StorSimple 8000.
Jeśli potrzebujesz migracji z serii StorSimple 7000, pierwszy krok polega na bezpłatnym uaktualnieniu do zgodnego urządzenia z pożyczką z serii 8000 od firmy Microsoft.
Skontaktuj się z AzureFiles@microsoft.com i pomożemy Ci zorganizować odpowiednią liczbę urządzeń kredytowych.

### <a name="general-approach"></a>Ogólne podejście
![+](media/storage-sync-files-storsimple-migration/storsimple-docs-overview-concept.png "Przedstawianie migracji po stronie chmury za pomocą tymczasowego urządzenia wirtualnego i systemu Windows Server do nowego lokalnego systemu Windows Server zastępującego lokalne urządzenie StorSimple")

1. Wykonaj klon woluminu lokalnego urządzenia StorSimple i zainstaluj go na tymczasowym urządzeniu wirtualnym StorSimple.
2. Połącz urządzenie wirtualne za pośrednictwem interfejsu iSCSI z tymczasową maszyną wirtualną platformy Azure.
3. Zainstaluj Azure File Sync na tymczasowej maszynie wirtualnej z systemem Windows Server — należy również ustawić określony klucz rejestru dla tej migracji przed skonfigurowaniem synchronizacji na serwerze.
    * W zależności od liczby udziałów na woluminie StorSimple należy wdrożyć je tak, jak wiele udziałów plików platformy Azure. (Zalecamy wdrożenie jednego udziału plików platformy Azure na konto magazynu).
    * Skonfiguruj synchronizację między pojedynczym udziałem na maszynie wirtualnej w chmurze systemu Windows Server i udziałem plików platformy Azure. (mapowanie 1:1)
    * Opcjonalnie można dodać serwer lokalny jako lokalną pamięć podręczną wydajności z włączonymi warstwami chmury. Ten krok jest niezbędny, jeśli chcesz zastąpić StorSimple lokalnie, używając większej funkcjonalności, lokalnej pamięci podręcznej, obsługiwanej przez system Windows Server i obsługę warstw chmur Azure File Sync. Lokalny serwer systemu Windows może być komputerem fizycznym lub klastrem lub maszyną wirtualną. Nie trzeba mieć tak dużej ilości magazynu, który został wdrożony jako rozmiar zestawu danych. Potrzebuje on tylko wystarczającej ilości miejsca do przechowywania lokalnie w pamięci podręcznej najczęściej używanych plików.

## <a name="minimizing-downtime"></a>Minimalizacja przestojów
Po kroku 3 powyżej StorSimple urządzenie lokalne jest nadal używane przez użytkowników i aplikacje. Dlatego zestaw plików synchronizowanych z początkowego klon woluminu jest nieco nieaktualny po zakończeniu synchronizacji.
Podejście do minimalizowania przestoju polega na powtórzeniu synchronizacji procesu klonowania woluminu, aby synchronizacja była szybsza i szybsza z każdą iteracją, która z kolei jest włączona, ponieważ zmiany między klonami woluminów stają się mniejsze i mniejsze.
Możesz powtórzyć ten proces do momentu, aż synchronizacja z sklonowanego woluminu będzie możliwa w czasie, w którym można znaleźć przestoje.
W takim przypadku użytkownicy i aplikacje nie mogą wprowadzać żadnych zmian na urządzeniu StorSimple. Rozpocznie się przestoje.
Wykonaj kolejną klon woluminu i pozwól na synchronizację z serwerami, które są połączone.
Ustanów dostęp do swoich użytkowników i aplikacji do nowych, Azure File Sync z systemem Windows Server.
Rozważ wdrożenie/dostosowanie przestrzeni nazw systemu plików DFS w celu przecięcia ze starego urządzenia StorSimple na nowy serwer z systemem Windows niewidoczny dla aplikacji i użytkowników.
Migracja została ukończona.

## <a name="migration-goal"></a>Cel migracji
Po zakończeniu migracji można anulować obsługę tymczasowego urządzenia wirtualnego StorSimple oraz maszyny wirtualnej platformy Azure.

Ponadto urządzenie lokalne StorSimple może zostać anulowane, ponieważ użytkownicy i aplikacje mają już dostęp do systemu Windows Server.
Na poniższej ilustracji przedstawiono pozostałą zawartość. Standardowe wdrożenie Azure File Sync zawiera wiele udziałów plików platformy Azure i serwerów z systemem Windows podłączonych do nich za pośrednictwem Azure File Sync. Należy pamiętać, że jeden serwer może łączyć różne foldery lokalne z różnymi udziałami plików w tym samym czasie.
Ponadto jeden udział plików platformy Azure może być synchronizowany z wieloma różnymi serwerami, w przypadku gdy potrzebne są dane buforowane w biurach oddziałów. Sprawdź również, czy możesz zoptymalizować zasady dotyczące warstw w chmurze, aby zwiększyć efektywność korzystania z lokalnego miejsca do magazynowania.

![+](media/storage-sync-files-storsimple-migration/storsimple-docs-goal.PNG "Ilustracja przedstawiająca cel po zakończeniu migracji. Przedstawia on wiele udziałów plików synchronizowanych z lokalnym serwerem systemu Windows z użytkownikami i aplikacjami, które uzyskują dostęp do plików w chmurze lub na serwerze z systemem Windows.")

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z Azure Files i Azure File Sync. Ważne jest zrozumienie Azure File Sync terminologii i wzorca wdrażania dla pomyślnej migracji. Dla każdego kroku w tym artykule omówiono więcej szczegółowych informacji. Przed zaplanowaniem i wykonaniem migracji należy skontaktować się z firmą Microsoft w celu uzyskania dostosowanej pomocy.

> [!IMPORTANT]
> Firma Microsoft dokłada starań, aby pomóc klientom w migracji. AzureFiles@microsoft.com e-mail dla niestandardowego planu migracji oraz pomoc podczas migracji.

## <a name="additional-resources"></a>Zasoby dodatkowe
Azure File Sync, ponieważ usługa docelowa ma dwa podstawowe dokumenty, które zalecamy przeczytać, jeśli dopiero zaczynasz Azure File Sync.
* [Azure File Sync — przegląd](storage-sync-files-planning.md)
* [Azure File Sync — Przewodnik wdrażania](storage-sync-files-deployment-guide.md)

Azure Files to usługa magazynu na platformie Azure, która oferuje udziały plików jako usługę. Nie trzeba uiszczać ani obsługiwać maszyny wirtualnej ani skojarzonej z nią magazynu maszyn wirtualnych.
* [Azure Files — przegląd](storage-files-introduction.md)
* [Azure Files — jak wdrożyć udział plików platformy Azure](storage-how-to-create-file-share.md)