---
title: Tablica wirtualnego StorSimple awaryjnego odzyskiwania i urządzenia trybu failover | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o sposobie pracy awaryjnej tablica wirtualne StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 3c1f9c62-af57-4634-a0d8-435522d969aa
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 12079f8dbc409afe5acc274fa08bda878c90b76e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927768"
---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array-via-azure-portal"></a>Awaryjnego odzyskiwania i urządzenia w trybie failover tablica wirtualnego StorSimple za pośrednictwem portalu Azure

## <a name="overview"></a>Omówienie
W tym artykule opisano odzyskiwania po awarii dla programu Microsoft Azure StorSimple wirtualnego tablicy szczegółowy opis kroków w tym do trybu failover do innej tablicy wirtualnego. Tryb failover służy do przenoszenia danych z *źródła* urządzenie w centrum danych do *docelowej* urządzenia. Urządzenie docelowe może znajdować się w tej samej lub innej lokalizacji geograficznej. Tryb failover urządzeń jest przeznaczony dla wszystkich danych z urządzenia. Podczas pracy w trybie failover danych w chmurze dla urządzenia źródłowego zmiany prawa własności do tego urządzenia docelowego.

Ten artykuł dotyczy tablice wirtualnego StorSimple tylko. Aby przełączyć urządzenie 8000 series, przejdź do [urządzenia trybu failover i odzyskiwania po awarii urządzenia StorSimple](storsimple-device-failover-disaster-recovery.md).

## <a name="what-is-disaster-recovery-and-device-failover"></a>Co to jest tryb failover odzyskiwania i urządzenia po awarii?

W przypadku odzyskiwanie po awarii urządzenie podstawowe przestanie działać. W tym scenariuszu można przenieść skojarzone z urządzeniem nie powiodło się na innym urządzeniu danych w chmurze. Można użyć urządzenie podstawowe jako *źródła* i określ inne urządzenie jako *docelowej*. Ten proces jest nazywany *pracy awaryjnej*. Podczas pracy awaryjnej wszystkie woluminy lub udziały z urządzenia źródłowego zmieniają właściciela i są przenoszone do urządzenia docelowego. Bez filtrowania danych jest dozwolone.

DR ma formę przywracania pełne urządzenia przy użyciu interakcji opartych na mapie warstwy i śledzenia. Mapa cieplna zdefiniowano przypisując wartość cieplna danych oparte na odczyt i zapis wzorce. Ta cieplna mapy następnie warstw najniższy fragmentów danych cieplna do chmury najpierw przy zachowaniu wysokiej cieplna (najczęściej używane) fragmentów danych w warstwie lokalnej. Podczas odzyskiwania po awarii StorSimple używa Mapa cieplna przywracania oraz rehydrate danych z chmury. Urządzenie pobiera wszystkie woluminy/udziały w ostatnim ostatniej kopii zapasowej (zgodnie z określoną wewnętrznie) i przeprowadza Przywracanie z kopii zapasowej. Tablica wirtualnego organizuje cały proces odzyskiwania po awarii.

> [!IMPORTANT]
> Urządzenia są usuwane po zakończeniu pracy awaryjnej urządzenia i dlatego powrotu po awarii nie jest obsługiwany.
> 
> 

Odzyskiwanie po awarii jest zorkiestrowana za pomocą funkcji trybu failover urządzenia i zainicjowano **urządzeń** bloku. Ten blok rejestruje wszystkie urządzenia StorSimple połączona z usługą Menedżera urządzeń StorSimple. Dla każdego urządzenia widać, przyjazną nazwę, stan, elastycznie i maksymalną pojemność, typ i modelu.

## <a name="prerequisites-for-device-failover"></a>Wymagania wstępne dotyczące urządzeń w pracy awaryjnej

### <a name="prerequisites"></a>Wymagania wstępne

Dla urządzenia trybu failover upewnij się, że są spełnione następujące wymagania wstępne:

* Urządzenia źródłowego musi być w **dezaktywowane** stanu.
* Docelowe urządzenie musi wyświetlane jako **przejść do konfigurowania** w portalu Azure. Udostępnianie wirtualnego tablicy docelowej pojemności tego samego lub wyższego. Konfigurowanie i pomyślnie zarejestrować docelowa tablica wirtualnych za pomocą lokalnego interfejsu użytkownika sieci web.
  
  > [!IMPORTANT]
  > Nie należy podejmować prób skonfigurowania zarejestrowanego urządzenia wirtualnego za pośrednictwem usługi. Konfiguracja urządzenia nie powinny być wykonywane za pośrednictwem usługi.
  > 
  > 
