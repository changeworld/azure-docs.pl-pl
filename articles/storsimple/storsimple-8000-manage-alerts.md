---
title: Wyświetlanie alertów i zarządzanie nimi dla urządzenia z serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Opisuje StorSimple warunków alertów i ważność, jak skonfigurować powiadomienia o alertach i alertami można zarządzać przy użyciu usługi Menedżer StorSimple urządzenia.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: e86b6af562208e51e36b4679fd088ea399ce70b8
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2018
ms.locfileid: "27745782"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-storsimple-alerts"></a>Użyj usługi Menedżer StorSimple urządzenia, aby wyświetlić alerty i zarządzaj nimi StorSimple

## <a name="overview"></a>Przegląd

**Alerty** bloku w usłudze Menedżer StorSimple urządzenia umożliwia przeglądanie i wyczyść alerty związane z urządzenia StorSimple na podstawie w czasie rzeczywistym. Z tego bloku można centralnie monitorować problemy dotyczące kondycji urządzenia StorSimple i ogólnego rozwiązania Microsoft Azure StorSimple.

W tym samouczku opisano typowe warunki alertu, poziomy ważności alertów i Konfigurowanie powiadomień o alertach. Ponadto zawiera tabele alertu krótkimi opisami, które umożliwiają szybkie zlokalizować określony alert i reagowania.

