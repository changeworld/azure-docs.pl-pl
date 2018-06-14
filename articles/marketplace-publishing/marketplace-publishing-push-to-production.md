---
title: Wdrażanie Twojej oferty w portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o i przeprowadzenie instrukcje, aby wdrożyć ofertę — obraz maszyny wirtualnej, deweloperów usługi, Usługa danych itd. — w portalu Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 8f79b891-84e2-4f41-ba0d-66420e2c6b2e
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2016
ms.author: mbaldwin
ms.openlocfilehash: 8df7b0e49e17612743b02596e99f7d1fbe8c6803
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2018
ms.locfileid: "29943245"
---
# <a name="deploy-your-offer-to-the-azure-marketplace"></a>Wdrażanie Twojej oferty w portalu Azure Marketplace
Po zakończeniu Twojej oferty (to znaczy zostały przetestowane scenariuszy, marketingu zawartości, itp.) i wszystko jest gotowe do uruchomienia, żądań **wypychania do produkcji** na **publikowania** kartę.  

1. Cztery kroki opisane w tym PRZEWODNIKIEM strony publikowania portalu powinna być została zakończona i zielony. W przypadku ofert maszyny wirtualnej upewnij się, że zostaną wykonane następujące wytyczne.
   
    ![Rysowanie][img-pubportal-walkthru-checked]
2. Wybierz **publikowania** kartę na liście z lewej strony.
   
    ![Rysowanie][img-pubportal-menu-publish]
3. Kliknij przycisk **zażądać zatwierdzenia do produkcji**. Po wysłaniu żądania, zespół zatwierdzenia wykonuje końcowej oceny, a następnie ofertę będą dostępne w portalu Azure Marketplace.
   
    ![Rysowanie][img-pubportal-publish-pushproduction]

> [!IMPORTANT]
> W przypadku maszyn wirtualnych po kliknięciu przycisku żądania zatwierdzenia do środowiska produkcyjnego, następujące czynności są wykonywane za sceny. Można wyświetlić postęp każdego kroku na karcie PUBLIKUJ w publikacji portalu. Należy sprawdzić tę stronę w regularnych odstępach czasu (do czasu stanu wyświetlany jest "Lista") Aby uzyskać informacje o błędzie, który konieczne korekty użytkownika końcowego.
> 
> * Na początku żądanie produkcyjnej prowadzi do zespołu certyfikacji, który zweryfikować dysku vhd. Jednak Jeśli aktualizujesz już wymienionych ofertę i żądania otrzymano tylko marketingu zmiany, następnie certyfikacji krok zostanie pominięty.
> * W następnym kroku żądania pochodzą z zespołem weryfikacji zawartości, który Sprawdź zawartość marketing oferty.
> * Jeśli powyższe kroki zostały wykonane pomyślnie, oferty jest zatwierdzona w środowisku produkcyjnym. W tej chwili stan stają się "na liście" portal publikowania. Jednak ten stan "Lista" oznacza zakończeniu procesu. Następujące kroki muszą być ukończone, zanim oferta jest dostępna w portalu Azure Marketplace.
> * Gdy oferta zostanie zatwierdzone w środowisku produkcyjnym w poprzednim kroku, rozpoczęcia replikacji oferty, we wszystkich centrach danych platformy Azure. Zazwyczaj zajmuje 24-48hours na zakończenie replikacji ale może potrwać do tygodnia, w zależności od rozmiaru wirtualnego dysku twardego. Jednak Jeśli aktualizujesz już wymienionych ofertę i otrzymano tylko marketingu zmiany, następnie replikacji jest szybsze.
> * Po ukończeniu replikacji jest to oferta, dla której będą dostępne w portalu Azure Marketplace.
> 
> Zawsze można usunąć ofertę, gdy jest on w **projekt** stanu (tj., nigdy nie **wypychania do przemieszczania** lub **wypychania do środowiska produkcyjnego**). Na **historii** , kliknij pozycję **Odrzuć wersję roboczą** znajdujący się u dołu strony, aby usunąć projekt.
> 
> 

