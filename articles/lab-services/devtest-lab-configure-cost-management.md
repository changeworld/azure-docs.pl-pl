---
title: Wyświetlanie miesięcznego szacowanego trendu kosztowego laboratorium w laboratorium Azure DevTest Labs
description: Ten artykuł zawiera informacje na temat śledzenia kosztów laboratorium (miesięczny wykres trendu szacowanego kosztu) w laboratorium DevTest Azure.
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
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 2e4fe55fac5edf73e16df05bd38cc2712a94377d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721731"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Śledzenie kosztów związanych z laboratorium w laboratorium usługi Azure DevTest Labs
Ten artykuł zawiera informacje na temat sposobu śledzenia kosztów laboratorium. Pokazuje, jak wyświetlić szacowany koszt trydentu dla bieżącego miesiąca kalendarzowego dla laboratorium. W tym artykule pokazano również, jak wyświetlić miesiąc do daty koszt zasobu w laboratorium.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Wyświetlanie miesięcznego szacowanego trendu kosztowego laboratorium 
W tej sekcji dowiesz się, jak użyć wykresu **Miesięczny trend szacowanego kosztu,** aby wyświetlić szacowany koszt bieżącego miesiąca kalendarzowego i przewidywany koszt na koniec miesiąca dla bieżącego miesiąca kalendarzowego. Dowiesz się również, jak zarządzać kosztami laboratoryjnymi, ustawiając cele wydatków i progi, które po osiągnięciu wyzwalają DevTest Labs, aby zgłosić wyniki.