* Urządzenie docelowe nie może mieć taką samą nazwę jak urządzenia źródłowego.
* Urządzenie źródłowe i docelowe muszą być tego samego typu. Możesz tylko w trybie Failover wirtualnego tablicy, skonfigurowany jako serwer plików do innego serwera plików. To samo dotyczy serwera iSCSI.
* Dla serwera plików odzyskiwania po awarii zaleca się dołączenie urządzenia docelowego do tej samej domeny jako źródła. Taka konfiguracja powoduje, że uprawnienia udziału zostały automatycznie rozwiązane. Tylko w trybie failover na urządzeniu docelowym w tej samej domenie.
* Urządzenia dostępnych do odzyskiwania po awarii są urządzeń mających samej lub większej pojemności w porównaniu do urządzenia źródłowego. Urządzenia, które są połączone z usługą, ale nie spełniają kryteria wystarczającą ilość miejsca nie są dostępne jako urządzenia docelowe.

### <a name="other-considerations"></a>Inne zagadnienia

* Dla planowanego trybu failover 
  
  * Zaleca się zająć wszystkie woluminy lub udziały na urządzenia źródłowego w trybie offline.
  * Zalecamy tworzenie kopii zapasowej urządzenia, a następnie kontynuować pracę w trybie failover, aby zminimalizować ryzyko utraty danych. 
* W przypadku nieplanowanego trybu failover urządzenie używa najnowszej kopii zapasowej do przywracania danych.

### <a name="device-failover-prechecks"></a>Urządzenie prechecks trybu failover

Przed rozpoczęciem odzyskiwania po awarii urządzenie wykonuje prechecks. Te sprawdzenia pomocy, upewnij się, że nie występują błędy podczas odzyskiwania po awarii rozpocznie się. Prechecks obejmują:

* Sprawdzanie poprawności konta magazynu.
* Sprawdzanie połączenia chmury Azure.
* Sprawdzanie miejsca na urządzeniu docelowym.
* Sprawdzanie, czy wolumin urządzenia iSCSI serwer źródłowy ma
  
  * Prawidłowe nazwy ACR.
  * Prawidłowe nazwy IQN (nie przekracza 220 znaków).
  * Nieprawidłowy protokół CHAP hasła (maksymalnie 12-16 znaków).

Żadnego z poprzednim prechecks niepowodzenie, nie można kontynuować odzyskiwania po awarii. Rozwiąż te problemy, a następnie ponów próbę odzyskiwania po awarii.

Po pomyślnym ukończeniu DR własność danych w chmurze na urządzenia źródłowego jest przekazywana do urządzenia docelowego. Urządzenia źródłowego następnie nie jest już dostępne w portalu. To zablokowanie dostępu do wszystkich woluminów/udziałów na urządzenia źródłowego i docelowego urządzenia staje się aktywny.

> [!IMPORTANT]
> Jeśli urządzenie nie jest już dostępna, które udostępnione w systemie hosta maszyny wirtualnej nadal korzysta z zasobów. Po pomyślnym wykonaniu DR można usunąć tej maszyny wirtualnej z systemu hosta.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Przełączyć wirtualnego tablicy

Zaleca się udostępniania, skonfigurować i zarejestrować innej tablicy wirtualnych StorSimple z usługą Menedżera urządzeń StorSimple, przed rozpoczęciem tej procedury.

> [!IMPORTANT]
> 
> * Nie można się przełączone awaryjnie z serii StorSimple 8000 urządzenia do urządzenia wirtualnego 1200.
> * Możesz w trybie failover z urządzenia wirtualnego przetwarzania Standard FIPS (Federal Information) włączone na innym urządzeniu FIPS włączona lub na urządzeniu z systemem innym niż FIPS wdrożone w portalu dla instytucji rządowych.


Wykonaj poniższe kroki, aby przywrócić urządzenia docelowego urządzenia wirtualnego StorSimple.

