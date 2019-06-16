---
title: Przepływ pracy Windows maszyny Wirtualnej platformy Azure architektury | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie procesów przepływu pracy, podczas wdrażania usługi.
services: cloud-services
documentationcenter: ''
author: genlin
manager: Willchen
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: 7c8459a6694663a49203b6ec21a760d3e6bd60c3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60480762"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Przepływ pracy w klasycznej architekturze maszyn wirtualnych Windows Azure 
Ten artykuł zawiera omówienie procesów przepływu pracy, które występują podczas wdrażania lub zaktualizuj zasób platformy Azure, np. maszyna wirtualna. 

> [!NOTE]
>Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: Usługi Resource Manager i model klasyczny. Ten artykuł dotyczy klasycznego modelu wdrożenia.

Na poniższym diagramie przedstawiono architekturę zasobów platformy Azure.

![Usługa Azure przepływu pracy](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>Podstawowe informacje dotyczące przepływu pracy
   
**A**. Fronton REDDOG / FFE to ścieżka do komunikacji przez użytkownika w sieci szkieletowej. Fronton REDDOG (koniec frontonu RedDog) jest publicznie ujawnionych interfejsu API, który jest frontonu do portalu zarządzania i interfejsu API zarządzania usługami, takie jak Visual Studio, Azure programu MMC i tak dalej.  Wszystkie żądania od użytkownika przechodzą przez fronton REDDOG. FFE (koniec frontonu Service Fabric) jest warstwy, która tłumaczy żądania z frontonu REDDOG poleceniami sieci szkieletowej. Wszystkie żądania z frontonu REDDOG przechodzą przez FFE nawiązać kontrolerów sieci szkieletowej.

**B**. Kontroler sieci szkieletowej jest odpowiedzialny za utrzymanie i monitorowania wszystkich zasobów w centrum danych. Komunikuje się ona z agentami hosta sieci szkieletowej w sieci szkieletowej systemu operacyjnego wysyłania informacje, takie jak wersja systemu operacyjnego gościa, pakiet usługi, konfiguracji usługi i stanu usługi.

**C**. Agent hosta jest mieszkańcem OSsystem hosta i jest odpowiedzialna za skonfigurowanie systemu operacyjnego gościa i komunikowania się z agentem gościa (WindowsAzureGuestAgent), aby można było zaktualizować roli w kierunku do stanu zamierzony cel i wykonaj pulsu umożliwia sprawdzenie za pomocą agenta gościa. Jeśli Agent hosta nie otrzyma odpowiedzi pulsu na 10 minut, Agent hosta powoduje ponowne uruchomienie systemu operacyjnego gościa.

**C2**. WaAppAgent jest odpowiedzialny za instalowanie, konfigurowanie i aktualizowanie WindowsAzureGuestAgent.exe.

**D**.  WindowsAzureGuestAgent jest odpowiedzialna za następujące czynności:

1. Konfigurowanie systemu operacyjnego gościa, w tym zapory, listy ACL, LocalStorage zasobów, pakietu usługi i konfiguracji i certyfikatów. Konfigurowanie identyfikatora SID dla konta użytkownika, które zostaną uruchomione roli.
2. Komunikacja Stan roli do sieci szkieletowej.
3. Uruchamianie WaHostBootstrapper i monitorowania, aby upewnić się, że rola jest w stanie celem.

**E**. WaHostBootstrapper jest odpowiedzialny za:

1. Odczytywanie konfiguracji roli i rozpoczynanie wszystkich odpowiednich zadań i procesów do konfigurowania i uruchamiania roli.
2. Monitorowanie jego procesów podrzędnych.
3. Wywoływanie zdarzeń StatusCheck na proces hosta roli.

**F**. IISConfigurator działa, jeśli ta rola jest skonfigurowana jako rola internetowa usługi IIS (nie będzie ona działać dla ról użytkownika 1.2 zestawu SDK). Jest on odpowiedzialny za:

1. Uruchamianie standardowymi usługami IIS
2. Konfigurowanie moduł ponowne zapisywanie adresów w konfiguracji sieci web
3. Konfigurowanie puli aplikacji skonfigurowanych roli w modelu usług
4. Konfigurowanie rejestrowania w usługach IIS, by wskazywał folder DiagnosticStore LocalStorage
5. Konfigurowanie uprawnień i listami kontroli dostępu
6. Witryna sieci Web znajduje się w % roleroot%:\sitesroot\0 a punktami pulę aplikacji do tej lokalizacji do uruchamiania usług IIS. 

**G**. Zadania uruchamiania są definiowane przez model roli i uruchamiane przez WaHostBootstrapper. Zadania uruchamiania można skonfigurować do uruchamiania w tle asynchronicznie, a następnie uruchomi zadanie uruchamiania i następnie przejdziemy do innych zadań uruchamiania programu inicjującego hosta. Zadania uruchamiania można skonfigurować w taki sposób, aby uruchomić w trybie proste (ustawienie domyślne), w którym program inicjujący hosta będzie czekać na zakończenie działania i zwróci kod zakończenia powodzenia (0) przed kontynuowaniem do następnego zadania uruchamiania zadania uruchamiania.

**H**. Te zadania są częścią zestawu SDK i są definiowane w postaci dodatków plug-in w roli definicji usługi (csdef). Po rozwinięciu do zadania uruchamiania **DiagnosticsAgent** i **RemoteAccessAgent** są unikatowe, każdy określają dwóch zadań uruchamiania, jeden-regularne i, który ma **/blockStartup** parametru. Zadanie normalnego uruchamiania jest zdefiniowany jako zadanie uruchamiania w tle, dzięki czemu można uruchomić w tle, gdy jest uruchomiona rola, sam. **/BlockStartup** zadanie uruchamiania jest zdefiniowany jako zadania uruchamiania prostych tak, aby WaHostBootstrapper czeka, aby zamknąć przed kontynuowaniem. **/BlockStartup** zadanie czeka na typowym zadaniem zakończyć inicjowanie, a następnie kończy działanie i umożliwić bootstrapper hosta, aby kontynuować. Można to zrobić, aby przed rozpoczęciem procesów roli (jest to realizowane zadań /blockStartup) można skonfigurować dane diagnostyczne i dostęp RDP. Umożliwia także dostępu RDP, nadal będą uruchamiane po zakończeniu zadania uruchamiania (jest to wykonywane przy użyciu normalnego zadania) program inicjujący hosta i diagnostyki.

**I**. WaWorkerHost polega na hostów standardowych dla ról procesów roboczych normalny. Ten proces hosta obsługuje biblioteki dll i kod punktu wejścia, takich jak dla metody OnStart i wykonywania wszystkich ról.

**J**. WaWebHost jest procesem hosta standardowego dla ról sieć web, jeśli są one skonfigurowane do używania zestawu SDK zgodnego z 1.2 składnika Hostable Web Core (HWC). Role można włączyć w trybie użytkownika przez usunięcie elementu z definicji usługi (csdef). W tym trybie kodu i bibliotek DLL wszystkie usługi działają z procesu WaWebHost. Usługi IIS (w3wp) nie jest używany i nie ma żadnych pul aplikacji skonfigurowany w Menedżerze usług IIS, ponieważ usługi IIS znajduje się wewnątrz WaWebHost.exe.

**K**. WaIISHost jest procesem hosta dla roli kod punktu wejścia dla ról sieci web, korzystających z usługi IIS. Ten proces ładowania pierwszego pliku DLL, który znajduje się używającej **RoleEntryPoint** klasy i wykonuje kod z tej klasy (OnStart, uruchom OnStop). Wszelkie **RoleEnvironment** zdarzeń (na przykład StatusCheck i zmieniono), które są tworzone w klasie RoleEntryPoint są wywoływane w ramach tego procesu.

**L**. W3wp jest standardowy proces roboczy usług IIS, która jest używana, gdy rola jest skonfigurowany do używania usługi IIS. To rozwiązanie działa pulę aplikacji, który jest skonfigurowany z IISConfigurator. Wszelkie zdarzenia RoleEnvironment (na przykład StatusCheck i zmieniono), które są tworzone w tym miejscu są wywoływane w ramach tego procesu. Pamiętaj, że RoleEnvironment zdarzenia będą uruchamiane w obu lokalizacjach (WaIISHost i w3wp.exe) Jeśli subskrypcji na zdarzenia w oba procesy.

## <a name="workflow-processes"></a>Procesy przepływu pracy

1. Użytkownik wysyła żądanie, takie jak przekazywanie plików cspkg i cscfg, informuje zasobów, aby zatrzymać lub wprowadzania zmian w konfiguracji i tak dalej. Można to zrobić za pośrednictwem witryny Azure portal lub to narzędzie, które używa interfejsie API zarządzania usługami, takie jak funkcja Publikowanie programu Visual Studio. To żądanie jest przesyłany do frontonu REDDOG celu wszystkich związanych z subskrypcją pracy, a następnie komunikować się żądanie FFE. Pozostałe kroki dotyczące przepływu pracy są wdrożyć nowy pakiet i należy ją uruchomić.
2. FFE znajdzie odpowiednią maszynę puli (na podstawie klienta wprowadzanie takich, jak grupy koligacji lub lokalizację geograficzną oraz dane wejściowe z sieci szkieletowej, takich jak komputer) i komunikuje się z kontrolerem sieci szkieletowej wzorca w puli maszyn.
3. Kontroler sieci szkieletowej umożliwia znalezienie hosta, który ma dostępnych rdzeni procesora CPU (lub obroty Konfigurowanie nowego hosta). Pakiet usługi i konfiguracji jest kopiowany do hosta i kontroler sieci szkieletowej komunikuje się z usługą agenta hosta na hoście systemu operacyjnego, aby wdrożyć pakiet (Skonfiguruj adresy DIP, portów, systemu operacyjnego gościa i tak dalej).
4. Agent hosta uruchamia systemu operacyjnego gościa i komunikuje się z agentem gościa (WindowsAzureGuestAgent). Host wysyła Puls dla gości, aby upewnić się, czy rola działa w kierunku jej stan docelowy.
5. WindowsAzureGuestAgent konfiguruje gościa systemu operacyjnego (zapory, listy ACL, LocalStorage i tak dalej), kopiuje nowy plik konfiguracji XML do c:\Config, a następnie uruchamia proces WaHostBootstrapper.
6. W przypadku ról sieci web usługi IIS WaHostBootstrapper rozpoczyna się IISConfigurator i informuje o usunięcie wszelkich istniejących pul aplikacji w roli sieci web za pomocą programu IIS.
7. Odczytuje WaHostBootstrapper **uruchamiania** zadań z E:\RoleModel.xml i rozpoczyna wykonywanie zadań uruchamiania. WaHostBootstrapper czeka, aż wszystkie zadania uruchamiania prostych zostały zakończone i zwrócił komunikat "success".
8. Dla ról sieci web usługi IIS WaHostBootstrapper informuje IISConfigurator, aby skonfigurować pulę aplikacji usług IIS i punkty witryny `E:\Sitesroot\<index>`, gdzie `<index>` jest indeksem 0 oparte na liczbę <Sites> elementy zdefiniowane dla usługi.
9. WaHostBootstrapper rozpocznie się proces hosta, w zależności od typu roli:
    1. **Rola procesu roboczego**: WaWorkerHost.exe został uruchomiony. WaHostBootstrapper wykonuje metodę metody OnStart(). Po powrocie go, WaHostBootstrapper rozpoczyna wykonywanie metody Run() jednocześnie oznacza rolę gotowe i umieszcza go w rotacji modułu równoważenia obciążenia (Jeżeli zdefiniowano InputEndpoints). WaHostBootsrapper następnie przechodzi w pętli sprawdzania stanu roli.
    1. **Rola sieci Web SDK 1.2 HWC**: WaWebHost został uruchomiony. WaHostBootstrapper wykonuje metodę metody OnStart(). Po powrocie go, WaHostBootstrapper rozpoczyna wykonywanie metody Run() jednocześnie oznacza rolę gotowe i umieszcza go w rotacji modułu równoważenia obciążenia. WaWebHost generuje żądanie rozgrzewania (GET /do.rd_runtime_init). Wszystkie żądania są wysyłane do WaWebHost.exe. WaHostBootsrapper następnie przechodzi w pętli sprawdzania stanu roli.
    1. **Pełne roli sieci Web usług IIS**: aIISHost została uruchomiona. WaHostBootstrapper wykonuje metodę metody OnStart(). Po powrocie go, rozpoczyna wykonania metody Run() i jednocześnie oznacza rolę gotowe i umieszcza go w rotacji modułu równoważenia obciążenia. WaHostBootsrapper następnie przechodzi w pętli sprawdzania stanu roli.
10. Przychodzące żądania sieci web do pełnego programu IIS sieci web wyzwalaczy rolę usług IIS, aby rozpocząć proces W3WP i obsłużyć żądania, takie same jak w środowisku usług IIS w środowisku lokalnym.

## <a name="log-file-locations"></a>Lokalizacje plików dziennika

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
Ten dziennik zawiera zmiany, usługi, w tym uruchamia, zatrzymuje i nowe konfiguracje. Jeśli usługa nie zmienia się, może pojawić się duże przerwy czasu, w tym pliku dziennika.
- C:\Logs\WaAppAgent.Log.  
Ten dziennik zawiera aktualizacje stanu i powiadomień pulsu i jest aktualizowany co 2 – 3 sekundy.  Ten dziennik zawiera wyświetlać historyczne informacje o stanie wystąpienie i poinformuje, jeśli wystąpienie nie jest w stanie gotowe.
 
**WaHostBootstrapper**

C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log
 
**WaWebHost**

C:\Resources\Directory\<guid>.<role>\WaWebHost.log
 
**WaIISHost**

C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log
 
**IISConfigurator**

C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log
 
**Dzienniki usług IIS**

C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1
 
**Dzienniki zdarzeń Windows**

D:\Windows\System32\Winevt\Logs
 



