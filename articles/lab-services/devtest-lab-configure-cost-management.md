---
title: Wyświetl miesięczne szacowane trendu kosztów laboratorium Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Więcej informacji na temat wykres trendu szacowany koszt miesięczny Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: d3a02a850059dc96e4e4ba306fed7cded2f7dd7d
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454191"
---
# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>Wyświetl miesięczne szacowane trendu kosztów laboratorium Azure DevTest Labs
Funkcja zarządzania kosztami DevTest Labs pomaga śledzić koszty środowiska laboratoryjnego. W tym artykule przedstawiono sposób użycia **miesięczne szacowane trendu kosztów** wykres, aby wyświetlić bieżący miesiąc kalendarza szacowany koszt od początku i przewidywany koszt koniec miesiąca dla bieżącego miesiąca kalendarzowego. W tym artykule również pokazano, jak w lepszym zarządzaniu kosztami laboratorium, ustawiając wydatków celów i progów, gdy osiągnięty wyzwalacza usługi DevTest Labs zgłosić wyników.

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>Wyświetlanie wykresu trendu kosztów szacowany miesięczny
Aby wyświetlić wykres trendu kosztów szacowane miesięczne, wykonaj następujące kroki: 

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. W razie potrzeby zaznacz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy. (Laboratorium może być już wyświetlane na pulpicie nawigacyjnym w obszarze **wszystkie zasoby**).
1. Z listy labs wybierz żądane laboratorium.  
1. W laboratorium **Przegląd** wybierz opcję **konfiguracji i zasad**.   
1. Po lewej stronie w obszarze **ŚLEDZENIE kosztów**, wybierz opcję **trendu kosztów**.

   Poniższy zrzut ekranu przedstawia przykład wykresu kosztów. 
   
    ![Wykres kosztów](./media/devtest-lab-configure-cost-management/graph.png)

**Szacowany koszt** wartość bieżącego miesiąca kalendarzowego szacowany koszt od początku. **Przewidywany koszt** jest szacowany koszt dla całego bieżącego miesiąca kalendarzowego obliczane przy użyciu kosztów laboratorium dla poprzednich 5 dni.

Kwoty kosztów jest zaokrąglana do najbliższej liczby całkowitej. Na przykład: 

* 5.01 Zaokrągla do 6 
* 5.50 Zaokrągla do 6
* 5.99 Zaokrągla do 6

