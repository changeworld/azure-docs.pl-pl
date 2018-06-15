---
title: Wyświetl alerty i zarządzaj nimi tablicy wirtualne Microsoft Azure StorSimple | Dokumentacja firmy Microsoft
description: Opisuje tablicy wirtualnego StorSimple warunków alertów i ważność oraz sposobu używania usługi Menedżer StorSimple do zarządzania alertami.
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
ms.openlocfilehash: 7d4d680e3460fbeff73c2f334c6461da7967374d
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2018
ms.locfileid: "27786411"
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>Użyj Menedżera urządzeń StorSimple do zarządzania alertami dla tablicy wirtualnego StorSimple

## <a name="overview"></a>Przegląd

Funkcja alertów w usłudze Menedżer StorSimple urządzenia umożliwia Przejrzyj i wyczyść alerty powiązane z macierzami wirtualnego StorSimple na podstawie w czasie rzeczywistym. Alerty mogą być używane na **Podsumowanie usługi** bloku, aby centralnie monitorować problemy dotyczące kondycji z macierzami wirtualnego StorSimple oraz ogólnego rozwiązania Microsoft Azure StorSimple.

Ten przewodnik opisuje sposób konfigurowania powiadomień o alertach, typowe warunki alertu, poziomy ważności alertów i sposobu wyświetlania i śledzenie alertów. Ponadto zawiera tabele alertu krótkimi opisami, które umożliwiają szybkie zlokalizować określony alert i reagowania.

