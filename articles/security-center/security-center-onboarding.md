---
title: Dołączanie do Azure Security Center Standard na potrzeby zwiększonych zabezpieczeń | Microsoft Docs
description: " Dowiedz się, jak dołączyć do Azure Security Center Standard, aby zwiększyć bezpieczeństwo. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: memildin
ms.openlocfilehash: 01cc625767cbf437031f503cf4b0d4dc59b62e08
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996303"
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Dołączanie do Azure Security Center Standard w celu zapewnienia zwiększonych zabezpieczeń
Uaktualnij do wersji Standard Security Center, aby korzystać z ulepszonych funkcji zarządzania zabezpieczeniami i ochrony przed zagrożeniami dla obciążeń chmury hybrydowej.  Możesz wypróbować usługę w warstwie Standardowa. Aby uzyskać więcej informacji, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/) Security Center.

Standard Security Center obejmuje:

- **Bezpieczeństwo hybrydowe** — Uzyskaj ujednolicony widok zabezpieczeń we wszystkich obciążeniach lokalnych i w chmurze. Stosuj zasady zabezpieczeń i stale oceniaj bezpieczeństwo obciążeń chmury hybrydowej w celu zapewnienia zgodności ze standardami zabezpieczeń. Zbieranie, wyszukiwanie i analizowanie danych zabezpieczeń z różnych źródeł, w tym zapór i innych rozwiązań partnerskich.
- **Zaawansowane wykrywanie zagrożeń** — Użyj zaawansowanej analizy i Microsoft Intelligent Security Graph, aby uzyskać krawędzie przed rozwojem ataków cybernetycznymi.  Korzystaj z wbudowanej analizy behawioralnej i uczenia maszynowego, aby identyfikować ataki i wypróbować programy wykorzystujące zero dni. Monitoruj sieci, maszyny i usługi w chmurze pod kątem ataków przychodzących i działań po naruszeniu. Usprawnij badanie przy użyciu interaktywnych narzędzi i kontekstowej analizy zagrożeń.
- **Kontrola dostępu i aplikacji** — Zablokuj złośliwe oprogramowanie i inne niechciane aplikacje, stosując zalecenia listy dozwolonych dostosowane do konkretnych obciążeń i obsługiwane przez uczenie maszynowe. Zmniejszenie podatności na ataki sieci z dostępem just-in-Time do portów zarządzania na maszynach wirtualnych platformy Azure, co znacząco zmniejsza narażenie na odżycie i inne ataki sieciowe.

## <a name="detecting-unprotected-resources"></a>Wykrywanie zasobów niechronionych     
Security Center automatycznie wykrywa wszelkie subskrypcje platformy Azure lub obszary robocze, które nie są włączone dla Security Center Standard. Obejmuje to subskrypcje platformy Azure korzystające z Security Center bezpłatnych i obszarów roboczych, dla których nie włączono rozwiązania zabezpieczeń.

Możesz uaktualnić całą subskrypcję platformy Azure do warstwy Standardowa, która jest dziedziczona przez wszystkie obsługiwane zasoby w ramach subskrypcji. Zastosowanie warstwy Standardowa do obszaru roboczego dotyczy wszystkich zasobów raportowanych do obszaru roboczego.

> [!NOTE]
> Możesz chcieć zarządzać kosztami i ograniczyć ilość danych zbieranych dla rozwiązania, ograniczając je do określonego zestawu agentów. [Określanie celu rozwiązania](../operations-management-suite/operations-management-suite-solution-targeting.md) pozwala zastosować zakres do rozwiązania i określić podzestaw komputerów w obszarze roboczym.  W przypadku korzystania z funkcji określania wartości docelowej rozwiązania Security Center zawiera listę obszarów roboczych bez rozwiązania.
>
>

## <a name="upgrade-an-azure-subscription-or-workspace"></a>Uaktualnianie subskrypcji lub obszaru roboczego platformy Azure
Aby uaktualnić subskrypcję lub obszar roboczy do warstwy Standardowa:
1. W menu głównym Security Center wybierz opcję **wprowadzenie**.
  ![Wprowadzenie](./media/security-center-onboarding/get-started.png)
2. W obszarze **uaktualnij**Security Center wyświetla listę subskrypcji i obszarów roboczych kwalifikujących się do dołączenia. 
   - Możesz kliknąć opcję Rozwiń, aby wyświetlić listę wszystkich subskrypcji i obszarów roboczych ze stanem uprawnień **wersji próbnej** .
   -    Możesz uaktualnić subskrypcje i obszary robocze, które nie kwalifikują się do korzystania z wersji próbnej.
   -    Możesz wybrać kwalifikujące się obszary robocze i subskrypcje, aby rozpocząć okres próbny.
