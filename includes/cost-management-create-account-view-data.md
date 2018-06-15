---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cost-management
author: bandersmsft
ms.service: cost-management
ms.topic: include
ms.date: 04/26/2018
ms.author: banders
manager: dougeby
ms.custom: include file
ms.openlocfilehash: 1b65775ef5ad40ca9e9c1e2c96fe1c2b8d92afdc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32198859"
---
## <a name="view-cost-data"></a>Wyświetlanie danych kosztów

Rozwiązanie Azure Cost Management firmy Cloudyn zapewnia dostęp do wszystkich danych zasobów w chmurze. Pulpit nawigacyjny zawiera standardowe i niestandardowe raporty wyświetlane w widoku z kartami. Poniżej przedstawiono przykłady popularnego pulpitu nawigacyjnego i raportu, który umożliwia natychmiastowe przedstawienie danych kosztów.

![Pulpit nawigacyjny zarządzania](./media/cost-management-create-account-view-data/mgt-dash.png)

W tym przykładzie pulpit nawigacyjny zarządzania obejmuje skonsolidowane koszty wszystkich zasobów w chmurze firmy Contoso. Firma Contoso używa platformy Azure, usług AWS i usługi Google. Pulpity nawigacyjne zapewniają wgląd w informacje i umożliwiają szybkie przechodzenie do raportów.  

Jeśli nie masz pewności, jaki jest cel raportu na pulpicie nawigacyjnym, umieść kursor na symbolu **i**, aby wyświetlić wyjaśnienie. Kliknij dowolny raport na pulpicie nawigacyjnym, aby wyświetlić pełny raport.

Możesz też wyświetlać raporty za pomocą menu raportów w górnej części portalu. Spójrzmy na wydatki związane z zasobami platformy Azure w firmie Contoso z ostatnich 30 dni. Kliknij pozycję **Koszt**  >  **Analiza kosztów**  >  **Rzeczywista analiza kosztów**. Wyczyść wszelkie wartości ustawione dla tagów, grup lub filtrów w raporcie.

![Rzeczywista analiza kosztów](./media/cost-management-create-account-view-data/actual-cost-01.png)

W tym przykładzie łączny koszt wynosi 75 970 USD, a budżet wynosi 130 000 USD.

Teraz zmodyfikujemy format raportu oraz ustawimy grupy i filtry, aby zawęzić wyniki dla kosztów związanych z platformą Azure. Jako **zakres dat** ustaw ostatnich 30 dni. W prawym górnym rogu kliknij symbol kolumny, aby zastosować formatowanie wykresu słupkowego, a następnie w obszarze Grupy wybierz pozycję **Dostawca**. Następnie jako filtr **Dostawca** ustaw wartość **Azure**.

![Odfiltrowana rzeczywista analiza kosztów](./media/cost-management-create-account-view-data/actual-cost-02.png)

W tym przykładzie łączny koszt zasobów platformy Azure z ostatnich 30 dni wynosi 3839 USD.

Kliknij prawym przyciskiem myszy pasek Dostawca (Azure), a następnie przejdź do szczegółów do pozycji **Typy zasobów**.

![przejście do szczegółów](./media/cost-management-create-account-view-data/actual-cost-03.png)

Na poniższej ilustracji przedstawiono koszty zasobów platformy Azure poniesione przez firmę Contoso. Łączny koszt wynosi 3839 USD. W tym przykładzie połowa kosztów dotyczyła magazynu lokalnie nadmiarowego, a druga połowa dotyczyła różnych wystąpień maszyn wirtualnych.

![typy zasobów](./media/cost-management-create-account-view-data/actual-cost-04.png)

Kliknij prawym przyciskiem myszy typ zasobu i wybierz pozycję **Jednostki kosztów**, aby wyświetlić jednostki kosztów i usługi, które korzystały z zasobu. W tym przykładzie użycie usług maszyn wirtualnych i procesów roboczych w metodologii DevOps wyniosło 486,60 USD i 435,71 USD. Łączny koszt dla obu usług wyniósł 922 USD.

![jednostki kosztów i usługi](./media/cost-management-create-account-view-data/actual-cost-05.png)

Aby obejrzeć wideo dotyczące wyświetlania danych rozliczeń dla chmury, zobacz [Analyzing your cloud billing data with Azure Cost Management by Cloudyn](https://youtu.be/G0pvI3iLH-Y) (Analizowanie danych rozliczeń dla chmury przy użyciu rozwiązania Azure Cost Management firmy Cloudyn).
