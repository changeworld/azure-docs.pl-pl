---
title: Aplikacja aranżacji poprawek Service Fabric platformy Azure | Microsoft Docs
description: Aplikacja do automatyzowania poprawek systemu operacyjnego w klastrze Service Fabric.
services: service-fabric
documentationcenter: .net
author: khandelwalbrijeshiitr
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: brkhande
ms.openlocfilehash: 2aa2dd8373a9568478a02691ca5e6a43e80cd408
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71289423"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Poprawianie systemu operacyjnego Windows w klastrze Service Fabric

> 
> [!IMPORTANT]
> Wersja aplikacji 1,2. * jest nieobsługiwana w dniu 30 kwietnia 2019. Przeprowadź uaktualnienie do najnowszej wersji.


[Automatyczne uaktualnianie obrazu systemu operacyjnego w usłudze Azure Virtual Machine Scale](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) to najlepsze rozwiązanie w zakresie zachowywania poprawek w systemach operacyjnych na platformie Azure, a aplikacja aranżacji poprawek (POA) to otoka usługi Service Fabric repairmanager Systems Service Włącza planowanie poprawek systemu operacyjnego opartego na konfiguracji dla klastrów hostowanych poza platformą Azure. POA nie jest wymagany w przypadku klastrów hostowanych poza platformą Azure, ale planowanie instalacji poprawek przez domeny uaktualnienia jest wymagane do poprawek Service Fabric klastrów hostów bez przestoju.

POA to aplikacja Service Fabric platformy Azure, która automatyzuje stosowanie poprawek systemu operacyjnego w klastrze Service Fabric bez przestojów.

Aplikacja aranżacji poprawek oferuje następujące funkcje:

- **Automatyczna instalacja aktualizacji systemu operacyjnego**. Aktualizacje systemu operacyjnego są automatycznie pobierane i instalowane. Węzły klastra są ponownie uruchamiane w razie potrzeby bez przestoju klastra.

- **Stosowanie poprawek obsługujących klaster i integracja z kondycją**. Podczas stosowania aktualizacji aplikacja aranżacji patch monitoruje kondycję węzłów klastra. Węzły klastra są uaktualniane jednym węzłem jednocześnie lub w jednej domenie uaktualnienia jednocześnie. Jeśli kondycja klastra ulegnie awarii ze względu na proces stosowania poprawek, poprawka zostaje zatrzymana, aby uniknąć pogorszenia problemu.

## <a name="internal-details-of-the-app"></a>Wewnętrzne szczegóły aplikacji

Aplikacja aranżacji poprawek składa się z następujących podskładników:

- **Usługa koordynatora**: Ta usługa stanowa jest odpowiedzialna za:
    - Koordynowanie zadania Windows Update w całym klastrze.
    - Przechowywanie wyniku zakończonych operacji Windows Update.
- **Usługa agenta węzłów**: Ta usługa bezstanowa jest uruchamiana na wszystkich Service Fabric węzłach klastra. Usługa jest odpowiedzialna za:
    - Inicjowanie programu Node Agent NTService.
    - Monitorowanie NTService agenta.
- **NTService agenta węzła**: Ta usługa systemu Windows NT działa z uprawnieniami wyższego poziomu (SYSTEM). Z kolei usługa agenta węzła i usługa koordynatora są uruchamiane z uprawnieniem niskiego poziomu (usługa sieciowa). Usługa jest odpowiedzialna za wykonywanie następujących zadań Windows Update na wszystkich węzłach klastra:
    - Wyłączanie automatycznego Windows Update w węźle.
    - Pobieranie i instalowanie Windows Update zgodnie z zasadami dostarczonymi przez użytkownika.
    - Ponowne uruchamianie instalacji Windows Update na komputerze.
    - Przekazywanie wyników aktualizacji systemu Windows do usługi koordynatora.
    - Raportowanie raportów kondycji w przypadku niepowodzenia operacji po wyczerpaniu wszystkich ponownych prób.

> [!NOTE]
> Aplikacja aranżacji poprawek używa usługi systemowej Service Fabric Repair Manager, aby wyłączyć lub włączyć węzeł i przeprowadzić kontrole kondycji. Zadanie naprawy utworzone przez aplikację aranżacji patch śledzi Windows Update postęp dla każdego węzła.

## <a name="prerequisites"></a>Wymagania wstępne

> [!NOTE]
> Minimalna wymagana wersja programu .NET Framework to 4,6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Włącz usługę Repair Manager (jeśli nie jest jeszcze uruchomiona)

Aplikacja aranżacji poprawek wymaga włączenia usługi System Manager Repair w klastrze.

#### <a name="azure-clusters"></a>Klastry platformy Azure

W klastrach platformy Azure w warstwie trwałości Silver Usługa Menedżera naprawy jest domyślnie włączona. W przypadku klastrów platformy Azure w warstwie trwałości Gold może być włączona usługa Repair Manager, w zależności od tego, kiedy te klastry zostały utworzone. W klastrach platformy Azure w warstwie trwałości Bronze domyślnie nie jest włączona usługa Menedżera naprawy. Jeśli usługa jest już włączona, można ją sprawdzić w sekcji usługi systemowe w Service Fabric Explorer.