Stwierdza, powyżej wykresu, kosztów, zobacz domyślnie na wykresie są *szacowany* koszty przy użyciu [płatność za rzeczywiste użycie](https://azure.microsoft.com/offers/ms-azr-0003p/) stawki oferty. Można także zdefiniować własne wydatków obiektów docelowych, które są wyświetlane na wykresach przez [zarządzania celami kosztów dla swojego laboratorium.](#managing-cost-targets-for-your-lab)

Ponadto poniżej przedstawiono *nie* uwzględnionych podczas obliczania kosztów:

* Subskrypcje dostawcy usług Kryptograficznych i Dreamspark nie są obecnie obsługiwane, ponieważ korzysta z usługi Azure DevTest Labs [interfejsy API rozliczeń Azure](../billing/billing-usage-rate-card-overview.md) do obliczania laboratorium koszt, który nie obsługuje dostawcy usług Kryptograficznych lub Dreamspark subskrypcji.
* Twoje stawki oferty. Obecnie nie można używać (podane w ramach Twojej subskrypcji), możesz mieć negocjowane z firmą Microsoft lub Microsoft partnerów stawki oferty. Używane są tylko stawek.
* Z podatków.
* Slevy
* Twoja waluta rozliczeniowa. Obecnie kosztów laboratorium jest wyświetlany tylko w walucie USD.

## <a name="managing-cost-targets-for-your-lab"></a>Zarządzanie kosztów związanych z laboratorium
DevTest Labs pozwala lepiej zarządzać kosztami w środowisku laboratoryjnym, ustawiając wydatków docelowy, który można wyświetlić na wykresie miesięczne szacowane trendu kosztów. DevTest Labs można również wysłane powiadomienie po osiągnięciu określonego obiektu docelowego wydatków lub wartość progową. 

1. W środowisku laboratoryjnym **Przegląd** okienku wybierz **konfiguracji i zasad**.
1. Po lewej stronie w obszarze **ŚLEDZENIE kosztów**, wybierz opcję **trendu kosztów**.

    ![Zarządzanie przycisk docelowej](./media/devtest-lab-configure-cost-management/cost-trend.png)

1. W **trendu kosztów** okienku wybierz **docelowej Zarządzaj**.

    ![Zarządzanie przycisk docelowej](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)

1. W okienku Zarządzanie docelowej Określ wybraną docelową wydatków i progów. Można również ustawić, czy każdy wybrany próg, jest zgłaszany na wykres trendu kosztów lub za pośrednictwem powiadomień elementu webhook.

    ![Zarządzanie okienka docelowej](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Wybierz przedział czasu, w którym mają być koszt celów śledzenia.
      - **Miesięczne**: elementy docelowe kosztów są śledzone na miesiąc.
      - **Naprawiono**: elementy docelowe kosztów są śledzone dla zakresu dat, określ daty rozpoczęcia i zakończenia. Zwykle to może odpowiadać, o ile projekt jest zaplanowane do uruchomienia.
   - Określ **docelowe koszt**. Na przykład może to być ile plan do wydania na tym środowisku laboratoryjnym w przedziale czasu, który został zdefiniowany.
   - Zaznacz, aby włączyć lub wyłączyć wszelkie próg ma zgłoszone — w przyrostach co 25% — do 125% z określonym **docelowe koszt**.
      - **Powiadom**: Po spełnieniu tego progu, otrzymasz powiadomienie, należy określić adres URL elementu webhook.
      - **Vykreslit v**: Po spełnieniu tego progu, wyniki są oznaczane na wykresach na wykres trendu kosztów, który można wyświetlić, zgodnie z opisem w [wyświetlania wykresu trendu kosztów szacowany miesięczny](#viewing-the-monthly-estimated-cost-trend-chart).
   - Jeśli zdecydujesz się **powiadamiania** po osiągnięciu wartości progowej, należy określić adres URL elementu webhook. W obszarze integracji kosztów, wybierz **kliknij tutaj, aby dodać integracji**.

      Wprowadź adres URL elementu Webhook w okienku Konfiguracja powiadomień, a następnie wybierz pozycję **OK**.

       ![Konfigurowanie okienkiem powiadomienia](./media/devtest-lab-configure-cost-management/configure-notification.png)

      - Jeśli określisz **powiadamiania**, należy określić adres URL elementu webhook.
      - Podobnie, jeśli zdefiniujesz adres URL elementu webhook, musisz ustawić **powiadomień** do **na** w okienku próg kosztów.
      - Należy utworzyć element webhook przed wprowadź go tutaj.  

      Aby uzyskać więcej informacji na temat elementów webhook, zobacz [Tworzenie elementu webhook lub interfejsu API usługi Azure Function](../azure-functions/functions-create-a-web-hook-or-api-function.md). 
 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Wpisy w blogu pokrewne
* [Więcej opcji Zachowaj koszt zgodnie z planem w usłudze DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [Dlaczego progach kosztów?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>Kolejne kroki
Oto kilka rzeczy, aby wypróbować następne:

* [Definiowanie zasad laboratorium](devtest-lab-set-lab-policy.md) — Dowiedz się, jak ustawić różne zasady stosowane do zarządzania, do czego służą środowiska laboratoryjnego i jego maszyn wirtualnych. 
* [Tworzenie obrazu niestandardowego](devtest-lab-create-template.md) — podczas tworzenia maszyny Wirtualnej, należy określić podstawowy, który może być niestandardowego obrazu lub obrazu z witryny Marketplace. W tym artykule pokazano, jak utworzyć niestandardowy obraz z pliku wirtualnego dysku twardego.
* [Konfigurowanie obrazów z witryny Marketplace](devtest-lab-configure-marketplace-images.md) — usługa DevTest Labs obsługuje tworzenia maszyn wirtualnych opartych na obrazach portalu Azure Marketplace. W tym artykule pokazano, jak określić, które ewentualnie obrazów portalu Azure Marketplace, mogą być używane podczas tworzenia maszyn wirtualnych w laboratorium.
* [Tworzenie maszyny Wirtualnej w laboratorium](devtest-lab-add-vm.md) -pokazano, jak utworzyć maszynę Wirtualną z obrazu podstawowego (albo niestandardowe lub portalu Marketplace) oraz sposób pracy z artefaktami w maszynie Wirtualnej.

