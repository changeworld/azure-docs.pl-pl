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
ms.date: 03/07/2019
ms.author: spelluru
ms.openlocfilehash: f761af3a5a3f08e4da89d8869aea5d666ecd69d0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60868290"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Śledź koszty związane z laboratorium Azure DevTest Labs
Ten artykuł zawiera informacje na temat śledzenia kosztów środowiska laboratoryjnego. Pokazuje sposób wyświetlania szacowany koszt trent w bieżącym miesiącu dla laboratorium. Artykuł przedstawia również sposób wyświetlania data miesiąca koszt zasobów w środowisku laboratoryjnym

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Wyświetl miesięczne szacowane trendu kosztów laboratorium 
W tej sekcji dowiesz się, jak używać **miesięczne szacowane trendu kosztów** wykres, aby wyświetlić bieżący miesiąc kalendarza szacowany koszt od początku i przewidywany koszt koniec miesiąca dla bieżącego miesiąca kalendarzowego. Poznasz również sposób zarządzania koszty laboratorium, ustawiając wydatków celów i progów, gdy osiągnięty wyzwalacza usługi DevTest Labs zgłosić wyników.

Aby wyświetlić wykres trendu kosztów szacowane miesięczne, wykonaj następujące kroki: 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.
3. Zaznacz na liście laboratoriów środowiska laboratoryjnego.  
4. Wybierz **konfiguracji i zasad** w menu po lewej stronie.  
4. Wybierz **trendu kosztów** w **śledzenie kosztów** sekcji, w menu po lewej stronie. Poniższy zrzut ekranu przedstawia przykład wykresu kosztów. 
   
    ![Wykres kosztów](./media/devtest-lab-configure-cost-management/graph.png)

    **Szacowany koszt** wartość bieżącego miesiąca kalendarzowego szacowany koszt od początku. **Przewidywany koszt** jest szacowany koszt dla całego bieżącego miesiąca kalendarzowego obliczane przy użyciu kosztów laboratorium dla poprzednich 5 dni.

    Kwoty kosztów jest zaokrąglana do najbliższej liczby całkowitej. Na przykład: 

   * 5.01 Zaokrągla do 6 
   * 5.50 Zaokrągla do 6
   * 5.99 Zaokrągla do 6

     Stwierdza, powyżej wykresu, kosztów, zobacz domyślnie na wykresie są *szacowany* koszty przy użyciu [płatność za rzeczywiste użycie](https://azure.microsoft.com/offers/ms-azr-0003p/) stawki oferty. Można także zdefiniować własne wydatków obiektów docelowych, które są wyświetlane na wykresach przez [zarządzania celami kosztów dla swojego laboratorium.](#managing-cost-targets-for-your-lab)

     Poniżej koszty *nie* uwzględnionych podczas obliczania kosztów:

   * Subskrypcje dostawcy usług Kryptograficznych i Dreamspark nie są obecnie obsługiwane, ponieważ korzysta z usługi Azure DevTest Labs [interfejsy API rozliczeń Azure](../billing/billing-usage-rate-card-overview.md) do obliczania laboratorium koszt, który nie obsługuje dostawcy usług Kryptograficznych lub Dreamspark subskrypcji.
   * Twoje stawki oferty. Obecnie nie można używać (podane w ramach Twojej subskrypcji), możesz mieć negocjowane z firmą Microsoft lub Microsoft partnerów stawki oferty. Używane są tylko stawek.
   * Z podatków.
   * Slevy
   * Twoja waluta rozliczeniowa. Obecnie kosztów laboratorium jest wyświetlany tylko w walucie USD.

### <a name="managing-cost-targets-for-your-lab"></a>Zarządzanie kosztów związanych z laboratorium
DevTest Labs pozwala lepiej zarządzać kosztami w środowisku laboratoryjnym, ustawiając wydatków docelowy, który można wyświetlić na wykresie miesięczne szacowane trendu kosztów. DevTest Labs można również wysłane powiadomienie po osiągnięciu określonego obiektu docelowego wydatków lub wartość progową. 

1. Na **trendu kosztów** wybierz opcję **docelowej Zarządzaj**.

    ![Zarządzanie przycisk docelowej](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. Na **docelowej Zarządzaj** Określ docelową wydatków i progów. Można również ustawić, czy każdy wybrany próg, jest zgłaszany na wykres trendu kosztów lub za pośrednictwem powiadomień elementu webhook.

    ![Zarządzanie okienka docelowej](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Wybierz przedział czasu, w którym mają być koszt celów śledzenia.
      - **Miesięczne**: elementy docelowe kosztów są śledzone na miesiąc.
      - **Naprawiono**: elementy docelowe kosztów są śledzone dla zakresu dat, określ daty rozpoczęcia i zakończenia. Zazwyczaj te wartości reprezentują, jak długo projektu jest zaplanowane do uruchomienia.
   - Określ **docelowe koszt**. Na przykład ile planujesz możesz wydać na tym środowisku laboratoryjnym w przedziale czasu, który został zdefiniowany.
   - Zaznacz, aby włączyć lub wyłączyć wszelkie próg ma zgłoszone — w przyrostach co 25% — do 125% z określonym **docelowe koszt**.
      - **Powiadom**: Po spełnieniu tego progu, otrzymasz powiadomienie, należy określić adres URL elementu webhook.
      - **Vykreslit v**: Po spełnieniu tego progu, wyniki są oznaczane na wykresach na wykres trendu kosztów, który można wyświetlić, zgodnie z opisem w wyświetlania wykresu trendu kosztów szacowane miesięczne.
   - Jeśli zdecydujesz się **powiadamiania** po osiągnięciu wartości progowej, należy określić adres URL elementu webhook. W obszarze integracji kosztów, wybierz **kliknij tutaj, aby dodać integracji**. Wprowadź **adresu URL elementu Webhook** w okienku powiadomienia Konfiguruj, a następnie wybierz pozycję **OK**.

       ![Konfigurowanie okienkiem powiadomienia](./media/devtest-lab-configure-cost-management/configure-notification.png)

     - Jeśli określisz **powiadamiania**, należy określić adres URL elementu webhook.
     - Podobnie, jeśli zdefiniujesz adres URL elementu webhook, musisz ustawić **powiadomień** do **na** w okienku próg kosztów.
     - Należy utworzyć element webhook przed wprowadź go tutaj.  

       Aby uzyskać więcej informacji na temat elementów webhook, zobacz [Tworzenie elementu webhook lub interfejsu API usługi Azure Function](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

## <a name="view-cost-by-resource"></a>Widok koszty według zasobu 
Miesięczny koszt trend funkcja w laboratoriach umożliwia można zobaczyć, ile możesz spędzić w bieżącym miesiącu. Pokazuje także rzut wydatków aż do końca miesiąca, w oparciu o swoje wydatki w ciągu ostatnich siedmiu dni. Aby lepiej zrozumieć, dlaczego wydatków w środowisku laboratoryjnym osiąga progi na wczesnym etapie, można użyć **koszty według zasobu** Funkcja pokazująca kosztów od początku miesiąca **poszczególnych zasobów** w tabeli.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.
3. Z listy labs wybierz żądane laboratorium.  
4. Wybierz **konfiguracji i zasad** w menu po lewej stronie.
5. Wybierz **koszty według zasobu** w **śledzenie kosztów** sekcji, w menu po lewej stronie. Zostanie wyświetlony kosztów związanych z poszczególnych zasobów skojarzonych z laboratorium. 

    ![Koszt według zasobu](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Ta funkcja pomaga łatwo zidentyfikować zasoby, których koszt najbardziej tak, aby można wykonać akcje, aby ograniczyć wydatki laboratorium. Na przykład koszt maszyny Wirtualnej opiera się na rozmiar maszyny Wirtualnej. Im większy rozmiar maszyny Wirtualnej, więcej jest koszt. Rozmiar maszyny Wirtualnej i jego właściciela, można łatwo znaleźć, dzięki czemu można rozmawiać, właściciel maszyny Wirtualnej, aby zrozumieć, dlaczego jest potrzebny takiego rozmiaru maszyny Wirtualnej i czy istnieje ryzyko, aby zmniejszyć rozmiar.

[Zasady automatycznego zamykania](devtest-lab-get-started-with-lab-policies.md#set-auto-shutdown) ułatwia obniżenie kosztów dzięki zamykaniu laboratorium, maszyny wirtualne o określonej porze dnia. Jednak użytkownik laboratorium można zrezygnować z zasad zamknięcia, co zwiększa koszt uruchamiania maszyny Wirtualnej. Możesz wybrać Maszynę wirtualną w tabeli, aby zobaczyć, jeśli je ma został wyłączony w poziomie zasad automatycznego zamykania. Jeśli tak jest rzeczywiście, możesz skontaktować się do właściciela maszyny Wirtualnej można znaleźć Dlaczego maszyna wirtualna ma został wyłączony w poziomie zasad.
 
## <a name="next-steps"></a>Kolejne kroki
Oto kilka rzeczy, aby wypróbować następne:

* [Definiowanie zasad laboratorium](devtest-lab-set-lab-policy.md) — Dowiedz się, jak ustawić różne zasady stosowane do zarządzania, do czego służą środowiska laboratoryjnego i jego maszyn wirtualnych. 
* [Tworzenie obrazu niestandardowego](devtest-lab-create-template.md) — podczas tworzenia maszyny Wirtualnej, należy określić podstawowy, który może być niestandardowego obrazu lub obrazu z witryny Marketplace. W tym artykule pokazano, jak utworzyć niestandardowy obraz z pliku wirtualnego dysku twardego.
* [Konfigurowanie obrazów z witryny Marketplace](devtest-lab-configure-marketplace-images.md) — usługa DevTest Labs obsługuje tworzenia maszyn wirtualnych opartych na obrazach portalu Azure Marketplace. W tym artykule pokazano, jak określić, które ewentualnie obrazów portalu Azure Marketplace, mogą być używane podczas tworzenia maszyn wirtualnych w laboratorium.
* [Tworzenie maszyny Wirtualnej w laboratorium](devtest-lab-add-vm.md) -pokazano, jak utworzyć maszynę Wirtualną z obrazu podstawowego (albo niestandardowe lub portalu Marketplace) oraz sposób pracy z artefaktami w maszynie Wirtualnej.

