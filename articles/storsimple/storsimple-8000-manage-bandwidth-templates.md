---
title: Zarządzanie szablonami przepustowości w przypadku serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Opisuje sposób Zarządzanie szablonami przepustowości StorSimple, które umożliwiają kontrolowanie użycia przepustowości.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699475"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-bandwidth-templates"></a>Zarządzanie szablonami przepustowości StorSimple przy użyciu usługi Menedżer urządzeń StorSimple

## <a name="overview"></a>Omówienie

Szablony przepustowości umożliwiają skonfigurowanie użycia przepustowości sieci między wiele harmonogramów pory dnia do warstwy danych z urządzenia StorSimple w chmurze.

Przy użyciu przepustowości harmonogramy możesz wykonywać następujące czynności:

* Określić harmonogramy dostosowane przepustowości, w zależności od obciążenia użycia sieci.
* Scentralizuj zarządzanie i ponowne użycie harmonogramy na wielu urządzeniach w sposób łatwy i bezproblemowe.

> [!NOTE]
> Ta funkcja jest dostępna tylko w przypadku urządzeń fizycznych StorSimple (modele 8100 i 8600), a nie dla urządzeń StorSimple w chmurze (modele urządzenia 8010 i 8020).


## <a name="the-bandwidth-templates-blade"></a>Blok szablony przepustowości

**Szablony przepustowości** blok zawiera wszystkie szablony przepustowości dla usługi w formacie tabelarycznym i zawiera następujące informacje:

* **Nazwa** — nazwa przypisana do szablonu przepustowości podczas jej tworzenia.
* **Harmonogram** — liczba harmonogramów zawarte w szablonie danej przepustowości.
* **Używane przez** — liczbę woluminów przy użyciu szablonów przepustowości.

Można również znaleźć dodatkowe informacje, aby łatwiej skonfigurować szablony przepustowości:

