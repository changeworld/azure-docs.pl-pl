---
title: Klaster HPC Pack dla programów Excel i SOA | Dokumentacja firmy Microsoft
description: Wprowadzenie do uruchamiania dużych obciążeń programu Excel i SOA w klastrze HPC Pack na platformie Azure
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
ms.openlocfilehash: aaf26e04fdb38fd76f4ab8211f9fdda8ebafd668
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="get-started-running-excel-and-soa-workloads-on-an-hpc-pack-cluster-in-azure"></a>Wprowadzenie do uruchamiania obciążeń programu Excel i SOA w klastrze HPC Pack na platformie Azure
W tym artykule przedstawiono sposób wdrażania klastra Microsoft HPC Pack 2012 R2 na maszynach wirtualnych Azure przy użyciu szablonu Azure Szybki Start lub opcjonalnie skrypt wdrażania środowiska Azure PowerShell. Klaster używa przeznaczonych do uruchamiania programu Microsoft Excel lub obciążeń zorientowane na usługę architektura (SOA) HPC Pack obrazów maszyny Wirtualnej Azure Marketplace. Klastra służy do uruchamiania usług SOA i HPC dla programu Excel z lokalnych komputera klienckiego. Usługi HPC dla programu Excel obejmują odciążenia skoroszytu programu Excel i funkcje zdefiniowane przez użytkownika programu Excel lub funkcji UDF.

> [!IMPORTANT] 
> W tym artykule opiera się na funkcje, szablony i skryptów HPC Pack 2012 R2. W tym scenariuszu nie jest obecnie obsługiwane w HPC Pack 2016.
>

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Na wysokim poziomie na poniższym diagramie przedstawiono klastra HPC Pack, że utworzono.

![Klaster HPC z węzłami uruchamiania obciążeń programu Excel][scenario]

