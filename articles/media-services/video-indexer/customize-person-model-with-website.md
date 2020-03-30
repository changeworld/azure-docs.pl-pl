---
title: Dostosowywanie modelu osoby za pomocą witryny video Indexer
titleSuffix: Azure Media Services
description: Dowiedz się, jak dostosować model osoby za pomocą witryny indeksatora wideo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: f29e651f5c8542722f0dc2c9878184ac0d2a6a1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499948"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Dostosowywanie modelu osoby za pomocą witryny video Indexer

Indeksator wideo obsługuje rozpoznawanie treści wideo przez celebrytów. Funkcja rozpoznawania gwiazd obejmuje około miliona twarzy na podstawie powszechnie żądanego źródła danych, takiego jak IMDB, Wikipedia i najlepsi influencerzy LinkedIn. Aby uzyskać szczegółowe omówienie, zobacz [Dostosowywanie modelu osoby w indeksatorze wideo](customize-person-model-overview.md).

Za pomocą witryny video indexer można edytować twarze wykryte w filmie, zgodnie z opisem w tym temacie. Można również użyć interfejsu API, zgodnie z opisem w [polu Dostosuj model osoby przy użyciu interfejsów API](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>Centralne zarządzanie modelami osób na koncie

1. Aby wyświetlić, edytować i usunąć modele osób na koncie, przejdź do witryny indeksatora wideo i zaloguj się.

2. Wybierz przycisk dostosowywania modelu zawartości w prawym górnym rogu strony.

    ![Dostosowywanie modelu zawartości](./media/customize-face-model/content-model-customization.png)

3. Wybierz kartę Kontakty.

    Na koncie zobaczysz model Osoby domyślnej. Model Osoby domyślnej zawiera wszystkie twarze, które mogły być edytowane lub zmienione w statystykach filmów, dla których nie określono niestandardowego modelu osoby podczas indeksowania.

    Jeśli utworzono inne modele osób, będą one również wyświetlane na tej stronie.

    ![Dostosowywanie modelu zawartości](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>Tworzenie nowego modelu osoby

1. Wybierz przycisk **+ Dodaj model.**

    ![Dodawanie nowego modelu osoby](./media/customize-face-model/add-new-person.png)

2. Wprowadź nazwę modelu i wybierz przycisk wyboru obok nazwy.

    ![Dodawanie nowego modelu osoby](./media/customize-face-model/add-new-person2.png)

    Utworzono nowy model osoby. Teraz można dodawać ściany do nowego modelu osoby.

3. Wybierz przycisk menu listy i wybierz **+ Dodaj osobę**.

    ![Dodawanie nowego modelu osoby](./media/customize-face-model/add-new-person3.png)

## <a name="add-a-new-person-to-a-person-model"></a>Dodawanie nowej osoby do modelu osoby

> [!NOTE]
> Indeksator wideo umożliwia dodanie wielu osób o tej samej nazwie w modelu osoby. Zaleca się jednak nadać unikatowe nazwy każdej osobie w modelu dla użyteczności i przejrzystości.

1. Aby dodać nową ścianę do modelu osoby, wybierz przycisk menu listy obok modelu osoby, do którego chcesz dodać ścianę.

1. Wybierz **+ Dodaj osobę** z menu.

    ![Dodawanie nowej twarzy do osoby](./media/customize-face-model/add-new-face.png)

    W wyskakującym okienku zostanie wyświetlony monit o wypełnienie danych osoby. Wpisz imię i nazwisko osoby i wybierz przycisk wyboru.

    ![Dodawanie nowej twarzy do osoby](./media/customize-face-model/add-new-face2.png)

    Następnie można wybrać z eksploratora plików lub przeciągnąć i upuścić obrazy twarzy twarzy. Indeksator wideo zajmie wszystkie standardowe typy plików obrazów (np. JPG, PNG i inne).

    Indeksator wideo może wykrywać wystąpienia tej osoby w przyszłych indeksowanych filmach i bieżących filmach, które zostały już zindeksowane, przy użyciu modelu osoby, do którego dodano tę nową twarz. Rozpoznanie osoby w bieżących filmach może zająć trochę czasu, ponieważ jest to proces wsadowy.

## <a name="rename-a-person-model"></a>Zmienianie nazwy modelu osoby

Możesz zmienić nazwę dowolnego modelu osoby na koncie, w tym modelu osoby domyślnej. Nawet jeśli zmienisz nazwę domyślnego modelu osoby, nadal będzie on służył jako domyślny model osoby na Twoim koncie.

1. Wybierz przycisk menu listy obok modelu osoby, którego nazwę chcesz zmienić.
2. Z menu **wybierz polecenie Zmień nazwę.**

    ![Zmienianie nazwy modelu osoby](./media/customize-face-model/rename-person.png)

3. Wybierz bieżącą nazwę modelu i wpisz nową nazwę.

    ![Zmienianie nazwy modelu osoby](./media/customize-face-model/rename-person2.png)

4. Wybierz przycisk wyboru, aby model miał zostać zmieniony.

## <a name="delete-a-person-model"></a>Usuwanie modelu osoby

Możesz usunąć dowolny model osoby utworzony na swoim koncie. Nie można jednak usunąć modelu osoby domyślnej.

1. Z menu wybierz **polecenie Usuń.**

    ![Usuwanie modelu osoby](./media/customize-face-model/delete-person.png)

    Pojawi się okno podręczne i zostanie wyświetlenie powiadomienia, że ta akcja spowoduje usunięcie modelu osoby oraz wszystkich osób i plików, które zawiera. Tej akcji nie można cofnąć.

    ![Usuwanie modelu osoby](./media/customize-face-model/delete-person2.png)

1. Jeśli na pewno wybierz pozycję Usuń ponownie.

> [!NOTE]
> Istniejące filmy wideo, które zostały zindeksowane przy użyciu tego (teraz usuniętego) modelu osoby, nie będą obsługiwać możliwości aktualizowania nazw twarzy wyświetlanych w filmie. Nazwy twarzy w tych filmach będą mogły edytować dopiero po ponownym wyeksji przy użyciu innego modelu osoby. Jeśli ponownie zdań bez określania modelu osoby, zostanie użyty model domyślny.

## <a name="manage-existing-people-in-a-person-model"></a>Zarządzanie istniejącymi osobami w modelu osoby

Aby przyjrzeć się zawartości któregokolwiek z modeli osoby, wybierz strzałkę obok nazwy modelu osoby. W rozwijanej części listy jest wyświetlanych wszystkie osoby w tym konkretnym modelu osoby. Jeśli wybierzesz przycisk menu listy obok każdej z osób, zobaczysz opcje zarządzania, zmiany nazwy i usuwania.  

![Dodawanie nowej twarzy do osoby](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Zmienianie nazwy osoby

1. Aby zmienić nazwę osoby w modelu osoby, wybierz przycisk menu listy i wybierz polecenie **Zmień nazwę** z menu listy.
1. Wybierz bieżącą nazwę osoby i wpisz nową nazwę.
1. Wybierz przycisk wyboru, a nazwa osoby zostanie zmieniona.

### <a name="delete-a-person"></a>Usuwanie osoby

1. Aby usunąć osobę z modelu osoby, wybierz przycisk menu listy i wybierz polecenie **Usuń** z menu listy.
1. W wyskakującym okienku zostanie wyświetlone okno podręczne z napisem, że ta akcja spowoduje usunięcie tej osoby i nie można jej cofnąć.
1. Wybierz **pozycję Usuń** ponownie, a spowoduje to usunięcie osoby z modelu osoby.

### <a name="manage-a-person"></a>Zarządzanie osobą

Jeśli **wybierzesz opcję Zarządzaj,** zobaczysz wszystkie ściany, z których jest szkolony ten model osoby. Te twarze pochodzą z wystąpień tej osoby w filmach, które używają tego modelu osoby lub z obrazów, które zostały przesłane ręcznie.

Możesz dodać więcej twarzy do osoby, wybierając **pozycję Dodaj obrazy**.

Okienko zarządzanie służy do zmiany nazwy osoby i usunięcia osoby z modelu osoby.

## <a name="use-a-person-model-to-index-a-video"></a>Indeksowanie klipu wideo za pomocą modelu osoby

Za pomocą modelu Osoby możesz zaindeksować nowy film, przypisując model osoby podczas przesyłania filmu.

Aby użyć modelu osoby w nowym klipie wideo, wykonaj następujące czynności:

1. Wybierz przycisk **Przekaż** u góry strony.

    ![Przekazywanie modelu osoby](./media/customize-face-model/upload.png)

1. Upuść plik wideo w kole lub wyszukaj plik.
1. Wybierz strzałkę **Opcji zaawansowanych.**

    ![Przekazywanie modelu osoby](./media/customize-face-model/upload2.png)

1. Wybierz wykaz i wybierz utworzony model osoby.

    ![Przekazywanie modelu osoby](./media/customize-face-model/upload3.png)

1. Wybierz opcję **Przekaż** u dołu strony, a nowy film zostanie zindeksowany przy użyciu modelu osoby.

Jeśli nie określisz modelu osoby podczas przesyłania, indeksator wideo zindeksuje film przy użyciu modelu osoby domyślnej na koncie.

## <a name="use-a-person-model-to-reindex-a-video"></a>Ponowne indeksowanie filmu za pomocą modelu osoby

Aby użyć modelu osoby do ponownego indeksowania filmu w kolekcji, przejdź do filmów z konta na stronie głównej indeksatora wideo i umieść wskaźnik myszy na nazwie filmu, który chcesz ponownie wyeksja.

Zobaczysz opcje edytowania, usuwania i ponownego indeksowania filmu.

1. Wybierz opcję ponownego indeksacji filmu.

    ![Użyj modelu osoby, aby ponownie wyeksliować film](./media/customize-face-model/reindex.png)

    Teraz możesz wybrać model osoby, w który chcesz ponownie za pomocą swojego filmu.
1. Wybierz z listy rozwijanej i wybierz model osoby, którego chcesz użyć.

    ![Użyj modelu osoby, aby ponownie wyeksliować film](./media/customize-face-model/reindex2.png)

1. Wybierz przycisk **Reindex,** a film zostanie ponownie zeksexed przy użyciu modelu osoby.

Wszelkie nowe zmiany wprowadzone w twarzach wykrytych i rozpoznanych w filmie, który został ponownie zreeksexed, zostaną zapisane w modelu osoby użytym do ponownego indeksowania wideo.

## <a name="managing-people-in-your-videos"></a>Zarządzanie osobami w filmach

Można zarządzać wykrytymi twarzami i osobami rozpoznawanymi w indeksowanych filmach, edytując i usuwając twarze.

Usunięcie twarzy usuwa określoną twarz ze statystyk filmu.

Edytowanie twarzy zmienia nazwę twarzy, która została wykryta i prawdopodobnie rozpoznawana w filmie. Podczas edytowania twarzy w filmie nazwa ta jest zapisywana jako wpis osoby w modelu osoby przypisany do filmu podczas przesyłania i indeksowania.

Jeśli nie przypiszesz modelu osoby do filmu podczas przesyłania, edycja zostanie zapisana w modelu domyślnej osoby na koncie.

### <a name="edit-a-face"></a>Edytowanie twarzy

> [!NOTE]
> Jeśli model osoby ma co najmniej dwie różne osoby o tej samej nazwie, nie będzie można oznaczyć tej nazwy w filmach wideo korzystających z tego modelu osoby. Zmiany w programie Tylko dla osób, które mają tę nazwę, będą mogły być wprowadzane tylko na karcie Kontakty na stronie dostosowywania modelu zawartości w indeksatorze wideo. Z tego powodu zaleca się nadać unikatowe nazwy każdej osobie w modelu osoby.

1. Przejdź do witryny internetowej Video Indexer i zaloguj się.
1. Wyszukaj film, który chcesz wyświetlić i edytować na swoim koncie.
1. Aby edytować twarz w filmie, przejdź do karty Statystyki i wybierz ikonę ołówka w prawym górnym rogu okna.

    ![Edytowanie twarzy w filmie](./media/customize-face-model/edit-face.png)

1. Wybierz dowolną z wykrytych twarzy i zmień ich nazwy z "Nieznany #X" (lub nazwę, która została wcześniej przypisana do ściany).
1. Po wpisaniu nowej nazwy wybierz ikonę wyboru obok nowej nazwy. Ta akcja zapisuje nową nazwę i rozpoznaje i wymienia wszystkie wystąpienia tej twarzy w innych bieżących filmach i w przyszłych filmach, które przesyłasz. Rozpoznanie twarzy w innych bieżących filmach może zająć trochę czasu, ponieważ jest to proces wsadowy.

Jeśli nadasz nazwę twarzy z nazwą istniejącej osoby w modelu osoby, którego używa film, wykryte obrazy twarzy z tego filmu tej osoby zostaną połączone z tym, co już istnieje w modelu. Jeśli nadasz nazwę twarzy z nową nazwą, w modelu osoby, którego używasz, zostanie utworzony nowy wpis osoby.

![Edytowanie twarzy w filmie](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>Usuwanie twarzy

Aby usunąć wykrytą twarz w filmie, przejdź do okienka Statystyka i wybierz ikonę ołówka w prawym górnym rogu okienka. Wybierz opcję **Usuń** pod nazwą ściany. Ta akcja usuwa wykrytą twarz z filmu. Twarz osoby będzie nadal wykrywana w innych filmach, w których się pojawia, ale możesz usunąć twarz z tych filmów, a także po ich zindeksja.

Osoba, jeśli zostały nazwane, będzie również nadal istnieć w modelu osoby, który został użyty do indeksowania wideo, z którego usunięto twarz, chyba że wyraźnie usunąć osobę z modelu osoby.

![Usuwanie twarzy w filmie](./media/customize-face-model/delete-face.png)

## <a name="next-steps"></a>Następne kroki

[Dostosowywanie modelu osoby przy użyciu interfejsów API](customize-person-model-with-api.md)
