---
title: Dołączenie do usługi Azure Security Center Standard poziom zabezpieczeń | Dokumentacja firmy Microsoft
description: " Dowiedz się, jak do dołączenia do usługi Azure Security Center Standard dla zwiększonych zabezpieczeń. "
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
ms.date: 09/14/2017
ms.author: terrylan
ms.openlocfilehash: d83beecfc5a8f6b8a01c64e809bc84c6fd0238bf
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31416493"
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Dołączenie do usługi Azure Security Center Standard poziom zabezpieczeń
Uaktualnij do Security Center Standard, aby skorzystać z rozszerzonymi funkcjami zabezpieczeń, zarządzania i ochrony przed zagrożeniami dla obciążeń chmury hybrydowej.  Możesz spróbować Standard bezpłatnie do 60 dni. Zobacz temat w Centrum zabezpieczeń [cennikiem](https://azure.microsoft.com/pricing/details/security-center/) Aby uzyskać więcej informacji.

Standard Centrum zabezpieczeń obejmują:

- **Hybrydowe zabezpieczeń** — Pobierz ujednoliconego podglądu zabezpieczeń we wszystkich lokalną i obciążeń w chmurze. Zastosuj zasady zabezpieczeń i stale oceny zabezpieczeń obciążeń chmury hybrydowej w celu zapewnienia zgodności ze standardami zabezpieczeń. Zbieranie, wyszukiwanie i analizowanie danych zabezpieczeń z różnych źródeł, w tym zapór i innych rozwiązań partnerskich.
- **Zaawansowane wykrywanie zagrożeń** — zaawansowane analizy i Microsoft inteligentnego zabezpieczeń wykres tak, aby uzyskać przewagę nad zmieniających się przez atakami.  Wykorzystaj wbudowaną analizę behawioralną i uczenie maszynowe do identyfikowania ataków i luk typu zero day. Monitoruj sieci, maszyny i usługi w chmurze pod kątem przychodzących ataków i działań po naruszeniu zabezpieczeń. Usprawnij badanie dzięki interaktywnym narzędziom i kontekstowej analizie zagrożeń.
- **Kontroli dostępu i aplikacji** — blok przed złośliwym oprogramowaniem i innych niechciane aplikacje, stosując zalecenia listę dozwolonych podobnej dostosowane do określonych obciążeń i obsługiwane przez usługę uczenie maszynowe. Zmniejsz sieci ataku just in time, kontrolowany dostęp do zarządzania porty na maszynach wirtualnych Azure, co znacznie zmniejsza podatność na ataki siłowe i inne ataki sieciowe.

## <a name="detecting-unprotected-resources"></a>Wykrywanie niechronione zasobów     
Usługa Security Center automatycznie wykrywa wszelkie subskrypcje i obszary robocze platformy Azure nie włączone do standardu usługi Security Center. Obejmuje to subskrypcje platformy Azure korzystające z usługi Security Center w warstwie bezpłatnej oraz obszary robocze, które nie mają włączonego rozwiązania w zakresie zabezpieczeń.

Można uaktualnić całą subskrypcji platformy Azure do warstwy standardowa, która jest dziedziczona przez wszystkie zasoby w ramach subskrypcji, lub można określić unikatowe zasady, aby uaktualnić tylko grupy zasobów dla określonych. Jeśli ustawienia zasad grupy zasobów są unikatowe, Centrum zabezpieczeń nie spowoduje zastąpienia zasady cenowej po uaktualnieniu subskrypcji do warstwy standardowa. Stosowanie standardowego warstwy do subskrypcji ma zastosowanie tylko do maszyn wirtualnych w ramach subskrypcji, które są raportowania do obszarów roboczych utworzonych przez Centrum zabezpieczeń. Stosowanie standardowego warstwy do obszaru roboczego dotyczą wszystkich zasobów raportowania do obszaru roboczego.

> [!NOTE]
> Możesz zarządzać kosztów i ograniczyć ilość danych zebranych przez rozwiązanie ograniczając go z określonym zestawem agentów. [Celem rozwiązania](../operations-management-suite/operations-management-suite-solution-targeting.md) pozwala zastosować zakres do rozwiązania i docelowymi podzbiorowi komputerów w obszarze roboczym.  Jeśli korzystasz z rozwiązania elementów docelowych, Centrum zabezpieczeń zawiera obszar roboczy jako nie ma rozwiązania.
>
>

## <a name="upgrade-an-azure-subscription"></a>Uaktualnij subskrypcję platformy Azure
Aby uaktualnić wszystkie subskrypcje ze standardem:
1. W menu głównym Centrum zabezpieczeń, wybierz **dołączania**.
2. W obszarze **dołączania z zabezpieczeniami zaawansowanymi**, Centrum zabezpieczeń wyświetla kwalifikujących się do dołączenia do subskrypcji. Można uaktualnić wszystkie z wymienionych subskrypcji, wybierając **planu Standard zastosować**.

  ![Uaktualnij wszystkie subskrypcje][1]

Aby uaktualnić subskrypcję poszczególnych ze standardem: można uaktualnić subskrypcję z **dołączania** wybierając **zastosować warstwy standardowa**. Aby uaktualnić grupę zasobów w ramach subskrypcji na Standard, wybierz subskrypcję:
1. Wybierz subskrypcję.  **Zasady zabezpieczeń** zawiera informacje o grupie zasobów zawartych w subskrypcji.
2. Wybierz subskrypcji lub grupy zasobów.

  ![Uaktualnij wszystkie subskrypcje][2]

3. Wybierz **standardowe** uaktualnienia z wolnym do standardowego.
4. Wybierz pozycję **Zapisz**.

> [!NOTE]
> Uaktualnianie subskrypcji ze standardem spowoduje włączenie [automatyczne udostępnianie](security-center-enable-data-collection.md) Jeśli wcześniej była wyłączona. Firma Microsoft zaleca automatyczne udostępnianie monitorowania agentów.
>
>

## <a name="upgrade-a-workspace"></a>Uaktualnianie obszaru roboczego
Stosowanie normy do obszaru roboczego ma zastosowanie do wszystkich zasobów, raportowanie do obszaru roboczego.

1. Wróć do **dołączania** bloku.
2. Wybierz obszar roboczy.

  ![Uaktualnianie obszaru roboczego][8]

3. Wybierz **standardowe** do uaktualnienia.  
4. Wybierz pozycję **Zapisz**.

   > [!NOTE]
   > Scenariusz, w którym użytkownik może nie mieć wolne lub Standard stosowany do swojego obszaru roboczego nie istnieje. W przypadku wybrania wolne funkcji bezpłatne Centrum zabezpieczeń są stosowane na maszynach wirtualnych platformy Azure tylko. Bezpłatne możliwości nie są stosowane do komputerów z systemem innym niż Azure. W przypadku wybrania Standard standardowych funkcji są stosowane do wszystkich maszyn wirtualnych platformy Azure i komputerów z systemem innym niż Azure raportowania do obszaru roboczego. Firma Microsoft zaleca stosowanie Standard do zapewnienia zaawansowanych zabezpieczeń platformy Azure i zasobów innych niż Azure.
   >
   >

## <a name="onboard-non-azure-computers"></a>Dołączyć komputery z systemem innym niż Azure
Usługa Security Center może monitorować stan bezpieczeństwa komputerów nienależących do platformy Azure, ale musisz najpierw dołączyć te zasoby. Można dodać komputery z systemem innym niż Azure z **dołączania** bloku lub **obliczeniowe** bloku. Zostanie omówiony obu metod.

### <a name="add-new-non-azure-computers-from-onboarding"></a>Dodaj nowe komputery z systemem innym niż Azure z dołączania

1. Wróć do **dołączania**.   
2. Wybierz **czy chcesz dodać nowe komputery z systemem innym niż Azure**.

  ![Dodawanie komputera spoza platformy Azure][3]

Jeśli masz istniejące obszarów roboczych, są one wyświetlane w obszarze **dodać nowe komputery Non-Azure**. Można dodać komputery do istniejący obszar roboczy lub utworzyć nowy obszar roboczy. Aby utworzyć nowy obszar roboczy, wybierz łącze **Dodaj nowy obszar roboczy**.

Zostanie omówiony obu metod:

- Utwórz nowy obszar roboczy, a następnie dodaj komputer
- Wybierz istniejący obszar roboczy, a następnie dodaj komputer

**Utwórz nowy obszar roboczy, a następnie dodaj komputer**

1. W obszarze **dodać nowe komputery z systemem innym niż Azure**, wybierz pozycję **Dodaj nowy obszar roboczy**.

   ![Dodaj nowy obszar roboczy][4]

2. W obszarze **zabezpieczeń i inspekcji**, wybierz pozycję **obszarem roboczym pakietu OMS** do utworzenia nowego obszaru roboczego.
3. W obszarze **obszarem roboczym pakietu OMS**, wprowadź informacje o obszarze roboczym.
4. W obszarze **obszarem roboczym pakietu OMS**, wybierz pozycję **OK**.  Po wybraniu OK zostanie wyświetlony link pobierania agenta systemu Windows lub Linux, a klucze dla Identyfikatora obszaru roboczego do użycia podczas konfigurowania agenta.
5. W obszarze **zabezpieczeń i inspekcji**, wybierz pozycję **OK**.

**Wybierz istniejący obszar roboczy, a następnie dodaj komputer**

Można dodać komputer, wykonując działania przepływu pracy od **dołączania**, jak pokazano powyżej. Można również dodać komputer, wykonując działania przepływu pracy od **obliczeniowe**. W tym przykładzie używamy **obliczeniowe**.

1. Wróć do Centrum zabezpieczeń w menu głównym i **omówienie** pulpitu nawigacyjnego.

   ![Przegląd][5]

2. Wybierz **obliczeniowe** kafelka.
3. W obszarze **obliczeniowe**, wybierz pozycję **dodać komputery**.

   ![Blok Obliczanie][6]

4. W obszarze **dodać nowe komputery z systemem innym niż Azure**, wybierz obszar roboczy, aby połączyć komputer, a następnie kliknij przycisk **Dodaj komputery**.

   ![Dodawanie komputerów][7]

 **Bezpośredniego agenta** bloku zawiera również link do pobierania agenta systemu Windows lub Linux, a klucze dla Identyfikatora obszaru roboczego do użycia podczas konfigurowania agenta.   

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób dołączyć Azure i zasobów innych niż Azure, aby można było korzystać z zaawansowanych zabezpieczeń w Centrum zabezpieczeń.  Aby zrobić więcej z zasobów został załadowany, zobacz

- [Włączanie zbierania danych](security-center-enable-data-collection.md)
- [Raport analizy zagrożeń](security-center-threat-report.md)
- [Na wszelki dostęp maszyny Wirtualnej](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-onboarding/onboard.png
[2]: ./media/security-center-onboarding/onboard-subscription.png
[3]: ./media/security-center-onboarding/add-non-azure-resource.png
[4]: ./media/security-center-onboarding/create-workspace.png
[5]: ./media/security-center-onboarding/overview.png
[6]: ./media/security-center-onboarding/compute-blade.png
[7]: ./media/security-center-onboarding/add-non-azure-computer.png
[8]: ./media/security-center-onboarding/onboard-workspace.png