3.  Kliknij pozycję **Rozpocznij okres próbny** , aby rozpocząć okres próbny dla wybranych subskrypcji.
  @no__t — subskrypcja 0Select @ no__t-1


   > [!NOTE]
   > Bezpłatne możliwości Security Center są stosowane wyłącznie do maszyn wirtualnych platformy Azure i VMSS. Bezpłatne możliwości nie są stosowane do komputerów spoza platformy Azure. W przypadku wybrania opcji standardowe funkcje standardowe są stosowane do wszystkich maszyn wirtualnych platformy Azure, zestawów skalowania maszyn wirtualnych i komputerów spoza platformy Azure, które są raportowane do obszaru roboczego. Zalecamy zastosowanie standardu, aby zapewnić zaawansowane zabezpieczenia dla zasobów platformy Azure i innych niż Azure.
   >
   >

## <a name="onboard-non-azure-computers"></a>Dołączanie komputerów spoza platformy Azure
Security Center może monitorować stan zabezpieczeń na komputerach spoza platformy Azure, ale musisz najpierw dołączyć te zasoby. Komputery spoza platformy Azure można dodawać z bloku **wprowadzenie** lub w bloku **obliczenia** . Przeprowadzimy obie metody.

### <a name="add-new-non-azure-computers-from-getting-started"></a>Dodaj nowe komputery spoza platformy Azure od **początku**

1. Wróć do **początku**.   
2. Wybierz kartę **wprowadzenie** .

   ![Poza platformą Azure](./media/security-center-onboarding/non-azure.png)

3. Kliknij pozycję **Konfiguruj** w obszarze **Dodaj nowe komputery spoza platformy Azure**. Zostanie wyświetlona lista obszarów roboczych Log Analytics. Lista zawiera, jeśli ma zastosowanie, domyślny obszar roboczy utworzony przez Security Center po włączeniu automatycznej aprowizacji. Wybierz ten obszar roboczy lub inny obszar roboczy, którego chcesz użyć.

   ![Dodaj komputer spoza platformy Azure][7]

Jeśli masz istniejące obszary robocze, są one wyświetlane w obszarze **Dodaj nowe komputery spoza platformy Azure**. Możesz dodać komputery do istniejącego obszaru roboczego lub utworzyć nowy obszar roboczy. Aby utworzyć nowy obszar roboczy, wybierz łącze **Dodaj nowy obszar roboczy**.

### <a name="add-new-non-azure-computers-from-compute"></a>Dodaj nowe komputery spoza platformy Azure z **obliczeń**

**Tworzenie nowego obszaru roboczego i Dodawanie komputera**

1. W obszarze **Dodaj nowe komputery spoza platformy Azure**wybierz pozycję **Dodaj nowy obszar roboczy**.

   ![Dodaj nowy obszar roboczy][4]

2. W obszarze **Security and Audit**wybierz pozycję **obszar roboczy pakietu OMS** , aby utworzyć nowy obszar roboczy.
   > [!NOTE]
   > Obszary robocze pakietu OMS są teraz określane jako Log Analytics obszary robocze.
3. W obszarze **obszar roboczy pakietu OMS**wprowadź informacje dotyczące obszaru roboczego.
4. W obszarze **obszar roboczy pakietu OMS**wybierz pozycję **OK**.  Po wybraniu przycisku OK zostanie wyświetlony link umożliwiający pobranie agenta i kluczy systemu Windows lub Linux dla identyfikatora obszaru roboczego do użycia podczas konfigurowania agenta.
5. W obszarze **Security and Audit**wybierz pozycję **OK**.

**Wybierz istniejący obszar roboczy i Dodaj komputer**

Komputer można dodać, wykonując przepływ **pracy z**dołączania, jak pokazano powyżej. Komputer można również dodać, wykonując przepływ pracy z zasobów **obliczeniowych**. W tym przykładzie używamy **obliczeń**.

1. Wróć do menu głównego i pulpitu nawigacyjnego **przeglądu** Security Center.

   ![Omówienie][5]

2. Wybierz pozycję **obliczenia & aplikacje**.
3. W obszarze **obliczeniowe & aplikacje**wybierz pozycję **Dodaj komputery**.

   ![Blok obliczeń][6]

4. W obszarze **Dodaj nowe komputery spoza platformy Azure**wybierz obszar roboczy, z którym chcesz połączyć komputer, a następnie kliknij pozycję **Dodaj komputery**.

   ![Dodaj komputery][7]

   Blok **bezpośredni Agent** zawiera link do pobierania agenta systemu Windows lub Linux oraz identyfikator obszaru roboczego i klucze do użycia podczas konfigurowania agenta.   

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób dołączania zasobów platformy Azure i spoza platformy Azure w celu skorzystania z zaawansowanych zabezpieczeń Security Center.  Aby dowiedzieć się więcej o zasobach dołączanych, zobacz

- [Włącz zbieranie danych](security-center-enable-data-collection.md)
- [Raport analizy zagrożeń](security-center-threat-report.md)
- [Dostęp do maszyny wirtualnej just in Time](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-onboarding/onboard.png
[2]: ./media/security-center-onboarding/onboard-subscription.png
[3]: ./media/security-center-onboarding/get-started.png
[4]: ./media/security-center-onboarding/create-workspace.png
[5]: ./media/security-center-onboarding/overview.png
[6]: ./media/security-center-onboarding/compute-blade.png
[7]: ./media/security-center-onboarding/add-computer.png
[8]: ./media/security-center-onboarding/onboard-workspace.png
