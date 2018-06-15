---
title: Węzły klastra skalowania automatycznego HPC Pack | Dokumentacja firmy Microsoft
description: Automatycznie zwiększyć lub zmniejszyć liczbę węzłów obliczeniowych klastra HPC Pack na platformie Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: ''
editor: tysonn
ms.assetid: 38762cd1-f917-464c-ae5d-b02b1eb21e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 12/08/2016
ms.author: danlep
ms.openlocfilehash: 4a2350183bc0cb9360e9315cd8a351be20b66584
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31603848"
---
# <a name="automatically-grow-and-shrink-the-hpc-pack-cluster-resources-in-azure-according-to-the-cluster-workload"></a>Automatycznie zwiększyć lub zmniejszyć zasobów klastra HPC Pack na platformie Azure zgodnie z obciążenie klastra
Jeśli wdrażania platformy Azure "serii" węzłów w klastrze HPC Pack lub utworzyć klaster HPC Pack w maszynach wirtualnych platformy Azure, możesz sposób, aby automatycznie zwiększać i zmniejszać zasoby klastra, takie jak węzłów lub rdzeni w zależności od obciążenia w klastrze. Skalowanie zasobów klastra w ten sposób pozwala na bardziej efektywnie korzystać z zasobów platformy Azure i kontrolę kosztów ich.

W tym artykule opisano, że zasoby obliczeniowe dwie metody udostępniające HPC Pack skalowania automatycznego:

* Właściwość klastra HPC Pack **AutoGrowShrink**

* **AzureAutoGrowShrink.ps1** skryptu środowiska PowerShell klastra HPC

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Obecnie tylko automatycznie można zwiększyć lub zmniejszyć węzły obliczeniowe HPC Pack uruchomionych w systemie operacyjnym Windows Server.


## <a name="set-the-autogrowshrink-cluster-property"></a>Ustaw właściwość AutoGrowShrink klastra
### <a name="prerequisites"></a>Wymagania wstępne

* **HPC Pack 2012 R2 Update 2 lub nowszym klastra** -węzła głównego klastra może być wdrożona lokalnie lub w maszynie Wirtualnej platformy Azure. Zobacz [Konfigurowanie klastra hybrydowego pakietem HPC](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) rozpocząć pracę z węzłem głównym lokalnymi i węzły platformy Azure "serii". Zobacz [skrypt wdrożenia HPC Pack IaaS](hpcpack-cluster-powershell-script.md) można szybko wdrożyć klaster HPC Pack w maszynach wirtualnych platformy Azure.

