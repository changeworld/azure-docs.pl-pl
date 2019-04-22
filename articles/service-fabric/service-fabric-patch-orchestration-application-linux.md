---
title: Azure Service Fabric patch orchestration aplikacji dla systemu linux | Dokumentacja firmy Microsoft
description: Aplikacja do Automatyzowanie stosowania poprawek systemu operacyjnego w klastrze systemu Linux usługi Service Fabric.
services: service-fabric
documentationcenter: .net
author: novino
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/22/2018
ms.author: nachandr
ms.openlocfilehash: 537450dbc386a94fa5c2e0d9334435dce041a32f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59266141"
---
# <a name="patch-the-linux-operating-system-in-your-service-fabric-cluster"></a>Stosowanie poprawek systemu operacyjnego Linux w klastrze usługi Service Fabric

> [!div class="op_single_selector"]
> * [Windows](service-fabric-patch-orchestration-application.md)
> * [Linux](service-fabric-patch-orchestration-application-linux.md)
>
>

Aplikacja orchestration poprawki jest aplikacji usługi Service Fabric, który automatyzuje systemu operacyjnego poprawek w klastrze usługi Service Fabric, bez przestojów.

Aplikacja orchestration poprawki zapewnia następujące funkcje:

- **Instalacja aktualizacji automatycznych systemu operacyjnego**. Aktualizacje systemu operacyjnego są automatycznie pobierane i instalowane. Węzły klastra są ponownie uruchamiane zgodnie z potrzebami bez przestoju klastra.

- **Typu cluster-aware, obsługą jej poprawek oraz kondycji integracji**. Podczas stosowania aktualizacji, aplikacja orchestration poprawki monitoruje kondycję węzłów klastra. Węzły klastra są uaktualnione jeden węzeł lub jedną domenę uaktualnienia w danym momencie. Jeśli kondycja klastra ulegnie awarii z powodu procesu stosowania poprawek, poprawek jest zatrzymana, aby zapobiec obciążające problem.

## <a name="internal-details-of-the-app"></a>Szczegóły wewnętrznej aplikacji

Aplikacji patch orchestration składa się z następujących Podskładniki:

- **Usługa koordynatora**: Ta usługa stanowa jest odpowiedzialny za:
    - Koordynowanie zadania aktualizacji systemu operacyjnego dla całego klastra.
    - Przechowywanie wyniku zakończone operacje aktualizacji systemu operacyjnego.
- **Usługa agenta węzła**: Tę usługę bezstanową działa we wszystkich węzłach klastra usługi Service Fabric. Usługa jest odpowiedzialna za:
    - Uruchamianie demona węzła agenta w systemie Linux.
    - Monitorowanie usługi demona.
- **Demon agenta węzła**: Ta usługa demonów systemu Linux jest uruchamiane na wyższym poziomie uprawnień (root). Z kolei Usługa agenta węzła i usługa koordynatora są uruchomione na niższym poziomie uprawnień. Usługa jest odpowiedzialny za wykonanie następujących zadań aktualizacji we wszystkich węzłach klastra:
    - Wyłączanie automatycznej aktualizacji systemu operacyjnego w węźle.
    - Pobieranie i instalowanie aktualizacji systemu operacyjnego, zgodnie z zasadami użytkownik udostępnił.
    - Ponowne uruchamianie maszyny po instalacji aktualizacji systemu operacyjnego, jeśli to konieczne.
    - Przekazywanie wyników aktualizacji systemu operacyjnego z usługą koordynatora.
    - Raportów raportowania kondycji, w przypadku, gdy operacja nie powiodła się po wykorzystaniu ponawiania prób.

> [!NOTE]
> Aplikacja aranżacji poprawki używa usługę Service Fabric naprawy Menedżera systemu, aby wyłączyć lub włączyć węzła i sprawdzać kondycję. Zadanie naprawy tworzonych przez aplikację patch orchestration śledzi postęp aktualizacji dla każdego węzła.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="ensure-that-your-azure-vms-are-running-ubuntu-1604"></a>Upewnij się, że maszynach wirtualnych platformy Azure działają Ubuntu 16.04
W momencie pisania tego dokumentu, Ubuntu 16.04 (`Xenial Xerus`) jest obsługiwany wyłącznie w wersji.

