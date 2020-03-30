---
title: Wyświetlanie alertów dla tablicy wirtualnej StorSimple i zarządzanie nimi
description: W tym artykule StorSimple Virtual Array alert warunki i ważność oraz jak korzystać z usługi StorSimple Manager do zarządzania alertami.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 97ee25a1-0ec3-4883-9a0a-54b722598462
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/12/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a7c469be4a1d8aba23857b1ba52ee829c126a431
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267432"
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>Menedżer urządzeń StorSimple służy do zarządzania alertami dla tablicy wirtualnej StorSimple

## <a name="overview"></a>Omówienie

Funkcja alertów w usłudze Menedżer urządzeń StorSimple umożliwia przeglądanie i wyczyszczenie alertów związanych z tablicami wirtualnymi StorSimple w czasie rzeczywistym. Alerty w bloku **podsumowania usługi** można użyć do centralnego monitorowania problemów zdrowotnych tablic wirtualnych StorSimple i ogólnego rozwiązania Microsoft Azure StorSimple.

W tym samouczku opisano sposób konfigurowania powiadomień alertów, typowych warunków alertów, poziomów ważności alertów oraz sposobu wyświetlania i śledzenia alertów. Ponadto zawiera tabele szybkiego odwołania alertów, które umożliwiają szybkie zlokalizowanie określonego alertu i odpowiednie reagowanie.