* **W przypadku klastra z węzła głównego na platformie Azure (modelu wdrażania usługi Resource Manager)** — począwszy od HPC Pack 2016 certyfikatów uwierzytelniania w aplikacji usługi Azure Active Directory służy do automatycznie powiększania i zmniejszanie rozmiaru klastra maszyny wirtualne wdrażane za pomocą Usługa Azure Resource Manager. Konfigurowanie certyfikatu w następujący sposób:

  1. Po wdrożeniu klastra przez pulpit zdalny połączyć się z jednego węzła głównego.

  2. Przekaż certyfikat (format PFX z kluczem prywatnym) do każdego węzła głównego i zainstaluj Cert: \LocalMachine\My i Cert: \LocalMachine\Root.

  3. Uruchom program Azure PowerShell jako administrator i uruchom następujące polecenia w jednym węźle głównym:

    ```powershell
        cd $env:CCP_HOME\bin

        Connect-AzureRmAccount
    ```
        
    Jeśli konto użytkownika należy do więcej niż jednej dzierżawy usługi Azure Active Directory lub subskrypcji platformy Azure, można uruchomić następujące polecenie, aby wybrać poprawny dzierżawy i subskrypcję:
  
    ```powershell
        Connect-AzureRmAccount -TenantId <TenantId> -SubscriptionId <subscriptionId>
    ```     
       
    Uruchom następujące polecenie, aby wyświetlić aktualnie wybranej dzierżawy i subskrypcji:
    
    ```powershell
        Get-AzureRMContext
    ```

  4. Uruchom poniższy skrypt

    ```powershell
        .\ConfigARMAutoGrowShrinkCert.ps1 -DisplayName “YourHpcPackAppName” -HomePage "https://YourHpcPackAppHomePage" -IdentifierUri "https://YourHpcPackAppUri" -CertificateThumbprint "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" -TenantId xxxxxxxx-xxxxx-xxxxx-xxxxx-xxxxxxxxxxxx
    ```

    gdzie

    **Nazwa wyświetlana** — Nazwa wyświetlana Azure aktywnej aplikacji. Jeśli aplikacja nie istnieje, jest tworzony w usłudze Azure Active Directory.

    **Strona główna** — strona główna aplikacji. Można skonfigurować zastępczego adresu URL, tak jak w poprzednim przykładzie.

    **IdentifierUri** — identyfikator aplikacji. Można skonfigurować zastępczego adresu URL, tak jak w poprzednim przykładzie.

    **CertificateThumbprint** — odcisk palca certyfikatu został zainstalowany w węźle głównym w kroku 1.

    **TenantId** -Identyfikatorem usługi Azure Active Directory dzierżawy. Identyfikator dzierżawcy można pobrać z portalu usługi Azure Active Directory **właściwości** strony.

    Aby uzyskać więcej informacji o **ConfigARMAutoGrowShrinkCert.ps1**Uruchom `Get-Help .\ConfigARMAutoGrowShrinkCert.ps1 -Detailed`.


