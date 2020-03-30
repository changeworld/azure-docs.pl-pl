---
title: Przepływ pracy architektury maszyn wirtualnych systemu Windows Azure | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie procesów przepływu pracy podczas wdrażania usługi.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: 5dd57a87658554bf59acf5cee1b6daf67b8692b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71162148"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Przepływ pracy klasycznej architektury maszyn wirtualnych systemu Windows Azure 
Ten artykuł zawiera omówienie procesów przepływu pracy, które występują podczas wdrażania lub aktualizowania zasobu platformy Azure, takiego jak maszyna wirtualna. 

> [!NOTE]
>Platforma Azure ma dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: Resource Manager i model klasyczny. Ten artykuł dotyczy klasycznego modelu wdrożenia.

Na poniższym diagramie przedstawiono architekturę zasobów platformy Azure.

![Przepływ pracy platformy Azure](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>Podstawowe informacje o przepływie pracy
   
**A**. RDFE / FFE to ścieżka komunikacji od użytkownika do tkaniny. RDFE (RedDog Front End) jest publicznie udostępniany interfejs API, który jest frontonem portalu zarządzania i interfejsu API zarządzania usługami, takich jak Visual Studio, Azure MMC i tak dalej.  Wszystkie żądania od użytkownika przechodzą przez RDFE. FFE (Front End sieci szkieletowej) to warstwa, która tłumaczy żądania z RDFE na polecenia sieci szkieletowej. Wszystkie żądania od RDFE przejść przez FFE, aby dotrzeć do kontrolerów sieci szkieletowej.

**B**. Kontroler sieci szkieletowej jest odpowiedzialny za utrzymywanie i monitorowanie wszystkich zasobów w centrum danych. Komunikuje się z agentami hosta sieci szkieletowej w sieci szkieletowej wysyłania informacji, takich jak wersja systemu operacyjnego gościa, pakiet usługi, konfiguracja usługi i stan usługi.

**C**. Agent hosta mieszka w systemie operacyjnym hosta i jest odpowiedzialny za konfigurowanie systemu operacyjnego gościa i komunikowanie się z agentem gościa (WindowsAzureGuestAgent) w celu zaktualizowania roli w kierunku zamierzonego stanu celu i sprawdzania pulsu za pomocą agenta gościa. Jeśli agent hosta nie otrzyma odpowiedzi pulsu przez 10 minut, agent hosta ponownie uruchamia system operacyjny gościa.

**C2**. Program WaAppAgent jest odpowiedzialny za instalowanie, konfigurowanie i aktualizowanie pliku WindowsAzureGuestAgent.exe.

**D**.  System WindowsAzureGuestAgent jest odpowiedzialny za następujące elementy:

1. Konfigurowanie systemu operacyjnego gościa, w tym zapory, list ACL, zasobów LocalStorage, pakietu usług i konfiguracji oraz certyfikatów.
2. Konfigurowanie identyfikatora SID dla konta użytkownika, pod którymi będzie działać rola.
3. Komunikowanie stanu roli do sieci szkieletowej.
4. Uruchamianie WaHostBootstrapper i monitorowanie go, aby upewnić się, że rola jest w stanie celu.

**E**. WaHostBootstrapper jest odpowiedzialny za:

1. Odczytywanie konfiguracji roli i uruchamianie wszystkich odpowiednich zadań i procesów w celu skonfigurowania i uruchomienia roli.
2. Monitorowanie wszystkich procesów podrzędnych.
3. Wywoływanie StatusCheck zdarzenia w procesie hosta roli.

**F**. Program IISConfigurator jest uruchamiany, jeśli rola jest skonfigurowana jako pełna rola sieci Web usług IIS (nie będzie działać dla ról HWC w 1.2 1.2). Jest odpowiedzialny za:

1. Uruchamianie standardowych usług usług IIS
2. Konfigurowanie modułu ponownego zapisu w konfiguracji sieci Web
3. Konfigurowanie apppool dla skonfigurowane roli w modelu usługi
4. Konfigurowanie rejestrowania usługi IIS w celu wskazania folderu LocalStorage w programie DiagnosticStore
5. Konfigurowanie uprawnień i list ACL
6. Witryna sieci Web znajduje się w %roleroot%:\sitesroot\0, a obszar apppool wskazuje tę lokalizację do uruchomienia usług IIS. 

**G**. Zadania uruchamiania są definiowane przez model roli i uruchamiane przez WaHostBootstrapper. Zadania uruchamiania można skonfigurować tak, aby działały w tle asynchronicznie, a program uruchamiany hosta uruchomi zadanie uruchamiania, a następnie przejdzie do innych zadań uruchamiania. Zadania uruchamiania można również skonfigurować tak, aby działały w trybie prostym (domyślnym), w którym program uruchamiany hosta będzie czekał na zakończenie pracy zadania uruchamiania i zwrócenie kodu zakończenia (0) przed kontynuowaniem następnego zadania uruchamiania.

**H**. Te zadania są częścią SDK i są definiowane jako wtyczki w definicji usługi roli (csdef). Po rozwinięciu do zadań **uruchamiania DiagnosticsAgent** i **RemoteAccessAgent** są unikatowe, ponieważ każdy z nich definiuje dwa zadania uruchamiania, jeden regularny i jeden, który ma **parametr /blockStartup.** Normalne zadanie uruchamiania jest zdefiniowane jako zadanie uruchamiania w tle, dzięki czemu można go uruchomić w tle, gdy sama rola jest uruchomiona. Zadanie uruchamiania **/blockStartup** jest zdefiniowane jako proste zadanie uruchamiania, dzięki czemu WaHostBootstrapper czeka na jego zakończenie przed kontynuowaniem. Zadanie **/blockStartup** czeka na zakończenie inicjowania zwykłego zadania, a następnie kończy pracę i zezwala na kontynuowanie programu inicjującego hosta. Odbywa się to tak, że diagnostyka i dostęp RDP można skonfigurować przed uruchomieniem procesów roli (odbywa się to za pomocą zadania /blockStartup). Umożliwia to również diagnostyki i dostępu RDP, aby kontynuować pracę po zakończeniu uruchamiania zadań uruchamiania hosta (odbywa się to za pomocą zadania Normalny).

**I**. WaWorkerHost jest standardowy proces hosta dla normalnych ról procesu roboczego. Ten proces hosta obsługuje wszystkie biblioteki DLL roli i kod punktu wejścia, takie jak OnStart i Run.

**J**. WaWebHost jest standardowy proces hosta dla ról sieci web, jeśli są one skonfigurowane do korzystania z SDK 1.2 zgodny hostable Web Core (HWC). Role można włączyć tryb HWC, usuwając element z definicji usługi (csdef). W tym trybie wszystkie kod usługi i biblioteki DLL są uruchamiane z procesu WaWebHost. Usługi IIS (w3wp) nie są używane i nie ma żadnych buforów aplikacji skonfigurowanych w Menedżerze usług IIS, ponieważ usługi IIS są hostowane wewnątrz programu WaWebHost.exe.

**K**. WaIISHost jest procesem hosta dla kodu punktu wejścia roli dla ról sieci web, które używają pełnych usług IIS. Ten proces ładuje pierwszą bibliotekę DLL, która zostanie znaleziona, która używa klasy **RoleEntryPoint** i wykonuje kod z tej klasy (OnStart, Run, OnStop). Wszystkie zdarzenia **RoleEnvironment** (takie jak StatusCheck i Changed), które są tworzone w klasie RoleEntryPoint są wywoływane w tym procesie.

**L**. W3WP jest standardowy proces roboczy usług IIS, który jest używany, jeśli rola jest skonfigurowany do korzystania z pełnych usług IIS. Spowoduje to uruchomienie apppool, który jest skonfigurowany z IISConfigurator. Wszystkie zdarzenia RoleEnvironment (takie jak StatusCheck i Changed), które są tworzone w tym miejscu są wywoływane w tym procesie. Należy zauważyć, że roleenvironment zdarzenia będą uruchamiane w obu lokalizacjach (WaIISHost i w3wp.exe), jeśli subskrybujesz zdarzenia w obu procesach.

## <a name="workflow-processes"></a>Procesy przepływu pracy

1. Użytkownik składa żądanie, takie jak przekazywanie plików ".cspkg" i ".cscfg", informujące zasób o zatrzymaniu lub wszczynaniu zmian konfiguracji itd. Można to zrobić za pomocą witryny Azure portal lub narzędzia, które używa interfejsu API zarządzania usługami, takich jak funkcja publikowania w programie Visual Studio. To żądanie trafia do RDFE, aby wykonać całą pracę związaną z subskrypcją, a następnie przekazać żądanie do ffe. Pozostałe kroki przepływu pracy są do wdrożenia nowego pakietu i uruchomić go.
2. FFE znajduje prawidłową pulę maszyn (na podstawie danych wejściowych klienta, takich jak grupa koligacji lub lokalizacji geograficznej plus dane wejściowe z sieci szkieletowej, takie jak dostępność maszyny) i komunikuje się z kontrolerem głównej sieci szkieletowej w tej puli maszyn.
3. Kontroler sieci szkieletowej znajduje hosta, który ma dostępne rdzenie procesora (lub obraca się nowy host). Pakiet usługi i konfiguracja jest kopiowany do hosta, a kontroler sieci szkieletowej komunikuje się z agentem hosta w hoście systemu operacyjnego w celu wdrożenia pakietu (konfigurowanie identyfikatorów DD, portów, systemu operacyjnego gościa i tak dalej).
4. Agent hosta uruchamia system operacyjny gościa i komunikuje się z agentem gościa (WindowsAzureGuestAgent). Host wysyła bicie serca do gościa, aby upewnić się, że rola pracuje w kierunku jego stanu celu.
5. System WindowsAzureGuestAgent konfiguruje system operacyjny gościa (zapora sieciowa, listy ACL, LocalStorage itd.), kopiuje nowy plik konfiguracyjny XML do c:\Config, a następnie uruchamia proces WaHostBootstrapper.
6. W przypadku pełnych ról sieci Web usług IIS program WaHostBootstrapper uruchamia program IISConfigurator i informuje o usunięciu istniejących buforów aplikacji dla roli sieci Web z usług IIS.
7. Program WaHostBootstrapper odczytuje zadania **uruchamiania** z pliku E:\RoleModel.xml i rozpoczyna wykonywanie zadań uruchamiania. WaHostBootstrapper czeka, aż wszystkie proste zadania uruchamiania zostały zakończone i zwrócił komunikat "sukces".
8. W przypadku pełnych ról sieci Web usług IIS program WaHostBootstrapper informuje usługę IISConfigurator o skonfigurowaniu usługi IIS AppPool i wskazuje witrynę na `E:\Sitesroot\<index>`indeks `<index>` oparty na 0 na liczbę elementów zdefiniowanych `<Sites>` dla usługi.
9. WaHostBootstrapper rozpocznie proces hosta w zależności od typu roli:
    1. **Rola procesu roboczego:** WaWorkerHost.exe jest uruchamiany. WaHostBootstrapper wykonuje OnStart() metody. Po powrocie WaHostBootstrapper rozpoczyna wykonywanie Metody Run(), a następnie jednocześnie oznacza rolę jako Gotowe i umieszcza ją w rotacji modułu równoważenia obciążenia (jeśli zdefiniowano punkty InputEndpoints). WaHostBootsrapper następnie przechodzi do pętli sprawdzania stanu roli.
    1. **SDK 1.2 HWC Web Role:** WaWebHost jest uruchomiona. WaHostBootstrapper wykonuje OnStart() metody. Po powrocie WaHostBootstrapper rozpoczyna wykonywanie Run(), a następnie jednocześnie oznacza rolę jako Gotowe i umieszcza ją w rotacji modułu równoważenia obciążenia. WaWebHost wystawia żądanie rozgrzewki (GET /do.rd_runtime_init). Wszystkie żądania sieci Web są wysyłane do programu WaWebHost.exe. WaHostBootsrapper następnie przechodzi do pętli sprawdzania stanu roli.
    1. **Pełna rola sieci Web usług IIS:** aIISHost jest uruchomiona. WaHostBootstrapper wykonuje OnStart() metody. Po powrocie rozpoczyna wykonywanie metody Run(), a następnie jednocześnie oznacza rolę jako Gotowe i umieszcza ją w rotacji modułu równoważenia obciążenia. WaHostBootsrapper następnie przechodzi do pętli sprawdzania stanu roli.
10. Przychodzące żądania sieci web do roli sieci Web pełnych usług IIS wyzwala usługi IIS, aby uruchomić proces W3WP i obsługiwać żądanie, tak samo jak w lokalnym środowisku usług IIS.

## <a name="log-file-locations"></a>Lokalizacje plików dziennika

**System WindowsAzureGuestagent**

- C:\Logs\AppAgentRuntime.Log.  
Ten dziennik zawiera zmiany w usłudze, w tym uruchamianie, zatrzymania i nowe konfiguracje. Jeśli usługa nie ulegnie zmianie, można oczekiwać, aby zobaczyć duże przerwy w czasie w tym pliku dziennika.
- C:\Logs\WaAppAgent.Log.  
Ten dziennik zawiera aktualizacje stanu i powiadomienia pulsu i jest aktualizowany co 2-3 sekundy.  Ten dziennik zawiera widok historyczny stanu wystąpienia i powie, gdy wystąpienie nie było w stanie Gotowy.
 
**WaHostBootstrapper**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**WaWebHost (WaWebHost)**

`C:\Resources\Directory\<guid>.<role>\WaWebHost.log`
 
**WaIISHost**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**Konfigurator IIS**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**Dzienniki usług IIS**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**Dzienniki zdarzeń systemu Windows**

`D:\Windows\System32\Winevt\Logs`
 



