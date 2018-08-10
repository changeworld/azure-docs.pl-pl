---
title: Wdróż swoją ofertę w portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o, a następnie przeprowadzenie instrukcjami, aby wdrażać oferty — obraz maszyny wirtualnej, usługi dla deweloperów, usługi danych itd. — w portalu Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: 8f79b891-84e2-4f41-ba0d-66420e2c6b2e
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2016
ms.author: hascipio
ms.openlocfilehash: 12dc81642905cd9449a1032c7ab57298e6b69ba8
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714285"
---
# <a name="deploy-your-offer-to-the-azure-marketplace"></a>Wdróż swoją ofertę w portalu Azure Marketplace
Gdy jesteś zadowolony z ofertą (oznacza to, należy przetestować scenariusze klientów, marketing zawartości, itp.) i wszystko będzie gotowe do uruchomienia i zażądać **wypchnięcia do produkcji** na **Publikuj** kartę.  

1. Cztery kroki opisane w temacie przewodnika strony w publikowaniu portal powinien być ukończone i zielony. Oferty maszyny wirtualnej upewnij się, że są przestrzegane następujące wskazówki.
   
    ![Rysowanie][img-pubportal-walkthru-checked]
2. Wybierz **Publikuj** karty na liście po lewej stronie.
   
    ![Rysowanie][img-pubportal-menu-publish]
3. Kliknij przycisk **żądania zatwierdzenia do wypchnięcia do produkcji**. Po wysłaniu żądania, zatwierdzenia zespół wykonuje ostateczny Przegląd, a następnie Twoja oferta będzie dostępna w portalu Azure Marketplace.
   
    ![Rysowanie][img-pubportal-publish-pushproduction]

> [!IMPORTANT]
> W przypadku maszyn wirtualnych po kliknięciu przycisku zatwierdzenia żądania do wypchnięcia do produkcji, poniższe kroki są wykonywane za sceną. Można wyświetlić postęp każdego kroku na karcie publikowanie w publikowania portalu. Musisz sprawdzić tę stronę w regularnych odstępach czasu (do czasu jest wyświetlany stan "Lista") Aby uzyskać informacje o błędzie, który potrzebne korekty od użytkownika końcowego.
> 
> * Na początku żądania produkcji jest przesyłany do zespołu certyfikacji, który zweryfikować wirtualnego dysku twardego. Jednak Jeśli aktualizujesz już wymienionych oferty i żądanie ma stało się tylko marketingowych zmiany, następnie certyfikacji krok zostanie pominięty.
> * W następnym kroku żądania są dostarczane do zespołu weryfikacji zawartości, do którego zweryfikować zawartości marketingowej oferty.
> * Jeśli powyższe czynności zakończą się powodzeniem, oferta została zatwierdzona w środowisku produkcyjnym. W tej chwili stan "zamieszczenie" w portalu wydawców. Jednak ten stan "Lista" nie oznacza, czy proces zostanie zakończony. Poniższe kroki muszą być ukończone, zanim oferta jest dostępna w witrynie Azure Marketplace.
> * Po zatwierdzeniu oferty w środowisku produkcyjnym w poprzednim kroku rozpoczęcia replikacji, oferty, we wszystkich centrach danych platformy Azure. Zazwyczaj zajmuje 24-48hours na zakończenie replikacji ale może potrwać tydzień w zależności od rozmiaru wirtualnego dysku twardego. Jednak Jeśli aktualizujesz już wymienionych oferty i otrzymano tylko marketingowych zmiany, następnie replikacji jest szybsze.
> * Po zakończeniu replikacji to tej oferty będą dostępne w witrynie Azure Marketplace.
> 
> Zawsze możesz usunąć ofertę, gdy są one **projekt** stanu (czyli nigdy nie **Wypychanie do wdrażania przejściowego** lub **wypchnięcia do produkcji**). Na **historii** kliknij pozycję **Odrzuć wersję roboczą** znajdujący się u dołu strony, aby usunąć projekt.
> 
> 