* **W przypadku klastra z węzła głównego na platformie Azure (klasycznego modelu wdrażania)** — Jeśli używasz skryptu wdrażania HPC Pack IaaS do tworzenia klastra w klasycznym modelu wdrożenia, Włącz **AutoGrowShrink** klastra właściwości przez ustawienie opcji AutoGrowShrink w pliku konfiguracji klastra. Aby uzyskać więcej informacji, zobacz towarzyszące dokumentacji [pobieranie skryptu](https://www.microsoft.com/download/details.aspx?id=44949).

    Można również włączyć **AutoGrowShrink** klastra właściwości po wdrożeniu klastra za pomocą poleceń środowiska PowerShell klastra HPC opisane w poniższej sekcji. Aby przygotować się do tego, najpierw należy wykonać następujące czynności:

  1. W węźle głównym i w subskrypcji platformy Azure, należy skonfigurować certyfikat zarządzania platformy Azure. W przypadku wdrożenia testu Użyj domyślnej Microsoft HPC Azure certyfikatu z podpisem własnym instalujący w węźle głównym HPC Pack i następnie przekaż certyfikat do subskrypcji platformy Azure. Dla opcji i procedury, zobacz [wskazówki w bibliotece TechNet](https://technet.microsoft.com/library/gg481759.aspx).

  2. Uruchom **regedit** w węźle głównym, przejdź do HKLM\SOFTWARE\Micorsoft\HPC\IaasInfo i Dodaj wartość ciągu. Ustaw nazwę wartości "Odcisk palca", a wartość danych odcisk palca certyfikatu w kroku 1.

### <a name="hpc-powershell-commands-to-set-the-autogrowshrink-property"></a>Polecenia środowiska PowerShell klastra HPC, aby ustawić właściwość AutoGrowShrink
Poniżej przedstawiono przykładowe polecenia środowiska PowerShell klastra HPC, aby ustawić **AutoGrowShrink** i dostosować jego zachowanie w przypadku dodatkowych parametrów. Zobacz [parametry AutoGrowShrink](#AutoGrowShrink-parameters) opisaną w tym artykule, aby uzyskać pełną listę ustawień.

Aby uruchomić te polecenia, uruchom program HPC PowerShell na węzła głównego klastra jako administrator.

**Aby włączyć właściwość AutoGrowShrink**

```powershell
Set-HpcClusterProperty –EnableGrowShrink 1
```

**Aby wyłączyć właściwość AutoGrowShrink**

```powershell
Set-HpcClusterProperty –EnableGrowShrink 0
```

**Aby zmienić interwał wzrostu w minutach**

```powershell
Set-HpcClusterProperty –GrowInterval <interval>
```

**Aby zmienić interwał zmniejszania w minutach**

```powershell
Set-HpcClusterProperty –ShrinkInterval <interval>
```

**Aby wyświetlić bieżącą konfigurację AutoGrowShrink**

```powershell
Get-HpcClusterProperty –AutoGrowShrink
```

**Aby wykluczyć węzeł grupy z AutoGrowShrink**

```powershell
Set-HpcClusterProperty –ExcludeNodeGroups <group1,group2,group3>
```

>[!NOTE] 
> Ten parametr jest obsługiwane począwszy od HPC Pack 2016
>

### <a name="autogrowshrink-parameters"></a>Parametry AutoGrowShrink
Poniżej przedstawiono AutoGrowShrink parametry, które można modyfikować za pomocą **HpcClusterProperty zestaw** polecenia.

* **EnableGrowShrink** — przełącznik, aby włączyć lub wyłączyć **AutoGrowShrink** właściwości.
* **ParamSweepTasksPerCore** — liczba zadań parametrycznych rośnie jednego rdzenia. Wartość domyślna to zwiększa jednego rdzenia poszczególnych zadań.

  > [!NOTE]
  > Zmiany HPC Pack QFE KB3134307 **ParamSweepTasksPerCore** do **TasksPerResourceUnit**. Jest oparty na typie zasobów zadania, a węzeł, gniazda lub core.
  >
  >
* **GrowThreshold** — próg umieszczonych w kolejce zadań do wyzwalania automatycznego przyrostu. Wartość domyślna to 1, co oznacza, że jeśli 1 lub więcej zadań, znajduje się w stanie umieszczonych w kolejce, automatycznie rozszerzaj węzłów.
* **GrowInterval** — interwał w minutach, aby wyzwolić automatyczne zwiększanie rozmiaru. Domyślny interwał wynosi 5 minut.
* **ShrinkInterval** — interwał w minutach, aby wyzwolić automatyczne zmniejszanie. Domyślny interwał wynosi 5 minut. |
* **ShrinkIdleTimes** — liczba ciągłego kontroli Zmniejszaj, aby wskazać węzły są w stanie bezczynności. Wartość domyślna to 3 razy. Na przykład jeśli **ShrinkInterval** wynosi 5 minut, HPC Pack sprawdza, czy węzeł jest w stanie bezczynności co 5 minut. Jeśli węzły są w stanie bezczynności, po 3 ciągłego sprawdza (15 minut), HPC Pack zmniejsza tego węzła.
* **ExtraNodesGrowRatio** -procent dodatkowe węzły do zwiększenia zadań komunikat interfejsu (Passing Interface). Wartość domyślna to 1, co oznacza, że HPC Pack rozwoju węzłów % 1 dla zadań MPI.
* **GrowByMin** — przełącznik, aby wskazać, czy zasady automatycznego przyrostu jest oparta na zasoby minimalne wymagane dla zadania. Wartość domyślna to false, co oznacza, że HPC Pack rozwoju węzłów dla zadania w oparciu o maksymalnej zasoby wymagane do zadania.
* **SoaJobGrowThreshold** — próg przychodzących żądań SOA do wyzwalania automatycznego wzrostu procesu. Wartość domyślna to 50000.

  > [!NOTE]
  > Ten parametr jest obsługiwana, począwszy od HPC Pack 2012 R2 Update 3.
  >
  >
* **SoaRequestsPerCore** — liczba SOA przychodzących żądań do zwiększenia jednego rdzenia. Wartość domyślna to 20000.

  > [!NOTE]
  > Ten parametr jest obsługiwana, począwszy od HPC Pack 2012 R2 Update 3.
  >
* **ExcludeNodeGroups** — węzły w grupach określony węzeł nie automatycznie zwiększyć lub zmniejszyć.
  
  > [!NOTE]
  > Ten parametr jest obsługiwana, począwszy od HPC Pack 2016.
  >

### <a name="mpi-example"></a>Przykład MPI
Domyślnie HPC Pack rozwoju 1% dodatkowe węzły zadań MPI (**ExtraNodesGrowRatio** jest ustawiona na 1). Dzieje się tak MPI może wymagać wielu węzłów, czy zadanie można uruchomić tylko, gdy wszystkie węzły są gotowe. Po uruchomieniu węzłów Azure czasami jeden węzeł może być konieczne dłuższego czasu, aby rozpocząć od innych, powodując inne węzły ze stanu bezczynności podczas oczekiwania na tym węźle w celu przygotowania. Powiększając dodatkowe węzły, HPC Pack skraca czas oczekiwania tego zasobu i potencjalnie zapisuje kosztów. Aby zwiększyć stopień dodatkowe węzły zadań MPI (na przykład do 10%), uruchom polecenie podobne do

    Set-HpcClusterProperty -ExtraNodesGrowRatio 10

### <a name="soa-example"></a>Przykład SOA
Domyślnie **SoaJobGrowThreshold** ustawiono 50000 i **SoaRequestsPerCore** ustawiono 20000. Jeśli prześlesz jedno zadanie SOA z żądaniami 70000 istnieje jedno zadanie w kolejce i żądania przychodzące są 70000. W takim przypadku HPC Pack rozwoju 1 rdzeń umieszczonych w kolejce zadań i żądań przychodzących, rozwoju (70000 50000) / podstawowe 20000 = 1, dlatego w łącznie rozwoju 2 rdzeni dla tego zadania SOA.

## <a name="run-the-azureautogrowshrinkps1-script"></a>Uruchom skrypt AzureAutoGrowShrink.ps1
### <a name="prerequisites"></a>Wymagania wstępne

* **HPC Pack 2012 R2 Update 1 lub nowszym klastra** - **AzureAutoGrowShrink.ps1** skryptów jest zainstalowany w folderze % CCP_HOME % bin. Węzła głównego klastra może być wdrożona lokalnie lub w maszynie Wirtualnej platformy Azure. Zobacz [Konfigurowanie klastra hybrydowego pakietem HPC](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) rozpocząć pracę z węzłem głównym lokalnymi i węzły platformy Azure "serii". Zobacz [skrypt wdrożenia HPC Pack IaaS](hpcpack-cluster-powershell-script.md) szybkie wdrożenie klastra HPC Pack w maszynach wirtualnych platformy Azure lub użyj [szablonie Szybki Start Azure](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/).
* **Program Azure PowerShell 1.4.0** -skrypt zależy od obecnie tę konkretną wersję programu Azure PowerShell.
* **Dla klastra przy użyciu platformy Azure serii węzłów** -uruchomić skrypt na komputerze klienckim, na których zainstalowano pakiet HPC lub w węźle głównym. Jeśli uruchomiony na komputerze klienckim, upewnij się, że ustawienia zmiennej $env: CCP_SCHEDULER, aby wskazywał węzła głównego. Węzły platformy Azure "serii" musi zostać dodany do klastra, ale mogą być w stanie wdrożyć nie.
* **Dla klastra wdrożone w maszynach wirtualnych platformy Azure (modelu wdrażania usługi Resource Manager)** — dla klastra Azure maszyn wirtualnych wdrożonych w modelu wdrażania usługi Resource Manager, skrypt obsługuje dwie metody uwierzytelniania systemu Azure: Zaloguj się do konta platformy Azure, aby uruchomić skrypt zawsze (uruchamiając `Connect-AzureRmAccount`, lub skonfiguruj nazwę główną usługi do uwierzytelniania przy użyciu certyfikatu. HPC Pack dostarcza skrypt **ConfigARMAutoGrowShrinkCert.ps** można utworzyć nazwy głównej usługi o certyfikat. Skrypt tworzy aplikację usługi Azure Active Directory (Azure AD) i nazwy głównej usługi i przypisanie roli współautora do nazwy głównej usługi. Aby uruchomić skrypt, uruchom program Azure PowerShell jako administrator i uruchom następujące polecenia:

    ```powershell
    cd $env:CCP_HOME\bin

    Connect-AzureRmAccount

    .\ConfigARMAutoGrowShrinkCert.ps1 -DisplayName “YourHpcPackAppName” -HomePage "https://YourHpcPackAppHomePage" -IdentifierUri "https://YourHpcPackAppUri" -PfxFile "d:\yourcertificate.pfx"
    ```

    Aby uzyskać więcej informacji o **ConfigARMAutoGrowShrinkCert.ps1**Uruchom `Get-Help .\ConfigARMAutoGrowShrinkCert.ps1 -Detailed`,

* **Dla klastra wdrożone w maszynach wirtualnych platformy Azure (klasycznego modelu wdrażania)** — Uruchom skrypt w węźle głównym maszyny Wirtualnej, ponieważ zależy on od **Start HpcIaaSNode.ps1** i **Stop HpcIaaSNode.ps1** skryptów Brak zainstalowanych. Ponadto te skrypty wymagają certyfikatu zarządzania platformy Azure lub plik ustawień publikowania (zobacz [Zarządzaj węzłów obliczeniowych w HPC Pack klastra w systemie Azure](hpcpack-cluster-node-manage.md)). Sprawdź, czy wszystkie węźle obliczeń maszyny wirtualne, konieczne są już dodane do klastra. Mogą być w stanie zatrzymania.



### <a name="syntax"></a>Składnia
```powershell
AzureAutoGrowShrink.ps1 [-NodeTemplates <String[]>] [-JobTemplates <String[]>] [-NodeType <String>]
    -NumOfActiveQueuedTasksPerNodeToGrow <Single> [-NumOfActiveQueuedTasksToGrowThreshold <Int32>]
    [-NumOfInitialNodesToGrow <Int32>] [-GrowCheckIntervalMins <Int32>] [-ShrinkCheckIntervalMins <Int32>]
    [-ShrinkCheckIdleTimes <Int32>] [-ExtraNodesGrowRatio <Int32>] [-ArgFile <String>] [-LogFilePrefix <String>]
    [<CommonParameters>]

AzureAutoGrowShrink.ps1 [-NodeTemplates <String[]>] [-JobTemplates <String[]>] [-NodeType <String>]
    -NumOfQueuedJobsPerNodeToGrow <Single> [-NumOfQueuedJobsToGrowThreshold <Int32>] [-NumOfInitialNodesToGrow
    <Int32>] [-GrowCheckIntervalMins <Int32>] [-ShrinkCheckIntervalMins <Int32>] [-ShrinkCheckIdleTimes <Int32>]
    [-ExtraNodesGrowRatio <Int32>] [-ArgFile <String>] [-LogFilePrefix <String>] [<CommonParameters>]

AzureAutoGrowShrink.ps1 -UseLastConfigurations [-ArgFile <String>] [-LogFilePrefix <String>] [<CommonParameters>]
```
### <a name="parameters"></a>Parametry
* **NodeTemplates** -nazwy szablonów węzła do zdefiniowania zakresu dla węzłów zwiększyć lub zmniejszyć. Jeśli nie zostanie określony (wartość domyślna to @()), wszystkie węzły w **AzureNodes** węzła grupy są w zakres podczas **NodeType** ma wartość AzureNodes i we wszystkich węzłach w **ComputeNodes**węzeł grupy są w przypadku zakres **NodeType** ma wartość ComputeNodes.
* **JobTemplates** -nazwy szablonów zadań do zdefiniowania zakresu dla węzłów rośnie.
* **Typ NodeType** — typ węzeł, aby zwiększyć lub zmniejszyć. Obsługiwane są następujące wartości:

  * **AzureNodes** — w przypadku Azure PaaS (serii) węzły w lokalnej lub w klastrze IaaS platformy Azure.
  * **ComputeNodes** — dla węzła obliczeniowego maszyn wirtualnych tylko w klastrze IaaS platformy Azure.

* **NumOfQueuedJobsPerNodeToGrow** -liczbę zadań umieszczonych w kolejce wymagane do zwiększenia jeden węzeł.
* **NumOfQueuedJobsToGrowThreshold** — próg liczbę zadań umieszczonych w kolejce do uruchomienia procesu wzrostu.
* **NumOfActiveQueuedTasksPerNodeToGrow** — liczba aktywnych w kolejce zadania wymagane do zwiększenia jeden węzeł. Jeśli **NumOfQueuedJobsPerNodeToGrow** zostanie podana wartość większą niż 0, ten parametr jest ignorowany.
* **NumOfActiveQueuedTasksToGrowThreshold** — próg liczba aktywnych zadań w kolejce do uruchomienia procesu wzrostu.
* **NumOfInitialNodesToGrow** -początkowej minimalną liczbę węzłów do powiększania, jeśli wszystkie węzły w zakresie są **wdrożone nie** lub **zatrzymane (Deallocated)**.
* **GrowCheckIntervalMins** — interwał w minutach pomiędzy kontrole zwiększa się.
* **ShrinkCheckIntervalMins** — interwał w minutach, między każdym sprawdzeniem, aby zmniejszyć.
* **ShrinkCheckIdleTimes** -kontroli zmniejszyć liczbę ciągłego (rozdzielone **ShrinkCheckIntervalMins**) wskazująca węzły są w stanie bezczynności.
* **UseLastConfigurations** -powyższych konfiguracjach zapisane w pliku argumentu.
* **ArgFile**-nazwa pliku argumentu używany do zapisywania i aktualizowania konfiguracji, aby uruchomić skrypt.
* **LogFilePrefix** — prefiks nazwy pliku dziennika. Można określić ścieżkę. Domyślnie dziennik jest zapisywany do bieżącego katalogu roboczego.

### <a name="example-1"></a>Przykład 1
Poniższy przykład umożliwia skonfigurowanie węzłów Azure serii wdrożone z szablonem AzureNode domyślne, aby zwiększyć lub zmniejszyć automatycznie. Jeśli wszystkie węzły są początkowo w **wdrożone nie** stanu, są uruchamiane co najmniej 3 węzłach. Jeśli liczba zadań w kolejce przekroczy 8, skrypt rozpoczyna węzłów aż do ich liczba przekracza stosunek zadania w kolejce **NumOfQueuedJobsPerNodeToGrow**. Jeśli węzeł zostanie znaleziony ze stanu bezczynności w 3 razy bezczynny, jest zatrzymana.

```powershell
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### <a name="example-2"></a>Przykład 2
Poniższy przykład umożliwia skonfigurowanie maszyn wirtualnych wdrożonych przy użyciu szablonu ComputeNode domyślne, aby zwiększyć lub zmniejszyć automatycznie węzeł obliczeń platformy Azure.
Zadania konfigurowane przez domyślny szablon zadania zdefiniować zakres obciążenia w klastrze. Jeśli wszystkie węzły są początkowo zatrzymana, są uruchamiane co najmniej 5 węzłów. Jeśli liczba aktywnych zadań w kolejce przekroczy 15, uruchamia skrypt węzłów aż do ich liczba przekracza stosunek liczby aktywnych zadań w kolejce do **NumOfActiveQueuedTasksPerNodeToGrow**. Jeśli węzeł zostanie znaleziony ze stanu bezczynności 10 kolejnych czas bezczynności, zostanie zatrzymana.

```powershell
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```
