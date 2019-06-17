---
title: Rozwiązania StorSimple Virtual Array awaryjnego odzyskiwania urządzenia trybu failover i | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat trybu failover rozwiązania StorSimple Virtual Array.
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
ms.openlocfilehash: be3d98dc0b3a8119fb853493440c6fc78d65c5a2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61409624"
---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array-via-azure-portal"></a>Awaryjnego odzyskiwania i urządzenia w trybie failover rozwiązania StorSimple Virtual Array za pośrednictwem witryny Azure portal

## <a name="overview"></a>Omówienie
W tym artykule opisano odzyskiwania po awarii dla usługi Microsoft Azure StorSimple Virtual Array szczegółowe instrukcje zostały podane w tym do trybu failover do innej macierzy wirtualnej. Tryb failover umożliwia przenoszenie danych z *źródła* urządzenia w centrum danych, aby *docelowej* urządzenia. Urządzenie docelowe może znajdować się w tej samej lub innej lokalizacji geograficznej. Tryb failover urządzenia dotyczy całego urządzenia. Podczas pracy w trybie failover danych w chmurze dla urządzenia źródłowego zmiany prawa własności do tego urządzenia docelowego.

Ten artykuł dotyczy macierze wirtualne StorSimple tylko. Do trybu failover urządzenia z serii 8000, przejdź do [urządzenia trybu failover i odzyskiwanie po awarii urządzenia StorSimple](storsimple-device-failover-disaster-recovery.md).

## <a name="what-is-disaster-recovery-and-device-failover"></a>Co to jest tryb failover odzyskiwania i urządzenia po awarii?

W scenariuszu odzyskiwania po awarii (DR) urządzenie podstawowe przestanie działać. W tym scenariuszu można przenieść danych skojarzone z urządzeniem nie powiodło się na inne urządzenie w chmurze. Można użyć urządzenie podstawowe jako *źródła* i określ innego urządzenia jako *docelowej*. Ten proces jest nazywany *trybu failover*. Podczas pracy w trybie failover wszystkie woluminy lub udziały z urządzenia źródłowego zmieniają właściciela i zostają przekazane na urządzeniu docelowym. Dozwolone jest nie filtrowania danych.

Odzyskiwanie po awarii jest modelowane jako Przywracanie pełnej urządzenia przy użyciu częstości dostępu oparte na mapie warstw i śledzenia. Mapa cieplna jest definiowany przez przypisywanie wartości częstości dostępu do danych na podstawie na odczyt i zapis wzorców. Ta ciepła mapy następnie warstwy najniższy fragmentów danych częstości dostępu do chmury najpierw przy jednoczesnym zachowaniu ciepło (najczęściej używane) fragmentów danych w warstwie lokalnej. Podczas odzyskiwania po awarii usługa StorSimple używa Mapa cieplna, aby przywrócić i przywrócenia z magazynu trwałego danych z chmury. Urządzenie pobiera wszystkich woluminów/udziałów w ostatnim ostatniej kopii zapasowej (zgodnie z określoną wewnętrznie) i wykonuje przywracanie z tej kopii zapasowej. Macierz wirtualna organizuje cały proces odzyskiwania po awarii.

> [!IMPORTANT]
> Urządzenie źródłowe jest usuwany po zakończeniu trybu failover urządzenia i dlatego nie jest obsługiwana podczas powrotu po awarii.
> 
> 

Odzyskiwanie po awarii jest zorganizowanych za pośrednictwem funkcji trybu failover urządzenia i jest inicjowane z **urządzeń** bloku. Ten blok rejestruje wszystkie urządzenia StorSimple połączoną z usługą Menedżera urządzeń StorSimple. Dla każdego urządzenia widać, przyjazna nazwa, stan, elastycznie i maksymalną pojemność, typ i modelu.

## <a name="prerequisites-for-device-failover"></a>Wymagania wstępne dotyczące urządzenia w tryb failover

