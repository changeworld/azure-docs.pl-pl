---
title: Analiza biznesowa programu SQL Server | Dokumentacja firmy Microsoft
description: Ten temat używa zasobów utworzonych za pomocą klasycznego modelu wdrażania i opisano dostępne funkcje analizy biznesowej (BI) programu SQL Server uruchomionego na usłudze Azure Virtual Machines (VMs).
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: c681e7a7-eeda-48aa-bc35-6277f4828244
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/30/2017
ms.author: maghan
ms.openlocfilehash: 29e851772e665b4130ee58b04c264d55bcd54523
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60609465"
---
# <a name="sql-server-business-intelligence-in-azure-virtual-machines"></a>Analiza biznesowa programu SQL Server w usłudze Azure Virtual Machines
> [!IMPORTANT] 
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Usługi Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

Galeria maszyny wirtualnej Microsoft Azure zawiera obrazy, które zawierają instalacji programu SQL Server. Wersje programu SQL Server w galerii obrazów obsługiwane są te same pliki instalacji, które można zainstalować na komputerach lokalnych i maszyn wirtualnych. Ten temat zawiera podsumowanie funkcji programu SQL Server Business Intelligence (BI) zainstalowanych na obrazach i procedury konfiguracji wymaganej po zaaprowizowaniu maszyny wirtualnej. W tym temacie opisano obsługiwane topologie wdrażania funkcji analizy Biznesowej i najlepsze rozwiązania.

## <a name="license-considerations"></a>Uwagi dotyczące licencji
Istnieją dwa sposoby, aby uzyskać licencję programu SQL Server na maszynach wirtualnych platformy Microsoft:

