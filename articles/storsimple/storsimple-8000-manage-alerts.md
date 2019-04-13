---
title: Wyświetlanie alertów i zarządzanie nimi dla urządzenia StorSimple 8000 series | Dokumentacja firmy Microsoft
description: Opisuje warunki alertu StorSimple i ważność, jak skonfigurować powiadomienia o alertach i jak alertami można zarządzać za pomocą usługi Menedżer urządzeń StorSimple.
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
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: c3be0cdf2ef33c26dfa9d177e9b34f808b1b862a
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59525534"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-storsimple-alerts"></a>Użyj usługi Menedżer urządzeń StorSimple, aby wyświetlić alerty i zarządzaj nimi StorSimple

## <a name="overview"></a>Omówienie

**Alerty** bloku usługi Menedżer urządzeń StorSimple umożliwia przeglądanie i wyczyść alerty związane z urządzeniem StorSimple na zasadzie w czasie rzeczywistym. W tym bloku mogą centralnie monitorować problemy z kondycją urządzenia StorSimple i ogólnego rozwiązania Microsoft Azure StorSimple.

W tym samouczku opisano typowe warunki alertu, poziomy ważności alertów i Konfigurowanie powiadomień o alertach. Ponadto zawiera krótki przewodnik po alertu tabelami, które umożliwiają szybko zlokalizować określony alert i odpowiednio reagować.

