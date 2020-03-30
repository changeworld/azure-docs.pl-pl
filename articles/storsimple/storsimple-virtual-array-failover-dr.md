---
title: Tryb failover i odzyskiwanie po awarii dla tablicy wirtualnej StorSimple
description: Dowiedz się więcej o tym, jak przewinąć awaryjnie tablicę wirtualną StorSimple.
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
ms.openlocfilehash: 464fa05f658dd6e6e25d79f8840ceeb939383149
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77467219"
---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array-via-azure-portal"></a>Disaster recovery and device failover for your StorSimple Virtual Array via Azure portal (Odzyskiwane po awarii i przełączanie urządzenia w tryb failover dla macierzy wirtualnej StorSimple w witrynie Azure Portal)

## <a name="overview"></a>Omówienie
W tym artykule opisano odzyskiwanie po awarii dla tablicy wirtualnej usługi Microsoft Azure StorSimple, w tym szczegółowe kroki, aby przejść awaryjnie do innej tablicy wirtualnej. Funkcja pracy awaryjnej umożliwia przeniesienie danych z urządzenia *źródłowego* w centrum danych do urządzenia *docelowego.* Urządzenie docelowe może znajdować się w tym samym lub innym położeniu geograficznym. Przewija się w stan failover urządzenia jest dla całego urządzenia. Podczas pracy awaryjnej dane w chmurze dla urządzenia źródłowego zmienia własność na własność urządzenia docelowego.

Ten artykuł dotyczy tylko tablic wirtualnych StorSimple. Aby przejść w tryb failover urządzenia z serii 8000, przejdź do [trybu failover urządzenia i odzyskiwania po awarii urządzenia StorSimple](storsimple-device-failover-disaster-recovery.md).

## <a name="what-is-disaster-recovery-and-device-failover"></a>Co to jest odzyskiwanie po awarii i przewija się w stan failover urządzenia?

W scenariuszu odzyskiwania po awarii (DR) urządzenie podstawowe przestaje działać. W tym scenariuszu można przenieść dane w chmurze skojarzone z urządzeniem, które uległo awarii, na inne urządzenie. Można użyć urządzenia podstawowego jako *źródła* i określić inne urządzenie jako *miejsce docelowe*. Ten proces jest określany jako *pracy awaryjnej*. Podczas pracy awaryjnej wszystkie woluminy lub udziały z urządzenia źródłowego zmieniają własność i są przenoszone na urządzenie docelowe. Filtrowanie danych nie jest dozwolone.

Odzyskiwania po awarii jest modelowany jako pełne przywracanie urządzenia przy użyciu warstwowania i śledzenia opartego na mapie cieplnej. Mapa cieplna jest definiowana przez przypisanie wartości ciepła do danych na podstawie wzorców odczytu i zapisu. Ta mapa ciepła następnie warstw najniższych fragmentów danych ciepła do chmury pierwszy przy zachowaniu wysokiej ciepła (najczęściej używane) fragmenty danych w warstwie lokalnej. Podczas odzyskiwania po awarii StorSimple używa mapy cieplnej do przywracania i ponownego nawadniania danych z chmury. Urządzenie pobiera wszystkie woluminy/udziały w ostatniej ostatniej kopii zapasowej (określonej wewnętrznie) i wykonuje przywracanie z tej kopii zapasowej. Tablica wirtualna organizuje cały proces odzyskiwania po awarii.

> [!IMPORTANT]
> Urządzenie źródłowe jest usuwane na końcu pracy awaryjnej urządzenia i dlatego powrót po awarii nie jest obsługiwany.
> 
> 

Odzyskiwanie po awarii jest organizowane za pośrednictwem funkcji pracy awaryjnej urządzenia i jest inicjowane z **bloku Urządzenia.** Ten blok tabulates wszystkich urządzeń StorSimple podłączony do usługi StorSimple Device Manager. Dla każdego urządzenia można zobaczyć przyjazną nazwę, stan, aprowizowana i maksymalną pojemność, typ i model.

