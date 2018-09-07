---
title: Usługa Azure Monitor w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Więcej informacji na temat usługi Azure Monitor w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: mabrigg
ms.openlocfilehash: b0cf2d7856a78bbe2aa531c6e872168e8e33b06a
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "44021815"
---
# <a name="azure-monitor-on-azure-stack"></a>Usługa Azure Monitor w usłudze Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Ten artykuł zawiera omówienie usługi Azure Monitor w usłudze Azure Stack. Omówiono w nim działania usługi Azure Monitor i dodatkowe informacje na temat korzystania z usługi Azure Monitor w usłudze Azure Stack. 

Wprowadzenie omówienie i jak rozpocząć pracę z usługą Azure Monitor, artykuł globalnej platformy Azure [Rozpoczynanie pracy z usługą Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started).

![Blok usługi Azure Monitor stosu](./media/azure-stack-metrics-azure-data/azs-monitor.png)

Usługa Azure Monitor to usługa platformy, która zapewnia jedno źródło monitorowania zasobów systemu Azure. Dzięki usłudze Azure Monitor może wizualizacji, zapytania, trasy, archiwum i w przeciwnym razie wykonaj działania dotyczące metryk i dzienników pochodzących z zasobów na platformie Azure. Te dane mogą pracować przy użyciu portalu administracyjnego usługi Azure Stack, poleceń cmdlet programu PowerShell monitora, Międzyplatformowego interfejsu wiersza polecenia lub interfejsów API REST usługi Azure Monitor. Określone łączności, obsługiwane przez usługę Azure Stack, zobacz [sposobu korzystania z danych monitorowania z usługi Azure Stack](azure-stack-metrics-monitor.md)

> [!Note]  
Metryki i dzienniki diagnostyczne nie są dostępne dla usługi Azure Stack Development Kit.

## <a name="prerequisites"></a>Wymagania wstępne

Zarejestruj **Microsoft.insights** dostawcy zasobów na ustawienia dostawcy zasobów oferty Twojej subskrypcji. Aby sprawdzić, czy dostawcy zasobów jest dostępna w ofercie skojarzonych z subskrypcją:

1. Otwórz portal administracyjny usługi Azure Stack.
2. Wybierz **oferuje**.
3. Wybierz oferty, skojarzone z tą subskrypcją.
4. Wybierz **dostawców zasobów** w obszarze **ustawienia.** 
5. Znajdź **Microsoft.Insights** na liście i sprawdź, że jest w stanie **zarejestrowana.**.

## <a name="overview"></a>Przegląd

Takich jak Azure Monitor na platformie Azure usługi Azure Monitor w usłudze Azure Stack zapewnia podstawowy poziom infrastrukturą metryk i dzienników dla większości usług.

## <a name="azure-monitor-sources-compute-subset"></a>Usługa Azure monitor źródeł: obliczenia podzbioru

