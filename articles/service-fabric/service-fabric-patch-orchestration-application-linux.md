---
title: Azure Service Fabric poprawki aranżacji aplikacji dla systemu linux | Dokumentacja firmy Microsoft
description: Aplikacja do automatyzacji w klastrze usługi sieć szkieletowa usług Linux stosowanie poprawek systemu operacyjnego.
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
ms.date: 1/22/2018
ms.author: nachandr
ms.openlocfilehash: f5d9b39a91567dd04b4e8ca0cd580c58024bb2f2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="patch-the-linux-operating-system-in-your-service-fabric-cluster"></a>Poprawka systemu operacyjnego Linux w klastrze usługi sieć szkieletowa

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
    - Koordynowanie zadania aktualizacji systemu operacyjnego dla całego klastra.
    - Przechowywanie wyniku ukończone operacje aktualizacji systemu operacyjnego.
- **Usługa agenta węzła**: tej usługi bezstanowej działa we wszystkich węzłach klastra sieci szkieletowej usług. Usługa jest odpowiedzialna za:
    - Uruchamianie agenta węzła demona w systemie Linux.
    - Usługa demona monitorowania.
- **Demon agenta węzła**: Linux ten demon usługi uruchamiane na wyższym poziomie uprawnień (root). Z kolei usługi agenta węzła i koordynatora jednocześnie uprawnień niższego poziomu. Usługa jest odpowiedzialna za wykonywanie następujących zadań aktualizacji we wszystkich węzłach klastra:
    - Wyłączanie automatycznej aktualizacji systemu operacyjnego na węźle.
    - Trwa pobieranie i instalowanie aktualizacji systemu operacyjnego, zgodnie z zasadami użytkownik udostępnił.
    - Ponowne uruchomienie komputera po instalacji aktualizacji systemu operacyjnego, w razie potrzeby.
    - Przekazywanie wyników aktualizacje systemu operacyjnego z usługą koordynatora.
    - Raporty dotyczące raportowania kondycji, w przypadku, gdy operacja nie powiodła się po wykorzystaniu ponawiania prób.

> [!NOTE]
> Poprawki aplikacji orchestration korzysta z sieci szkieletowej usług naprawy Menedżera systemu usługi, aby wyłączyć lub włączyć węzeł i sprawdzania kondycji. Zadanie naprawy tworzonych przez aplikację aranżacji poprawki śledzi postęp aktualizacji dla każdego węzła.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="ensure-that-your-azure-vms-are-running-ubuntu-1604"></a>Upewnij się, że maszynach wirtualnych platformy Azure są uruchomione Ubuntu 16.04
W momencie pisania tego dokumentu, Ubuntu 16.04 (`Xenial Xerus`) jest jedyną obsługiwaną wersją.

### <a name="ensure-that-the-service-fabric-linux-cluster-is-version-61x-and-above"></a>Upewnij się, że w klastrze usługi sieci szkieletowej systemu linux jest w wersji 6.1.x i powyżej.

Poprawka aranżacji aplikacji w systemie linux używa pewnych funkcji środowiska uruchomieniowego, które są dostępne tylko w wersji środowiska uruchomieniowego sieci szkieletowej usług 6.1.x i powyżej.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Włącz usługę Menedżer napraw (Jeśli nie jest już uruchomiona)

Aplikacja orchestration poprawki wymaga usługi repair Menedżera system zostać włączony w klastrze.

#### <a name="azure-clusters"></a>Klastry platformy Azure

Klastry z systemem linux Azure w srebrna i warstwa złota trwałości mają usługę Menedżer naprawy domyślnie włączone. Klastry platformy Azure w warstwie brązową trwałości, domyślnie nie być włączona usługa Menedżera naprawy. Jeśli usługa jest już włączony, można to sprawdzić w sekcji system usługi Service Fabric Explorer.

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

Klastry z autonomicznej usługi sieci szkieletowej z systemem Linux nie są obsługiwane w momencie pisania tego dokumentu.

### <a name="disable-automatic-os-update-on-all-nodes"></a>Wyłącz automatyczną aktualizację systemu operacyjnego na wszystkich węzłach