## <a name="prerequisites-for-device-failover"></a>Wymagania wstępne dotyczące trybu failover urządzenia

### <a name="prerequisites"></a>Wymagania wstępne

W przypadku pracy awaryjnej urządzenia upewnij się, że spełnione są następujące wymagania wstępne:

* Urządzenie źródłowe musi być w stanie **deactivated.**
* Urządzenie docelowe musi być wyświetlane jako **gotowe do skonfigurowania** w witrynie Azure portal. Aprowizuj docelową tablicę wirtualną o takiej samej lub większej pojemności. Użyj lokalnego interfejsu użytkownika sieci Web, aby skonfigurować i pomyślnie zarejestrować docelową tablicę wirtualną.
  
  > [!IMPORTANT]
  > Nie próbuj konfigurować zarejestrowanego urządzenia wirtualnego za pośrednictwem usługi. Żadna konfiguracja urządzenia nie powinna być wykonywana za pośrednictwem usługi.
  > 
  > 
* Urządzenie docelowe nie może mieć takiej samej nazwy jak urządzenie źródłowe.
* Urządzenie źródłowe i docelowe muszą być tego samego typu. Można tylko w trybie fail over tablicy wirtualnej skonfigurowany jako serwer plików do innego serwera plików. To samo dotyczy serwera iSCSI.
* W przypadku odzyskiwania po awarii serwera plików zaleca się dołączenie urządzenia docelowego do tej samej domeny co źródło. Ta konfiguracja zapewnia, że uprawnienia udziału są automatycznie rozpoznawane. Obsługiwane jest tylko przewijane w błąd urządzenia docelowego w tej samej domenie.
* Dostępne urządzenia docelowe dla odzyskiwania po awarii to urządzenia o takiej samej lub większej pojemności w porównaniu z urządzeniem źródłowym. Urządzenia, które są podłączone do usługi, ale nie spełniają kryteriów wystarczającej ilości miejsca, nie są dostępne jako urządzenia docelowe.

### <a name="other-considerations"></a>Inne zagadnienia

* W przypadku planowanej pracy awaryjnej:
  
  * Zaleca się przełącznie wszystkich woluminów lub udziałów na urządzeniu źródłowym w tryb offline.
  * Zaleca się wykonanie kopii zapasowej urządzenia, a następnie kontynuowanie pracy awaryjnej w celu zminimalizowania utraty danych.
* W przypadku nieplanowanej pracy awaryjnej urządzenie używa najnowszej kopii zapasowej do przywrócenia danych.

### <a name="device-failover-prechecks"></a>Wstępne sprawdzanie pracy awaryjnej urządzenia

Przed rozpoczęciem odzyskiwania po awarii urządzenie wykonuje wstępne sprawdzanie. Te kontrole pomagają upewnić się, że po rozpoczęciu odzyskiwania po awarii nie występują żadne błędy. Wstępne sprawdzanie obejmuje:

* Sprawdzanie poprawności konta magazynu.
* Sprawdzanie łączności w chmurze z platformą Azure.
* Sprawdzanie dostępnego miejsca na urządzeniu docelowym.
* Sprawdzanie, czy wolumin urządzenia źródłowego serwera iSCSI
  
  * prawidłowych nazw ACR.
  * prawidłowe IQN (nieprzekraczanie 220 znaków).
  * prawidłowe hasła CHAP (długość 12-16 znaków).

Jeśli którykolwiek z poprzednich prechecks zakończyć się niepowodzeniem, nie można kontynuować dr. Rozwiąż te problemy, a następnie ponów próbę ponowienia próby odzyskiwania po awarii.

Po pomyślnym zakończeniu odzyskiwania po awarii własność danych w chmurze na urządzeniu źródłowym jest przesyłana do urządzenia docelowego. Urządzenie źródłowe nie jest już dostępne w portalu. Dostęp do wszystkich woluminów/udziałów na urządzeniu źródłowym jest zablokowany, a urządzenie docelowe staje się aktywne.