## <a name="prerequisites"></a>Wymagania wstępne
* **Komputer kliencki** — potrzebny jest komputer klienta z systemem Windows do przesyłania zadań programu Excel i SOA próbki do klastra. Należy również komputerem z systemem Windows do uruchomienia skryptu wdrażania klastra programu Azure PowerShell (w przypadku wybrania tej metody wdrażania).
* **Subskrypcja platformy Azure** — Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.
* **Limit przydziału rdzeni** — należy zwiększyć limit przydziału rdzeni, zwłaszcza, jeśli wdrożono kilka węzłów klastra z wielordzeniowych rozmiarów maszyn wirtualnych. Jeśli przy użyciu szablonu Azure szybkiego startu przydziału rdzeni w Menedżerze zasobów jest na region platformy Azure. W takim przypadku należy zwiększyć ten przydział w określonym regionie. Zobacz [limity subskrypcji platformy Azure, przydziały i ograniczenia](../../azure-subscription-service-limits.md). Aby zwiększyć przydział, [otwarcia żądania pomocy technicznej online klienta](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) bez dodatkowych opłat.
* **Microsoft Office licencji** — Jeśli wdrażanie węzłów za pomocą obrazu maszyny Wirtualnej Marketplace HPC Pack 2012 R2 z programem Microsoft Excel, 30-dniowej wersji ewaluacyjnej programu Microsoft Excel Professional Plus 2013 zainstalowano obliczeń. Po okresie ewaluacyjnym konieczne jest zapewnienie prawidłowej licencji Microsoft Office, aby aktywować programu Excel, aby kontynuować uruchamianie obciążeń. Zobacz [aktywacji w programie Excel](#excel-activation) dalszej części tego artykułu. 

## <a name="step-1-set-up-an-hpc-pack-cluster-in-azure"></a>Krok 1. Konfigurowanie klastra HPC Pack na platformie Azure
Zostanie przedstawiony dwie opcje do skonfigurowania klastra HPC Pack 2012 R2: najpierw przy użyciu szablonu Azure Szybki Start i portalu Azure; i sekundę, za pomocą skryptu wdrażania programu Azure PowerShell.

### <a name="option-1-use-a-quickstart-template"></a>Sposób 1. Szablon szybkiego startu
Szablon Szybki Start Azure umożliwia szybkie wdrożenie klastra HPC Pack w portalu Azure. Po otwarciu szablonu w portalu można uzyskać Interfejsu prostego, gdzie możesz wprowadzić ustawienia dla klastra. Poniżej przedstawiono kroki. 

> [!TIP]
> Należy użyć [szablonu portalu Azure Marketplace](https://portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn) tworzącą podobne klastra specjalnie dla obciążeń programu Excel. Spośród następujących kroków różnić się nieznacznie.
> 
> 

1. Odwiedź stronę [strony szablonu tworzenia klastrów HPC w serwisie GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). Jeśli chcesz, przejrzyj informacje dotyczące szablonu i kod źródłowy.
2. Kliknij przycisk **wdrażanie na platformie Azure** rozpocząć wdrażanie przy użyciu szablonu w portalu Azure.
   
   ![Wdrażanie szablonu na platformie Azure][github]
3. W portalu wykonaj następujące kroki, aby wprowadzić parametry szablonu klastra HPC.
   
   a. Na **parametry** strony, wprowadź lub zmień wartości parametrów szablonu. (Kliknij ikonę obok każdego ustawienia, aby uzyskać pomoc.) W poniższym ekranie przedstawiono przykładowe wartości. W tym przykładzie jest tworzony klaster o nazwie *hpc01* w *hpc.local* węzły obliczeniowe domeny składające się z węzła głównego i 2. Węzły obliczeniowe są tworzone na podstawie obrazu HPC Pack VM, który zawiera program Microsoft Excel.
   
   ![Wprowadzanie parametrów][parameters-new-portal]
   
   > [!NOTE]
   > Maszyna wirtualna jest utworzona automatycznie z węzłem głównym [najnowsze obrazu z witryny Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) HPC Pack 2012 R2 w systemie Windows Server 2012 R2. Obecnie obraz jest oparty na HPC Pack 2012 R2 Update 3.
   > 
   > Maszyny wirtualne z węzła obliczeń są tworzone na podstawie najnowszej obraz rodziny węzła obliczeń wybranych. Wybierz **ComputeNodeWithExcel** opcji dla najnowszego pakietu HPC obliczeniowe obrazu węzła, który obejmuje wersji ewaluacyjnej programu Microsoft Excel Professional Plus 2013. Aby wdrożyć klaster ogólne SOA sesji lub Odciążanie UDF programu Excel, wybierz **ComputeNode** opcji (bez programu Excel zainstalowane).
   > 
   > 
   
   b. Wybierz subskrypcję.
   
   c. Tworzenie grupy zasobów klastra, takich jak *hpc01RG*.
   
   d. Wybierz lokalizację dla grupy zasobów, takich jak środkowe stany USA.
   
   e. Na **postanowienia prawne** Przejrzyj postanowienia. Jeśli akceptujesz, kliknij przycisk **zakupu**. Następnie, po zakończeniu ustawienie wartości dla szablonu, kliknij przycisk **Utwórz**.
4. Po zakończeniu wdrożenia (zwykle trwa około 30 minut), eksportowania pliku certyfikatu klastra z węzła głównego klastra. W kolejnym kroku należy zaimportować ten publiczny certyfikat na komputerze klienckim, aby zapewnić uwierzytelnianie po stronie serwera dla bezpiecznego powiązania protokołu HTTP.
   
   a. W portalu Azure, przejdź do pulpitu nawigacyjnego, wybierz węzła głównego, a następnie kliknij przycisk **Connect** w górnej części strony, aby połączyć się przy użyciu pulpitu zdalnego.
   
    <!-- ![Connect to the head node][connect] -->
   
   b. Użyj standardowych procedur w Menedżerze certyfikatów, aby wyeksportować certyfikat węzła głównego (znajdujący się w obszarze Cert: \LocalMachine\My) bez klucza prywatnego. W tym przykładzie należy wyeksportować *CN = hpc01.eastus.cloudapp.azure.com*.
   
   ![Eksportowanie certyfikatu][cert]

### <a name="option-2-use-the-hpc-pack-iaas-deployment-script"></a>Sposób 2. Użyj skryptu HPC Pack IaaS wdrożenia
Skrypt wdrożenia HPC Pack IaaS umożliwia innym elastyczne wdrożenie klastra HPC Pack. Tworzy klaster w klasycznym modelu wdrażania, podczas gdy szablon korzysta z modelu wdrażania usługi Azure Resource Manager. Ponadto skryptu jest zgodny z subskrypcją w usłudze Azure globalne lub chińskiej wersji platformy Azure.

**Dodatkowe wymagania wstępne**

* **Program Azure PowerShell** - [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview) (wersja 0.8.10 lub nowszego) na komputerze klienckim.
* **Skrypt wdrożenia HPC Pack IaaS** — Pobierz i Rozpakuj najnowszej wersji skryptu z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Sprawdź wersję skryptu, uruchamiając `New-HPCIaaSCluster.ps1 –Version`. Ten artykuł jest oparty na wersji 4.5.0 lub nowszej skryptu.

**Utwórz plik konfiguracji**

 Skrypt wdrażania HPC Pack IaaS używa pliku konfiguracji XML jako dane wejściowe, który opisuje infrastruktura klastra HPC. Aby wdrożyć klaster składa się z węzłem głównym i 18 węzły obliczeniowe utworzone na podstawie obrazu węzła obliczeń, który zawiera program Microsoft Excel, Zastąp wartości w danym środowisku, w poniższym przykładowym pliku konfiguracji. Aby uzyskać więcej informacji o pliku konfiguracji, zobacz plik Manual.rtf w folderze skryptów i [utworzyć klaster HPC z skrypt wdrożenia HPC Pack IaaS](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

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

* **VMName** węzła głównego **musi** być taka sama jak **ServiceName**, lub zadań SOA nie udało się uruchomić.
* Upewnij się, że możesz określić **EnableWebPortal** tak, aby certyfikat węzła głównego jest generowany i wyeksportowane.
* Plik Określa skryptu środowiska PowerShell po konfiguracji PostConfig.ps1 działającą z węzła głównego. Poniższy przykładowy skrypt umożliwia skonfigurowanie parametrów połączenia magazynu Azure, usuwa rolę węzła obliczeń z węzła głównego i oferuje wszystkie węzły w tryb online, gdy są one wdrażane. 

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
2. Zmień katalog na folder skryptów (E:\IaaSClusterScript w tym przykładzie).
   
   ```
   cd E:\IaaSClusterScript
   ```
3. Aby wdrożyć klaster HPC Pack, uruchom następujące polecenie. W tym przykładzie przyjęto założenie, że plik konfiguracji znajduje się w E:\HPCDemoConfig.xml.
   
   ```
   .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
   ```

Uruchamia skrypt wdrożenia HPC Pack przez pewien czas. Jedyną operacją, której ten skrypt wykonuje się wyeksportować i Pobierz certyfikat klastra i zapisz go w folderze dokumenty bieżącego użytkownika na komputerze klienckim. Skrypt generuje komunikat podobny do następującego. W poniższym kroku należy zaimportować certyfikat w magazynie odpowiedni certyfikat.    

    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## <a name="step-2-offload-excel-workbooks-and-run-udfs-from-an-on-premises-client"></a>Krok 2. Odciążanie skoroszytów programu Excel i uruchamiania funkcji UDF z klienta lokalnego
### <a name="excel-activation"></a>Aktywacja programu Excel
W przypadku używania obrazu maszyny Wirtualnej ComputeNodeWithExcel dla obciążeń produkcyjnych, musisz podać prawidłowy klucz licencji Microsoft Office do aktywacji programu Excel w węzłach obliczeniowych. W przeciwnym razie wersję ewaluacyjną programu Excel wygasa po 30 dniach, a systemem skoroszytów programu Excel zakończy się niepowodzeniem z COMException (0x800AC472). 

Można licencjonowania programu Excel o 30 dni oceny czasu: Zaloguj się do węzła głównego i clusrun `%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe` na Excel wszystkie węzły za pomocą Menedżera klastra HPC obliczeniowe. Można rearm maksymalnie dwa razy. Po tym należy podać prawidłowy klucz licencji pakietu Office.

Pakiet Office Professional Plus 2013 zainstalować w obrazie maszyny Wirtualnej to wersja woluminu z ogólnym woluminu licencji klucza (GVLK). Możesz to zrobić za pomocą usługi zarządzania kluczami (KMS) / aktywację opartą na usłudze (AD BA) lub klucza aktywacji wielokrotnej (MAK). 

    * Aby korzystać z usługi KMS/AD-BA, użyj istniejącego serwera usługi KMS, lub skonfigurować nową przy użyciu pakietu Microsoft Office 2013 woluminu licencji. (Jeśli chcesz, skonfigurować serwer w węźle głównym). Następnie Aktywuj klucz hosta usługi KMS za pośrednictwem telefonu lub Internetu. Następnie clusrun `ospp.vbs` Ustaw serwer usługi KMS i portu i aktywowanie pakietu Office na wszystkie węzły obliczeniowe programu Excel. 

    * Aby za pomocą klucza MAK, pierwszy clusrun `ospp.vbs` wprowadzić klucz i aktywować wszystkie węzły za pośrednictwem Internetu lub telefonu obliczeniowe programu Excel. 

> [!NOTE]
> Nie można używać Retail kluczami produktów Office Professional Plus 2013 z tego obrazu maszyny Wirtualnej. Jeśli masz prawidłowe klucze i nośnik instalacyjny dla wersji pakietu Office lub programu Excel innych niż ta wersja woluminu Office Professional Plus 2013 możesz ich użyć. Najpierw Odinstaluj tę wersję woluminu i zainstaluj wersję, do której masz. Ponownie węźle obliczeń programu Excel, można przechwycić jako dostosowanego obrazu maszyny Wirtualnej do użycia w ramach wdrożenia na dużą skalę.
> 
> 

### <a name="offload-excel-workbooks"></a>Odciążanie skoroszytów programu Excel
Wykonaj następujące kroki w celu odciążenia skoroszytu programu Excel, aby został uruchomiony w klastrze HPC Pack na platformie Azure. Aby to zrobić, musi mieć programu Excel 2010 lub 2013 już zainstalowana na komputerze klienckim.

1. Użyj jednej z opcji w kroku 1, aby wdrożyć klaster HPC Pack z programu Excel obliczeniowe obrazu węzła. Uzyskaj klastra pliku certyfikatu (.cer) i nazwy klastra użytkownika i hasła.
2. Na komputerze klienckim należy zaimportować certyfikat do klastra, w obszarze Cert: \CurrentUser\Root.
3. Upewnij się, że zainstalowano programu Excel. Utwórz plik Excel.exe.config z następującą zawartość w tym samym folderze co Excel.exe na komputerze klienckim. Ten krok zapewnia, że dodatek HPC Pack 2012 R2 Excel COM załadowaniu.
   
    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
4. Konfigurowanie klienta umożliwiają przesyłanie zadań do klastra HPC Pack. Jedną z opcji jest pobranie pełnego [instalacji HPC Pack 2012 R2 Update 3](http://www.microsoft.com/download/details.aspx?id=49922) i zainstaluj klienta HPC Pack. Można również pobrać i zainstalować [narzędzi klienta HPC Pack 2012 R2 Update 3](https://www.microsoft.com/download/details.aspx?id=49923) i odpowiednie Visual C++ 2010 redistributable dla tego komputera ([x64](http://www.microsoft.com/download/details.aspx?id=14632), [x86](https://www.microsoft.com/download/details.aspx?id=5555) ).
5. W tym przykładzie używamy próbki skoroszytu programu Excel o nazwie ConvertiblePricing_Complete.xlsb. Można go pobrać [tutaj](https://www.microsoft.com/en-us/download/details.aspx?id=2939).
6. Skopiuj skoroszytu programu Excel do folderu roboczego, takich jak D:\Excel\Run.
7. Otwórz skoroszyt programu Excel. Na **opracowanie** wstążki, kliknij przycisk **dodatki COM** i upewnij się, że dodatek HPC Pack Excel COM został załadowany pomyślnie.
   
   ![Dodatek dla pakietu HPC programu Excel][addin]
8. Edytuj makro VBA HPCControlMacros w programie Excel, zmieniając komentarze wierszy, jak pokazano w poniższym skrypcie. Zastąp wartości odpowiednich dla danego środowiska.
   
   ![Makra programu Excel dla pakietu HPC][macro]
   
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
9. Skopiuj skoroszytu programu Excel do katalogu przekazywania, takich jak D:\Excel\Upload. Ten katalog jest określony w stałej HPC_DependsFiles w makrze VBA.
10. Aby uruchomić skoroszytu w klastrze na platformie Azure, kliknij przycisk **klastra** przycisk w arkuszu.

### <a name="run-excel-udfs"></a>Uruchom funkcje UDF programu Excel
Aby uruchomić plikami UDF programu Excel, wykonaj poprzednie kroki 1 – 3, aby skonfigurować komputer kliencki. Dla funkcji UDF programu Excel nie trzeba zainstalowaną na węzłach obliczeniowych aplikacją programu Excel. Tak gdy węzły obliczeniowe tworzenia klastra, można wybrać zwykłym obliczeniowe obrazu węzła zamiast obrazu węzła obliczeń przy użyciu programu Excel.

> [!NOTE]
> Istnieje limit 34 znaków w programie Excel 2010 i 2013 — okno dialogowe łącznik klastra. To okno dialogowe służy do określania klastra funkcji UDF. Jeśli klaster Pełna nazwa jest dłuższa (na przykład hpcexcelhn01.southeastasia.cloudapp.azure.com), nie mieści się w oknie dialogowym. Należy ustawić zmiennej dla komputera, takich jak *CCP_IAASHN* z wartością nazwy klastra długo. Następnie wprowadź *CCP_IAASHN %* w oknie dialogowym nazwy węzła głównego klastra. 
> 
> 

Po pomyślnym wdrożeniu klastra, kontynuuj następujące kroki, aby uruchomić przykładowe wbudowanych UDF programu Excel. Dostosowane plikami UDF programu Excel, zobacz te [zasobów](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) tworzenie XLL i wdrożyć je w klastrze IaaS.

1. Otwórz nowy skoroszyt programu Excel. Na **opracowanie** wstążki, kliknij przycisk **Add-Ins**. Następnie w oknie dialogowym kliknij **Przeglądaj**, przejdź do folderu %CCP_HOME%Bin\XLL32 i wybierz przykład ClusterUDF32.xll. Jeśli ClusterUDF32 nie istnieje na komputerze klienckim, skopiuj go w folderze %CCP_HOME%Bin\XLL32 na węzła głównego.
   
   ![Wybierz funkcję zdefiniowaną przez użytkownika][udf]
2. Kliknij przycisk **pliku** > **opcje** > **zaawansowane**. W obszarze **formuły**, sprawdź **Zezwalaj na uruchamianie klastra obliczeniowego funkcji XLL użytkownika**. Następnie kliknij przycisk **opcje** , a następnie wprowadź nazwę klastra pełna w **nazwy węzła głównego klastra**. (Jak zanotowane wcześniej to pole wejściowe jest ograniczona do 34 znaków, tak długo nazwa_klastra może nie mieści się. Można użyć zmiennej dla komputera, w tym miejscu dla nazwy klastra długie.)
   
   ![Skonfiguruj funkcję zdefiniowaną przez użytkownika][options]
3. Aby uruchomić obliczania funkcji zdefiniowanej przez użytkownika w klastrze, kliknij komórkę z =XllGetComputerNameC() wartości, a następnie naciśnij klawisz Enter. Funkcja po prostu pobiera nazwę węzła obliczeń, na którym jest uruchomiony funkcję zdefiniowaną przez użytkownika. Dla pierwszego uruchomienia okno dialogowe poświadczeń monituje o podanie nazwy użytkownika i hasła do nawiązania połączenia klastra IaaS.
   
   ![Uruchamianie funkcji zdefiniowanej przez użytkownika][run]
   
   W przypadku wielu komórek do obliczenia, naciśnij klawisz Ctrl-Alt-Shift + F9, aby uruchomić obliczenia na wszystkie komórki.

## <a name="step-3-run-a-soa-workload-from-an-on-premises-client"></a>Krok 3. Uruchom obciążenia SOA z klienta lokalnego
Aby uruchomić ogólne SOA aplikacji w klastrze HPC Pack IaaS, należy najpierw użyć jednej z metod w kroku 1 do wdrożenia klastra. Określ ogólnego obliczeniowe obrazu węzła w tym przypadku, ponieważ nie ma potrzeby programu Excel w węzłach obliczeniowych. Następnie wykonaj następujące kroki.

1. Po pobraniu certyfikatu klastra, należy zaimportować go na komputerze klienckim w obszarze Cert: \CurrentUser\Root.
2. Zainstaluj [HPC Pack 2012 R2 Update 3 SDK](http://www.microsoft.com/download/details.aspx?id=49921) i [narzędzi klienta HPC Pack 2012 R2 Update 3](https://www.microsoft.com/download/details.aspx?id=49923). Te narzędzia umożliwiają tworzenie i uruchamianie aplikacji klienckich SOA.
3. Pobierz HelloWorldR2 [przykładowy kod](https://www.microsoft.com/download/details.aspx?id=41633). Otwórz HelloWorldR2.sln w Visual Studio 2010 lub 2012. (Ten przykład nie jest obecnie zgodny z nowszej wersji programu Visual Studio).
4. Tworzenie pierwszej kompilacji projektu EchoService. Następnie Wdróż usługę klastra IaaS w taki sam sposób wdrożyć w klastrze lokalnym. Aby uzyskać szczegółowe instrukcje Zobacz Readme.doc w HelloWordR2. Modyfikowanie i tworzenie HellWorldR2 i inne projekty, zgodnie z opisem w poniższej sekcji, można wygenerować aplikacje klienckie SOA uruchamianych w klastrze IaaS platformy Azure.

### <a name="use-http-binding-with-azure-storage-queue"></a>Używaj wiązania Http z kolejką usługi Azure storage
Umożliwia powiązanie Http z kolejką usługi Azure storage, należy wprowadzić kilka zmian w przykładowy kod.

* Aktualizacja nazwy klastra.
  
    ```
  // Before
  const string headnode = "[headnode]";
  // After e.g.
  const string headnode = "hpc01.eastus.cloudapp.azure.com";
  or
  const string headnode = "hpc01.cloudapp.net";
  ```
* Opcjonalnie użyj domyślnej TransportScheme w SessionStartInfo lub jawnie ustaw HTTP.

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
  
    Ani nie ustawiaj jawnie użycie klasy basicHttpBinding.
  
    ```
  BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
  binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
  ```
* Opcjonalnie można ustawić flagi UseAzureQueue na wartość true w SessionStartInfo. Jeśli nie jest ustawiona, zostanie ustawiona wartość true, domyślnie, gdy nazwa klastra ma sufiksy domen platformy Azure i TransportScheme Http.
  
    ```
    info.UseAzureQueue = true;
  ```

### <a name="use-http-binding-without-azure-storage-queue"></a>Używaj wiązania Http bez kolejki magazynu Azure
Aby jawnie używaj wiązania Http bez kolejki magazynu Azure, należy ustawić flagę UseAzureQueue false w SessionStartInfo.

```
    info.UseAzureQueue = false;
```

### <a name="use-nettcp-binding"></a>Użyj NetTcp powiązania
Aby użyć NetTcp powiązania, konfiguracja jest podobny do nawiązywania połączenia z lokalnego klastra. Należy otworzyć kilka punktów końcowych w węźle głównym maszyny Wirtualnej. Jeśli skrypt wdrożenia HPC Pack IaaS jest używany do tworzenia klastra, na przykład ustawić punkty końcowe w portalu Azure w następujący sposób.

1. Zatrzymaj maszynę Wirtualną.
2. Dodaj porty TCP 9090, 9087, 9091, Broker 9094 w sesji, odpowiednio Broker pracownik i usługi danych
   
    ![Konfigurowanie punktów końcowych][endpoint-new-portal]
3. Uruchom maszynę wirtualną.

Aplikacja kliencka SOA nie wymagają żadnych zmian, z wyjątkiem zmiana nazwy head pełnej nazwy klastra IaaS.

## <a name="next-steps"></a>Kolejne kroki
* Zobacz [tych zasobów](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) uzyskać więcej informacji dotyczących uruchamiania obciążeń programu Excel z HPC Pack.
* Zobacz [Zarządzanie SOA usług Microsoft HPC Pack](https://technet.microsoft.com/library/ff919412.aspx) szczegółowe informacje na temat wdrażania i zarządzania usługami SOA pakietem HPC.

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
