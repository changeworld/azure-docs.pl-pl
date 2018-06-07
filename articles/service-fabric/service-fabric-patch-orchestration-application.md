---
title: Azure Service Fabric poprawki aranżacji aplikacji | Dokumentacja firmy Microsoft
description: Aplikacja do automatyzacji w klastrze usługi sieć szkieletowa stosowanie poprawek systemu operacyjnego.
services: service-fabric
documentationcenter: .net
author: novino
manager: timlt
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/22/2018
ms.author: nachandr
ms.openlocfilehash: 69806520f3d57cb1d383999ba53fefb7e0bd56b4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642815"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Poprawka systemu operacyjnego Windows w klastrze usługi sieć szkieletowa

> [!div class="op_single_selector"]
> * [Windows](service-fabric-patch-orchestration-application.md)
> * [Linux](service-fabric-patch-orchestration-application-linux.md)
>
>

Aplikacja orchestration poprawki jest aplikacja sieć szkieletowa usług Azure, która automatyzuje systemu operacyjnego stosowanie poprawek w klastrze usługi sieć szkieletowa bez przestoju.

Poprawka aplikacji aranżacji oferuje następujące funkcje:

- **Instalacja aktualizacji automatycznych systemu operacyjnego**. Automatycznie pobierania i instalowania aktualizacji systemu operacyjnego. Węzły klastra są ponownie uruchamiane zgodnie z potrzebami bez przestoju klastra.

- **Typu cluster-aware integracji stosowanie poprawek i kondycji**. Podczas stosowania aktualizacji, aplikacja orchestration poprawki monitoruje kondycję węzłów klastra. Węzły klastra są uaktualnionego jeden węzeł lub domeny uaktualnienia pojedynczo. Jeśli kondycji klaster przestanie działać z powodu proces stosowania poprawek, poprawki jest zatrzymana, aby zapobiec obciążające problem.

## <a name="internal-details-of-the-app"></a>Wewnętrzny szczegóły aplikacji

Poprawka aplikacji orchestration składa się z następujących Podskładniki:

- **Usługa koordynatora**: tej usługi stanowej jest odpowiedzialny za:
    - Koordynowanie zadania usługi Windows Update dla całego klastra.
    - Przechowywanie wyniku ukończone operacje usługi Windows Update.
- **Usługa agenta węzła**: tej usługi bezstanowej działa we wszystkich węzłach klastra sieci szkieletowej usług. Usługa jest odpowiedzialna za:
    - Uruchamianie NTService agenta węzła.
    - Monitorowanie NTService agenta węzła.
- **Węzeł agenta NTService**: Usługa ta systemu Windows NT jest uruchamiana na wyższym poziomie uprawnień (SYSTEM). Z kolei usługę agenta węzła i usługę koordynatora działać na niższym poziomie uprawnienia (Usługa sieciowa). Usługa jest odpowiedzialna za wykonywanie następujących zadań usługi Windows Update na wszystkich węzłach klastra:
    - Wyłączanie automatycznej aktualizacji systemu Windows w węźle.
    - Trwa pobieranie i instalowanie usługi Windows Update zgodnie z zasadami użytkownik udostępnił.
    - Ponowne uruchomienie komputera po instalacji usługi Windows Update.
    - Przekazywanie wyniki aktualizacji systemu Windows z usługą koordynatora.
    - Raporty dotyczące raportowania kondycji, w przypadku, gdy operacja nie powiodła się po wykorzystaniu ponawiania prób.

> [!NOTE]
> Poprawki aplikacji orchestration korzysta z sieci szkieletowej usług naprawy Menedżera systemu usługi, aby wyłączyć lub włączyć węzeł i sprawdzania kondycji. Zadanie naprawy tworzonych przez aplikację aranżacji poprawki śledzi postęp procesu usługi Windows Update dla każdego węzła.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Włącz usługę Menedżer napraw (Jeśli nie jest już uruchomiona)

Aplikacja orchestration poprawki wymaga usługi repair Menedżera system zostać włączony w klastrze.

#### <a name="azure-clusters"></a>Klastry platformy Azure

Azure klastrów w warstwie srebrny trwałości ma usługę Menedżer naprawy domyślnie włączone. Azure klastrów w warstwie trwałości gold mogą lub nie mieć usługę Menedżer naprawy włączone, w zależności od tego, kiedy te klastry zostały utworzone. Klastry platformy Azure w warstwie brązową trwałości, domyślnie nie być włączona usługa Menedżera naprawy. Jeśli usługa jest już włączony, można to sprawdzić w sekcji system usługi Service Fabric Explorer.