Aby wyświetlić wykres Miesięczny trend szacowanego kosztu, wykonaj następujące czynności: 

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.**
3. Z listy laboratoriów wybierz laboratorium.  
4. Wybierz **opcję Konfiguracja i zasady** w menu po lewej stronie.  
4. Wybierz **pozycję Trend kosztów** w sekcji **Śledzenie kosztów** w menu po lewej stronie. Poniższy zrzut ekranu przedstawia przykład wykresu kosztów. 
   
    ![Wykres kosztów](./media/devtest-lab-configure-cost-management/graph.png)

    Szacowana wartość **kosztu** jest szacowanym kosztem bieżącego miesiąca kalendarzowego. Przewidywany koszt to **szacowany** koszt dla całego bieżącego miesiąca kalendarzowego, obliczany przy użyciu kosztu laboratorium dla poprzednich pięciu dni.

    Kwoty kosztów są zaokrąglane w górę do następnej liczby rundzie 1000. Przykład: 

   * 5.01 zaokrągla do 6 
   * 5.50 zaokrągla do 6
   * 5.99 zaokrągla do 6

     Jak stanowi powyżej wykresu, koszty widoczne domyślnie na wykresie są *szacowane* koszty przy użyciu [pay-as-you-go](https://azure.microsoft.com/offers/ms-azr-0003p/) stawki oferty. Można również ustawić własne cele wydatków, które są wyświetlane na wykresach, [zarządzając celami kosztów dla laboratorium.](#managing-cost-targets-for-your-lab)

     W obliczeniach kosztów *nie* uwzględniono następujących kosztów:

   * Subskrypcje CSP i Dreamspark nie są obecnie obsługiwane, ponieważ usługa Azure DevTest Labs używa interfejsów API rozliczeń platformy Azure do obliczania kosztów [laboratoryjnych,](../cost-management-billing/manage/usage-rate-card-overview.md) które nie obsługują subskrypcji CSP lub Dreamspark.
   * Stawki oferty. Obecnie nie można korzystać ze stawek oferty (wyświetlanych w ramach subskrypcji), które zostały wynegocjowane z partnerami firmy Microsoft lub Microsoft. Używane są tylko stawki płatności zgodnie z rzeczywistym użyciem.
   * Twoje podatki
   * Twoje rabaty
   * Twoja waluta rozliczeniowa. Obecnie koszt laboratorium jest wyświetlany tylko w walucie USD.

### <a name="managing-cost-targets-for-your-lab"></a>Zarządzanie kosztami docelowymi dla twojego laboratorium
DevTest Labs pozwala lepiej zarządzać kosztami w laboratorium, ustawiając cel wydatków, który można następnie wyświetlić na wykresie Miesięczny trend szacowanego kosztu. DevTest Labs można również wysłać powiadomienie po osiągnięciu określonego wydatków docelowych lub próg. 

1. Na stronie **Trend kosztu** wybierz pozycję **Zarządzaj celem**.

    ![Przycisk Zarządzaj obiektem docelowym](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. Na stronie **Zarządzanie obiektem docelowym** określ cel wydatków i progi. Można również określić, czy każdy wybrany próg jest zgłaszany na wykresie trendu kosztów, czy za pomocą powiadomienia elementu webhook.

    ![Zarządzanie okienkem docelowym](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Wybierz okres, w którym mają być śledzone cele kosztów.
      - **Miesięczne:** cele kosztów są śledzone miesięcznie.
      - **Stała**: cele kosztów są śledzone dla zakresu dat określonego w datach rozpoczęcia i zakończenia. Zazwyczaj te wartości reprezentują, jak długo projekt jest zaplanowane do uruchomienia.
   - Określ **koszt docelowy**. Na przykład, ile planujesz wydać na to laboratorium w zdefiniowanym okresie czasu.
   - Wybierz, aby włączyć lub wyłączyć dowolny próg, który ma zostać zgłoszony – w przyrostach 25% — do 125% określonego **kosztu docelowego.**
      - **Powiadom:** Po osiągnięciu tego progu zostaniesz powiadomiony przez określony adres URL elementu webhook.
      - **Wykres na wykresie:** Po osiągnięciu tego progu wyniki są kreślone na wykresie trendu kosztów, który można wyświetlić, zgodnie z opisem w tabeli Wyświetlanie wykresu miesięcznego trendu szacowanego kosztu.
   - Jeśli wybierzesz **opcję Powiadamianie o** osiągnięciu progu, musisz określić adres URL elementu webhook. W obszarze Integracje kosztów wybierz pozycję **Kliknij tutaj, aby dodać integrację**. Wprowadź **adres URL elementu webhook** w okienku Konfigurowanie powiadomień, a następnie wybierz przycisk **OK**.

       ![Konfigurowanie okienka powiadomień](./media/devtest-lab-configure-cost-management/configure-notification.png)

     - Jeśli określisz **Powiadom,** musisz zdefiniować adres URL elementu webhook.
     - Podobnie, jeśli zdefiniujesz adres URL elementu webhook, należy ustawić **powiadomienie** **na Włączone** w okienku progu kosztu.
     - Przed wprowadzeniem go w tym miejscu należy utworzyć element webhook.  

       Aby uzyskać więcej informacji na temat elementów webhook, zobacz [Tworzenie elementu webhook lub funkcji platformy Azure interfejsu API](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

## <a name="view-cost-by-resource"></a>Wyświetlanie kosztu według zasobu 
Funkcja trendu kosztów miesięcznych w laboratoriach pozwala zobaczyć, ile wydałeś w bieżącym miesiącu kalendarzowym. Pokazuje również prognozę wydatków do końca miesiąca, w oparciu o wydatki w ciągu ostatnich siedmiu dni. Aby ułatwić zrozumienie, dlaczego wydatki w laboratorium są wczesne spełnianie progów, można użyć funkcji **koszt po zasobie,** która pokazuje miesięczny koszt **na zasób** w tabeli.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.**
3. Z listy laboratoriów wybierz żądane laboratorium.  
4. Wybierz **opcję Konfiguracja i zasady** w menu po lewej stronie.
5. Wybierz **pozycję Koszt według zasobu** w sekcji Śledzenie **kosztów** w menu po lewej stronie. Widoczne są koszty skojarzone z każdym zasobem skojarzonym z laboratorium. 

    ![Koszt według zasobu](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Ta funkcja ułatwia identyfikowanie zasobów, które kosztują najwięcej, dzięki czemu można podjąć działania w celu zmniejszenia wydatków laboratoryjnych. Na przykład koszt maszyny Wirtualnej jest oparty na rozmiarze maszyny Wirtualnej. Im większy rozmiar maszyny Wirtualnej, tym więcej jest kosztem. Można łatwo znaleźć rozmiar maszyny Wirtualnej i właściciela, dzięki czemu można porozmawiać z właścicielem maszyny Wirtualnej, aby zrozumieć, dlaczego taki rozmiar maszyny Wirtualnej jest potrzebne i czy istnieje szansa, aby zmniejszyć rozmiar.

[Zasady automatycznego zamykania](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy) pomaga zmniejszyć koszty, zamykając maszyny wirtualne laboratorium o określonej porze dnia. Jednak użytkownik laboratorium można zrezygnować z zasad zamykania systemu, co zwiększa koszt uruchomienia maszyny Wirtualnej. Można wybrać maszynę wirtualną w tabeli, aby sprawdzić, czy została zrezygnowana z zasad automatycznego zamykania. W takim przypadku można porozmawiać z właścicielem maszyny Wirtualnej, aby dowiedzieć się, dlaczego maszyna wirtualna została zrezygnowana z zasad.
 
## <a name="next-steps"></a>Następne kroki
Oto kilka rzeczy, które należy wypróbować w następnej kolejności:

* [Zdefiniuj zasady laboratorium](devtest-lab-set-lab-policy.md) — dowiedz się, jak ustawić różne zasady używane do określania sposobu, w jaki używane są twoje laboratorium i jego maszyny wirtualne. 
* [Tworzenie obrazu niestandardowego](devtest-lab-create-template.md) — podczas tworzenia maszyny Wirtualnej należy określić bazę, która może być obrazem niestandardowym lub obrazem w portalu Marketplace. W tym artykule pokazano, jak utworzyć obraz niestandardowy z pliku VHD.
* [Konfigurowanie obrazów portalu Marketplace](devtest-lab-configure-marketplace-images.md) — laboratoria DevTest obsługują tworzenie maszyn wirtualnych na podstawie obrazów w portalu Azure Marketplace. W tym artykule pokazano, jak określić, które, jeśli istnieją, obrazy portalu Azure Marketplace mogą być używane podczas tworzenia maszyn wirtualnych w laboratorium.
* [Tworzenie maszyny Wirtualnej w laboratorium](devtest-lab-add-vm.md) — ilustruje sposób tworzenia maszyny Wirtualnej z obrazu podstawowego (niestandardowego lub marketplace) i jak pracować z artefaktami na maszynie wirtualnej.