### <a name="prerequisites"></a>Wymagania wstępne

Przejścia w tryb failover urządzenia upewnij się, że są spełnione następujące wymagania wstępne:

* Urządzenie źródłowe musi być zapisana w **Deactivated** stanu.
* Na urządzeniu docelowym musi stanowić **gotowe do skonfigurowania** w witrynie Azure portal. Aprowizowanie macierzy wirtualnej docelowego o tej samej lub większej pojemności. Aby skonfigurować i pomyślnie zarejestrować macierz wirtualną w docelowej, należy użyć lokalnego Interfejsu w przeglądarce.
  
  > [!IMPORTANT]
  > Nie należy próbować skonfigurować zarejestrowane urządzenia wirtualnego za pośrednictwem usługi. Konfiguracja urządzenia nie powinno być przeprowadzane za pośrednictwem usługi.
  > 
  > 
* Na urządzeniu docelowym nie może mieć taką samą nazwę jak urządzenie źródłowe.
* Urządzenie źródłowe i docelowe muszą być tego samego typu. Można jedynie przejścia w tryb failover macierzy wirtualnej skonfigurowany jako serwer plików do innego serwera plików. Dotyczy to na serwerze iSCSI.
* Dla serwera plików odzyskiwania po awarii zaleca się przyłączyć urządzenie docelowe do tej samej domenie co źródłowy. Ta konfiguracja gwarantuje, że uprawnienia udziału zostały automatycznie rozwiązane. Tylko tryb failover na urządzeniu docelowym w tej samej domenie.
* Te urządzenia dostępne do odzyskiwania po awarii są urządzeń o takiej samej lub większej pojemności w porównaniu do urządzenia źródłowego. Urządzenia, które są połączone z usługą, ale nie spełniają kryteriów wystarczającej ilości miejsca, nie są dostępne jako urządzenia docelowe.

### <a name="other-considerations"></a>Inne zagadnienia

* Do planowanego trybu failover 
  
  * Zaleca się wykonać wszystkie woluminy lub udziały na urządzeniu źródłowym w trybie offline.
  * Firma Microsoft zaleca tworzenie kopii zapasowej urządzenia, a następnie kontynuować pracy w trybie failover, aby zminimalizować ryzyko utraty danych. 
* W przypadku nieplanowanego trybu failover urządzenie używa najnowszej kopii zapasowej do przywrócenia danych.

### <a name="device-failover-prechecks"></a>Prechecks trybu failover urządzenia

Przed rozpoczęciem odzyskiwania po awarii, urządzenie wykonuje prechecks. Te sprawdzenia pomoc, upewnij się, że nie występują błędy podczas odzyskiwania po awarii zaczyna. Prechecks obejmują:

* Sprawdzanie poprawności konta magazynu.
* Sprawdzanie łączności chmury na platformie Azure.
* Sprawdzanie wolnego miejsca na urządzeniu docelowym.
* Sprawdzanie, czy ma woluminie urządzenia źródłowy serwer iSCSI
  
  * Prawidłowe nazwy rejestru Azure container Registry.
  * prawidłową nazwę IQN (nie przekraczającą 220 znaków).
  * Nieprawidłowa haseł protokołu CHAP (maksymalnie 12 – 16 znaków).

Jeśli dowolny z poprzednim prechecks kończyć się niepowodzeniem, nie można kontynuować odzyskiwania po awarii. Rozwiąż te problemy, a następnie ponów próbę odzyskiwania po awarii.

Po pomyślnym zakończeniu DR, własności danych w chmurze na urządzeniu źródłowym jest przekazywany do urządzenia docelowego. Urządzenie źródłowe następnie nie jest już dostępne w portalu. To zablokowanie dostępu do wszystkich woluminów/udziałów na urządzenie źródłowe i docelowe urządzenie stanie się aktywny.