##### <a name="azure-portal"></a>Azure Portal
Menedżera naprawy można włączyć z Azure Portal w momencie konfigurowania klastra. Wybierz opcję **dołącz Menedżer naprawy** w obszarze **funkcje dodatków** w czasie konfiguracji klastra.
![Obraz przedstawiający Włączanie Menedżer naprawy z Azure Portal](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>Azure Resource Manager model wdrażania
Alternatywnie można użyć [modelu wdrażania Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) , aby włączyć usługę Repair Manager w nowych i istniejących klastrach Service Fabric. Pobierz szablon klastra, który chcesz wdrożyć. Można użyć przykładowych szablonów lub utworzyć szablon niestandardowego modelu wdrażania Azure Resource Manager. 

Aby włączyć usługę Repair Manager przy użyciu [Azure Resource Manager szablonu modelu wdrażania](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm):

1. Najpierw sprawdź, czy `apiversion` jest `2017-07-01-preview` ustawiony na wartość dla `Microsoft.ServiceFabric/clusters` zasobu. Jeśli jest inny, należy zaktualizować `apiVersion` wartość do wartości `2017-07-01-preview` lub wyższej:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Teraz Włącz usługę Repair Manager, dodając następującą `addonFeatures` sekcję `fabricSettings` po sekcji:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Po zaktualizowaniu szablonu klastra przy użyciu tych zmian należy je zastosować i umożliwić zakończenie uaktualniania. Teraz można zobaczyć usługę systemową Repair Manager działającą w klastrze. Jest on wywoływany `fabric:/System/RepairManagerService` w sekcji usługi systemowe w Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Autonomiczne klastry lokalne

Możesz użyć [ustawień konfiguracji autonomicznego klastra systemu Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest) , aby włączyć usługę Repair Manager w nowym i istniejącym klastrze Service Fabric.

Aby włączyć usługę Repair Manager:

1. Najpierw należy sprawdzić, `apiversion` czy w obszarze [ogólne Konfiguracje klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) jest `04-2017` ustawiona wartość lub większa:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

2. Teraz Włącz usługę naprawy Menedżera, dodając następującą `addonFeatures` sekcję `fabricSettings` poniżej sekcji, jak pokazano poniżej:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Zaktualizuj manifest klastra o te zmiany, używając zaktualizowanego manifestu klastra [Utwórz nowy klaster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) lub [Uaktualnij konfigurację klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server). Po uruchomieniu klastra z zaktualizowanym manifestem klastra można teraz zobaczyć usługę systemową Repair Manager działającą w klastrze, która jest wywoływana `fabric:/System/RepairManagerService`, w sekcji usługi systemowe w Eksploratorze Service Fabric.

### <a name="configure-windows-updates-for-all-nodes"></a>Skonfiguruj aktualizacje systemu Windows dla wszystkich węzłów

Automatyczne aktualizacje systemu Windows mogą prowadzić do utraty dostępności, ponieważ w tym samym czasie można uruchomić wiele węzłów klastra. Aplikacja aranżacji poprawek domyślnie próbuje wyłączyć automatyczne Windows Update na każdym węźle klastra. Jeśli jednak ustawienia są zarządzane przez administratora lub zasady grupy, zalecamy ustawienie zasad Windows Update na "powiadamianie przed pobraniem".

## <a name="download-the-app-package"></a>Pobierz pakiet aplikacji

Aby pobrać pakiet aplikacji, odwiedź [stronę](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) wydania usługi GitHub w aplikacji aranżacji poprawki.

## <a name="configure-the-app"></a>Konfigurowanie aplikacji

Zachowanie aplikacji aranżacji patch można skonfigurować tak, aby spełniały Twoje potrzeby. Zastąp wartości domyślne, przekazując parametr aplikacji podczas tworzenia lub aktualizowania aplikacji. Parametry aplikacji można podać, określając `ApplicationParameter` `Start-ServiceFabricApplicationUpgrade` dla poleceń cmdlet lub `New-ServiceFabricApplication` .

|**Parametr**        |**Typ**                          | **Szczegóły**|
|:-|-|-|
|MaxResultsToCache    |Długie                              | Maksymalna liczba Windows Updateych wyników, które powinny być buforowane. <br>Wartość domyślna to 3000 przy założeniu, że: <br> -Liczba węzłów wynosi 20. <br> -Liczba aktualizacji w węźle miesięcznie wynosi pięć. <br> -Liczba wyników na operację może wynosić 10. <br> -Wyniki z ostatnich trzech miesięcy powinny być przechowywane. |
|TaskApprovalPolicy   |Enum <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy wskazuje zasady, które mają być używane przez usługę koordynatora do instalowania aktualizacji systemu Windows na Service Fabric węzłach klastra.<br>                         Dozwolone wartości to: <br>                                                           <b>NodeWise</b>. Windows Update jest instalowany jeden węzeł w danym momencie. <br>                                                           <b>UpgradeDomainWise</b>. Windows Update zainstalowaniu jednej domeny uaktualnienia w danym momencie. (Maksymalnie, wszystkie węzły należące do domeny uaktualnienia mogą przejść dla Windows Update.)<br> Zapoznaj się z sekcją [często zadawanych pytań](#frequently-asked-questions) , aby określić, które zasady najlepiej nadają się do użycia w klastrze.
|LogsDiskQuotaInMB   |Długie  <br> Wartooć 1024)               |Maksymalny rozmiar dzienników aplikacji Orchestration dla aranżacji w MB, które mogą być utrwalane lokalnie w węzłach.
| WUQuery               | ciąg<br>Wartooć "IsInstalled = 0")                | Zapytanie w celu pobrania aktualizacji systemu Windows. Aby uzyskać więcej informacji, zobacz [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| InstallWindowsOSOnlyUpdates | Boolean <br> (wartość domyślna: false)                 | Użyj tej flagi, aby określić, które aktualizacje mają zostać pobrane i zainstalowane. Następujące wartości są dozwolone <br>true — instaluje tylko aktualizacje systemu operacyjnego Windows.<br>false — instaluje wszystkie dostępne aktualizacje na komputerze.          |
| WUOperationTimeOutInMinutes | Int <br>Wartooć 90)                   | Określa limit czasu dla każdej operacji Windows Update (Wyszukaj lub Pobierz lub zainstaluj). Jeśli operacja nie zostanie zakończona w określonym limicie czasu, zostanie przerwana.       |
| WURescheduleCount     | Int <br> Wartooć 5)                  | Maksymalna liczba ponownych uruchomień usługi w usłudze Windows Update w przypadku niepowodzenia operacji.          |
| WURescheduleTimeInMinutes | Int <br>Wartooć 30) | Interwał, w którym usługa ponownie planuje usługę Windows Update w przypadku niepowodzenia utrwalania. |
| WUFrequency           | Ciąg rozdzielony przecinkami (domyślnie: "Tygodniowe, środa, 7:00:00")     | Częstotliwość instalowania Windows Update. Format i możliwe wartości to: <br>-Miesięcznie, DD, gg: MM: SS, na przykład miesięcznie, 5, 12:22:32.<br>Dozwolone wartości pola DD (Day) to liczby z zakresu od 1-28 do "Last". <br> -Weekly, dzień, gg: MM: SS, na przykład Weekly, wtorek, 12:22:32.  <br> -Codziennie, gg: MM: SS, na przykład codziennie, 12:22:32.  <br> -Brak wskazuje, że nie należy wykonać Windows Update.  <br><br> Należy pamiętać, że czasy są w formacie UTC.|
| AcceptWindowsUpdateEula | Boolean <br>(Wartość domyślna: true) | Ustawiając tę flagę, aplikacja akceptuje umowę licencyjną użytkownika końcowego dla Windows Update w imieniu właściciela maszyny.              |

