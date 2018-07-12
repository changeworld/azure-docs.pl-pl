---
title: Dołączanie do usługi Azure Security Center w warstwie standardowa celu uzyskania zwiększonych zabezpieczeń | Dokumentacja firmy Microsoft
description: " Dowiedz się, jak można dołączyć do usługi Azure Security Center w warstwie standardowa dla zwiększonych zabezpieczeń. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2018
ms.author: terrylan
ms.openlocfilehash: a68c0ecc15f81efe6045f4c298b48f9809916297
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38479465"
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Dołączanie do usługi Azure Security Center w warstwie standardowa celu uzyskania zwiększonych zabezpieczeń
Uaktualnij do standardowej usługi Security Center z zalet zarządzania zwiększone zabezpieczenia i ochrona przed zagrożeniami dla obciążeń chmury hybrydowej.  Możesz wypróbować bezpłatnie przez 60 dni Standard. Odwiedź Centrum zabezpieczeń [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/) Aby uzyskać więcej informacji.

Security Center w warstwie standardowa obejmuje:

- **Zabezpieczenia hybrydowe** — Uzyskaj ujednolicony widok zabezpieczeń dla wszystkich lokalnych obciążeń i w chmurze. Stosowanie zasad zabezpieczeń i nieustannie oceniaj zabezpieczenia obciążeń chmury hybrydowej w celu zapewnienia zgodności ze standardami zabezpieczeń. Zbieranie, wyszukiwanie i analizowanie danych zabezpieczeń z różnych źródeł, w tym zapór i innych rozwiązań partnerskich.
- **Zaawansowane wykrywanie zagrożeń** — zaawansowane analizy i usługi Microsoft Intelligent Security Graph, aby uzyskać przewagę nad ewoluującymi cyberatakami.  Wykorzystaj wbudowaną analizę behawioralną i uczenie maszynowe do identyfikowania ataków i luk typu zero day. Monitoruj sieci, maszyny i usługi w chmurze pod kątem przychodzących ataków i działań po naruszeniu zabezpieczeń. Usprawnij badanie dzięki interaktywnym narzędziom i kontekstowej analizie zagrożeń.
- **Kontroli dostępu i aplikacji** — blokuj złośliwe oprogramowanie i inne niechciane aplikacje, stosując zalecenia dotyczące listy dozwolonych dostosowane do konkretnych obciążeń i obsługiwane przez usługi machine learning. Zmniejsz narażony na ataki sieciowe dzięki just-in-time kontrolowany dostęp do portów zarządzania na maszynach wirtualnych platformy Azure, znacząco ograniczyć narażenie na ataki siłowe i inne ataki sieciowe.

## <a name="detecting-unprotected-resources"></a>Wykrywanie niechronionych zasobach     
Usługa Security Center automatycznie wykrywa wszelkie subskrypcje i obszary robocze platformy Azure nie włączone do standardu usługi Security Center. Obejmuje to subskrypcje platformy Azure korzystające z usługi Security Center w warstwie bezpłatnej oraz obszary robocze, które nie mają włączonego rozwiązania w zakresie zabezpieczeń.

Całej subskrypcji platformy Azure można uaktualnić do warstwy standardowa, która jest dziedziczona przez wszystkie zasoby w ramach subskrypcji, lub można zdefiniować unikatowe zasady do konkretnej grupy zasobów tylko uaktualnienia. Jeśli ustawienia zasad grupy zasobów są unikatowe, usługa Security Center nie spowoduje zastąpienia polityką cenową podczas uaktualniania subskrypcji do warstwy standardowa. Stosowanie standardowej warstwy do subskrypcji ma zastosowanie tylko do maszyn wirtualnych w ramach subskrypcji, które przesyłają raporty do obszarów roboczych utworzonych przez usługę Security Center. Stosowanie standardowej warstwy do obszaru roboczego, który ma zastosowanie do wszystkich zasobów raportowania do obszaru roboczego.