### <a name="ensure-that-the-service-fabric-linux-cluster-is-version-62x-and-above"></a>Upewnij się, że klaster usługi Service fabric systemu linux w wersji 6.2.x i nowsze wersje

Niektóre funkcje środowiska uruchomieniowego, które są dostępne tylko w wersja środowiska uruchomieniowego usługi Service fabric korzysta z Patch orchestration aplikacji w systemie linux 6.2.x i nowsze wersje.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Włącz usługę Menedżer naprawy (Jeśli nie jest już uruchomiona)

Aplikacja orchestration poprawki wymaga naprawy Menedżera systemu włączenia usługi w klastrze.

#### <a name="azure-clusters"></a>Klastry platformy Azure

Klastry systemu linux platformy Azure, w silver i warstwa trwałości gold ma usługa Menedżera naprawy, które są domyślnie włączone. Klastry platformy Azure w ramach warstwy trwałości bronze domyślnie włączona usługa Menedżera naprawy nie jest konieczne. Jeśli usługa jest już włączony, można to sprawdzić w sekcji usługi systemowe Service Fabric Explorer.

##### <a name="azure-portal"></a>Azure Portal
Menedżer naprawy w witrynie Azure portal można włączyć w momencie utworzenia klastra. Wybierz **obejmują Menedżera naprawy** opcji w obszarze **funkcje dodatku** w czasie konfiguracji klastra.
![Obraz Menedżera naprawy Włączanie z witryny Azure portal](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>Model wdrażania usługi Azure Resource Manager
Możesz też użyć [modelu wdrażania usługi Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) włączyć usługę Menedżer naprawy na nowych i istniejących klastrów usługi Service Fabric. Pobierz szablon dla klastra, który chcesz wdrożyć. Można użyć przykładowych szablonów lub utworzyć szablon niestandardowy model wdrażania usługi Azure Resource Manager. 

Aby umożliwić używanie usługi Menedżera naprawy [szablon modelu wdrożenia usługi Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm):

1. Najpierw sprawdź, czy `apiversion` ustawiono `2017-07-01-preview` dla `Microsoft.ServiceFabric/clusters` zasobów. Jeśli jest inny, a następnie należy zaktualizować `apiVersion` wartość `2017-07-01-preview` lub nowszej:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Teraz Włącz usługę Menedżer naprawy, dodając następujące `addonFeatures` sekcji po `fabricSettings` sekcji:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Po zaktualizowaniu szablonu klastra za pomocą tych zmian stosować je i umożliwić uaktualnienie Zakończ. Teraz widać usługa systemowa Menedżera naprawy działającym w klastrze. Jest on nazywany `fabric:/System/RepairManagerService` w sekcji usługi systemowe Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Autonomicznych klastrów w środowisku lokalnym

Klastry autonomiczne usługi Service Fabric systemu Linux nie są obsługiwane w momencie pisania tego dokumentu.

### <a name="disable-automatic-os-update-on-all-nodes"></a>Wyłącz automatyczną aktualizację systemu operacyjnego na wszystkich węzłach

Automatyczne aktualizacje systemu operacyjnego może prowadzić do utraty dostępności i lub zmienić zachowanie uruchomionych aplikacji. Poprawka aplikacji aranżacji, domyślnie próbuje Wyłącz automatyczne aktualizacji systemu operacyjnego w każdym węźle klastra, aby uniknąć takich scenariuszy.
Dla systemu Ubuntu [nienadzorowanego uaktualnienia](https://help.ubuntu.com/community/AutomaticSecurityUpdates) są wyłączone przez aplikację orkiestracji poprawek.

## <a name="download-the-app-package"></a>Pobieranie pakietu aplikacji

Aplikacja, która skrypty instalacyjne, które można pobrać z [łącze archiwum](https://go.microsoft.com/fwlink/?linkid=867984).

Aplikacja w formacie sfpkg można pobrać z [łącze sfpkg](https://aka.ms/POA/POA_v2.0.3.sfpkg). Jeśli źródłem jest przydatna dla [usługi Azure Resource Manager, na podstawie wdrożenia aplikacji](service-fabric-application-arm-resource.md).

## <a name="configure-the-app"></a>Konfigurowanie aplikacji

Zachowanie aplikacji orkiestracji poprawek można skonfigurować do własnych potrzeb. Zastąp wartości domyślne, przekazując w parametrze aplikacji podczas tworzenia aplikacji lub aktualizacji. Można podać parametry aplikacji, określając `ApplicationParameter` do `Start-ServiceFabricApplicationUpgrade` lub `New-ServiceFabricApplication` polecenia cmdlet.

|**Parametr**        |**Typ**                          | **Szczegóły**|
|:-|-|-|
|MaxResultsToCache    |Długie                              | Maksymalna liczba wyników aktualizacji, które mają być buforowane. <br>Wartość domyślna to 3000 zakładając, że: <br> -Liczba węzłów to 20. <br> -Liczba aktualizacje wykonywane w węźle na miesiąc wynosi pięć. <br> -Liczba wyników na operację może być 10. <br> — Powinny być przechowywane wyniki ostatnie trzy miesiące. |
|TaskApprovalPolicy   |Wyliczenia <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy wskazuje zasady, które ma być używany przez usługę koordynatora do instalowania aktualizacji w węzłach klastra usługi Service Fabric.<br>                         Dozwolone wartości to: <br>                                                           <b>NodeWise</b>. Aktualizacje są zainstalowane na jednym węźle naraz. <br>                                                           <b>UpgradeDomainWise</b>. Aktualizacje są zainstalowane jedną domenę uaktualnienia w danym momencie. (Maksymalnie, wszystkie węzły należące do domeny uaktualnienia można przejść do aktualizacji.)
| UpdateOperationTimeOutInMinutes | Int <br>(Domyślnie: 180)                   | Określa limit czasu dla wszelkich operacji aktualizacji (pobieranie lub instalację). Jeśli operacja nie jest ukończone przed upływem określonego limitu czasu, zostało przerwane.       |
| RescheduleCount      | Int <br> (Domyślnie: 5)                  | Maksymalna liczba przypadków, w usłudze zmieni ustalony systemu operacyjnego aktualizacji w przypadku, gdy operacja stale kończy się niepowodzeniem.          |
| RescheduleTimeInMinutes  | Int <br>(Domyślnie: 30) | Interwał, w którym usługa zmieni ustalony systemu operacyjnego aktualizacji w przypadku, gdy błąd będzie nadal występować. |
| UpdateFrequency           | Ciąg rozdzielony przecinkami (domyślne: "Co tydzień, Środa, 7:00:00")     | Częstotliwość instalowania aktualizacji systemu operacyjnego w klastrze. Format i możliwe wartości to: <br>-Miesięczny, DD: mm: ss, na przykład co miesiąc, 5, 12:22:32. <br> -Co tydzień, dzień, ss, na przykład co tydzień, Wtorek, 12:22:32.  <br> -Codziennie: mm: ss, na przykład codziennie, 12:22:32.  <br> -None wskazuje, nie można wykonać tej aktualizacji.  <br><br> Wszystkie godziny są w formacie UTC.|
| UpdateClassification | Ciąg rozdzielony przecinkami (domyślnie: "securityupdates") | Typ aktualizacji, które należy zainstalować na węzłach klastra. Dopuszczalne wartości to securityupdates, wszystkie. <br> -securityupdates - będą instalowane tylko aktualizacje zabezpieczeń <br> z polecenia apt - Al - zainstalować wszystkie dostępne aktualizacje.|
| ApprovedPatches | Ciąg rozdzielony przecinkami (domyślnie: "") | Jest to lista zatwierdzonych aktualizacji, które należy zainstalować na węzłach klastra. Rozdzielana przecinkami lista zawiera zatwierdzonych pakietów i opcjonalnie wybraną docelową wersję.<br> na przykład: "apt utils = 1.2.10ubuntu1, języku python3 jwt, apt https transportu < 1.2.194, libsystemd0 > = 229 4ubuntu16" <br> Powyższe zostaną zainstalowane <br> -apt utils 1.2.10ubuntu1 wersji, jeśli jest on dostępny w użyciu narzędzia apt w pamięci podręcznej. Jeśli w tej konkretnej wersji nie jest dostępna, jest pusta. <br> -Token jwt środowiska python3 jako uaktualnienia do najnowszej dostępnej wersji. Jeśli pakiet nie jest obecny, jest pusta. <br> — najwyższa wersja, która jest mniejsza niż 1.2.194 uaktualnień apt https transportu. Jeśli ta wersja nie jest obecny, jest pusta. <br> — najwyższa wersja, która jest większa niż lub równe 229 4ubuntu16 libsystemd0 uaktualnień. Jeśli takie wersji nie istnieje, jest pusta.|
| RejectedPatches | Ciąg rozdzielony przecinkami (domyślnie: "") | To jest lista aktualizacji, które nie należy instalować na węzłach klastra <br> na przykład: "bash," sudo"" <br> Poprzedni odfiltrowuje bash, "sudo" odbieranie wszelkich aktualizacji. |


> [!TIP]
> Jeśli chcesz, aby aktualizacji systemu operacyjnego, natychmiastowe, ustaw `UpdateFrequency` względem czasu wdrożenia aplikacji. Na przykład, załóżmy, że klaster testu z pięcioma węzłami, a następnie zaplanować wdrożenie aplikacji o około 5:00 czasu UTC. Jeśli przyjęto założenie, że uaktualnienie aplikacji lub wdrożenia trwa 30 minut na maksymalnym, ustaw UpdateFrequency jako "Codziennie, 17:30:00."

## <a name="deploy-the-app"></a>Wdrażanie aplikacji

1. Przygotuj klaster za pomocą wstępnie wymagane kroki.
2. Wdrażanie aplikacji aranżacji poprawek, jak dowolną inną aplikację usługi Service Fabric. Aplikację można wdrożyć przy użyciu programu PowerShell lub interfejsu wiersza polecenia usługi Azure Service Fabric. Postępuj zgodnie z instrukcjami w [Wdróż i usunąć aplikacje przy użyciu programu PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) lub [wdrażanie aplikacji przy użyciu interfejsu wiersza polecenia usługi Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/scripts/cli-deploy-application)
3. Aby skonfigurować aplikację w czasie wdrażania, należy przekazać `ApplicationParameter` do `New-ServiceFabricApplication` polecenia cmdlet i skryptów. Dla wygody programu powershell (Deploy.ps1) i skrypty powłoki bash (Deploy.sh) są dostarczane wraz z aplikacji. Aby użyć skryptu:

    - Łączenie z klastrem usługi Service Fabric.
    - Wykonywanie skryptu wdrażania. Opcjonalnie można podać parametr aplikacji skryptu. na przykład:.\Deploy.ps1 - ApplicationParameter @{UpdateFrequency = "Codziennie, 11:00:00"} OR./Deploy.sh "{\"UpdateFrequency\":\"codziennie, 11:00:00\"}" 

> [!NOTE]
> Zachowaj skrypt i folderu aplikacji PatchOrchestrationApplication w tym samym katalogu.

## <a name="upgrade-the-app"></a>Uaktualnianie aplikacji

Aby uaktualnić istniejącą aplikację orkiestracji poprawek, wykonaj kroki opisane w [uaktualnianie aplikacji usługi Service Fabric przy użyciu programu PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell) lub [uaktualnianie aplikacji usługi Service Fabric przy użyciu interfejsu wiersza polecenia usługi Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade)

## <a name="remove-the-app"></a>Usuń aplikację

Aby usunąć aplikację, wykonaj kroki opisane w [Wdróż i usunąć aplikacje przy użyciu programu PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) lub [Usuń aplikację przy użyciu interfejsu wiersza polecenia usługi Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-delete)

Dla wygody programu powershell (Undeploy.ps1) i skrypty powłoki bash (Undeploy.sh) są dostarczane wraz z aplikacji. Aby użyć skryptu:

  - Łączenie z klastrem usługi Service Fabric.
  - Wykonywanie skryptu Undeploy.ps1 lub Undeploy.sh

> [!NOTE]
> Zachowaj skrypt i folderu aplikacji PatchOrchestrationApplication w tym samym katalogu.

## <a name="view-the-update-results"></a>Wyświetlanie wyników aktualizacji

Aplikacja orchestration poprawki udostępnia interfejsy API REST, aby wyświetlić wyniki historyczne dla użytkownika. Poniżej przedstawiono przykładowy wynik: ```testadm@bronze000001:~$ curl -X GET http://10.0.0.5:20002/PatchOrchestrationApplication/v1/GetResults```
```json
[ 
  { 
    "NodeName": "_bronze_0", 
    "UpdateOperationResults": [ 
      { 
        "OperationResult": "succeeded", 
        "NodeName": "_bronze_0", 
        "OperationTime": "2017-11-21T12:39:29.0435917Z", 
        "UpdateDetails": [ 
          { 
            "UpdateId": "linux-cloud-tools-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-headers-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-image-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-tools-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "python3-apport:amd64=2.20.1-0ubuntu2.13", 
            "ResultCode": "succeeded" 
          }, 
        ], 
        "OperationType": "installation", 
        "UpdateClassification": "securityupdates", 
        "UpdateFrequency": "Daily, 7:00:00", 
        "RebootRequired": true, 
        "ApprovedList": "", 
        "RejectedList": "" 
      } 
    ] 
  } 
] 
```

Pola JSON zostały opisane w następujący sposób:

Pole | Wartości | Szczegóły
-- | -- | --
OperationResult | 0 — Powodzenie<br> 1 — zakończyło się pomyślnie z błędami<br> 2 — nie powiodło się<br> 3 — zostało przerwane<br> 4 — zostało przerwane z przekroczeniem limitu czasu | Wskazuje wynik ogólny operacji (zazwyczaj instalacji co najmniej jednej aktualizacji).
ResultCode | Takie same jak klasy OperationResult | To pole wskazuje wynik operacji instalacji dla indywidualnej aktualizacji.
OperationType | 1 — Instalacja<br> 0 - wyszukiwanie i pobieranie.| Instalacja jest tylko typ operacji, która będzie wyświetlana w wynikach domyślnie.
UpdateClassification | Wartość domyślna to "securityupdates" | Typ aktualizacji, który jest instalowany podczas operacji aktualizacji
UpdateFrequency | Wartość domyślna to "Co tydzień, Środa, 7:00:00" | Zaktualizuj częstotliwości skonfigurowana dla tej aktualizacji.
RebootRequired | wartość true — ponowny rozruch nie jest wymagana<br> FALSE — ponowny rozruch nie jest wymagane | Wskazuje, że ponowne uruchomienie komputera nie jest wymagana do ukończenia instalacji aktualizacji.
ApprovedList | Wartość domyślna to "" | Lista zatwierdzonych poprawki dla tej aktualizacji
RejectedList | Wartość domyślna to "" | Lista odrzuconych poprawek dla tej aktualizacji

Jeśli aktualizacja nie jest jeszcze zaplanowane, wynik JSON jest pusta.

Zaloguj się do klastra z wynikami aktualizacji zapytania. Dowiedz się, repliki adres podstawowy usługi koordynatora i trafień adresu URL w przeglądarce: http://&lt;IP REPLIKI&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetResults .

Punkt końcowy REST dla usługi koordynatora ma portu dynamicznego. Aby sprawdzić adres URL, zapoznaj się z narzędzia Service Fabric Explorer. Na przykład, wyniki są dostępne pod adresem `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetResults`.

![Obraz punktu końcowego REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

## <a name="diagnosticshealth-events"></a>Zdarzenia diagnostyczne/kondycji

### <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Patch orchestration aplikacji dzienniki są zbierane w ramach dzienniki środowiska uruchomieniowego usługi Service Fabric.

W przypadku, gdy zachodzi potrzeba przechwycenia dzienniki za pośrednictwem narzędzia diagnostyczne/potoku wybranych przez użytkownika. Patch orchestration application używa następujących stałych dostawcy identyfikatory służące do rejestrowania zdarzeń za pośrednictwem [eventsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netstandard-2.0)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Raportów o kondycji

Aplikacja orchestration poprawki są również publikowane raporty kondycji na podstawie usługa koordynatora lub usługę agenta węzła w następujących przypadkach:

#### <a name="an-update-operation-failed"></a>Operacja aktualizacji nie powiodło się

Jeśli operacja aktualizacji nie powiedzie się w węźle, raport o kondycji jest generowany dla usługi agenta węzła. Szczegółowe informacje o raport o kondycji zawiera nazwy węzła problematyczne.

Po pomyślnym zakończeniu poprawek w węźle problematyczne, raport zostanie automatycznie wyczyszczony.

#### <a name="the-node-agent-daemon-service-is-down"></a>Usługa demona agenta węzła nie działa

Jeśli usługa węzła agenta demon nie działa w węźle, generowania raportu kondycji poziom ostrzeżeń dla usługi agenta węzła.

#### <a name="the-repair-manager-service-is-not-enabled"></a>Usługa Menedżera naprawy nie jest włączona

Usługi Koordynator generowany jest raport o kondycji poziom ostrzeżenia, gdy usługa Menedżera naprawy nie znajduje się w klastrze.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

PYTANIE: **Dlaczego widzisz Mój klaster w stanie błędu, gdy uruchomiona jest aplikacja orchestration poprawki?**

A. Podczas procesu instalacji aplikacji patch orchestration powoduje wyłączenie lub ponowne uruchomienie węzłów. Ta operacja może spowodować tymczasowo kondycji klastra zostanie wyłączona.

Na podstawie zasad dla aplikacji, albo jeden węzeł można przejść w dół podczas operacji stosowania poprawek *lub* całej domeny uaktualnienia można jednocześnie wyłączane.

Przed zakończeniem instalacji, węzły są reenabled po ponownym uruchomieniu.

W poniższym przykładzie klaster przeszedł do stanu błędu tymczasowo z uwzględnieniem dwóch węzłów było wyłączone, ponieważ masz naruszenie zasad MaxPercentageUnhealthyNodes. Ten błąd jest tymczasowy, dopóki trwa operacja stosowania poprawek.

![Obraz przedstawiający klaster w złej kondycji](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Jeśli problem będzie się powtarzać, zapoznaj się z sekcją rozwiązywanie problemów.

PYTANIE: **Aplikacja orchestration poprawki jest w stanie ostrzeżenia**

A. Sprawdź, czy raport o kondycji opublikowane w związku z aplikacją główną przyczynę. Zazwyczaj ostrzeżenie zawiera szczegółowe informacje o problemie. Jeśli problem będzie się przejściowy, aplikacja powinna automatyczne odzyskiwanie z tego stanu.

PYTANIE: **Co mogę zrobić, jeśli mój klaster jest w złej kondycji i muszę wykonać aktualizację pilne systemu operacyjnego?**

A. Aplikacji patch orchestration nie instaluje aktualizacji, podczas gdy klaster jest w złej kondycji. Aby odblokować poprawki aplikacji przepływu pracy, Przełącz klaster do stanu dobrej kondycji.

PYTANIE: **Dlaczego poprawek w klastrach podąża tak długo do uruchomienia?**

A. Czas potrzebny przez aplikację patch orchestration przede wszystkim jest zależna od następujących czynników:

- Zasady usługi koordynatora. 
  - Domyślne zasady `NodeWise`, powoduje stosowanie poprawek tylko jeden węzeł w danym momencie. Szczególnie w przypadku większego klastra, firma Microsoft zaleca użycie `UpgradeDomainWise` zasady, aby osiągnąć szybciej poprawek w klastrze.
- Liczba dostępnych do pobrania i zainstalowania aktualizacji. 
- Średni czas potrzebny do pobrania i zainstalowania aktualizacji, które nie powinna przekraczać kilka godzin.
- Wydajność maszyny Wirtualnej i sieci przepustowość.

PYTANIE: **Jak ma patch orchestration aplikacja decyduje o aktualizacji, które są aktualizacje zabezpieczeń.**

A. Patch orchestration aplikacja używa logikę specyficzną dla dystrybucji, do określenia, które aktualizacje wśród dostępne aktualizacje są aktualizacje zabezpieczeń. Na przykład: W systemie ubuntu aplikacji wyszukuje aktualizacje z archiwami $RELEASE — zabezpieczenia, $RELEASE — aktualizacje ($RELEASE = xenial lub linux standardowego podstawowej wersji). 

 
PYTANIE: **Jak można zablokować do określonej wersji pakietu?**

A. Za pomocą ustawień ApprovedPatches zablokować pakietów do określonej wersji. 


PYTANIE: **Co stanie się aktualizacje automatyczne jest włączona w systemie Ubuntu?**

A. Zaraz po zainstalowaniu aplikacji orkiestracji poprawek w klastrze, zostałoby wyłączone nienadzorowanego uaktualnienia w węźle klastra. Wszystkie przepływy pracy okresową aktualizacją będzie uzależniona patch orchestration aplikacji.
Zapewnienie spójności środowiska w klastrze, firma Microsoft zaleca instalowanie aktualizacji za pośrednictwem patch orchestration tylko aplikacji. 
 
PYTANIE: **Uaktualnienie POST patch orchestration aplikacji wykonaj czyszczenie nieużywanych pakietów?**

A. Tak, jako część poinstalacyjną miało miejsce oczyszczania. 

PYTANIE: **Może służyć do poprawiania Mój klaster dev (klastra z jednym węzłem) Orkiestracji poprawek aplikacji?**

A. Nie, Patch orchestration aplikacji nie może służyć do klastra z jednym węzłem poprawki. To ograniczenie jest zgodne z projektem, jako [usługi usługi systemowe Service fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) lub wszystkie aplikacje klienta będzie może wystąpić Przestój, a więc wszystkie zadania naprawy dla stosowania poprawek nigdy nie będzie zatwierdzenia przez Menedżera naprawy.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="a-node-is-not-coming-back-to-up-state"></a>Węzeł nie jest powracające do stanu w górę

**Węzeł mogła zostać zablokowana w stanie wyłączenie, ponieważ**:

Sprawdzanie bezpieczeństwa jest w stanie oczekiwania. Aby rozwiązać ten problem, upewnij się, że wystarczającej liczby węzłów są dostępne w dobrej kondycji.

**Węzeł mogła zostać zablokowana w stanie wyłączenia, ponieważ**:

- Węzeł został wyłączony ręcznie.
- Węzeł został wyłączony z powodu zadania bieżące infrastruktury platformy Azure.
- Węzeł została tymczasowo wyłączona przez aplikację patch orchestration zastosowania poprawki względem węzła.

**Węzeł może zostać zatrzymane w stan naciśnięcia, ponieważ**:

- Węzeł został wprowadzony w stan naciśnięcia ręcznie.
- Węzeł jest w trakcie ponownego uruchomienia (które mogą być wyzwalane przez aplikację orkiestracji poprawek).
- Węzeł nie działa z powodu błędnej maszyny Wirtualnej lub problemy z połączeniem komputera lub sieci.

### <a name="updates-were-skipped-on-some-nodes"></a>Aktualizacje zostały pominięte niektóre węzły

Aplikacja orchestration poprawki próbuje zainstalować aktualizację zgodnie z zasadami planowaniem mogą. Usługa próbuje odzyskać węzła i pominąć tę aktualizację, zgodnie z zasadami aplikacji.

W takim przypadku generowania raportu kondycji poziom ostrzeżeń dla usługi agenta węzła. Wynik aktualizacji zawiera także możliwe przyczyny błędu.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>Kondycja klastra jest przesyłany do błędu podczas instalacji aktualizacji

Uszkodzony aktualizacji można obniżyć kondycję aplikacji lub klastra w określonym węźle lub domena uaktualnienia. Aplikacja orchestration poprawki zaprzestaje dowolne operacje kolejną aktualizację, aż klaster jest w dobrej kondycji ponownie.

Administrator musi interweniować i ustalić, dlaczego aplikacji lub klastra stał się nieprawidłowy z powodu wcześniej zainstalowanych aktualizacji.

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

Aplikacja orchestration poprawki gromadzi dane telemetryczne umożliwiający śledzenie użycia i wydajności. Telemetria usługi application następuje ustawienie ustawienie dane telemetryczne środowiska uruchomieniowego usługi Service Fabric, (która jest domyślnie włączone).

## <a name="release-notes"></a>Informacje o wersji

### <a name="version-010"></a>Wersja 0.1.0
- Wersja prywatnej wersji zapoznawczej

### <a name="version-200"></a>Wersja 2.0.0
- Po publicznym udostępnieniu

### <a name="version-201"></a>Wersja 2.0.1
- Ponownie kompilowana w aplikacji przy użyciu najnowszy zestaw SDK usługi Service Fabric

### <a name="version-202"></a>Wersja 2.0.2 
- Rozwiązano problem z ostrzeżeniem kondycji wprowadzenie pozostawione podczas ponownego uruchamiania.

### <a name="version-203-latest"></a>Wersji 2.0.3 (Najnowsza wersja)
- Rozwiążesz ten problem, gdy użycie procesora CPU agenta węzła Usługa demona osiągnięto maksymalnie 99% na maszynach wirtualnych maszyna wirtualna Standard_D1_v2.
- Rozwiążesz ten problem, do której dokonywane poprawianie cyle życia w węźle, w przypadku, gdy istnieją węzły z nazwą, która jest podzbiorem nazwę bieżącego węzła. Dla tych węzłów, jego możliwe, stosowanie poprawek brakuje lub Trwa oczekiwanie na ponowne.
- Usunięto usterkę z powodu której demona agenta węzła często występuje awaria podczas uszkodzony ustawienia są przekazywane do usługi.