* [Pytania i odpowiedzi dotyczące szablonami przepustowości](#questions-and-answers-about-bandwidth-templates)
* [Najlepsze rozwiązania dotyczące szablonów przepustowości](#best-practices-for-bandwidth-templates)

## <a name="add-a-bandwidth-template"></a>Dodawanie szablonu przepustowości

Wykonaj poniższe kroki, aby utworzyć nowy szablon przepustowości.

#### <a name="to-add-a-bandwidth-template"></a>Aby dodać szablon przepustowości

1. Przejdź do usługi Menedżer urządzeń StorSimple, kliknij przycisk **szablony przepustowości** a następnie kliknij przycisk **+ szablonu przepustowości Dodaj**.

    ![Kliknij przycisk + Dodaj szablonu przepustowości](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp1.png)

2. W **szablonu przepustowości Dodaj** blok, wykonaj następujące czynności:
   
    1. Określ unikatową nazwę dla szablonu przepustowości.
    2. Zdefiniowanie harmonogramu przepustowości. Aby utworzyć harmonogram:
   
        1. Z listy rozwijanej wybierz **dni** tygodnia harmonogram jest skonfigurowany dla. Możesz wybrać wiele dni.        
        
        2. Wprowadź **czas rozpoczęcia** w _gg: mm_ formatu. Jest to, gdy rozpocznie się harmonogramu.

        3. Wprowadź **czas zakończenia** w _gg: mm_ formatu. Jest to, gdy harmonogram zostanie zatrzymane.
      
           > [!NOTE]
           > Nakładające się harmonogramy nie są dozwolone. Czas rozpoczęcia i zakończenia spowoduje nakładających się harmonogramu, zobaczysz komunikat o błędzie w tym celu.

        4. Określ **przepustowość**. Jest to przepustowość w megabitach na sekundę (MB/s) używany przez urządzenia StorSimple w operacje dotyczące chmury (wysyłanie i pobieranie danych). W tym polu podaj liczbę z zakresu od 1 do 1000.

            ![Zdefiniuj harmonogram przepustowości](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp2.png)
         
            Powtórz powyższe kroki, aby zdefiniować wiele harmonogramów dla szablonu, aż wszystko będzie gotowe.

        5. Kliknij przycisk **Dodaj** aby rozpocząć tworzenie szablonu przepustowości. Utworzony szablon zostanie dodany do listy szablonów przepustowości.
      

## <a name="edit-a-bandwidth-template"></a>Edytuj szablon przepustowości

Wykonaj poniższe kroki, aby edytować szablon przepustowości.

### <a name="to-edit-a-bandwidth-template"></a>Aby edytować szablon przepustowości

1. Przejdź do Menedżera urządzeń StorSimple, usługi, a następnie kliknij przycisk **szablony przepustowości**.
2. Na liście szablony przepustowości wybierz szablon, który chcesz usunąć. Kliknij prawym przyciskiem myszy, a następnie z menu kontekstowego wybierz pozycję **Usuń**.
3. Po wyświetleniu monitu o potwierdzenie, kliknij przycisk **OK**. Powinno to usuwanie szablonu przepustowości. 
4. Lista aktualizacji szablonów przepustowości celu odzwierciedlenia usunięcia.

> [!NOTE]
> Nie można zapisać zmian, jeśli edytowanych harmonogramu pokrywa się z istniejącego harmonogramu w szablonie przepustowości, która jest modyfikowana.

## <a name="delete-a-bandwidth-template"></a>Usuwanie szablonu przepustowości

Wykonaj poniższe kroki, aby usunąć szablon przepustowości.

#### <a name="to-delete-a-bandwidth-template"></a>Aby usunąć szablon przepustowości

1. Przejdź do Menedżera urządzeń StorSimple, usługi, a następnie kliknij przycisk **szablony przepustowości**.
2. Na liście szablony przepustowości wybierz szablon, który chcesz usunąć. Kliknij prawym przyciskiem myszy, a następnie z menu kontekstowego wybierz polecenie Usuń.
3. Po wyświetleniu monitu o potwierdzenie, kliknij przycisk **OK**. Powinno to usuwanie szablonu przepustowości.
4. Lista aktualizacji szablonów przepustowości celu odzwierciedlenia usunięcia.

Jeśli szablon jest używany przez wszystkie woluminy, nie będzie można go usunąć. Pojawi się komunikat o błędzie wskazujący, że szablon jest używany. Pojawi się okno dialogowe komunikat o błędzie wniosku, że należy usunąć wszystkie odwołania do szablonu.

Możesz usunąć wszystkie odwołania do szablonu, uzyskując dostęp do **kontenery woluminów** strony i modyfikowanie kontenerów woluminów, korzystających z tego szablonu, aby użyć innego szablonu, lub użyj ustawienia przepustowości niestandardowych lub wartością nieograniczoną. Po usunięciu wszystkich odwołań, można usunąć szablonu.

## <a name="use-a-default-bandwidth-template"></a>Użyj domyślnego szablonu przepustowości

Domyślny szablon przepustowości znajduje się i jest używane przez kontenery woluminów domyślnie do wymuszania kontroli przepustowości podczas uzyskiwania dostępu do chmury. Domyślny szablon służy również jako gotowe odwołania dla użytkowników, którzy tworzą własne szablony. Szczegóły tego szablonu domyślnego są:

* **Nazwa** — nieograniczone Noce i weekendy
* **Harmonogram** — jeden harmonogram od poniedziałku do piątku, która ma zastosowanie przepustowość 1 MB/s od 8: 00 i 17: 00 czasu urządzenia. Przepustowość jest równa bez ograniczeń na pozostałą część tygodnia.

Można edytować szablonu domyślnego. Użycie tego szablonu (w tym wersje edytowanych) jest śledzone.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Utwórz całodzienne szablonu przepustowości, która rozpoczyna się o określonej godzinie

Wykonaj tę procedurę, aby utworzyć harmonogram, który rozpoczyna się w określonym momencie i jest cały dzień. W tym przykładzie harmonogram rozpoczyna się od 9: 00 rano i działa aż do 9: 00 następnego dnia rano. Należy pamiętać, który godziny rozpoczęcia i zakończenia dla danego harmonogramu muszą być zarówno zawarte na tym samym harmonogramem 24-godzinnego i nie może obejmować wiele dni. Jeśli musisz skonfigurować szablony przepustowości, które rozciągają się wiele dni, należy użyć wielu harmonogramów (jak pokazano w przykładzie).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Aby utworzyć całodzienne szablonu przepustowości

1. Utwórz harmonogram, który rozpoczyna się od 9: 00 rano i działa aż do północy.
2. Dodać kolejny harmonogram. Skonfiguruj drugi harmonogram wykonywania od północy do 9: 00 w nocy.
3. Zapisz szablon przepustowości.

Harmonogram złożony zostanie następnie uruchomione w czasie wybrane i uruchomisz całodzienne.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Pytania i odpowiedzi dotyczące szablonami przepustowości

**Q**. Co się stanie sterujące przepustowością w przypadku Between harmonogramy? (Zakończeniem jednego harmonogramu i inny nie została jeszcze uruchomiona.)

**A**. W takich przypadkach zostanie zatrudnionych bez kontroli przepustowości. Oznacza to, że urządzenie może używać bez ograniczeń przepustowości podczas obsługi warstw danych w chmurze.

**Q**. Można zmodyfikować szablony przepustowości na urządzeniu w trybie offline?

**A**. Nie można zmodyfikować szablony przepustowości w kontenerach woluminów, jeśli odpowiednie urządzenie jest w trybie offline.

**Q**. Można edytować szablon przepustowości skojarzony kontener woluminów, gdy skojarzone woluminy są w trybie offline?

**A**. Można zmodyfikować szablonu przepustowości, skojarzony kontener woluminów, na których woluminy są w trybie offline. Należy pamiętać, że gdy woluminy są w trybie offline, żadne dane nie będą umieszczane z urządzenia do chmury.

**Q**. Można usunąć domyślnego szablonu?

**A**. Mimo że można usunąć domyślnego szablonu, nie jest dobry pomysł, aby to zrobić. Użycie szablonu domyślnego, w tym wersje edytowanych jest śledzone. Dane śledzenia są analizowane, a w miarę upływu czasu, zostanie użyty do usprawnienia szablonu domyślnego.

**Q**. Jak można ustalić, czy trzeba zmodyfikować szablony przepustowości?

**A**. Jest jednym ze znaków, należy zmodyfikować szablony przepustowości, gdy wykonywanych sieci działa wolno lub podlewka wiele razy w ciągu dnia. W takim przypadku monitorowania sieci magazynu i użycia, patrząc na wykresy wydajności operacji We/Wy i przepływność sieci.

Dane przepływność sieci należy zidentyfikować godzina i kontenery woluminów, w których występuje wąskich gardeł. Jeśli dzieje, gdy dane są są warstwowe w chmurze (uzyskać te informacje z wydajności operacji We/Wy dla wszystkich kontenerów woluminów dla urządzenia do chmury), a następnie należy zmodyfikować szablony przepustowości skojarzony z kontenerami woluminów.

Po modyfikacji szablonów są używane, należy monitorować ponownie znaczne opóźnienia sieci. Jeśli te nadal istnieje, będą potrzebne do szablonów przepustowości.

**Q**. Co się stanie, jeśli masz wiele kontenerów woluminów na urządzeniu z systemem planuje tego nakładają się na siebie, ale różnych limity mają zastosowanie do każdego?

**A**. Załóżmy, że masz urządzenie z 3 kontenery woluminów. Harmonogramy skojarzone z tych kontenerów całkowicie nakładają się na siebie. Dla każdego z tych kontenerów limity przepustowości, używane są 5, 10 lub 15 MB/s. Podczas operacji We/Wy występują we wszystkich tych kontenerów, w tym samym czasie, co najmniej 3 limity przepustowości mogą być stosowane: w tym przypadku 5 MB/s, jak te wychodzących żądań We/Wy udostępnianie tej samej kolejki.

## <a name="best-practices-for-bandwidth-templates"></a>Najlepsze rozwiązania dotyczące szablonów przepustowości

Wykonaj te najlepsze praktyki dotyczące urządzenia StorSimple:

* Skonfiguruj szablony przepustowości na urządzeniu, aby włączyć zmiennej ograniczanie przepustowości sieci przez urządzenie o różnych porach dnia. Te szablony przepustowości, gdy jest używane z harmonogramów tworzenia kopii zapasowych mogą efektywnie korzystać z dodatkową przepustowość dla operacji w chmurze poza godzinami szczytu.
* Oblicz rzeczywiste przepustowość wymaganą dla określonego wdrożenia na podstawie rozmiaru wdrożenia i cel czasu odzyskiwania wymagane (RTO).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [przy użyciu usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