1. Udostępniania i konfigurowania urządzenia docelowego, który spełnia [wymagania wstępne dotyczące urządzeń w pracy awaryjnej](#prerequisites). Ukończenie konfiguracji urządzenia za pośrednictwem lokalnego interfejsu użytkownika sieci web i zarejestruj go w usłudze Menedżer StorSimple urządzenia. W przypadku tworzenia serwera plików, przejdź do kroku 1 [skonfigurowany jako serwer plików](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device). Jeśli tworzenie serwera iSCSI, przejdź do kroku 1 [skonfigurowany jako serwer iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device).

2. Zająć woluminów/udziałów w tryb offline na hoście. Woluminy/udziałów przełączenia do trybu offline, skorzystaj z instrukcji specyficzne dla systemu operacyjnego hosta. Jeśli nie już w trybie offline, należy wykonać wszystkie woluminy/udziały w tryb offline na urządzeniu, wykonując następujące czynności.
   
    1. Przejdź do **urządzeń** bloku i wybrać urządzenie.
   
    2. Przejdź do **Ustawienia > Zarządzaj > udziałów** (lub **Ustawienia > Zarządzaj > woluminów**). 
   
    3. Wybierz udział/wolumin, kliknij prawym przyciskiem myszy i wybierz **przełączyć do trybu offline**. 
   
    4. Po wyświetleniu monitu o potwierdzenie, sprawdź **I zrozumienie wpływu tego udziału przełączeniem do trybu offline.** 
   
    5. Kliknij przycisk **przełączyć do trybu offline**.

3. W usłudze Menedżer StorSimple urządzeń, przejdź do **zarządzania > urządzenia**. W **urządzeń** bloku, wybierz i kliknij przycisk urządzenia źródłowego.

4. W Twojej **pulpitu nawigacyjnego urządzenia** bloku, kliknij przycisk **Dezaktywuj**.

5. W **Dezaktywuj** bloku, zostanie wyświetlony monit o potwierdzenie. Dezaktywacja urządzenia jest *stałe* procesu, którego nie można cofnąć. Zostanie wyświetlone przypomnienie do wykonania akcji/woluminów w tryb offline na hoście. Wpisz nazwę urządzenia, aby potwierdzić, a następnie kliknij przycisk **Dezaktywuj**.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. Uruchamia dezaktywację. Po pomyślnym zakończeniu dezaktywację, otrzymasz powiadomienie.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. Na stronie urządzenia stan urządzenia teraz zmieni się na **dezaktywowane**.
    ![](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. W **urządzeń** bloku, wybierz i kliknij przycisk dezaktywowane urządzenia do pracy awaryjnej. 
9. W **pulpitu nawigacyjnego urządzenia** bloku, kliknij przycisk **awaryjnie**. 
10. W **w tryb failover urządzeń** blok, wykonaj następujące czynności:
    
    1. Pole urządzenia źródłowego jest wypełniane automatycznie. Należy pamiętać, rozmiar całkowitą danych urządzenia źródłowego. Rozmiar danych powinna być wcześniejsza niż dostępna pojemność na urządzeniu docelowym. Przejrzyj szczegóły skojarzone z urządzenia źródłowego, takie jak nazwa urządzenia, łączna pojemność i nazwy akcji, które są przejścia w tryb failover.

    2. Z listy rozwijanej dostępnych urządzeń, wybierz **urządzenie docelowe**. Tylko na urządzeniach, które mają wystarczającą pojemność są wyświetlane na liście rozwijanej.

    3. Sprawdź, czy **rozumiem, że ta operacja zakończy się niepowodzeniem, nad danymi na urządzeniu docelowym**. 

    4. Kliknij przycisk **awaryjnie**.
    
        ![](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. Inicjuje zadania trybu failover i zostanie wyświetlone powiadomienie. Przejdź do **urządzenia > zadań** do monitorowania pracy awaryjnej.
    
     ![](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. W **zadania** bloku, zobacz trybu failover zadania utworzone dla urządzenia źródłowego. To zadanie przeprowadza prechecks odzyskiwania po awarii.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     Po pomyślnym DR prechecks zadanie trybu failover zostanie zduplikować zadania przywracania dla każdego udziału/woluminu, która istnieje na urządzeniu źródła.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. Po zakończeniu pracy awaryjnej, przejdź do **urządzeń** bloku.
    
    1. Wybierz, a następnie kliknij przycisk urządzenia StorSimple, która została użyta jako urządzenie docelowe dla procesu pracy awaryjnej.
    2. Przejdź do **Ustawienia > zarządzania > udziałów** (lub **woluminów** Jeśli serwer iSCSI). W **udziałów** bloku można wyświetlić wszystkie udziały (woluminy) ze starym urządzeniem.
        ![](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. Konieczne będzie [utworzyć DNS alias](https://support.microsoft.com/kb/168322) tak, aby wszystkie aplikacje, które próbujesz nawiązać połączenie można przekierowanie do nowego urządzenia.

## <a name="errors-during-dr"></a>Błędy podczas odzyskiwania po awarii

**Awaria łączności chmurze podczas odzyskiwania po awarii**

Jeśli połączenie chmury jest zakłócona po rozpoczęciu odzyskiwania po awarii i przed zakończeniem przywracania urządzenia DR zakończy się niepowodzeniem. Zostanie wyświetlone powiadomienie failore. Urządzenie docelowe do odzyskiwania po awarii jest oznaczony jako *korzystanie z niej.* Nie można użyć tego samego urządzenia docelowego dla przyszłych rejestracji urządzeń.

**Nie urządzeń docelowych zgodne**

Jeśli nie ma wystarczającą ilość miejsca na dostępnych urządzeń, zostanie wyświetlony błąd potwierdzające, że nie ma żadnych urządzeń docelowych zgodne.

**Sprawdź błędy**

Jeśli jeden z prechecks nie zostanie spełniony, zostanie wyświetlony precheck błędów.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Odzyskiwanie po awarii ciągłości biznesowej (BCDR)

Scenariusz odzyskiwania (BCDR) po awarii ciągłości biznesowej występuje, gdy całe centrum danych Azure przestanie działać. To może mieć wpływ na usługi Menedżer StorSimple urządzenia i skojarzone urządzenia StorSimple.

W przypadku urządzeń StorSimple, które zostały zarejestrowane tuż przed wystąpienia awarii, a następnie te urządzenia StorSimple może być konieczne do usunięcia. Po awarii możesz ponownie utworzyć i skonfigurować te urządzenia.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o sposobie [administrowania tablica wirtualnego StorSimple przy użyciu lokalnego interfejsu użytkownika sieci web](storsimple-ova-web-ui-admin.md).

