---
title: Debugowanie mikrousług Azure w systemie Windows | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorowanie i diagnozowanie usługi napisane przy użyciu usługi sieć szkieletowa usług Microsoft Azure na maszynie lokalnej.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 93cf4985e94c0af480d9f4e2e38b792cffe4df6e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitorowanie i diagnozowanie usług w Instalatorze programowanie komputera lokalnego
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

Monitorowanie, wykrywanie, diagnozowanie i rozwiązywanie problemów z umożliwiają usług kontynuować przy minimalnym przerw w działaniu środowiska użytkownika. Podczas monitorowania i diagnostyki są szczególnie ważne w środowisku produkcyjnym wdrożony rzeczywisty, wydajność będzie zależeć od przyjęcia podobne modelu podczas tworzenia usługi, aby upewnić się, że działają one w przypadku przenoszenia do Instalatora rzeczywistych. Sieć szkieletowa usług ułatwia deweloperom usługa implementuje diagnostyki bezproblemowo działającej na konfiguracje pojedynczego komputera lokalnego rozwoju i w konfiguracji klastra produkcyjnego rzeczywistych.

## <a name="event-tracing-for-windows"></a>Śledzenie zdarzeń systemu Windows
[Śledzenie zdarzeń systemu Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) to technologia zalecane dla komunikatów śledzenia w sieci szkieletowej usług. Niektóre zalety używania funkcji ETW są:

* **ETW jest szybkie.** Został skonstruowany jako technologia śledzenia, która ma minimalny wpływ na czas wykonywania kodu.
* **Śledzenie zdarzeń systemu Windows współdziała między środowisk deweloperskich lokalne, a także konfiguracje klastra rzeczywistych.** Oznacza to, że nie masz przepisywania kod śledzenia, gdy wszystko będzie gotowe wdrożyć kod do rzeczywistego klastra.
* **Kod systemu sieci szkieletowej usług używa również ETW śledzenia wewnętrznego.** Dzięki temu można wyświetlić śladów aplikacji przeplatana z sieci szkieletowej usług systemu śledzenia. Pomaga również łatwiej zrozumieć sekwencji i wzajemne relacje między kodem aplikacji i zdarzeń w podstawowym systemie.
* **Brak obsługi wbudowanych w narzędzia Service Fabric Visual Studio Aby wyświetlić zdarzenia ETW.** Zdarzenia ETW są wyświetlane w widoku zdarzeń diagnostycznych programu Visual Studio po Visual Studio jest poprawnie skonfigurowane z sieci szkieletowej usług. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Wyświetl zdarzenia systemowe platformy Service Fabric w programie Visual Studio
Sieć szkieletowa usług emituje zdarzenia ETW, aby pomóc deweloperom aplikacji zorientować się w platformie. Jeśli jeszcze tego nie zrobiono, przejdź dalej i postępuj zgodnie z instrukcjami [tworzenie pierwszej aplikacji w programie Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md). Te informacje pomogą Ci aplikacji do pracy z Podglądu zdarzeń diagnostycznych przedstawiający komunikaty śledzenia.

1. Jeśli diagnostyki okna zdarzeń nie są automatycznie wyświetlane, przejdź do **widoku** w programie Visual Studio, wybierz pozycję **inne okna** , a następnie **podglądu zdarzeń diagnostycznych**.
2. Każdego zdarzenia zawiera informacje standardowe metadanych, które pozwalają węzła, aplikacji i usług, których pochodzi zdarzenia. Można również filtrować listę zdarzeń za pomocą **filtrować zdarzenia** u góry okna zdarzeń. Na przykład można filtrować na **nazwa węzła** lub **nazwy usługi.** I podczas przeglądania szczegóły zdarzenia, można również wstrzymać przy użyciu **wstrzymać** znajdujący się u góry okna zdarzeń i wznowić później, bez utraty zdarzeń.
   
   ![W Podglądzie zdarzeń diagnostycznych programu Visual Studio](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>Dodawanie własne niestandardowe dane śledzenia do kodu aplikacji
Szablony projektu Visual Studio sieci szkieletowej usług zawiera przykładowy kod. Kod przedstawia sposób dodawania aplikacji niestandardowej ślady ETW kodu, widoczne w podglądzie programu Visual Studio ETW obok śledzenia systemu z sieci szkieletowej usług. Zaletą tej metody jest metadanych jest automatycznie dodawany do śledzenia, czy program Visual Studio podglądu zdarzeń diagnostycznych jest już skonfigurowana do ich wyświetlania.

Dla projektów utworzone na podstawie **szablony usługi** (bezstanowych lub stateful) po prostu wyszukaj `RunAsync` implementacji:

1. Wywołanie `ServiceEventSource.Current.ServiceMessage` w `RunAsync` metody przedstawiono przykład niestandardowych śledzenia funkcji ETW z kodu aplikacji.
2. W **ServiceEventSource.cs** plików, można znaleźć przeciążenia `ServiceEventSource.ServiceMessage` metody, które mają być używane dla zdarzeń o dużej częstotliwości z powodu ze względu na wydajność.

Dla projektów utworzone na podstawie **szablony aktora** (bezstanowych lub stateful):

1. Otwórz **.cs "ProjectName"** pliku where *ProjectName* jest nazwą wybrany dla projektu programu Visual Studio.  
2. Znajdź kod `ActorEventSource.Current.ActorMessage(this, "Doing Work");` w *DoWorkAsync* metody.  To jest przykład niestandardowych śledzenia funkcji ETW z kodu aplikacji.  
3. W pliku **ActorEventSource.cs**, można znaleźć przeciążenia `ActorEventSource.ActorMessage` metody, które mają być używane dla zdarzeń o dużej częstotliwości z powodu ze względu na wydajność.

Po dodaniu niestandardowych ETW śledzenia do kodu usługi, tworzenie, wdrażanie i uruchom aplikację ponownie, aby wyświetlić zdarzenia z Podglądu zdarzeń diagnostycznych. Jeśli debugowanie aplikacji z **F5**, automatycznie zostanie wyświetlone podglądu zdarzeń diagnostycznych.

## <a name="next-steps"></a>Kolejne kroki
Ten sam kod śledzenia dodanego do aplikacji powyżej dla lokalnych diagnostyki będzie działać z narzędziami, które służy do wyświetlania tych zdarzeń, podczas uruchamiania aplikacji w klastrze platformy Azure. Zapoznaj się z tych artykułów, omówiono w nim różne opcje dla narzędzi i opisujące, jak można skonfigurować je.

* [Jak zebrać dzienniki Diagnostyka Azure](service-fabric-diagnostics-how-to-setup-wad.md)
* [Agregacja zdarzeń i kolekcji przy użyciu EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)