> [!TIP]
> Jeśli chcesz, aby Windows Update się natychmiast, ustaw `WUFrequency` względem czasu wdrożenia aplikacji. Załóżmy na przykład, że masz klaster testowy z pięcioma węzłami i planujesz wdrożenie aplikacji na około 5:00 PM czasu UTC. Jeśli założono, że uaktualnienie lub wdrożenie aplikacji trwa 30 minut, ustaw wartość WUFrequency na "codziennie, 17:30:00"

## <a name="deploy-the-app"></a>Wdrażanie aplikacji

1. Wykonaj wszystkie kroki wymagań wstępnych, aby przygotować klaster.
2. Wdróż aplikację aranżacji poprawek, taką jak każda inna aplikacja Service Fabric. Aplikację można wdrożyć przy użyciu programu PowerShell. Wykonaj kroki opisane w temacie [wdrażanie i usuwanie aplikacji przy użyciu programu PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
3. Aby skonfigurować aplikację w czasie wdrażania, należy przekazać `ApplicationParameter` `New-ServiceFabricApplication` do polecenia cmdlet. Dla wygody użytkownika udostępniamy skrypt Deploy. ps1 wraz z aplikacją. Aby użyć skryptu:

    - Nawiązywanie połączenia z klastrem Service Fabric `Connect-ServiceFabricCluster`przy użyciu programu.
    - Wykonaj skrypt PowerShell Deploy. ps1 z odpowiednią `ApplicationParameter` wartością.

> [!NOTE]
> Zachowaj skrypt i folder aplikacji PatchOrchestrationApplication w tym samym katalogu.

## <a name="upgrade-the-app"></a>Uaktualnianie aplikacji

Aby uaktualnić istniejącą aplikację aranżacji patch przy użyciu programu PowerShell, wykonaj kroki opisane w [Service Fabric Uaktualnianie aplikacji przy użyciu programu PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell).

## <a name="remove-the-app"></a>Usuń aplikację

Aby usunąć aplikację, wykonaj kroki opisane w temacie [wdrażanie i usuwanie aplikacji przy użyciu programu PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

Dla wygody udostępnimy skrypt undeploy. ps1 wraz z aplikacją. Aby użyć skryptu:

  - Nawiązywanie połączenia z klastrem Service Fabric ```Connect-ServiceFabricCluster```przy użyciu programu.

  - Wykonanie skryptu programu PowerShell undeploy. ps1.

> [!NOTE]
> Zachowaj skrypt i folder aplikacji PatchOrchestrationApplication w tym samym katalogu.

## <a name="view-the-windows-update-results"></a>Wyświetl wyniki Windows Update

Aplikacja aranżacji poprawek uwidacznia interfejsy API REST, aby wyświetlić wyniki historyczne dla użytkownika. Przykład JSON wynik:
```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2019-05-13T08:44:56.4836889Z",
        "OperationStartTime": "2019-05-13T08:44:33.5285601Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0,
            "HResult": 0
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

Pola w formacie JSON są opisane poniżej.

Pole | Wartości | Szczegóły
-- | -- | --
OperationResult | 0 — powodzenie<br> 1 — powodzenie z błędami<br> 2 — Niepowodzenie<br> 3 — przerwana<br> 4 — przerwano z limitem czasu | Wskazuje wynik operacji ogólnej (zazwyczaj obejmuje instalację jednej lub kilku aktualizacji).
ResultCode | Analogicznie jak klasy OperationResult | To pole wskazuje wynik operacji instalacji dla pojedynczej aktualizacji.
OperationType | 1 — Instalacja<br> 0 — wyszukiwanie i pobieranie.| Instalacja jest jedyną operacją, która będzie domyślnie wyświetlana w wynikach.
WindowsUpdateQuery | Wartość domyślna to "IsInstalled = 0" |Zapytanie usługi Windows Update, które zostało użyte do wyszukiwania aktualizacji. Aby uzyskać więcej informacji, zobacz [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
RebootRequired | true — wymagany jest ponowny rozruch<br> FAŁSZ — nie jest wymagane ponowne uruchomienie komputera | Wskazuje, czy do ukończenia instalacji aktualizacji jest wymagany ponowny rozruch.
OperationStartTime | DateTime | Wskazuje czas, w którym uruchomiono operację (pobieranie/instalacja).
OperationTime | DateTime | Wskazuje godzinę ukończenia operacji (pobieranie/instalacja).
Wynik | 0 — powodzenie<br> inne — niepowodzenie| Wskazuje przyczynę niepowodzenia aktualizacji systemu Windows z updateID "7392acaf-6a85-427c-8a8d-058c25beb0d6".

Jeśli nie zaplanowano jeszcze żadnej aktualizacji, wynikowy kod JSON jest pusty.

Zaloguj się do klastra w celu zbadania Windows Update wyników. Następnie sprawdź adres repliki dla podstawowego usługi koordynatora i naciśnij adres URL z przeglądarki: http://&lt;Replica-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/ GetWindowsUpdateResults.

Punkt końcowy REST usługi koordynatora ma port dynamiczny. Aby sprawdzić dokładny adres URL, zapoznaj się z Service Fabric Explorer. Na przykład wyniki są dostępne pod adresem `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults`.

![Obraz punktu końcowego REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


Jeśli zwrotny serwer proxy jest włączony w klastrze, można również uzyskać dostęp do adresu URL spoza klastra.
Punkt końcowy, który musi zostać trafiony,&lt;to&gt;http://&lt;SERVERURL&gt;: REVERSEPROXYPORT/PatchOrchestrationApplication/CoordinatorService/V1/GetWindowsUpdateResults.

Aby włączyć zwrotny serwer proxy w klastrze, postępuj zgodnie z instrukcjami w [odwrotnym serwerze proxy na platformie Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> Po skonfigurowaniu zwrotnego serwera proxy wszystkie mikrousługi w klastrze, które uwidaczniają punkt końcowy HTTP, są adresowane spoza klastra.

## <a name="diagnosticshealth-events"></a>Zdarzenia diagnostyki/kondycji

W poniższej sekcji omówiono debugowanie/diagnozowanie problemów z aktualizacjami poprawek za pomocą aplikacji aranżacji poprawek w klastrach Service Fabric.

> [!NOTE]
> Należy mieć zainstalowaną wersję 1.4.0 w wersji 2.0, aby można było uzyskać wiele z poniższych elementów o nazwie udoskonalenia samodiagnostyki.

NodeAgentNTService tworzy [zadania naprawy](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet) w celu zainstalowania aktualizacji w węzłach. Każde zadanie jest następnie przygotowywane przez CoordinatorService zgodnie z zasadami zatwierdzania zadań. Przygotowane zadania są ostatecznie zatwierdzone przez Menedżer naprawy, które nie zatwierdzą żadnego zadania, Jeśli klaster jest w złej kondycji. Umożliwia przechodzenie krok po kroku, aby zrozumieć, jak aktualizacje są przebiegać w węźle.

1. NodeAgentNTService, uruchomione w każdym węźle, wyszukuje dostępne Windows Update w zaplanowanym czasie. Jeśli aktualizacje są dostępne, zostaną pobrane do węzła.
2. Po pobraniu aktualizacji NodeAgentNTService tworzy odpowiednie zadanie naprawy dla węzła o nazwie POS___ < unique_id >. Jeden z nich może przeglądać te zadania naprawy przy użyciu polecenia cmdlet [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) lub SFX w sekcji Szczegóły węzła. Gdy zadanie naprawy zostanie utworzone, program szybko przejdzie do [żądanego stanu](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet).
3. Usługa koordynatora okresowo szuka zadań naprawczych w stanie zatwierdzono i aktualizuje je w celu przygotowania stanu na podstawie TaskApprovalPolicy. Jeśli TaskApprovalPolicy jest skonfigurowany jako NodeWise, zadanie naprawy odpowiadające węzłowi jest przygotowywane tylko wtedy, gdy w trakcie przygotowywania/zatwierdzania/wykonywania/przywracania nie ma żadnych innych zadań naprawy. Podobnie, w przypadku UpgradeWise TaskApprovalPolicy, jest on zapewniony w dowolnym momencie, że istnieją zadania w powyższych Stanach tylko dla węzłów należących do tej samej domeny uaktualnienia. Gdy zadanie naprawy zostanie przeniesione do stanu przygotowywania, odpowiedni węzeł Service Fabric jest [wyłączony](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) z intencją jako "Uruchom ponownie".

   POA (v 1.4.0 i nowsze) zapisuje zdarzenia z właściwością "ClusterPatchingStatus" na CoordinaterService, aby wyświetlić węzły, które są aktualizowane. Na poniższej ilustracji przedstawiono aktualizacje, które są instalowane w systemie _poanode_0:

    [![Obraz stanu poprawek klastra](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

4. Po wyłączeniu węzła zadanie naprawy jest przenoszone do stanu wykonywania.
   
   >[!NOTE]
   > Węzeł zablokowany w stanie wyłączonym może blokować nowe zadanie naprawy, co spowoduje zatrzymanie operacji stosowania poprawki w klastrze.

5. Gdy zadanie naprawy jest w stanie wykonywania, rozpocznie się instalacja poprawek w tym węźle. W tym miejscu po zainstalowaniu poprawki węzeł może lub nie może zostać uruchomiony ponownie w zależności od poprawki. Opublikuj to zadanie naprawy, aby przywrócić stan przywracania, co umożliwia ponowne włączenie węzła, a następnie jego oznaczenie zostało zakończone.

   W programie v 1.4.0 i nowszych wersjach aplikacji można znaleźć stan aktualizacji, przeglądając zdarzenia kondycji na NodeAgentService z właściwością "WUOperationStatus-[nodename]". Wyróżnione sekcje na poniższej ilustracji pokazują stan aktualizacji systemu Windows w węźle "poanode_0" i "poanode_2":

   [![Obraz stanu operacji aktualizacji systemu Windows](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Obraz stanu operacji aktualizacji systemu Windows](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   Może również uzyskać szczegółowe informacje za pomocą programu PowerShell, łącząc się z klastrem i pobierając stan zadania naprawy przy użyciu polecenia [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps). Podobnie jak w poniższym przykładzie pokazano, że zadanie "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15" jest w stanie DownloadComplete. Oznacza to, że aktualizacje zostały pobrane w węźle "poanode_2", a instalacja zostanie podjęta, gdy zadanie przejdzie do stanu wykonania.

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   Jeśli nadal chcesz znaleźć więcej informacji, zaloguj się do określonych maszyn wirtualnych/maszyn wirtualnych, aby dowiedzieć się więcej o problemie przy użyciu dzienników zdarzeń systemu Windows. Wymienione powyżej zadanie naprawy może mieć tylko te podstany wykonawcy:

      ExecutorSubState | Szczegóły
    -- | -- 
      Brak = 1 |  Oznacza, że nie było trwającą operacją w węźle. Możliwe przejścia stanu.
      DownloadCompleted=2 | Oznacza, że operacja pobierania zakończyła się pomyślnie, częściowym błędem lub błędem.
      InstallationApproved=3 | Oznacza, że operacja pobierania została zakończona wcześniej, a Menedżer naprawya zatwierdziła instalację.
      InstallationInProgress=4 | Odnosi się do stanu wykonania zadania naprawy.
      InstallationCompleted=5 | Oznacza, że instalacja została ukończona z sukcesem, częściowym sukcesem lub niepowodzeniem.
      RestartRequested = 6 | Oznacza, że instalacja poprawek została zakończona, a w węźle istnieje oczekująca akcja ponownego uruchomienia.
      RestartNotNeeded=7 |  Oznacza, że ponowne uruchomienie nie jest wymagane po zakończeniu instalacji poprawki.
      RestartCompleted=8 | Oznacza, że ponowne uruchomienie zostało pomyślnie zakończone.
      OperationCompleted=9 | Operacja aktualizacji systemu Windows została ukończona pomyślnie.
      OperationAborted=10 | Oznacza, że operacja aktualizacji systemu Windows została przerwana.

6. W programie v 1.4.0 i nowszych aplikacjach po zakończeniu próby aktualizacji w węźle zostanie wysłane zdarzenie z właściwością "WUOperationStatus-[nodename]" na NodeAgentService w celu powiadomienia, gdy zostanie podjęta kolejna próba pobrania i zainstalowania aktualizacji, Uruchom. Zapoznaj się z poniższym obrazem:

     [![Obraz stanu operacji aktualizacji systemu Windows](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Dzienniki aplikacji aranżacji poprawek są zbierane w ramach dzienników środowiska uruchomieniowego Service Fabric.

Jeśli chcesz przechwytywać dzienniki za pośrednictwem wybranego narzędzia diagnostycznego lub potoku. Aplikacja aranżacji poprawek używa poniżej stałych identyfikatorów dostawcy do rejestrowania zdarzeń za pośrednictwem [źródła zdarzeń](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Raporty dotyczące kondycji

Aplikacja aranżacji patch publikuje także raporty o kondycji dotyczące usługi koordynatora lub usługi agenta węzłów w następujących przypadkach:

#### <a name="the-node-agent-ntservice-is-down"></a>NTService agenta węzła nie działa

Jeśli Agent węzła NTService nie działa w węźle, generowany jest raport o kondycji na poziomie ostrzeżenia dla usługi agenta węzła.

#### <a name="the-repair-manager-service-is-not-enabled"></a>Usługa Menedżera naprawy nie jest włączona

Jeśli w klastrze nie znaleziono usługi Menedżera naprawy, dla usługi koordynatora zostanie wygenerowany raport o kondycji na poziomie ostrzeżenia.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

PYTANIE: **Dlaczego mój klaster jest wyświetlany w stanie błędu, gdy jest uruchomiona aplikacja aranżacji poprawek?**

A. Podczas instalacji aplikacja aranżacji patch wyłącza lub uruchamia ponownie węzły, co może tymczasowo spowodować kondycję klastra.

W oparciu o zasady dla aplikacji, jeden węzeł może przejść w dół operacji poprawiania *lub* cała domena uaktualnienia może przejść jednocześnie.

Po zakończeniu instalacji Windows Update węzły są ponownie włączone po ponownym uruchomieniu.

W poniższym przykładzie klaster przeszedł do stanu błędu tymczasowo, ponieważ dwa węzły były wyłączone i zasady MaxPercentageUnhealthyNodes zostały naruszone. Ten błąd jest tymczasowy do momentu trwającej operacji poprawiania.

![Obraz przedstawiający klaster w złej kondycji](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Jeśli problem będzie się powtarzać, zapoznaj się z sekcją rozwiązywania problemów.

PYTANIE: **Aplikacja aranżacji poprawek jest w stanie ostrzegawczym**

A. Sprawdź, czy raport kondycji opublikowany dla aplikacji jest główną przyczyną. Zwykle ostrzeżenie zawiera szczegółowe informacje o problemie. Jeśli problem jest przejściowy, aplikacja oczekuje na Autoodzyskiwanie z tego stanu.

PYTANIE: **Co mogę zrobić, jeśli mój klaster jest w złej kondycji i muszę przeprowadzić pilną aktualizację systemu operacyjnego?**

A. Aplikacja aranżacji poprawek nie instaluje aktualizacji, gdy klaster jest w złej kondycji. Spróbuj przełączyć klaster do stanu dobrej kondycji, aby odblokować przepływ pracy aplikacji aranżacji poprawek.

PYTANIE: **Czy należy ustawić TaskApprovalPolicy jako "NodeWise" lub "UpgradeDomainWise" dla mojego klastra?**

A. "UpgradeDomainWise" sprawia, że ogólna poprawka klastra jest szybsza, co poprawia wszystkie węzły należące do domeny uaktualnienia równolegle. Oznacza to, że węzły należące do całej domeny uaktualnienia byłyby niedostępne (w stanie [wyłączone](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled) ) podczas procesu stosowania poprawek.

W przeciwieństwie do zasad "NodeWise" tylko jeden węzeł w tym samym czasie, to oznacza to, że ogólna poprawka klastra zajmie więcej czasu. Jednak co najwyżej jeden węzeł będzie niedostępny (w stanie [wyłączone](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled) ) podczas procesu poprawiania.

Jeśli klaster może tolerować działanie w N-1 Liczba domenach uaktualnienia w cyklu stosowania poprawek (gdzie N to całkowita liczba domen uaktualnienia w klastrze), można ustawić zasady jako "UpgradeDomainWise", w przeciwnym razie ustawić je na "NodeWise".

PYTANIE: **Ile czasu zajmuje poprawka węzła?**

A. Stosowanie poprawek w węźle może potrwać kilka minut (na przykład: [Aktualizacje definicji Windows Defender](https://www.microsoft.com/en-us/wdsi/definitions)) na godziny (na przykład: [Aktualizacje zbiorcze systemu Windows](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)). Czas wymagany do zastosowania poprawek węzła zależy głównie od 
 - Rozmiar aktualizacji
 - Liczba aktualizacji, które należy zastosować w oknie poprawek
 - Czas potrzebny na zainstalowanie aktualizacji, ponowne uruchomienie węzła (jeśli jest to wymagane) i zakończenie kroków instalacji po ponownym uruchomieniu.
 - Wydajność maszyn wirtualnych/maszyn i warunków sieciowych.

PYTANIE: **Jak długo trwa poprawka całego klastra?**

A. Czas wymagany do zastosowania poprawki całego klastra zależy od następujących czynników:

- Czas wymagany do zastosowania poprawki do węzła.
- Zasady usługi koordynatora. -Zasady `NodeWise`domyślne,, w wyniku stosowania poprawek tylko jeden węzeł, co może być wolniejsze niż `UpgradeDomainWise`. Na przykład: Jeśli w węźle trwa wartość 1 godzina, aby można było zastosować poprawki, w celu zastosowania poprawki do 20 węzłów (tego samego typu węzły) klastra z 5 domenami uaktualnienia każdy z nich zawiera 4 węzły.
    - Poprawka całego klastra powinna trwać po 20 godzinach`NodeWise`
    - Jeśli zasady powinny trwać o około 5 godzinach`UpgradeDomainWise`
- Ładowanie klastra — każda operacja stosowania poprawek wymaga ponownego lokalizowania obciążenia klienta do innych dostępnych węzłów w klastrze. W tym czasie poprawka w węźle będzie [](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling) w stanie wyłączać. Jeśli klaster działa blisko szczytowego obciążenia, proces wyłączania zajmie dłuższy czas. W związku z tym ogólna procedura stosowania poprawek może wydawać się niska w takich warunkach.
- Błędy kondycji klastra podczas stosowania poprawek — ewentualne [obniżenie](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) [kondycji klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) spowoduje przerwanie procesu stosowania poprawki. Spowoduje to dodanie do całkowitego czasu wymaganego do poprawienia całego klastra.

PYTANIE: **Dlaczego widzę niektóre aktualizacje w Windows Update wynikach uzyskanych za pośrednictwem interfejsu API REST, ale nie w ramach historii Windows Update na maszynie?**

A. Niektóre aktualizacje produktów będą widoczne tylko w odpowiedniej historii aktualizacji/poprawek. Na przykład aktualizacje usługi Windows Defender mogą nie być wyświetlane w historii Windows Update w systemie Windows Server 2016.

PYTANIE: **Czy można zastosować poprawkę aplikacji aranżacji do poprawki mojego klastra deweloperskiego (klaster z jednym węzłem)?**

A. Nie, aplikacja aranżacji poprawek nie może zostać użyta do poprawki klastra z jednym węzłem. To ograniczenie jest zależne od projektu, ponieważ usługi [usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) lub aplikacje klienta będą powodować przestoje i w związku z tym jakiekolwiek zadanie naprawy dla poprawek nigdy nie zostanie zatwierdzone przez Menedżera napraw.

PYTANIE: **Jak mogę zastosować poprawki do węzłów klastra w systemie Linux?**

A. Zapoznaj się z [automatycznymi uaktualnieniami obrazu systemu operacyjnego na platformie Azure Virtual Machine Scale](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) , aby organizować aktualizacje w systemie Linux.

P.**dlaczego cykl aktualizacji trwa tak długo?**

A. Wykonaj zapytanie dotyczące wynikowego kodu JSON, a następnie przejdź przez wpis cyklu aktualizacji dla wszystkich węzłów, a następnie spróbuj sprawdzić czas trwania instalacji aktualizacji na każdym węźle przy użyciu OperationStartTime i OperationTime (OperationCompletionTime). W przypadku dużego przedziału czasu, w którym nie przeprowadzono aktualizacji, może to być spowodowane faktem, że klaster był w stanie błędu, a ponieważ Menedżer naprawy nie zatwierdził żadnych innych zadań naprawy POA. Jeśli instalacja aktualizacji trwała w dowolnym węźle, może to oznaczać, że węzeł nie został zaktualizowany przez długi czas, a wiele aktualizacji oczekuje na instalację, co trwało. Może również istnieć przypadek, w którym nastąpi zablokowanie poprawek w węźle, ze względu na to, że węzeł jest blokowany w stanie wyłączania, co zwykle zdarza się, ponieważ wyłączenie węzła może prowadzić do sytuacji utraty kworum/danych.

PYTANIE: **Dlaczego jest wymagane, aby wyłączyć węzeł, gdy jest on poprawiany przez plik POA?**

A. Aplikacja aranżacji poprawek wyłącza węzeł z zamiarem "restart", która powoduje zatrzymanie/ponowną alokację wszystkich usług usługi Service Fabric uruchomionych w węźle. W tym celu należy się upewnić, że aplikacje nie zakończą się przy użyciu kombinacji nowych i starych bibliotek DLL, dlatego nie zaleca się stosowania poprawek do węzła bez wyłączania.

## <a name="disclaimers"></a>Zastrzeżenia

- Aplikacja aranżacji poprawek akceptuje umowę licencyjną użytkownika końcowego Windows Update w imieniu użytkownika. Opcjonalnie ustawienie to może być wyłączone w konfiguracji aplikacji.

- Aplikacja aranżacji poprawek zbiera dane telemetryczne w celu śledzenia użycia i wydajności. Dane telemetryczne aplikacji są zgodne z ustawieniem ustawienia telemetrii środowiska uruchomieniowego Service Fabric, które jest domyślnie włączone.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="a-node-is-not-coming-back-to-up-state"></a>Węzeł nie wraca do stanu up

**Węzeł może być zablokowany w stanie wyłączania, ponieważ**:

Sprawdzanie bezpieczeństwa jest w stanie oczekiwania. Aby rozwiązać ten problem, upewnij się, że wystarczająca liczba węzłów jest w dobrej kondycji.

**Węzeł może być zablokowany w stanie wyłączenia, ponieważ**:

- Węzeł został wyłączony ręcznie.
- Węzeł został wyłączony z powodu trwającego zadania infrastruktury platformy Azure.
- Węzeł został tymczasowo wyłączony przez aplikację aranżacji patch w celu zastosowania poprawki do węzła.

**Węzeł może być zablokowany w stanie down, ponieważ**:

- Węzeł został umieszczony w stanie down ręcznie.
- Węzeł zostanie ponownie uruchomiony (może zostać wyzwolony przez aplikację aranżacji poprawek).
- Węzeł nie działa ze względu na wadliwą maszynę wirtualną lub maszynę lub problemy z łącznością sieciową.

### <a name="updates-were-skipped-on-some-nodes"></a>Aktualizacje zostały pominięte na niektórych węzłach

Aplikacja aranżacji poprawek próbuje zainstalować aktualizację systemu Windows zgodnie z zasadami ponownego planowania. Usługa próbuje odzyskać węzeł i pominąć aktualizację zgodnie z zasadami aplikacji.

W takim przypadku Raport kondycji na poziomie ostrzeżenia jest generowany w odniesieniu do usługi agenta węzła. Wynik dla Windows Update również zawiera możliwą przyczynę niepowodzenia.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>Kondycja klastra przechodzi do błędu podczas instalacji aktualizacji

Wadliwa Aktualizacja systemu Windows może spowodować, że kondycja aplikacji lub klastra znajduje się w określonym węźle lub domenie uaktualnienia. Aplikacja aranżacji poprawek nie kontynuuje żadnej kolejnej Windows Update operacji, dopóki klaster nie będzie w dobrej kondycji.

Administrator musi interweniować i określić przyczynę złej kondycji aplikacji lub klastra z powodu Windows Update.

## <a name="release-notes"></a>Informacje o wersji

>[!NOTE]
> Począwszy od wersji 1.4.0, informacje o wersji i wydania można znaleźć na [stronie](https://github.com/microsoft/Service-Fabric-POA/releases/)wydania usługi GitHub.

### <a name="version-110"></a>Wersji 1.1.0
- Wydanie publiczne

### <a name="version-111"></a>Wersja 1.1.1
- Naprawiono usterkę w SetupEntryPoint NodeAgentService, która uniemożliwiła instalację NodeAgentNTService.

### <a name="version-120"></a>Wersji 1.2.0 lub nowszej

- Poprawki błędów wokół przepływu pracy ponownego uruchomienia systemu.
- Poprawka błędu podczas tworzenia zadań Menedżera zasobów, z powodu tego, że sprawdzanie kondycji podczas przygotowywania zadań naprawy nie było wykonywane zgodnie z oczekiwaniami.
- Zmieniono tryb uruchamiania usługi Windows POANodeSvc z automatycznie na opóźniony — automatycznie.

### <a name="version-121"></a>Wersja 1.2.1

- Poprawka błędu w przepływie pracy skalowania w dół klastra. Wprowadzono logikę wyrzucania elementów bezużytecznych dla zadań naprawy POA należących do nieistniejących węzłów.

### <a name="version-122"></a>Wersja 1.2.2

- Różne poprawki błędów.
- Pliki binarne są teraz podpisane.
- Dodano link sfpkg dla aplikacji.

### <a name="version-130"></a>Wersja 1.3.0

- Ustawienie InstallWindowsOSOnlyUpdates na false spowoduje zainstalowanie wszystkich dostępnych aktualizacji.
- Zmieniono logikę wyłączania aktualizacji automatycznych. Eliminuje to usterkę, w której aktualizacje automatyczne nie zostały wyłączone na serwerze 2016 i nowszych.
- Sparametryzowane ograniczenie położenia dla mikrousług POA dla zaawansowanych przypadków użycia.

### <a name="version-131"></a>Wersja 1.3.1
- Naprawianie regresji w przypadku, gdy POA 1.3.0 nie będzie działał w systemie Windows Server 2012 R2 lub niższym z powodu niepowodzenia wyłączenia aktualizacji automatycznych. 
- Naprawianie usterki, w której konfiguracja InstallWindowsOSOnlyUpdates jest zawsze wybierana jako true.
- Zmiana wartości domyślnej InstallWindowsOSOnlyUpdates na false.

### <a name="version-132"></a>Wersja 1.3.2
- Rozwiązanie problemu, który zadziałał w cyklu życia poprawek w węźle w przypadku, gdy istnieją węzły o nazwie, która jest podzbiorem bieżącej nazwy węzła. W przypadku takich węzłów możliwe jest stosowanie poprawek lub oczekiwanie na ponowne uruchomienie. 