![Usługa Azure monitor źródła - podzbioru obliczeń](media//azure-stack-metrics-azure-data/azs-monitor-computersubset.png)

**Microsoft.Compute** dostawcy zasobów w usłudze Azure Stack obejmuje:
 - Maszyny wirtualne 
 - Zestawy skalowania maszyn wirtualnych

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>Aplikacja — dzienniki diagnostyczne, dzienniki aplikacji i metryki

Aplikacje mogą być uruchamiane w systemie operacyjnym maszyny wirtualnej z systemem **Microsoft.Compute** dostawcy zasobów. Te aplikacje i maszyn wirtualnych emitowania własnych zestawów dzienniki i metryki. Usługa Azure Monitor zbiera większość metryk i dzienników na poziomie aplikacji w oparciu o rozszerzenie diagnostyki platformy Azure (Windows lub Linux). 

Typy działań:
 - Liczniki wydajności
 - Dzienniki aplikacji
 - Dzienniki zdarzeń systemu Windows
 - Źródło zdarzenia platformy .NET
 - Dzienniki usług IIS
 - ETW oparte na manifeście
 - Zrzuty awaryjne
 - Dzienniki błędów klienta

> [!Note]  
> Rozszerzenie diagnostyki systemu Linux w usłudze Azure Stack nie są obsługiwane.

### <a name="host-and-guest-vm-metrics"></a>Metryki hosta i maszyny wirtualnej gościa

Zasoby obliczeniowe wymienione wcześniej mają dedykowanego hosta maszyny Wirtualnej i systemu operacyjnego gościa. Maszyna wirtualna hosta oraz system operacyjny gościa są odpowiednikami główną maszynę Wirtualną i gościa maszyny Wirtualnej w funkcji hypervisor Hyper-V. Można zbierać metryki dla systemu operacyjnego gościa i hostów maszyn wirtualnych. Ponadto można zebrać dzienniki diagnostyczne dla systemu operacyjnego gościa. Lista kolekcjonowane metryki dla metryki hosta i gościa maszyny Wirtualnej w usłudze Azure Stack są dostępne pod adresem [metryki obsługiwane z usługą Azure Monitor w usłudze Azure Stack](azure-stack-metrics-supported.md). 

### <a name="activity-log"></a>Dziennik aktywności

Można przeszukiwać dzienniki aktywności, aby uzyskać informacje dotyczące zasobów obliczeniowych widziany przez infrastrukturę usługi Azure Stack. Dziennik zawiera takie informacje, jak czas utworzenia lub zniszczenia zasobów. Dzienniki aktywności w usłudze Azure Stack jest zgodna z platformą Azure. Aby uzyskać więcej informacji, zobacz opis [działanie w dzienniku — omówienie na platformie Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). 


## <a name="azure-monitor-sources-everything-else"></a>Usługa Azure monitor źródeł: wszystkie inne elementy

![Źródła usługi Azure monitor — wszystko inne](media//azure-stack-metrics-azure-data/azs-monitor-othersubset.png)

### <a name="resources---metrics-and-diagnostics-logs"></a>Rejestruje zasoby — metryki i Diagnostyka

Kolekcjonowane dzienniki metryki i Diagnostyka różnią się zależnie od typu zasobu. Lista kolekcjonowane metryki dla każdego zasobu w usłudze Azure Stack jest dostępna na obsługiwanych metryk. Aby uzyskać więcej informacji, zobacz [metryki obsługiwane z usługą Azure Monitor w usłudze Azure Stack](azure-stack-metrics-supported.md).

### <a name="activity-log"></a>Dziennik aktywności

Dziennik aktywności jest taka sama dla zasobów obliczeniowych. 

### <a name="uses-for-monitoring-data"></a>Zastosowań danych monitorowania

**Store i archiwum**  

Niektóre dane monitorowania są już przechowywane i dostępne w usłudze Azure Monitor przez określony okres czasu. 
 - Metryki są przechowywane przez 90 dni. 
 - Wpisy dziennika aktywności są przechowywane przez 90 dni. 
 - Dzienniki diagnostyczne nie są przechowywane.
 - Archiwizowanie danych do konta magazynu w celu dłuższy okres przechowywania.

**Zapytanie**  

Dostęp do danych w systemie lub w usłudze Azure storage, można użyć interfejsu API REST usługi Azure Monitor, poleceń interfejsu wiersza polecenia (CLI) dla wielu platform, poleceń cmdlet programu PowerShell lub zestawu .NET SDK. 

**Wizualizacja**

Wizualizacja danych monitorowania w postaci graficznej i na wykresach umożliwia szybsze znajdowanie trendów niż w przypadku przeglądania danych. 

Kilka metod wizualizacji obejmuje:
 - Korzystanie z portalu użytkowników i administratorów usługi Azure Stack
 - Przesyłanie danych do usługi Microsoft Power BI
 - Kierowanie danych do narzędzia wizualizacji innej firmy przy użyciu transmisji strumieniowej na żywo lub przez włączenie w narzędziu funkcji odczytywania z archiwum w magazynie Azure

## <a name="methods-of-accessing-azure-monitor-on-azure-stack"></a>Metody dostępu do platformy Azure, monitorowanie w usłudze Azure Stack

Ogólnie rzecz biorąc, można manipulować śledzeniem, kierowaniem i pobieraniem danych przy użyciu jednej z poniższych metod. Nie wszystkie metody są dostępne dla wszystkich akcji lub typów danych.

 - [Portal usługi Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-use-portal)
 - [Program PowerShell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples)
 - [Interface(CLI) wiersza polecenia dla wielu platform](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples)
 - [Interfejs API REST](https://docs.microsoft.com/rest/api/monitor)
 - [Zestaw SDK platformy .NET](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat opcji monitorowania zużycia danych w usłudze Azure Stack w artykule [zużywania dane monitorowania z usługi Azure Stack](azure-stack-metrics-monitor.md).