##### <a name="azure-portal"></a>Azure Portal
Podczas konfigurowania klastra można włączyć naprawy menedżera z portalu Azure. Wybierz **Menedżera naprawy obejmują** opcję w obszarze **dodatkowe funkcje** w czasie konfiguracji klastra.
![Obraz Menedżera naprawy włączenie z portalu Azure](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>Model wdrażania usługi Azure Resource Manager
Możesz też użyć [modelu wdrażania usługi Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) Aby włączyć usługę Menedżer naprawy na nowych i istniejących klastrów sieci szkieletowej usług. Pobierz szablon dla klastra, który chcesz wdrożyć. Można korzystać z przykładowych szablonów lub utworzyć niestandardowy szablon modelu wdrażania usługi Azure Resource Manager. 

Aby włączyć naprawy Menedżera usługi przy użyciu [szablonu modelu wdrażania usługi Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm):

1. Najpierw sprawdź, czy `apiversion` ustawiono `2017-07-01-preview` dla `Microsoft.ServiceFabric/clusters` zasobów. Jeśli jest inny, musisz zaktualizować `apiVersion` wartość `2017-07-01-preview` lub nowszy:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Teraz Włącz usługę Menedżer naprawy przez dodanie poniższego `addonFeatures` sekcji po `fabricSettings` sekcji:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Po zaktualizowaniu szablonu klastra wprowadzone zmiany ich zastosowania i umożliwić zakończenie uaktualniania. Możesz teraz przeglądać usługi repair Menedżera system działające w klastrze. Jest to `fabric:/System/RepairManagerService` w sekcji system usługi Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Autonomiczny lokalnymi klastrów

Można użyć [ustawienia konfiguracji dla klastra systemu Windows autonomiczny](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest) Aby włączyć usługę Menedżer naprawy na nowych i istniejących klastra sieci szkieletowej usług.

Aby włączyć usługę Menedżer naprawy:

1. Najpierw sprawdź, czy `apiversion` w [konfiguracje klastrów ogólne](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) ma ustawioną wartość `04-2017` lub nowszy:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

2. Teraz Włącz usługę Menedżer naprawy przez dodanie poniższego `addonFeatures` sekcji po `fabricSettings` sekcji, jak pokazano poniżej:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Zaktualizuj manifeście klastra za pomocą tych zmian przy użyciu manifest klastra o zaktualizowanych [Utwórz nowy klaster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) lub [Uaktualnij konfigurację klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server#Upgrade-the-cluster-configuration). Gdy w klastrze działa z manifestu klastra zaktualizowane, można przeglądać usługi repair Menedżera system działające w klastrze, która jest wywoływana `fabric:/System/RepairManagerService`w obszarze części Eksploratora usługi sieć szkieletowa usług systemowych.

### <a name="disable-automatic-windows-update-on-all-nodes"></a>Wyłącz automatyczną aktualizację systemu Windows na wszystkich węzłach

Aktualizacje automatyczne systemu Windows, mogą spowodować utratę dostępności, ponieważ wiele węzłów klastra można uruchomić ponownie w tym samym czasie. Poprawka aplikacji aranżacji, domyślnie próbuje wyłączania automatycznej aktualizacji systemu Windows w każdym węźle klastra. Jeśli ustawienia są zarządzane przez administratora lub zasad grupy, firma Microsoft zaleca jednak jawnie ustawienie zasad usługi Windows Update "Powiadom przed pobierania".

## <a name="download-the-app-package"></a>Pobierz pakiet aplikacji

Można pobrać z aplikacji oraz skrypty instalacyjne [łącze archiwum](https://go.microsoft.com/fwlink/?linkid=869566).

Aplikacja w formacie sfpkg można pobrać z [łącze sfpkg](https://go.microsoft.com/fwlink/?linkid=869567). Jeśli źródłem jest przydatna do [wdrożenia aplikacji na podstawie usługi Azure Resource Manager](service-fabric-application-arm-resource.md).

## <a name="configure-the-app"></a>Konfigurowanie aplikacji

Zachowanie aplikacji aranżacji poprawek można skonfigurować zgodnie z potrzebami. Zastąpić wartości domyślne, przekazując w parametrze aplikacji podczas tworzenia aplikacji lub aktualizacji. Można podać parametry aplikacji, określając `ApplicationParameter` do `Start-ServiceFabricApplicationUpgrade` lub `New-ServiceFabricApplication` polecenia cmdlet.

|**Parametr**        |**Typ**                          | **Szczegóły**|
|:-|-|-|
|MaxResultsToCache    |Długie                              | Maksymalna liczba wyników aktualizacji systemu Windows, które mają być buforowane. <br>Wartość domyślna to 3000 zakładając, że: <br> -Liczba węzłów to 20. <br> -Liczba aktualizacji pojawia się w węźle miesięcznie wynosi pięć. <br> -Liczba wyników dla operacji może być 10. <br> — Powinny być przechowywane wyniki dla ostatnich trzech miesięcy. |
|TaskApprovalPolicy   |wyliczenia <br> {NodeWise, UpgradeDomainWise}                          |TaskApprovalPolicy wskazuje zasad, który ma być używany przez usługę koordynatora instalowania aktualizacji systemu Windows w węzłach klastra sieci szkieletowej usług.<br>                         Dozwolone wartości to: <br>                                                           <b>NodeWise</b>. Windows Update jest zainstalowany jeden węzeł naraz. <br>                                                           <b>UpgradeDomainWise</b>. Windows Update jest zainstalowanych domeny uaktualnienia pojedynczo. (Maksymalnie, wszystkie węzły należące do domeny uaktualnienia można przejść do usługi Windows Update.)
|LogsDiskQuotaInMB   |Długie  <br> (Domyślnie: 1024)               |Maksymalny rozmiar poprawki aplikacji aranżacji loguje MB, co może trwale znajdować się lokalnie w węzłach.
| WUQuery               | ciąg<br>(Domyślnie: "IsInstalled = 0")                | Zapytania w celu pobrania aktualizacji systemu Windows. Aby uzyskać więcej informacji, zobacz [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| InstallWindowsOSOnlyUpdates | Wartość logiczna <br> (domyślne: True)                 | Ta flaga zezwala na instalację aktualizacji systemu operacyjnego Windows.            |
| WUOperationTimeOutInMinutes | Int <br>(Domyślnie: 90).                   | Określa limit czasu do żadnej operacji usługi Windows Update (wyszukiwania lub pobierania lub instalacji). Jeśli działanie nie zostało ukończone w określonym czasie, zostało przerwane.       |
| WURescheduleCount     | Int <br> (Domyślne: 5).                  | Maksymalna liczba usługi zmienia harmonogram systemu Windows aktualizacji w przypadku, gdy operacja trwale kończy się niepowodzeniem.          |
| WURescheduleTimeInMinutes | Int <br>(Domyślnie: 30). | Interwał, jaką usługa zmienia harmonogram aktualizacji systemu Windows w przypadku awaria nie zniknie. |
| WUFrequency           | Ciąg rozdzielony przecinkami (domyślne: "Co tydzień, środę, 7:00:00")     | Częstotliwość instalacji usługi Windows Update. Format i możliwe wartości to: <br>— Co miesiąc, DD gg, na przykład, co miesiąc, 5, 12: 22:32. <br> — Gg co tydzień i dzień, na przykład, co tydzień, Wtorek, 12:22:32.  <br> -Codziennie, ss, na przykład codziennie, 12:22:32.  <br> -Wskazuje brak, nie można wykonać aktualizacji systemu Windows.  <br><br> Należy pamiętać, że czasy są w formacie UTC.|
| AcceptWindowsUpdateEula | Wartość logiczna <br>(Domyślnie: true) | Ustawiając tę flagę aplikacji akceptuje umowy licencyjnej użytkownika końcowego dla Windows Update w imieniu właściciela maszyny.              |

> [!TIP]
> Jeśli chcesz usługi Windows Update, aby natychmiast ustawić `WUFrequency` względem czasu wdrożenia aplikacji. Na przykład klaster z pięcioma węzłami testu i Planowanie wdrażania aplikacji przy około 17:00:00 czasu UTC. Jeśli założono, że uaktualnienie aplikacji lub wdrożenia ma 30 minut maksymalnie, ustaw WUFrequency jako "Codziennie, 17:30:00."

## <a name="deploy-the-app"></a>Wdrażanie aplikacji

1. Zakończ wszystkie wstępnie wymagane kroki, aby przygotować klaster.
2. Wdrażanie aplikacji aranżacji poprawki, takich jak każda inna aplikacja usługi Service Fabric. Aplikację można wdrożyć przy użyciu programu PowerShell. Postępuj zgodnie z instrukcjami [Wdróż i usunąć aplikacje przy użyciu programu PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
3. Aby skonfigurować aplikację w czasie wdrażania, należy przekazać `ApplicationParamater` do `New-ServiceFabricApplication` polecenia cmdlet. Dla wygody przygotowaliśmy skryptu Deploy.ps1 wraz z aplikacji. Aby użyć skryptu:

    - Ustanawianie połączenia z klastrem usługi sieć szkieletowa usług za pomocą `Connect-ServiceFabricCluster`.
    - Uruchom skrypt programu PowerShell Deploy.ps1 z odpowiednią `ApplicationParameter` wartość.

> [!NOTE]
> Zachowaj skrypt i folder aplikacji PatchOrchestrationApplication w tym samym katalogu.

## <a name="upgrade-the-app"></a>Uaktualnianie aplikacji

Aby uaktualnić istniejącą aplikację aranżacji poprawki przy użyciu programu PowerShell, postępuj zgodnie z instrukcjami [uaktualniania aplikacji sieci szkieletowej usług za pomocą programu PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell).

## <a name="remove-the-app"></a>Usuwanie aplikacji

Aby usunąć aplikację, postępuj zgodnie z instrukcjami [Wdróż i usunąć aplikacje przy użyciu programu PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

Dla wygody przygotowaliśmy skryptu Undeploy.ps1 wraz z aplikacji. Aby użyć skryptu:

  - Ustanawianie połączenia z klastrem usługi sieć szkieletowa usług za pomocą ```Connect-ServiceFabricCluster```.

  - Uruchom skrypt programu PowerShell Undeploy.ps1.

> [!NOTE]
> Zachowaj skrypt i folder aplikacji PatchOrchestrationApplication w tym samym katalogu.

## <a name="view-the-windows-update-results"></a>Wyświetl wyniki usługi Windows Update

Poprawka aplikacji aranżacji uwidacznia interfejsów API REST, aby wyświetlić wyniki historyczne dla użytkownika. Przykład wyniku JSON:
```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2017-05-21T11:46:52.1953713Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

Poniżej opisano pola JSON.

Pole | Wartości | Szczegóły
-- | -- | --
OperationResult | 0 - powiodło się.<br> 1 - zakończyło się pomyślnie z błędami<br> 2 - nie powiodło się<br> 3 - zostało przerwane<br> 4 - zostało przerwane z limitem czasu | Wskazuje wynik operacji ogólnej (zazwyczaj dotyczących instalacji co najmniej jednej aktualizacji).
ResultCode | Identyczny klasy OperationResult | To pole wskazuje wynik operacji instalacji dla indywidualnej aktualizacji.
Typ operacji | 1 — Instalacja<br> 0 — wyszukiwanie i pobieranie.| Instalacja jest tylko typ operacji, która będzie wyświetlana w wynikach domyślnie.
WindowsUpdateQuery | Domyślna to "IsInstalled = 0" |Usługi Windows update zapytania, którego użyto do wyszukiwania aktualizacji. Aby uzyskać więcej informacji, zobacz [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
RebootRequired | wartość true — był wymagany ponowny rozruch<br> FALSE — nie był wymagany ponowny rozruch | Wskazuje, jeśli ponowne uruchomienie był wymagany do ukończenia instalacji aktualizacji.

Jeśli aktualizacja nie jest zaplanowane jeszcze, wyniku JSON nie jest pusty.

Zaloguj się do klastra zapytania usługi Windows Update wyników. Następnie sprawdzić repliki adres podstawowy usługi koordynatora i kliknij adres URL z przeglądarki: http://&lt;IP REPLIKI&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults.

Punkt końcowy REST usługi Koordynator ma portów dynamicznych. Aby sprawdzić dokładny adres URL, zajrzyj do Eksploratora usługi sieć szkieletowa. Na przykład, wyniki są dostępne pod adresem `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults`.

![Obraz końcowy REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


Zwrotny serwer proxy jest włączona w klastrze, można uzyskać dostępu do adresu URL z spoza klastra, a także.
Punkt końcowy, który musi być trafień jest http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults.

Aby włączyć zwrotnego serwera proxy w klastrze, wykonaj czynności opisane w [odwrotny serwer proxy w sieci szkieletowej usług Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> Po skonfigurowaniu zwrotnego serwera proxy, wszystkie micro usługi w klastrze, które ujawniać punkt końcowy HTTP adresowane z spoza klastra.

## <a name="diagnosticshealth-events"></a>Diagnostyka kondycji zdarzenia

### <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Poprawka aranżacji aplikacji dzienniki są gromadzone w ramach dzienniki środowisko uruchomieniowe usługi sieć szkieletowa usług.

W przypadku, gdy chcesz przechwytywać dzienniki za pośrednictwem narzędzia diagnostyczne/potoku wybranych przez użytkownika. Aplikacja orchestration poprawki używa poniżej stałym dostawcy identyfikatorów do rejestrowania zdarzeń za pomocą [źródła zdarzeń](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Raportów o kondycji

Poprawka aplikacji aranżacji publikuje również raporty kondycji względem usługi Koordynator lub agenta węzła w następujących przypadkach:

#### <a name="a-windows-update-operation-failed"></a>Operacja usługi Windows Update, nie powiodła się

W przypadku niepowodzenia operacji usługi Windows Update w węźle raport o kondycji jest generowany z usługą agenta węzła. Szczegóły raport o kondycji zawiera nazwy węzła powodować problemy.

Po pomyślnym problematyczne węzła zakończeniu stosowanie poprawek, raportu są automatycznie usuwane.

#### <a name="the-node-agent-ntservice-is-down"></a>Węzeł NTService Agent nie działa

Jeśli węzeł NTService Agent działa w węźle, z usługą agenta węzła generowany jest raport dotyczący kondycji poziom ostrzeżeń.

#### <a name="the-repair-manager-service-is-not-enabled"></a>Usługa Menedżera naprawy nie jest włączona.

Jeśli usługa Menedżera naprawy nie zostanie znaleziony w klastrze, usługi Koordynator generowany jest raport o kondycji poziom ostrzeżeń.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

Q. **Dlaczego wyświetlać Mój klastra w stanie błędu, gdy aplikacja orchestration poprawki działa?**

A. W procesie instalacji poprawki aplikacji aranżacji wyłącza lub ponownego uruchomienia węzłów, które mogą skutkować tymczasowo kondycji klastra, przechodząc w dół.

Na podstawie zasad dla aplikacji, albo jeden węzeł może przejść w dół podczas operacji stosowania poprawek *lub* całej domeny uaktualnienia można przestaną działać jednocześnie.

Na koniec instalacji usługi Windows Update są reenabled węzły po ponownym uruchomieniu.

W poniższym przykładzie klastra przejścia do stanu błędu tymczasowo ponieważ dwa węzły zostały w dół, a zasady MaxPercentageUnhealthNodes zostało naruszone. Błąd jest tymczasowy, dopóki trwa operacja stosowania poprawek.

![Obraz zła klastra](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Jeśli problem będzie się powtarzać, zapoznaj się z rozdziałem Rozwiązywanie problemów.

Q. **Aplikacja orchestration poprawki jest w stanie ostrzeżenia**

A. Sprawdź, czy raport o kondycji opublikowane w związku z aplikacją jest główną przyczynę. Zazwyczaj ostrzeżenie zawiera szczegółowe informacje o problemie. W przypadku przejściowy problem aplikacji powinien automatycznego odzyskiwania z tego stanu.

Q. **Co można zrobić, jeśli mój klastra jest nieprawidłowy i należy wykonać aktualizację pilnych systemu operacyjnego?**

A. Aplikacja orchestration poprawki nie można zainstalować aktualizacji klastra jest zła. Spróbuj przełączyć klastra do dobrej kondycji, aby odblokować przepływu pracy aplikacji aranżacji poprawki.

Q. **Dlaczego poprawki w klastrach podąża tak długo do uruchomienia?**

A. Czas potrzebny aplikacji aranżacji poprawki przede wszystkim jest zależna od następujących czynników:

- Zasady usługi koordynatora. 
  - Domyślne zasady `NodeWise`, powoduje stosowanie poprawek tylko jeden węzeł naraz. Zwłaszcza, jeśli jest klastrem większy, firma Microsoft zaleca użycie `UpgradeDomainWise` zasady w celu uzyskania szybszego poprawki w klastrach.
- Liczba dostępnych do pobrania i zainstalowania aktualizacji. 
- Średni czas potrzebny do pobrania i zainstalowania aktualizacji, który nie może przekraczać po kilku godzinach.
- Wydajność maszyny Wirtualnej i sieci przepustowości.

Q. **Dlaczego widzę niektórych aktualizacji w wynikach usługi Windows Update uzyskany za pośrednictwem interfejsu API REST, ale nie w historii usługi Windows Update na komputerze?**

A. Niektóre aktualizacje produktu może występować tylko w historii odpowiednich aktualizacji/poprawki. Na przykład aktualizacje programu Windows Defender nie są wyświetlane w historii usługi Windows Update w systemie Windows Server 2016.

Q. **Może służyć do poprawka Moje deweloperów klastra (klastra z jednym węzłem) aranżacji poprawkę aplikacji?**

A. Nie, aplikacja orchestration poprawki nie może służyć do klastra z jednym węzłem poprawki. To ograniczenie jest zgodne z założeniami, jako [usługi sieć szkieletowa usług systemowych](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-technical-overview#system-services) lub wszystkie aplikacje odbiorcy będą występować Przestój i dlatego wszystkie zadania naprawy dla stosowania poprawek nigdy nie może pobrać zatwierdzone przez Menedżera naprawy.

## <a name="disclaimers"></a>Zastrzeżenia

- Poprawka aplikacji aranżacji akceptuje umowy licencyjnej użytkownika końcowego z Windows Update w imieniu użytkownika. Opcjonalnie ustawienie może zostać wyłączone w konfiguracji aplikacji.

- Poprawka aplikacji aranżacji zbiera dane telemetryczne umożliwiający śledzenie użycia i wydajności. Dane telemetryczne aplikacji następuje ustawienie ustawienia telemetrii środowiska uruchomieniowego platformy Service Fabric, (która jest domyślnie włączona).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="a-node-is-not-coming-back-to-up-state"></a>Węzeł nie jest powracające stanu w górę

**Węzeł może zostać zablokowana na wyłączenie stanu, ponieważ**:

Trwa oczekiwanie na sprawdzenie bezpieczeństwa. Aby rozwiązać ten problem, upewnij się, że wystarczającej liczby węzłów są dostępne w dobrej kondycji.

**Węzeł może zostać zatrzymane w stanie wyłączenia, ponieważ**:

- Węzeł został wyłączony ręcznie.
- Węzeł został wyłączony z powodu trwającej infrastruktury platformy Azure zadanie.
- Węzeł został wyłączony tymczasowo przez aplikację aranżacji poprawki do stosowania poprawek węzła.

**Węzeł może zostać zatrzymane w stanie down ponieważ**:

- Węzeł została umieszczona w stanie down ręcznie.
- Węzeł jest w trakcie ponownego uruchomienia (które może zostać wyzwolone przez aplikację aranżacji poprawki).
- Węzeł nie działa z powodu błędny maszyny Wirtualnej lub problemy z połączeniem komputera lub sieci.

### <a name="updates-were-skipped-on-some-nodes"></a>Aktualizacje zostały pominięte na niektóre węzły

Poprawka aplikacji aranżacji próbuje zainstalować Windows update zgodnie z zasadami planowaniem mogą. Usługa próbuje odzyskać węzeł i Pomiń aktualizację zgodnie z zasadami aplikacji.

W takim przypadku generowany jest raport o kondycji poziom ostrzeżeń z usługą agenta węzła. Wynik dla usługi Windows Update zawiera także możliwe przyczyny niepowodzenia.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>Kondycja klastra przechodzi do błędu podczas instalacji aktualizacji

Błędny usługi Windows update można obniżyć kondycji aplikacji lub klastra w określonym węźle lub domena uaktualnienia. Poprawka aplikacji aranżacji zaprzestaje wszelkie kolejne operacje usługi Windows Update, dopóki klastra jest w dobrej kondycji ponownie.

Administrator musi interweniować, aby ustalić, dlaczego aplikacja lub klastra stał się niezdrowe, ponieważ usługa Windows Update.

## <a name="release-notes"></a>Informacje o wersji

### <a name="version-110"></a>Wersja 1.1.0
- Publiczny zlecenia

### <a name="version-111"></a>Wersja 1.1.1
- Rozwiązane usterki w SetupEntryPoint z NodeAgentService uniemożliwiający instalacji NodeAgentNTService.

### <a name="version-120"></a>Wersji 1.2.0

- Poprawki błędów wokół systemu ponownie uruchomić przepływ pracy.
- Poprawka błędu podczas tworzenia zadania Menedżera zasobów z powodu którego kondycji wyboru podczas przygotowywania zadań naprawy nie wykonywane zgodnie z oczekiwaniami.
- Zmienić trybu uruchamiania usługi systemu windows POANodeSvc z automatycznego na opóźnione auto.

### <a name="version-121-latest"></a>Wersji 1.2.1 (Najnowsza wersja)

- Poprawka błędu w dół klastra w przepływie pracy. Wprowadzono logikę kolekcji garbage POA naprawy zadania należące do nieistniejącego węzłów.
