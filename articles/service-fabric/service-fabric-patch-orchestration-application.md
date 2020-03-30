---
title: Poprawianie systemu operacyjnego Windows w klastrze sieci szkieletowej usług
description: W tym artykule omówiono sposób automatyzacji poprawek systemu operacyjnego w klastrze sieci szkieletowej usług przy użyciu aplikacji aranżacji poprawek.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: atsenthi
ms.openlocfilehash: 857a4da0b24d600ecc572933af578e2e8faf501a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366326"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Poprawianie systemu operacyjnego Windows w klastrze sieci szkieletowej usług

> 
> [!IMPORTANT]
> Od 30 kwietnia 2019 r. aplikacja Patch Orchestration w wersji 1.2.* nie jest już obsługiwana. Pamiętaj, aby uaktualnić do najnowszej wersji.

> [!NOTE]
> Pobieranie [automatycznych uaktualnień obrazu systemu operacyjnego w zestawie skalowania maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) jest najlepszym rozwiązaniem dla utrzymania systemu operacyjnego załatane na platformie Azure. Automatyczne uaktualnienia obrazów systemu operacyjnego oparte na zestawie skalowania maszyny wirtualnej będą wymagały srebrnej lub większej trwałości w zestawie skalowania.
>

 Aplikacja aranżacji poprawek (POA) to otoka wokół usługi Azure Service Fabric Repair Manager, która umożliwia planowanie poprawek systemu operacyjnego opartego na konfiguracji dla klastrów hostowanych innych niż azure. Poa nie jest wymagane dla klastrów hostowanych innych niż Azure, ale planowanie instalacji poprawek przez domenę aktualizacji jest wymagane do poprawki hostów klastra sieci szkieletowej usług bez ponoszenia przestojów.

POA to aplikacja sieci szkieletowej usług, która automatyzuje łatanie systemu operacyjnego w klastrze sieci szkieletowej usług bez ponoszenia przestojów.

POA oferuje następujące funkcje:

- **Automatyczna instalacja aktualizacji systemu operacyjnego**. Aktualizacje systemu operacyjnego są automatycznie pobierane i instalowane. Węzły klastra są ponownie uruchamiane w razie potrzeby bez ponoszenia przestojów klastra.

- **Łatanie i integracja kondycji z uwzględnieniem klastra**. Podczas gdy POA stosuje aktualizacje, monitoruje kondycję węzłów klastra. Węzły klastra są aktualizowane o jeden węzeł naraz lub jedna domena aktualizacji naraz. Jeśli kondycja klastra ustępuje z powodu procesu łatanie, łatanie jest zatrzymane, aby zapobiec pogłębieniu problemu.

## <a name="internal-details-of-poa"></a>Wewnętrzne szczegóły POA

POA składa się z następujących podskładników:

- **Służba koordynatora**: Ta służba stanowa jest odpowiedzialna za:
    - Koordynowanie zadania usługi Windows Update dla całego klastra.
    - Przechowywanie wyniku zakończonych operacji usługi Windows Update.

- **Usługa agenta węzła:** Ta usługa bezstanowa jest uruchamiana we wszystkich węzłach klastra sieci szkieletowej usług. Usługa jest odpowiedzialna za:
    - Boottrapping agenta węzła NTService.
    - Monitorowanie agenta węzła NTService.

- **Agent węzła NTService:** Ta usługa Systemu Windows NT jest uruchamiana z uprawnieniami wyższego poziomu (SYSTEM). Natomiast usługa agenta węzła i usługa koordynatora działają z uprawnieniami niższego poziomu (USŁUGA SIECIOWA). Usługa jest odpowiedzialna za wykonywanie następujących zadań usługi Windows Update we wszystkich węzłach klastra:
    - Wyłączanie automatycznych aktualizacji systemu Windows w węźle.
    - Pobieranie i instalowanie aktualizacji systemu Windows zgodnie z zasadami dostarczonymi przez użytkownika.
    - Ponowne uruchomienie komputera po instalacji aktualizacji systemu Windows.
    - Przekazywanie wyników aktualizacji systemu Windows do usługi koordynatora.
    - Raportowanie raportów kondycji, jeśli operacja nie powiodła się po wyczerpaniu wszystkich ponownych prób.

> [!NOTE]
> POA używa usługi Menedżer naprawy sieci szkieletowej, aby wyłączyć lub włączyć węzeł i wykonać kontrole kondycji. Zadanie naprawy utworzone przez poa śledzi postęp usługi Windows Update dla każdego węzła.

## <a name="prerequisites"></a>Wymagania wstępne

> [!NOTE]
> Wymagana minimalna wersja programu .NET Framework to 4.6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Włącz usługę Menedżera napraw (jeśli nie jest jeszcze uruchomiona)

Poa wymaga, aby usługa Menedżera napraw była włączona w klastrze.

#### <a name="azure-clusters"></a>Klastry platformy Azure

Klastry platformy Azure w warstwie trwałości srebra mają domyślnie włączoną usługę Menedżera napraw. Klastry platformy Azure w warstwie trwałości złota mogą lub nie mogą mieć włączonej usługi Menedżera napraw, w zależności od tego, kiedy te klastry zostały utworzone. Klastry platformy Azure w warstwie trwałość z brązu, domyślnie nie mają włączonej usługi Menedżera napraw. Jeśli usługa jest już włączona, można ją wyświetlić w sekcji usług systemowych w Eksploratorze sieci szkieletowej usług.

