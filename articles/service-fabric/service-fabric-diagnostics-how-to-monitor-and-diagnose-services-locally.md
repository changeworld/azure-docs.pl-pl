---
title: Debugowanie aplikacji sieci szkieletowej usług Azure w systemie Windows
description: Dowiedz się, jak monitorować i diagnozować usługi napisane przy użyciu sieci szkieletowej usługi Microsoft Azure na lokalnym komputerze deweloperskim.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 8435bb82afddd0070679768bb8d22ad9290f2279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258514"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitor and diagnose services in a local machine development setup (Monitorowanie i diagnozowanie usług w konfiguracji środowiska deweloperskiego na maszynie lokalnej)
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

Monitorowanie, wykrywanie, diagnozowanie i rozwiązywanie problemów umożliwiają usługom kontynuowanie z minimalnymi zakłóceniami w doświadczeniu użytkownika. Podczas monitorowania i diagnostyki mają kluczowe znaczenie w rzeczywistym środowisku produkcyjnym wdrożone, wydajność będzie zależeć od przyjęcia podobnego modelu podczas opracowywania usług, aby upewnić się, że działają one po przejściu do rzeczywistych konfiguracji. Sieć szkieletowa usług ułatwia deweloperom usług implementowanie diagnostyki, która może bezproblemowo pracować zarówno w konfiguracjach lokalnego programowania jednoprzez, jak i w rzeczywistych konfiguracjach klastra produkcyjnego.

## <a name="event-tracing-for-windows"></a>Śledzenie zdarzeń systemu Windows
[Śledzenie zdarzeń dla systemu Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) jest zalecaną technologią śledzenia wiadomości w sieci szkieletowej usług. Niektóre korzyści z używania ETW to:

* **ETW jest szybki.** Został on zbudowany jako technologia śledzenia, która ma minimalny wpływ na czas wykonywania kodu.
* **Śledzenie ETW działa bezproblemowo w lokalnych środowiskach programistyzacyjnych, a także w rzeczywistych konfiguracjach klastrów.** Oznacza to, że nie trzeba przepisać kod śledzenia, gdy jesteś gotowy do wdrożenia kodu do prawdziwego klastra.
* **Kod systemu sieci szkieletowej usług używa również ETW do śledzenia wewnętrznego.** Dzięki temu można wyświetlić ślady aplikacji przeplatane śladami systemu sieci szkieletowej usług. Pomaga również łatwiej zrozumieć sekwencje i wzajemne powiązania między kodem aplikacji i zdarzeniami w systemie źródłowym.
* **Istnieje wbudowana obsługa w narzędziach usługi Sieci szkieletowej programu Visual Studio do wyświetlania zdarzeń ETW.** Zdarzenia ETW są wyświetlane w widoku zdarzenia diagnostyczne programu Visual Studio, gdy program Visual Studio jest poprawnie skonfigurowany za pomocą sieci szkieletowej usług. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Wyświetlanie zdarzeń systemu sieci szkieletowej usług w programie Visual Studio
Usługa Fabric emituje zdarzenia ETW, aby pomóc deweloperom aplikacji zrozumieć, co dzieje się na platformie. Jeśli jeszcze tego nie zrobiono, wykonaj czynności opisane w programie [Creating pierwszej aplikacji w programie Visual Studio.](service-fabric-tutorial-create-dotnet-app.md) Te informacje pomogą Ci uruchomić aplikację z podglądem zdarzeń diagnostyki przedstawiającym komunikaty śledzenia.

1. Jeśli okno zdarzeń diagnostycznych nie jest automatycznie wyświetlane, przejdź do karty **Widok** w programie Visual Studio, wybierz pozycję **Inne okna,** a następnie **Przeglądarkę zdarzeń diagnostycznych**.
2. Każde zdarzenie ma standardowe informacje o metadanych, które informują o węźle, aplikacji i usłudze, z której pochodzi zdarzenie. Listę zdarzeń można również filtrować, korzystając z pola **Filtruj zdarzenia** u góry okna zdarzeń. Na przykład można filtrować nazwę **węzła** lub **nazwę usługi.** A gdy patrzysz na szczegóły zdarzenia, możesz również wstrzymać, używając przycisku **Wstrzymaj** u góry okna zdarzeń i wznowić później bez utraty zdarzeń.
   
   ![Podgląd zdarzeń diagnostyki programu Visual Studio](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>Dodawanie własnych śladów niestandardowych do kodu aplikacji
Szablony projektów usługi Sieci szkieletowej programu Visual Studio zawierają przykładowy kod. Kod pokazuje, jak dodać niestandardowy kod aplikacji ŚLADY ETW, które pojawiają się w przeglądarce ETW programu Visual Studio obok śladów systemu z sieci szkieletowej usług. Zaletą tej metody jest to, że metadane są automatycznie dodawane do śledzenia, a Podgląd zdarzeń diagnostycznych programu Visual Studio jest już skonfigurowany do ich wyświetlania.

Dla projektów utworzonych na podstawie **szablonów usług** (bezstanowych lub stanowych) wystarczy wyszukać `RunAsync` implementację:

1. Wywołanie `ServiceEventSource.Current.ServiceMessage` w `RunAsync` metodzie pokazuje przykład niestandardowego śledzenia ETW z kodu aplikacji.
2. W pliku **ServiceEventSource.cs** znajdziesz przeciążenie `ServiceEventSource.ServiceMessage` metody, która powinna być używana dla zdarzeń o wysokiej częstotliwości ze względu na wydajność.

Dla projektów utworzonych na podstawie **szablonów aktora** (bezstanowych lub stanowych):

1. Otwórz plik **"ProjectName".cs,** w którym *ProjectName* to nazwa wybrana dla projektu programu Visual Studio.  
2. Znajdź kod `ActorEventSource.Current.ActorMessage(this, "Doing Work");` w *DoWorkAsync* metody.  Jest to przykład niestandardowego śledzenia ETW napisanego z kodu aplikacji.  
3. W **pliku ActorEventSource.cs**, znajdziesz przeciążenie `ActorEventSource.ActorMessage` dla metody, która powinna być używana dla zdarzeń o wysokiej częstotliwości ze względu na wydajność.

Po dodaniu niestandardowego śledzenia ETW do kodu usługi można ponownie skompilować, wdrożyć i uruchomić aplikację, aby wyświetlić zdarzenia w Podglądzie zdarzeń diagnostycznych. Jeśli debugujesz aplikację za pomocą **F5,** podgląd zdarzeń diagnostycznych otworzy się automatycznie.

## <a name="next-steps"></a>Następne kroki
Ten sam kod śledzenia, który został dodany do aplikacji powyżej dla diagnostyki lokalnej będzie współpracować z narzędziami, których można użyć do wyświetlania tych zdarzeń podczas uruchamiania aplikacji w klastrze platformy Azure. Zapoznaj się z tymi artykułami, w których omówiono różne opcje narzędzi i opisano, jak można je skonfigurować.

* [Jak zbierać dzienniki za pomocą usługi Azure Diagnostics](service-fabric-diagnostics-how-to-setup-wad.md)
* [Agregacja i zbieranie zdarzeń przy użyciu usługi EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)