> [!IMPORTANT]
> Chociaż urządzenie nie jest już dostępne, maszyna wirtualna, która została zainicjowana w systemie hosta, nadal zużywa zasoby. Po pomyślnym zakończeniu odzyskiwania po awarii można usunąć tę maszynę wirtualną z systemu hosta.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Przeładuj awaryjnie do tablicy wirtualnej

Przed uruchomieniem tej procedury zaleca się aprowizowanie, konfigurowanie i rejestrowanie innej tablicy wirtualnej StorSimple za pomocą usługi StorSimple Device Manager.

> [!IMPORTANT]
> 
> * Nie można przejść awaryjnie z urządzenia storsimple serii 8000 do urządzenia wirtualnego 1200.
> * Urządzenie wirtualne z włączoną funkcją FIPS (Federal Information Processing Standard) można przemieścić awaryjnie na innym urządzeniu z włączoną funkcją FIPS lub na urządzeniu innym niż FIPS wdrożonym w portalu rządowym.


Wykonaj następujące kroki, aby przywrócić urządzenie do docelowego urządzenia wirtualnego StorSimple.

1. Aprowizuj i skonfiguruj urządzenie docelowe, które spełnia [wymagania wstępne dotyczące pracy awaryjnej urządzenia.](#prerequisites) Uzupełnij konfigurację urządzenia za pośrednictwem lokalnego interfejsu użytkownika sieci Web i zarejestruj ją w usłudze StorSimple Device Manager. W przypadku tworzenia serwera plików przejdź do kroku 1 [konfiguracji jako serwer plików](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device). W przypadku tworzenia serwera iSCSI przejdź do kroku 1 [skonfigurowanego jako serwer iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device).

2. Przejmij woluminy/udziały w trybie offline na hoście. Aby przetraktować woluminy/udziały w trybie offline, zapoznaj się z instrukcjami specyficznymi dla systemu operacyjnego dla hosta. Jeśli nie jest jeszcze w trybie offline, należy wykonać wszystkie woluminy /udziały w trybie offline na urządzeniu, wykonując następujące czynności.
   
    1. Przejdź do **bloku Urządzenia** i wybierz urządzenie.
   
    2. Przejdź do **pozycji Ustawienia > Zarządzanie udziałami >** (lub Ustawienia > Zarządzanie **woluminami >**). 
   
    3. Wybierz udział/wolumin, kliknij prawym przyciskiem myszy i wybierz pozycję **Przejmij do trybu offline**. 
   
    4. Po wyświetleniu monitu o potwierdzenie sprawdź, **czy rozumiem wpływ przełączeniu tego udziału w tryb offline.** 
   
    5. Kliknij **pozycję Przejmij do trybu offline**.

3. W usłudze StorSimple Device Manager przejdź do **witryny Zarządzanie > urządzeniami**. W bloku **Urządzenia** wybierz i kliknij urządzenie źródłowe.

4. W bloku **pulpitu nawigacyjnego urządzenia** kliknij pozycję **Dezaktywuj**.

5. W **bloku Dezaktywuj** zostanie wyświetlony monit o potwierdzenie. Dezaktywacja urządzenia jest *procesem trwałym,* czego nie można cofnąć. Przypomina się również, aby wziąć swoje udziały / woluminy w trybie offline na hoście. Wpisz nazwę urządzenia, aby potwierdzić i kliknąć **przycisk Dezaktywuj**.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. Rozpocznie się dezaktywacja. Otrzymasz powiadomienie po pomyślnym zakończeniu dezaktywacji.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. Na stronie Urządzenia stan urządzenia zmieni się teraz na **Dezaktywowany**.
    ![](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. W bloku **Urządzenia** wybierz i kliknij wyłączone urządzenie źródłowe w celu pracy awaryjnej. 
9. W bloku **pulpit nawigacyjny urządzenia** kliknij pozycję **Przewiduj powiększe**. 
10. W **bloku Awaryjne urządzenie** wykonaj następujące czynności:
    
    1. Pole urządzenia źródłowego jest wypełniane automatycznie. Zanotuj całkowity rozmiar danych dla urządzenia źródłowego. Rozmiar danych powinien być mniejszy niż dostępna pojemność na urządzeniu docelowym. Przejrzyj szczegóły skojarzone z urządzeniem źródłowym, takie jak nazwa urządzenia, całkowita pojemność i nazwy udziałów, które są przejęte awaryjnie.

    2. Z listy rozwijanej dostępnych urządzeń wybierz **urządzenie docelowe**. Na liście rozwijanej są wyświetlane tylko urządzenia o wystarczającej pojemności.

    3. Sprawdź, czy **rozumiem, że ta operacja będzie awaryjnie danych do urządzenia docelowego**. 

    4. Kliknij pozycję **Przewiduj niepowodzenie**.
    
        ![](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. Zadanie pracy awaryjnej inicjuje i otrzymasz powiadomienie. Przejdź do **urządzenia > zadania,** aby monitorować pracę awaryjną.
    
     ![](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. W **zadaniach** bloku zostanie wyświetlene zadanie pracy awaryjnej utworzone dla urządzenia źródłowego. To zadanie wykonuje sprawdzanie prechecks odzyskiwania po awarii.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     Po pomyślnym zakończeniu preczeków odzyskiwania po awarii zadanie pracy awaryjnej spowoduje odtworzenie zadań przywracania dla każdego udziału/woluminu, który istnieje na urządzeniu źródłowym.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. Po zakończeniu pracy awaryjnej przejdź do **urządzenia** bloku.
    
    1. Wybierz i kliknij urządzenie StorSimple, które było używane jako urządzenie docelowe dla procesu pracy awaryjnej.
    2. Przejdź do **pozycji Ustawienia > zarządzania > udziałami** (lub **woluminami,** jeśli serwer iSCSI). W **bloku Udziały** można wyświetlić wszystkie udziały (woluminy) ze starego urządzenia.
        ![](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. Należy utworzyć [alias DNS,](https://support.microsoft.com/kb/168322) aby wszystkie aplikacje, które próbują się połączyć, mogły zostać przekierowane na nowe urządzenie.

## <a name="errors-during-dr"></a>Błędy podczas odzyskiwania po awarii

**Awaria łączności w chmurze podczas odzyskiwania po awarii**

Jeśli łączność w chmurze zostanie zakłócona po uruchomieniu odzyskiwania po awarii i przed zakończeniem przywracania urządzenia, funkcja odzyskiwania po awarii zakończy się niepowodzeniem. Otrzymasz powiadomienie o awarii. Urządzenie docelowe dla odzyskiwania po awarii jest oznaczone jako *bezużyteczne.* Nie można używać tego samego urządzenia docelowego dla przyszłych dr.

**Brak kompatybilnych urządzeń docelowych**

Jeśli dostępne urządzenia docelowe nie mają wystarczającej ilości miejsca, zostanie wyświetlony błąd, w tym że nie ma zgodnych urządzeń docelowych.

**Błędy wstępnej kontroli**

Jeśli jedna z preznaków nie jest spełniona, zostanie wyświetlony błąd wstępnej kontroli.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Odzyskiwanie po awarii ciągłości działania (BCDR)

Scenariusz odzyskiwania po awarii ciągłości biznesowej (BCDR) występuje, gdy całe centrum danych platformy Azure przestaje działać. Może to mieć wpływ na usługę StorSimple Device Manager i skojarzone urządzenia StorSimple.

Jeśli istnieją urządzenia StorSimple, które zostały zarejestrowane tuż przed wystąpieniem awarii, te urządzenia StorSimple może wymagać usunięcia. Po awarii można odtworzyć i skonfigurować te urządzenia.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [administrowaniu tablicą wirtualną StorSimple przy użyciu lokalnego interfejsu użytkownika sieci Web](storsimple-ova-web-ui-admin.md).