> [!NOTE]
> Można zarządzać koszty i ograniczyć ilość danych zebranych dla rozwiązania przez ograniczenie go do określonego zestawu agentów. [Określanie celu rozwiązania](../operations-management-suite/operations-management-suite-solution-targeting.md) pozwala zastosować zakres z rozwiązaniem i wybierać docelowo podzbiorowi komputerów w obszarze roboczym.  Jeśli używasz rozwiązania przeznaczone dla usługi Security Center wyświetla listę obszaru roboczego jako niemający rozwiązania.
>
>

## <a name="upgrade-an-azure-subscription"></a>Uaktualnienie subskrypcji platformy Azure
Aby uaktualnić wszystkie subskrypcje do warstwy standardowa:
1. W menu głównym usługi Security Center wybierz pozycję **Dołączanie do zabezpieczeń zaawansowanych**.
2. W obszarze **dołączanie do zabezpieczeń zaawansowanych**, usługa Security Center wyświetla listę subskrypcji, które kwalifikują się do dołączenia. Można uaktualnić wszystkie z wymienionych subskrypcji, wybierając **zastosować standardowy plan**.

  ![Uaktualnij wszystkie subskrypcje][1]

Aby przejść z indywidualnej subskrypcji na warstwę standardowa: można uaktualnić subskrypcję z **dołączania** , wybierając **zastosować warstwy standardowa**. Aby uaktualnić grupę zasobów w ramach subskrypcji na warstwę standardowa, wybierz subskrypcję:
1. Wybierz subskrypcję.  **Zasady zabezpieczeń** zawiera informacje dotyczące grupy zasobów zawartych w subskrypcji.
2. Wybierz subskrypcję lub grupę zasobów.

  ![Uaktualnij wszystkie subskrypcje][2]

3. Wybierz **standardowa** Aby zmienić warstwę bezpłatna do warstwy standardowa.
4. Wybierz pozycję **Zapisz**.

> [!NOTE]
> Uaktualnienie subskrypcji do warstwy standardowa, aby włączyć [automatycznej aprowizacji](security-center-enable-data-collection.md) Jeśli wcześniej była wyłączona. Firma Microsoft zaleca automatyczną aprowizację agentów monitorowania.
>
>

## <a name="upgrade-a-workspace"></a>Uaktualnianie obszaru roboczego
Stosowanie normy, do obszaru roboczego ma zastosowanie do wszystkich zasobów raportowania do obszaru roboczego.

1. Wróć do **dołączania** bloku.
2. Wybierz obszar roboczy.

  ![Uaktualnianie obszaru roboczego][8]

3. Wybierz **standardowa** do uaktualnienia.  
4. Wybierz pozycję **Zapisz**.

   > [!NOTE]
   > Jest to scenariusz, gdzie możesz nie mieć bezpłatnej lub Standard stosowany do swojego obszaru roboczego. Jeśli wybierzesz bezpłatna, bezpłatnych funkcji dostępnych w Centrum zabezpieczeń są stosowane na maszynach wirtualnych platformy Azure tylko. Bezpłatne funkcje nie są stosowane do komputerów spoza platformy Azure. Jeśli wybierzesz Standard, funkcje wersji standardowej są stosowane do wszystkich maszyn wirtualnych platformy Azure i komputerów spoza platformy Azure wysyłających zgłoszenia do obszaru roboczego. Firma Microsoft zaleca się zastosowanie Standard w celu zapewnienia zaawansowanych zabezpieczeń platformy Azure i zasobów nienależących do platformy Azure.
   >
   >

## <a name="onboard-non-azure-computers"></a>Dodawanie komputerów nienależących do platformy Azure
Usługa Security Center może monitorować stan bezpieczeństwa komputerów nienależących do platformy Azure, ale musisz najpierw dołączyć te zasoby. Można dodać komputery spoza platformy Azure z **dołączania** bloku lub **obliczenia** bloku. Omówimy obu tych metod.

