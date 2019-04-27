---
title: Dołączanie do usługi Azure Security Center w warstwie standardowa celu uzyskania zwiększonych zabezpieczeń | Dokumentacja firmy Microsoft
description: " Dowiedz się, jak można dołączyć do usługi Azure Security Center w warstwie standardowa dla zwiększonych zabezpieczeń. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 19/02/2019
ms.author: monhaber
ms.openlocfilehash: ef6b476e2c85715e86ef4c1cfe1194bb25cade28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60704258"
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Dołączanie do usługi Azure Security Center w warstwie standardowa celu uzyskania zwiększonych zabezpieczeń
Uaktualnij do standardowej usługi Security Center z zalet zarządzania zwiększone zabezpieczenia i ochrona przed zagrożeniami dla obciążeń chmury hybrydowej.  Możesz spróbować standardowa jest bezpłatna. Odwiedź Centrum zabezpieczeń [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/) Aby uzyskać więcej informacji.

Security Center w warstwie standardowa obejmuje:

- **Zabezpieczenia hybrydowe** — Uzyskaj ujednolicony widok zabezpieczeń dla wszystkich lokalnych obciążeń i w chmurze. Stosowanie zasad zabezpieczeń i nieustannie oceniaj zabezpieczenia obciążeń chmury hybrydowej w celu zapewnienia zgodności ze standardami zabezpieczeń. Zbieranie, wyszukiwanie i analizowanie danych zabezpieczeń z różnych źródeł, w tym zapór i innych rozwiązań partnerskich.
- **Zaawansowane wykrywanie zagrożeń** — zaawansowane analizy i usługi Microsoft Intelligent Security Graph, aby uzyskać przewagę nad ewoluującymi cyberatakami.  Wykorzystaj wbudowaną analizę behawioralną i uczenie maszynowe do identyfikowania ataków i luk typu zero day. Monitoruj sieci, maszyny i usługi w chmurze pod kątem przychodzących ataków i działań po naruszeniu zabezpieczeń. Usprawnij badanie dzięki interaktywnym narzędziom i kontekstowej analizie zagrożeń.
- **Kontroli dostępu i aplikacji** — blokuj złośliwe oprogramowanie i inne niechciane aplikacje, stosując zalecenia dotyczące listy dozwolonych dostosowane do konkretnych obciążeń i obsługiwane przez usługi machine learning. Zmniejsz narażony na ataki sieciowe dzięki just-in-time kontrolowany dostęp do portów zarządzania na maszynach wirtualnych platformy Azure, znacząco ograniczyć narażenie na ataki siłowe i inne ataki sieciowe.

## <a name="detecting-unprotected-resources"></a>Wykrywanie niechronionych zasobach     
Usługa Security Center automatycznie wykrywa wszelkie subskrypcje i obszary robocze platformy Azure nie włączone do standardu usługi Security Center. Obejmuje to subskrypcje platformy Azure korzystające z usługi Security Center w warstwie bezpłatnej oraz obszary robocze, które nie mają włączonego rozwiązania w zakresie zabezpieczeń.

Cały subskrypcji platformy Azure można uaktualnić do warstwy standardowa, która jest dziedziczona przez wszystkie zasoby obsługiwane w ramach subskrypcji. Stosowanie standardowej warstwy do obszaru roboczego, który ma zastosowanie do wszystkich zasobów raportowania do obszaru roboczego.

> [!NOTE]
> Można zarządzać koszty i ograniczyć ilość danych zebranych dla rozwiązania przez ograniczenie go do określonego zestawu agentów. [Określanie celu rozwiązania](../operations-management-suite/operations-management-suite-solution-targeting.md) pozwala zastosować zakres z rozwiązaniem i wybierać docelowo podzbiorowi komputerów w obszarze roboczym.  Jeśli używasz rozwiązania przeznaczone dla usługi Security Center wyświetla listę obszaru roboczego jako niemający rozwiązania.
>
>

## <a name="upgrade-an-azure-subscription-or-workspace"></a>Uaktualnienie subskrypcji platformy Azure lub obszaru roboczego
Aby uaktualnić subskrypcję lub obszar roboczy na warstwę standardowa:
1. W menu głównym usługi Security Center wybierz pozycję **Wprowadzenie**.
  ![Rozpoczęcie pracy](./media/security-center-onboarding/get-started.png)
2. W obszarze **Uaktualnienie** usługa Security Center wyświetla listę subskrypcji i obszarów roboczych, które kwalifikują się do dołączenia. 
   - Możesz kliknąć listę rozwijaną **Zastosuj wersję próbną**, aby wyświetlić listę wszystkich subskrypcji i obszarów roboczych ze stanem kwalifikacji do korzystania z wersji próbnej.
   -    Możesz uaktualnić subskrypcje i obszary robocze, które nie kwalifikują się do korzystania z wersji próbnej.
   -    Możesz wybrać kwalifikujące się obszary robocze i subskrypcje i rozpocząć korzystanie z wersji próbnej.