> [!IMPORTANT]
> Jeśli urządzenie nie jest już dostępna, maszyna wirtualna aprowizowana przez Ciebie w systemie hosta jest nadal zużywają zasoby. Gdy DR zostanie ukończone pomyślnie, można usunąć tej maszyny wirtualnej w systemie hosta.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Funkcjonować w trybie awaryjnym macierzy wirtualnej

Zaleca się aprowizowanie, konfigurowanie i rejestrowanie innej macierzy wirtualnej StorSimple przy użyciu usługi Menedżer urządzeń StorSimple, przed rozpoczęciem tej procedury.

> [!IMPORTANT]
> 
> * Użytkownik nie może przełączyć się z urządzenia StorSimple 8000 series do urządzenia wirtualnego 1200.
> * Możesz przełączać awaryjnie z urządzenia wirtualnego informacji przetwarzania Standard FIPS (Federal) włączone na innym urządzeniu FIPS włączone lub na urządzeniu — ze standardem FIPS, wdrożone w portalu dla instytucji rządowych.


Wykonaj poniższe kroki, aby przywrócić urządzenia docelowego urządzenia wirtualnego StorSimple.

1. Aprowizowanie i konfigurowanie urządzenia docelowego, który spełnia [wymagania wstępne dotyczące urządzenia w tryb failover](#prerequisites). Ukończ konfigurację urządzenia przy użyciu lokalnego Interfejsu w przeglądarce i zarejestruj je do usługi Menedżer urządzeń StorSimple. W przypadku tworzenia serwera plików, przejdź do kroku 1 [skonfigurowany jako serwer plików](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device). Jeśli tworzenie serwera iSCSI, przejdź do kroku 1 procedury [Konfigurowanie serwera jako serwera iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device).

2. Podjąć woluminów/udziałów w trybie offline na hoście. Aby móc woluminów/udziałów w trybie offline, zapoznaj się z instrukcjami specyficzne dla systemu operacyjnego hosta. Jeśli nie już w trybie offline, należy wykonać wszystkich woluminów/udziałów w trybie offline na urządzeniu, wykonując następujące czynności.
   
    1. Przejdź do **urządzeń** bloku i wybierz swoje urządzenie.
   
    2. Przejdź do **Ustawienia > Zarządzaj > udziałów** (lub **Ustawienia > Zarządzaj > woluminów**). 
   
    3. Wybierz wolumin/udział, kliknij prawym przyciskiem myszy i wybierz **przełączyć do trybu offline**. 
   
    4. Po wyświetleniu monitu o potwierdzenie, sprawdź **rozumiem konsekwencje przełączenia tego udziału w tryb offline.** 
   
    5. Kliknij przycisk **przełączyć do trybu offline**.

3. W usłudze Menedżer urządzeń StorSimple przejdź do **zarządzania > urządzenia**. W **urządzeń** bloku, wybierz i kliknij swoje urządzenie źródłowe.

4. W swojej **pulpit nawigacyjny urządzeń** bloku kliknij **Dezaktywuj**.

5. W **Dezaktywuj** bloku zostanie wyświetlony monit o potwierdzenie. Dezaktywacja urządzenia jest *stałe* procesu, którego nie można cofnąć. Zostanie wyświetlone przypomnienie do wykonania akcji/woluminów w tryb offline na hoście. Wpisz nazwę urządzenia, aby potwierdzić, a następnie kliknij przycisk **Dezaktywuj**.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. Rozpoczyna się dezaktywację. Otrzymasz powiadomienie po pomyślnym zakończeniu dezaktywację.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. Na stronie urządzenia, stan urządzenia teraz zmieni się na **Deactivated**.
    ![](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. W **urządzeń** bloku, wybierz i kliknij urządzenie źródłowe dezaktywowane, pracy w trybie Failover. 
9. W **pulpit nawigacyjny urządzeń** bloku kliknij **w trybie Failover**. 
10. W **w trybie Failover urządzenia** blok, wykonaj następujące czynności:
    
    1. Pola urządzenia źródłowego jest wypełniane automatycznie. Należy pamiętać, łącznemu rozmiarowi danych do urządzenia źródłowego. Rozmiar danych powinien być mniejszy niż dostępna pojemność na urządzeniu docelowym. Przejrzyj szczegóły skojarzone z urządzenia źródłowego, takich jak nazwa urządzenia, łączna pojemność i nazwy akcji, które są w trybie Failover.

    2. Wybierz z listy rozwijanej dostępnych urządzeń, **urządzenie docelowe**. Tylko urządzenia, które mają wystarczającą pojemność są wyświetlane na liście rozwijanej.

    3. Sprawdź, czy **zdaję sobie sprawę, że ta operacja zakończy się niepowodzeniem, nad danymi na urządzeniu docelowym**. 

    4. Kliknij przycisk **w trybie Failover**.
    
        ![](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. Inicjuje zadania trybu failover, a otrzymasz powiadomienie. Przejdź do **urządzeń > zadania** do monitorowania pracy w trybie failover.
    
     ![](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. W **zadań** wyświetlony blok zadania trybu failover utworzone dla urządzenia źródłowego. To zadanie przeprowadza prechecks odzyskiwania po awarii.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     Po pomyślnym prechecks odzyskiwania po awarii zadania trybu failover będzie zduplikować zadania przywracania dla każdego udziału/woluminu, która istnieje na urządzeniu źródłowym.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. Po zakończeniu pracy w trybie failover, przejdź do **urządzeń** bloku.
    
    1. Wybierz i kliknij urządzenie StorSimple, która została użyta jako urządzenie docelowe dla procesu pracy awaryjnej.
    2. Przejdź do **Ustawienia > Zarządzanie > udziałów** (lub **woluminów** Jeśli serwer iSCSI). W **udziałów** bloku można wyświetlić wszystkie udziały (woluminy) ze starym urządzeniem.
        ![](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. Konieczne będzie [Utwórz DNS alias](https://support.microsoft.com/kb/168322) tak, aby wszystkie aplikacje, które próbujesz nawiązać połączenie, może uzyskać przekierowany do nowego urządzenia.

## <a name="errors-during-dr"></a>Błędy podczas odzyskiwania po awarii

**Awaria łączności chmury podczas odzyskiwania po awarii**

Łączność z chmurą jest zakłócone. przed rozpoczęciem odzyskiwania po awarii i przed Przywracanie zostało ukończone, DR zakończy się niepowodzeniem. Zostanie wyświetlone powiadomienie o awarii. Urządzenie docelowe do odzyskiwania po awarii jest oznaczony jako *uniemożliwiającym jego używanie.* Nie można używać na tym samym urządzeniu docelowym usługi rejestracji urządzeń w przyszłości.

**Nie urządzeń zgodnych docelowych**

Jeśli nie ma wystarczającą ilość miejsca na dostępnych urządzeń, zostanie wyświetlony błąd potwierdzające, że nie istnieją żadne urządzenia docelowego zgodne.

**Sprawdź błędy**

Jeśli jeden z prechecks nie jest spełniony, zostanie wyświetlony precheck błędów.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Odzyskiwanie po awarii ciągłość działania firmy (BCDR)

Dotyczy scenariusza biznesowego ciągłości działania po awarii recovery (BCDR) występuje, gdy całe centrum danych platformy Azure, przestanie działać. Może to wpłynąć na usługi Menedżer urządzeń StorSimple i skojarzone urządzenia StorSimple.

W przypadku urządzeń StorSimple, które zostały zarejestrowane przed wystąpienia awarii, a następnie może trzeba będzie usunąć te urządzenia StorSimple. Po awarii można ponownie utworzyć i skonfigurować te urządzenia.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat [administrowania rozwiązania StorSimple Virtual Array przy użyciu lokalnego Interfejsu w przeglądarce](storsimple-ova-web-ui-admin.md).