##### <a name="the-azure-portal"></a>Witryna Azure Portal
Podczas konfigurowania klastra można włączyć menedżera napraw w witrynie Azure portal. Podczas konfigurowania klastra wybierz opcję **Dołącz Menedżera napraw** w obszarze **Funkcje dodatku**.

![Obraz włączania Menedżera napraw z witryny Azure portal](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="the-azure-resource-manager-deployment-model"></a>Model wdrażania usługi Azure Resource Manager
Alternatywnie można użyć [modelu wdrażania usługi Azure Resource Manager,](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) aby włączyć usługę Menedżera napraw w nowych i istniejących klastrach sieci szkieletowej usług. Pobierz szablon klastra, który chcesz wdrożyć. Można użyć przykładowych szablonów lub utworzyć niestandardowy szablon modelu wdrażania usługi Azure Resource Manager. 

Aby włączyć usługę Menedżera naprawy przy użyciu [szablonu modelu wdrażania usługi Azure Resource Manager,](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)wykonaj następujące czynności:

1. Sprawdź, `apiVersion` czy jest ustawiona na *2017-07-01-preview* dla zasobu *Microsoft.ServiceFabric/clusters.* Jeśli jest inny, musisz zaktualizować `apiVersion` do *wersji zapoznawczej 2017-07-01 lub nowszej:*

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

1. Włącz usługę Menedżera napraw, `addonFeatures` dodając następującą sekcję `fabricSettings` po sekcji:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Po zaktualizowaniu szablonu klastra o te zmiany zastosuj je i pozwól na zakończenie aktualizacji. Teraz można wyświetlić usługę Menedżera napraw uruchomioną w klastrze. To się nazywa *fabric:/System/RepairManagerService* w sekcji usług systemowych w Eksploratorze sieci szkieletowej usług. 

### <a name="standalone-on-premises-clusters"></a>Autonomiczne klastry lokalne

Aby włączyć usługę Menedżera napraw w nowym lub istniejącym klastrze sieci szkieletowej usług, można użyć [ustawień konfiguracji autonomicznego klastra systemu Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest).

Aby włączyć usługę Menedżera napraw:

1. Sprawdź, `apiVersion` czy w [ogólnych konfiguracjach klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) jest ustawiona na *04-2017* lub nowszą, jak pokazano poniżej:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

1. Włącz usługę Menedżera napraw, `addonFeatures` dodając następującą sekcję `fabricSettings` po sekcji, jak pokazano poniżej:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

1. Zaktualizuj manifest klastra za pomocą tych zmian przy użyciu zaktualizowanego manifestu [klastra, utwórz nowy klaster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) lub [uaktualnij konfigurację klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server). 

   Po uruchomieniu klastra ze zaktualizowanym manifestem klastra można wyświetlić usługę Menedżera napraw uruchomiona w klastrze. To się nazywa *fabric:/System/RepairManagerService*, i to w sekcji usług systemowych w Eksploratorze sieci szkieletowej usług.

### <a name="configure-windows-updates-for-all-nodes"></a>Konfigurowanie aktualizacji systemu Windows dla wszystkich węzłów

Automatyczne aktualizacje systemu Windows może prowadzić do utraty dostępności, ponieważ wiele węzłów klastra można ponownie uruchomić w tym samym czasie. Poa domyślnie próbuje wyłączyć automatyczne aktualizacje systemu Windows w każdym węźle klastra. Jeśli jednak ustawienia są zarządzane przez administratora lub zasady grupy, zalecamy ustawienie zasady usługi Windows Update na "Powiadom przed pobraniem" jawnie.

## <a name="download-the-application-package"></a>Pobierz pakiet aplikacji

Aby pobrać pakiet aplikacji, przejdź do [patch orchestration aplikacji strony w](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) usłudze GitHub.

## <a name="configure-poa-behavior"></a>Konfigurowanie zachowania poa

Zachowanie POA można skonfigurować zgodnie z potrzebami. Zastądnie wartości domyślne, przekazując parametr aplikacji podczas tworzenia lub aktualizowania aplikacji. Parametry aplikacji można podać, `ApplicationParameter` określając `Start-ServiceFabricApplicationUpgrade` `New-ServiceFabricApplication` polecenia cmdlet lub.

| Parametr        | Typ                          | Szczegóły |
|:-|-|-|
|MaxResultsToCache    |Długi                              | Maksymalna liczba wyników usługi Windows Update, które powinny być buforowane. <br><br>Wartość domyślna to 3000, przy założeniu, że: <br> &nbsp;&nbsp;- Liczba węzłów wynosi 20. <br> &nbsp;&nbsp;- Liczba aktualizacji węzła miesięcznie wynosi 5. <br> &nbsp;&nbsp;- Liczba wyników na operację może wynosić 10. <br> &nbsp;&nbsp;- Wyniki za ostatnie trzy miesiące powinny być przechowywane. |
|Polityka aplikacji TaskApproval   |Wyliczenie <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy wskazuje zasady, które mają być używane przez usługę koordynatora do instalowania aktualizacji systemu Windows w węzłach klastra sieci szkieletowej usług.<br><br>Dozwolone wartości to: <br>*NodeWise*: Aktualizacje systemu Windows są instalowane po jednym węźle naraz. <br> *UpgradeDomainWise*: Aktualizacje systemu Windows są instalowane w jednej domenie aktualizacji naraz. (Co najwyżej wszystkie węzły należące do domeny aktualizacji mogą przejść do aktualizacji systemu Windows).<br><br> Aby ułatwić podjęcie decyzji, które zasady są najlepiej odpowiednie dla klastra, zobacz sekcję [często zadawane pytania.](#frequently-asked-questions)
|DziennikSDiskQuotaInMB   |Długi  <br> (Domyślnie: *1024*)               | Maksymalny rozmiar dzienników aplikacji aranżacji poprawek w MB, które mogą być utrwalone lokalnie w węzłach.
| WUQuery ( WUQuery )               | ciąg<br>(Domyślnie: *IsInstalled=0*)                | Kwerenda, aby uzyskać aktualizacje systemu Windows. Aby uzyskać więcej informacji, zobacz [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| Instalowanie systemuWindowsOSOnlyUpdates | *Wartość logiczna* <br> (domyślnie: false)                 | Ta flaga służy do kontrolowania, które aktualizacje powinny być pobierane i instalowane. Następujące wartości są dozwolone <br>true - Instaluje tylko aktualizacje systemu operacyjnego Windows.<br>false - instaluje wszystkie dostępne aktualizacje na komputerze.          |
| WUOperationTimeOutInMinutes | int <br>(Domyślnie: *90*)                   | Określa limit czasu dla każdej operacji usługi Windows Update (wyszukaj lub pobierz lub zainstaluj). Jeśli operacja nie zostanie ukończona w określonym przełęczy, zostanie przerwana.       |
| Liczba WURescheduleCount     | int <br> (Domyślnie: *5*)                  | Maksymalna liczba ponownych planów usługi jest przestania od aktualizacji systemu Windows w przypadku trwałego niepowodzenia operacji.          |
| WURescheduleTimeInMinutes | int <br>(Domyślnie: *30*) | Interwał, w którym usługa ponownie planuje aktualizacje systemu Windows, jeśli błąd będzie się powtarzał. |
| Częstotliwość WU           | Ciąg rozdzielany przecinkami (domyślnie: *co tydzień, środa, 7:00:00*)     | Częstotliwość instalowania aktualizacji systemu Windows. Format i możliwe wartości to: <br>&nbsp;&nbsp;- Co miesiąc: DD, HH:MM:SS (na przykład *Miesięczny, 5,12:22:32)*<br>Dozwolone wartości dla pola DD (dzień) to liczby od 1 do 28 i "ostatnie". <br> &nbsp;&nbsp;- tygodniowo, dzień, HH:MM:SS (na przykład *tygodniowy, wtorek, 12:22:32)*  <br> &nbsp;&nbsp;- codziennie, HH:MM:SS (na przykład *codziennie, 12:22:32)*  <br> &nbsp;&nbsp;-  *Brak* wskazuje, że aktualizacje systemu Windows nie powinny być wykonywane.  <br><br> Czasy są w UTC.|
| AcceptWindowsUpdateEula | Wartość logiczna <br>(Domyślnie: *prawda)* | Ustawiając tę flagę, aplikacja akceptuje umowę licencyjną użytkownika końcowego dla witryny Windows Update w imieniu właściciela komputera.              |

> [!TIP]
> Jeśli chcesz, aby aktualizacje `WUFrequency` systemu Windows odbywało się natychmiast, ustaw względem czasu wdrożenia aplikacji. Załóżmy na przykład, że masz klaster testowy z pięcioma węzłami i planujesz wdrożyć aplikację około godziny 17:00 czasu UTC. Jeśli zakładasz, że uaktualnienie lub wdrożenie aplikacji trwa najwyżej 30 minut, ustaw WUFrequency jako *Daily, 17:30:00*.

## <a name="deploy-poa"></a>Wdrażanie poa

1. Zakończ wszystkie kroki wstępne, aby przygotować klaster.
1. Wdrażanie POA jak każda inna aplikacja sieci szkieletowej usług. Aby wdrożyć go za pomocą programu PowerShell, zobacz [Wdrażanie i usuwanie aplikacji przy użyciu programu PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
1. Aby skonfigurować aplikację w momencie wdrożenia, przekaż polecenie `ApplicationParameter` `New-ServiceFabricApplication` cmdlet. Dla Twojej wygody udostępniliśmy skrypt Deploy.ps1 wraz z aplikacją. Aby użyć skryptu:

    - Połącz się z klastrem sieci szkieletowej usług przy użyciu programu `Connect-ServiceFabricCluster`.
    - Wykonaj skrypt programu PowerShell Deploy.ps1 o odpowiedniej `ApplicationParameter` wartości.

> [!NOTE]
> Zachowaj skrypt i folder aplikacji *PatchOrchestrationApplication* w tym samym katalogu.

## <a name="upgrade-poa"></a>Uaktualnienie POA

Aby uaktualnić wersję POA przy użyciu programu PowerShell, postępuj zgodnie z instrukcjami [w uaktualnieniu aplikacji sieci szkieletowej usług przy użyciu programu PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell).

## <a name="remove-poa"></a>Usuń POA

Aby usunąć aplikację, postępuj zgodnie z instrukcjami w [programie Deploy i usuń aplikacje za pomocą programu PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

Dla Twojej wygody, dostarczyliśmy skrypt Undeploy.ps1 wraz z aplikacją. Aby użyć skryptu:

  - Połącz się z klastrem sieci szkieletowej usług przy użyciu programu ```Connect-ServiceFabricCluster```.
  - Wykonaj skrypt programu PowerShell Undeploy.ps1.

> [!NOTE]
> Zachowaj skrypt i folder aplikacji *PatchOrchestrationApplication* w tym samym katalogu.

## <a name="view-the-windows-update-results"></a>Wyświetlanie wyników usługi Windows Update

Poa udostępnia interfejsy API rest, aby wyświetlić wyniki historyczne dla użytkowników. Oto przykład wyniku JSON:

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

Pola JSON są opisane w poniższej tabeli:

Pole | Wartości | Szczegóły
-- | -- | --
Operationresult | 0 - Udało się<br> 1 - Udało się z błędami<br> 2 - Nie powiodło się<br> 3 - Przerwano<br> 4 - Przerwano z limitem czasu | Wskazuje wynik operacji ogólnej, która zwykle obejmuje instalację jednej lub więcej aktualizacji.
Kod wyników | Tak samo jak OperacjaResult | To pole wskazuje wynik operacji instalacji dla pojedynczej aktualizacji.
Operationtype | 1 - Instalacja<br> 0 - Wyszukiwanie i pobieranie| Domyślnie instalacja jest tylko OperationType, który jest wyświetlany w wynikach.
Zapytanie WindowsUpdateQuery | Wartość domyślna to "IsInstalled=0" | Kwerenda usługi Windows Update, która została użyta do wyszukiwania aktualizacji. Aby uzyskać więcej informacji, zobacz [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx).
Wymagane ponowne uruchomienie | true - wymagany był ponowny rozruch<br> false - ponowne uruchomienie nie było wymagane | Wskazuje, czy do ukończenia instalacji aktualizacji wymagane było ponowne uruchomienie.
OperationStartTime (Czas rozpoczęcia operacji) | DateTime | Wskazuje godzinę rozpoczęcia operacji(Pobieranie/instalacja).
Czas działania | DateTime | Wskazuje czas, w którym operacja(Pobieranie/instalacja) została zakończona.
Hresult | 0 - Udany<br> inne - awaria| Wskazuje przyczynę awarii aktualizacji systemu Windows z updateID "7392acaf-6a85-427c-8a8d-058c25beb0d6".

Jeśli żadna aktualizacja nie jest jeszcze zaplanowana, wynik JSON jest pusty.

Zaloguj się do klastra, aby zbadać wyniki usługi Windows Update. Znajdź adres IP repliki dla podstawowego adresu usługi koordynatora i otwórz następujący&lt;adres URL&gt;&lt;z&gt;przeglądarki: http:// REPLIKA-IP: ApplicationPort /PatchOrchestrationApplication/v1/GetWindowsUpdateResults.

Punkt końcowy REST dla usługi koordynatora ma port dynamiczny. Aby sprawdzić dokładny adres URL, zobacz Eksplorator sieci szkieletowej usług. Na przykład wyniki są *http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults*dostępne w pliku .

![Obraz punktu końcowego REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

Jeśli serwer proxy odwrotnej jest włączony w klastrze, można uzyskać dostęp do adresu URL spoza klastra, jak również.

Punktem końcowym, który należy osiągnąć, jest *&lt;http://&gt;&lt;SERVERURL:&gt;REVERSEPROXYPORT /PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults*.

Aby włączyć odwrotny serwer proxy w klastrze, postępuj zgodnie z instrukcjami w [odwrotnej sieci proxy w sieci szkieletowej usług Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> Po skonfigurowaniu odwrotnego serwera proxy wszystkie mikrousługi w klastrze, które udostępniają punkt końcowy HTTP, są adresowalne spoza klastra.

## <a name="diagnostics-and-health-events"></a>Diagnostyka i zdarzenia zdrowotne

W tej sekcji omówiono sposób debugowania lub diagnozowania problemów z aktualizacjami poprawek za pośrednictwem poa w klastrach sieci szkieletowej usług.

> [!NOTE]
> Aby uzyskać wiele z następujących wywoływanych, samodiagnostyki ulepszeń, należy mieć POA w wersji 1.4.0 lub nowszej.

Agent węzła NTService tworzy [zadania naprawy](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet) do instalowania aktualizacji w węzłach. Każde zadanie jest następnie przygotowywane przez służbę koordynatora zgodnie z zasadami zatwierdzania zadań. Na koniec przygotowane zadania są zatwierdzane przez Menedżera napraw, który nie zatwierdza żadnego zadania, jeśli klaster jest w stanie złej kondycji. 

Aby ułatwić zrozumienie, jak aktualizacje przebiegają w węźle, przejdźmy krok po kroku:

1. NodeAgentNTService, uruchomiony w każdym węźle, wyszukuje dostępne aktualizacje systemu Windows w zaplanowanym czasie. Jeśli aktualizacje są dostępne, pobiera je w węźle.

1. Po pobraniu aktualizacji agent węzła NTService tworzy odpowiednie zadanie naprawy węzła o nazwie *POS___\<unique_id>*. Te zadania naprawy można wyświetlić za pomocą polecenia cmdlet [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) lub przy użyciu SFX w sekcji szczegółów węzła. Po utworzeniu zadania naprawy szybko przechodzi do [ *stanu Twierdził* ](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet).

1. Usługa koordynatora okresowo wyszukuje zadania naprawy w stanie *Twierdził,* a następnie aktualizuje je do stanu *przygotowanie* na podstawie TaskApprovalPolicy. Jeśli TaskApprovalPolicy jest skonfigurowany jako NodeWise, zadanie naprawy, które odpowiada węzła jest przygotowywane tylko wtedy, gdy żadne inne zadanie naprawy nie jest obecnie w *przygotowaniu*, *Zatwierdzone*, *Wykonywanie*lub *Przywracanie* stanu. 

   Podobnie w przypadku UpgradeWise TaskApprovalPolicy istnieją zadania w poprzednich stanach tylko dla węzłów, które należą do tej samej domeny aktualizacji. Po przeniesieniu zadania naprawy do stanu *Przygotowanie* odpowiedni węzeł sieci szkieletowej usług jest [wyłączony](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) z zamiarem ustawionym na *Uruchom ponownie*.

   POA w wersji 1.4.0 i nowsze po zdarzenia z ClusterPatchingStatus właściwości na CoordinatorService do wyświetlania węzłów, które są załatane. Aktualizacje są instalowane na _poanode_0, jak pokazano na poniższej ilustracji:

    [![Obraz stanu poprawek klastra](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

1. Po wyłączeniu węzła zadanie naprawy jest przenoszone do stanu *wykonywania.* 
   
   > [!NOTE]
   > Węzeł, który utknął w stanie *Wyłączone* można zablokować nowe zadanie naprawy, która zatrzymuje operację poprawek w klastrze.

1. Gdy zadanie naprawy jest w stanie *wykonywania,* rozpoczyna się instalacja poprawki w tym węźle. Po zainstalowaniu poprawki węzeł może zostać ponownie uruchomiony lub nie, w zależności od poprawki. Następnie zadanie naprawy jest przenoszone do *przywracania* stanu, który ponownie można wywrzeć węzeł. Zadanie naprawy jest następnie oznaczone jako ukończone.

   W wersjach POA 1.4.0 i nowszych można znaleźć stan aktualizacji, wyświetlając zdarzenia kondycji na NodeAgentService z WUOperationStatus-\<NodeName> właściwości. Wyróżnione sekcje na poniższych obrazach przedstawiają stan aktualizacji systemu Windows w węzłach *poanode_0* i *poanode_2:*

   [![Obraz stanu operacji usługi Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Obraz stanu operacji usługi Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   Szczegóły można również uzyskać za pomocą programu PowerShell. Aby to zrobić, należy połączyć się z klastrem i pobrać stan zadania naprawy przy użyciu [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps). 
   
   W poniższym przykładzie zadanie "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15" jest w stanie *DownloadComplete.* Oznacza to, że aktualizacje zostały pobrane w węźle *poanode_2,* a instalacja zostanie podjęta po przesunięciu zadania do stanu *wykonywania.*

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   Jeśli nadal można znaleźć więcej problemów, zaloguj się do maszyny wirtualnej (VM) lub maszyn wirtualnych i dowiedz się o nich za pomocą dzienników zdarzeń systemu Windows. Wcześniej wymienione zadanie naprawy może istnieć tylko w następujących podstanach wykonawcy:

      ExecutorSubState | Opis
    -- | -- 
      Brak=1 |  Oznacza to, że nie było trwającej operacji w węźle. Stan może być w okresie przejściowym.
      DownloadCompleted=2 | Oznacza, że operacja pobierania została zakończona z powodzeniem, częściowym niepowodzeniem lub niepowodzeniem.
      InstalacjaZastosowana=3 | Oznacza to, że operacja pobierania została ukończona wcześniej, a Menedżer napraw zatwierdził instalację.
      InstallationInProgress=4 | Odpowiada stanowi wykonania zadania naprawy.
      InstalacjaZupełniona=5 | Oznacza, że instalacja została ukończona z powodzeniem, częściowym sukcesem lub niepowodzeniem.
      RestartRequested=6 | Oznacza, że instalacja poprawki została ukończona i istnieje oczekująca akcja ponownego uruchomienia w węźle.
      RestartNotNeeded=7 |  Oznacza to, że ponowne uruchomienie nie było potrzebne po zakończeniu instalacji poprawki.
      RestartUj=8 | Oznacza, że ponowne uruchomienie zostało zakończone pomyślnie.
      OperacjaCompleted=9 | Operacja Usługi Windows Update została ukończona pomyślnie.
      OperacjaBorted=10 | Oznacza to, że operacja Windows Update została przerwana.

1. W wersjach POA 1.4.0 i nowszych, po zakończeniu próby aktualizacji węzła, zdarzenie z właściwością "WUOperationStatus-[NodeName]" jest księgowane w NodeAgentService, aby powiadomić cię o rozpoczęciu następnej próby pobrania i zainstalowania aktualizacji systemu Windows. Jest to wyświetlane na poniższej ilustracji:

     [![Obraz stanu operacji usługi Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostics-logs"></a>Dzienniki diagnostyczne

Dzienniki aplikacji aranżacji poprawek są zbierane jako część dzienników środowiska uruchomieniowego sieci szkieletowej usług.

Dzienniki można przechwytywać za pomocą wybranego narzędzia diagnostycznego lub potoku. POA używa następujących stałych identyfikatorów dostawców do rejestrowania zdarzeń za pośrednictwem [źródła zdarzeń:](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Raporty o stanie zdrowia

POA publikuje również raporty kondycji dotyczące usługi agenta węzła lub usługi koordynatora w następujących scenariuszach:

* Agent węzła NTService jest w dół

   Jeśli agent węzła NTService jest w dół w węźle, raport kondycji na poziomie ostrzeżenia jest generowany względem usługi agenta węzła.

* Usługa Menedżera napraw nie jest włączona

   Jeśli usługa Menedżera napraw nie zostanie znaleziona w klastrze, dla usługi koordynatora jest generowany raport kondycji na poziomie ostrzeżenia.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Pyt.: Dlaczego widzę mój klaster w stanie błędu, gdy poa jest uruchomiony?**

Odp.: Podczas procesu instalacji poa wyłącza lub uruchamia ponownie węzły, co może tymczasowo spowodować w złej kondycji klastra.

W zależności od zasad aplikacji jeden węzeł może upaść podczas operacji wprowadzania poprawek *lub* cała domena aktualizacji może przejść w dół naraz.

Pod koniec instalacji aktualizacji systemu Windows węzły są ponownie uruchamiane po ponownym uruchomieniu.

W poniższym przykładzie klaster udał się do stanu błędu tymczasowo, ponieważ dwa węzły zostały w dół i MaxPercentageUnhealthyNodes zasady zostały naruszone. Błąd jest tymczasowy, dopóki nie można rozpocząć operacji poprawek.

![Obraz niezdrowego klastra](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Jeśli problem będzie się powtarzał, zapoznaj się z sekcją Rozwiązywanie problemów.

**Pyt.: Co mogę zrobić, jeśli POA jest w stanie ostrzegawczym?**

Odp.: Sprawdź, czy raport o kondycji opublikowany dla aplikacji wskazuje główną przyczynę. Zazwyczaj ostrzeżenie zawiera szczegóły problemu. Jeśli problem jest przejściowy, oczekuje się, że aplikacja zostanie odzyskana automatycznie.

**Pyt.: Co mogę zrobić, jeśli mój klaster jest w złej kondycji i muszę wykonać pilną aktualizację systemu operacyjnego?**

Odp.: POA nie instaluje aktualizacji, gdy klaster jest w złej kondycji. Spróbuj doprowadzić klaster do stanu dobrej kondycji, aby odblokować przepływ pracy POA.

**Pyt.: Czy należy ustawić TaskApprovalPolicy jako "NodeWise" lub "UpgradeDomainWise" dla mojego klastra?**

Odp.: Ustawienie "UpgradeDomainWise" przyspiesza ogólną naprawę klastra, łatując równolegle wszystkie węzły należące do domeny aktualizacji. Podczas procesu węzły należące do całej domeny aktualizacji są niedostępne (w stanie [ *Wyłączone).* ](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)

Z kolei ustawienie "NodeWise" łata tylko jeden węzeł naraz, co oznaczałoby, że ogólne łatanie klastra może trwać dłużej. Jednak tylko jeden węzeł co najwyżej będzie niedostępny (w stanie *Wyłączone)* podczas procesu wprowadzania poprawek.

Jeśli klaster może tolerować uruchamianie na n-1 liczby domen aktualizacji podczas cyklu poprawek (gdzie N jest całkowita liczba domen aktualizacji w klastrze), można ustawić zasady jako "UpgradeDomainWise". W przeciwnym razie ustaw go na "NodeWise".

**Pyt.: Ile czasu zajmuje łatanie węzła?**

Odp.: Łatanie węzła może potrwać od minut (na przykład [aktualizacje definicji usługi Windows Defender)](https://www.microsoft.com/en-us/wdsi/definitions)do godzin (na przykład [aktualizacje zbiorcze systemu Windows).](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update) Czas wymagany do załatania węzła zależy głównie od: 
 - Rozmiar aktualizacji.
 - Liczba aktualizacji, które muszą być stosowane w oknie poprawek.
 - Czas potrzebny do zainstalowania aktualizacji, ponownego uruchomienia węzła (jeśli jest to wymagane) i zakończenia kroków instalacji po ponownym uruchomieniu.
 - Wydajność maszyny wirtualnej lub maszyny oraz warunki sieciowe.

**Pyt.: Jak długo trwa poprawka całego klastra?**

Odp.: Czas wymagany do załatania całego klastra zależy od:

- Czas potrzebny do poprawki węzła.

- Polityka Służby Koordynatora. Domyślna zasada "NodeWise" powoduje łatanie tylko jednego węzła naraz, podejście, które jest wolniejsze niż przy użyciu "UpgradeDomainWise". 

   Na przykład: Jeśli trwa okładowanie węzła ~1 godziny, instalowanie poprawek klastra 20 węzłów (tego samego typu węzłów) z 5 domenami aktualizacji, z których każda zawiera 4 węzły, wymaga:
    - Dla "NodeWise": ~20 godzin.
    - Dla "UpgradeDomainWise": ~5 godzin.

- Obciążenie klastra. Każda operacja poprawek wymaga przeniesienia obciążenia klienta do innych dostępnych węzłów w klastrze. Węzeł, który jest łatany będzie w stanie [ *wyłączenia* ](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling) w tym czasie. Jeśli klaster działa w pobliżu obciążenia szczytowego, proces wyłączania potrwa dłużej. W związku z tym ogólny proces łatanie może wydawać się powolny w takich warunkach podkreślił.

- Błędy kondycji klastra podczas łatanie. Wszelkie [pogorszenie](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) [kondycji klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) spowoduje przerwanie procesu łatanie. Ten problem spowoduje dodanie do całkowitego czasu wymaganego do poprawki całego klastra.

**Pyt.: Dlaczego w wynikach usługi Windows Update są uzyskiwane za pośrednictwem interfejsu API REST, ale nie w obszarze historia usługi Windows Update na komputerze?**

Odp.: Niektóre aktualizacje produktów są wyświetlane tylko w ich własnej historii aktualizacji lub poprawek. Na przykład aktualizacje usługi Windows Defender mogą być wyświetlane w historii usługi Windows Update w systemie Windows Server 2016.

**Pyt.: Czy poa może służyć do poprawki mojego klastra deweloperów (klaster z jednym węzłem)?**

Odp.: Nie, poa nie można użyć do poprawki klastra z jednym węzłem. To ograniczenie jest zgodne z projektem, ponieważ [usługi systemu sieci szkieletowej usług](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) usług lub inne aplikacje klienta poniosą przestoje. W związku z tym łatanie zadań naprawy nigdy nie zostanie zatwierdzony przez Menedżera naprawy.

**Pyt.: Jak załatać węzły klastra w systemie Linux?**

Odp.: Aby dowiedzieć się więcej o organizowaniu aktualizacji w systemie Linux, zobacz [Skalowanie maszyny wirtualnej platformy Azure ustawia automatyczne uaktualnienia obrazów systemu operacyjnego](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade).

**P: Dlaczego cykl aktualizacji trwa tak długo?**

Odp.: Kwerenda dla wyniku JSON, wprowadź cykl aktualizacji dla wszystkich węzłów, a następnie można spróbować dowiedzieć się czas wykonany przez instalację aktualizacji w każdym węźle przy użyciu OperationStartTime i OperationTime (OperationCompletionTime). 

Jeśli istnieje duże okno czasu, w którym nie ma miejsca żadna aktualizacja, klaster może być w stanie błędu i w związku z tym Menedżer napraw nie może zatwierdzić żadnych zadań naprawy poa. Jeśli instalacja aktualizacji zajmuje dużo czasu w dowolnym węźle, ten węzeł może nie zostać zaktualizowany w czasie. Wiele aktualizacji może być oczekujących na instalację, co może spowodować opóźnienia. 

Może być również możliwe, że poprawki węzła jest zablokowany, ponieważ jest zablokowany w stanie *wyłączenia.* Zwykle dzieje się tak, ponieważ wyłączenie węzła może prowadzić do sytuacji kworum lub utraty danych.

**Pyt.: Dlaczego węzeł musi być wyłączony, gdy poa jest łatanie go?**

Odp.: POA wyłącza węzeł z zamiarem *ponownego uruchomienia,* który zatrzymuje lub ponownie przydziela wszystkie usługi sieci szkieletowej usług, które są uruchomione w węźle. POA robi to, aby upewnić się, że aplikacje nie kończą się przy użyciu kombinacji nowych i starych bibliotek DLL, więc nie zaleca się poprawianie węzła bez wyłączania go.

**Pyt.: Jaka jest maksymalna liczba węzłów, które można zaktualizować przy użyciu POA?**

Odp.: POA używa Menedżera naprawy sieci szkieletowej do tworzenia zadań naprawczych dla węzłów aktualizacji. Jednak nie więcej niż 250 zadań naprawczych może być obecny w tym samym czasie. Obecnie POA tworzy zadania naprawy dla każdego węzła w tym samym czasie, więc POA można zaktualizować nie więcej niż 250 węzłów w klastrze. 

## <a name="disclaimers"></a>Zastrzeżenia

- Poa akceptuje umowę licencyjną użytkownika końcowego dla witryny Windows Update w imieniu użytkownika. Opcjonalnie ustawienie można wyłączyć w konfiguracji aplikacji.

- Poa zbiera dane telemetryczne do śledzenia użycia i wydajności. Dane telemetryczne aplikacji jest zgodna z ustawieniem ustawienia danych telemetrycznych sieci szkieletowej usługi (które jest domyślnie włączone).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Ta sekcja zawiera możliwe rozwiązania rozwiązywania problemów z łataniem węzłów.

### <a name="a-node-is-not-coming-back-to-up-state"></a>Węzeł nie wraca do stanu wzrostowego

* Węzeł może zostać zablokowany w stanie *wyłączanie,* ponieważ:

  - Kontrola bezpieczeństwa jest w toku. Aby zaradzić tej sytuacji, upewnij się, że wystarczająca liczba węzłów są dostępne w dobrym stanie.

* Węzeł może zostać zablokowany w stanie *Wyłączone,* ponieważ:

  - Został on wyłączony ręcznie.
  - Została wyłączona z powodu trwającego zadania infrastruktury platformy Azure.
  - Został on tymczasowo wyłączony przez POA, aby załatać węzeł.

* Węzeł może zostać zablokowany w stanie w dół, ponieważ:

  - Został on umieszczony w stanie w dół ręcznie.
  - Jest w trakcie ponownego uruchamiania (które mogą być wyzwalane przez POA).
  - Ma wadliwą maszynę wirtualną lub komputer lub ma problemy z łącznością sieciową.

### <a name="updates-were-skipped-on-some-nodes"></a>Aktualizacje zostały pominięte w niektórych węzłach

POA próbuje zainstalować aktualizację systemu Windows zgodnie z zasadami ponownego rozłąki. Usługa próbuje odzyskać węzeł i pominąć aktualizację zgodnie z zasadami aplikacji.

W takim przypadku raport kondycji na poziomie ostrzeżenia jest generowany dla usługi agenta węzła. Wynik usługi Windows Update zawiera również możliwą przyczynę błędu.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-is-being-installed"></a>Kondycja klastra przechodzi do błędu podczas instalowania aktualizacji

Wadliwa aktualizacja systemu Windows może obniżyć kondycję aplikacji lub klastra w określonym węźle lub domenie aktualizacji. POA przerywa wszelkie kolejne operacje usługi Windows Update, dopóki klaster nie będzie w dobrej kondycji.

Administrator musi interweniować i ustalić, dlaczego aplikacja lub klaster stał się w złej kondycji z powodu usługi Windows Update.

## <a name="poa-release-notes"></a>Informacje o wydaniu POA

>[!NOTE]
> W przypadku poa w wersjach 1.4.0 i nowszych można znaleźć informacje o wersji i wydania na [patch orchestration aplikacji strony](https://github.com/microsoft/Service-Fabric-POA/releases/) na GitHub.

### <a name="version-110"></a>Wersja 1.1.0
- Publiczne udostępnienie

### <a name="version-111"></a>Wersja 1.1.1
- Naprawiono błąd w InstalatorEntryPoint of NodeAgentService, który uniemożliwiał instalację NodeAgentNTService.

### <a name="version-120"></a>Wersja 1.2.0

- Poprawki błędów wokół przepływu pracy ponownego uruchomienia systemu.
- Poprawka błędu podczas tworzenia zadań RM, dzięki której kontrola kondycji podczas przygotowywania zadań naprawczych nie odbywała się zgodnie z oczekiwaniami.
- Zmieniono tryb uruchamiania usługi systemu Windows POANodeSvc z automatycznego na opóźnione auto.

### <a name="version-121"></a>Wersja 1.2.1

- Poprawka problemu w przepływie pracy skalowania klastra w dół. Wprowadzono logikę wyrzucania elementów bezużytecznych dla zadań naprawy POA należących do nieistniejących węzłów.

### <a name="version-122"></a>Wersja 1.2.2

- Różne poprawki błędów.
- Pliki binarne są teraz podpisane.
- Dodano link sfpkg do aplikacji.

### <a name="version-130"></a>Wersja 1.3.0

- Ustawienie InstallWindowsOSOnlyUpdates to false teraz instaluje wszystkie dostępne aktualizacje.
- Zmieniono logikę wyłączania aktualizacji automatycznych. Spowoduje to naprawienie błędu, w którym aktualizacje automatyczne nie były wyłączone na serwerze 2016 i powyżej.
- Sparametryzowane ograniczenie umieszczania dla obu mikrousług POA dla zaawansowanych przypadków użycia.

### <a name="version-131"></a>Wersja 1.3.1
- Naprawianie regresji, w przypadku gdy POA 1.3.0 nie będzie działać w systemie Windows Server 2012 R2 lub starszym z powodu niepowodzenia w wyłączaniu aktualizacji automatycznych. 
- Naprawianie błędu, w którym konfiguracja InstallWindowsOSOnlyUpddates jest zawsze wybierana jako True.
- Zmiana domyślnej wartości InstallWindowsOSOnlyUpdates na False.

### <a name="version-132"></a>Wersja 1.3.2
- Rozwiązanie problemu, który wpływa na cykl życia poprawek w węźle, jeśli istnieją węzły o nazwie, która jest podzbiorem bieżącej nazwy węzła. W przypadku takich węzłów możliwe jest, że poprawki zostały pominięte lub trwa ponowne uruchomienie.
