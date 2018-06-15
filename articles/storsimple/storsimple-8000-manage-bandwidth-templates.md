---
title: Zarządzaj szablonami przepustowości dla serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Opisuje sposób zarządzania StorSimple przepustowości szablonów, które umożliwiają kontrolowanie użycia przepustowości.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 50d0a920bef097013feddc828d2c37133b9057b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875074"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-bandwidth-templates"></a>Zarządzaj szablonami przepustowości StorSimple przy użyciu usługi Menedżer StorSimple urządzenia

## <a name="overview"></a>Omówienie

Szablony przepustowości umożliwiają konfigurowanie użycia przepustowości sieci między kilka harmonogramów czasu dnia do warstwy danych z urządzenia StorSimple w chmurze.

Harmonogramy ograniczania przepustowości można:

* Określ harmonogramy niestandardowych przepustowości w zależności od użycia sieci obciążenia.
* Scentralizowane zarządzanie i ponowne użycie harmonogramy na wielu urządzeniach w sposób łatwy i bezproblemowe.

> [!NOTE]
> Ta funkcja jest dostępna tylko w przypadku urządzenia fizycznego StorSimple (modele 8100 i 8600), a nie urządzenia chmury StorSimple (modele urządzenia 8010 i 8020).


## <a name="the-bandwidth-templates-blade"></a>Blok szablony przepustowości

**Szablony przepustowości** bloku ma wszystkie szablony przepustowości dla usługi w formacie tabelarycznym i zawiera następujące informacje:

* **Nazwa** — nazwa przypisana do szablonu przepustowości podczas jej tworzenia.
* **Harmonogram** — liczba harmonogramów zawarte w szablonie danego przepustowości.
* **Używane przez** — liczba woluminów za pomocą szablonów przepustowości.

Można również znaleźć dodatkowe informacje ułatwiające konfigurowanie szablonów przepustowości w:

* [Pytania i odpowiedzi dotyczące przepustowości szablonów](#questions-and-answers-about-bandwidth-templates)
* [Najlepsze rozwiązania dotyczące przepustowości szablonów](#best-practices-for-bandwidth-templates)

## <a name="add-a-bandwidth-template"></a>Dodaj szablon przepustowości

Wykonaj poniższe kroki, aby utworzyć nowy szablon przepustowości.

#### <a name="to-add-a-bandwidth-template"></a>Aby dodać szablon przepustowości

1. Przejdź do usługi Menedżer urządzeń StorSimple, kliknij przycisk **szablony przepustowości** , a następnie kliknij przycisk **+ Dodaj przepustowości szablonu**.

    ![Kliknij pozycję + Dodaj szablon przepustowości](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp1.png)

2. W **Dodaj szablon przepustowości** blok, wykonaj następujące czynności:
   
    1. Określ unikatową nazwę dla szablonu przepustowości.
    2. Zdefiniuj harmonogram przepustowości. Aby utworzyć harmonogram:
   
        1. Z listy rozwijanej wybierz **dni** tygodnia harmonogramu jest skonfigurowany dla. Można wybrać kilka dni.        
        
        2. Wprowadź **czas rozpoczęcia** w _gg: mm_ format. Jest to, gdy rozpocznie się harmonogramu.

        3. Wprowadź **czas zakończenia** w _gg: mm_ format. Jest to harmonogram zostanie zatrzymane.
      
           > [!NOTE]
           > Nakładające się harmonogramy nie są dozwolone. Czas rozpoczęcia i zakończenia spowoduje nakładające się harmonogramu, pojawi się odpowiedni komunikat o błędzie.

        4. Określ **szybkość przepustowości**. Jest to przepustowość w megabitach na sekundę (MB/s) używany przez urządzenia StorSimple w operacji dotyczących chmury (przekazywania i pobierania). Wprowadź liczbę z zakresu od 1 do 1000 dla tego pola.

            ![Zdefiniuj harmonogram przepustowości](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp2.png)
         
            Powtórz powyższe kroki, aby zdefiniować wiele harmonogramów szablonu, aż wszystko będzie gotowe.

        5. Kliknij przycisk **Dodaj** aby rozpocząć tworzenie szablonu przepustowości. Utworzony szablon jest dodawany do listy szablonów przepustowości.
      

## <a name="edit-a-bandwidth-template"></a>Edytuj szablon przepustowości

Wykonaj poniższe kroki, aby edytować szablon przepustowości.

### <a name="to-edit-a-bandwidth-template"></a>Aby edytować szablon przepustowości

1. Przejdź do Menedżera urządzenia StorSimple usługi, a następnie kliknij przycisk **szablony przepustowości**.
2. Na liście szablonów przepustowości wybierz szablon, który chcesz usunąć. Kliknij prawym przyciskiem myszy i wybierz z menu kontekstowego **usunąć**.
3. Po wyświetleniu monitu o potwierdzenie, kliknij przycisk **OK**. To powinien usunąć szablon przepustowości. 
4. Lista aktualizacji szablonów przepustowości do uwzględnienia usunięcia.

> [!NOTE]
> Nie można zapisać zmiany, jeśli edytowanych harmonogramu pokrywa się z istniejącego harmonogramu modyfikowanego szablonu przepustowości.

## <a name="delete-a-bandwidth-template"></a>Usuń szablon przepustowości

Wykonaj poniższe kroki, aby usunąć szablon przepustowości.

#### <a name="to-delete-a-bandwidth-template"></a>Aby usunąć szablon przepustowości

1. Przejdź do Menedżera urządzenia StorSimple usługi, a następnie kliknij przycisk **szablony przepustowości**.
2. Na liście szablonów przepustowości wybierz szablon, który chcesz usunąć. Kliknij prawym przyciskiem myszy i z menu kontekstowego wybierz polecenie Usuń.
3. Po wyświetleniu monitu o potwierdzenie, kliknij przycisk **OK**. To powinien usunąć szablon przepustowości.
4. Lista aktualizacji szablonów przepustowości do uwzględnienia usunięcia.

Jeśli szablon jest używany przez wszystkie woluminy, nie będzie można go usunąć. Pojawi się komunikat o błędzie wskazujący, że szablon jest używany. Zostanie wyświetlone okno dialogowe komunikat błędu, udzielanie porad należy usunąć wszystkie odwołania do szablonu.

Możesz usunąć wszystkie odwołania do szablonu po zalogowaniu się do **kontenery woluminów** strony i modyfikowanie kontenery woluminów, które użyć tego szablonu, aby użyć innego szablonu lub użyć ustawienia niestandardowe lub nieograniczone przepustowości. Po usunięciu wszystkich odwołań, można usunąć szablonu.

## <a name="use-a-default-bandwidth-template"></a>Użyj domyślnego szablonu przepustowości

Domyślny szablon przepustowości podano i jest używany przez kontenery woluminów domyślnie w celu wymuszenia kontroli przepustowości podczas uzyskiwania dostępu do chmury. Domyślny szablon służy również jako punkt odniesienia gotowy dla użytkowników, którzy tworzyć własne szablony. Szczegóły tego szablonu domyślnego są:

* **Nazwa** — w nocy nieograniczone, jak i w weekendy
* **Harmonogram** — jeden harmonogram od poniedziałku do piątku stosowanym współczynnika przepustowości 1 MB/s miedzy 8 a 17: 00 czasu urządzenia. W pozostałej części tygodnia wynosi przepustowości bez ograniczeń.

Można edytować szablon domyślny. Użycie tego szablonu (w tym wersje edytowanych) są śledzone.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Tworzenie szablonu przepustowości całodzienne, która rozpoczyna się od określonego czasu

Wykonaj tę procedurę, aby utworzyć harmonogram, który rozpoczyna się od określonego czasu i uruchamia cały dzień. W tym przykładzie harmonogram rozpoczyna się od 9 AM w nocy i uruchamia do 9 AM następnego dnia rano. Należy pamiętać, że godziny rozpoczęcia i zakończenia dla danego harmonogramu muszą być jednocześnie zawarte na ten sam harmonogram 24-godzinnym i nie może obejmować wiele dni. Jeśli trzeba zdefiniować szablony przepustowości, obejmujące wiele dni, należy użyć wielu harmonogramów (jak pokazano w przykładzie).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Aby utworzyć szablon przepustowości całodzienne

1. Utwórz harmonogram, który rozpoczyna się od 9 AM w nocy i uruchamia do północy.
2. Dodaj inny harmonogram. Skonfiguruj drugi harmonogram wykonywania od północy do 9 AM w nocy.
3. Zapisz szablon przepustowości.

Harmonogram złożony będą uruchomione w czasie wybrane i uruchom całodzienne.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Pytania i odpowiedzi dotyczące przepustowości szablonów

**Q**. Co się dzieje kontroli przepustowości podczas pracy Between harmonogramy? (Zakończył się zgodnie z harmonogramem i innego nie została jeszcze uruchomiona.)

**A**. W takich przypadkach zostanie zastosowane bez kontroli przepustowości. Oznacza to, że urządzenie może używać nieograniczone przepustowości podczas tworzenia warstw danych w chmurze.

**Q**. Czy możesz zmodyfikować szablony przepustowość na urządzeniu w trybie offline?

**A**. Nie można zmodyfikować szablony przepustowość na kontenery woluminów, jeśli odpowiednie urządzenie jest w trybie offline.

**Q**. Możesz edytować szablon przepustowości skojarzone z kontenera woluminów, gdy skojarzone woluminy są w trybie offline

**A**. Można zmodyfikować szablonu przepustowości skojarzone z kontenera woluminów, których woluminy są w trybie offline. Należy pamiętać, że gdy woluminy są w trybie offline, żadne dane nie będą należeć do warstwy z urządzenia do chmury.

**Q**. Można usunąć domyślny szablon?

**A**. Mimo że można usunąć szablonu domyślnego, nie jest dobrym rozwiązaniem, aby to zrobić. Użycie szablonu domyślnego, tym edytowanych wersje są śledzone. Dane śledzenia są analizowane i w ciągu godziny jest używany do zwiększenia domyślnego szablonu.

**Q**. Jaki sposób można określić, czy szablony przepustowości muszą zostać zmodyfikowane?

**A**. Jest jednym ze znaków, należy zmodyfikować szablony przepustowości podczas uruchamiania wyświetlać sieci działa wolno lub podlewka wiele razy w ciągu jednego dnia. W takim przypadku monitorowania sieci magazynu i użycia, analizując wykresy wydajności operacji We/Wy i przepustowość sieci.

Z danych przepustowość sieci należy zidentyfikować godzina i kontenery woluminów, w których występuje wąskich gardeł. Jeśli dzieje, gdy jest on warstwowa danych do chmury (uzyskać te informacje z wydajność We/Wy dla wszystkich kontenerów woluminu dla urządzenia do chmury), a następnie należy zmodyfikować szablony przepustowości skojarzone z kontenerów woluminu.

Po modyfikacji szablony są używane, należy monitorować ponownie znaczne opóźnienia sieci. Jeśli te nadal istnieje, następnie konieczne będzie ponowne przeanalizowanie szablonów przepustowości.

**Q**. Co się stanie, jeśli ma wiele kontenerów wolumin w urządzeniu planuje tego nakładają się na siebie, ale różne limity mają zastosowanie do każdej?

**A**. Załóżmy, że masz urządzenie z 3 kontenery woluminów. Harmonogramy skojarzone z tych kontenerów całkowicie nakładają się. Dla każdego z tych kontenerów limity przepustowości, używane są 5, 10 lub 15 MB/s odpowiednio. Podczas operacji We/Wy występujących w wszystkie kontenery w tym samym czasie, co najmniej 3 limity przepustowości mogą być stosowane: w tym przypadku 5 MB/s one wychodzących żądań We/Wy udostępnić tę samą kolejkę.

## <a name="best-practices-for-bandwidth-templates"></a>Najlepsze rozwiązania dotyczące przepustowości szablonów

Należy stosować następujące najlepsze rozwiązania dla urządzenia StorSimple:

* Konfigurowanie szablonów przepustowość na urządzeniu, aby włączyć zmiennej ograniczania przepustowości sieci przez urządzenie o różnych porach dnia. Te szablony przepustowości, gdy jest używany z harmonogramy tworzenia kopii zapasowej można efektywnie wykorzystać dodatkowej przepustowości sieci dla operacji poza godzinami szczytu.
* Oblicz rzeczywistej wymaganej dla konkretnego wdrożenia na podstawie rozmiaru wdrożenia i celu czasu wymaganego odzyskiwania (RTO) przepustowości.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [przy użyciu usługi Menedżer StorSimple urządzenia do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