![Strona alertów](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="common-alert-conditions"></a>Typowe warunki alertu

Urządzenie StorSimple generuje alerty w odpowiedzi na szereg warunków. Poniżej przedstawiono najbardziej typowych warunków alertów:

* **Problemów ze sprzętem** — te alerty informujące o kondycji sprzętu. Umożliwiają one o tym, czy aktualizacje oprogramowania są wymagane, jeśli interfejs sieciowy ma problemy, czy występuje problem z jednym z dysków z danymi.
* **Problemy z łącznością** — te alerty występują, gdy istnieje trudności podczas transferu danych. Problemy z komunikacją może wystąpić podczas transferu danych do i z kontem usługi Azure storage lub z powodu braku łączności między urządzeniami i usługą Menedżera urządzeń StorSimple. Problemy z komunikacją są niektóre z najtrudniejszych rozwiązać problem, ponieważ istnieje tak wiele punktów awarii. Należy zawsze najpierw sprawdzić połączenie sieciowe oraz dostęp do Internetu są dostępne przed przejściem do bardziej zaawansowanego rozwiązywania problemów. Aby uzyskać pomoc w rozwiązywaniu problemów, przejdź do [Rozwiązywanie problemów za pomocą polecenia cmdlet Test-Connection](storsimple-8000-troubleshoot-deployment.md).
* **Problemy z wydajnością** — te alerty są powodowane, gdy system nie działa optymalnie, takich jak znajduje się pod dużym obciążeniem.

Ponadto możesz zobaczyć alerty dotyczące zabezpieczeń, aktualizacji lub niepowodzeń zadań.

## <a name="alert-severity-levels"></a>Poziomy ważności alertów

Alerty mają różne poziomy ważności, w zależności od alertu sytuacji będzie miał wpływ i potrzebę odpowiedzi na alert. Dostępne są następujące poziomy ważności:

* **Krytyczne** — ten alert jest w odpowiedzi na warunek, który ma wpływ na pomyślne wydajność systemu. Czynności upewnij się, że usługi StorSimple, usługi nie zostało przerwane.
* **Ostrzeżenie** — ten stan może stać się krytyczny, jeśli nie uda się rozwiązać. Należy zbadać sytuację i podejmować żadnych działań, wymagane, aby wyczyścić ten problem.
* **Informacje o** — ten alert zawiera informacje, które mogą być przydatne do śledzenia i zarządzania systemem.

## <a name="configure-alert-settings"></a>Konfigurowanie ustawień alertów

Można wybrać, czy chcesz otrzymywać powiadomienia pocztą e-mail alertu warunki dla każdego z urządzeń StorSimple. Ponadto można zidentyfikować innych adresatów powiadomień o alertach, wprowadzając ich adresy e-mail w **innych adresatów wiadomości e-mail** okno, oddzielając je średnikami.

> [!NOTE]
> Możesz wprowadzić maksymalnie 20 adresów e-mail na urządzeniu.

Po włączeniu powiadomień e-mail dla urządzeń, elementy członkowskie do listy powiadomień otrzymają wiadomość e-mail z każdym alert krytyczny o wystąpieniu. Wysyłane z *storsimple — alerty noreply\@mail.windowsazure.com* i opisano warunek alertu. Adresaci mogą kliknąć **Unsubscribe** aby został usunięty z listy powiadomień e-mail.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Aby włączyć powiadomienia e-mail o alertach dla urządzenia
1. Przejdź do usługi Menedżer urządzeń StorSimple. Z listy urządzeń wybierz i kliknij urządzenie, które chcesz skonfigurować.
2. Przejdź do **ustawienia** > **ogólne** dla tego urządzenia.

   ![Blok kafelka alerty](./media/storsimple-8000-manage-alerts/configure-alerts-email2.png)
   
2. W **ustawienia ogólne** przejdź do bloku **ustawienia alertu** i ustaw następujące opcje:
   
   1. W **Wyślij powiadomienie e-mail** pól, zaznacz **tak**.
   2. W **E-mail administratorów usługi** pól, zaznacz **tak** do administratora usługi i wszyscy współadministratorzy otrzymywać powiadomienia o alertach.
   3. W **innych adresatów wiadomości e-mail** wprowadź adresy e-mail wszystkich pozostałych adresatów, którzy mają otrzymywać powiadomienia o alertach. Wprowadź nazwy w formacie *ktoś\@somewhere.com*. Użyj średników do oddzielenia adresów e-mail. Można skonfigurować maksymalnie 20 adresów e-mail na urządzeniu. 
      
3. Aby Wyślij testowe powiadomienie e-mail, kliknij przycisk **Wyślij testową wiadomość e-mail**. Usługa Menedżer urządzeń StorSimple spowoduje wyświetlenie komunikatów o stanie, jak przekazuje testowe powiadomienie.

    ![Ustawienia alertu](./media/storsimple-8000-manage-alerts/configure-alerts-email3.png)

4. Zostanie wyświetlone powiadomienie o wysłaniu testową wiadomość e-mail. 
   
    ![Alerty testu wysłana wiadomość e-mail z powiadomieniem](./media/storsimple-8000-manage-alerts/configure-alerts-email4.png)
   
   > [!NOTE]
   > Jeśli nie można wysłać komunikatu z powiadomieniem testowym, usługi Menedżer urządzeń StorSimple, wyświetli odpowiedni komunikat o błędzie. Poczekaj kilka minut, a następnie spróbuj ponownie wysłać komunikatu z powiadomieniem testowym. 

5. Po zakończeniu konfiguracji kliknij **Zapisz**. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**.

     ![Alerty testu wysłana wiadomość e-mail z powiadomieniem](./media/storsimple-8000-manage-alerts/configure-alerts-email5.png)

## <a name="view-and-track-alerts"></a>Wyświetlanie i śledzenie alertów

Bloku podsumowania usługi Menedżer urządzeń StorSimple pozwala szybko dowiedzieć się liczba alertów na urządzeniach, uporządkowane według ważności.

![Pulpit nawigacyjny alertów](./media/storsimple-8000-manage-alerts/device-summary4.png)

Poziom ważności kliknięcie powoduje otwarcie **alerty** bloku. Wyniki obejmują tylko alerty, które odpowiadają tym poziom ważności.

Kliknięcie alertu na liście zapewnia dodatkowe szczegóły alertu, czas ostatniego, który został zgłoszony alert, w tym liczbę wystąpień alertu na urządzeniu i zalecaną akcję, aby rozwiązać alert. Jeśli jest to alert sprzętu, jednak również zidentyfikować składnik sprzętowy.

![Przykład alertu sprzętu](./media/storsimple-8000-manage-alerts/configure-alerts-email14.png)

Szczegóły alertu można skopiować do pliku tekstowego, jeśli chcesz wysłać informacje do firmy Microsoft Support. Po i zalecenia i usunięciu warunek alertu lokalnej należy wyczyścić alert z poziomu urządzenia, wybierając alertu w **alerty** bloku i klikając **wyczyść**. Aby wyczyścić wiele alertów, wybierz poszczególne alerty, kliknij dowolną kolumnę z wyjątkiem **Alert** kolumny, a następnie kliknij **wyczyść** po wybraniu wszystkich alertów do wyczyszczenia. Należy pamiętać, że niektóre alerty są automatycznie czyszczone, gdy problem zostanie rozwiązany, lub gdy system alert zostaje zaktualizowana o nowe informacje.

Po kliknięciu **wyczyść**, będziesz mieć możliwość zapewnienia komentarze na temat alertu oraz czynności, które zostały podjęte w celu rozwiązania problemu. Niektóre zdarzenia zostaną wyczyszczone przez system, jeśli inne zdarzenie zostanie wyzwolony o nowe informacje. W takim przypadku zostanie wyświetlony następujący komunikat.

![Komunikat alertu wyczyść](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Sortowanie i przegląd alertów

Może okazać się bardziej wydajne, aby uruchamiać raporty na temat alertów, tak, aby można przejrzeć, a następnie je wyczyść w grupach. Ponadto **alerty** bloku można wyświetlić alerty do 250. Po przekroczeniu tej liczby alertów, nie wszystkie alerty będą wyświetlane w widoku domyślnego. Można połączyć następujące pola, aby dostosować alerty są wyświetlane:

* **Stan** — można wyświetlić **Active** lub **wyczyszczone** alertów. Aktywne alerty nadal są wyzwalane w systemie, natomiast wyczyszczone alerty zostały albo ręcznie wyczyścić przez administratora lub programowo wyczyszczone, ponieważ system zaktualizowany stan alertu o nowe informacje.
* **Ważność** — można wyświetlać alerty, wszystkie poziomy ważności (informacje o krytycznych,) lub tylko niektórych ważność, takich jak alerty tylko krytyczne.
* **Źródło** — Wyświetl alerty od wszystkich źródeł lub ograniczyć alerty do tych, które pochodzą z usługi lub jeden lub wszystkie urządzenia.
* **Zakres czasu** — określając **z** i **do** dat i sygnatury czasowe, można wyświetlać alerty przedziale czasu, który Cię interesuje.

![Lista alertów](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="alerts-quick-reference"></a>Krótki przewodnik dotyczący alertów

W poniższej tabeli wymieniono niektóre alerty Microsoft Azure StorSimple, które można napotkać, a także dodatkowe informacje i zalecenia dotyczące gdzie są dostępne. Alerty urządzeń StorSimple można podzielić na następujące kategorie:

* [Alerty łączności chmury](#cloud-connectivity-alerts)
* [Alerty klastra](#cluster-alerts)
* [Alerty odzyskiwania po awarii](#disaster-recovery-alerts)
* [Alerty sprzętu](#hardware-alerts)
* [Alerty dotyczące błędów zadań](#job-failure-alerts)
* [Alerty woluminu przypiętego lokalnie](#locally-pinned-volume-alerts)
* [Alerty sieci](#networking-alerts)
* [Alerty wydajności](#performance-alerts)
* [Alerty zabezpieczeń](#security-alerts)
* [Obsługa alertów pakietu](#support-package-alerts)

### <a name="cloud-connectivity-alerts"></a>Alerty łączności chmury

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Łączność z <*nazwa poświadczeń w chmurze*> nie może zostać ustanowione. |Nie można nawiązać połączenia z kontem usługi storage. |Wygląda na to, może to być problem z łącznością z urządzeniem. Uruchom `Test-HcsmConnection` polecenia cmdlet z interfejsu programu Windows PowerShell dla usługi StorSimple na urządzeniu, aby zidentyfikować i rozwiązać problem. Jeśli te ustawienia są prawidłowe, problem może być przy użyciu poświadczeń konta magazynu, dla którego został zgłoszony alert. W takim przypadku należy użyć `Test-HcsStorageAccountCredential` polecenia cmdlet, aby sprawdzić, czy są problemy, które można rozwiązać.<ul><li>Sprawdź ustawienia sieci.</li><li>Sprawdź swoje poświadczenia konta magazynu.</li></ul> |
| Nie Otrzymaliśmy pulsu z urządzenia przez ostatnie <*numer*> minut. |Nie można nawiązać połączenia z urządzeniem. |Prawdopodobnie występuje problem z łącznością z urządzeniem. Użyj `Test-HcsmConnection` polecenia cmdlet z interfejsu programu Windows PowerShell dla usługi StorSimple na urządzeniu, aby zidentyfikować i rozwiązać ten problem lub skontaktuj się z administratorem sieci. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>Zachowanie usługi StorSimple, gdy łączność z chmurą nie powiodło się.

Co się stanie, jeśli łączność z chmurą nie powiedzie się dla urządzenia StorSimple działających w środowisku produkcyjnym?

Jeśli łączność z chmurą nie powiedzie się na urządzeniu StorSimple w środowisku produkcyjnym, następnie w zależności od stanu urządzenia, że mogą wystąpić:

* **Dla danych lokalnych na urządzeniu z systemem**: Przez pewien czas nie będzie żadnych zakłóceń i odczytuje będą w dalszym ciągu być obsługiwane. Liczba oczekujących operacji We-Wy zwiększa się i przekracza limit, operacji odczytu może kończą się niepowodzeniem.

    W zależności od ilości danych na swoim urządzeniu zapisy również będą nadal występują dla pierwszego kilka godzin po zakłócenie w łączność z chmurą. Zapisy zostaną następnie spowolnić i ostatecznie kończą się niepowodzeniem, jeśli łączność z chmurą jest zakłócane przez kilka godzin. (Brak magazynu tymczasowego na urządzeniu na potrzeby danych, który ma zostać wypchnięty na chmurze. Ten obszar jest opróżniany, gdy dane są wysyłane. Jeśli połączenie nie powiedzie się, nie będą wypychane dane w tej dziedzinie magazynu w chmurze, a nie powiedzie się operacji We/Wy.)
* **W przypadku danych w chmurze**: Większość błędów łączności chmury zwracany jest błąd. Po przywróceniu łączności, użytkownik nie woluminu w trybie online do wznowienia systemu IOs. W rzadkich przypadkach użytkownik może być wymagane do przywrócenia wolumin w tryb online w witrynie Azure portal.
* **Dla migawki w chmurze w toku**: Próba powtórzenia operacji kilka razy w ciągu 4-5 godzin, a jeśli połączenie nie zostanie przywrócona, migawki w chmurze zakończy się niepowodzeniem.

### <a name="cluster-alerts"></a>Alerty klastra

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Urządzenie przełączone w tryb failover <*nazwy urządzenia*>. |Urządzenie jest w trybie konserwacji. |Urządzenie w trybie failover z powodu wprowadzanych konserwacji lub wychodzenia z trybu konserwacji. Jest to normalne i nie wymaga żadnej akcji. Po potwierdzeniu tego alertu wyczyść go na stronie alertów. |
| Urządzenie przełączone w tryb failover <*nazwy urządzenia*>. |Oprogramowanie układowe urządzenia lub oprogramowania właśnie został zaktualizowany. |Wystąpił z powodu aktualizacji klastra pracy awaryjnej. Jest to normalne i nie wymaga żadnej akcji. Po potwierdzeniu tego alertu wyczyść go na stronie alertów. |
| Urządzenie przełączone w tryb failover <*nazwy urządzenia*>. |Kontroler został zamknięty lub ponownie uruchomiony. |Urządzenie zostało przełączone za pośrednictwem ponieważ aktywny kontroler został zamknięty lub ponownie uruchomiony przez administratora. Żadna akcja nie jest wymagana. Po potwierdzeniu tego alertu wyczyść go na stronie alertów. |
| Urządzenie przełączone w tryb failover <*nazwy urządzenia*>. |Planowany tryb failover. |Sprawdź, czy ta planowany tryb failover. Po podjęciu odpowiedniej akcji wyczyść ten alert na stronie alertów. |
| Urządzenie przełączone w tryb failover <*nazwy urządzenia*>. |Nieplanowany tryb failover. |Usługa StorSimple jest skompilowana do automatycznego odzyskiwania po nieplanowanej pracy w trybie Failover. Jeśli widzisz dużą liczbę tych alertów, skontaktuj się z Microsoft Support. |
| Urządzenie przełączone w tryb failover <*nazwy urządzenia*>. |Inne/Nieznana przyczyna. |Jeśli widzisz dużą liczbę tych alertów, skontaktuj się z Microsoft Support. Po usunięciu problemu wyczyść ten alert na stronie alertów. |
| Usługi krytyczne urządzenie raportuje stan jako zakończony niepowodzeniem. |Awaria usługi ścieżki danych. |Aby uzyskać pomoc, skontaktuj się z Microsoft Support. |
| Wirtualny adres IP dla interfejsu sieciowego <*danych #*> zgłasza stan jako zakończony niepowodzeniem. |Inne/Nieznana przyczyna. |Czasami tymczasowe warunki mogą być przyczyną tych alertów. Jeśli jest to możliwe, następnie ten alert zostanie automatycznie wyczyszczony po pewnym czasie. Jeśli problem będzie się powtarzać, skontaktuj się z działem pomocy technicznej firmy Microsoft. |
| Wirtualny adres IP dla interfejsu sieciowego <*danych #*> zgłasza stan jako zakończony niepowodzeniem. |Nazwa interfejsu: <*danych #*> adres IP `<IP address>` nie można przełączyć online, ponieważ wykryto zduplikowany adres IP w sieci. |Upewnij się, że zduplikowany adres IP został usunięty z sieci, lub ponownie skonfigurować interfejs przy użyciu innego adresu IP. |

### <a name="disaster-recovery-alerts"></a>Alerty odzyskiwania po awarii

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Operacje odzyskiwania nie można przywrócić wszystkich ustawień dla tej usługi. Dane konfiguracji urządzenia jest w niespójnym stanie dla niektórych urządzeń. |Niespójność danych wykryte po zakończeniu odzyskiwania po awarii. |Zaszyfrowane dane w usłudze nie jest zsynchronizowany z, na urządzeniu. Autoryzuj urządzenie <*nazwy urządzenia*> w Menedżerze urządzeń StorSimple można uruchomić procesu synchronizacji. Użyj interfejsu programu Windows PowerShell dla usługi StorSimple do uruchomienia `Restore-HcsmEncryptedServiceData` na urządzeniu <*nazwy urządzenia*> polecenia cmdlet, podając stare hasło jako dane wejściowe do tego polecenia cmdlet, aby przywrócić profil zabezpieczeń. Następnie uruchom `Invoke-HcsmServiceDataEncryptionKeyChange` polecenia cmdlet, aby zaktualizować klucz szyfrowania danych usługi. Po podjęciu odpowiedniej akcji wyczyść ten alert na stronie alertów. |

### <a name="hardware-alerts"></a>Alerty sprzętu

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Składnik sprzętowy <*identyfikator składnika*> raportuje stan <*stan*>. | |Czasami tymczasowe warunki mogą być przyczyną tych alertów. Jeśli tak, ten alert zostanie automatycznie wyczyszczony po pewnym czasie. Jeśli problem będzie się powtarzać, skontaktuj się z działem pomocy technicznej firmy Microsoft. |
| Nieprawidłowe działanie kontrolera pasywnego. |Kontrolerze pasywnym (informacje pomocnicze) nie działa. |Urządzenie działa, ale jeden z kontrolerów działa nieprawidłowo. Spróbuj ponownie uruchomić tego kontrolera. Jeśli problem nie zostanie rozwiązany, skontaktuj się z Microsoft Support. |

### <a name="job-failure-alerts"></a>Alerty dotyczące błędów zadań

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Tworzenie kopii zapasowych <*identyfikator grupy woluminu źródłowego*> nie powiodło się. |Zadanie tworzenia kopii zapasowej nie powiodło się. |Problemy z łącznością mogą uniemożliwiać pomyślne ukończenie operacji wykonywania kopii zapasowej. W przypadku żadnych problemów z łącznością mogą osiągnięto maksymalną liczbę kopii zapasowych. Usuń wszelkie kopie zapasowe, które nie są już potrzebne i spróbuj ponownie wykonać operację. Po podjęciu odpowiedniej akcji wyczyść ten alert na stronie alertów. |
| Klon <*źródło elementu kopii zapasowej identyfikatory*> do <*numery seryjne wolumin docelowy*> nie powiodło się. |Zadanie klonowania nie powiodło się. |Odśwież listę kopii zapasowych, aby sprawdzić, czy kopia zapasowa jest nadal ważny. Jeśli kopia zapasowa jest prawidłowa, istnieje możliwość, że problemy z łącznością chmury uniemożliwiają uniemożliwiać pomyślne ukończenie operacji klonowania. W przypadku żadnych problemów z łącznością może osiągnięto limit magazynowania. Usuń wszelkie kopie zapasowe, które nie są już potrzebne i spróbuj ponownie wykonać operację. Po podjęciu odpowiedniej akcji w celu rozwiązania problemu wyczyść ten alert na stronie alertów. |
| Przywracanie <*źródło elementu kopii zapasowej identyfikatory*> nie powiodło się. |Przywróć zadanie nie powiodło się. |Odśwież listę kopii zapasowych, aby sprawdzić, czy kopia zapasowa jest nadal ważny. Jeśli kopia zapasowa jest prawidłowa, istnieje możliwość, że problemy z łącznością chmury uniemożliwiają pomyślne ukończenie operacji przywracania. W przypadku żadnych problemów z łącznością może osiągnięto limit magazynowania. Usuń wszelkie kopie zapasowe, które nie są już potrzebne i spróbuj ponownie wykonać operację. Po podjęciu odpowiedniej akcji w celu rozwiązania problemu wyczyść ten alert na stronie alertów. |

### <a name="locally-pinned-volume-alerts"></a>Alerty woluminu przypiętego lokalnie

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Tworzenie woluminu lokalnego <*nazwa woluminu*> nie powiodło się. |Zadanie tworzenia woluminu nie powiodło się. <*Komunikat o błędzie odpowiadający kod błędu nie powiodło się:*>. |Problemy z łącznością mogą uniemożliwiać uniemożliwiać pomyślne ukończenie operacji tworzenia miejsca. Woluminy przypięte lokalnie jest alokowany nieelastycznie i proces tworzenia miejsca obejmuje rozlanie woluminów warstwowych do chmury. Jeśli nie występują żadne problemy z łącznością, może wyczerpano lokalne miejsce na urządzeniu. Ustal, czy miejsce przed ponowieniem próby wykonania tej operacji istnieje na urządzeniu. |
| Rozszerzenie woluminu lokalnego <*nazwa woluminu*> nie powiodło się. |Zadanie modyfikacji woluminu nie udało się ze względu <*odpowiadający kod błędu nie powiodło się: komunikat o błędzie*>. |Problemy z łącznością mogą uniemożliwiać uniemożliwiać pomyślne ukończenie operacji rozszerzenia woluminu. Woluminy przypięte lokalnie jest alokowany nieelastycznie i proces rozszerzania istniejącego miejsca obejmuje rozlanie woluminów warstwowych do chmury. Jeśli nie występują żadne problemy z łącznością, może wyczerpano lokalne miejsce na urządzeniu. Ustal, czy miejsce przed ponowieniem próby wykonania tej operacji istnieje na urządzeniu. |
| Konwersja woluminu <*nazwa woluminu*> nie powiodło się. |Zadanie konwersji woluminu w celu konwersji typu woluminu z lokalnie przypięte do warstwowy nie powiodło się. |Konwersję typu woluminu z typu lokalnie przypięte do warstwy nie można ukończyć. Upewnij się, że nie istnieją żadne problemy z łącznością operacji pomyślne zakończenie działania. Do rozwiązywania problemów z łącznością problemów, przejdź do [Rozwiązywanie problemów za pomocą polecenia cmdlet Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Oryginalnego woluminu przypiętego lokalnie teraz została oznaczona jako wolumin warstwowy, ponieważ część danych z woluminu przypiętego lokalnie ma rozlane z chmurą podczas konwersji. Wynikowe wolumin warstwowy jest nadal zajmuje lokalne miejsce na urządzeniu, które nie mogą być odzyskiwane do przyszłych woluminy lokalne.<br>Rozwiąż wszelkie problemy z łącznością, wyczyść alert i przekonwertować ten wolumin z powrotem oryginalny typ woluminu przypiętego lokalnie, aby upewnić się, wszystkie wprowadzone dane są dostępne lokalnie ponownie. |
| Konwersja woluminu <*nazwa woluminu*> nie powiodło się. |Zadanie konwersji woluminu w celu konwersji typu woluminu z warstwowego na lokalnie przypięty nie powiodło się. |Konwersja typu woluminu warstwowego na lokalnie przypięty nie mogła zostać zakończona. Upewnij się, że nie istnieją żadne problemy z łącznością operacji pomyślne zakończenie działania. Do rozwiązywania problemów z łącznością problemów, przejdź do [Rozwiązywanie problemów za pomocą polecenia cmdlet Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Jako część procesu konwersji w dalszym ciągu ma danych znajdujących się w chmurze, gdy nie będzie można odzyskać grubo aprowizowane miejsce na urządzeniu dla tego woluminu do przyszłych woluminy lokalne: nazwa oryginalnego woluminu warstwowego teraz jest oznaczony jako woluminu przypiętego lokalnie.<br>Rozwiąż wszelkie problemy z łącznością, wyczyść alert i przekonwertować ten wolumin z powrotem oryginalny typ woluminu warstwowego w celu zapewnienia, że lokalne miejsce aprowizowane na urządzeniu można odzyskać. |
| Bliskie zużycie lokalne miejsce dla lokalnych migawek <*Nazwa grupy woluminu*> |Lokalne migawki dla zasad tworzenia kopii zapasowej wkrótce może zabraknąć miejsca i unieważnione, aby zapobiec błędom zapisu hosta. |Częste lokalne migawki obok dużej liczby zmian w woluminach skojarzonych z tą grupą zasad kopii zapasowych powodują lokalne miejsce na urządzeniu, aby szybko wykorzystać. Usuń wszelkie lokalne migawki, które nie są już potrzebne. Ponadto należy zaktualizować harmonogramy lokalnych migawek dla tych zasad kopii zapasowych były mniej częste lokalne migawki i upewnij się, że migawki w chmurze podjęto regularnie. Jeśli działania te nie zostaną podjęte, lokalne miejsce dla tych migawek może wkrótce się wyczerpać i system automatycznie usunie je, aby upewnić się, że host zapisów w dalszym ciągu pomyślnie przetworzone. |
| Lokalne migawki dla <*Nazwa grupy woluminu*> zostały unieważnione. |Lokalne migawki dla <*Nazwa grupy woluminu*> unieważnione, a następnie usunąć, ponieważ zostały one przekraczające lokalne miejsce na urządzeniu. |Aby upewnić się, że nie powtórzy się w przyszłości, przejrzyj harmonogramy lokalnych migawek dla tych zasad kopii zapasowych i usuń lokalne migawki, które nie są już potrzebne. Częste lokalne migawki obok dużej liczby zmian w woluminach skojarzonych z tą grupą zasad kopii zapasowych może spowodować lokalne miejsce na urządzeniu, aby szybko wykorzystać. |
| Przywracanie <*źródło elementu kopii zapasowej identyfikatory*> nie powiodło się. |Zadanie przywracania nie powiodło się. |Jeśli przypięty lokalnie lub w kombinacji tych zasad kopii zapasowych lokalnie przypięty i podzielonych na warstwy woluminy Odśwież listę kopii zapasowych do sprawdzenia, czy kopia zapasowa jest nadal ważny. Jeśli kopia zapasowa jest prawidłowa, istnieje możliwość, że problemy z łącznością chmury uniemożliwiają pomyślne ukończenie operacji przywracania. Woluminy przypięte lokalnie, które zostały przywracana w ramach tej grupy migawki nie mają wszystkie swoje dane pobrane z urządzeniem, a jeśli masz kombinację woluminy warstwowe i przypiętego lokalnie w tej grupie migawki, nie będzie synchronizowany ze sobą. Do pomyślnego ukończenia operacji przywracania, Przełącz woluminy w tej grupie trybu offline na hoście, a następnie spróbuj ponownie wykonać operację przywracania. Należy pamiętać, że wszelkie modyfikacje danych woluminów, które były wykonywane podczas procesu przywracania zostaną utracone. |

### <a name="networking-alerts"></a>Alerty sieci

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Nie można uruchomić usług StorSimple. |Błąd ścieżki danych |Jeśli problem będzie nadal występować, skontaktuj się z pomocą techniczną firmy Microsoft. |
| Wykryto "Data0" zduplikowanego adresu IP. | |System wykrył konflikt adresu IP "10.0.0.1". Zasób sieciowy "Data0" na urządzeniu  *\<urządzenia 1 >* jest w trybie offline. Upewnij się, że ten adres IP nie jest używany przez żadną inną jednostkę w tej sieci. Aby rozwiązać problemy z siecią, przejdź do [Rozwiązywanie problemów za pomocą polecenia cmdlet Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Aby uzyskać pomoc w rozwiązaniu tego problemu, skontaktuj się z administratorem sieci. Jeśli problem będzie nadal występować, skontaktuj się z pomocą techniczną firmy Microsoft. |
| Adres IPv4 (lub IPv6) dla "Data0" jest w trybie offline. | |Zasób sieciowy "Data0" z adresem IP "10.0.0.1". i długość "22" na urządzeniu prefiksu  *\<urządzenia 1 >* jest w trybie offline. Upewnij się, że porty przełącznika, z którymi jest połączony ten interfejs operacyjnej. Aby rozwiązać problemy z siecią, przejdź do [Rozwiązywanie problemów za pomocą polecenia cmdlet Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
| Nie można nawiązać połączenia z usługą uwierzytelniania. |Błąd ścieżki danych |URLthat jest używany do uwierzytelniania jest nieosiągalny. Upewnij się, że reguły zapory obejmują wzorce adresów URL, określony dla urządzenia StorSimple. Aby uzyskać więcej informacji na temat wzorców adresów URL w witrynie Azure portal, przejdź do protokołu https:\//aka.ms/ss-8000-network-reqs. Jeśli używasz chmury dla instytucji rządowych platformy Azure, przejdź do wzorców adresów URL przy użyciu protokołu https:\//aka.ms/ss8000-gov-network-reqs.|

### <a name="performance-alerts"></a>Alerty wydajności

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje | |
|:--- |:--- |:--- | --- |
| Obciążenie urządzenia przekroczyło <*próg*>. |Wolniejsza niż oczekiwany czas odpowiedzi. |Urządzenie raportuje wykorzystanie przy dużym obciążeniu wejścia/wyjścia. Może to powodować, że urządzenie nie działa tak dobrze, zgodnie z oczekiwaniami. Przejrzyj obciążenia podłączone do urządzenia i ustal, czy istnieją jakieś, które mogą zostać przeniesione do innego urządzenia lub które nie są już potrzebne.|
| Nie można uruchomić usług StorSimple. |Błąd ścieżki danych |Jeśli problem będzie nadal występować, skontaktuj się z pomocą techniczną firmy Microsoft. |

### <a name="security-alerts"></a>Alerty zabezpieczeń

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Rozpoczęto sesję Microsoft Support. |Innych firm, dostęp do sesji pomocy technicznej. |Potwierdź, że ten dostęp jest autoryzowany. Po podjęciu odpowiedniej akcji wyczyść ten alert na stronie alertów. |
| Hasło dla <*elementu*> wygaśnie <*czas*>. |Zbliża się do wygaśnięcia hasła. |Zmień hasło przed jego wygaśnięciem. |
| Brak informacji o konfiguracji zabezpieczeń <*identyfikator elementu*>. | |Woluminów skojarzonych z tym kontenerem woluminów nie może służyć do replikowania konfiguracji StorSimple. Aby upewnić się, że dane są bezpiecznie przechowywane, zaleca się usunięcie kontenera woluminów i wszystkich woluminów skojarzonych z kontenerem woluminów. Po podjęciu odpowiedniej akcji wyczyść ten alert na stronie alertów. |
| <*Liczba*> nieudanych prób logowania dla <*identyfikator elementu*>. |Wiele nieudanych prób logowania. |Urządzenie może być celem ataku lub autoryzowany użytkownik próbuje nawiązać połączenie przy użyciu niepoprawnego hasła.<ul><li>Skontaktuj się z autoryzowanymi użytkownikami i sprawdź, czy te próby były podjęte z wiarygodnego źródła. Jeśli nadal widzisz dużą liczbę nieudanych prób logowania, należy rozważyć wyłączenie zdalnego zarządzania i skontaktowanie się z administratorem sieci. Po podjęciu odpowiedniej akcji wyczyść ten alert na stronie alertów.</li><li>Sprawdź, że Twoje wystąpienia menedżera migawek są skonfigurowane z poprawne hasło. Po podjęciu odpowiedniej akcji wyczyść ten alert na stronie alertów.</li></ul>Aby uzyskać więcej informacji, przejdź do [Zmień hasło urządzenia wygasłe](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password). |
| Wystąpił jeden lub więcej błędów podczas zmiany klucza szyfrowania danych usługi. | |Wystąpiły błędy podczas modyfikowania klucza szyfrowania danych usługi. Po usunięciu warunków błędów uruchom `Invoke-HcsmServiceDataEncryptionKeyChange` polecenia cmdlet z interfejsu programu Windows PowerShell dla usługi StorSimple na urządzeniu, aby zaktualizować usługę. Jeśli ten problem będzie nadal występować, skontaktuj się z pomocą techniczną firmy Microsoft. Po rozwiązaniu problemu wyczyść ten alert na stronie alertów. |

### <a name="support-package-alerts"></a>Obsługa alertów pakietu

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Nie można utworzyć pakietu dla pomocy technicznej. |Usługa StorSimple nie można wygenerować pakietu. |Ponów próbę wykonania tej operacji. Jeśli problem będzie się powtarzać, skontaktuj się z działem pomocy technicznej firmy Microsoft. Po rozwiązaniu problemu wyczyść ten alert na stronie alertów. |

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [StorSimple błędy i rozwiązywanie problemów z wdrażaniem urządzenia](storsimple-8000-troubleshoot-deployment.md).