Automatyczne aktualizacje systemu operacyjnego może spowodować utratę dostępności i lub zmiany w zachowaniu uruchamianie aplikacji. Poprawka aplikacji aranżacji, domyślnie próbuje wyłączanie automatycznych aktualizacji systemu operacyjnego w każdym węźle klastra, aby zapobiec takich scenariuszy.
Dla Ubuntu [nienadzorowane uaktualnienia](https://help.ubuntu.com/community/AutomaticSecurityUpdates) są wyłączone przez aplikację aranżacji poprawki.

## <a name="download-the-app-package"></a>Pobierz pakiet aplikacji

Pobierz aplikację z [pobrać link](https://go.microsoft.com/fwlink/?linkid=867984).

## <a name="configure-the-app"></a>Konfigurowanie aplikacji

Zachowanie aplikacji aranżacji poprawek można skonfigurować zgodnie z potrzebami. Zastąpić wartości domyślne, przekazując w parametrze aplikacji podczas tworzenia aplikacji lub aktualizacji. Można podać parametry aplikacji, określając `ApplicationParameter` do `Start-ServiceFabricApplicationUpgrade` lub `New-ServiceFabricApplication` polecenia cmdlet.

|**Parametr**        |**Typ**                          | **Szczegóły**|
|:-|-|-|
|MaxResultsToCache    |Długie                              | Maksymalna liczba wyników aktualizacji, które mają być buforowane. <br>Wartość domyślna to 3000 zakładając, że: <br> -Liczba węzłów to 20. <br> -Liczba aktualizacji pojawia się w węźle miesięcznie wynosi pięć. <br> -Liczba wyników dla operacji może być 10. <br> — Powinny być przechowywane wyniki dla ostatnich trzech miesięcy. |
|TaskApprovalPolicy   |wyliczenia <br> {NodeWise, UpgradeDomainWise}                          |TaskApprovalPolicy wskazuje zasad, który ma być używany przez usługę koordynatora do instalowania aktualizacji w węzłach klastra sieci szkieletowej usług.<br>                         Dozwolone wartości to: <br>                                                           <b>NodeWise</b>. Aktualizacje są zainstalowane jednym węźle naraz. <br>                                                           <b>UpgradeDomainWise</b>. Aktualizacje są zainstalowane jedną domenę uaktualnienia naraz. (Maksymalnie, wszystkie węzły należące do domeny uaktualnienia można przejść do aktualizacji.)
| UpdateOperationTimeOutInMinutes | Int <br>(Domyślnie: 180)                   | Określa limit czasu do żadnej operacji aktualizacji (pobierania lub instalacji). Jeśli działanie nie zostało ukończone w określonym czasie, zostało przerwane.       |
| RescheduleCount      | Int <br> (Domyślne: 5).                  | Maksymalna liczba usługi zmienia harmonogram używany system operacyjny aktualizacji w przypadku, gdy operacja trwale kończy się niepowodzeniem.          |
| RescheduleTimeInMinutes  | Int <br>(Domyślnie: 30). | Interwał, w którym usługa zmienia harmonogram używany system operacyjny aktualizacji, jeśli awaria nie zniknie. |
| UpdateFrequency           | Ciąg rozdzielony przecinkami (domyślne: "Co tydzień, środę, 7:00:00")     | Częstotliwość instalowania aktualizacji systemu operacyjnego w klastrze. Format i możliwe wartości to: <br>— Co miesiąc, DD gg, na przykład, co miesiąc, 5, 12:22:32. <br> — Gg co tydzień i dzień, na przykład, co tydzień, Wtorek, 12:22:32.  <br> -Codziennie, ss, na przykład codziennie, 12:22:32.  <br> -Wskazuje brak, nie można wykonać tej aktualizacji.  <br><br> Wszystkie godziny są w formacie UTC.|
| UpdateClassification | Ciąg rozdzielony przecinkami (domyślne: "securityupdates") | Typ aktualizacji, które należy zainstalować na węzłach klastra. Dopuszczalne wartości to securityupdates, wszystkie. <br> -securityupdates — będą instalowane tylko aktualizacje zabezpieczeń <br> -all - zainstalować wszystkie dostępne aktualizacje z stanie.|
| ApprovedPatches | Ciąg rozdzielony przecinkami (domyślne: "") | Jest to lista zatwierdzonych aktualizacji, które należy zainstalować na węzłach klastra. Rozdzielana przecinkami lista zawiera zatwierdzonych pakietów i opcjonalnie żądaną docelową wersję.<br> na przykład: "stanie witryny = 1.2.10ubuntu1, python3 jwt, stanie https transportu < 1.2.194 libsystemd0 > = 229 4ubuntu16" <br> Powyższe będą instalowane <br> -stanie witryny 1.2.10ubuntu1 wersji, jeśli jest dostępna w stanie pamięci podręcznej. Jeśli tej konkretnej wersji nie jest dostępna, jest pusta. <br> -python3 jwt uaktualnienia do najnowszej dostępnej wersji. Jeśli pakiet nie jest obecny, jest pusta. <br> -uaktualnienia stanie https transportu do najwyższej wersji, która jest mniejsza niż 1.2.194. Jeśli ta wersja nie jest obecny, jest pusta. <br> -libsystemd0 uaktualnień do najwyższej wersji, która jest większa niż równa 229 4ubuntu16. Jeśli takie wersja nie istnieje, jest pusta.|
| RejectedPatches | Ciąg rozdzielony przecinkami (domyślne: "") | Jest to lista aktualizacji, które nie powinny być instalowane w węzłach klastra <br> na przykład: "bash, sudo" <br> Poprzedni odfiltrowuje bash, sudo odbieranie wszelkich aktualizacji. |


> [!TIP]
> Jeśli chcesz aktualizacji systemu operacyjnego, aby natychmiast ustawić `UpdateFrequency` względem czasu wdrożenia aplikacji. Na przykład klaster z pięcioma węzłami testu i Planowanie wdrażania aplikacji przy około 17:00:00 czasu UTC. Jeśli założono, że uaktualnienie aplikacji lub wdrożenia ma 30 minut maksymalnie, ustaw UpdateFrequency jako "Codziennie, 17:30:00."

## <a name="deploy-the-app"></a>Wdrażanie aplikacji

1. Przygotuj klastra za pomocą wstępnie wymagane kroki.
2. Wdrażanie aplikacji aranżacji poprawki, takich jak każda inna aplikacja usługi Service Fabric. Aplikację można wdrożyć przy użyciu programu PowerShell lub interfejsu wiersza polecenia Azure Service Fabric. Postępuj zgodnie z instrukcjami [Wdróż i usunąć aplikacje przy użyciu programu PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) lub [wdrażanie aplikacji przy użyciu interfejsu wiersza polecenia Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/scripts/cli-deploy-application)
3. Aby skonfigurować aplikację w czasie wdrażania, należy przekazać `ApplicationParamater` do `New-ServiceFabricApplication` polecenia cmdlet i skryptów. Dla wygody środowiska powershell (Deploy.ps1) i skrypty powłoki systemowej (Deploy.sh) są udostępniane wraz z aplikacji. Aby użyć skryptu:

    - Połącz z klastrem usługi sieć szkieletowa usług.
    - Uruchom skrypt wdrażania. Opcjonalnie przekaż go do skryptu jako parametr aplikacji. na przykład:.\Deploy.ps1 - ApplicationParameter @{UpdateFrequency = "Codziennie, 11:00:00"}./Deploy.sh lub "{\"UpdateFrequency\":\"codziennie, 11:00:00\"}" 

> [!NOTE]
> Zachowaj skrypt i folder aplikacji PatchOrchestrationApplication w tym samym katalogu.

## <a name="upgrade-the-app"></a>Uaktualnianie aplikacji

Aby uaktualnić istniejącą aplikację aranżacji poprawki, postępuj zgodnie z instrukcjami [uaktualniania aplikacji sieci szkieletowej usług za pomocą programu PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell) lub [przy użyciu interfejsu wiersza polecenia Azure Service Fabric uaktualniania aplikacji sieci szkieletowej usług](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade)

## <a name="remove-the-app"></a>Usuwanie aplikacji

Aby usunąć aplikację, postępuj zgodnie z instrukcjami [Wdróż i usunąć aplikacje przy użyciu programu PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) lub [Usuń aplikację przy użyciu interfejsu wiersza polecenia Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-delete)

Dla wygody środowiska powershell (Undeploy.ps1) i skrypty powłoki systemowej (Undeploy.sh) są udostępniane wraz z aplikacji. Aby użyć skryptu:

  - Połącz z klastrem usługi sieć szkieletowa usług.
  - Wykonanie skryptu Undeploy.ps1 lub Undeploy.sh

> [!NOTE]
> Zachowaj skrypt i folder aplikacji PatchOrchestrationApplication w tym samym katalogu.

## <a name="view-the-update-results"></a>Wyświetl wyniki aktualizacji

Poprawka aplikacji aranżacji uwidacznia interfejsów API REST, aby wyświetlić wyniki historyczne dla użytkownika. Poniżej znajduje się wynik próbki: ```testadm@bronze000001:~$ curl -X GET http://10.0.0.5:20002/PatchOrchestrationApplication/v1/GetResults```
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

Pola JSON są opisane w następujący sposób:

Pole | Wartości | Szczegóły
-- | -- | --
OperationResult | 0 - powiodło się.<br> 1 - zakończyło się pomyślnie z błędami<br> 2 - nie powiodło się<br> 3 - zostało przerwane<br> 4 - zostało przerwane z limitem czasu | Wskazuje wynik operacji ogólnej (zazwyczaj dotyczących instalacji co najmniej jednej aktualizacji).
ResultCode | Identyczny klasy OperationResult | To pole wskazuje wynik operacji instalacji dla indywidualnej aktualizacji.
Typ operacji | 1 — Instalacja<br> 0 — wyszukiwanie i pobieranie.| Instalacja jest tylko typ operacji, która będzie wyświetlana w wynikach domyślnie.
UpdateClassification | Domyślna to "securityupdates" | Typ aktualizacji, który jest instalowany podczas operacji aktualizacji
UpdateFrequency | Domyślna to "Co tydzień, środę, 7:00:00" | Zaktualizuj częstotliwości skonfigurowana dla tej aktualizacji.
RebootRequired | wartość true — był wymagany ponowny rozruch<br> FALSE — nie był wymagany ponowny rozruch | Wskazuje, że ponowne uruchomienie komputera nie jest wymagana do ukończenia instalacji aktualizacji.
ApprovedList | Domyślna to "" | Lista zatwierdzonych poprawki dla tej aktualizacji
RejectedList | Domyślna to "" | Lista odrzuconych poprawki dla tej aktualizacji

Jeśli aktualizacja nie jest zaplanowane jeszcze, wyniku JSON nie jest pusty.

Zaloguj się do klastra z wynikami zapytania aktualizacji. Następnie sprawdzić repliki adres podstawowy usługi koordynatora i kliknij adres URL z przeglądarki: http://&lt;IP REPLIKI&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetResults .

Punkt końcowy REST usługi Koordynator ma portów dynamicznych. Aby sprawdzić dokładny adres URL, zajrzyj do Eksploratora usługi sieć szkieletowa. Na przykład, wyniki są dostępne pod adresem `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetResults`.

![Obraz końcowy REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

## <a name="diagnosticshealth-events"></a>Diagnostyka kondycji zdarzenia

### <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Poprawka aranżacji aplikacji dzienniki są gromadzone w ramach dzienniki środowisko uruchomieniowe usługi sieć szkieletowa usług.

W przypadku, gdy chcesz przechwytywać dzienniki za pośrednictwem narzędzia diagnostyczne/potoku wybranych przez użytkownika. Aplikacja orchestration poprawki używa następujących stałych dostawcy identyfikatorów do rejestrowania zdarzeń za pomocą [źródła zdarzeń](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netstandard-2.0)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Raportów o kondycji

Poprawka aplikacji aranżacji publikuje również raporty kondycji względem usługi Koordynator lub agenta węzła w następujących przypadkach:

#### <a name="an-update-operation-failed"></a>Operacja aktualizacji nie powiodła się

Jeśli operacja aktualizacji nie powiedzie się w węźle, z usługą agenta węzła generowany jest raport o kondycji. Szczegóły raport o kondycji zawiera nazwy węzła powodować problemy.

Po pomyślnym problematyczne węzła zakończeniu stosowanie poprawek, raportu są automatycznie usuwane.

#### <a name="the-node-agent-daemon-service-is-down"></a>Usługa demona agenta węzeł nie działa

Jeśli usługa demona agenta węzeł nie działa w węźle, z usługą agenta węzła generowany jest raport o kondycji poziom ostrzeżeń.

#### <a name="the-repair-manager-service-is-not-enabled"></a>Usługa Menedżera naprawy nie jest włączona.

Dla usługi Koordynator generowany jest raport o kondycji poziom ostrzeżenia, gdy naprawy Menedżera usługi nie znajduje się w klastrze.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

Q. **Dlaczego wyświetlać Mój klastra w stanie błędu, gdy aplikacja orchestration poprawki działa?**

A. W procesie instalacji poprawki aplikacji aranżacji wyłącza lub ponownego uruchomienia węzłów. Ta operacja może spowodować tymczasowo kondycji klastra, przechodząc w dół.

Na podstawie zasad dla aplikacji, albo jeden węzeł może przejść w dół podczas operacji stosowania poprawek *lub* całej domeny uaktualnienia można przestaną działać jednocześnie.

Na koniec instalacji węzły są reenabled po ponownym uruchomieniu.

W poniższym przykładzie klastra przejścia do stanu błędu tymczasowo ponieważ dwa węzły zostały w dół, a otrzymano naruszenie zasad MaxPercentageUnhealthyNodes. Błąd jest tymczasowy, dopóki trwa operacja stosowania poprawek.

![Obraz zła klastra](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Jeśli problem będzie się powtarzać, zapoznaj się z rozdziałem Rozwiązywanie problemów.

Q. **Aplikacja orchestration poprawki jest w stanie ostrzeżenia**

A. Sprawdź, czy raport o kondycji opublikowane w związku z aplikacją jest główną przyczynę. Zazwyczaj ostrzeżenie zawiera szczegółowe informacje o problemie. W przypadku przejściowy problem aplikacji powinien automatycznego odzyskiwania z tego stanu.

Q. **Co można zrobić, jeśli mój klastra jest nieprawidłowy i należy wykonać aktualizację pilnych systemu operacyjnego?**

A. Aplikacja orchestration poprawki nie można zainstalować aktualizacji klastra jest zła. Aby odblokować przepływu pracy aplikacji aranżacji poprawki, Przełącz klastra dobrej kondycji.

Q. **Dlaczego poprawki w klastrach podąża tak długo do uruchomienia?**

A. Czas potrzebny aplikacji aranżacji poprawki przede wszystkim jest zależna od następujących czynników:

- Zasady usługi koordynatora. 
  - Domyślne zasady `NodeWise`, powoduje stosowanie poprawek tylko jeden węzeł naraz. Zwłaszcza, jeśli jest klastrem większy, firma Microsoft zaleca użycie `UpgradeDomainWise` zasady w celu uzyskania szybszego stosowanie poprawek w klastrze.
- Liczba dostępnych do pobrania i zainstalowania aktualizacji. 
- Średni czas potrzebny do pobrania i zainstalowania aktualizacji, który nie może przekraczać po kilku godzinach.
- Wydajność maszyny Wirtualnej i sieci przepustowości.

Q. **Jak aplikacja orchestration poprawki ma decyduje o aktualizacji, które są aktualizacje zabezpieczeń.**

A. Poprawki aranżacji aplikacja używa logiki distro dotyczące określania, które aktualizacje wśród aktualizacje dostępne są aktualizacje zabezpieczeń. Na przykład: W aplikacji wyszukuje aktualizacje z archiwami $RELEASE ubuntu — zabezpieczeń, $RELEASE-aktualizacji ($RELEASE = xenial lub linux standardowej podstawowej wersji). 

 
Q. **Jak można zablokować do określonej wersji pakietu?**

A. Użyj ustawień ApprovedPatches zablokować pakietów do określonej wersji. 


Q. **Co się dzieje na aktualizacje automatyczne jest włączona w Ubuntu?**

A. Zaraz po zainstalowaniu poprawki aranżacji aplikacji w klastrze, zostanie wyłączone nienadzorowane uaktualnienia w węźle klastra. Wszystkie przepływy pracy okresowej aktualizacji będzie wymuszany przez aplikację aranżacji poprawki.
Aby spójności środowiska w klastrze, firma Microsoft zaleca instalowanie aktualizacji za pomocą poprawki aranżacji tylko aplikacji. 
 
Q. **Post uaktualnienia poprawka aranżacji aplikacji oczyszczania nieużywane pakietów?**

A. Oczyszczanie odbywa się tak, w ramach czynności po instalacji. 

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

Aplikacja orchestration poprawki próbuje zainstalować aktualizację, zgodnie z zasadami planowaniem mogą. Usługa próbuje odzyskać węzeł i Pomiń aktualizację zgodnie z zasadami aplikacji.

W takim przypadku generowany jest raport o kondycji poziom ostrzeżeń z usługą agenta węzła. Wynik dla aktualizacji zawiera także możliwe przyczyny niepowodzenia.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>Kondycja klastra przechodzi do błędu podczas instalacji aktualizacji

Błędny aktualizacji można obniżyć kondycji aplikacji lub klastra w określonym węźle lub domena uaktualnienia. Poprawka aplikacji aranżacji zaprzestaje żadnych operacji kolejnych aktualizacji do momentu klastra jest w dobrej kondycji ponownie.

Administrator musi interweniować, aby ustalić, dlaczego aktywowano niezdrowe, ponieważ wcześniej zainstalowanej aktualizacji aplikacji lub w klastrze.

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

Poprawka aplikacji aranżacji zbiera dane telemetryczne umożliwiający śledzenie użycia i wydajności. Dane telemetryczne aplikacji następuje ustawienie ustawienia telemetrii środowiska uruchomieniowego platformy Service Fabric, (która jest domyślnie włączona).

## <a name="release-notes"></a>Informacje o wersji

### <a name="version-010"></a>Wersja 0.1.0
- Wersja zapoznawcza prywatnych

### <a name="version-200-latest"></a>Wersji 2.0.0 (Najnowsza wersja)
- Publiczny zlecenia
