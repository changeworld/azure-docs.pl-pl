---
title: Korzystanie z witryny sieci Web Video Indexer do dostosowywania modelu osoby — Azure
titleSuffix: Azure Media Services
description: W tym artykule pokazano, jak dostosować model osoby za pomocą witryny sieci Web Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 060d94d6181e894c18d268845b48eb802c52730c
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838288"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Dostosowywanie modelu osoby za pomocą witryny sieci Web Video Indexer

Video Indexer obsługuje rozpoznawanie osobistości dla zawartości wideo. Funkcja rozpoznawania osobistości obejmuje około 1 000 000 twarzy na podstawie często zażądanego źródła danych, takiego jak IMDB, Wikipedia i najpopularniejszych wpływów serwisu LinkedIn. Aby zapoznać się z szczegółowym omówieniem, zobacz [Dostosowywanie modelu osoby w Video Indexer](customize-person-model-overview.md).

Za pomocą witryny sieci Web Video Indexer można edytować powierzchnie wykryte w filmie wideo, zgodnie z opisem w tym temacie. Możesz również użyć interfejsu API, zgodnie z opisem w temacie [Dostosowywanie modelu osoby za pomocą interfejsów API](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>Centralne zarządzanie modelami osób na Twoim koncie

1. Aby wyświetlać, edytować i usuwać modele osób na Twoim koncie, przejdź do witryny sieci Web Video Indexer i zaloguj się.
2. Kliknij przycisk dostosowywania modelu zawartości w prawym górnym rogu strony.

    ![Dostosowywanie modelu zawartości](./media/customize-face-model/content-model-customization.png)
3. Wybierz kartę osoby.

    Zostanie wyświetlony domyślny model osoby na Twoim koncie. Domyślny model osoby zawiera wszystkie powierzchnie, które mogły zostać zmodyfikowane lub zmienione w szczegółowych informacjach wideo, dla których nie został określony niestandardowy model osoby podczas indeksowania. 

    Jeśli utworzono inne modele osób, będą one również wyświetlane na tej stronie. 

    ![Dostosowywanie modelu zawartości](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>Utwórz nowy model osoby

1. Kliknij przycisk **+ Dodaj model** .

    ![Dodaj nową osobę](./media/customize-face-model/add-new-person.png)
2. Wprowadź nazwę modelu i kliknij przycisk Sprawdź obok nazwy.

    ![Dodaj nową osobę](./media/customize-face-model/add-new-person2.png)

    Utworzono nowy model osoby. Teraz możesz dodać powierzchnie do nowego modelu osoby.
3. Kliknij przycisk menu listy i wybierz pozycję **+ Dodaj osobę**.

    ![Dodaj nową osobę](./media/customize-face-model/add-new-person3.png)
    
## <a name="add-a-new-person-to-a-person-model"></a>Dodawanie nowej osoby do modelu osoby

> [!NOTE]
> Video Indexer umożliwia dodanie wielu osób o tej samej nazwie w modelu osoby. Zaleca się jednak nadanie nazw unque każdej osobie w modelu w celu zapewnienia użyteczności i przejrzystości.

1. Aby dodać nową miarę do modelu osoby, kliknij przycisk menu listy obok modelu osoby, do której chcesz dodać miarę.
1. Kliknij pozycję **+ Dodaj osobę** z menu.

    ![Dodaj nową miarę do osoby](./media/customize-face-model/add-new-face.png)
 
    W oknie podręcznym zostanie wyświetlony monit o wypełnienie szczegółów osoby. Wpisz nazwę osoby i kliknij przycisk wyboru.

    ![Dodaj nową miarę do osoby](./media/customize-face-model/add-new-face2.png)
 
Następnie możesz wybrać z Eksploratora plików lub przeciągać i upuszczać obrazy z czołową stroną. Video Indexer będą przyjmować wszystkie standardowe typy plików obrazów (np. JPG, PNG itp.).

Video Indexer powinna być w stanie wykrywać wystąpienia tej osoby w przyszłych klipach wideo, które są indeksowane, i bieżące wideo, które zostały już zindeksowane, przy użyciu modelu osoby, do którego dodano nową miarę. Rozpoznawanie osoby w Twoich bieżących filmach wideo może zająć trochę czasu, ponieważ jest to proces wsadowy.


## <a name="rename-a-person-model"></a>Zmiana nazwy modelu osoby

Można zmienić nazwę dowolnego modelu osoby na koncie, w tym domyślny model osoby. Nawet jeśli zmienisz nazwę domyślnego modelu osoby, będzie on nadal używany jako domyślny model osoby na Twoim koncie.

1. Kliknij przycisk menu listy obok modelu osoby, którego nazwę chcesz zmienić.
2. Kliknij polecenie **Zmień nazwę** z menu.

    ![Zmiana nazwy osoby](./media/customize-face-model/rename-person.png)
3. Kliknij bieżącą nazwę modelu i wpisz nową nazwę.

    ![Zmiana nazwy osoby](./media/customize-face-model/rename-person2.png)
4. Kliknij przycisk Sprawdź, aby zmienić nazwę modelu.

## <a name="delete-a-person-model"></a>Usuń model osoby

Możesz usunąć dowolny model osoby utworzony na Twoim koncie. Nie można jednak usunąć domyślnego modelu osoby.

1. Kliknij przycisk **Usuń** w menu.

    ![Usuwanie osoby](./media/customize-face-model/delete-person.png)
    
    Zostanie wyświetlone okno podręczne, a następnie zostanie wyświetlony komunikat z informacją, że ta akcja spowoduje usunięcie modelu osoby i wszystkich osób i plików, które zawiera. Tej akcji nie można cofnąć. 

    ![Usuwanie osoby](./media/customize-face-model/delete-person2.png)
1. Jeśli masz pewność, kliknij ponownie przycisk Usuń.

> [!NOTE]
> Istniejące filmy wideo, które były indeksowane przy użyciu tego modelu osoby (teraz usunięty), nie obsługują możliwości aktualizowania nazw twarzy, które są wyświetlane w filmie wideo. Można edytować nazwy twarzy w tych filmach wideo dopiero po ponownym indeksie ich przy użyciu innego modelu osoby. W przypadku ponownego indeksowania bez określenia modelu osoby zostanie użyty domyślny model. 

## <a name="manage-existing-people-in-a-person-model"></a>Zarządzanie istniejącymi osobami w modelu osoby

Aby przyjrzeć się zawartości dowolnego z modeli osób, kliknij strzałkę obok nazwy modelu osoby.
Lista rozwijana zawiera wszystkie osoby należące do danego modelu osoby. Jeśli klikniesz przycisk menu listy obok każdej osoby, zobaczysz opcje Zarządzanie, zmiana nazwy i usuwanie.  

![Dodaj nową miarę do osoby](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Zmiana nazwy osoby

1. Aby zmienić nazwę osoby w modelu osoby, kliknij przycisk menu listy i wybierz polecenie **Zmień nazwę** z menu listy.
1. Kliknij bieżącą nazwę osoby i wpisz nową nazwę.
1. Kliknij przycisk Sprawdź, a nazwa osoby zostanie zmieniona.

### <a name="delete-a-person"></a>Usuwanie osoby

1. Aby usunąć osobę z modelu osoby, kliknij przycisk menu listy i wybierz polecenie **Usuń** z menu listy.
1. W oknie podręcznym zostanie wyświetlona informacja, że ta akcja spowoduje usunięcie osoby i nie można cofnąć tej akcji.
1. Kliknij przycisk Usuń ponownie. spowoduje to usunięcie osoby z modelu osoby.

### <a name="manage-a-person"></a>Zarządzanie osobą

Jeśli klikniesz pozycję **Zarządzaj**, zobaczysz wszystkie powierzchnie, z których jest szkolony ten model osoby. Te powierzchnie pochodzą z wystąpień tej osoby w wideo korzystających z tego modelu osoby lub z obrazów przekazanych ręcznie. 

Możesz dodać więcej twarzy do osoby, klikając pozycję Dodaj obrazy.

Możesz użyć okienka zarządzanie, aby zmienić nazwę osoby i usunąć osobę z modelu osoby.

## <a name="use-a-person-model-to-index-a-video"></a>Używanie modelu osoby do indeksowania wideo

Za pomocą modelu osoby można indeksować nowe wideo, przypisując modelowi osoby podczas przekazywania wideo.

Aby skorzystać z modelu osoby na nowym filmie wideo, wykonaj następujące czynności:

1. Kliknij przycisk **Przekaż** w górnej części strony.

    ![Upload](./media/customize-face-model/upload.png)
1. Upuść plik wideo w okręgu lub Wyszukaj plik.
1. Kliknij strzałkę opcje zaawansowane.

    ![Upload](./media/customize-face-model/upload2.png)
1. Kliknij listę rozwijaną i wybierz utworzony przez siebie model osoby.

    ![Upload](./media/customize-face-model/upload3.png)
1. Kliknij opcję Przekaż w dolnej części strony, a nowe wideo zostanie indeksowane przy użyciu modelu osoby.

Jeśli model osoby nie zostanie określony podczas przekazywania, Video Indexer będzie indeksować wideo przy użyciu domyślnego modelu osoby na Twoim koncie.

## <a name="use-a-person-model-to-reindex-a-video"></a>Używanie modelu osoby do ponownego indeksowania wideo 

Aby użyć modelu osoby do ponownego indeksowania wideo w kolekcji, przejdź do swoich filmów wideo na stronie głównej Video Indexer i umieść kursor nad nazwą filmu wideo, który chcesz ponownie zindeksować.

Zobaczysz opcje umożliwiające edytowanie, usuwanie i ponowne indeksowanie wideo. 

1. Kliknij opcję, aby ponownie zindeksować wideo.

    ![Ponowna indeksacja](./media/customize-face-model/reindex.png)

    Teraz możesz wybrać model osoby, aby ponownie zindeksować wideo za pomocą programu.
1. Kliknij listę rozwijaną i wybierz model osoby, którego chcesz użyć. 

    ![Ponowna indeksacja](./media/customize-face-model/reindex2.png)

1. Kliknij przycisk ponownie **Indeksuj** , a Twoje wideo zostanie ponownie przeindeksowane przy użyciu modelu osoby.

Wszelkie nowe zmiany wprowadzane do twarzy i rozpoznawane przez Ciebie w filmie wideo zostaną zapisane w modelu osoby, który został użyty do ponownego indeksowania wideo.

## <a name="managing-people-in-your-videos"></a>Zarządzanie osobami w wideo

Można zarządzać wykrytymi powierzchniami i osobami, które zostały rozpoznane w filmach wideo w celu edytowania i usuwania twarzy.

Usunięcie kroju i usunięcie konkretnej z wglądu w dane wideo.

Edytowanie kroju, zmienia nazwę wykrytej i może zostać rozpoznany w filmie wideo. Gdy edytujesz miarę w filmie wideo, ta nazwa jest zapisywana jako wpis osoby w modelu osoby, który został przypisany do filmu wideo podczas przekazywania i indeksowania.

Jeśli model osoby nie zostanie przypisany do wideo podczas przekazywania, Edycja zostanie zapisana w domyślnym modelu osoby Twojego konta.

### <a name="edit-a-face"></a>Edytowanie kroju


> [!NOTE]
> Jeśli model osoby ma dwie lub więcej różnych osób o tej samej nazwie, nie będzie można oznaczyć tej nazwy w klipach wideo korzystających z tego modelu osoby. Można wprowadzać zmiany tylko dla osób, które współużytkują tę nazwę, na karcie osoby na stronie dostosowywania modelu zawartości w Video Indexer. Z tego powodu zaleca się nadawanie unikatowych nazw każdej osobie w modelu osoby.

1. Przejdź do witryny sieci Web Video Indexer i zaloguj się.
1. Wyszukaj wideo, które chcesz wyświetlić i edytować na swoim koncie.
1. Aby edytować swoją część wideo, przejdź do karty szczegółowe informacje i kliknij ikonę ołówka w prawym górnym rogu okna.

    ![Edytowanie kroju obrazu w filmie wideo](./media/customize-face-model/edit-face.png)
1. Kliknij dowolną z wykrytych twarzy i zmień ich nazwy z "nieznane #X" (lub nazwę, która została wcześniej przypisana do twarzy). 
1. Po wpisaniu nowej nazwy kliknij ikonę wyboru obok nowej nazwy. Spowoduje to zapisanie nowej nazwy i rozpoznanie wszystkich wystąpień tej wartości w innych bieżących filmach wideo oraz w przyszłych nagraniach wideo. Wprowadzenie zmian w innych bieżących filmach wideo może zająć trochę czasu, ponieważ jest to proces wsadowy.

Jeśli nazwa użytkownika jest nazwą istniejącej osoby w modelu osoby, z której korzysta wideo, spowoduje to scalenie wykrytych obrazów czołowych z tego filmu wideo tej osoby z tym, co już istnieje w modelu. Jeśli zmienisz nazwę na nową nazwę, spowoduje to utworzenie nowego wpisu osoby w modelu osoby, którego używa wideo. 

    ![Edit a face in your video](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>Usuwanie kroju

Aby usunąć wykrytą część wideo, przejdź do okienka szczegółowe informacje i kliknij ikonę ołówka w prawym górnym rogu okienka. Kliknij opcję Usuń poniżej nazwy kroju. Spowoduje to usunięcie wykrytej czcionki z filmu wideo. Powierzchnie osoby będą nadal wykrywane w innych filmach wideo, w których są wyświetlane, ale można je również usunąć z tych filmów. Osoba, jeśli ma nazwę, również nadal istnieje w modelu osoby, który został użyty do indeksowania filmu wideo, z którego została usunięta, chyba że użytkownik usunie osobę z modelu osoby.

![Usuwanie kroju obrazu wideo](./media/customize-face-model/delete-face.png)


## <a name="next-steps"></a>Następne kroki

[Dostosuj model osoby przy użyciu interfejsów API](customize-person-model-with-api.md)