![Strona alertów](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Konfigurowanie ustawień alertów

Można wybrać, czy chcesz otrzymać powiadomienie e-mail alertów warunki dla każdego z macierzami wirtualne StorSimple. Ponadto można zidentyfikować innych odbiorców powiadomień o alertach, wprowadzając ich adresów e-mail w **adresatów wiadomości e-mail dodatkowych** okno, oddzielając je średnikami.

> [!NOTE]
> Możesz wprowadzić maksymalnie 20 adresy e-mail na tablicę wirtualnego.


Po włączeniu powiadomień e-mail dla tablicy wirtualnych elementów członkowskich listy powiadomień otrzyma wiadomość e-mail z każdym alert krytyczny wystąpieniu. Wysyłane z  *storsimple-alerts-noreply@mail.windowsazure.com*  i opisano stan alertu. Można kliknąć adresatów **anulowania subskrypcji** usunąć siebie z listy powiadomień poczty e-mail.

#### <a name="to-enable-email-notification-for-alerts"></a>Aby włączyć powiadomienia e-mail dla alertów

1. Przejdź do usługi Menedżer StorSimple urządzenia i w **zarządzania** , wybierz i kliknij pozycję **urządzeń**. Na liście wyświetlane urządzenia wybierz i kliknij urządzenie.
   
    ![Ustawienia alertów](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. Spowoduje to otwarcie **ustawienia** bloku. W **ustawienia urządzenia** zaznacz **ogólne**. Spowoduje to otwarcie **ustawienia ogólne** bloku.
   
    ![Konfiguracja powiadomień alertów](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. W **ustawienia ogólne** bloku, przejdź do **ustawienia alertu** sekcji i określić następujące ustawienia:
   
   1. W **Włącz powiadomienia e-mail** pól, zaznacz **tak**.
   2. W **poczty E-mail Administratorzy usługi** pól, zaznacz **tak** chcesz, aby administrator usługi i współadministratorzy wszystkie wyświetlone powiadomienia o alertach.
   3. W **adresatów wiadomości e-mail dodatkowych** wprowadź adresy e-mail wszystkich adresatów, którzy mają otrzymywać powiadomienia o alertach. Wprowadź nazwy w formacie  *someone@somewhere.com* . Użyj średników do rozdzielania adresów e-mail. Można skonfigurować maksymalnie 20 adresy e-mail na urządzeniu wirtualnym.
      
       ![Konfiguracja powiadomień alertów](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. Aby wysłać powiadomienie e-mail testowe, kliknij przycisk **Wyślij testową wiadomość e-mail**. Usługę Menedżer StorSimple urządzenia będą wyświetlane komunikaty o stanie, jak przesyła powiadomienie testowe.
      
       ![Alerty przetestować powiadomienia e-mail wysyłanego](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > Jeśli nie można wysłać komunikatu powiadomienia testu, usługi Menedżer StorSimple urządzenia wyświetli odpowiedni komunikat. Kliknij przycisk **OK**, zaczekaj kilka minut, a następnie spróbuj ponownie wysłać komunikatu powiadomienia testu.
      > 
      > 
   5. W dolnej części strony kliknij **zapisać** Aby zapisać konfigurację. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**.
      
      ![Alerty przetestować powiadomienia e-mail wysyłanego](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>Typowe warunki alertu

Tablica wirtualnego StorSimple generuje alerty w odpowiedzi na szereg warunków. Poniżej przedstawiono najbardziej typowych alertów warunki:

* **Problemy z łącznością** — te alerty występują w przypadku trudności podczas transferu danych. Problemy z komunikacją mogą wystąpić podczas transferu danych do i z kontem magazynu platformy Azure lub z powodu braku łączności między urządzenia wirtualne i usługi Menedżer StorSimple urządzenia. Problemy z komunikacją się najtrudniejsze rozwiązać problem, ponieważ istnieje wiele punktów awarii. Należy zawsze najpierw sprawdź, czy połączenie sieciowe oraz dostęp do Internetu są dostępne przed kontynuowaniem do bardziej zaawansowanego rozwiązywania problemów. Aby uzyskać informacje o portach i ustawienia zapory, przejdź do [wymagania systemowe tablicy wirtualnego StorSimple](storsimple-ova-system-requirements.md). Aby uzyskać pomoc dotyczącą rozwiązywania problemów, przejdź do [Rozwiązywanie problemów za pomocą polecenia cmdlet Test-Connection](storsimple-troubleshoot-deployment.md).
* **Problemy z wydajnością** — te alerty są powodowane przeprowadzania systemu nie jest optymalnie, np. gdy jest mocno obciążony.

Ponadto można napotkać alertów dotyczących zabezpieczeń, aktualizacje lub niepowodzenia zadań.

## <a name="alert-severity-levels"></a>Poziomy ważności alertów

Alerty mają różne poziomy ważności, w zależności od wpływu tego alertu sytuacji i potrzebę odpowiedzi na alert. Dostępne są następujące poziomy ważności:

* **Krytyczne** — alert jest w odpowiedzi na warunek, który ma wpływ na pomyślne wydajność systemu. Czynności upewnij się, że StorSimple usługi nie zostało przerwane.
* **Ostrzeżenie** — ten stan może stać się krytyczny, jeśli nie uda się rozwiązać. Należy zbadać sytuację i wykonaj czynności wymagane do wyczyść problem.
* **Informacje o** — tego alertu zawiera informacje, które mogą być przydatne w śledzenia i zarządzanie systemem.

## <a name="view-and-track-alerts"></a>Wyświetlanie i śledzenie alertów

Blok podsumowania usługi Menedżer StorSimple urządzenia zapewnia szybkiego dostępu na liczbę alertów dla urządzenia wirtualnego uporządkowane według poziomu ważności.

![Pulpit nawigacyjny alertów](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

Kliknij przycisk Poziom ważności, aby otworzyć **alerty** bloku. Wyniki obejmują tylko alerty spełniające ten poziom ważności.

![Raport o alertach zakres typu alertu](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

Kliknij alert, na liście, aby uzyskać dodatkowe szczegóły alertu, czasu, gdy został zgłoszony alert, w tym liczbę wystąpień alertu na urządzeniu i zalecanej akcji, aby rozwiązać alert.

![Lista alertów i szczegóły](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

Szczegóły alertu można skopiować do pliku tekstowego, jeśli chcesz wysłać informacje do firmy Microsoft Support. Po po zalecenia i rozpoznać warunku alertu lokalną, należy wyczyścić alert z listy. Wybierz z listy alert, a następnie kliknij przycisk **wyczyść**. Aby wyczyścić wiele alertów, wybierz poszczególne alerty, kliknij przycisk wszystkich kolumn z wyjątkiem **Alert** kolumny, a następnie kliknij przycisk **wyczyść** po wybraniu wszystkich alertów do wyczyszczenia.

Po kliknięciu **wyczyść**, będzie mieć możliwość zapewnienia komentarze dotyczące alertu i kroki, które miały Aby rozwiązać ten problem. 

![komentarze alertu](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

Niektóre zdarzenia zostaną usunięte przez system, jeśli inny zdarzenie zostanie wyzwolone przy użyciu nowych informacji. 

## <a name="sort-and-review-alerts"></a>Sortowanie i przegląd alertów

**Alerty** bloku można wyświetlać alerty do 250. Po przekroczeniu tej liczby alertów, nie wszystkie alerty będą wyświetlane w widoku domyślnym. Możesz połączyć ze sobą następujące pola, aby dostosować alerty są wyświetlane:

* **Stan** — można wyświetlić **Active** lub **wyczyszczone** alertów. Aktywne alerty nadal są inicjowane w systemie, gdy alerty wyczyszczone zostały albo ręcznie usunięte przez administratora lub programistycznie wyczyścić, ponieważ system Aktualizowanie stanu alertu nowymi informacjami.
* **Ważność** — można wyświetlać alerty wszystkie poziomy ważności (krytyczny, ostrzeżenie, informacje o) lub tylko niektórych ważność, takich jak alerty tylko krytyczne.
* **Źródło** — wyświetlania alertów ze wszystkich źródeł lub ograniczyć alerty do tych, które pochodzą z jednego lub wszystkich urządzeń wirtualnych lub usługi.
* **Zakres czasu** — określając **z** i **do** dat i sygnatury czasowe, można wyświetlać alerty w czasie, który chcesz.

## <a name="alerts-quick-reference"></a>Krótki przewodnik alertów

W poniższych tabelach przedstawiono niektóre alerty StorSimple, które można napotkać, a także dodatkowe informacje i zalecenia w przypadku, gdy dostępna. Alerty tablicy wirtualnego StorSimple można podzielić na następujące kategorie:

* [Alerty łączności chmury](#cloud-connectivity-alerts)
* [Alerty konfiguracji](#configuration-alerts)
* [Alerty błędów zadania](#job-failure-alerts)
* [Alerty wydajności](#performance-alerts)
* [Alerty zabezpieczeń](#security-alerts)

### <a name="cloud-connectivity-alerts"></a>Alerty łączności chmury

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Urządzenie  *<device name>*  nie jest połączony z chmurą. |Nazwanego urządzenia nie może połączyć się z chmurą. |Nie można nawiązać połączenia z chmury. Może to być spowodowane jedną z następujących czynności:<ul><li>Może to być problem z ustawienia sieci na urządzeniu.</li><li>Być może wystąpił problem z poświadczeniami konta magazynu.</li></ul>Aby uzyskać więcej informacji na temat rozwiązywania problemów z łącznością, przejdź do [lokalnego interfejsu użytkownika sieci web](storsimple-ova-web-ui-admin.md) urządzenia. |

### <a name="configuration-alerts"></a>Alerty konfiguracji

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Konfiguracja urządzenia wirtualnego lokalnymi nieobsługiwany. |Niska wydajność. |Bieżąca konfiguracja może spowodować obniżenie wydajności. Upewnij się, że serwer spełnia wymagania dotyczące minimalnej konfiguracji. Aby uzyskać więcej informacji, przejdź do [wymagania dotyczące urządzenia StorSimple wirtualnego tablicy](storsimple-ova-system-requirements.md). |
| Kończy się miejsce na dysku elastycznie <*nazwa urządzenia*>. |Ostrzeżenie miejsca na dysku. |Masz mało miejsca na dysku elastycznie. Aby zwolnić miejsce, należy wziąć pod uwagę przeniesienie obciążeń do innego woluminu lub udziału lub usunięcie danych. |

### <a name="job-failure-alerts"></a>Alerty błędów zadania

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Tworzenie kopii zapasowych <*nazwa urządzenia*> nie można ukończyć. |Niepowodzenie zadania tworzenia kopii zapasowej. |Nie można utworzyć kopii zapasowej. Weź pod uwagę jedną z następujących czynności:<ul><li>Problemy z łącznością może uniemożliwiać pomyślne wykonanie operacji tworzenia kopii zapasowej. Upewnij się, że nie ma problemów z połączeniem. Aby uzyskać więcej informacji na temat rozwiązywania problemów z łącznością, przejdź do [lokalnego interfejsu użytkownika sieci web](storsimple-ova-web-ui-admin.md) dla urządzenia wirtualnego.</li><li>Osiągnięto limit dostępnej pamięci. Aby zwolnić miejsce, należy rozważyć usunięcie wszelkich kopii zapasowych, które nie są już potrzebne.</li></ul> Rozwiąż problemy, wyczyść alert, a następnie spróbuj ponownie wykonać operację. |
| Klon <*nazwa urządzenia*> nie można ukończyć. |Klonowanie niepowodzenie zadania. |Nie można utworzyć klonu. Weź pod uwagę jedną z następujących czynności:<ul><li>Z listy kopii zapasowych jest nieprawidłowy. Odśwież listę, aby sprawdzić, czy jest nadal ważny.</li><li>Problemy z łącznością może uniemożliwiać pomyślne wykonanie operacji klonowania. Upewnij się, że nie ma problemów z połączeniem.</li><li>Osiągnięto limit dostępnej pamięci. Aby zwolnić miejsce, należy rozważyć usunięcie wszelkich kopii zapasowych, które nie są już potrzebne.</li></ul>Rozwiąż problemy, wyczyść alert, a następnie spróbuj ponownie wykonać operację. |

### <a name="networking-alerts"></a>Alerty sieci
| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Nie można nawiązać połączenia z usługą uwierzytelniania. |Błąd ścieżki danych |Adres URL, który jest używany do uwierzytelniania nie jest dostępny. Upewnij się, że reguły zapory obejmuje wzorce adres URL określony dla urządzenia StorSimple. Aby uzyskać więcej informacji dotyczących wzorców adresów URL w portalu Azure, przejdź do [tablicy wirtualnego StorSimple wymagań sieciowych](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).|

### <a name="performance-alerts"></a>Alerty wydajności

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Występują nieoczekiwane opóźnienia transferu danych. |Transfer danych powolne. |Ograniczenia przepustowości błędy występują w przypadku przekroczenia wartości docelowe skalowalności usługi magazynu. Usługa magazynu jest to, aby upewnić się, że nie jednego klienta lub dzierżawcy mogą używać usługi kosztem innych użytkowników. Aby uzyskać więcej informacji na temat usuwania konta magazynu Azure, przejdź do [monitorowanie, diagnozowanie i rozwiązywanie problemów z usługi Magazyn Microsoft Azure](../storage/common/storage-monitoring-diagnosing-troubleshooting.md). |
| Możesz zaczyna brakować miejsca na dysku lokalnym rezerwacji <*nazwa urządzenia*>. |Długi czas odpowiedzi. |10% całkowitego rozmiaru udostępnione dla <*nazwa urządzenia*> jest zarezerwowana na lokalnym urządzenia, a teraz zaczyna brakować miejsca zastrzeżone. Obciążenie <*nazwa urządzenia*> generuje wyższa szybkość zmian lub może mieć ostatnio zmigrowany dużej ilości danych. To może spowodować zmniejszenie wydajności. Weź pod uwagę jedną z następujących czynności, aby rozwiązać ten problem:<ul><li>Zwiększenie przepustowości chmury do tego urządzenia.</li><li>Zmniejsz lub przenoszenie obciążeń do innego woluminu lub udziału.</li></ul> |

### <a name="security-alerts"></a>Alerty zabezpieczeń

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Hasło dla <*nazwa urządzenia*> wygaśnie za <*numer*> dni. |Ostrzeżenie o hasło. |Twoje hasło wygaśnie w < numer < dni. Rozważ zmianę hasła. Aby uzyskać więcej informacji, przejdź do [Zmień hasło administratora urządzenia StorSimple wirtualnego tablicy](storsimple-virtual-array-change-device-admin-password.md). |

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej o tablicy wirtualnego StorSimple](storsimple-ova-overview.md).