![Strona alertów](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="common-alert-conditions"></a>Typowe warunki alertu

Urządzenia StorSimple generuje alerty w odpowiedzi na szereg warunków. Poniżej przedstawiono najbardziej typowych alertów warunki:

* **Problemy ze sprzętem** — te alerty informujące o kondycji sprzętu. Umożliwiają one Cię o tym, czy aktualizacje oprogramowania są wymagane, jeśli interfejs sieciowy ma problemy w przypadku problemu z jednym z dysków z danymi.
* **Problemy z łącznością** — te alerty występują w przypadku trudności podczas transferu danych. Problemy z komunikacją mogą wystąpić podczas transferu danych do i z kontem magazynu platformy Azure lub z powodu braku łączności między urządzeniami i usługi Menedżer StorSimple urządzenia. Problemy z komunikacją się najtrudniejsze rozwiązać problem, ponieważ istnieje wiele punktów awarii. Należy zawsze najpierw sprawdź, czy połączenie sieciowe oraz dostęp do Internetu są dostępne przed kontynuowaniem do bardziej zaawansowanego rozwiązywania problemów. Aby uzyskać pomoc dotyczącą rozwiązywania problemów, przejdź do [Rozwiązywanie problemów za pomocą polecenia cmdlet Test-Connection](storsimple-8000-troubleshoot-deployment.md).
* **Problemy z wydajnością** — te alerty są powodowane przeprowadzania systemu nie jest optymalnie, np. gdy jest mocno obciążony.

Ponadto można napotkać alertów dotyczących zabezpieczeń, aktualizacje lub niepowodzenia zadań.

## <a name="alert-severity-levels"></a>Poziomy ważności alertów

Alerty mają różne poziomy ważności, w zależności od wpływu tego alertu sytuacji i potrzebę odpowiedzi na alert. Dostępne są następujące poziomy ważności:

* **Krytyczne** — alert jest w odpowiedzi na warunek, który ma wpływ na pomyślne wydajność systemu. Czynności upewnij się, że StorSimple usługi nie zostało przerwane.
* **Ostrzeżenie** — ten stan może stać się krytyczny, jeśli nie uda się rozwiązać. Należy zbadać sytuację i wykonaj czynności wymagane do wyczyść problem.
* **Informacje o** — tego alertu zawiera informacje, które mogą być przydatne w śledzenia i zarządzanie systemem.

## <a name="configure-alert-settings"></a>Konfigurowanie ustawień alertów

Można wybrać, czy chcesz otrzymać powiadomienie e-mail alertów warunki dla każdego urządzenia StorSimple. Ponadto można zidentyfikować innych odbiorców powiadomień o alertach, wprowadzając ich adresów e-mail w **innych adresatów wiadomości e-mail** okno, oddzielając je średnikami.

> [!NOTE]
> Możesz wprowadzić maksymalnie 20 adresy e-mail na urządzeniu.

Po włączeniu powiadomień e-mail dla urządzeń, elementy członkowskie na liście powiadomień otrzymają wiadomość e-mail z każdym alert krytyczny wystąpieniu. Wysyłane z  *storsimple-alerts-noreply@mail.windowsazure.com*  i opisano stan alertu. Można kliknąć adresatów **anulowania subskrypcji** usunąć siebie z listy powiadomień poczty e-mail.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Aby włączyć powiadomienia e-mail o alertach dla urządzenia
1. Przejdź do usługi Menedżer urządzeń StorSimple. Z listy urządzeń wybierz, a następnie kliknij urządzenie, które chcesz skonfigurować.
2. Przejdź do **ustawienia** > **ogólne** dla urządzenia.

   ![Blok alerty](./media/storsimple-8000-manage-alerts/configure-alerts-email2.png)
   
2. W **ustawienia ogólne** bloku, przejdź do **ustawienia alertu** i określić następujące ustawienia:
   
   1. W **wysyłania powiadomień e-mail** pól, zaznacz **tak**.
   2. W **poczty E-mail Administratorzy usługi** pól, zaznacz **tak** do administratora usługi, a wszystkie współadministratorów otrzymywać powiadomienia o alertach.
   3. W **innych adresatów wiadomości e-mail** wprowadź adresy e-mail wszystkich adresatów, którzy mają otrzymywać powiadomienia o alertach. Wprowadź nazwy w formacie  *someone@somewhere.com* . Użyj średników do rozdzielania adresów e-mail. Można skonfigurować maksymalnie 20 adresy e-mail na urządzeniu. 
      
3. Aby wysłać powiadomienie e-mail testowe, kliknij przycisk **Wyślij testową wiadomość e-mail**. Usługę Menedżer StorSimple urządzenia będą wyświetlane komunikaty o stanie, jak przesyła powiadomienie testowe.

    ![Ustawienia alertów](./media/storsimple-8000-manage-alerts/configure-alerts-email3.png)

4. Po wysłaniu testowa wiadomość e-mail zostanie wyświetlone powiadomienie. 
   
    ![Alerty przetestować powiadomienia e-mail wysyłanego](./media/storsimple-8000-manage-alerts/configure-alerts-email4.png)
   
   > [!NOTE]
   > Jeśli nie można wysłać komunikatu powiadomienia testu, usługi Menedżer StorSimple urządzenia wyświetli odpowiedni komunikat o błędzie. Poczekaj kilka minut, a następnie spróbuj ponownie wysłać komunikatu powiadomienia testu. 

5. Po zakończeniu konfiguracji kliknij **zapisać**. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**.

     ![Alerty przetestować powiadomienia e-mail wysyłanego](./media/storsimple-8000-manage-alerts/configure-alerts-email5.png)

## <a name="view-and-track-alerts"></a>Wyświetlanie i śledzenie alertów

Blok podsumowania usługi Menedżer StorSimple urządzenia zapewnia szybkiego dostępu na liczbę alertów na urządzeniach, aby uporządkowane według poziomu ważności.

![Pulpit nawigacyjny alertów](./media/storsimple-8000-manage-alerts/device-summary4.png)

Poziom ważności kliknięcie spowoduje otwarcie **alerty** bloku. Wyniki obejmują tylko alerty spełniające ten poziom ważności.

Kliknięcie alertu na liście zapewnia dodatkowe szczegóły alertu, czasu, gdy został zgłoszony alert, w tym liczbę wystąpień alertu na urządzeniu i zalecanej akcji, aby rozwiązać alert. Jeśli jest to alert sprzętu, również zidentyfikuje składnik sprzętowy.

![Przykład alertu sprzętu](./media/storsimple-8000-manage-alerts/configure-alerts-email14.png)

Szczegóły alertu można skopiować do pliku tekstowego, jeśli chcesz wysłać informacje do firmy Microsoft Support. Po po zalecenia i rozpoznać warunku alertu lokalną, należy wyczyścić alert z urządzenia, wybierając alert w **alerty** bloku i klikając **wyczyść**. Aby wyczyścić wiele alertów, wybierz poszczególne alerty, kliknij przycisk wszystkich kolumn z wyjątkiem **Alert** kolumny, a następnie kliknij przycisk **wyczyść** po wybraniu wszystkich alertów do wyczyszczenia. Należy pamiętać, niektóre alerty automatycznie są czyszczone po rozwiązaniu problemu, lub gdy system aktualizuje alertu o nowe informacje.

Po kliknięciu **wyczyść**, będzie mieć możliwość zapewnienia komentarze dotyczące alertu i kroki, które miały Aby rozwiązać ten problem. Niektóre zdarzenia zostaną usunięte przez system, jeśli inny zdarzenie zostanie wyzwolone przy użyciu nowych informacji. W takim przypadku zostanie wyświetlony następujący komunikat.

![Komunikat alertu czyszczenia.](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Sortowanie i przegląd alertów

Może być bardziej wydajne, aby uruchamiać raporty alerty, dzięki czemu można przejrzeć, a następnie je wyczyść w grupach. Ponadto **alerty** bloku można wyświetlać alerty do 250. Po przekroczeniu tej liczby alertów, nie wszystkie alerty będą wyświetlane w widoku domyślnym. Możesz połączyć ze sobą następujące pola, aby dostosować alerty są wyświetlane:

* **Stan** — można wyświetlić **Active** lub **wyczyszczone** alertów. Aktywne alerty nadal są inicjowane w systemie, gdy alerty wyczyszczone zostały albo ręcznie usunięte przez administratora lub programistycznie wyczyścić, ponieważ system Aktualizowanie stanu alertu nowymi informacjami.
* **Ważność** — można wyświetlać alerty wszystkie poziomy ważności (krytyczny, ostrzeżenie, informacje o) lub tylko niektórych ważność, takich jak alerty tylko krytyczne.
* **Źródło** — wyświetlania alertów ze wszystkich źródeł lub ograniczyć alerty do tych, które pochodzą z usługi lub jednego lub wszystkich urządzeń.
* **Zakres czasu** — określając **z** i **do** dat i sygnatury czasowe, można wyświetlać alerty w czasie, który chcesz.

![Lista alertów](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="alerts-quick-reference"></a>Krótki przewodnik alertów

W poniższych tabelach przedstawiono niektóre alerty Microsoft Azure StorSimple, które można napotkać, a także dodatkowe informacje i zalecenia w przypadku, gdy dostępna. Alerty urządzenia StorSimple można podzielić na następujące kategorie:

* [Alerty łączności chmury](#cloud-connectivity-alerts)
* [Alerty klastra](#cluster-alerts)
* [Alerty odzyskiwania po awarii](#disaster-recovery-alerts)
* [Alerty sprzętu](#hardware-alerts)
* [Alerty błędów zadania](#job-failure-alerts)
* [Alerty woluminu przypiętego lokalnie](#locally-pinned-volume-alerts)
* [Alerty sieci](#networking-alerts)
* [Alerty wydajności](#performance-alerts)
* [Alerty zabezpieczeń](#security-alerts)
* [Obsługa alertów pakietu](#support-package-alerts)

### <a name="cloud-connectivity-alerts"></a>Alerty łączności chmury

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Łączność z <*nazwa poświadczeń w chmurze*> nie powiodło się. |Nie można nawiązać połączenia z kontem magazynu. |Wygląda na to, może być problem z łącznością z urządzeniem. Uruchom `Test-HcsmConnection` polecenia cmdlet interfejsu programu Windows PowerShell dla urządzenia StorSimple na urządzeniu, aby zidentyfikować i rozwiązać problem. Jeśli ustawienia są poprawne, problem może być przy użyciu poświadczeń konta magazynu, którego dotyczy alert. W takim przypadku należy użyć `Test-HcsStorageAccountCredential` polecenia cmdlet, aby określić, czy istnieją problemy, które można rozwiązać.<ul><li>Sprawdź ustawienia sieciowe.</li><li>Sprawdź poświadczenia konta magazynu.</li></ul> |
| Nie Otrzymaliśmy pulsu z urządzenia przez ostatnie <*numer*> minut. |Nie można nawiązać połączenia z urządzeniem. |Prawdopodobnie istnieje problem z łącznością z urządzeniem. Użyj `Test-HcsmConnection` polecenia cmdlet interfejsu programu Windows PowerShell dla urządzenia StorSimple na urządzeniu, aby zidentyfikować i rozwiązać problem lub skontaktuj się z administratorem sieci. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>Zachowanie StorSimple, gdy łączność chmury nie powiodło się.

Co się stanie, jeśli łączność chmury nie powiedzie się dla urządzenia StorSimple w środowisku produkcyjnym?

Jeśli chmury połączenie nie powiedzie się na urządzeniu StorSimple w środowisku produkcyjnym, następnie w zależności od stanu urządzenia, poniżej może wystąpić:

* **Dla danych lokalnych w urządzeniu**: przez pewien czas nie będą bez przerw w działaniu i odczytuje będą w dalszym ciągu można zrealizować. Liczba oczekujących zwiększa i przekracza limit, odczytami można kończą się niepowodzeniem.

    W zależności od ilości danych na urządzeniu zapisami również będzie występować pierwsze kilka godzin po przerw w działaniu w chmurze łączności. Zapisy zostanie następnie spowolnić i ostatecznie kończą się niepowodzeniem, jeśli łączność chmury jest zakłócona przez kilka godzin. (Brak magazyn tymczasowy na urządzeniu w przypadku danych, który ma zostać przeniesiony do chmury. Ten obszar jest opróżniany, gdy dane są przesyłane. Jeśli połączenie nie powiedzie się, dane w tym obszarze magazynu nie zostanie przekazany do chmury, a nie powiedzie się we/wy.)
* **Dla danych w chmurze**: większość błędów łączności chmury, zwracany jest błąd. Po przywróceniu łączności, użytkownik nie woluminu w trybie online do wznowienia systemu IOs. W rzadkich przypadkach użytkownik może być wymagane do przywrócenia woluminu w trybie online w portalu Azure.
* **Dla migawki w chmurze w toku**: jest ponowić próbę wykonania operacji kilka razy w ciągu 4 do 5 godzin, jeśli połączenie nie zostanie przywrócona, niepowodzeniem migawki w chmurze.

### <a name="cluster-alerts"></a>Alerty klastra

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Nie można urządzenia za pośrednictwem <*nazwa urządzenia*>. |Urządzenie jest w trybie konserwacji. |Urządzenie pracę awaryjną z powodu wprowadzania lub opuszczeniu trybu konserwacji. Jest to normalne i nie jest potrzebne nie działanie. Po zostały potwierdzone ten alert, należy go wyczyścić na stronie alerty. |
| Nie można urządzenia za pośrednictwem <*nazwa urządzenia*>. |Oprogramowanie układowe urządzenia lub oprogramowania został zaktualizowany. |Wystąpił klastra pracy awaryjnej z powodu aktualizacji. Jest to normalne i nie jest potrzebne nie działanie. Po zostały potwierdzone ten alert, należy go wyczyścić na stronie alerty. |
| Nie można urządzenia za pośrednictwem <*nazwa urządzenia*>. |Kontroler został wyłączony lub ponownie uruchomiony. |Urządzenie nie za pośrednictwem, ponieważ kontroler active został wyłączony lub ponownie uruchomiony przez administratora. Nie jest potrzebne nie działanie. Po zostały potwierdzone ten alert, należy go wyczyścić na stronie alerty. |
| Nie można urządzenia za pośrednictwem <*nazwa urządzenia*>. |Planowany tryb failover. |Sprawdź, czy to planowanego trybu failover. Po wykonaniu odpowiednich działań, usuń zaznaczenie tego alertu na stronie alerty. |
| Nie można urządzenia za pośrednictwem <*nazwa urządzenia*>. |Nieplanowane przełączenie awaryjne. |StorSimple jest tworzone automatycznie odzyskać z nieplanowanej pracy w trybie Failover. Jeśli widzisz dużą liczbę alertów, skontaktuj się z Microsoft Support. |
| Nie można urządzenia za pośrednictwem <*nazwa urządzenia*>. |Inne/Nieznana przyczyna. |Jeśli widzisz dużą liczbę alertów, skontaktuj się z Microsoft Support. Po problem został rozwiązany, usuń zaznaczenie tego alertu na stronie alerty. |
| Stan raporty o usługach urządzeń krytycznych jako zakończony niepowodzeniem. |Awaria usługi ścieżki danych. |Aby uzyskać pomoc, skontaktuj się z Microsoft Support. |
| Wirtualny adres IP interfejsu sieciowego <*danych #*> zgłasza stan jako zakończony niepowodzeniem. |Inne/Nieznana przyczyna. |Czasami tymczasowych warunków może spowodować te alerty. Jeśli tak jest, następnie ten alert zostanie automatycznie usunięty po pewnym czasie. Jeśli problem będzie się powtarzać, skontaktuj się z działem pomocy technicznej firmy Microsoft. |
| Wirtualny adres IP interfejsu sieciowego <*danych #*> zgłasza stan jako zakończony niepowodzeniem. |Nazwa interfejsu: <*danych #*> adresu IP <IP address> nie może być przeniesiony do trybu online, ponieważ wykryto zduplikowany adres IP w sieci. |Upewnij się, że zduplikowany adres IP został usunięty z sieci lub ponownego skonfigurowania interfejsu za pomocą innego adresu IP. |

### <a name="disaster-recovery-alerts"></a>Alerty odzyskiwania po awarii

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Operacje odzyskiwania nie może przywrócić wszystkie ustawienia dla tej usługi. Dane konfiguracji urządzenia jest niespójna dla niektórych urządzeń. |Niezgodność danych po awarii. |Zaszyfrowane dane w usłudze nie jest zsynchronizowany z tym na urządzeniu. Zezwolić urządzeniu <*nazwa urządzenia*> w Menedżerze urządzeń StorSimple można uruchomić procesu synchronizacji. Użyj interfejsu programu Windows PowerShell dla urządzenia StorSimple, aby uruchomić `Restore-HcsmEncryptedServiceData` na urządzeniu <*nazwa urządzenia*> polecenia cmdlet, podając starego hasła jako dane wejściowe tego polecenia cmdlet, aby przywrócić profil zabezpieczeń. Następnie uruchom `Invoke-HcsmServiceDataEncryptionKeyChange` polecenia cmdlet można zaktualizować klucza szyfrowania danych usługi. Po wykonaniu odpowiednich działań, usuń zaznaczenie tego alertu na stronie alerty. |

### <a name="hardware-alerts"></a>Alerty sprzętu

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Składnik sprzętowy <*identyfikator składnika*> zgłasza stan jako <*stan*>. | |Czasami tymczasowych warunków może spowodować te alerty. Jeśli tak, ten alert zostanie automatycznie usunięty po pewnym czasie. Jeśli problem będzie się powtarzać, skontaktuj się z działem pomocy technicznej firmy Microsoft. |
| Kontroler pasywnym działa poprawnie. |Pasywne kontrolera (informacje pomocnicze) nie działa. |Urządzenie działa, ale jeden z kontrolerów działa poprawnie. Spróbuj ponownie uruchomić tego kontrolera. Jeśli problem nie zniknie, skontaktuj się z Microsoft Support. |

### <a name="job-failure-alerts"></a>Alerty błędów zadania

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Tworzenie kopii zapasowych <*identyfikator grupy woluminu źródłowego*> nie powiodło się. |Zadanie tworzenia kopii zapasowej nie powiodło się. |Problemy z łącznością może uniemożliwiać pomyślne wykonanie operacji tworzenia kopii zapasowej. Jeśli nie ma problemów z połączeniem, może osiągnięto maksymalną liczbę kopii zapasowych. Usuń wszystkie kopie zapasowe, które nie są już potrzebne, a następnie spróbuj ponownie wykonać operację. Po wykonaniu odpowiednich działań, usuń zaznaczenie tego alertu na stronie alerty. |
| Klon <*źródła identyfikatory elementów kopii zapasowych*> do <*numery seryjne wolumin docelowy*> nie powiodło się. |Zadanie klonowania nie powiodło się. |Odśwież listę kopii zapasowych, aby sprawdzić, czy kopia zapasowa jest nadal ważny. Jeśli kopia zapasowa jest prawidłowa, istnieje możliwość, że problemy z łącznością chmury uniemożliwiają pomyślne wykonanie operacji klonowania. Jeśli nie ma problemów z połączeniem, możliwe, że osiągnięto limit magazynu. Usuń wszystkie kopie zapasowe, które nie są już potrzebne, a następnie spróbuj ponownie wykonać operację. Po wykonaniu odpowiednią akcję, aby rozwiązać ten problem, usuń zaznaczenie tego alertu na stronie alerty. |
| Przywracanie <*źródła identyfikatory elementów kopii zapasowych*> nie powiodło się. |Przywróć zadanie nie powiodło się. |Odśwież listę kopii zapasowych, aby sprawdzić, czy kopia zapasowa jest nadal ważny. Jeśli kopia zapasowa jest prawidłowa, istnieje możliwość, że problemy z łącznością chmury uniemożliwiają pomyślne wykonanie operacji przywracania. Jeśli nie ma problemów z połączeniem, możliwe, że osiągnięto limit magazynu. Usuń wszystkie kopie zapasowe, które nie są już potrzebne, a następnie spróbuj ponownie wykonać operację. Po wykonaniu odpowiednią akcję, aby rozwiązać ten problem, usuń zaznaczenie tego alertu na stronie alerty. |

### <a name="locally-pinned-volume-alerts"></a>Alerty woluminu przypiętego lokalnie

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Tworzenie woluminu lokalnego <*nazwa woluminu*> nie powiodło się. |Zadanie tworzenia woluminu nie powiodła się. <*Komunikat o błędzie odpowiadający danemu kodowi błędu nie powiodło się*>. |Problemy z łącznością może uniemożliwiać pomyślne wykonanie operacji tworzenia miejsca. Woluminów przypiętych lokalnie jest alokowany nieelastycznie i proces tworzenia miejsca obejmuje przenoszenie woluminy warstwowe do chmury. Jeśli nie ma problemów z połączeniem, może wykorzystał lokalne miejsce na urządzeniu. Określ, czy miejsce istnieje na urządzeniu, przed ponowną próbą wykonania tej operacji. |
| Rozszerzanie woluminu lokalnego <*nazwa woluminu*> nie powiodło się. |Zadanie modyfikacji woluminu nie powiodła się z powodu <*odpowiadający danemu kodowi błędu nie powiodło się komunikat o błędzie*>. |Problemy z łącznością może uniemożliwiać pomyślne wykonanie operacji rozszerzania woluminów. Woluminów przypiętych lokalnie jest alokowany nieelastycznie i proces rozszerzania istniejącej przestrzeni polega na przenoszenie woluminy warstwowe do chmury. Jeśli nie ma problemów z połączeniem, może wykorzystał lokalne miejsce na urządzeniu. Określ, czy miejsce istnieje na urządzeniu, przed ponowną próbą wykonania tej operacji. |
| Konwersja woluminu <*nazwa woluminu*> nie powiodło się. |Zadanie konwersji wolumin do przekonwertowania typu woluminu z lokalnie przypięty do warstwy nie powiodło się. |Konwersja woluminu typu lokalnie przypięty do warstwy nie można ukończyć. Upewnij się, że nie ma problemów z połączeniem uniemożliwiające pomyślne zakończenie operacji. Do rozwiązywania problemów z łącznością problemy, przejdź do [Rozwiązywanie problemów za pomocą polecenia cmdlet Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Oryginalnego woluminu przypiętego lokalnie teraz została oznaczona jako wolumin warstwowy, ponieważ niektóre dane z woluminu przypiętego lokalnie ma rozrzucone w chmurze podczas konwersji. Wynikowe wolumin warstwowy nadal zajmuje lokalne miejsce na urządzeniu nie można odzyskać przyszłych woluminów lokalnych.<br>Rozwiąż wszelkie problemy dotyczące łączności, wyczyść alert i przekonwertować ten wolumin do oryginalnego typu woluminu przypiętego lokalnie, aby upewnić się, wszystkie wprowadzone dane są dostępne lokalnie ponownie. |
| Konwersja woluminu <*nazwa woluminu*> nie powiodło się. |Zadanie konwersji wolumin do przekonwertowania na typ woluminu z warstwy do przypięty lokalnie nie powiodło się. |Konwersja typu woluminu do warstwy do przypięty lokalnie nie można ukończyć. Upewnij się, że nie ma problemów z połączeniem uniemożliwiające pomyślne zakończenie operacji. Do rozwiązywania problemów z łącznością problemy, przejdź do [Rozwiązywanie problemów za pomocą polecenia cmdlet Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Oryginalnego woluminu warstwowego teraz oznaczony jako woluminu przypiętego lokalnie jako część procesu konwersji w dalszym ciągu ma danych znajdujących się w chmurze, gdy nie będzie można odzyskać alokowany elastycznie miejsca na urządzeniu dla tego woluminu dla przyszłych woluminów lokalnych.<br>Rozwiąż wszelkie problemy dotyczące łączności, wyczyść alert i przekonwertować ten wolumin do oryginalnego typu woluminu warstwowego na upewnij się, że będzie można odzyskać lokalne miejsce alokowany nieelastycznie na urządzeniu. |
| Niedługo lokalne miejsce zużycie inaczej lokalnymi migawkami <*Nazwa grupy woluminu*> |Migawki lokalne zasady tworzenia kopii zapasowej wkrótce się skończy miejsca i unieważnione, aby uniknąć błędów zapisu hosta. |Częste migawki lokalne obok danych z dużą ilość danych churn w woluminach skojarzonych z tą grupą zasad tworzenia kopii zapasowej powoduje lokalne miejsce na urządzeniu, aby szybko zużycia. Usuń wszystkie migawki lokalne, które nie są już potrzebne. Ponadto aktualizacji planów migawka lokalna dla tych zasad tworzenia kopii zapasowej dłuższe interwały migawek lokalnych i zapewnić regularnie podjęcie migawki w chmurze. Jeśli nie są wykonywane następujące akcje, lokalne miejsce dla migawek wkrótce może wyczerpane, a system automatycznie usunie je, aby upewnić się, że zapisy hosta w dalszym ciągu pomyślnie przetworzone. |
| Migawki lokalne dla <*Nazwa grupy woluminu*> zostały unieważnione. |Migawki lokalne dla <*Nazwa grupy woluminu*> unieważniona, a następnie usunąć, ponieważ zostały one przekraczającej lokalne miejsce na urządzeniu. |Aby zapewnić, że to nie powtarzanie w przyszłości, przejrzyj harmonogramy migawka lokalna dla tych zasad tworzenia kopii zapasowej i usuń wszelkie migawki lokalne, które nie są już potrzebne. Częste migawki lokalne obok danych z dużą ilość danych churn w woluminach skojarzonych z tą grupą zasad tworzenia kopii zapasowej może spowodować lokalne miejsce na urządzeniu, aby szybko zużycia. |
| Przywracanie <*źródła identyfikatory elementów kopii zapasowych*> nie powiodło się. |Zadanie przywracania nie powiodło się. |Jeśli przypięty lokalnie lub mieszane woluminów warstwowych i przypiętych lokalnie w tych zasad tworzenia kopii zapasowej, Odśwież listę kopii zapasowej do sprawdzenia, czy kopia zapasowa jest nadal ważny. Jeśli kopia zapasowa jest prawidłowa, istnieje możliwość, że problemy z łącznością chmury uniemożliwiają pomyślne wykonanie operacji przywracania. Woluminów przypiętych lokalnie, które zostały przywracana w ramach tej grupy migawki nie mają wszystkie swoje dane pobrane z urządzeniem, a jeśli masz kombinację woluminów warstwowych i przypiętych lokalnie w tej grupie migawki, nie będzie on zsynchronizowany ze sobą. Do pomyślnego ukończenia operacji przywracania, wykonać woluminów w tej grupie w tryb offline na hoście i spróbuj ponownie wykonać operację przywracania. Należy pamiętać, że wszelkie modyfikacje danych woluminu, które zostały wykonane podczas procesu przywracania zostaną utracone. |

### <a name="networking-alerts"></a>Alerty sieci

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Nie można uruchomić usług StorSimple. |Błąd ścieżki danych |Jeśli problem będzie nadal występować, skontaktuj się z pomocą techniczną firmy Microsoft. |
| "Data0" Wykryto zduplikowany adres IP. | |System wykrył konflikt adresu IP "10.0.0.1". Na urządzeniu "Data0" zasobu sieciowego  *<device1>*  jest w trybie offline. Upewnij się, że ten adres IP nie jest używany przez inne jednostki w tej sieci. Aby rozwiązać problemy z siecią, przejdź do [Rozwiązywanie problemów za pomocą polecenia cmdlet Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Aby uzyskać pomoc w rozwiązaniu tego problemu, skontaktuj się z administratorem sieci. Jeśli problem będzie nadal występować, skontaktuj się z pomocą techniczną firmy Microsoft. |
| Adres IPv4 (lub IPv6) dla "Data0" jest w trybie offline. | |Zasobu sieciowego "Data0" o adresie IP "10.0.0.1". i prefiks długości "22" na urządzeniu  *<device1>*  jest w trybie offline. Upewnij się, czy działają porty przełącznika, z którymi jest połączony ten interfejs. Aby rozwiązać problemy z siecią, przejdź do [Rozwiązywanie problemów za pomocą polecenia cmdlet Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
| Nie można nawiązać połączenia z usługą uwierzytelniania. |Błąd ścieżki danych |URLthat jest używany do uwierzytelniania nie jest dostępny. Upewnij się, że reguły zapory obejmuje wzorce adres URL określony dla urządzenia StorSimple. Aby uzyskać więcej informacji dotyczących wzorców adresów URL w portalu Azure przejdź do https://aka.ms/ss-8000-network-reqs. Jeśli przy użyciu chmury Azure dla instytucji rządowych, przejdź do wzorców adresów URL w https://aka.ms/ss8000-gov-network-reqs.|

### <a name="performance-alerts"></a>Alerty wydajności

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Obciążenia urządzenia przekroczyła <*próg*>. |Wolniej niż oczekiwany czas odpowiedzi. |Urządzenie raportuje użycie bardzo duże obciążenie wejścia/wyjścia. Może to spowodować, że urządzenie może nie działać również powinien. Przejrzyj obciążeń podłączone do urządzenia i ustalić, czy istnieją jakieś, który można przenieść do innego urządzenia lub które nie są już wymagane.| Nie można uruchomić usług StorSimple. |Błąd ścieżki danych |Jeśli problem będzie nadal występować, skontaktuj się z pomocą techniczną firmy Microsoft. |i stan bieżący, przejdź do [monitorować urządzenia przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-8000-monitor-device.md) |

### <a name="security-alerts"></a>Alerty zabezpieczeń

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Microsoft Support sesji zostało uruchomione. |Innych firm dostęp do sesji pomocy technicznej. |Potwierdź, że taki dostęp jest autoryzowany. Po wykonaniu odpowiednich działań, usuń zaznaczenie tego alertu na stronie alerty. |
| Hasło dla <*elementu*> wygaśnie za <*czas*>. |Zbliża się do wygaśnięcia hasła. |Zmień hasło przed jego wygaśnięciem. |
| Brak informacji o konfiguracji zabezpieczeń <*identyfikator elementu*>. | |Woluminy skojarzone z tym kontenerem woluminu nie może służyć do replikowania konfiguracji StorSimple. Aby upewnić się, że dane są bezpiecznie przechowywane, zaleca się usunięcie kontenera woluminów zaś wszelkie woluminy skojarzone z kontenera woluminów. Po wykonaniu odpowiednich działań, usuń zaznaczenie tego alertu na stronie alerty. |
| <*numer*> prób logowania nie powiodła się dla <*identyfikator elementu*>. |Wiele nieudane próby logowania. |Urządzenie może być celem ataku lub autoryzowany użytkownik próbuje nawiązać połączenie przy użyciu niepoprawnego hasła.<ul><li>Skontaktuj się z autoryzowanych użytkowników i sprawdź, czy te próby zostały z wiarygodnego źródła. Jeśli będziesz kontynuować wyświetlić dużą liczbę nieudanych prób logowania, należy wziąć pod uwagę wyłączanie zdalnego zarządzania i skontaktować się z administratorem sieci. Po wykonaniu odpowiednich działań, usuń zaznaczenie tego alertu na stronie alerty.</li><li>Sprawdź, czy swoich wystąpień Snapshot Manager są skonfigurowane poprawne hasło. Po wykonaniu odpowiednich działań, usuń zaznaczenie tego alertu na stronie alerty.</li></ul>Aby uzyskać więcej informacji, przejdź do [zmienić hasło urządzenia wygasłe](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password). |
| Wystąpił jeden lub więcej błędów podczas zmiany klucza szyfrowania danych usługi. | |Wystąpiły błędy podczas zmieniania klucza szyfrowania danych usługi. Po usunąć warunków błędów, uruchom `Invoke-HcsmServiceDataEncryptionKeyChange` polecenia cmdlet interfejsu programu Windows PowerShell dla urządzenia StorSimple na urządzeniu, aby zaktualizować usługę. Jeśli ten problem będzie nadal występować, skontaktuj się z pomocą techniczną firmy Microsoft. Po rozwiązaniu problemu, usuń zaznaczenie tego alertu na stronie alerty. |

### <a name="support-package-alerts"></a>Obsługa alertów pakietu

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Nie można utworzyć pakietu obsługi. |StorSimple nie można wygenerować pakietu. |Ponów tę operację. Jeśli problem będzie się powtarzać, skontaktuj się z działem pomocy technicznej firmy Microsoft. Po rozwiązaniu problemu, usuń zaznaczenie tego alertu na stronie alerty. |

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [StorSimple błędy i rozwiązywanie problemów dotyczących wdrożenia urządzenia](storsimple-8000-troubleshoot-deployment.md).

