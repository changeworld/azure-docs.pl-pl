---
title: Badanie użytkowników za pomocą analizy użytkowników w wersji zapoznawczej Azure przez wartownika | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu badania użytkowników za pomocą analizy użytkowników w przez wartownika platformy Azure.
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 5b06aef0-16be-4ca0-83e4-a33795a82353
ms.service: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: b9944764345f97c561f3f82f9cce9d37e204f134
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2019
ms.locfileid: "56992978"
---
# <a name="investigate-users-with-user-analytics"></a>Badanie użytkowników za pomocą analizy użytkowników

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Użytkownicy są jednostki, które mają być ściśle monitorowane. Aby uzyskać wgląd w użytkowników, przez wartownika Azure bezproblemowo integruje się z zaawansowanej ochrony przed zagrożeniami dla platformy Azure. Ta integracja umożliwia analizowanie zachowania użytkowników i ustalić ich priorytety użytkowników, którzy należy badać w pierwszym, na podstawie ich alerty i wzorców podejrzanej aktywności na platformie Azure przez wartownika i Microsoft 365.

Azure wartownik uzupełnia danych użytkownika za pomocą analiz usługi Microsoft 365, aby umożliwić użytkownika kompleksowej analizy i analizy ryzyka dla wszystkich użytkowników w usłudze Azure Active Directory. 

## <a name="how-it-works"></a>Jak to działa

1. Na podstawie zabezpieczeń analityczne reguły dopasowania zostanie wykryty przez wartownika Azure wysyła alerty do usługi Azure ATP.
1. Narzędzie Azure ATP sprawdza, czy jednostki użytkownika, które są związane z alertami, a następnie oblicza priorytet badania dla tych użytkowników.
3. Narzędzie Azure ATP następnie oblicza wynik użytkowników po jest wzbogacony przy użyciu danych z regułami analizy, dla platformy Azure przez wartownika.


## <a name="prerequisites"></a>Wymagania wstępne

- Licencję usługi Azure Advanced Threat Protection 
- Aby włączyć tę funkcję, potrzebne są uprawnienia administratora globalnego dla dzierżawy, w którym zainstalowano na platformie Azure przez wartownika

> [!NOTE]
> - Dla każdej dzierżawy usługi Azure ATP możesz połączyć jedno wystąpienie przez wartownika platformy Azure.
> - Analiza użytkownika jest obecnie dostępna tylko dla użytkowników usługi Azure Active Directory. 

## <a name="enable-user-analytics"></a>Włącz analizę użytkownika

1. Aby włączyć funkcje analityczne użytkownika w usłudze Azure przez wartownika, w portalu przejdź do **analizy użytkowników** strony, a następnie kliknij przycisk **Włącz**. Spowoduje to wysłanie informacji na temat obszaru roboczego do usługi Azure ATP.

1. Następnie jego przejście do usługi Azure ATP. W obszarze **rozszerzeń zabezpieczeń** w **wskaźnikowe systemu Microsoft Azure** kliknij pozycję **+ plus** Aby dodać, a następnie wybierz obszar roboczy. 
1. Kliknij przycisk **Połącz**.

## <a name="investigate-a-user"></a>Badanie użytkownika

1. W folderze, w menu platformy Azure przez wartownika **analizy użytkowników**, zapoznaj się z listą użytkowników według ich priorytetu badania. Wynik zależy od platformy Azure przez wartownika alertów i alertów usługi Microsoft 365.

2. Wyszukaj użytkownika, który chcesz zbadać. Kliknij użytkownika, aby przejść do strony użytkownika. Przejrzyj działania i alerty, użytkownika wraz z upływem czasu na osi czasu. Możesz zobaczyć wszystkie działania z Microsoft 365 i Azure przez wartownika. Zostanie również wyświetlona strona użytkownika przez przechodzenie dla użytkowników w przypadku.
      
    ![Użytkownicy](./media/user-analytics/user-investigation.png)

 
3. Aby to działało poprawnie, należy poprawnie skonfigurować [niestandardową regułę alertu](tutorial-detect-threats.md) do mapowania identyfikatorów użytkownika odpowiedni do **konta** jednostki.

    - Dla zdarzeń Windows mapy **konta** do **identyfikatora SID**
    - Mapowanie danych platformy Azure AD (która znajduje się w wielu dzienników aktywności platformy Azure w tym) lub dane usługi Office 365, **konta** właściwości **GUID**, lub **główna nazwa użytkownika**. 

   ![Zapytanie](./media/user-analytics/query-window.png)



Na przykład: 
> [!NOTE]
> W dziennikach aktywności aktywności platformy Azure podmiot wywołujący zawiera nazwę UPN.

1. To zapytanie wyszukuje tworzenia nietypową liczbę zasobów lub wdrażania działań w dzienniku aktywności platformy Azure.

        AzureActivity      
        | where TimeGenerated >= startofday(ago(7d))        
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"        
        | where ActivityStatus == "Succeeded"        
        | make-series dResourceCount=dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(startofday(ago(7d)), now(), 1d) by Caller        
        | extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(dResourceCount)        
        | where Slope > 0.2        
        | join kind=leftsemi (        
        // Last day's activity is anomalous        
        AzureActivity        
        | where TimeGenerated >= startofday(ago(1d))        
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"        
        | where ActivityStatus == "Succeeded"        
        | make-series dResourceCount=dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(startofday(ago(1d)), now(), 1d) by Caller        
        | extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(dResourceCount)        
        | where Slope >0.2        
        ) on Caller        
        | extend AccountCustomEntity = Caller
 
2. W niestandardowej reguły alertu mapować **konta** właściwości **obiekt wywołujący**.
   
   ![Zapytanie](./media/user-analytics/query-window.png)

3. Zbadaj użytkownika w oknie badania użytkownika. Aby uzyskać porady na temat sposobu badania użytkowników, zobacz [samouczka: Badanie użytkownika](https://docs.microsoft.com/azure-advanced-threat-protection/investigate-a-user).



## <a name="next-steps"></a>Kolejne kroki

W tym dokumencie przedstawiono sposób nawiązać połączenie z dostawcą analizy zagrożeń usługi Azure przez wartownika. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły.

- Aby rozpocząć korzystanie z platformy Azure przez wartownika, musisz mieć subskrypcji platformy Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
- Dowiedz się, jak [dołączyć dane do platformy Azure przez wartownika](quickstart-onboard.md), i [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