## <a name="production-checklist-for-all-virtual-machine-offers"></a>Lista kontrolna produkcji, dla wszystkich ofert z maszyny wirtualnej
* Upewnij się, że jesteś partnerem z certyfikatem platformy Microsoft Azure
* Na karcie jednostki SKU opcję "Hide tej jednostki SKU z witryny Marketplace, ponieważ zawsze powinny zostać zakupione za pomocą szablonu rozwiązania" powinien być oznaczony jako tak tylko wtedy, gdy jednostka SKU jest częścią szablonu rozwiązania. We wszystkich innych przypadkach ta opcja zawsze powinien być oznaczony jako nie.
* Pamiętaj: Nie należy zmieniać ustawienie widoczności jednostki SKU po jednostki SKU znajduje się na liście. Ta funkcja nie jest obsługiwana.
* Upewnij się, że logo przestrzegają wytycznych dotyczących logo portalu Azure Marketplace, które zostały podane poniżej.
* Opis oferty i jednostki SKU nie powinny być takie same.
* Jednostek SKU tytuł i oferują długie podsumowanie nie powinny być takie same.
* Jednostka SKU tytuł czy Podsumowanie oferty nie powinny być takie same.
* Tytuły jednostki SKU nie powinna być taka sama, jak dla oferty wielu jednostek SKU.

**Azure Marketplace wytycznych dotyczących logo**

* Projekt platformy Azure ma prostą paletę kolorów. Niskich numer podstawowy i pomocniczy kolory na logo.
* Kolory motywu w witrynie Azure Portal są białe i czarne. Dlatego należy unikać te kolory jako kolor tła logo usługi. Użyj niektóre koloru, która spowodowałaby wprowadzenie Twoje logo widocznym w witrynie Azure portal. Zalecamy proste kolory podstawowe. Jeśli korzystają z przezroczystym tłem, upewnij się, że logo/tekstu nie jest białe lub czarny.
* Nie należy używać gradientu tła na logo.
* Należy unikać wprowadzania tekstu, nawet Twoja firma lub nazwa marki na logo.
* Wygląd i działanie logo powinno być "płaską" i unikać gradientów.
* Logo nie powinien być rozciągnięty.

**Dodatkowe wytyczne dotyczące Hero logo:**

* Logo usługi Hero jest opcjonalne. Wydawcy mogą zrezygnować z Przekaż Hero logo. **Jednak po przekazanych ikony elementów hero, nie można usunąć z publikowanie portalu. W tym czasie partner musi korzystać z portalu Azure Marketplace dotyczących ikony Hero inne oferty nie zostanie zatwierdzona do środowiska produkcyjnego.**
* Nazwa wyświetlana wydawcy, nazwa jednostki SKU i długie Podsumowanie oferty są wyświetlane w kolorze białym czcionki. Dlatego należy unikać, pamiętając wszelkie jasny kolor tła ikony elementów Hero. Czarny, biały i przezroczyste tło nie jest dozwolone dla elementu Hero ikon.
* Wydawcy Nazwa wyświetlana, nazwa jednostki SKU, długie Podsumowanie oferty i przycisk Utwórz są osadzone programowo wewnątrz Hero logo po oferty, które będą uwzględnione na liście. Nie należy więc wprowadzić dowolny tekst, podczas projektowania Hero logo. Po prostu pozostaw puste miejsce po prawej stronie, ponieważ tekst (np. Nazwa wyświetlana wydawcy, tytuł jednostki SKU, długie Podsumowanie oferty) zostaną uwzględnione programowo przez firmę Microsoft za pośrednictwem miejsca. Puste miejsce tekst powinien być 415 x 100, po prawej stronie (i przesunięty 370px z lewej strony).

## <a name="additional-production-checklist-for-already-listed-virtual-machine-offers"></a>Oferuje dodatkowe produkcji listy kontrolnej zawierającej już uwzględnione na liście maszyny wirtualnej
* Sprawdź, czy jest już oferty o takiej samej nazwie oferty z Twojej firmy. Jeśli tak, należy dodać nową wersję jednostki SKU w istniejącej oferty zamiast tworzenia nowej oferty duplikatów.
* Dysk danych, nie należy zmieniać między dwoma wersjami tego samego jednostki SKU.
* W portalu Azure Marketplace nie obsługuje zmianą cen uwzględnione na liście jednostek SKU, ponieważ ma wpływ na rozliczenia istniejących klientów. Upewnij się, że nie zmienisz cenach uwzględnione na liście jednostek SKU w regionach, w której jednostka SKU jest dostępna. Można jednak dodać nowe jednostki SKU lub Dodaj nowe regiony, do istniejących jednostek SKU.

## <a name="next-steps"></a>Kolejne kroki
Po tej oferty przechodzi na żywo, należy przetestować scenariusze klientów, sprawdź, czy wszystkie kontrakty i funkcje działają prawidłowo w środowisku produkcyjnym jako przetestowany i zweryfikowany w środowisku przejściowym.

## <a name="see-also"></a>Zobacz także
* [Wprowadzenie: jak opublikować ofertę w portalu Azure Marketplace](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]:media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]:media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]:media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png