3.  Kliknij przycisk **Rozpocznij okres próbny**, aby rozpocząć okres próbny w ramach wybranych subskrypcji.
  ![Wybierz subskrypcję](./media/security-center-onboarding/select-subscription.png)


   > [!NOTE]
   > Bezpłatnych funkcji dostępnych w Centrum zabezpieczeń są stosowane tylko do zestawu skalowania maszyn wirtualnych i maszyn wirtualnych platformy Azure. Bezpłatne funkcje nie są stosowane do komputerów spoza platformy Azure. Jeśli wybierzesz Standard, funkcje wersji standardowej są stosowane do wszystkich maszyn wirtualnych platformy Azure, zestawy skalowania maszyn wirtualnych i komputerów spoza platformy Azure wysyłających zgłoszenia do obszaru roboczego. Firma Microsoft zaleca się zastosowanie Standard w celu zapewnienia zaawansowanych zabezpieczeń platformy Azure i zasobów nienależących do platformy Azure.
   >
   >

## <a name="onboard-non-azure-computers"></a>Dodawanie komputerów spoza platformy Azure
Usługa Security Center może monitorować stan bezpieczeństwa komputerów nienależących do platformy Azure, ale musisz najpierw dołączyć te zasoby. Można dodać komputery spoza platformy Azure z **wprowadzenie** bloku lub **obliczenia** bloku. Omówimy obu tych metod.

### <a name="add-new-non-azure-computers-from-getting-started"></a>Dodać nowe komputery spoza platformy Azure z **wprowadzenie**

1. Wróć do **wprowadzenie**.   
2. Wybierz kartę **Rozpoczęcie pracy**.

   ![Poza platformą Azure](./media/security-center-onboarding/non-azure.png)

3. Kliknij przycisk **Konfiguruj** w obszarze **Dodaj nowe komputery spoza platformy Azure**. Zostanie wyświetlona lista obszarów roboczych usługi Log Analytics. Jeśli ma to zastosowanie, lista zawiera domyślny obszar roboczy utworzony przez usługę Security Center po włączeniu automatycznej aprowizacji. Wybierz ten obszar roboczy lub inny obszar roboczy, którego chcesz użyć.

   ![Dodawanie komputera spoza platformy Azure][7]

W przypadku istniejących obszarów roboczych są wymienione w obszarze **dodać nowe komputery spoza platformy Azure**. Możesz dodać komputery do istniejącego obszaru roboczego, lub Utwórz nowy obszar roboczy. Aby utworzyć nowy obszar roboczy, wybierz link **Dodaj nowy obszar roboczy**.

### <a name="add-new-non-azure-computers-from-compute"></a>Dodać nowe komputery spoza platformy Azure z **obliczeń**

**Utwórz nowy obszar roboczy i Dodaj komputer**

1. W obszarze **dodać nowe komputery spoza platformy Azure**, wybierz opcję **Dodaj nowy obszar roboczy**.

   ![Dodaj nowy obszar roboczy][4]

2. W obszarze **zabezpieczenia i inspekcja**, wybierz opcję **obszaru roboczego pakietu OMS** Aby utworzyć nowy obszar roboczy.
   > [!NOTE]
   > Obszary robocze OMS są teraz nazywane obszarami roboczymi usługi Log Analytics.
3. W obszarze **obszaru roboczego pakietu OMS**, wprowadź informacje o obszarze roboczym.
4. W obszarze **obszaru roboczego pakietu OMS**, wybierz opcję **OK**.  Po wybraniu przycisku OK otrzymasz link pobierania agenta Windows lub Linux i kluczami identyfikatora obszaru roboczego do użycia podczas konfigurowania agenta.
5. W obszarze **zabezpieczenia i inspekcja**, wybierz opcję **OK**.

**Wybierz istniejący obszar roboczy i Dodaj komputer**

Możesz dodać komputer, postępując zgodnie z przepływu pracy od **dołączania**, jak pokazano powyżej. Możesz również dodać komputer, postępując zgodnie z przepływu pracy od **obliczenia**. W tym przykładzie używamy **obliczenia**.

1. Wróć do menu głównego usługi Security Center i **Przegląd** pulpitu nawigacyjnego.

   ![Omówienie][5]

2. Wybierz **obliczenia i aplikacje**.
3. W obszarze **obliczenia i aplikacje**, wybierz opcję **dodawania komputerów**.

   ![Blok Obliczanie][6]

4. W obszarze **dodać nowe komputery spoza platformy Azure**, wybierz obszar roboczy, aby połączyć komputer, a następnie kliknij przycisk **Dodaj komputery**.

   ![Dodawanie komputerów][7]

   **Agent bezpośredni** blok zawiera również link do pobierania agenta Windows lub Linux, a także identyfikator obszaru roboczego i klucze do użycia podczas konfigurowania agenta.   

## <a name="next-steps"></a>Kolejne kroki
W tym artykule pokazano, jak dołączyć platformy Azure i zasobów nienależących do platformy Azure, aby można było korzystać z zaawansowanych zabezpieczeń w Centrum zabezpieczeń.  Aby robić więcej przy użyciu zasobów dołączone, zobacz

- [Włączanie zbierania danych](security-center-enable-data-collection.md)
- [Raport analizy zagrożeń](security-center-threat-report.md)
- [Dostęp dokładnie na czas maszyny Wirtualnej](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-onboarding/onboard.png
[2]: ./media/security-center-onboarding/onboard-subscription.png
[3]: ./media/security-center-onboarding/get-started.png
[4]: ./media/security-center-onboarding/create-workspace.png
[5]: ./media/security-center-onboarding/overview.png
[6]: ./media/security-center-onboarding/compute-blade.png
[7]: ./media/security-center-onboarding/add-computer.png
[8]: ./media/security-center-onboarding/onboard-workspace.png