## <a name="production-checklist-for-all-virtual-machine-offers"></a>Lista kontrolna produkcyjnych w przypadku wszystkich ofert maszyny wirtualnej
* Upewnij się, że jesteś partnerem certyfikowane systemu Microsoft Azure
* Na karcie jednostki SKU opcję "Ukryj tej jednostki SKU z witryny Marketplace ponieważ zawsze powinna zostać zakupione za pośrednictwem szablon rozwiązania" powinien być oznaczony jako tak tylko wtedy, gdy jednostka SKU jest częścią szablonu rozwiązania. We wszystkich innych przypadkach ta opcja zawsze powinien być oznaczony jako nie.
* Pamiętaj: Nie należy zmieniać ustawienie widoczności SKU gdy jednostka SKU jest wyświetlany. Ta funkcja nie jest obsługiwana.
* Upewnij się, że logo przestrzegają wytycznych dotyczących logo portalu Azure Marketplace, które zostały podane poniżej.
* Opis oferta, jednostka SKU nie powinny być takie same.
* W jednostce SKU tytuł i oferują długie podsumowanie nie powinna być taka sama.
* Tytuł jednostki SKU i Podsumowanie oferty nie powinna być taka sama.
* Jednostka SKU tytuły nie powinny być identyczne dla oferty z wielu jednostki SKU.

**Wytycznych dotyczących logo Azure Marketplace**

* Projekt platformy Azure ma paletę kolorów proste. Zachowaj numer podstawowy i pomocniczy kolory na logo niski.
* Motyw kolorów portalu Azure są białe i czarne. Dlatego należy unikać kolorów te jako kolor tła swoje logo. Użyj niektórych koloru, która swoje logo widoczne w portalu Azure. Zaleca się proste kolorów podstawowych. Jeśli używasz przezroczyste tło, a następnie upewnij się, że logo/tekstu nie jest białe lub czarny.
* Nie należy używać gradientu tła na logo.
* Należy unikać wprowadzania tekstu, nawet Twoja firma lub marką, na logo.
* Wygląd i działanie logo powinno być "prosty" i unikać gradienty.
* Logo nie powinny być skalowane.

**Dodatkowe wskazówki dotyczące bohater logo:**

* Logo bohater jest opcjonalna. Wydawcy można zrezygnować z przekazać logo bohater. **Jednak po przekazane ikona bohater nie można usunąć z publikowania portalu. W tym czasie partnera musi postępuj zgodnie z wytycznymi portalu Azure Marketplace ikon bohater else oferta nie zostanie zatwierdzona do środowiska produkcyjnego.**
* Nazwa wyświetlana wydawcy, nazwa jednostki SKU i długie Podsumowanie oferty są wyświetlane w kolorze białym znakiem. Dlatego należy unikać zachowuje żadnych jasny kolor w tle ikona bohater. Czarne, białe i przezroczystego tła ikon bohater jest niedozwolone.
* Wydawca Nazwa wyświetlana, nazwa jednostki SKU, długie Podsumowanie oferty i przycisk Utwórz są osadzone programowo wewnątrz logo bohater po oferty przejdzie do listy. Nie należy więc wprowadzić dowolny tekst, podczas projektowania bohater logo. Tylko pozostaw puste miejsce po prawej stronie, ponieważ tekst (tj. wydawca, nazwa wyświetlana, nazwa jednostki SKU, długie Podsumowanie oferty) zostanie uwzględniona programowo przez nas nad nim. Puste miejsce na tekst powinien być 415 x 100 po prawej stronie (i przesunięty 370px z lewej strony).

## <a name="additional-production-checklist-for-already-listed-virtual-machine-offers"></a>Oferuje dodatkowe produkcji Lista kontrolna już wymienionych maszyny wirtualnej
* Sprawdź, czy jest już ofertę o takiej samej nazwie oferta w firmie. Jeśli tak, należy dodać nowej wersji jednostki SKU w istniejących oferta zamiast tworzenia nowej oferty zduplikowane.
* Dysk danych nie należy zmieniać między dwoma wersjami tej samej jednostki SKU.
* Portalu Azure Marketplace nie obsługuje cenową zmiany listy jednostki SKU zgodnie z ich wpływ rozliczeń istniejących odbiorców. Upewnij się, że nie zmienisz cennik wymienionych jednostki SKU w regionach, gdzie dostępna jest opcja jednostki SKU. Możesz jednak dodawać nowe jednostki SKU lub dodawanie nowych regionów do istniejącej jednostki SKU.

## <a name="next-steps"></a>Kolejne kroki
Gdy oferta odbywa się na żywo, testu scenariuszy, aby sprawdzić wszystkie kontrakty i funkcje działają poprawnie w środowisku produkcyjnym jako przetestowany i zweryfikowany w środowisku przemieszczania.

## <a name="see-also"></a>Zobacz także
* [Wprowadzenie: jak publikowanie oferty w portalu Azure Marketplace](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]:media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]:media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]:media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png