### <a name="add-new-non-azure-computers-from-onboarding"></a>Dodać nowe komputery spoza platformy Azure z dołączania

1. Wróć do **dołączania**.   
2. Wybierz **czy chcesz dodać nowe komputery spoza platformy Azure**.

  ![Dodawanie komputera spoza platformy Azure][3]

W przypadku istniejących obszarów roboczych są wymienione w obszarze **dodać nowe komputery spoza platformy Azure**. Możesz dodać komputery do istniejącego obszaru roboczego, lub Utwórz nowy obszar roboczy. Aby utworzyć nowy obszar roboczy, wybierz link **Dodaj nowy obszar roboczy**.

Omówimy obu tych metod:

- Utwórz nowy obszar roboczy i Dodaj komputer
- Wybierz istniejący obszar roboczy i Dodaj komputer

**Utwórz nowy obszar roboczy i Dodaj komputer**

1. W obszarze **dodać nowe komputery spoza platformy Azure**, wybierz opcję **Dodaj nowy obszar roboczy**.

   ![Dodaj nowy obszar roboczy][4]

2. W obszarze **zabezpieczenia i inspekcja**, wybierz opcję **obszaru roboczego pakietu OMS** Aby utworzyć nowy obszar roboczy.
3. W obszarze **obszaru roboczego pakietu OMS**, wprowadź informacje o obszarze roboczym.
4. W obszarze **obszaru roboczego pakietu OMS**, wybierz opcję **OK**.  Po wybraniu przycisku OK otrzymasz link pobierania agenta Windows lub Linux i kluczami identyfikatora obszaru roboczego do użycia podczas konfigurowania agenta.
5. W obszarze **zabezpieczenia i inspekcja**, wybierz opcję **OK**.

**Wybierz istniejący obszar roboczy i Dodaj komputer**

Możesz dodać komputer, postępując zgodnie z przepływu pracy od **dołączania**, jak pokazano powyżej. Możesz również dodać komputer, postępując zgodnie z przepływu pracy od **obliczenia**. W tym przykładzie używamy **obliczenia**.

1. Wróć do menu głównego usługi Security Center i **Przegląd** pulpitu nawigacyjnego.

   ![Przegląd][5]

2. Wybierz **obliczenia i aplikacje**.
3. W obszarze **obliczenia i aplikacje**, wybierz opcję **dodawania komputerów**.

   ![Blok Obliczanie][6]

4. W obszarze **dodać nowe komputery spoza platformy Azure**, wybierz obszar roboczy, aby połączyć komputer, a następnie kliknij przycisk **Dodaj komputery**.

   ![Dodawanie komputerów][7]

 **Agent bezpośredni** blok zawiera również link do pobierania agenta Windows lub Linux i kluczami identyfikatora obszaru roboczego do użycia podczas konfigurowania agenta.   

## <a name="next-steps"></a>Kolejne kroki
W tym artykule pokazano, jak dołączyć platformy Azure i zasobów nienależących do platformy Azure, aby można było korzystać z zaawansowanych zabezpieczeń w Centrum zabezpieczeń.  Aby robić więcej przy użyciu zasobów dołączone, zobacz

- [Włączanie zbierania danych](security-center-enable-data-collection.md)
- [Raport analizy zagrożeń](security-center-threat-report.md)
- [Dostęp dokładnie na czas maszyny Wirtualnej](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-onboarding/onboard.png
[2]: ./media/security-center-onboarding/onboard-subscription.png
[3]: ./media/security-center-onboarding/add-non-azure-resource.png
[4]: ./media/security-center-onboarding/create-workspace.png
[5]: ./media/security-center-onboarding/overview.png
[6]: ./media/security-center-onboarding/compute-blade.png
[7]: ./media/security-center-onboarding/add-non-azure-computer.png
[8]: ./media/security-center-onboarding/onboard-workspace.png
