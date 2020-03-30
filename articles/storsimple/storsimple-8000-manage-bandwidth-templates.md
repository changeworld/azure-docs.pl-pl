---
title: Zarządzanie szablonami przepustowości dla serii StorSimple 8000 | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób zarządzania szablonami przepustowości StorSimple, które umożliwiają kontrolowanie zużycia przepustowości.
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
ms.openlocfilehash: 13a3e57bb27c075fc045e87790dbe13369ed9f8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254900"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-bandwidth-templates"></a>Zarządzanie szablonami przepustowości StorSimple za pomocą usługi StorSimple Device Manager

## <a name="overview"></a>Omówienie

Szablony przepustowości umożliwiają skonfigurowanie użycia przepustowości sieci w wielu harmonogramach dotyczących czasu na dzień w celu zseporządkowania danych z urządzenia StorSimple do chmury.

Dzięki harmonogramom ograniczania przepustowości można:

* Określ dostosowane harmonogramy przepustowości w zależności od użycia sieci obciążenia.
* Centralizacja zarządzania i ponownego wykorzystania harmonogramów na wielu urządzeniach w łatwy i bezproblemowy sposób.

> [!NOTE]
> Ta funkcja jest dostępna tylko dla urządzeń fizycznych StorSimple (modele 8100 i 8600), a nie dla urządzeń StorSimple Cloud Appliances (modele 8010 i 8020).


## <a name="the-bandwidth-templates-blade"></a>Blok Szablony przepustowości

Blok **Szablony przepustowości** zawiera wszystkie szablony przepustowości dla usługi w formacie tabelarycznym i zawiera następujące informacje:

* **Nazwa** — unikatowa nazwa przypisana do szablonu przepustowości podczas jego tworzenia.
* **Harmonogram** — liczba harmonogramów zawartych w szablonie danej przepustowości.
* **Używane przez** — liczba woluminów przy użyciu szablonów przepustowości.

Dodatkowe informacje ułatwiające konfigurowanie szablonów przepustowości można również znaleźć w:

* [Pytania i odpowiedzi dotyczące szablonów przepustowości](#questions-and-answers-about-bandwidth-templates)
* [Najważniejsze wskazówki dotyczące szablonów przepustowości](#best-practices-for-bandwidth-templates)

## <a name="add-a-bandwidth-template"></a>Dodawanie szablonu przepustowości

Wykonaj następujące kroki, aby utworzyć nowy szablon przepustowości.

#### <a name="to-add-a-bandwidth-template"></a>Aby dodać szablon przepustowości

1. Przejdź do usługi StorSimple Device Manager, kliknij pozycję **Szablony przepustowości,** a następnie kliknij pozycję **+ Dodaj szablon Przepustowość**.

    ![Kliknij + Dodaj szablon przepustowości](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp1.png)

2. W bloku **Dodaj szablon przepustowości** wykonaj następujące czynności:
   
    1. Określ unikatową nazwę szablonu przepustowości.
    2. Zdefiniuj harmonogram przepustowości. Aby utworzyć harmonogram:
   
        1. Z listy rozwijanej wybierz **dni** tygodnia, dla które jest skonfigurowany harmonogram. Można wybrać wiele dni.        
        
        2. Wprowadź **godzinę rozpoczęcia** w formacie _hh:mm._ To jest, gdy rozpocznie się harmonogram.

        3. Wprowadź **godzinę zakończenia** w formacie _hh:mm._ To jest, gdy harmonogram zostanie zatrzymany.
      
           > [!NOTE]
           > Nakładające się harmonogramy nie są dozwolone. Jeśli godziny rozpoczęcia i zakończenia spowoduje nakładający się harmonogram, zostanie wyświetlony komunikat o błędzie w tym celu.

        4. Określ **szybkość przepustowości**. Jest to przepustowość w megabitach na sekundę (Mb/s) używana przez urządzenie StorSimple w operacjach związanych z chmurą (zarówno przesyłanych, jak i pobieranych plików). W tym polu podaj liczbę z zakresu od 1 do 1000.

            ![Definiowanie harmonogramu przepustowości](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp2.png)
         
            Powtórz powyższe kroki, aby zdefiniować wiele harmonogramów dla szablonu, dopóki nie zostaniesz gotowy.

        5. Kliknij **przycisk Dodaj,** aby rozpocząć tworzenie szablonu przepustowości. Utworzony szablon zostanie dodany do listy szablonów przepustowości.
      

## <a name="edit-a-bandwidth-template"></a>Edytowanie szablonu przepustowości

Wykonaj następujące czynności, aby edytować szablon przepustowości.

### <a name="to-edit-a-bandwidth-template"></a>Aby edytować szablon przepustowości

1. Przejdź do usługi StorSimple Device Manager i kliknij pozycję **Szablony przepustowości**.
2. Na liście szablonów przepustowości wybierz szablon, który chcesz usunąć. Kliknij prawym przyciskiem myszy i z menu kontekstowego wybierz polecenie **Usuń**.
3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **OK**. Powinno to usunąć szablon przepustowości. 
4. Lista szablonów przepustowości jest aktualizowana w celu odzwierciedlenia usunięcia.

> [!NOTE]
> Nie można zapisać zmian, jeśli edytowany harmonogram nakłada się na istniejący harmonogram w modyfikowanym szablonie przepustowości.

## <a name="delete-a-bandwidth-template"></a>Usuwanie szablonu przepustowości

Wykonaj następujące czynności, aby usunąć szablon przepustowości.

#### <a name="to-delete-a-bandwidth-template"></a>Aby usunąć szablon przepustowości

1. Przejdź do usługi StorSimple Device Manager i kliknij pozycję **Szablony przepustowości**.
2. Na liście szablonów przepustowości wybierz szablon, który chcesz usunąć. Kliknij prawym przyciskiem myszy i z menu kontekstowego wybierz polecenie Usuń.
3. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **OK**. Powinno to usunąć szablon przepustowości.
4. Lista szablonów przepustowości jest aktualizowana w celu odzwierciedlenia usunięcia.

Jeśli szablon jest używany przez jakiekolwiek woluminy, nie będzie można go usunąć. Zostanie wyświetlony komunikat o błędzie wskazujący, że szablon jest w użyciu. Pojawi się okno dialogowe komunikat o błędzie z informacją, że wszystkie odwołania do szablonu powinny zostać usunięte.

Można usunąć wszystkie odwołania do szablonu, uzyskując dostęp do strony **Kontenery woluminów** i modyfikując kontenery woluminów, które używają tego szablonu, tak aby używały innego szablonu lub używały niestandardowego lub nieograniczonego ustawienia przepustowości. Po usunięciu wszystkich odwołań można usunąć szablon.

## <a name="use-a-default-bandwidth-template"></a>Używanie domyślnego szablonu przepustowości

Domyślny szablon przepustowości jest dostarczany i jest domyślnie używany przez kontenery woluminów do wymuszania kontroli przepustowości podczas uzyskiwania dostępu do chmury. Szablon domyślny służy również jako gotowe odwołanie dla użytkowników, którzy tworzą własne szablony. Szczegóły tego szablonu domyślnego to:

* **Nazwa** – Nieograniczone noce i weekendy
* **Harmonogram** — pojedynczy harmonogram od poniedziałku do piątku, który stosuje przepustowość 1 Mb/s między 8:00 a 17:00 czasu urządzenia. Przepustowość jest ustawiona na Nieograniczony do końca tygodnia.

Szablon domyślny można edytować. Użycie tego szablonu (w tym edytowanych wersji) jest śledzone.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Tworzenie caedytowego szablonu przepustowości, który rozpoczyna się o określonej godzinie

Wykonaj tę procedurę, aby utworzyć harmonogram, który rozpoczyna się o określonej godzinie i działa przez cały dzień. W tym przykładzie harmonogram rozpoczyna się o godzinie 9:00 rano i trwa do 9:00 następnego dnia rano. Należy pamiętać, że godziny rozpoczęcia i zakończenia dla danego harmonogramu muszą być zawarte w tym samym harmonogramie 24-godzinnym i nie mogą obejmować wielu dni. Jeśli chcesz skonfigurować szablony przepustowości, które obejmują wiele dni, należy użyć wielu harmonogramów (jak pokazano w przykładzie).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Aby utworzyć cais dniowy szablon przepustowości

1. Utwórz harmonogram, który rozpoczyna się o godzinie 9:00 rano i trwa do północy.
2. Dodaj inny harmonogram. Skonfiguruj drugi harmonogram, który będzie działał od północy do 9:00 rano.
3. Zapisz szablon przepustowości.

Harmonogram złożony rozpocznie się w wybranym czasie i będzie działał przez cały dzień.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Pytania i odpowiedzi dotyczące szablonów przepustowości

**Q**. Co się stanie z formantami przepustowości, gdy znajdujesz się między harmonogramami? (Harmonogram został zakończony, a kolejny jeszcze się nie rozpoczął).

**A**. W takich przypadkach nie zostaną zastosowane żadne kontrole przepustowości. Oznacza to, że urządzenie może korzystać z nieograniczonej przepustowości podczas warstwowania danych do chmury.

**Q**. Czy można modyfikować szablony przepustowości na urządzeniu offline?

**A**. Nie będzie można modyfikować szablonów przepustowości w kontenerach woluminów, jeśli odpowiednie urządzenie jest w trybie offline.

**Q**. Czy można edytować szablon przepustowości skojarzony z kontenerem woluminów, gdy skojarzone woluminy są w trybie offline?

**A**. Można zmodyfikować szablon przepustowości skojarzony z kontenerem woluminów, którego woluminy są w trybie offline. Należy zauważyć, że gdy woluminy są w trybie offline, żadne dane nie będą warstwowe z urządzenia do chmury.

**Q**. Czy można usunąć szablon domyślny?

**A**. Chociaż można usunąć szablon domyślny, nie jest to dobry pomysł, aby to zrobić. Śledzenie użycia szablonu domyślnego, w tym edytowanych wersji, jest śledzone. Dane śledzenia są analizowane i z biegiem czasu, służy do poprawy domyślnego szablonu.

**Q**. Jak ustalić, że szablony przepustowości muszą zostać zmodyfikowane?

**A**. Jednym ze znaków, które należy zmodyfikować szablony przepustowości jest, gdy zaczniesz widzieć sieci spowolnić lub dławić wiele razy w ciągu dnia. W takim przypadku należy monitorować sieć magazynowania i użycia, patrząc na wykresy wydajności we/wy i przepustowość sieci.

Na podstawie danych przepływności sieci należy zidentyfikować porę dnia i kontenery woluminów, w których występuje wąskie gardło sieci. Jeśli tak się stanie, gdy dane są warstwowe do chmury (uzyskać te informacje z wydajności we/wy dla wszystkich kontenerów woluminów dla urządzenia do chmury), należy zmodyfikować szablony przepustowości skojarzone z kontenerów woluminów.

Po zmodyfikowanych szablonów są w użyciu, należy ponownie monitorować sieć dla znaczących opóźnień. Jeśli te nadal istnieją, należy ponownie odwiedzić szablony przepustowości.

**Q**. Co się stanie, jeśli wiele kontenerów woluminów na moim urządzeniu ma harmonogramy, które nakładają się na siebie, ale do każdego z nich mają zastosowanie różne limity?

**A**. Załóżmy, że masz urządzenie z kontenerami 3 woluminów. Harmonogramy skojarzone z tymi kontenerami całkowicie nakładają się na siebie. Dla każdego z tych kontenerów limity przepustowości są 5, 10 i 15 Mbps odpowiednio. Gdy we/wy występują na wszystkich tych kontenerów w tym samym czasie, minimum 3 limity przepustowości mogą być stosowane: w tym przypadku 5 Mbps, jak te wychodzące żądania we/wy współużytkują tę samą kolejkę.

## <a name="best-practices-for-bandwidth-templates"></a>Najważniejsze wskazówki dotyczące szablonów przepustowości

Postępuj zgodnie z tymi najlepszymi rozwiązaniami dla urządzenia StorSimple:

* Skonfiguruj szablony przepustowości na urządzeniu, aby włączyć zmienne ograniczanie przepustowości sieci przez urządzenie o różnych porach dnia. Te szablony przepustowości używane z harmonogramami tworzenia kopii zapasowych mogą skutecznie wykorzystywać dodatkową przepustowość sieci do operacji w chmurze poza godzinami szczytu.
* Oblicz rzeczywistą przepustowość wymaganą dla określonego wdrożenia na podstawie rozmiaru wdrożenia i wymaganego celu czasu odzyskiwania (RTO).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [administrowaniu urządzeniem StorSimple za pomocą usługi StorSimple Device Manager.](storsimple-8000-manager-service-administration.md)

