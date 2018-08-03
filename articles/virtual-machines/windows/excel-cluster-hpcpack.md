---
title: Klastra pakietu HPC Pack dla programów Excel i SOA | Dokumentacja firmy Microsoft
description: Wprowadzenie do uruchamiania na dużą skalę obciążeń programu Excel i SOA w klastrze pakietu HPC Pack na platformie Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,hpc-pack
ms.assetid: cb6a9abe-caf3-44da-b911-849a50f6cfb3
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/01/2017
ms.author: danlep
ms.openlocfilehash: 4a6327fcfe6f6e6f3b8b5c6ecbd14b832b4134c5
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421216"
---
# <a name="get-started-running-excel-and-soa-workloads-on-an-hpc-pack-cluster-in-azure"></a>Wprowadzenie do uruchamiania obciążeń programu Excel i SOA w klastrze pakietu HPC Pack na platformie Azure
W tym artykule przedstawiono sposób wdrażania klastra Microsoft HPC Pack 2012 R2 na maszynach wirtualnych platformy Azure przy użyciu szablonu szybkiego startu platformy Azure lub opcjonalnie skrypt wdrażania środowiska Azure PowerShell. Klaster używa obrazów maszyny Wirtualnej portalu Azure Marketplace, zaprojektowane do uruchamiania programu Microsoft Excel lub obciążeń dotycząca architektury zorientowanej na usługi (SOA) za pomocą pakietu HPC Pack. Klastra służy do uruchamiania HPC dla programu Excel i SOA usługi z poziomu komputera klienckiego w środowisku lokalnym. Usługi HPC dla programu Excel obejmują odciążenia skoroszytu programu Excel i funkcji zdefiniowanych przez użytkownika programu Excel lub UDF.

> [!IMPORTANT] 
> Ten artykuł jest oparty na funkcje, szablony i skrypty dla pakietu HPC Pack 2012 R2. W tym scenariuszu nie jest obecnie obsługiwane w zestawu HPC Pack 2016.
>

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Na wysokim poziomie na poniższym diagramie przedstawiono klastra pakietu HPC Pack, że utworzono.

![Klaster HPC z węzłami w uruchamianiu obciążeń liczonych w programie Excel][scenario]

