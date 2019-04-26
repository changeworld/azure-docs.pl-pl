---
title: Wyświetlanie i Zarządzanie alertami programu Microsoft Azure StorSimple Virtual Array | Dokumentacja firmy Microsoft
description: Opisuje warunki alertu rozwiązania StorSimple Virtual Array i ważności oraz korzystać z usługi StorSimple Manager do zarządzania alertami.
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
ms.openlocfilehash: bb6ef5a87c5610d90188471db961ef20dfb18835
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60302809"
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>Menedżer urządzeń StorSimple Użyj, aby zarządzać alertami dla rozwiązania StorSimple Virtual Array

## <a name="overview"></a>Omówienie

Funkcja alertów w usłudze Menedżer urządzeń StorSimple umożliwia przeglądanie i wyczyść alerty powiązane z macierzy wirtualnych StorSimple na zasadzie w czasie rzeczywistym. Alerty mogą być używane na **Podsumowanie usługi** bloku centralne monitorowanie problemów dotyczących kondycji usługi macierze wirtualne StorSimple i ogólnego rozwiązania Microsoft Azure StorSimple.

W tym samouczku opisano, jak skonfigurować powiadomienia o alertach, typowe warunki alertu, poziomy ważności alertów oraz jak wyświetlać i śledzić alerty. Ponadto zawiera krótki przewodnik po alertu tabelami, które umożliwiają szybko zlokalizować określony alert i odpowiednio reagować.