![Strona Alerty](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Konfigurowanie ustawień alertów

Można wybrać, czy chcesz otrzymywać powiadomienia pocztą e-mail o warunkach alertów dla każdej z tablic wirtualnych StorSimple. Ponadto można zidentyfikować innych adresatów powiadomień o alertach, wprowadzając ich adresy e-mail w polu **Dodatkowe adresaci wiadomości e-mail, oddzielone** średnikami.

> [!NOTE]
> Na tablicę wirtualną można wprowadzić maksymalnie 20 adresów e-mail.

Po włączeniu powiadomienia e-mail dla tablicy wirtualnej członkowie listy powiadomień otrzymają wiadomość e-mail za każdym razem, gdy pojawi się alert krytyczny. Wiadomości będą wysyłane z *storsimple-alerts-noreply\@mail.windowsazure.com* i opisze warunek alertu. Adresaci mogą kliknąć pozycję **Wypisuj subskrypcję,** aby usunąć się z listy powiadomień e-mail.

#### <a name="to-enable-email-notification-for-alerts"></a>Aby włączyć powiadomienia e-mail dotyczące alertów

1. Przejdź do usługi StorSimple Device Manager i w sekcji **Zarządzanie** wybierz i kliknij pozycję **Urządzenia**. Z wyświetlona lista urządzeń wybierz i kliknij urządzenie.
   
    ![ustawienia alertów](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. Spowoduje to otwarcie bloku **Ustawienia.** W sekcji **Ustawienia urządzenia** wybierz pozycję **Ogólne**. Spowoduje to otwarcie bloku **Ustawienia ogólne.**
   
    ![konfiguracja powiadomień alertów](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. W bloku **Ustawienia ogólne** przejdź do sekcji **Ustawienia alertu** i ustaw następujące ustawienia:
   
   1. W polu **Włącz powiadomienie e-mail** wybierz pozycję **TAK**.
   2. W polu **Administratorzy usługi poczty e-mail** wybierz pozycję **TAK,** jeśli chcesz, aby administrator usługi i wszyscy współadministratorzy otrzymywali powiadomienia o alertach.
   3. W polu **Dodatkowe adresaci wiadomości e-mail** wprowadź adresy e-mail wszystkich innych adresatów, którzy powinni otrzymywać powiadomienia o alertach. Wprowadź nazwy w *\@formacie, który ktoś somewhere.com*. Użyj średników, aby oddzielić adresy e-mail. Na urządzenie wirtualne można skonfigurować maksymalnie 20 adresów e-mail.
      
       ![konfiguracja powiadomień alertów](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. Aby wysłać testowe powiadomienie e-mail, kliknij przycisk **Wyślij testową wiadomość e-mail**. Usługa StorSimple Device Manager wyświetli komunikaty o stanie podczas przesyłania dalej powiadomienia testowego.
      
       ![Wysłane wiadomości e-mail z powiadomieniem testowym alertów](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > Jeśli nie można wysłać komunikatu powiadomienia testowego, usługa StorSimple Device Manager wyświetli odpowiedni komunikat. Kliknij **przycisk OK**, zaczekaj kilka minut, a następnie spróbuj ponownie wysłać wiadomość z powiadomieniem testowym.
      >
      >
   5. U dołu strony kliknij przycisk **Zapisz,** aby zapisać konfigurację. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**.
      
      ![Wysłane wiadomości e-mail z powiadomieniem testowym alertów](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>Typowe warunki alarmowe

Twoja tablica wirtualna StorSimple generuje alerty w odpowiedzi na różne warunki. Poniżej przedstawiono najczęstsze typy stanów alertów:

* **Problemy z łącznością** — te alerty występują, gdy występują trudności w przesyłaniu danych. Problemy z komunikacją mogą wystąpić podczas przesyłania danych do i z konta magazynu platformy Azure lub z powodu braku łączności między urządzeniami wirtualnymi a usługą StorSimple Device Manager. Problemy z komunikacją są jednymi z najtrudniejszych do naprawienia, ponieważ jest tak wiele punktów awarii. Przed kontynuowaniem bardziej zaawansowanego rozwiązywania problemów należy zawsze najpierw sprawdzić, czy łączność sieciowa i dostęp do Internetu są dostępne. Aby uzyskać informacje o portach i ustawieniach zapory, przejdź do [ustawień systemu StorSimple Virtual Array](storsimple-ova-system-requirements.md). Aby uzyskać pomoc dotyczącą rozwiązywania problemów, przejdź do [strony Rozwiązywanie problemów za pomocą polecenia cmdlet Test-Connection](storsimple-troubleshoot-deployment.md).
* **Problemy z wydajnością** — te alerty są spowodowane, gdy system nie działa optymalnie, na przykład gdy jest pod dużym obciążeniem.

Ponadto mogą być widoczne alerty związane z zabezpieczeniami, aktualizacjami lub błędami zadań.

## <a name="alert-severity-levels"></a>Poziomy ważności alertów

Alerty mają różne poziomy ważności, w zależności od wpływu, jaki będzie miała sytuacja alertu i potrzeby odpowiedzi na alert. Poziomy ważności to:

* **Krytyczne** — ten alert jest odpowiedzią na warunek, który ma wpływ na pomyślną wydajność systemu. Akcja jest wymagana, aby upewnić się, że Usługa StorSimple nie zostanie przerwana.
* **Ostrzeżenie** — ten warunek może stać się krytyczny, jeśli nie zostanie rozwiązany. Należy zbadać sytuację i podjąć wszelkie działania wymagane, aby usunąć problem.
* **Informacje** — ten alert zawiera informacje, które mogą być przydatne w śledzeniu i zarządzaniu systemem.

## <a name="view-and-track-alerts"></a>Wyświetlanie i śledzenie alertów

Serwer podsumowania usługi StorSimple Device Manager umożliwia szybkie spojrzenie na liczbę alertów na urządzeniach wirtualnych, uporządkowaną według poziomu ważności.

![Pulpit nawigacyjny alertów](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

Kliknij poziom ważności, aby otworzyć **alerty** bloku. Wyniki obejmują tylko alerty, które odpowiadają tego poziomu ważności.

![Raport alertów o zakresie typu alertu](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

Kliknij alert na liście, aby uzyskać dodatkowe szczegóły dotyczące alertu, w tym ostatni raz alert został zgłoszony, liczbę wystąpień alertu na urządzeniu i zalecaną akcję, aby rozwiązać alert.

![Lista alertów i szczegóły](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

Szczegóły alertu można skopiować do pliku tekstowego, jeśli konieczne jest wysłanie informacji do pomocy technicznej firmy Microsoft. Po postępowaniu zgodnie z zaleceniem i rozwiązaniu warunku alertu lokalnie należy wyczyścić alert z listy. Wybierz alert z listy, a następnie kliknij przycisk **Wyczyść**. Aby wyczyścić wiele alertów, zaznacz każdy alert, kliknij dowolną kolumnę z wyjątkiem kolumny **Alert,** a następnie kliknij przycisk **Wyczyść** po wybraniu wszystkich alertów do wyczyszczenie.

Po kliknięciu **przycisku Wyczyść,** będziesz mieć możliwość podania komentarzy na temat alertu i kroków, które zostały podjęte w celu rozwiązania problemu.

![komentarze alertów](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

Niektóre zdarzenia zostaną wyczyszczone przez system, jeśli inne zdarzenie zostanie wyzwolone z nowymi informacjami.

## <a name="sort-and-review-alerts"></a>Sortowanie i przeglądanie alertów

W bloku **Alerty** może być wyświetlanych maksymalnie 250 alertów. Jeśli liczba alertów została przekroczona, nie wszystkie alerty będą wyświetlane w widoku domyślnym. Można połączyć następujące pola, aby dostosować wyświetlane alerty:

* **Stan** — można wyświetlić alerty **aktywne** lub **wyczyszczone.** Aktywne alerty są nadal wyzwalane w systemie, podczas gdy wyczyszczone alerty zostały ręcznie wyczyszczone przez administratora lub programowo wyczyszczone, ponieważ system zaktualizował stan alertu o nowe informacje.
* **Ważność** — można wyświetlać alerty o wszystkich poziomach ważności (krytyczne, ostrzeżenie, informacje) lub tylko pewną ważność, takie jak tylko alerty krytyczne.
* **Źródło** — można wyświetlać alerty ze wszystkich źródeł lub ograniczyć alerty do tych, które pochodzą z usługi lub jednego lub wszystkich urządzeń wirtualnych.
* **Zakres czasu** — określając **daty od** i **do** i sygnatury czasowe, można przeglądać alerty w okresie, który Cię interesuje.

## <a name="alerts-quick-reference"></a>Szybkie alerty

W poniższych tabelach przedstawiono niektóre alerty StorSimple, które mogą wystąpić, a także dodatkowe informacje i zalecenia, jeśli są dostępne. Alerty StorSimple Virtual Array należą do jednej z następujących kategorii:

* [Alerty o łączności w chmurze](#cloud-connectivity-alerts)
* [Alerty konfiguracji](#configuration-alerts)
* [Alerty o niepowodzeniu zadania](#job-failure-alerts)
* [Alerty dotyczące wydajności](#performance-alerts)
* [Alerty zabezpieczeń](#security-alerts)

### <a name="cloud-connectivity-alerts"></a>Alerty o łączności w chmurze

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane działania |
|:--- |:--- |:--- |
| *Nazwa urządzenia* <> nie jest połączona z chmurą. |Nazwane urządzenie nie może połączyć się z chmurą. |Nie można połączyć się z chmurą. Może to być spowodowane jedną z następujących przyczyn:<ul><li>Być może wystąpił problem z ustawieniami sieciowymi na urządzeniu.</li><li>Może występować problem z poświadczeniami konta magazynu.</li></ul>Aby uzyskać więcej informacji na temat rozwiązywania problemów z łącznością, przejdź do [lokalnego interfejsu użytkownika sieci Web](storsimple-ova-web-ui-admin.md) urządzenia. |

### <a name="configuration-alerts"></a>Alerty konfiguracji

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane działania |
|:--- |:--- |:--- |
| Lokalna konfiguracja urządzenia wirtualnego nie jest podparta. |Niska wydajność. |Bieżąca konfiguracja może spowodować obniżenie wydajności. Upewnij się, że serwer spełnia minimalne wymagania konfiguracyjne. Aby uzyskać więcej informacji, przejdź do [storsimple virtual array wymagania](storsimple-ova-system-requirements.md). |
| Zabrakło aprowizowanego miejsca na dysku <*nazwie*\>urządzenia . |Ostrzeżenie o miejscu na dysku. |Brakuje miejsca na dysku aprowizowanym. Aby zwolnić miejsce, należy rozważyć przeniesienie obciążeń na inny wolumin lub udostępnienie lub usunięcie danych. |

### <a name="job-failure-alerts"></a>Alerty o niepowodzeniu zadania

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane działania |
|:--- |:--- |:--- |
| Nie można ukończyć kopii zapasowej *nazwy* \> urządzenia <. |Niepowodzenie zadania tworzenia kopii zapasowej. |Nie można utworzyć kopii zapasowej. Należy wziąć pod uwagę jedną z następujących czynności:<ul><li>Problemy z łącznością mogą uniemożliwiać pomyślne ukończenie operacji tworzenia kopii zapasowej. Upewnij się, że nie ma żadnych problemów z łącznością. Aby uzyskać więcej informacji na temat rozwiązywania problemów z łącznością, przejdź do [lokalnego interfejsu użytkownika sieci Web](storsimple-ova-web-ui-admin.md) dla urządzenia wirtualnego.</li><li>Osiągnięto limit dostępnego miejsca. Aby zwolnić miejsce, należy rozważyć usunięcie wszystkich kopii zapasowych, które nie są już potrzebne.</li></ul> Rozwiąż problemy, wyczyść alert i ponów próbę wykonania operacji. |
| Nie można ukończyć klonowania *nazwy* \> urządzenia <. |Niepowodzenie zadania klonowania. |Nie można utworzyć klona. Należy wziąć pod uwagę jedną z następujących czynności:<ul><li>Lista kopii zapasowych może być nieprawidłowa. Odśwież listę, aby sprawdzić, czy jest nadal prawidłowa.</li><li>Problemy z łącznością mogą uniemożliwiać pomyślne ukończenie operacji klonowania. Upewnij się, że nie ma żadnych problemów z łącznością.</li><li>Osiągnięto limit dostępnego miejsca. Aby zwolnić miejsce, należy rozważyć usunięcie wszystkich kopii zapasowych, które nie są już potrzebne.</li></ul>Rozwiąż problemy, wyczyść alert i ponów próbę wykonania operacji. |

### <a name="networking-alerts"></a>Alerty sieciowe

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane działania |
|:--- |:--- |:--- |
| Nie można połączyć się z usługą uwierzytelniania. |Błąd ścieżki danych |Adres URL używany do uwierzytelniania jest nie osiągalny. Upewnij się, że reguły zapory zawierają wzorce adresów URL określone dla urządzenia StorSimple. Aby uzyskać więcej informacji na temat wzorców adresów URL w witrynie Azure portal, przejdź do [storsimple wymagania dotyczące sieci tablic wirtualnych](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).|

### <a name="performance-alerts"></a>Alerty dotyczące wydajności

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane działania |
|:--- |:--- |:--- |
| Występują nieoczekiwane opóźnienia w transferze danych. |Powolny transfer danych. |Błędy ograniczania występują po przekroczeniu docelowych skalowalności usługi magazynu. Usługa magazynu robi to, aby upewnić się, że żaden pojedynczy klient lub dzierżawca może korzystać z usługi kosztem innych. Aby uzyskać więcej informacji na temat rozwiązywania problemów z kontem magazynu platformy Azure, przejdź do [monitorowania, diagnozowania i rozwiązywania problemów z usługą Microsoft Azure Storage](../storage/common/storage-monitoring-diagnosing-troubleshooting.md). |
| Na <*nazwie*\>urządzenia jest mało miejsca na dysku rezerwacji lokalnej . |Powolny czas reakcji. |10% całkowitego aprowizowanego rozmiaru <*nazwy* \> urządzenia jest zarezerwowana na urządzeniu lokalnym, a obecnie brakuje miejsca zarezerwowanego. Obciążenie <*nazwą* \> urządzenia generuje wyższą szybkość zmian lub być może niedawno zmigrowano dużą ilość danych. Może to spowodować zmniejszenie wydajności. Aby rozwiązać ten problem, rozważ jedną z następujących czynności:<ul><li>Zwiększ przepustowość chmury do tego urządzenia.</li><li>Zmniejszanie lub przenoszenie obciążeń do innego woluminu lub udziału.</li></ul> |

### <a name="security-alerts"></a>Alerty zabezpieczeń

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane działania |
|:--- |:--- |:--- |
| Hasło do <*nazwy* \> urządzenia wygaśnie w ciągu <*dni.* \> |Ostrzeżenie o haśle. |Hasło wygaśnie w ciągu <*dni.* \> Rozważ zmianę hasła. Aby uzyskać więcej informacji, przejdź do [tematu Zmienianie hasła administratora urządzenia StorSimple Virtual Array](storsimple-virtual-array-change-device-admin-password.md). |

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o tablicy wirtualnej StorSimple](storsimple-ova-overview.md).