## <a name="prerequisites"></a>Wymagania wstępne
* **Komputer kliencki** — należy komputerem klienckim z systemem Windows do przesyłania zadań do programu Excel i SOA próbki do klastra. Należy również komputer Windows, aby uruchomić skrypt wdrożenia klastra Azure PowerShell (po wybraniu tej metody wdrażania).
* **Subskrypcja platformy Azure** — Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.
* **Limit przydziału rdzeni** — może być konieczne zwiększenie limitu przydziału rdzeni, szczególnie w przypadku, gdy wdrożysz kilka węzłów klastra z wyspecjalizowanymi rozmiarów maszyn wirtualnych. Jeśli używasz szablonu szybkiego startu platformy Azure jest limit przydziału rdzeni w usłudze Resource Manager, na region platformy Azure. W takiej sytuacji może być konieczne zwiększenie limitu przydziału w określonym regionie. Zobacz [limity subskrypcji platformy Azure, przydziały i ograniczenia](../../azure-subscription-service-limits.md). Aby zwiększyć limit przydziału, [Otwórz żądanie obsługi klienta online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) bez dodatkowych opłat.
* **Licencja programu Microsoft Office** — w przypadku wdrożenia obliczeń węzłów przy użyciu obrazu maszyny Wirtualnej Marketplace HPC Pack 2012 R2 z programem Microsoft Excel, jest zainstalowana wersja 30-dniowej wersji ewaluacyjnej programu Microsoft Excel Professional Plus 2013. Po okresie ewaluacyjnym musisz podać prawidłową licencję programu Microsoft Office na aktywowanie programu Excel, aby kontynuować uruchamianie obciążeń. Zobacz [aktywacji w programie Excel](#excel-activation) w dalszej części tego artykułu. 

## <a name="step-1-set-up-an-hpc-pack-cluster-in-azure"></a>Krok 1. Konfigurowanie klastra pakietu HPC Pack na platformie Azure
Przedstawiono dwie opcje do skonfigurowania klastra pakietu HPC Pack 2012 R2: najpierw przy użyciu szablonu szybkiego startu platformy Azure i usługi Azure portal. i drugiego, za pomocą skryptu wdrożenia programu Azure PowerShell.

### <a name="option-1-use-a-quickstart-template"></a>Sposób 1. Korzystanie z szablonu szybkiego startu
Szablon szybkiego startu platformy Azure umożliwia szybkie wdrożenie klastra pakietu HPC Pack w witrynie Azure portal. Po otwarciu szablonu w portalu możesz uzyskać prostego interfejsu użytkownika, gdzie wprowadź ustawienia dla klastra. Poniżej przedstawiono kroki. 

> [!TIP]
> Jeśli chcesz, użyj [szablonu portalu Azure Marketplace](https://portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn) tworząca podobne klastra dla obciążeń programu Excel. Kroki różnią się nieco od następującego.
> 
> 

1. Odwiedź stronę [Utwórz klaster HPC szablon strony w witrynie GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). Jeśli chcesz, przejrzyj informacje dotyczące szablonu i kod źródłowy.
1. Kliknij przycisk **Wdróż na platformie Azure** można uruchomić wdrożenia przy użyciu szablonu w witrynie Azure portal.
   
   ![Wdrażanie szablonu na platformie Azure][github]
1. W portalu wykonaj następujące kroki, aby wprowadzić parametry szablonu klastra HPC.
   
   a. Na **parametry** strony, wprowadź lub zmodyfikuj wartości parametrów szablonu. (Kliknij ikonę obok każdego ustawienia, aby uzyskać informacje pomocy). Na poniższym ekranie pokazywane będą przykładowe wartości. Ten przykład obejmuje tworzenie klastra o nazwie *hpc01* w *hpc.local* domeny składający się z węzła głównego i 2 węzłów obliczeniowych. Węzły obliczeniowe są tworzone na podstawie obrazu maszyny Wirtualnej pakietu HPC Pack, który zawiera program Microsoft Excel.
   
   ![Wprowadzanie parametrów][parameters-new-portal]
   
   > [!NOTE]
   > Maszyna wirtualna jest tworzona automatycznie z węzłem głównym [najnowszego obrazu portalu Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) HPC Pack 2012 R2 w systemie Windows Server 2012 R2. Obecnie obraz, który opiera się na HPC Pack 2012 R2 Update 3.
   > 
   > Węzeł maszyn wirtualnych tworzonych przy użyciu najnowszego obrazu z rodziny węzła obliczeniowego wybranego obliczeń. Wybierz **ComputeNodeWithExcel** dotycząca najnowsze pakietu HPC Pack obliczenia obrazu węzła, który zawiera wersję ewaluacyjną programu Microsoft Excel Professional Plus 2013. Wdrażanie klastra dla sesji ogólnych po SOA lub Odciążanie UDF programu Excel, wybierz **ComputeNode** opcji (bez programem Excel zainstalowanym).
   > 
   > 
   
   b. Wybierz subskrypcję.
   
   c. Utwórz grupę zasobów klastra, takie jak *hpc01RG*.
   
   d. Wybierz lokalizację dla grupy zasobów, takich jak środkowe stany USA.
   
   e. Na **postanowienia prawne** Przejrzyj warunki. Jeśli wyrazisz zgodę, kliknij przycisk **zakupu**. Następnie kliknij przycisk po zakończeniu ustawiania wartości dla szablonu **Utwórz**.
1. Po zakończeniu wdrożenia (zwykle trwa około 30 minut), eksportowania pliku certyfikatu klastra z głównym węzłem klastra. Na późniejszym etapie możesz zaimportować ten certyfikat publiczny, na komputerze klienckim w celu zapewnienia uwierzytelniania po stronie serwera dla bezpiecznego powiązania protokołu HTTP.
   
   a. W witrynie Azure portal przejdź do pulpitu nawigacyjnego, wybierz węzeł główny, a następnie kliknij przycisk **Connect** w górnej części strony Aby nawiązać połączenie przy użyciu pulpitu zdalnego.
   
    <!-- ![Connect to the head node][connect] -->
   
   b. Aby wyeksportować certyfikat (znajdujący się w obszarze Cert: \LocalMachine\My) węzła głównego bez klucza prywatnego, należy użyć standardowych procedur w Menedżerze certyfikatów. W tym przykładzie należy wyeksportować *CN = hpc01.eastus.cloudapp.azure.com*.
   
   ![Eksportowanie certyfikatu][cert]

### <a name="option-2-use-the-hpc-pack-iaas-deployment-script"></a>Sposób 2. Użyj skryptu wdrażania IaaS pakietu HPC
Skryptem wdrażania IaaS pakietu HPC Pack udostępnia inny sposób uniwersalne do wdrożenia klastra pakietu HPC Pack. Tworzy klaster w klasycznym modelu wdrażania, dlatego szablon używa modelu wdrażania usługi Azure Resource Manager. Ponadto skrypt jest zgodna z subskrypcją w usłudze Azure Global lub chińska wersja platformy Azure.

**Dodatkowe wymagania wstępne**

* **Program Azure PowerShell** - [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview) (wersja 0.8.10 lub nowszej) na komputerze klienckim.
* **Skryptem wdrażania IaaS pakietu HPC Pack** — pobieranie i rozpakowywanie najnowszej wersji skryptu [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Sprawdź wersję skryptu, uruchamiając `New-HPCIaaSCluster.ps1 –Version`. Ten artykuł jest oparty na wersji 4.5.0 lub nowszej skryptu.

**Utwórz plik konfiguracji**

 Skryptem wdrażania IaaS pakietu HPC Pack używa plik konfiguracyjny XML jako danych wejściowych, który opisuje infrastruktura klastra HPC. Aby wdrożyć klaster składający się z węzła głównego i 18 węzły obliczeniowe utworzone na podstawie obrazu węzła obliczeniowego, który zawiera program Microsoft Excel, Zastąp wartości w danym środowisku, w następujący przykładowy plik konfiguracji. Aby uzyskać więcej informacji na temat pliku konfiguracji, zobacz plik Manual.rtf w folderze skryptu i [Utwórz klaster HPC ze skryptem wdrażania IaaS pakietu HPC Pack](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
    <PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName>HPCPack2012R2_ComputeNodeWithExcel</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**Uwagi dotyczące pliku konfiguracji**

* **VMName** węzła głównego **musi** być taka sama jak **ServiceName**, lub zadań SOA uruchomienie nie powiedzie się.
* Upewnij się, że **EnableWebPortal** tak, aby certyfikat węzła głównego jest wygenerowany i wyeksportowane.
* Plik Określa skrypt PowerShell po konfiguracji PostConfig.ps1, która działa w węźle głównym. Widoczny poniżej przykładowy skrypt konfiguruje parametry połączenia usługi Azure storage, usuwa rolę węzła obliczeń z węzłem głównym i łączy wszystkie węzły w trybie online, gdy są one wdrażane. 

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**Uruchom skrypt**

1. Otwórz konsolę programu PowerShell na komputerze klienckim jako administrator.
1. Zmień katalog na folder skryptów (E:\IaaSClusterScript w tym przykładzie).
   
   ```
   cd E:\IaaSClusterScript
   ```
1. Aby wdrożyć w klastrze pakietu HPC Pack, uruchom następujące polecenie. W tym przykładzie przyjęto założenie, że plik konfiguracji znajduje się w E:\HPCDemoConfig.xml.
   
   ```
   .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
   ```

Skrypt wdrażania pakietu HPC Pack jest uruchamiany przez pewien czas. Jedyną operacją, której działanie skryptu jest do eksportowania i Pobierz certyfikat klastra i zapisz go w folderze dokumenty bieżącego użytkownika na komputerze klienckim. Skrypt generuje komunikat podobny do następującego. W poniższym kroku należy zaimportować certyfikat w magazynie odpowiedni certyfikat.    

    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## <a name="step-2-offload-excel-workbooks-and-run-udfs-from-an-on-premises-client"></a>Krok 2. Odciążanie lokalnych urządzeń skoroszytów programu Excel i uruchom funkcje zdefiniowane przez użytkownika z klienta w środowisku lokalnym
### <a name="excel-activation"></a>Aktywacja programu Excel
W przypadku używania obrazu maszyny Wirtualnej ComputeNodeWithExcel dla obciążeń produkcyjnych, należy podać prawidłowy klucz licencji Microsoft Office, aby aktywować programu Excel w węzłach obliczeniowych. W przeciwnym razie wersję ewaluacyjną programu Excel wygasa po upływie 30 dni i uruchomienie skoroszytów programu Excel zakończy się niepowodzeniem z COMException (0x800AC472). 

Program Excel może rearm o kolejne 30 dni okresu próbnego: Zaloguj się do węzła głównego i clusrun `%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe` w programie Excel wszystkie węzły za pomocą Menedżera klastra HPC obliczeniowe. Można rearm maksymalnie dwa razy. Po tym należy podać prawidłowy klucz licencji pakietu Office.

Pakiet Office Professional Plus 2013 instalowany w obrazie maszyny Wirtualnej to wersja woluminu za pomocą ogólnego woluminu licencji klucza (GVLK). Możesz to zrobić za pomocą usługi zarządzania kluczami (KMS) / aktywację (AD BA) lub klucza aktywacji wielokrotnej (MAK). 

    * Aby korzystać z usługi KMS/AD-BA, używanie istniejącego serwera usługi KMS lub utworzyć nową za pomocą pakietu Microsoft Office 2013 woluminu licencji. (Jeśli chcesz, skonfigurować serwer w węźle głównym). Następnie należy aktywować klucz hosta usługi KMS za pośrednictwem Internetu lub telefonu. Następnie clusrun `ospp.vbs` serwer usługi KMS i port i aktywacja pakietu Office na wszystkich węzłach obliczeniowych programu Excel. 

    * Aby przy użyciu klucza MAK, pierwszy clusrun `ospp.vbs` należy wprowadź klucz, a następnie uaktywnić wszystkie węzły za pośrednictwem Internetu lub telefonu obliczeniowe programu Excel. 

> [!NOTE]
> Nie można użyć handlu detalicznego klucze produktów pakietu Office Professional Plus 2013 z tym obrazem maszyny Wirtualnej. Jeśli klucze i nośnika instalacyjnego dla pakietu Office lub programu Excel wersji innej niż ta wersja pakietu Office Professional Plus 2013 woluminu, możesz ich użyć. Najpierw Odinstaluj tę wersję woluminu i zainstaluj wersję, do której masz. Ponownie zainstalować węzeł obliczeniowy Excel mogą być przechwytywane jako dostosowany obraz maszyny Wirtualnej do użycia we wdrożeniu w odpowiedniej skali.
> 
> 

### <a name="offload-excel-workbooks"></a>Odciążanie skoroszytów programu Excel
Wykonaj następujące kroki w celu odciążenia skoroszytu programu Excel, aby była uruchamiana na klastrze pakietu HPC Pack na platformie Azure. Aby to zrobić, musisz mieć Excel 2010 lub 2013 już zainstalowany na komputerze klienckim.

1. Użyj jednej z opcji w kroku 1, aby wdrażanie klastra pakietu HPC Pack za pomocą programu Excel obliczanie obrazu węzła. Uzyskaj klastra plik certyfikatu (.cer) i nazwa użytkownika klastra i hasło.
1. Na komputerze klienckim należy zaimportować certyfikat klastra w obszarze Cert: \CurrentUser\Root.
1. Upewnij się, że zainstalowany jest program Excel. Utwórz plik Excel.exe.config z następującą zawartość w tym samym folderze co Excel.exe na komputerze klienckim. Ten krok zapewnia, że dodatek HPC Pack 2012 R2 Excel COM załadowaniu.
   
    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
1. Skonfiguruj klienta, aby przesyłać zadania do klastra pakietu HPC Pack. Jedną z opcji jest pobranie pełnego [instalacji pakietu HPC Pack 2012 R2 Update 3](http://www.microsoft.com/download/details.aspx?id=49922) i instalowanie klienta pakietu HPC Pack. Alternatywnie, Pobierz i zainstaluj [narzędzi klienta HPC Pack 2012 R2 Update 3](https://www.microsoft.com/download/details.aspx?id=49923) i odpowiednie Visual C++ 2010 redistributable dla komputera ([x64](http://www.microsoft.com/download/details.aspx?id=14632), [x86](https://www.microsoft.com/download/details.aspx?id=5555) ).
1. W tym przykładzie używamy przykładowy skoroszyt programu Excel o nazwie ConvertiblePricing_Complete.xlsb. Możesz ją pobrać [tutaj](https://www.microsoft.com/en-us/download/details.aspx?id=2939).
1. Skoroszyt programu Excel należy skopiować do folderu roboczego, takich jak D:\Excel\Run.
1. Otwórz skoroszyt programu Excel. Na **programowanie** wstążki, kliknij przycisk **dodatki COM** i upewnij się, że dodatek HPC Pack programu Excel w modelu COM został załadowany pomyślnie.
   
   ![Dodatek dla pakietu HPC Pack w programie Excel][addin]
1. Edytuj makro VBA HPCControlMacros w programie Excel, zmieniając komentarzem wierszy, jak pokazano w poniższym skrypcie. Zastąp odpowiednie wartości dla danego środowiska.
   
   ![Makr pakietu HPC Pack w programie Excel][macro]
   
   ```
   'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
   Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"
   
   'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
   Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"
   
   'HPCExcelClient.Initialize ActiveWorkbook
   HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles
   
   'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
   HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"
   
   'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
   HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
   ```
1. Skopiuj skoroszyt programu Excel do katalogu przekazywania, takich jak D:\Excel\Upload. Ten katalog jest określony w stałej HPC_DependsFiles w makrze VBA.
1. Aby uruchomić skoroszytu w klastrze na platformie Azure, kliknij **klastra** przycisku w arkuszu.

### <a name="run-excel-udfs"></a>Uruchom plikami UDF programu Excel
Aby uruchomić plikami UDF programu Excel, postępuj zgodnie z powyższych kroków 1 – 3, aby skonfigurować komputer kliencki. Dla funkcji UDF programu Excel nie trzeba utworzyć aplikację programu Excel, instalowane w węzłach obliczeniowych. Dlatego podczas tworzenia klastra węzłów obliczeniowych, możesz wybrać normalnego obliczanie obrazu węzła zamiast obrazu węzła obliczeniowego za pomocą programu Excel.

> [!NOTE]
> Jest ograniczona do 34 znaków w taki sposób, w programie Excel 2010 i 2013 — okno dialogowe łącznika klastra. To okno dialogowe umożliwia Określ żądany klaster działa, funkcje zdefiniowane przez użytkownika. Jeśli klaster Pełna nazwa jest dłuższa (na przykład hpcexcelhn01.southeastasia.cloudapp.azure.com) nie mieści się w oknie dialogowym. Obejście polega na ustawienia zmiennej dla komputera, takich jak *CCP_IAASHN* z wartością nazwy klastra długie. Następnie wprowadź *CCP_IAASHN %* w oknie dialogowym nazwy węzła głównego klastra. 
> 
> 

Po pomyślnym wdrożeniu klastra Kontynuuj poniższe kroki, aby uruchomić próbkę wbudowanych funkcji zdefiniowanej przez użytkownika w programie Excel. Dostosowane plikami UDF programu Excel, zobacz te [zasobów](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) kompilacji XLL i wdrożyć je w klastrze IaaS.

1. Otwórz nowy skoroszyt programu Excel. Na **programowanie** wstążki, kliknij przycisk **Add-Ins**. Następnie w oknie dialogowym kliknij **Przeglądaj**, przejdź do folderu %CCP_HOME%Bin\XLL32 i wybierz przykład ClusterUDF32.xll. Jeśli ClusterUDF32 nie istnieje na komputerze klienckim, skopiuj go z folderu %CCP_HOME%Bin\XLL32 węzła głównego.
   
   ![Wybieranie funkcji zdefiniowanej przez użytkownika][udf]
1. Kliknij przycisk **pliku** > **opcje** > **zaawansowane**. W obszarze **formuły**, sprawdź **Zezwalaj na funkcje zdefiniowane przez użytkownika XLL uruchomić klaster obliczeniowy**. Następnie kliknij przycisk **opcje** i wprowadź nazwę klastra pełnego w **nazwy węzła głównego klastra**. (Jak wspomniano wcześniej pola danych wejściowych jest ograniczone do 34 znaków, tak długo nazwa_klastra może nie być dopasowane. Możesz może użyć zmiennej dla komputera, który jest tutaj nazwę klastra długie).
   
   ![Konfigurowanie funkcji zdefiniowanej przez użytkownika][options]
1. Aby uruchomić obliczania funkcji zdefiniowanej przez użytkownika w klastrze, kliknij komórkę zawierającą wartość =XllGetComputerNameC(), a następnie naciśnij klawisz Enter. Funkcja po prostu pobiera nazwę węzła obliczeniowego, na którym działa funkcji zdefiniowanej przez użytkownika. Dla pierwszego uruchomienia okno dialogowe poświadczeń monituje o podanie nazwy użytkownika i hasła do łączenia z klastrem IaaS.
   
   ![Uruchamianie funkcji zdefiniowanej przez użytkownika][run]
   
   W przypadku wielu komórek do obliczania, naciśnij klawisze Alt-Shift-Ctrl + F9, aby uruchomić obliczenia na wszystkie komórki.

## <a name="step-3-run-a-soa-workload-from-an-on-premises-client"></a>Krok 3. Uruchamianie obciążenia SOA z klienta w środowisku lokalnym
Aby uruchomić aplikacje SOA ogólne w klastrze IaaS pakietu HPC Pack, najpierw użyj jednej z metod w kroku 1 do wdrożenia klastra. Określ ogólnego obrazu węzła obliczeń w tym przypadku, ponieważ nie trzeba programu Excel w węzłach obliczeniowych. Następnie wykonaj poniższe kroki.

1. Po pobraniu certyfikatu klastra, należy zaimportować go na komputerze klienckim, w obszarze Cert: \CurrentUser\Root.
1. Zainstaluj [HPC Pack 2012 R2 Update 3 SDK](http://www.microsoft.com/download/details.aspx?id=49921) i [narzędzi klienta HPC Pack 2012 R2 Update 3](https://www.microsoft.com/download/details.aspx?id=49923). Te narzędzia umożliwiają tworzenie i uruchamianie aplikacji klienckich SOA.
1. Pobierz HelloWorldR2 [przykładowego kodu](https://www.microsoft.com/download/details.aspx?id=41633). Otwórz HelloWorldR2.sln w programie Visual Studio 2010 lub 2012. (W tym przykładzie nie jest obecnie zgodna z nowszej wersji programu Visual Studio).
1. Najpierw Skompiluj projekt EchoService. Następnie można wdrożyć usługę w klastrze IaaS w taki sam sposób, które wdrażasz na lokalny klaster. Aby uzyskać szczegółowe instrukcje Zobacz Readme.doc w HelloWordR2. Modyfikowanie i tworzenie HellWorldR2 i inne projekty, zgodnie z opisem w poniższej sekcji, aby wygenerować aplikacje klienckie SOA, które są uruchamiane w klastrze usługi IaaS platformy Azure.

### <a name="use-http-binding-with-azure-storage-queue"></a>Powiązanie Http za pomocą usługi Azure storage queue
Aby użyć powiązania protokołu Http z kolejki usługi Azure storage, należy wprowadzić kilka zmian do przykładowego kodu.

* Zaktualizuj nazwę klastra.
  
    ```
  // Before
  const string headnode = "[headnode]";
  // After e.g.
  const string headnode = "hpc01.eastus.cloudapp.azure.com";
  or
  const string headnode = "hpc01.cloudapp.net";
  ```
* Opcjonalnie użyj wartości domyślnej TransportScheme w SessionStartInfo lub jawnie ustawić protokół HTTP.

```
    info.TransportScheme = TransportScheme.Http;
```

* Użyj domyślnego powiązania dla BrokerClient.
  
    ```
  // Before
  using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
  // After
  using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
  ```
  
    Lub ustawione jawnie przy użyciu basicHttpBinding.
  
    ```
  BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
  binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
  ```
* Opcjonalnie można ustawić flagę UseAzureQueue na wartość true w SessionStartInfo. Jeśli nie jest ustawiona, zostanie ustawiona wartość true, domyślnie, gdy nazwa klastra zawiera sufiksy domeny platformy Azure i TransportScheme to Http.
  
    ```
    info.UseAzureQueue = true;
  ```

### <a name="use-http-binding-without-azure-storage-queue"></a>Używaj wiązania Http bez Kolejka usługi Azure storage
Aby użyć powiązania Http bez kolejki usługi Azure storage, należy jawnie ustawić flagę UseAzureQueue na wartość false w SessionStartInfo.

```
    info.UseAzureQueue = false;
```

### <a name="use-nettcp-binding"></a>Użyj NetTcp powiązania
Aby użyć powiązania NetTcp, konfiguracji jest podobna do łączenia się z klastrem w środowisku lokalnym. Musisz otworzyć kilka punktów końcowych w węźle głównym maszyny Wirtualnej. Jeśli skrypt wdrażania IaaS pakietu HPC Pack umożliwia utworzenie klastra było możliwe, na przykład ustawić punkty końcowe w witrynie Azure portal w następujący sposób.

1. Zatrzymaj maszynę Wirtualną.
1. Dodaj porty TCP 9090, 9087, 9091, brokera 9094 w sesji, odpowiednio brokera procesu roboczego i usługi danych
   
    ![Konfigurowanie punktów końcowych][endpoint-new-portal]
1. Uruchom maszynę wirtualną.

Aplikacja kliencka SOA nie wymaga żadnych zmian, z wyjątkiem sytuacji, zmiana nazwy główne do pełnej nazwy klastra w modelu IaaS.

## <a name="next-steps"></a>Kolejne kroki
* Zobacz [te zasoby](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) Aby uzyskać więcej informacji o uruchamianiu obciążeń programu Excel za pomocą pakietu HPC Pack.
* Zobacz [Zarządzanie usługami SOA w Microsoft HPC Pack](https://technet.microsoft.com/library/ff919412.aspx) więcej informacji na temat wdrażania usług i zarządzanie nimi SOA za pomocą pakietu HPC Pack.

<!--Image references-->
[scenario]: ./media/excel-cluster-hpcpack/scenario.png
[github]: ./media/excel-cluster-hpcpack/github.png
[template]: ./media/excel-cluster-hpcpack/template.png
[parameters]: ./media/excel-cluster-hpcpack/parameters.png
[parameters-new-portal]: ./media/excel-cluster-hpcpack/parameters-new-portal.png
[create]: ./media/excel-cluster-hpcpack/create.png
[connect]: ./media/excel-cluster-hpcpack/connect.png
[cert]: ./media/excel-cluster-hpcpack/cert.png
[addin]: ./media/excel-cluster-hpcpack/addin.png
[macro]: ./media/excel-cluster-hpcpack/macro.png
[options]: ./media/excel-cluster-hpcpack/options.png
[run]: ./media/excel-cluster-hpcpack/run.png
[endpoint]: ./media/excel-cluster-hpcpack/endpoint.png
[endpoint-new-portal]: ./media/excel-cluster-hpcpack/endpoint-new-portal.png
[udf]: ./media/excel-cluster-hpcpack/udf.png
