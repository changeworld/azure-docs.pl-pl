---
title: Wyświetlanie alertów dla urządzenia z serii StorSimple serii 8000 i zarządzanie nimi
description: W tym artykule StorSimple alert warunki i ważność, jak skonfigurować powiadomienia alertów i jak używać StorSimple Device Manager usługi do zarządzania alertami.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: ff50836e1438b8d35f26ddfdf165084406f52faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267822"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-storsimple-alerts"></a>Użyj usługi Menedżer urządzeń StorSimple, aby wyświetlać alerty StorSimple i zarządzać nimi

## <a name="overview"></a>Omówienie

**Alerty** bloku w StorSimple Device Manager usługi umożliwia przeglądanie i czyszczenie storsimple alerty związane z urządzeniem w czasie rzeczywistym. Z tego bloku można centralnie monitorować problemy zdrowotne urządzeń StorSimple i ogólne rozwiązanie Microsoft Azure StorSimple.

W tym samouczku opisano typowe warunki alertów, poziomy ważności alertów i sposób konfigurowania powiadomień alertów. Ponadto zawiera tabele szybkiego odwołania alertów, które umożliwiają szybkie zlokalizowanie określonego alertu i odpowiednie reagowanie.

![Strona Alerty](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="common-alert-conditions"></a>Typowe warunki alarmowe

Urządzenie StorSimple generuje alerty w odpowiedzi na różne warunki. Poniżej przedstawiono najczęstsze typy stanów alertów:

* **Problemy ze sprzętem** — te alerty informują o kondycji sprzętu. Pozwalają one wiedzieć, czy potrzebne są uaktualnienia oprogramowania układowego, jeśli wystąpiły problemy z interfejsem sieciowym lub jeśli występuje problem z jednym z dysków danych.
* **Problemy z łącznością** — te alerty występują, gdy występują trudności w przesyłaniu danych. Problemy z komunikacją mogą wystąpić podczas przesyłania danych do i z konta magazynu platformy Azure lub z powodu braku łączności między urządzeniami a usługą StorSimple Device Manager. Problemy z komunikacją są jednymi z najtrudniejszych do naprawienia, ponieważ jest tak wiele punktów awarii. Przed kontynuowaniem bardziej zaawansowanego rozwiązywania problemów należy zawsze najpierw sprawdzić, czy łączność sieciowa i dostęp do Internetu są dostępne. Aby uzyskać pomoc dotyczącą rozwiązywania problemów, przejdź do [strony Rozwiązywanie problemów za pomocą polecenia cmdlet Test-Connection](storsimple-8000-troubleshoot-deployment.md).
* **Problemy z wydajnością** — te alerty są spowodowane, gdy system nie działa optymalnie, na przykład gdy jest pod dużym obciążeniem.

Ponadto mogą być widoczne alerty związane z zabezpieczeniami, aktualizacjami lub błędami zadań.

## <a name="alert-severity-levels"></a>Poziomy ważności alertów

Alerty mają różne poziomy ważności, w zależności od wpływu, jaki będzie miała sytuacja alertu i potrzeby odpowiedzi na alert. Poziomy ważności to:

* **Krytyczne** — ten alert jest odpowiedzią na warunek, który ma wpływ na pomyślną wydajność systemu. Akcja jest wymagana, aby upewnić się, że Usługa StorSimple nie zostanie przerwana.
* **Ostrzeżenie** — ten warunek może stać się krytyczny, jeśli nie zostanie rozwiązany. Należy zbadać sytuację i podjąć wszelkie działania wymagane, aby usunąć problem.
* **Informacje** — ten alert zawiera informacje, które mogą być przydatne w śledzeniu i zarządzaniu systemem.

## <a name="configure-alert-settings"></a>Konfigurowanie ustawień alertów

Możesz wybrać, czy chcesz otrzymywać powiadomienia pocztą e-mail o warunkach alertów dla każdego z urządzeń StorSimple. Ponadto można zidentyfikować innych adresatów powiadomień o alertach, wprowadzając ich adresy e-mail w polu **Inni adresaci wiadomości e-mail, oddzielone** średnikami.

> [!NOTE]
> Możesz wprowadzić maksymalnie 20 adresów e-mail na urządzenie.

Po włączeniu powiadomienia e-mail dla urządzenia członkowie listy powiadomień otrzymają wiadomość e-mail za każdym razem, gdy pojawi się alert krytyczny. Wiadomości będą wysyłane z *storsimple-alerts-noreply\@mail.windowsazure.com* i opisze warunek alertu. Adresaci mogą kliknąć pozycję **Wypisuj subskrypcję,** aby usunąć się z listy powiadomień e-mail.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Aby włączyć powiadomienia e-mail o alertach dla urządzenia
1. Przejdź do usługi Menedżer urządzeń StorSimple. Z listy urządzeń wybierz i kliknij urządzenie, które chcesz skonfigurować.
2. Przejdź do **ustawień** > **ogólnych** dla urządzenia.

   ![Ostrze alertów](./media/storsimple-8000-manage-alerts/configure-alerts-email2.png)
   
2. W bloku **Ustawienia ogólne** przejdź do **ustawień alertu** i ustaw następujące ustawienia:
   
   1. W polu **Wyślij powiadomienie e-mail** wybierz pozycję **TAK**.
   2. W polu **Administratorzy usługi poczty e-mail** wybierz pozycję **TAK,** aby administrator usługi i wszyscy współadministratorzy otrzymywali powiadomienia o alertach.
   3. W polu **Inni adresaci wiadomości e-mail** wprowadź adresy e-mail wszystkich innych adresatów, którzy powinni otrzymywać powiadomienia o alertach. Wprowadź nazwy w *\@formacie, który ktoś somewhere.com*. Użyj średników, aby oddzielić adresy e-mail. Można skonfigurować maksymalnie 20 adresów e-mail na urządzenie. 
      
3. Aby wysłać testowe powiadomienie e-mail, kliknij przycisk **Wyślij testową wiadomość e-mail**. Usługa StorSimple Device Manager wyświetli komunikaty o stanie podczas przesyłania dalej powiadomienia testowego.

    ![Ustawienia alertów](./media/storsimple-8000-manage-alerts/configure-alerts-email3.png)

4. Zostanie wyświetlone powiadomienie podczas wysyłania testowej wiadomości e-mail. 
   
    ![Wysłane wiadomości e-mail z powiadomieniem testowym alertów](./media/storsimple-8000-manage-alerts/configure-alerts-email4.png)
   
   > [!NOTE]
   > Jeśli nie można wysłać komunikatu powiadomienia testowego, usługa StorSimple Device Manager wyświetli odpowiedni komunikat o błędzie. Zaczekaj kilka minut, a następnie spróbuj ponownie wysłać wiadomość z powiadomieniem testowym. 

5. Po zakończeniu konfiguracji kliknij przycisk **Zapisz**. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**.

     ![Wysłane wiadomości e-mail z powiadomieniem testowym alertów](./media/storsimple-8000-manage-alerts/configure-alerts-email5.png)

## <a name="view-and-track-alerts"></a>Wyświetlanie i śledzenie alertów

Blok podsumowania usługi StorSimple Device Manager umożliwia szybki rzut oka na liczbę alertów na urządzeniach, uporządkowaną według poziomu ważności.

![Pulpit nawigacyjny alertów](./media/storsimple-8000-manage-alerts/device-summary4.png)

Kliknięcie poziomu ważności powoduje otwarcie bloku **Alerty.** Wyniki obejmują tylko alerty, które odpowiadają tego poziomu ważności.

Kliknięcie alertu na liście zawiera dodatkowe szczegóły dotyczące alertu, w tym czas ostatniego zgłoszenia alertu, liczbę wystąpień alertu na urządzeniu i zalecaną akcję rozpoznawania alertu. Jeśli jest to alert sprzętowy, zidentyfikuje również składnik sprzętowy.

![Przykład alertu sprzętowego](./media/storsimple-8000-manage-alerts/configure-alerts-email14.png)

Szczegóły alertu można skopiować do pliku tekstowego, jeśli konieczne jest wysłanie informacji do pomocy technicznej firmy Microsoft. Po wykonaniu zalecenia i rozwiązaniu warunku alertu w środowisku lokalnym należy wyczyścić alert z urządzenia, wybierając alert w bloku Alerty i klikając **pozycję Wyczyść**. **Alerts** Aby wyczyścić wiele alertów, zaznacz każdy alert, kliknij dowolną kolumnę z wyjątkiem kolumny **Alert,** a następnie kliknij przycisk **Wyczyść** po wybraniu wszystkich alertów do wyczyszczenie. Należy zauważyć, że niektóre alerty są automatycznie czyszczone po rozwiązaniu problemu lub gdy system aktualizuje alert o nowe informacje.

Po kliknięciu **przycisku Wyczyść,** będziesz mieć możliwość podania komentarzy na temat alertu i kroków, które zostały podjęte w celu rozwiązania problemu. Niektóre zdarzenia zostaną wyczyszczone przez system, jeśli inne zdarzenie zostanie wyzwolone z nowymi informacjami. W takim przypadku zostanie wyświetlony następujący komunikat.

![Wyczyść komunikat ostrzegawczy](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Sortowanie i przeglądanie alertów

Bardziej wydajne może być uruchamianie raportów w alertach, dzięki czemu można je przeglądać i czyścić w grupach. Ponadto **alerty** bloku może wyświetlać do 250 alertów. Jeśli liczba alertów została przekroczona, nie wszystkie alerty będą wyświetlane w widoku domyślnym. Można połączyć następujące pola, aby dostosować wyświetlane alerty:

* **Stan** — można wyświetlić alerty **aktywne** lub **wyczyszczone.** Aktywne alerty są nadal wyzwalane w systemie, podczas gdy wyczyszczone alerty zostały ręcznie wyczyszczone przez administratora lub programowo wyczyszczone, ponieważ system zaktualizował stan alertu o nowe informacje.
* **Ważność** — można wyświetlać alerty o wszystkich poziomach ważności (krytyczne, ostrzeżenie, informacje) lub tylko pewną ważność, takie jak tylko alerty krytyczne.
* **Źródło** — można wyświetlać alerty ze wszystkich źródeł lub ograniczyć alerty do tych, które pochodzą z usługi lub jednego lub wszystkich urządzeń.
* **Zakres czasu** — określając **daty od** i **do** i sygnatury czasowe, można przeglądać alerty w okresie, który Cię interesuje.

![Lista alertów](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="alerts-quick-reference"></a>Szybkie alerty

W poniższych tabelach przedstawiono niektóre alerty usługi Microsoft Azure StorSimple, które mogą wystąpić, a także dodatkowe informacje i zalecenia, jeśli są dostępne. Alerty urządzenia StorSimple należą do jednej z następujących kategorii:

* [Alerty o łączności w chmurze](#cloud-connectivity-alerts)
* [Alerty klastra](#cluster-alerts)
* [Alerty odzyskiwania po awarii](#disaster-recovery-alerts)
* [Alerty sprzętowe](#hardware-alerts)
* [Alerty o niepowodzeniu zadania](#job-failure-alerts)
* [Alerty woluminu przypięte lokalnie](#locally-pinned-volume-alerts)
* [Alerty sieciowe](#networking-alerts)
* [Alerty dotyczące wydajności](#performance-alerts)
* [Alerty zabezpieczeń](#security-alerts)
* [Alerty pakietu pomocy technicznej](#support-package-alerts)

### <a name="cloud-connectivity-alerts"></a>Alerty o łączności w chmurze

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane działania |
|:--- |:--- |:--- |
| Nie można ustanowić łączności z *<nazwą poświadczeń w chmurze,*> nie można ustanowić. |Nie można połączyć się z kontem magazynu. |Wygląda na to, że może wystąpić problem z łącznością z urządzeniem. Uruchom `Test-HcsmConnection` polecenie cmdlet z interfejsu programu Windows PowerShell dla storsimple na urządzeniu, aby zidentyfikować i rozwiązać problem. Jeśli ustawienia są poprawne, problem może być z poświadczeniami konta magazynu, dla którego alert został zgłoszony. W takim przypadku `Test-HcsStorageAccountCredential` użyj polecenia cmdlet, aby ustalić, czy istnieją problemy, które można rozwiązać.<ul><li>Sprawdź ustawienia sieciowe.</li><li>Sprawdź poświadczenia konta magazynu.</li></ul> |
| Nie otrzymaliśmy pulsu z urządzenia przez ostatnie <*numer*> minut. |Nie można połączyć się z urządzeniem. |Wygląda na to, że występuje problem z łącznością z urządzeniem. Użyj `Test-HcsmConnection` polecenia cmdlet z interfejsu programu Windows PowerShell dla storsimple na urządzeniu, aby zidentyfikować i rozwiązać problem lub skontaktować się z administratorem sieci. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>StorSimple zachowanie, gdy łączność w chmurze nie powiedzie się

Co się stanie, jeśli łączność w chmurze nie powiedzie się na moim urządzeniu StorSimple działającym w produkcji?

Jeśli łączność w chmurze ulegnie awarii na urządzeniu produkcyjnym StorSimple, w zależności od stanu urządzenia mogą wystąpić następujące zdarzenia:

* **W przypadku danych lokalnych na urządzeniu**: Przez pewien czas nie będzie żadnych zakłóceń i odczyty będą nadal obsługiwane. Jednak w miarę wzrostu liczby nieuregulowane japozycy wzrasta i przekracza limit, odczyty mogą zacząć się niepowodzeniem.

    W zależności od ilości danych na urządzeniu zapisy będą również występować przez pierwsze kilka godzin po zakłóceniu łączności w chmurze. Zapisy spowolnią i ostatecznie zaczną się niepowodzeniem, jeśli łączność w chmurze zostanie zakłócona na kilka godzin. (Na urządzeniu znajduje się tymczasowe przechowywanie danych, które mają zostać wypchnięte do chmury. Ten obszar jest opróżniany podczas wysyłania danych. Jeśli łączność nie powiedzie się, dane w tym obszarze magazynu nie zostaną wypchnięte do chmury, a we/wy zakończy się niepowodzeniem.)
* **W przypadku danych w chmurze**: W przypadku większości błędów łączności w chmurze zwracany jest błąd. Po przywróceniu łączności usługi we/wy są wznawiane bez konieczności przełączenia woluminu do trybu online. W rzadkich przypadkach interwencji użytkownika może być wymagane, aby przywrócić wolumin w trybie online z witryny Azure portal.
* **W przypadku migawek w chmurze w toku:** operacja jest ponowiona kilka razy w ciągu 4-5 godzin, a jeśli łączność nie zostanie przywrócona, migawki w chmurze zakończy się niepowodzeniem.

### <a name="cluster-alerts"></a>Alerty klastra

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane działania |
|:--- |:--- |:--- |
| > *nazwa urządzenia* uległa awarii <. |Urządzenie jest w trybie konserwacji. |Urządzenie nie powiodło się z powodu wejścia lub zakończenia trybu konserwacji. Jest to normalne i nie jest potrzebne żadne działanie. Po potwierdzeniu tego alertu wyczyść go ze strony alertów. |
| > *nazwa urządzenia* uległa awarii <. |Oprogramowanie układowe lub oprogramowanie urządzenia zostało właśnie zaktualizowane. |Nastąpiła przewijalnia awaryjna klastra z powodu aktualizacji. Jest to normalne i nie jest potrzebne żadne działanie. Po potwierdzeniu tego alertu wyczyść go ze strony alertów. |
| > *nazwa urządzenia* uległa awarii <. |Kontroler został zamknięty lub ponownie uruchomiony. |Urządzenie zostało przejęte awaryjnie, ponieważ aktywny kontroler został zamknięty lub ponownie uruchomiony przez administratora. Nie są potrzebne żadne działania. Po potwierdzeniu tego alertu wyczyść go ze strony alertów. |
| > *nazwa urządzenia* uległa awarii <. |Planowane przemija awaryjne. |Sprawdź, czy była to planowana przewijka awaryjna. Po podjęcia odpowiednich działań, wyczyść ten alert ze strony alertów. |
| > *nazwa urządzenia* uległa awarii <. |Nieplanowane przejście awaryjne. |StorSimple jest zbudowany do automatycznego odzyskiwania po nieplanowanych pracy awaryjnej. Jeśli widzisz dużą liczbę tych alertów, skontaktuj się z pomocą techniczną firmy Microsoft. |
| > *nazwa urządzenia* uległa awarii <. |Inna/nieznana przyczyna. |Jeśli widzisz dużą liczbę tych alertów, skontaktuj się z pomocą techniczną firmy Microsoft. Po rozwiązaniu problemu wyczyść ten alert ze strony alertów. |
| Usługa urządzenia o krytycznym znaczeniu zgłasza stan jako nieudany. |Błąd usługi Datapath. |Aby uzyskać pomoc, skontaktuj się z pomocą techniczną firmy Microsoft. |
| Wirtualny adres IP interfejsu sieciowego <*DATA #*> zgłasza stan jako nie powiodło się. |Inna/nieznana przyczyna. |Czasami tymczasowe warunki mogą powodować te alerty. W takim przypadku ten alert zostanie automatycznie wyczyszczony po pewnym czasie. Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft. |
| Wirtualny adres IP interfejsu sieciowego <*DATA #*> zgłasza stan jako nie powiodło się. |Nazwa interfejsu: <*DATA #*> adres `<IP address>` IP nie mogą być przełączone do trybu online, ponieważ w sieci wykryto zduplikowany adres IP. |Upewnij się, że zduplikowany adres IP został usunięty z sieci lub ponownie skonfiguruj interfejs przy innym adresie IP. |

### <a name="disaster-recovery-alerts"></a>Alerty odzyskiwania po awarii

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane działania |
|:--- |:--- |:--- |
| Operacje odzyskiwania nie można przywrócić wszystkie ustawienia dla tej usługi. Dane konfiguracji urządzenia jest w niespójnym stanie dla niektórych urządzeń. |Niespójność danych wykryta po odzyskaniu po awarii. |Zaszyfrowane dane w usłudze nie są synchronizowane z danymi na urządzeniu. Autoryzuj nazwę *urządzenia* <> z Menedżera urządzeń StorSimple, aby rozpocząć proces synchronizacji. Użyj interfejsu programu Windows PowerShell dla `Restore-HcsmEncryptedServiceData` storsimple, aby uruchomić nazwę urządzenia <*>,* podając stare hasło jako dane wejściowe do tego polecenia cmdlet, aby przywrócić profil zabezpieczeń. Następnie uruchom `Invoke-HcsmServiceDataEncryptionKeyChange` polecenie cmdlet, aby zaktualizować klucz szyfrowania danych usługi. Po podjęcia odpowiednich działań, wyczyść ten alert ze strony alertów. |

### <a name="hardware-alerts"></a>Alerty sprzętowe

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane działania |
|:--- |:--- |:--- |
| Identyfikator składnika <*składnika sprzętowego*> zgłasza stan jako *stan* <>. | |Czasami tymczasowe warunki mogą powodować te alerty. Jeśli tak, ten alert zostanie automatycznie wyczyszczony po pewnym czasie. Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft. |
| Działanie kontrolera pasywnego. |Kontroler pasywny (pomocniczy) nie działa. |Urządzenie działa, ale jeden z kontrolerów działa nieprawidłowo. Spróbuj ponownie uruchomić ten kontroler. Jeśli problem nie został rozwiązany, skontaktuj się z pomocą techniczną firmy Microsoft. |

### <a name="job-failure-alerts"></a>Alerty o niepowodzeniu zadania

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane działania |
|:--- |:--- |:--- |
| Kopia zapasowa identyfikatora *grupy woluminów źródłowych* <nie powiodła> się. |Zadanie tworzenia kopii zapasowej nie powiodło się. |Problemy z łącznością mogą uniemożliwiać pomyślne ukończenie operacji tworzenia kopii zapasowej. Jeśli nie ma żadnych problemów z łącznością, być może osiągnięto maksymalną liczbę kopii zapasowych. Usuń wszystkie kopie zapasowe, które nie są już potrzebne i ponów próbę wykonania operacji. Po podjęcia odpowiednich działań, wyczyść ten alert ze strony alertów. |
| Klonowanie *identyfikatorów <źródłowych elementów kopii zapasowej*> do <*numerów seryjnych woluminu docelowego*> nie powiodło się. |Zadanie klonowania nie powiodło się. |Odśwież listę kopii zapasowych, aby sprawdzić, czy kopia zapasowa jest nadal prawidłowa. Jeśli kopia zapasowa jest prawidłowa, jest możliwe, że problemy z łącznością w chmurze uniemożliwiają pomyślne ukończenie operacji klonowania. Jeśli nie ma żadnych problemów z łącznością, być może osiągnięto limit magazynu. Usuń wszystkie kopie zapasowe, które nie są już potrzebne i ponów próbę wykonania operacji. Po podjęcia odpowiednich działań w celu rozwiązania problemu, wyczyść ten alert ze strony alertów. |
| Przywracanie <*źródłowych identyfikatorów elementów kopii zapasowej*> nie powiodło się. |Zadanie przywracania nie powiodło się. |Odśwież listę kopii zapasowych, aby sprawdzić, czy kopia zapasowa jest nadal prawidłowa. Jeśli kopia zapasowa jest prawidłowa, jest możliwe, że problemy z łącznością w chmurze uniemożliwiają pomyślne ukończenie operacji przywracania. Jeśli nie ma żadnych problemów z łącznością, być może osiągnięto limit magazynu. Usuń wszystkie kopie zapasowe, które nie są już potrzebne i ponów próbę wykonania operacji. Po podjęcia odpowiednich działań w celu rozwiązania problemu, wyczyść ten alert ze strony alertów. |

### <a name="locally-pinned-volume-alerts"></a>Alerty woluminu przypięte lokalnie

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane działania |
|:--- |:--- |:--- |
| Tworzenie nazwy woluminu lokalnego <*>* nie powiodło się. |Zadanie tworzenia woluminu nie powiodło się. <*Komunikat o błędzie odpowiadający> kodu błędu, który uległ awarii.* |Problemy z łącznością mogą uniemożliwiać pomyślne ukończenie operacji tworzenia przestrzeni. Woluminy przypięte lokalnie są gęsto aprowizacji i proces tworzenia miejsca obejmuje rozlanie woluminów warstwowych do chmury. Jeśli nie ma żadnych problemów z łącznością, być może wyczerpano miejsce lokalne na urządzeniu. Przed ponowieniem tej operacji należy określić, czy na urządzeniu istnieje miejsce. |
| Rozszerzenie woluminu lokalnego <*nazwa woluminu*> nie powiodła się. |Zadanie modyfikacji woluminu nie powiodło się z powodu <*komunikatu o błędzie odpowiadającego> kodu błędu, który nie powiódł się.* |Problemy z łącznością mogą uniemożliwiać pomyślne ukończenie operacji rozszerzania woluminu. Woluminy przypięte lokalnie są aprowizacji grubo i proces rozszerzania istniejącej przestrzeni obejmuje rozlanie woluminów warstwowych do chmury. Jeśli nie ma żadnych problemów z łącznością, być może wyczerpano miejsce lokalne na urządzeniu. Przed ponowieniem tej operacji należy określić, czy na urządzeniu istnieje miejsce. |
| Konwersja *nazwy woluminu* <woluminu> nie powiodła się. |Zadanie konwersji woluminu w celu konwersji typu woluminu z lokalnie przypiętego do warstwowego nie powiodło się. |Nie można ukończyć konwersji woluminu z typu przypiętego lokalnie do warstwowego. Upewnij się, że nie ma żadnych problemów z łącznością uniemożliwiających pomyślne ukończenie operacji. Aby rozwiązać problemy z łącznością, przejdź do [sekcji Rozwiązywanie problemów za pomocą polecenia cmdlet Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Oryginalny wolumin przypięty lokalnie został teraz oznaczony jako wolumin warstwowy, ponieważ niektóre dane z woluminu przypiętego lokalnie rozlały się do chmury podczas konwersji. Wynikowy wolumin warstwowy nadal zajmuje miejsce lokalne na urządzeniu, które nie może zostać odzyskane dla przyszłych woluminów lokalnych.<br>Rozwiąż wszelkie problemy z łącznością, wyczyść alert i przekonwertuj ten wolumin z powrotem na oryginalny typ woluminu przypiętego lokalnie, aby upewnić się, że wszystkie dane są ponownie udostępniane lokalnie. |
| Konwersja *nazwy woluminu* <woluminu> nie powiodła się. |Zadanie konwersji woluminu w celu konwersji typu woluminu z warstwowego na przypięte lokalnie nie powiodło się. |Nie można ukończyć konwersji woluminu z typu warstwowego na przypięty lokalnie. Upewnij się, że nie ma żadnych problemów z łącznością uniemożliwiających pomyślne ukończenie operacji. Aby rozwiązać problemy z łącznością, przejdź do [sekcji Rozwiązywanie problemów za pomocą polecenia cmdlet Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Oryginalny wolumin warstwowy oznaczony teraz jako wolumin przypięty lokalnie w ramach procesu konwersji nadal ma dane przebywające w chmurze, podczas gdy gęsto aprowizowanego miejsca na urządzeniu dla tego woluminu nie można już odzyskać dla przyszłych woluminów lokalnych.<br>Rozwiąż wszelkie problemy z łącznością, wyczyść alert i przekonwertuj ten wolumin z powrotem na oryginalny typ woluminu warstwowego, aby zapewnić, że przestrzeń lokalna jest gęsto aprowiowana na urządzeniu. |
| Zbliżanie się do lokalnego zużycia przestrzeni dla migawek lokalnych <*nazwa grupy woluminów*> |Lokalne migawki dla zasad tworzenia kopii zapasowych może wkrótce zabraknąć miejsca i zostać unieważnione, aby uniknąć błędów zapisu hosta. |Częste migawki lokalne wraz z wysokimi zmianami danych w woluminach skojarzonych z tą grupą zasad tworzenia kopii zapasowych powodują szybkie użycie miejsca lokalnego na urządzeniu. Usuń wszystkie migawki lokalne, które nie są już potrzebne. Ponadto zaktualizuj harmonogramy migawek lokalnych dla tej zasady tworzenia kopii zapasowych, aby wykonać rzadsze migawki lokalne i upewnij się, że migawki w chmurze są regularnie pobierane. Jeśli te akcje nie zostaną podjęte, miejsce lokalne dla tych migawek może wkrótce zostać wyczerpane, a system automatycznie je usunie, aby upewnić się, że zapisy hosta będą nadal przetwarzane pomyślnie. |
| Migawki lokalne dla <*nazwy grupy woluminów*> zostały unieważnione. |Migawki lokalne dla <*nazwa grupy woluminów*> zostały unieważnione, a następnie usunięte, ponieważ przekraczały przestrzeń lokalną na urządzeniu. |Aby upewnić się, że nie powtórzy się to w przyszłości, przejrzyj harmonogramy migawek lokalnych dla tej zasady tworzenia kopii zapasowych i usuń wszystkie migawki lokalne, które nie są już potrzebne. Częste migawki lokalne wraz z wysokimi zmianami danych w woluminach skojarzonych z tą grupą zasad tworzenia kopii zapasowych mogą spowodować szybkie użycie miejsca lokalnego na urządzeniu. |
| Przywracanie <*źródłowych identyfikatorów elementów kopii zapasowej*> nie powiodło się. |Zadanie przywracania nie powiodło się. |Jeśli w tej zasadach kopii zapasowej jest przypięty lokalnie lub zawierano kombinację woluminów przypiętych lokalnie i warstwowych, odśwież listę kopii zapasowych, aby sprawdzić, czy kopia zapasowa jest nadal prawidłowa. Jeśli kopia zapasowa jest prawidłowa, jest możliwe, że problemy z łącznością w chmurze uniemożliwiają pomyślne ukończenie operacji przywracania. Woluminy przypięte lokalnie, które zostały przywrócone jako część tej grupy migawek, nie mają wszystkich ich danych pobranych na urządzenie, a jeśli masz kombinację woluminów warstwowych i przypiętych lokalnie w tej grupie migawek, nie będą one synchronizowane ze sobą. Aby pomyślnie zakończyć operację przywracania, należy włączyć woluminy w tej grupie do trybu offline na hoście i ponowić próbę operacji przywracania. Należy zauważyć, że wszelkie modyfikacje danych woluminu, które zostały wykonane podczas procesu przywracania zostaną utracone. |

### <a name="networking-alerts"></a>Alerty sieciowe

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane działania |
|:--- |:--- |:--- |
| Nie można uruchomić usługi StorSimple. |Błąd ścieżki danych |Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft. |
| Wykryto zduplikowany adres IP dla "Data0". | |System wykrył konflikt adresu IP "10.0.0.1". Zasób sieciowy "Data0" na * \<urządzeniu1>* jest w trybie offline. Upewnij się, że ten adres IP nie jest używany przez żadną inną jednostkę w tej sieci. Aby rozwiązać problemy z siecią, przejdź do [tematu Rozwiązywanie problemów za pomocą polecenia cmdlet Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Skontaktuj się z administratorem sieci, aby uzyskać pomoc w rozwiązaniu tego problemu. Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft. |
| Adres IPv4 (lub IPv6) dla "Data0" jest w trybie offline. | |Zasób sieciowy "Data0" o adresie IP "10.0.0.1". i długość prefiksu "22" na * \<urządzeniu1>* jest w trybie offline. Upewnij się, że porty przełącznika, do których podłączony jest ten interfejs, działają. Aby rozwiązać problemy z siecią, przejdź do [tematu Rozwiązywanie problemów za pomocą polecenia cmdlet Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
| Nie można połączyć się z usługą uwierzytelniania. |Błąd ścieżki danych |URL, który jest używany do uwierzytelniania nie jest osiągalny. Upewnij się, że reguły zapory zawierają wzorce adresów URL określone dla urządzenia StorSimple. Aby uzyskać więcej informacji na temat wzorców adresów\/URL w witrynie Azure portal, przejdź do https: /aka.ms/ss-8000-network-reqs. Jeśli używasz usługi Azure Government Cloud, przejdź\/do wzorców adresów URL w https: /aka.ms/ss8000-gov-network-reqs.|

### <a name="performance-alerts"></a>Alerty dotyczące wydajności

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane działania | |
|:--- |:--- |:--- | --- |
| Obciążenie urządzenia przekroczyło <*próg*>. |Wolniejsze niż oczekiwano czasy odpowiedzi. |Urządzenie zgłasza wykorzystanie przy dużym obciążeniu wejściowym/wyjściowym. Może to spowodować, że urządzenie nie będzie działać tak dobrze, jak powinno. Przejrzyj obciążenia, które zostały dołączone do urządzenia i określ, czy istnieją jakieś, które mogą zostać przeniesione do innego urządzenia lub które nie są już konieczne.|
| Nie można uruchomić usługi StorSimple. |Błąd ścieżki danych |Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft. |

### <a name="security-alerts"></a>Alerty zabezpieczeń

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane działania |
|:--- |:--- |:--- |
| Rozpoczęto sesję pomocy technicznej firmy Microsoft. |Sesja pomocy technicznej innej firmy, do która uzyskała dostęp. |Proszę potwierdzić, że ten dostęp jest autoryzowany. Po podjęcia odpowiednich działań, wyczyść ten alert ze strony alertów. |
| Hasło do> *elementu* <wygaśnie <*>.* |Zbliża się wygaśnięcie hasła. |Zmień hasło przed jego wygaśnięciem. |
| Brak informacji o konfiguracji zabezpieczeń dla> *identyfikatora elementu* <. | |Woluminów skojarzonych z tym kontenerem woluminów nie można użyć do replikowania konfiguracji StorSimple. Aby upewnić się, że dane są bezpiecznie przechowywane, zaleca się usunięcie kontenera woluminów i woluminów skojarzonych z kontenerem woluminów. Po podjęcia odpowiednich działań, wyczyść ten alert ze strony alertów. |
| <*numer*> próby logowania nie powiodły się dla> *identyfikatora elementu* <. |Wiele nieudanych prób logowania. |Urządzenie może być atakowane lub autoryzowany użytkownik próbuje połączyć się z nieprawidłowym hasłem.<ul><li>Skontaktuj się z autoryzowanymi użytkownikami i sprawdź, czy te próby pochodziły z legalnego źródła. Jeśli nadal widzisz dużą liczbę nieudanych prób logowania, rozważ wyłączenie zdalnego zarządzania i skontaktowanie się z administratorem sieci. Po podjęcia odpowiednich działań, wyczyść ten alert ze strony alertów.</li><li>Sprawdź, czy wystąpienia Menedżera migawek są skonfigurowane przy przy obliczu prawidłowego hasła. Po podjęcia odpowiednich działań, wyczyść ten alert ze strony alertów.</li></ul>Aby uzyskać więcej informacji, przejdź do [temat Zmienianie wygasłego hasła urządzenia](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password). |
| Wystąpił co najmniej jeden błąd podczas zmiany klucza szyfrowania danych usługi. | |Wystąpiły błędy podczas zmiany klucza szyfrowania danych usługi. Po uwzględnienie warunków błędu uruchom `Invoke-HcsmServiceDataEncryptionKeyChange` polecenie cmdlet z interfejsu programu Windows PowerShell interface for StorSimple na urządzeniu, aby zaktualizować usługę. Jeśli ten problem będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft. Po rozwiązaniu problemu wyczyść ten alert ze strony alertów. |

### <a name="support-package-alerts"></a>Alerty pakietu pomocy technicznej

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane działania |
|:--- |:--- |:--- |
| Tworzenie pakietu pomocy technicznej nie powiodło się. |StorSimple nie może wygenerować pakietu. |Ponów próbę wykonania tej operacji. Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft. Po rozwiązaniu problemu wyczyść ten alert ze strony alertów. |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [błędach StorSimple i rozwiązywaniu problemów z wdrażaniem urządzeń](storsimple-8000-troubleshoot-deployment.md).

