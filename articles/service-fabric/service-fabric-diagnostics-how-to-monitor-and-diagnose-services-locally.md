---
title: Debugowanie usługi Azure Service Fabric aplikacje w Windows | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować i diagnozować usługi napisane przy użyciu usługi Microsoft Azure Service Fabric na lokalnej maszynie do programowania.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 31c559c1ab314b7e1f29bd96f74d6d82cfcc0420
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60392849"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitorowanie i diagnozowanie usług w lokalnym komputerze deweloperskim
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

Monitorowanie, wykrywanie, diagnozowanie i rozwiązywanie problemów z umożliwiają usług kontynuować przy minimalnym zakłóceniu środowisko użytkownika. Podczas monitorowania i diagnostyki są szczególnie ważne w środowisku rzeczywistej produkcji wdrożone, wydajność będzie zależeć od przyjęcia podobne modelu podczas tworzenia usługi, aby upewnić się, że działają one w przypadku przenoszenia do instalacji rzeczywistych. Usługa Service Fabric można łatwo dla deweloperów usługi do zaimplementowania diagnostyki, która bezproblemowo mogą korzystać z różnych ustawień rozwoju lokalnego pojedynczego komputera i konfiguracji klastra produkcyjnego w rzeczywistych warunkach.

## <a name="event-tracing-for-windows"></a>Śledzenie zdarzeń systemu Windows
[Śledzenie zdarzeń dla Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) to technologia zalecane komunikaty dotyczące śledzenia w usłudze Service Fabric. Niektóre korzyści z używania funkcji ETW są:

* **ETW jest szybkie.** Została skompilowana jako technologia śledzenia, która ma minimalny wpływ na czas realizacji kodu.
* **Śledzenie bezproblemowo współpracuje w środowiskach programowania lokalnego i również konfiguracje klastra w rzeczywistych warunkach.** Oznacza to, że nie trzeba ponownie pisać kodu śledzenia, gdy wszystko jest gotowe do wdrożenia kodu rzeczywistym klastrem.
* **Usługa Service Fabric systemu kod używa również ETW śledzenia wewnętrznego.** Dzięki temu można wyświetlić za pomocą usługi Service Fabric systemu ślady z przeplotem śladów aplikacji. W tym obszarze pomaga też łatwiej zrozumieć sekwencji i współzależności między kodem aplikacji i zdarzeń w systemie podstawowym.
* **Brak wbudowanej obsługi w narzędziach usługi Service Fabric programu Visual Studio, aby wyświetlić zdarzenia ETW.** Zdarzenia ETW są wyświetlane w widoku zdarzeń diagnostycznych programu Visual Studio, gdy program Visual Studio jest poprawnie skonfigurowana przy użyciu usługi Service Fabric. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Wyświetlać zdarzenia systemowe Service Fabric w programie Visual Studio
Usługa Service Fabric emituje zdarzenia ETW, aby ułatwić deweloperom aplikacji zrozumieć, co dzieje się na platformie. Jeśli nie zostało to jeszcze zrobione, przejdź dalej i postępuj zgodnie z instrukcjami w [Utwórz swoją pierwszą aplikację w programie Visual Studio](service-fabric-tutorial-create-dotnet-app.md). Ta informacja pomoże Ci rozpocząć pracę aplikacji za pomocą Podglądu zdarzeń diagnostycznych, które są wyświetlane komunikaty śledzenia.

1. Jeśli Diagnostyka okno zdarzenia automatycznie nie uwzględnia, przejdź do **widoku** karcie w programie Visual Studio, wybierz polecenie **Windows inne** i następnie **podglądu zdarzeń diagnostycznych**.
2. Każde zdarzenie ma standardowy metadane, informujące o węzła, aplikacji i usług, których pochodzi zdarzenie. Można również filtrować listę zdarzeń za pomocą **Filtruj zdarzenia** polu w górnej części okna zdarzenia. Na przykład można filtrować na **nazwa węzła** lub **nazwy usługi.** A jeśli patrzysz szczegóły zdarzenia można również wstrzymać przy użyciu **wstrzymać** znajdujący się w górnej części okna zdarzenia i wznowić później bez utraty zdarzeń.
   
   ![Visual Studio Diagnostics Events Viewer](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>Dodawanie własnych niestandardowych danych śledzenia do kodu aplikacji
Szablony projektu usługi Service Fabric Visual Studio zawiera przykładowy kod. Kod przedstawia sposób dodawania aplikacji niestandardowej śladów funkcji ETW kodu, które pojawiają się w podglądzie ETW. program Visual Studio obok ślady systemu z usługi Service Fabric. Zaletą tej metody jest, że metadanych jest automatycznie dodawany do śledzenia i Visual Studio podglądu zdarzeń diagnostycznych jest już skonfigurowany, aby je wyświetlić.

W przypadku projektów utworzone na podstawie **szablonów usług** (bezstanowe lub stanowe) po prostu wyszukać `RunAsync` implementacji:

1. Wywołanie `ServiceEventSource.Current.ServiceMessage` w `RunAsync` metoda pokazano przykład niestandardowe śledzenia zdarzeń systemu Windows w kodzie aplikacji.
2. W **ServiceEventSource.cs** pliku, można znaleźć przeciążenia `ServiceEventSource.ServiceMessage` metody, które mają być używane dla zdarzeń o wysokiej częstotliwości z powodu ze względu na wydajność.

W przypadku projektów utworzone na podstawie **szablony aktora** (bezstanowe lub stanowe):

1. Otwórz **.cs "ProjectName"** pliku gdzie *ProjectName* to nazwa, został wybrany dla projektu programu Visual Studio.  
2. Znajdź kod `ActorEventSource.Current.ActorMessage(this, "Doing Work");` w *DoWorkAsync* metody.  Jest to przykład niestandardowe śledzenia zdarzeń systemu Windows zapisywane w kodzie aplikacji.  
3. W pliku **ActorEventSource.cs**, można znaleźć przeciążenia `ActorEventSource.ActorMessage` metody, które mają być używane dla zdarzeń o wysokiej częstotliwości z powodu ze względu na wydajność.

Po dodaniu niestandardowych funkcji ETW śledzenia do kodu usługi, tworzenie, wdrażanie i uruchom aplikację ponownie, aby zobaczyć swoje zdarzeń w Podglądzie zdarzeń diagnostycznych. Jeśli debugujesz aplikację za pomocą **F5**, zostanie automatycznie otwarta podglądu zdarzeń diagnostycznych.

## <a name="next-steps"></a>Kolejne kroki
Ten sam kod śledzenia, który został dodany do aplikacji powyżej dla lokalnych danych diagnostycznych będzie działać z narzędziami, które można użyć, aby wyświetlić te zdarzenia, podczas uruchamiania aplikacji w klastrze platformy Azure. Zapoznaj się z następującymi artykułami, omówiono różne opcje narzędzi, które opisują, jak możesz skonfigurować je.

* [Jak gromadzić dzienniki za pomocą diagnostyki Azure](service-fabric-diagnostics-how-to-setup-wad.md)
* [Zdarzenie agregacji i zbieranie przy użyciu struktury EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)

