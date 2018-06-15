---
title: Wyświetl trend miesięczny koszt szacowany laboratorium w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Więcej informacji o usłudze Azure DevTest Labs miesięczne szacowany koszt wykresu trendu.
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
ms.openlocfilehash: 13535dae82ef2c8896dad7d6221553d15e4e6a95
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787633"
---
# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>Wyświetl trend miesięczny koszt szacowany laboratorium w usłudze Azure DevTest Labs
Funkcja zarządzania koszt DevTest Labs pomaga śledzić koszt laboratorium. W tym artykule przedstawiono sposób użycia **miesięczny Trend szacowany koszt** wykresu, aby wyświetlić bieżącego miesiąca kalendarzowego szacowany koszt do daty i szacowany koszt koniec miesiąca dla bieżącego miesiąca kalendarzowego. W tym artykule przedstawiono również sposób lepiej zarządzać kosztami laboratorium, ustawiając wydatków progi i obiekty docelowe, po osiągnięciu wyzwalacza DevTest Labs zgłosić wyników.

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>Wyświetlanie wykresu miesięczny Trend szacowany koszt
Aby wyświetlić na wykresie miesięczny Trend szacowany koszt, wykonaj następujące kroki: 

1. Zaloguj się w witrynie [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. W razie potrzeby wybierz **wszystkie usługi**, a następnie wybierz **DevTest Labs** z listy. (Laboratorium może być jest wyświetlana na pulpicie nawigacyjnym w obszarze **wszystkie zasoby**).
1. Z listy labs wybierz żądany laboratorium.  
1. W laboratorium **omówienie** wybierz opcję **konfiguracji i zasadach**.   
1. Po lewej stronie w obszarze **ŚLEDZENIE kosztów**, wybierz pozycję **koszty**.

   Poniższy zrzut ekranu przedstawia przykład wykresu kosztów. 
   
    ![Wykres kosztu](./media/devtest-lab-configure-cost-management/graph.png)

**Szacowany koszt** wartość jest bieżącego miesiąca kalendarzowego szacowany koszt do daty. **Koszt planowany** jest szacowany koszt dla całego bieżącego miesiąca kalendarzowego obliczane przy użyciu koszt laboratorium dla poprzedniego pięć dni.

Kwoty kosztów są zaokrąglona w górę do najbliższej liczby całkowitej. Na przykład: 

* 5.01 Zaokrągla do 6 
* 5.50 Zaokrągla do 6
* 5.99 Zaokrągla do 6

Stany go powyżej wykresu, kosztów, zobacz domyślnie na wykresie są *szacowany* koszty przy użyciu [płatność za rzeczywiste użycie](https://azure.microsoft.com/offers/ms-azr-0003p/) stawek oferty. Można również ustawić własne wydatków obiektów docelowych, które są wyświetlane na wykresach przez [Zarządzanie docelowe koszt laboratorium.](#managing-cost-targets-for-your-lab)

Ponadto są następujące *nie* uwzględnionych podczas obliczania kosztów:

* Dostawca usług Kryptograficznych i Dreamspark subskrypcje obecnie nie są obsługiwane jako używa Azure DevTest Labs [Azure API rozliczeń](../billing/billing-usage-rate-card-overview.md) do obliczania kosztów, który nie obsługuje dostawcy usług Kryptograficznych lub Dreamspark subskrypcji laboratorium.
* Twoje stawek oferty. Nie można obecnie używać stawek oferty (wyświetlany w obszarze subskrypcji) czy musisz mieć negocjowane z Microsoft lub partnerów. Używane są tylko płatność za rzeczywiste użycie stawki.
* Twoje podatki
* Twoje zniżki
* Waluta. Obecnie koszt laboratorium jest wyświetlany tylko w walucie USD.

## <a name="managing-cost-targets-for-your-lab"></a>Zarządzanie docelowe koszt laboratorium
DevTest Labs umożliwia lepiej zarządzać kosztami w laboratorium, ustawiając wydatków docelowego, który można wyświetlić na wykresie miesięczny Trend szacowany koszt. DevTest Labs można również wysyłanie powiadomień po osiągnięciu określonego obiektu docelowego wydatków lub wartość progową. 

1. Na środowiska laboratoryjnego **omówienie** okienku wybierz **konfiguracji i zasadach**.
1. Po lewej stronie w obszarze **ŚLEDZENIE kosztów**, wybierz pozycję **koszty**.

    ![Zarządzanie przycisk docelowego](./media/devtest-lab-configure-cost-management/cost-trend.png)

1. W **koszty** okienku wybierz **docelowej Zarządzaj**.

    ![Zarządzanie przycisk docelowego](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)

1. W okienku Zarządzanie docelowej Określ progi i wydatków docelowy. Można również ustawić, czy każdy wybrany próg zostaje zgłoszone wykresu trendu koszt lub za pośrednictwem powiadomień elementu webhook.

    ![Zarządzanie okienka docelowego](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Wybierz przedział czasu, w którym mają być śledzone cele kosztów.
      - **Miesięczne**: elementy docelowe koszt są śledzone na miesiąc.
      - **Stałe**: elementy docelowe koszt są śledzone zakresu można określić daty rozpoczęcia i zakończenia. Zwykle to może odpowiadać jak długo projektu jest zaplanowane do uruchomienia.
   - Określ **Target koszt**. Na przykład może to być ile planujesz poświęcić w przedziale czasu, który został zdefiniowany w tym laboratorium.
   - Zaznacz, aby włączyć lub wyłączyć wszelkie próg ma zgłoszone — w przyrostach 25% — do 125% z określonym **Target koszt**.
      - **Powiadom**: po spełnieniu tego progu, otrzymasz powiadomienie, należy określić adres URL elementu webhook.
      - **Kreślenia na wykresie**: po spełnieniu tego progu, wyniki są kreślone na wykresu trendu kosztów, który można wyświetlać, zgodnie z opisem w [wyświetlania wykresu miesięczny Trend szacowany koszt](#viewing-the-monthly-estimated-cost-trend-chart).
   - Jeśli użytkownik chce **powiadamiania** ze osiągnięty jest próg, należy określić adres URL elementu webhook. W obszarze integracji koszt wybierz **kliknij tutaj, aby dodać integracji**.

      Wprowadź adres URL elementu Webhook w okienku Konfigurowanie powiadomień, a następnie wybierz **OK**.

       ![Skonfiguruj okienko powiadomień](./media/devtest-lab-configure-cost-management/configure-notification.png)

      - Jeśli określisz **powiadamiania**, należy określić adres URL elementu webhook.
      - Podobnie, jeśli adres URL elementu webhook, należy ustawić **powiadomień** do **na** w okienku próg kosztów.
      - Należy utworzyć elementu webhook przed wprowadzić go tutaj.  

      Aby uzyskać więcej informacji na temat elementów webhook, zobacz [tworzenia elementu webhook lub funkcja interfejsu API Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 
 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Wpisy na blogu pokrewne
* [Więcej opcji Zachowaj koszt zgodnie z planem w usłudze DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [Dlaczego koszt progi?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>Kolejne kroki
Poniżej przedstawiono niektóre czynności, aby spróbować obok:

* [Definiowanie zasad laboratorium](devtest-lab-set-lab-policy.md) — Dowiedz się, jak skonfigurowanie różnych zasad używane do sterowania korzystania laboratorium i jego maszyn wirtualnych. 
* [Tworzenie niestandardowego obrazu](devtest-lab-create-template.md) — podczas tworzenia maszyny Wirtualnej, należy określić podstawowy, który może być niestandardowy obraz lub obrazu z witryny Marketplace. W tym artykule przedstawiono sposób tworzenia niestandardowego obrazu z pliku VHD.
* [Konfigurowanie portalu Marketplace obrazów](devtest-lab-configure-marketplace-images.md) — DevTest Labs obsługuje tworzenie maszyn wirtualnych, oparte na obrazach portalu Azure Marketplace. W tym artykule przedstawiono sposób określić, które, portalu Azure Marketplace obrazy mogą być używane podczas tworzenia maszyn wirtualnych w laboratorium.
* [Utwórz maszynę Wirtualną w laboratorium](devtest-lab-add-vm.md) -ilustruje sposób tworzenia maszyny Wirtualnej z obrazu podstawowego (albo niestandardowe lub Marketplace) oraz sposób pracy z artefaktami w maszynie Wirtualnej.