![Strona alertów](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Konfigurowanie ustawień alertów

Można wybrać, czy chcesz otrzymywać powiadomienia pocztą e-mail dotyczące warunków alertów dla każdej macierzy wirtualnej StorSimple. Ponadto można zidentyfikować innych adresatów powiadomień o alertach, wprowadzając ich adresy e-mail w **dodatkowi adresaci wiadomości e-mail** okno, oddzielając je średnikami.

> [!NOTE]
> Możesz wprowadzić maksymalnie 20 adresów e-mail na macierz wirtualną.

Po włączeniu powiadomienia e-mail dla macierzy wirtualnej, członków listy powiadomień otrzymają wiadomość e-mail z każdym alert krytyczny o wystąpieniu. Wysyłane z *storsimple — alerty noreply\@mail.windowsazure.com* i opisano warunek alertu. Adresaci mogą kliknąć **Unsubscribe** aby został usunięty z listy powiadomień e-mail.

#### <a name="to-enable-email-notification-for-alerts"></a>Aby włączyć powiadomienia e-mail dla alertów

1. Przejdź do usługi Menedżer urządzeń StorSimple i **zarządzania** sekcji, wybierz i kliknij **urządzeń**. Z listy urządzenia wyświetlane wybierz i kliknij swoje urządzenie.
   
    ![Ustawienia alertów](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. Spowoduje to otwarcie **ustawienia** bloku. W **ustawienia urządzenia** zaznacz **ogólne**. Spowoduje to otwarcie **ustawienia ogólne** bloku.
   
    ![Konfiguracja powiadomień alertów](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. W **ustawienia ogólne** przejdź do bloku **ustawienia alertu** sekcji, a następnie ustaw następujące opcje:
   
   1. W **Włącz powiadomienie e-mail** pól, zaznacz **tak**.
   2. W **E-mail administratorów usługi** pól, zaznacz **tak** Jeśli chcesz mieć administratora usługi i wszyscy współadministratorzy otrzymywać powiadomienia o alertach.
   3. W **dodatkowi adresaci wiadomości e-mail** wprowadź adresy e-mail wszystkich pozostałych adresatów, którzy mają otrzymywać powiadomienia o alertach. Wprowadź nazwy w formacie *ktoś\@somewhere.com*. Użyj średników do oddzielenia adresów e-mail. Można skonfigurować maksymalnie 20 adresów e-mail na urządzeniu wirtualnym.
      
       ![Konfiguracja powiadomień alertów](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. Aby Wyślij testowe powiadomienie e-mail, kliknij przycisk **Wyślij testową wiadomość e-mail**. Usługa Menedżer urządzeń StorSimple spowoduje wyświetlenie komunikatów o stanie, jak przekazuje testowe powiadomienie.
      
       ![Alerty testu wysłana wiadomość e-mail z powiadomieniem](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > Jeśli nie można wysłać komunikatu z powiadomieniem testowym, usługi Menedżer urządzeń StorSimple, wyświetli odpowiedni komunikat. Kliknij przycisk **OK**, poczekaj kilka minut, a następnie spróbuj ponownie wysłać komunikatu z powiadomieniem testowym.
      >
      >
   5. W dolnej części strony kliknij **Zapisz** Aby zapisać konfigurację. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**.
      
      ![Alerty testu wysłana wiadomość e-mail z powiadomieniem](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>Typowe warunki alertu

Rozwiązania StorSimple Virtual Array generuje alerty w odpowiedzi na szereg warunków. Poniżej przedstawiono najbardziej typowych warunków alertów:

* **Problemy z łącznością** — te alerty występują, gdy istnieje trudności podczas transferu danych. Problemy z komunikacją może wystąpić podczas transferu danych do i z kontem usługi Azure storage lub z powodu braku łączności między urządzenia wirtualne i usługi Menedżer urządzeń StorSimple. Problemy z komunikacją są niektóre z najtrudniejszych rozwiązać problem, ponieważ istnieje tak wiele punktów awarii. Należy zawsze najpierw sprawdzić połączenie sieciowe oraz dostęp do Internetu są dostępne przed przejściem do bardziej zaawansowanego rozwiązywania problemów. Aby uzyskać informacje o portach i ustawienia zapory, przejdź do [wymagania systemowe macierzy wirtualnej StorSimple](storsimple-ova-system-requirements.md). Aby uzyskać pomoc w rozwiązywaniu problemów, przejdź do [Rozwiązywanie problemów za pomocą polecenia cmdlet Test-Connection](storsimple-troubleshoot-deployment.md).
* **Problemy z wydajnością** — te alerty są powodowane, gdy system nie działa optymalnie, takich jak znajduje się pod dużym obciążeniem.

Ponadto możesz zobaczyć alerty dotyczące zabezpieczeń, aktualizacji lub niepowodzeń zadań.

## <a name="alert-severity-levels"></a>Poziomy ważności alertów

Alerty mają różne poziomy ważności, w zależności od alertu sytuacji będzie miał wpływ i potrzebę odpowiedzi na alert. Dostępne są następujące poziomy ważności:

* **Krytyczne** — ten alert jest w odpowiedzi na warunek, który ma wpływ na pomyślne wydajność systemu. Czynności upewnij się, że usługi StorSimple, usługi nie zostało przerwane.
* **Ostrzeżenie** — ten stan może stać się krytyczny, jeśli nie uda się rozwiązać. Należy zbadać sytuację i podejmować żadnych działań, wymagane, aby wyczyścić ten problem.
* **Informacje o** — ten alert zawiera informacje, które mogą być przydatne do śledzenia i zarządzania systemem.

## <a name="view-and-track-alerts"></a>Wyświetlanie i śledzenie alertów

Bloku podsumowania usługi Menedżer urządzeń StorSimple pozwala szybko dowiedzieć się liczba alertów na urządzeniach wirtualnych, uporządkowane według ważności.

![Pulpit nawigacyjny alertów](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

Kliknij przycisk Poziom ważności, aby otworzyć **alerty** bloku. Wyniki obejmują tylko alerty, które odpowiadają tym poziom ważności.

![Raport o alertach ograniczone do typu alertu](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

Kliknij alert, na liście, aby uzyskać dodatkowe szczegóły alertu, w tym czas ostatniego, który został zgłoszony alert, liczbę wystąpień alertu na urządzeniu i zalecaną akcję, aby rozwiązać alert.

![Lista alertów i szczegółów](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

Szczegóły alertu można skopiować do pliku tekstowego, jeśli chcesz wysłać informacje do firmy Microsoft Support. Po i zalecenia i usunięciu warunek alertu w środowisku lokalnym, należy wyczyścić alert z listy. Z listy wybierz alert, a następnie kliknij przycisk **wyczyść**. Aby wyczyścić wiele alertów, wybierz poszczególne alerty, kliknij dowolną kolumnę z wyjątkiem **Alert** kolumny, a następnie kliknij **wyczyść** po wybraniu wszystkich alertów do wyczyszczenia.

Po kliknięciu **wyczyść**, będziesz mieć możliwość zapewnienia komentarze na temat alertu oraz czynności, które zostały podjęte w celu rozwiązania problemu.

![komentarze alertu](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

Niektóre zdarzenia zostaną wyczyszczone przez system, jeśli inne zdarzenie zostanie wyzwolony o nowe informacje.

## <a name="sort-and-review-alerts"></a>Sortowanie i przegląd alertów

**Alerty** bloku można wyświetlić alerty do 250. Po przekroczeniu tej liczby alertów, nie wszystkie alerty będą wyświetlane w widoku domyślnego. Można połączyć następujące pola, aby dostosować alerty są wyświetlane:

* **Stan** — można wyświetlić **Active** lub **wyczyszczone** alertów. Aktywne alerty nadal są wyzwalane w systemie, natomiast wyczyszczone alerty zostały albo ręcznie wyczyścić przez administratora lub programowo wyczyszczone, ponieważ system zaktualizowany stan alertu o nowe informacje.
* **Ważność** — można wyświetlać alerty, wszystkie poziomy ważności (informacje o krytycznych,) lub tylko niektórych ważność, takich jak alerty tylko krytyczne.
* **Źródło** — Wyświetl alerty od wszystkich źródeł lub ograniczyć alerty do tych, które pochodzą z jednego lub wszystkich urządzeń wirtualnych lub usługi.
* **Zakres czasu** — określając **z** i **do** dat i sygnatury czasowe, można wyświetlać alerty przedziale czasu, który Cię interesuje.

## <a name="alerts-quick-reference"></a>Krótki przewodnik dotyczący alertów

W poniższej tabeli wymieniono niektóre alerty StorSimple, które można napotkać, a także dodatkowe informacje i zalecenia dotyczące gdzie są dostępne. Alerty macierz wirtualną StorSimple można podzielić na następujące kategorie:

* [Alerty łączności chmury](#cloud-connectivity-alerts)
* [Alerty konfiguracji](#configuration-alerts)
* [Alerty dotyczące błędów zadań](#job-failure-alerts)
* [Alerty wydajności](#performance-alerts)
* [Alerty zabezpieczeń](#security-alerts)

### <a name="cloud-connectivity-alerts"></a>Alerty łączności chmury

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Urządzenia <*nazwy urządzenia*> nie jest połączone z chmurą. |Nazwanego urządzenia nie można połączyć z chmurą. |Nie można połączyć się z chmurą. Może to być spowodowane jedną z następujących przyczyn:<ul><li>Może to być problem z ustawieniami sieci na urządzeniu.</li><li>Może to być problem z poświadczeń konta magazynu.</li></ul>Aby uzyskać więcej informacji na temat rozwiązywania problemów z łącznością, przejdź do [lokalnego Interfejsu w przeglądarce](storsimple-ova-web-ui-admin.md) urządzenia. |

### <a name="configuration-alerts"></a>Alerty konfiguracji

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Nieobsługiwana konfiguracja urządzenia wirtualnego w środowisku lokalnym. |Niska wydajność. |Bieżąca konfiguracja może spowodować obniżenie wydajności. Upewnij się, że serwer spełnia minimalne wymagania dotyczące konfiguracji. Aby uzyskać więcej informacji, przejdź do [wymagania dotyczące macierzy wirtualnej StorSimple](storsimple-ova-system-requirements.md). |
| Kończy się aprowizowane miejsce na dysku <*nazwy urządzenia*\>. |Ostrzeżenie miejsca na dysku. |Kończy się na aprowizowane miejsce na dysku. Aby zwolnić miejsce, należy wziąć pod uwagę przeniesienia obciążeń do innego woluminu lub udziału lub usunięcie danych. |

### <a name="job-failure-alerts"></a>Alerty dotyczące błędów zadań

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Tworzenie kopii zapasowych <*nazwy urządzenia* \> nie można ukończyć. |Błąd zadania tworzenia kopii zapasowej. |Nie można utworzyć kopii zapasowej. Należy wziąć pod uwagę jedną z następujących czynności:<ul><li>Problemy z łącznością mogą uniemożliwiać pomyślne ukończenie operacji wykonywania kopii zapasowej. Upewnij się, że nie istnieją żadne problemy z łącznością. Aby uzyskać więcej informacji na temat rozwiązywania problemów z łącznością, przejdź do [lokalnego Interfejsu w przeglądarce](storsimple-ova-web-ui-admin.md) dla urządzenia wirtualnego.</li><li>Osiągnięto limit magazynowania danych. Aby zwolnić miejsce, należy rozważyć usunięcie wszelkich kopii zapasowych, które nie są już potrzebne.</li></ul> Rozwiąż problemy, wyczyść alert i spróbuj ponownie wykonać operację. |
| Klon <*nazwy urządzenia* \> nie można ukończyć. |Klonuj niepowodzenie zadania. |Nie można utworzyć klonu. Należy wziąć pod uwagę jedną z następujących czynności:<ul><li>Na liście kopii zapasowej jest nieprawidłowy. Odśwież listę, aby sprawdzić, czy jest nadal ważny.</li><li>Problemy z łącznością mogą uniemożliwiać pomyślne ukończenie operacji klonowania. Upewnij się, że nie istnieją żadne problemy z łącznością.</li><li>Osiągnięto limit magazynowania danych. Aby zwolnić miejsce, należy rozważyć usunięcie wszelkich kopii zapasowych, które nie są już potrzebne.</li></ul>Rozwiąż problemy, wyczyść alert i spróbuj ponownie wykonać operację. |

### <a name="networking-alerts"></a>Alerty sieci

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Nie można nawiązać połączenia z usługą uwierzytelniania. |Błąd ścieżki danych |Adres URL, który jest używany do uwierzytelniania jest nieosiągalny. Upewnij się, że reguły zapory obejmują wzorce adresów URL, określony dla urządzenia StorSimple. Aby uzyskać więcej informacji na temat wzorców adresów URL w witrynie Azure portal, przejdź do [rozwiązania StorSimple Virtual Array wymagania sieciowe](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).|

### <a name="performance-alerts"></a>Alerty wydajności

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Występują nieoczekiwane opóźnienia transferu danych. |Transfer danych powolne. |Błędy ograniczania wystąpić, gdy przekracza cele skalowalności usługi magazynu. Usługa storage ma ten element, aby zagwarantować, że nie pojedynczego klienta lub dzierżawy mogą używać usługi kosztem innych. Aby uzyskać więcej informacji na temat rozwiązywania problemów z kontem usługi Azure storage, przejdź do [monitorowanie, diagnozowanie i rozwiązywanie problemów z usługi Microsoft Azure Storage](../storage/common/storage-monitoring-diagnosing-troubleshooting.md). |
| Kończy się zarezerwowane miejsce lokalne miejsca na dysku na <*nazwy urządzenia*\>. |Czas wolnej odpowiedzi. |10% całkowitego aprowizowanego rozmiaru dla <*nazwy urządzenia* \> jest zastrzeżony na lokalnym urządzeniu, a teraz kończy się zarezerwowane miejsce na. Obciążenia na <*nazwy urządzenia* \> generuje wyższy stopień zmian lub może być ostatnio migracji dużej ilości danych. Może to spowodować zmniejszenie wydajności. Należy wziąć pod uwagę jedną z następujących czynności, aby rozwiązać ten problem:<ul><li>Zwiększyć przepustowość chmury na tym urządzeniu.</li><li>Zmniejsz lub przenieść obciążenia do innego woluminu lub udziału.</li></ul> |

### <a name="security-alerts"></a>Alerty zabezpieczeń

| Tekst alertu | Wydarzenie | Więcej informacji / zalecane akcje |
|:--- |:--- |:--- |
| Hasło dla <*nazwy urządzenia* \> wygaśnie <*numer* \> dni. |Ostrzeżenie o hasło. |Twoje hasło wygaśnie <*numer* \> dni. Należy rozważyć zmianę hasła. Aby uzyskać więcej informacji, przejdź do [zmienić hasło administratora urządzenia StorSimple Virtual Array](storsimple-virtual-array-change-device-admin-password.md). |

## <a name="next-steps"></a>Kolejne kroki

* [Więcej informacji na temat rozwiązania StorSimple Virtual Array](storsimple-ova-overview.md).