1. Korzyści przenoszenia licencji, które są dostępne w ramach pakietu Software Assurance. Aby uzyskać więcej informacji, zobacz [przenośności licencji za pośrednictwem programu Software Assurance na platformie Azure](https://azure.microsoft.com/pricing/license-mobility/).
2. Płatność za stawka godzinowa w usłudze Azure Virtual Machines zainstalowanym programem SQL Server. Zobacz sekcję "Program SQL Server" w [cennik usługi Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

Aby uzyskać więcej informacji na temat licencjonowania i bieżącej szybkości, zobacz [często zadawane pytania dotyczące licencjonowania maszyn wirtualnych](https://azure.microsoft.com/pricing/licensing-faq/).

## <a name="sql-server-images-available-in-azure-virtual-machine-gallery"></a>SQL Server obrazów dostępnych w galerii maszyn wirtualnych platformy Azure
Galeria maszyny wirtualne systemu Azure firmy Microsoft zawiera kilka obrazów, które zawierają programu Microsoft SQL Server. Oprogramowanie zainstalowane na obrazy maszyn wirtualnych zależy od wersji systemu operacyjnego i wersji programu SQL Server. Lista obrazów dostępnych w galerii maszyn wirtualnych platformy Azure zmienia się często.

<!--![SQL image in azure VM gallery](./media/virtual-machines-windows-classic-ps-sql-bi/IC741367.png)-->
![Obraz SQL w galerii maszyn wirtualnych platformy Azure](./media/virtual-machines-windows-classic-ps-sql-bi/vm-sql-images.png)

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Poniższy skrypt programu PowerShell zwraca listę wszystkich obrazów platformy Azure, które zawierają "SQL-Server" w ImageName:

    # assumes you have already uploaded a management certificate to your Microsoft Azure Subscription. View the thumbprint value from the "Subscriptions" menu in Azure portal.

    $subscriptionID = ""    # REQUIRED: Provide your subscription ID.
    $subscriptionName = "" # REQUIRED: Provide your subscription name.
    $thumbPrint = "" # REQUIRED: Provide your certificate thumbprint.
    $certificate = Get-Item cert:\currentuser\my\$thumbPrint # REQUIRED: If your certificate is in a different store, provide it here.-Ser  store is the one specified with the -ss parameter on MakeCert

    Set-AzureSubscription -SubscriptionName $subscriptionName -Certificate $certificate -SubscriptionID $subscriptionID

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2016"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2016*"} | select imagename,category, location, label, description

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2014"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2014*"} | select imagename,category, location, label, description

Aby uzyskać więcej informacji na temat wersji i funkcji obsługiwanych przez program SQL Server zobacz następujące tematy:

* [Wersje programu SQL Server](https://www.microsoft.com/sql-server/sql-server-2017-editions)
* [Funkcje obsługiwane w różnych wersjach programu SQL Server 2016](https://msdn.microsoft.com/library/cc645993.aspx)

### <a name="bi-features-installed-on-the-sql-server-virtual-machine-gallery-images"></a>Funkcje analizy BIZNESOWEJ zainstalowanym obrazy galerii maszyn wirtualnych programu SQL Server
W poniższej tabeli podsumowano funkcje analizy biznesowej, zainstalowanym typowe obrazy galerii maszyny wirtualnej Microsoft Azure dla programu SQL Server:

* SQL Server 2016 SP1 Enterprise
* SQL Server 2016 SP1 Standard
* SQL Server 2014 SP2 Enterprise
* SQL Server 2014 SP2 Standard
* SQL Server 2012 SP3 Enterprise
* SQL Server 2012 SP3 Standard

| Funkcję analizy Biznesowej programu SQL Server | Instalowany w obrazie galerii | Uwagi |
| --- | --- | --- |
| **Reporting Services Native Mode** |Yes |Zainstalowany, ale wymaga konfiguracji, w tym adres URL Menedżera raportów. Zobacz sekcję [konfigurowania usług Reporting Services](#configure-reporting-services). |
| **Reporting Services w trybie programu SharePoint** |Nie |Obraz galerii maszyny wirtualnej Microsoft Azure nie ma programu SharePoint lub SharePoint pliki instalacyjne. <sup>1</sup> |
| **Wyszukiwania danych i wielowymiarowych usług Analysis Services (OLAP)** |Yes |Zainstalowany i skonfigurowany jako domyślne wystąpienie usług Analysis Services |
| **Tabelaryczne usługi Analysis Services** |Nie |Obsługiwane w programie SQL Server 2012, 2014 i 2016 obrazy, ale go nie zainstalowano domyślnie. Zainstaluj inne wystąpienie usług Analysis Services. Zobacz sekcję dotyczącą instalowania innych usług SQL Server i funkcji w tym temacie. |
| **Analizy usługi Power Pivot dla programu SharePoint** |Nie |Obraz galerii maszyny wirtualnej Microsoft Azure nie ma programu SharePoint lub SharePoint pliki instalacyjne. <sup>1</sup> |

<sup>1</sup> dodatkowe informacje na temat programu SharePoint i maszyn wirtualnych platformy Azure, zobacz [architektury Azure firmy Microsoft dla programu SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) i [wdrożenia programu SharePoint na Microsoft Azure Virtual Machines](https://www.microsoft.com/download/details.aspx?id=34598).

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Uruchom następujące polecenie programu PowerShell, aby uzyskać listę zainstalowanych usług, które zawierają "SQL" w nazwie usługi.

    get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## <a name="general-recommendations-and-best-practices"></a>Ogólne zalecenia i najlepsze rozwiązania
* Minimalny rozmiar zalecany dla maszyny wirtualnej jest **A3** podczas korzystania z programu SQL Server Enterprise Edition. **A4** rozmiar maszyny wirtualnej jest zalecane w przypadku wdrożeń analizy Biznesowej programu SQL Server Analysis Services i Reporting Services.
  
    Aby uzyskać informacje o bieżących rozmiarach maszyn wirtualnych, zobacz [rozmiarów maszyn wirtualnych na platformie Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Najlepszym rozwiązaniem do zarządzania dyskami jest do przechowywania danych, dzienników i pliki kopii zapasowych na dyskach innych niż **C**: i **D**:. Na przykład można utworzyć dysków z danymi **E**: i **F**:.
  
  * Dysk pamięci podręcznej zasad na domyślnym dysku **C**: nie są optymalne dla pracy z danymi.
  * **D**: jest tymczasowe dysk, który jest używany głównie do pliku stronicowania. **D**: dysk nie jest trwały i nie są zapisywane w magazynie obiektów blob. Zadania zarządzania, takie jak zmiany do maszyny wirtualnej rozmiar resetowania **D**: dysk. Zaleca się **nie** użyj **D**: dysk dla plików bazy danych, w tym bazy danych tempdb.
    
    Aby uzyskać więcej informacji na temat tworzenia i dołączanie dysków, zobacz [jak dołączyć dysk danych do maszyny wirtualnej](../classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Zatrzymaj lub odinstalowania usług, których nie zamierzasz używać. Na przykład jeśli maszyna wirtualna jest używana tylko dla usług Reporting Services, zatrzymać lub odinstalowanie usług Analysis Services i SQL Server Integration Services. Poniższa ilustracja jest przykładem usługi, które są uruchamiane domyślnie.
  
    ![Usługi programu SQL Server](./media/virtual-machines-windows-classic-ps-sql-bi/IC650107.gif)
  
  > [!NOTE]
  > Aparat bazy danych programu SQL Server jest wymagany w obsługiwanych scenariuszach Power BI. Na serwerze jednej maszyny Wirtualnej topologii aparatu bazy danych musi działać w tej samej maszyny Wirtualnej.
  
    Aby uzyskać więcej informacji zobacz następujące tematy: [Odinstalowywanie usług Reporting Services](https://msdn.microsoft.com/library/hh479745.aspx) i [wystąpienia usług Analysis Services](https://msdn.microsoft.com/library/ms143687.aspx).
* Sprawdź **Windows Update** nowe "ważne aktualizacje". Często są odświeżane obrazów maszyn wirtualnych platformy Azure firmy Microsoft; jednak ważne aktualizacje może stać się niedostępne z **Windows Update** po ostatniego odświeżania obrazu maszyny Wirtualnej.

## <a name="example-deployment-topologies"></a>Przykładowe topologie wdrażania
Poniżej przedstawiono przykład wdrożenia, korzystających z systemu Microsoft Azure Virtual Machines. Topologie w te diagramy są tylko niektóre z możliwych topologii, które za pomocą funkcji analizy Biznesowej programu SQL Server i Microsoft Azure Virtual Machines.

### <a name="single-virtual-machine"></a>Pojedyncza maszyna wirtualna
Analysis Services, usług Reporting Services, aparatu bazy danych programu SQL Server i źródeł danych na jednej maszynie wirtualnej.

![Scenariusz 1 maszyna wirtualna iaas analizy biznesowej](./media/virtual-machines-windows-classic-ps-sql-bi/IC650108.gif)

### <a name="two-virtual-machines"></a>Dwie maszyny wirtualne
* Usługi Analysis Services, usługi Reporting Services i aparatu bazy danych serwera SQL na jednej maszynie wirtualnej. To wdrożenie obejmuje baz danych serwera raportów.
* Źródła danych na drugiej maszynie Wirtualnej. Drugiej maszyny Wirtualnej zawiera aparat bazy danych programu SQL Server jako źródła danych.

![Scenariusz 2 maszyny wirtualne iaas analizy biznesowej](./media/virtual-machines-windows-classic-ps-sql-bi/IC650109.gif)

### <a name="mixed-azure--data-on-azure-sql-database"></a>Mieszane platformy Azure — dane w bazie danych Azure SQL
* Usługi Analysis Services, usługi Reporting Services i aparatu bazy danych serwera SQL na jednej maszynie wirtualnej. To wdrożenie obejmuje baz danych serwera raportów.
* Źródło danych jest bazą danych Azure SQL.

![Maszyna wirtualna scenariuszy analizy biznesowej iaas i AzureSQL jako źródło danych](./media/virtual-machines-windows-classic-ps-sql-bi/IC650110.gif)

### <a name="hybrid-data-on-premises"></a>Hybrydowe — danych w sieci lokalnej
* W tego przykładowego wdrożenia usług Analysis Services usługi Reporting Services i aparatu bazy danych programu SQL Server uruchomiony na pojedynczej maszyny wirtualnej. Maszyna wirtualna hostuje bazy danych serwera raportów. Maszyna wirtualna jest przyłączona do domeny lokalnej za pośrednictwem sieci wirtualnej platformy Azure lub innych VPN, tunelowanie rozwiązania.
* Źródło danych jest w środowisku lokalnym.

![analizy biznesowej iaas scenariusze maszyn wirtualnych i lokalnych źródeł danych](./media/virtual-machines-windows-classic-ps-sql-bi/IC654384.gif)

## <a name="reporting-services-native-mode-configuration"></a>Konfigurowanie usług Reporting Services w trybie macierzystym
Obraz galerii maszyny wirtualnej dla programu SQL Server zawiera trybu natywnego usług raportowania zainstalowany, jednak serwer raportów nie jest skonfigurowany. Kroki opisane w tej sekcji Konfigurowanie serwera raportów usług Reporting Services. Aby uzyskać więcej informacji na temat konfigurowania trybu natywnego usług raportowania, zobacz [zainstalować Reporting Services natywnych tryb raportu Server (SSRS)](https://msdn.microsoft.com/library/ms143711.aspx).

> [!NOTE]
> Podobne zawartości, który używa skryptów programu Windows PowerShell, aby skonfigurować serwer raportów, zobacz [Użyj programu PowerShell do utworzenia maszyny Wirtualnej za pomocą natywnego trybu raportu serwera Azure](../classic/ps-sql-report.md).

### <a name="connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager"></a>Łączenie z maszyną wirtualną i uruchomić Menedżera konfiguracji usług raportowania
Istnieją dwie typowe przepływy pracy dotyczące łączenia usługi na maszynie wirtualnej platformy Azure:

* Łączenie w, kliknij nazwę maszyny wirtualnej, a następnie kliknij przycisk **Connect**. Zostanie otwarte połączenie pulpitu zdalnego, a nazwa komputera jest wypełniane automatycznie.
  
    ![Łączenie z maszyną wirtualną platformy azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC650112.gif)
* Połącz z maszyną wirtualną za pomocą Windows Podłączanie pulpitu zdalnego. W interfejsie użytkownika pulpitu zdalnego:
  
  1. Typ **nazwa usługi w chmurze** jako nazwę komputera.
  2. Wpisz dwukropek (:) i numeru portu publicznego, który jest skonfigurowany dla protokołu TCP punktu końcowego pulpitu zdalnego.
     
      Myservice.cloudapp.net:63133
     
      Aby uzyskać więcej informacji, zobacz [co to jest usługa w chmurze?](https://azure.microsoft.com/manage/services/cloud-services/what-is-a-cloud-service/).


**Uruchom Menedżera konfiguracji usług raportowania**

W **systemu Windows Server 2012/2016**:

1. Z **Start** ekranu, wpisz **usług Reporting Services** umożliwia wyświetlenie listy aplikacji.
2. Kliknij prawym przyciskiem myszy **Reporting Services Configuration Manager** i kliknij przycisk **Uruchom jako Administrator**.

W **systemu Windows Server 2008 R2**:

1. Kliknij przycisk **Start**, a następnie kliknij przycisk **wszystkie programy**.
2. Click **Microsoft SQL Server 2016**.
3. Kliknij przycisk **narzędzia konfiguracji**.
4. Kliknij prawym przyciskiem myszy **Reporting Services Configuration Manager** i kliknij przycisk **Uruchom jako Administrator**.

lub:

1. Kliknij przycisk **Uruchom**.
2. W **Wyszukaj programy i pliki** typ okna dialogowego **usług reporting services**. Jeśli maszyna wirtualna działa w systemie Windows Server 2012, wpisz **usług reporting services** na ekranie Start do systemu Windows Server 2012.
3. Kliknij prawym przyciskiem myszy **Reporting Services Configuration Manager** i kliknij przycisk **Uruchom jako Administrator**.
   
    ![Wyszukaj menedżera konfiguracji usług ssrs](./media/virtual-machines-windows-classic-ps-sql-bi/IC650113.gif)

### <a name="configure-reporting-services"></a>Konfiguracja usług Reporting Services
**Konto usługi i adres URL usługi sieci web:**

1. Sprawdź **nazwy serwera** to nazwa serwera lokalnego i kliknij przycisk **Connect**.
2. Należy zwrócić uwagę pustą **Nazwa bazy danych serwera raportów**. Baza danych jest tworzony po zakończeniu konfiguracji.
3. Sprawdź **stan serwera raportów** jest **uruchomiono**. Jeśli chcesz sprawdzić, czy usługa w Menedżerze serwera systemu Windows, usługa jest **SQL Server Reporting Services** usługi Windows.
4. Kliknij przycisk **konta usługi** i zmienić konto, zgodnie z potrzebami. Jeśli maszyna wirtualna jest używana w środowisku przyłączone do domeny wbudowane **ReportServer** konta jest wystarczająca. Aby uzyskać więcej informacji na temat konta usługi, zobacz [konta usługi](https://msdn.microsoft.com/library/ms189964.aspx).
5. Kliknij przycisk **adres URL usługi sieci Web** w okienku po lewej stronie.
6. Kliknij przycisk **Zastosuj** można skonfigurować wartości domyślne.
7. Uwaga **raport adresów URL usługi sieci Web serwera**. Należy zauważyć, domyślny port TCP to 80 i jest częścią adresu URL. W kolejnym kroku utworzysz punkt końcowy maszyny wirtualnej programu Microsoft Azure za pośrednictwem portu usługi.
8. W **wyniki** okienko, sprawdź akcje zakończone powodzeniem.

**Baza danych:**

1. Kliknij przycisk **bazy danych** w okienku po lewej stronie.
2. Kliknij przycisk **zmienić bazę danych**.
3. Sprawdź **Tworzenie nowej bazy danych serwera raportów** jest zaznaczone, a następnie kliknij przycisk Dalej.
4. Sprawdź **nazwy serwera** i kliknij przycisk **Testuj połączenie**.
5. Jeśli wynik jest **pomyślnie nawiązano połączenie testowe**, kliknij przycisk **OK** a następnie kliknij przycisk **dalej**.
6. Należy pamiętać, nazwa bazy danych jest **ReportServer** i **tryb serwera raportów** jest **natywnych** kliknięcie **dalej**.
7. Kliknij przycisk **dalej** na **poświadczenia** strony.
8. Kliknij przycisk **dalej** na **Podsumowanie** strony.
9. Kliknij przycisk **dalej** na **postęp i zakończenie** strony.

**Sieci Web adres URL portalu lub adres URL Menedżera raportów, 2012 i 2014:**

1. Kliknij przycisk **adresu URL portalu internetowego**, lub **adres URL Menedżera raportów** 2014 i 2012, w okienku po lewej stronie.
2. Kliknij przycisk **Zastosuj**.
3. W **wyniki** okienko, sprawdź akcje zakończone powodzeniem.
4. Kliknij przycisk **zakończenia**.

Aby uzyskać informacje na temat uprawnień serwera raportów, zobacz [udzielanie uprawnień na serwerze raportów trybu natywnego](https://msdn.microsoft.com/library/ms156014.aspx).

### <a name="browse-to-the-local-report-manager"></a>Przejdź do Menedżera raportów lokalnych
Aby sprawdzić konfigurację, przejdź do Menedżera raportów na maszynie Wirtualnej.

1. Na maszynie Wirtualnej Uruchom program Internet Explorer z uprawnieniami administratora.
2. Przejdź do protokołu http: \/ /localhost/raporty na maszynie Wirtualnej.

### <a name="to-connect-to-remote-web-portal-or-report-manager-for-2014-and-2012"></a>Nawiązywanie połączenia zdalnego w sieci web portalu lub Menedżera raportów, 2014 i 2012
Jeśli chcesz połączyć z portalu sieci web lub Menedżera raportów dla 2014 i 2012 na maszynie wirtualnej z komputera zdalnego, należy utworzyć nową maszynę wirtualną punktu końcowego TCP. Domyślnie serwer raportów nasłuchuje żądań HTTP na **portu 80**. Jeśli skonfigurujesz adresów URL serwera raportów, aby użyć innego portu, należy określić ten numer portu w poniższych instrukcjach.

1. Tworzenie punktu końcowego dla maszyny wirtualnej na porcie 80 protokołu TCP. Aby uzyskać więcej informacji, zobacz, [punktów końcowych maszyny wirtualnej i porty zapory](#virtual-machine-endpoints-and-firewall-ports) sekcji tego dokumentu.
2. Otwórz port 80 w zaporze maszyny wirtualnej.
3. Przejdź do portalu sieci web lub raportu manager, za pomocą maszyny wirtualnej platformy Azure **nazwy DNS** jako nazwy serwera w adresie URL. Na przykład:
   
    **Serwer raportów**: http://uebi.cloudapp.net/reportserver  **Portal sieci Web**: http://uebi.cloudapp.net/reports
   
    [Konfigurowanie zapory dla dostępu do serwera raportów](https://msdn.microsoft.com/library/bb934283.aspx)

### <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Można tworzyć i publikować raporty do maszyny wirtualnej platformy Azure
Poniższa tabela zawiera podsumowanie opcji, aby opublikować istniejące raporty z komputera lokalnego na serwerze raportów hostowany na maszynie wirtualnej platformy Microsoft:

* **Report Builder**: Maszyna wirtualna zawiera kliknięcie — raz wersji programu Microsoft SQL Server Report Builder dla programu SQL 2014 i 2012. Uruchomienie raportu konstruktora pierwszej maszynie wirtualnej z programem SQL 2016:
  
  1. Uruchom przeglądarkę z uprawnieniami administracyjnymi.
  2. Przejdź do portalu sieci web na maszynie wirtualnej, a następnie wybierz pozycję **Pobierz** ikonę w prawym górnym rogu.
  3. Wybierz **Report Builder**.
     
     Aby uzyskać więcej informacji, zobacz [uruchomić program Report Builder](https://msdn.microsoft.com/library/ms159221.aspx).
* **SQL Server Data Tools**: VM:  SQL Server Data Tools jest zainstalowany na maszynie wirtualnej i może służyć do tworzenia **projekty z serwera raportów** i raporty na maszynie wirtualnej. SQL Server Data Tools mogą publikować raporty serwera raportów na maszynie wirtualnej.
* **SQL Server Data Tools: Zdalne**:  Na komputerze lokalnym Utwórz projekt usług Reporting Services w programie SQL Server Data Tools, która zawiera raporty usług Reporting Services. Skonfiguruj projekt, aby nawiązać połączenie z adresu URL usługi internetowej.
  
    ![Program SSDT właściwości projektu dla projektu usług SSRS](./media/virtual-machines-windows-classic-ps-sql-bi/IC650114.gif)
* Tworzenie. Wirtualnego dysku twardego dysku twardym, który zawiera raporty i następnie przekaż i dołączanie dysku.
  
  1. Tworzenie. Dysk wirtualny dysk twardy na komputerze lokalnym, który zawiera raporty.
  2. Tworzenie i instalowanie certyfikatu zarządzania.
  3. Przekazywanie pliku VHD na platformie Azure przy użyciu polecenia cmdlet Add-AzureVHD [tworzenie i przekazywanie VHD systemu Windows Server na platformie Azure](../classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
  4. Dołącz dysk do maszyny wirtualnej.

## <a name="install-other-sql-server-services-and-features"></a>Instalowanie innych usług SQL Server i funkcji
Aby zainstalować dodatkowy serwer SQL usług, takich jak usługi Analysis Services w trybie tabelarycznym, uruchom Kreator konfiguracji programu SQL server. Pliki konfiguracji znajdują się na lokalnym dysku maszyny wirtualnej.

1. Kliknij przycisk **Start** a następnie kliknij przycisk **wszystkie programy**.
2. Kliknij przycisk **Microsoft SQL Server 2016**, **Microsoft SQL Server 2014** lub **Microsoft SQL Server 2012** a następnie kliknij przycisk **narzędzia konfiguracji** .
3. Kliknij przycisk **Centrum instalacji SQL Server**.

Or run C:\SQLServer_13.0_full\setup.exe, C:\SQLServer_12.0_full\setup.exe or C:\SQLServer_11.0_full\setup.exe

> [!NOTE]
> Podczas pierwszego uruchomienia Instalatora programu SQL Server więcej plików instalacyjnych może zostać pobrana i wymaga ponownego uruchomienia maszyny wirtualnej i ponownie uruchomić Instalatora programu SQL Server.
> 
> Jeśli zachodzi potrzeba wielokrotnie dostosować obraz maszyny wirtualnej z Microsoft Azure, należy rozważyć utworzenie własnego obrazu programu SQL Server. Funkcje narzędzia SysPrep usług analizy została włączona przy użyciu pakietu CU2 programu SQL Server 2012 z dodatkiem SP1. Aby uzyskać więcej informacji, zobacz [informacje dotyczące instalowania programu SQL Server przy użyciu narzędzia SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) i [Obsługa narzędzia Sysprep dla ról serwera](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).
> 
> 

### <a name="to-install-analysis-services-tabular-mode"></a>Aby zainstalować trybu tabelarycznego usług analizy
Kroki opisane w tej sekcji **Podsumuj** instalacji w trybie tabelarycznym usług Analysis Services. Aby uzyskać więcej informacji zobacz następujące tematy:

* [Instalowanie usług Analysis Services działają w trybie tabelarycznym](https://msdn.microsoft.com/library/hh231722.aspx)
* [Modelowanie tabelaryczne (samouczek Adventure Works)](https://msdn.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**Aby zainstalować trybu tabelarycznego usług analizy:**

1. W Kreatorze instalacji programu SQL Server kliknij **instalacji** w okienku po lewej stronie, a następnie kliknij **SQL na nowy serwer instalacji autonomicznej lub Dodaj funkcje do istniejącej instalacji**.
   
   * Jeśli widzisz **przeglądanie w poszukiwaniu folderu**, przejdź do c:\SQLServer_13.0_full, c:\SQLServer_12.0_full lub c:\SQLServer_11.0_full, a następnie kliknij przycisk **Ok**.
2. Kliknij przycisk **dalej** na stronie aktualizacje produktu.
3. Na **typu instalacji** wybierz opcję **nowej instalacji programu SQL Server** i kliknij przycisk **dalej**.
4. Na **Rola Instalatora** kliknij **instalacja funkcji programu SQL Server**.
5. Na **wybór funkcji** kliknij **usług Analysis Services**.
6. Na **Konfiguracja wystąpienia** strony, wpisz nazwę opisową, taką jak **tabelaryczny** do **wystąpienia o nazwie** i **identyfikator wystąpienia** pól tekstowych .
7. Na **Konfiguracja usług analizy** wybierz opcję **trybie tabelarycznym**. Dodaj bieżącego użytkownika do listy uprawnień administracyjnych.
8. Zakończyć i zamknąć kreatora instalacji programu SQL Server.

## <a name="analysis-services-configuration"></a>Konfiguracja usług analizy
### <a name="remote-access-to-analysis-services-server"></a>Dostęp zdalny do serwera usług Analysis Services
Serwer usług Analysis Services obsługują tylko uwierzytelnianie systemu windows. Aby uzyskać dostęp do usług Analysis Services zdalnie z aplikacji klienckich, takich jak SQL Server Management Studio lub SQL Server Data Tools, maszyna wirtualna musi być przyłączony do domeny lokalnej, przy użyciu sieci wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md).

A **domyślnego wystąpienia** usług Analysis Services nasłuchuje na porcie TCP **2383**. Otwórz port w zaporze maszyn wirtualnych. Klastrowane nazwane wystąpienie usług Analysis Services również nasłuchuje na porcie **2383**.

Aby uzyskać **nazwane wystąpienie** usług Analysis Services, usługi SQL Server Browser jest wymagana do zarządzania dostępu do portu. Domyślnej konfiguracji programu SQL Server Browser jest port **2382**.

W zaporze maszyn wirtualnych, należy otworzyć port **2382** i utworzyć statyczne Analysis Services o nazwie portu wystąpienia.

1. Aby sprawdzić, czy porty, które są już używane w maszynie Wirtualnej i jakie procesy wykorzystują te porty, uruchom następujące polecenia z uprawnieniami administracyjnymi:
   
        netstat /ao
2. Użyj SQL Server Management Studio, aby utworzyć statyczne usług Analysis Services o nazwie portu wystąpienia, aktualizując "Port" wartość w tabelarycznych jako właściwości ogólnych wystąpienia. Aby uzyskać więcej informacji, zobacz "korzystaj stały port dla domyślnego lub nazwanego wystąpienia" w [Skonfiguruj zaporę Windows tak, aby zezwolić na dostęp do usług analizy](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed).
3. Ponowne uruchomienie wystąpienia tabelarycznego usług Analysis Services.

Aby uzyskać więcej informacji, zobacz, **punktów końcowych maszyny wirtualnej i porty zapory** sekcji tego dokumentu.

## <a name="virtual-machine-endpoints-and-firewall-ports"></a>Punktów końcowych maszyny wirtualnej i porty zapory
Ta sekcja zawiera podsumowanie punktów końcowych maszyny wirtualnej platformy Azure firmy Microsoft do tworzenia i portów, otwieranych w zapory maszyny wirtualnej. Poniższa tabela zawiera podsumowanie **TCP** portów do utworzenia punktów końcowych i porty do otwarcia w zaporze maszyn wirtualnych.

* Jeśli używasz jednej maszyny Wirtualnej i spełnione są następujące dwa elementy, nie musisz utworzyć punktów końcowych maszyny Wirtualnej i nie trzeba otworzyć porty w zaporze na maszynie Wirtualnej.
  
  * Funkcje programu SQL Server na maszynie Wirtualnej nie zdalnie nawiązać. Ustanawianie połączenia pulpitu zdalnego z maszyną wirtualną i uzyskiwania dostępu do funkcji programu SQL Server lokalnie na maszynie Wirtualnej nie jest uważany za połączenia zdalnego z funkcji programu SQL Server.
  * Nie dołączaj maszynę Wirtualną do domeny lokalnej za pośrednictwem sieci wirtualnej platformy Azure lub inne rozwiązanie tunelowania sieci VPN.
* Jeśli maszyna wirtualna nie jest przyłączony do domeny, ale chcesz zdalnie nawiązać funkcje programu SQL Server na maszynie Wirtualnej:
  
  * Otwórz porty w zaporze na maszynie Wirtualnej.
  * Tworzenie punktów końcowych maszyny wirtualnej dla portów dostrzeżone (*).
* Jeśli maszyna wirtualna jest przyłączona do domeny przy użyciu tunelu VPN, takich jak sieci wirtualne platformy Azure, punktów końcowych, które nie są wymagane. Jednak otworzyć porty w zaporze na maszynie Wirtualnej.
  
  | Port | Type | Opis |
  | --- | --- | --- |
  | **80** |TCP |Raport serwera dostępu zdalnego (*). |
  | **1433** |TCP |SQL Server Management Studio (*). |
  | **1434** |UDP |SQL Server Browser. Jest to pożądane, gdy maszyna wirtualna w przyłączony do domeny. |
  | **2382** |TCP |SQL Server Browser. |
  | **2383** |TCP |Wystąpienie domyślne programu SQL Server Analysis Services i klastrowanych wystąpień nazwanych. |
  | **zdefiniowane przez użytkownika** |TCP |Utwórz statyczny Analysis Services o nazwie wystąpienia port numer portu, którego możesz wybrać, a następnie odblokuj numer portu w zaporze. |

Aby uzyskać więcej informacji na temat tworzenia punktów końcowych zobacz następujące tematy:

* Tworzenie punktów końcowych:[jak skonfigurować punkty końcowe do maszyny wirtualnej](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Program SQL Server: Zobacz sekcję "Pełna Konfiguracja kroki nawiązywania połączenia z maszyną wirtualną Using SQL Server Management Studio" [inicjowania obsługi administracyjnej maszyny wirtualnej programu SQL Server na platformie Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

Na poniższym diagramie przedstawiono porty do otwarcia w zaporze maszyny Wirtualnej, aby zezwolić na zdalny dostęp do funkcji i składników na maszynie Wirtualnej.

![porty otworzyć dla aplikacji analizy biznesowej w maszynach wirtualnych platformy Azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC654385.gif)

## <a name="resources"></a>Zasoby
* Przejrzyj zasady pomocy technicznej dla oprogramowania serwerowego firmy Microsoft używane w środowisku maszyny wirtualnej platformy Azure. Poniższy temat zawiera podsumowanie obsługi funkcji, takich jak funkcja BitLocker, klaster trybu Failover i równoważenia obciążenia sieciowego. [Pomoc techniczna dotycząca oprogramowania serwera firmy Microsoft dla usługi Azure Virtual Machines](https://support.microsoft.com/kb/2721672).
* [Program SQL Server na maszynach wirtualnych platformy Azure — omówienie](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Inicjowanie obsługi administracyjnej maszyny wirtualnej programu SQL Server na platformie Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Jak dołączyć dysk danych do maszyny wirtualnej](../classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Migrowanie bazy danych do programu SQL Server na maszynie Wirtualnej platformy Azure](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)
* [Określić tryb serwera wystąpienie usług Analysis Services](https://msdn.microsoft.com/library/gg471594.aspx)
* [Modelowania wielowymiarowego (samouczek Adventure Works)](https://technet.microsoft.com/library/ms170208.aspx)
* [Centrum dokumentacji platformy Azure](https://azure.microsoft.com/documentation/)
* [Przy użyciu usługi Power BI w środowisku hybrydowym](https://msdn.microsoft.com/library/dn798994.aspx)

> [!NOTE]
> [Prześlij opinię i informacje kontaktowe za pośrednictwem połączenia programu Microsoft SQL Server](https://connect.microsoft.com/SQLServer/Feedback)

### <a name="community-content"></a>Zawartość społeczności
* [Zarządzanie bazą danych Azure SQL przy użyciu programu PowerShell](https://azure.microsoft.com/blog/windows-azure-sql-database-management-with-powershell/)

