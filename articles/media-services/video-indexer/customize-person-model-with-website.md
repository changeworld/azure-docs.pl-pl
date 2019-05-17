---
title: Umożliwia dostosowywanie modelu osoby — Azure Video Indexer witryny sieci Web
titlesuffix: Azure Media Services
description: W tym artykule pokazano, jak dostosować model osoby z witryną internetową Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 37e556ca458a5b0a171664e089d39cfb448d5f96
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800114"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Dostosuj model osoby, z witryną internetową indeksatora wideo

Indeksator wideo obsługuje rozpoznawanie osobistości dla zawartości wideo. Funkcja rozpoznawania osobistości obejmuje około jeden milion twarze na podstawie źródła najczęściej żądanych danych, takich jak ZAUFANI Wikipedia i górnym specjaliści LinkedIn. Aby uzyskać szczegółowym omówieniem, zobacz [Dostosowywanie modelu osoby w Video Indexer](customize-person-model-overview.md).

Video Indexer witryny sieci Web służy do edytowania twarzy, które zostały wykryte w filmach wideo, zgodnie z opisem w tym temacie. Możesz również użyć interfejsu API, zgodnie z opisem w [dostosowania modelu osoba, za pomocą interfejsów API](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>Centralne zarządzanie osoby modeli w ramach Twojego konta

1. Aby wyświetlić, edytować, usuwać modele osoby na Twoim koncie, przejdź do witryny sieci Web Video Indexer i zaloguj się.
2. Kliknij przycisk dostosowania modelu zawartości w prawym górnym rogu strony.

    ![Dostosowywanie modelu zawartości](./media/customize-face-model/content-model-customization.png)
3. Wybierz kartę osób.

    Zobaczysz modelu domyślne osoby na Twoim koncie. Model domyślnie osoby przechowuje wszystkie twarzy mógł edytować lub zmienione w szczegółowe informacje dotyczące filmów wideo, dla których nie określono modelu niestandardowego osoby podczas indeksowania. 

    Jeśli utworzono inne modele osoby, zostaną wyświetlone na tej stronie, jak również. 

    ![Dostosowywanie modelu zawartości](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>Utwórz nowy model osoby

1. Kliknij przycisk **+ Dodaj modelu** przycisku.

    ![Dodawanie nowej osoby](./media/customize-face-model/add-new-person.png)
2. Wprowadź nazwę modelu, a następnie kliknij przycisk wyboru obok nazwy.

    ![Dodawanie nowej osoby](./media/customize-face-model/add-new-person2.png)

    Utworzono nowy model osoby. Możesz teraz dodać twarzy do nowego modelu osoby.
3. Kliknij przycisk menu listy i wybierz **+ Dodaj osobę**.

    ![Dodawanie nowej osoby](./media/customize-face-model/add-new-person3.png)
    
## <a name="add-a-new-person-to-a-person-model"></a>Dodawanie nowej osoby do modelu osoby

> [!NOTE]
> Usługa Video Indexer umożliwia dodawanie wielu osób o takiej samej nazwie w modelu osoby. Jednak zalecane jest nadawana unque nazw do każdej osoby w modelu użyteczność i przejrzystości.

1. Dodawanie nowej twarzy do modelu osoby, kliknij przycisk menu listy obok model osoby, który chcesz dodać twarzy do.
1. Kliknij przycisk **+ Dodaj osobę** z menu.

    ![Dodawanie nowej twarzy do osoby](./media/customize-face-model/add-new-face.png)
 
    Okno podręczne wyświetli monit Wypełnij szczegóły tej osoby. Wpisz nazwę osoby, a następnie kliknij przycisk wyboru.

    ![Dodawanie nowej twarzy do osoby](./media/customize-face-model/add-new-face2.png)
 
Możesz następnie wybrać w Eksploratorze plików lub przeciągnij i upuść obrazy twarzy powierzchni. Usługa Video Indexer spowoduje przejście wszystkich standardowy obraz typów plików (np: JPG, PNG i inne).

Usługa Video Indexer powinno być możliwe wykrywanie wystąpienia tej osobie w przyszłości filmach wideo, indeksu możesz i aktualnych filmów wideo, które można było już indeksowane, za pomocą modelu osoby, do którego dodano ten nowej twarzy do. Rozpoznawanie osoby w Twojej aktualnych filmów wideo może trochę potrwać zaczęły obowiązywać, ponieważ jest to proces usługi batch.


## <a name="rename-a-person-model"></a>Zmień nazwę modelu osoby

Na Twoim koncie, w tym modelu osoby domyślne można zmienić nazwę dowolnego modelu osoby. Nawet jeśli zmienisz domyślny model osoby, nadal będzie służyć jako domyślny model osoby na Twoim koncie.

1. Kliknij przycisk menu listy obok model osoby, który chcesz zmienić.
2. Kliknij przycisk **Zmień nazwę** z menu.

    ![Zmień nazwę osoby](./media/customize-face-model/rename-person.png)
3. Kliknij nazwę bieżącego modelu, a następnie wpisz nazwę nowego.

    ![Zmień nazwę osoby](./media/customize-face-model/rename-person2.png)
4. Kliknij przycisk wyboru dla modelu ma mieć zmienioną nazwę.

## <a name="delete-a-person-model"></a>Usuń model osoby

Możesz usunąć dowolnego modelu osoby, który został utworzony w ramach Twojego konta. Jednak nie można usunąć domyślnego modelu osoby.

1. Kliknij przycisk **Usuń** z menu.

    ![Usuń osoby](./media/customize-face-model/delete-person.png)
    
    Okno podręczne będą wyświetlane i informujące o tym, że ta akcja spowoduje usunięcie modelu osoby i wszystkie osoby i pliki, które zawiera. Tej akcji nie można cofnąć. 

    ![Usuń osoby](./media/customize-face-model/delete-person2.png)
1. Jeśli wiesz, ponownie kliknij przycisk Usuń.

> [!NOTE]
> Istniejące pliki wideo, które są indeksowane, za pomocą modelu osoby (usuniętego) nie obsługuje możliwość zaktualizowania nazwy twarzy, które pojawiają się w trakcie filmu wideo. Będzie można edytować nazwy twarzy w tych filmach wideo, tylko wtedy, gdy jest to ponowna indeksacja je przy użyciu innego modelu osoby. Jeśli ponowna indeksacja się bez określania modelu osoby, będzie używany domyślny model. 

## <a name="manage-existing-people-in-a-person-model"></a>Zarządzanie osobami istniejących w modelu osoby

Aby wyświetlić zawartość dowolnego zestawu swoje modele, osoby, kliknij strzałkę obok nazwy modelu osoby.
Z listy rozwijanej pokazuje wszystkie osoby w modelu osoby. Po kliknięciu przycisku menu listy obok każdego osób, zobacz Zarządzanie, zmiana nazwy i usuwanie opcji.  

![Dodawanie nowej twarzy do osoby](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Zmień nazwę osoby

1. Aby zmienić nazwę osoby w modelu osoby, kliknij przycisk menu listy, a następnie wybierz **Zmień nazwę** menu listy.
1. Kliknij nazwę bieżącej osoby i wpisz nazwę nowego.
1. Kliknij przycisk wyboru, a osoba zostanie zmieniona.

### <a name="delete-a-person"></a>Usuń osoby

1. Aby usunąć osoby z modelu osoby, kliknij przycisk menu listy, a następnie wybierz **Usuń** menu listy.
1. Wyświetla komunikat informujący o tym, że ta akcja spowoduje usunięcie osoby i że tej akcji nie można cofnąć.
1. Ponownie kliknij przycisk Usuń i osoby zostanie usunięty z modelu osoby.

### <a name="manage-a-person"></a>Zarządzanie osoby

Po kliknięciu **Zarządzaj**, zobacz powierzchnie, które jest jest uczony ten model osoby z. Te powierzchnie pochodzą z wystąpieniami tej osobie w filmach wideo, używające tego modelu osoby lub z obrazów przekazanych ręcznie. 

Możesz dodać więcej twarzy do osoby, klikając przycisk Dodaj obrazów.

W okienku Zarządzanie można użyć, aby zmienić nazwę osoby oraz usuwać osoby z modelu osoby.

## <a name="use-a-person-model-to-index-a-video"></a>Indeks wideo przy użyciu modelu osoby

Można użyć modelu osoby do indeksowania nowy film wideo, przypisując modelu osoby podczas przekazywania filmu wideo.

Aby używać modelu osoby na nowy film wideo, wykonaj następujące czynności:

1. Kliknij przycisk **przekazywanie** przycisku w górnej części strony.

    ![Przekazanie](./media/customize-face-model/upload.png)
1. Upuść plik wideo w okręgu lub Przeglądaj w poszukiwaniu pliku.
1. Kliknij strzałkę opcje zaawansowane.

    ![Przekazanie](./media/customize-face-model/upload2.png)
1. Kliknij listę rozwijaną i wybierz utworzony przez siebie model osoby.

    ![Przekazanie](./media/customize-face-model/upload3.png)
1. Kliknij przycisk opcji przekazywania w dolnej części strony i nowy film wideo będzie indeksowany przy użyciu modelu osoby.

Jeśli nie określisz modelu osoby w trakcie przekazywania, Video Indexer będzie indeks wideo przy użyciu modelu domyślne osoby na Twoim koncie.

## <a name="use-a-person-model-to-reindex-a-video"></a>Ponowna indeksacja wideo przy użyciu modelu osoby 

Aby użyć modelu osoby do ponownego poindeksowania danych wideo w kolekcji, przejdź do konta filmów wideo na stronie głównej Video Indexer i umieść kursor na nazwie film wideo, który ma być ponowna indeksacja.

Zostanie wyświetlony umożliwiające edytowanie, usuwanie i ponowna indeksacja filmu wideo. 

1. Kliknij opcję ponownego poindeksowania danych filmu wideo.

    ![Ponowna indeksacja](./media/customize-face-model/reindex.png)

    Teraz można wybrać model osoby do ponownego poindeksowania danych wideo.
1. Kliknij listę rozwijaną i wybierz model osoby, do którego chcesz użyć. 

    ![Ponowna indeksacja](./media/customize-face-model/reindex2.png)

1. Kliknij przycisk **ponowna indeksacja** przycisk, a Twój film wideo będzie można ponownie indeksowane przy użyciu modelu osoby.

Nowe zmiany wprowadzone do powierzchni wykryte i rozpoznawane w trakcie filmu wideo, który po prostu ponownie indeksowane zostaną zapisane w modelu osoby, użytego w celu ponownego poindeksowania danych wideo.

## <a name="managing-people-in-your-videos"></a>Zajmuje się osobami w filmach wideo

Możesz zarządzać twarzy, które są wykrywane i osób, które zostały rozpoznane pod kątem filmów wideo, indeksowanie, edytowanie i usuwanie twarzy.

Usuwanie twarzy, usuwa określone rozpoznawania twarzy z szczegółowe informacje dotyczące filmu wideo.

Edytowanie twarz, zmienia nazwę krój, który jest wykrywany i prawdopodobnie są rozpoznawane w wideo. Podczas edytowania twarzy w wideo, takiej nazwie zostanie zapisany jako wpis osoby w modelu osoby, który został przypisany do wideo podczas przekazywania i indeksowania.

Jeśli nie zostanie przypisany modelu osoby do pliku wideo podczas przekazywania, edycji zostanie zapisany w modelu osoby domyślne Twoje konto.

### <a name="edit-a-face"></a>Edytuj twarzy


> [!NOTE]
> Jeśli model osoba ma co najmniej dwóch różnych osób o takiej samej nazwie, nie można tag tej nazwie w filmach wideo, korzystające z modelu osoby. Tylko można wprowadzić zmiany do osób, które współużytkują tę nazwę w karcie osoby strony dostosowania modelu zawartości w Video Indexer. Z tego powodu zaleca się nadanie unikatowych nazw do każdej osoby w modelu osoby.

1. Przejdź do witryny sieci Web Video Indexer i zaloguj się.
1. Wyszukiwanie wideo, który chcesz wyświetlić i edytować na Twoim koncie.
1. Aby edytować twarzy w wideo, przejdź na kartę szczegółowe informacje, a następnie kliknij ikonę ołówka w prawym górnym rogu okna.

    ![Edytuj twarzy w wideo](./media/customize-face-model/edit-face.png)
1. Kliknij dowolny wykryte twarze i zmień ich nazwy "Nieznany #X" (lub nazwę, która wcześniej została przypisana do rozpoznawania twarzy). 
1. Po wpisaniu nową nazwę, kliknij ikonę znacznika wyboru obok nowej nazwy. Zapisuje nową nazwę i rozpoznaje i nazwy wszystkich wystąpień tego twarzy w innych aktualnych filmów wideo i w przyszłości filmów wideo, które można przekazać. Rozpoznawanie twarzy w Twojej bieżącej wideo może trochę potrwać zaczęły obowiązywać, ponieważ jest to proces usługi batch.

W przypadku nazwy twarz o nazwie osoby istniejących w modelu osoby, który używa wideo, to spowoduje scalenie obrazy twarzy wykryte z tej usługi wideo w tej osoby, z czego już istnieje w modelu. Twarzy w przypadku nazwy z całkowicie nową nazwą, spowoduje to utworzenie nowego wpisu osoby w modelu osoby, który używa filmu wideo. 

    ![Edit a face in your video](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>Usuwanie twarzy

Aby usunąć twarz wykryte w wideo, przejdź do okienka szczegółowe informacje i kliknij ikonę ołówka w prawym górnym rogu okienka. Kliknij opcję Usuń pod nazwą powierzchni. Spowoduje to usunięcie które wykryte twarzy z filmu wideo. Twarzy osoby nadal zostanie wykryty w wideo, w których pojawia się, ale możesz usunąć twarz z pliki wideo, a także po zostały zindeksowane. Osoby, jeśli było został nazwany, również będą nadal istnieje w modelu osoby, który został użyty do indeksowania wideo, z którego usunięto twarz o ile nie usuniesz specjalnie osoby z modelu osoby.

![Usuwanie twarzy w wideo](./media/customize-face-model/delete-face.png)


## <a name="next-steps"></a>Kolejne kroki

[Dostosuj model osoby za pomocą interfejsów API](customize-person-model-with-api.md)
