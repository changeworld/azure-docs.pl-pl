---
title: SQL Server Business Intelligence | Dokumentacja firmy Microsoft
description: W tym temacie używa zasobów utworzone za pomocą klasycznym modelu wdrażania i zawiera opis funkcji analizy biznesowej (BI) dostępnej dla programu SQL Server uruchomionego na maszynach wirtualnych Azure (VM).
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
ms.openlocfilehash: c8204e204978a9fd7cda2e9c02be0c70722b01bb
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37021364"
---
# <a name="sql-server-business-intelligence-in-azure-virtual-machines"></a>Analiza biznesowa programu SQL Server w usłudze Azure Virtual Machines
> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.

Galeria maszyny wirtualnej platformy Microsoft Azure zawiera obrazy, które zawierają instalacji programu SQL Server. Wersje programu SQL Server obsługiwane galerii obrazów są tego samego pliki instalacyjne, które można zainstalować na komputerach lokalnych i maszyn wirtualnych. Ten temat zawiera podsumowanie funkcji programu SQL Server Business Intelligence (BI) zainstalowanych na obrazy i czynności konfiguracyjnych wymaganych po udostępnieniu maszyny wirtualnej. W tym temacie opisano obsługiwane topologie wdrażania funkcji analizy Biznesowej i najlepsze rozwiązania.

## <a name="license-considerations"></a>Uwagi dotyczące licencji
Istnieją dwa sposoby licencji programu SQL Server w maszynach wirtualnych platformy Azure firmy Microsoft:

1. Korzyści mobilności licencji, które są częścią Software Assurance. Aby uzyskać więcej informacji, zobacz [przenośność licencji za pośrednictwem programu Software Assurance na platformie Azure](https://azure.microsoft.com/pricing/license-mobility/).
2. Należy zwrócić na godzinę szybkość maszyn wirtualnych Azure z programem SQL Server zainstalowana. Zobacz sekcję "Program SQL Server" w [cennik maszyn wirtualnych](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

Aby uzyskać więcej informacji o licencji i bieżącej szybkości, zobacz [często zadawane pytania dotyczące licencjonowania maszyn wirtualnych](https://azure.microsoft.com/pricing/licensing-faq/).

## <a name="sql-server-images-available-in-azure-virtual-machine-gallery"></a>SQL Server dostępnych obrazów w galerii maszyn wirtualnych platformy Azure
Galeria maszyny wirtualnej platformy Microsoft Azure zawiera kilka obrazów, które zawierają programu Microsoft SQL Server. Oprogramowanie zostało zainstalowane na obrazy maszyny wirtualnej w zależności od wersji systemu operacyjnego i wersji programu SQL Server. Lista dostępnych obrazów w galerii Azure maszyny wirtualnej zmienia się często.

<!--![SQL image in azure VM gallery](./media/virtual-machines-windows-classic-ps-sql-bi/IC741367.png)-->
![Obraz SQL w galerii maszyn wirtualnych Azure](./media/virtual-machines-windows-classic-ps-sql-bi/vm-sql-images.png)

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Poniższy skrypt programu PowerShell zwraca listę Azure obrazów, które zawierają "SQL-Server" w Nazwa_obrazu:

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

Aby uzyskać więcej informacji o wersji i funkcji obsługiwanych przez program SQL Server zobacz następujące tematy:

* [Wersje programu SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-editions/#fbid=Zae0-E6r5oh)
* [Funkcje obsługiwane przez wersje programu SQL Server 2016](https://msdn.microsoft.com/library/cc645993.aspx)

### <a name="bi-features-installed-on-the-sql-server-virtual-machine-gallery-images"></a>Funkcji analizy BIZNESOWEJ zainstalowanych na obrazy SQL Server w galerii maszyn wirtualnych
Poniższa tabela zawiera podsumowanie funkcji analizy biznesowej zainstalowane na wspólnej obrazów Galeria maszyny wirtualnej platformy Microsoft Azure dla programu SQL Server:

* SQL Server 2016 SP1 Enterprise
* SQL Server 2016 z dodatkiem SP1 Standard
* SQL Server 2014 SP2 Enterprise
* SQL Server 2014 z dodatkiem SP2 Standard
* SQL Server 2012 SP3 Enterprise
* SQL Server 2012 SP3 Standard

| SQL Server BI funkcji | Zainstalować w obrazie galerii | Uwagi |
| --- | --- | --- |
| **Tryb macierzysty usług raportowania** |Yes |Zainstalowany, ale wymaga konfiguracji, w tym adresu URL Menedżera raportów. Zobacz sekcję [skonfigurować usługi Reporting Services](#configure-reporting-services). |
| **Usług raportowania w trybie programu SharePoint** |Nie |Obraz galerii maszyny wirtualnej platformy Microsoft Azure nie ma programu SharePoint lub SharePoint plików instalacyjnych. <sup>1</sup> |
| **Wyszukiwania danych i wielowymiarowych usług Analysis Services (OLAP)** |Yes |Zainstalowany i skonfigurowany jako domyślnego wystąpienia usług Analysis Services |
| **Tabelaryczne usług Analysis Services** |Nie |Obsługiwane w programie SQL Server 2012, 2014 i 2016 obrazów, ale go nie zainstalowano domyślnie. Zainstalować inne wystąpienie usług Analysis Services. Zobacz sekcję zainstalowanie innych usług SQL Server i funkcji w tym temacie. |
| **Dodatek Power Pivot usług analizy dla programu SharePoint** |Nie |Obraz galerii maszyny wirtualnej platformy Microsoft Azure nie ma programu SharePoint lub SharePoint plików instalacyjnych. <sup>1</sup> |

<sup>1</sup> dodatkowe informacje na temat programu SharePoint i maszyn wirtualnych platformy Azure, zobacz [Microsoft Azure architektury programu SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) i [wdrożenia programu SharePoint w Microsoft Azure Virtual Machines](https://www.microsoft.com/download/details.aspx?id=34598).

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Uruchom następujące polecenie programu PowerShell, aby uzyskać listę zainstalowanych usług, które zawierają "SQL" w nazwie usługi.

    get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## <a name="general-recommendations-and-best-practices"></a>Ogólne zalecenia i najlepsze rozwiązania
* Minimalna zalecana rozmiar maszyny wirtualnej jest **A3** przy użyciu programu SQL Server Enterprise Edition. **A4** rozmiar maszyny wirtualnej jest zalecane w przypadku wdrożeń analizy Biznesowej programu SQL Server Analysis Services i usług Reporting Services.
  
    Uzyskać informacji o bieżącym rozmiarów maszyn wirtualnych, zobacz [rozmiarów maszyn wirtualnych na platformie Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Najlepszym rozwiązaniem do zarządzania dyskami jest inny niż przechowywanie danych, dzienników i pliki kopii zapasowych na dyskach **C**: i **D**:. Na przykład utworzyć dysków z danymi **E**: i **F**:.
  
  * Dysk buforowanie zasad na domyślnym dysku **C**: nie są optymalne dla pracy z danymi.
  * **D**: dysk jest dyskiem tymczasowego, który jest używany głównie dla pliku stronicowania. **D**: dysk nie jest trwały i nie są zapisywane w magazynie obiektów blob. Resetuj rozmiar zadań zarządzania, takich jak zmiana w maszynie wirtualnej **D**: dysku. Zaleca się **nie** użyj **D**: dysku plików bazy danych, takie jak bazy danych tempdb.
    
    Aby uzyskać więcej informacji na temat tworzenia i dołączania dysków, zobacz [jak dołączyć dysku danych do maszyny wirtualnej](../classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Zatrzymaj lub odinstalować usługi, które nie będą używane. Dla przykładu Jeśli maszyna wirtualna jest używana tylko dla usług Reporting Services, Zatrzymaj lub odinstalowanie usług Analysis Services i SQL Server Integration Services. Poniższa ilustracja jest przykładem usług, które są uruchamiane domyślnie.
  
    ![Usługi programu SQL Server](./media/virtual-machines-windows-classic-ps-sql-bi/IC650107.gif)
  
  > [!NOTE]
  > Aparat bazy danych programu SQL Server jest wymagany w obsługiwane scenariusze analizy Biznesowej. W jednym serwerze topologia maszyny Wirtualnej aparatu bazy danych jest wymagany na uruchomienie na tej samej maszyny Wirtualnej.
  
    Aby uzyskać więcej informacji, zobacz następujące tematy: [odinstalowania usług Reporting Services](https://msdn.microsoft.com/library/hh479745.aspx) i [Odinstalowywanie wystąpienia Analysis Services](https://msdn.microsoft.com/library/ms143687.aspx).
* Sprawdź **usługi Windows Update** nowe "ważne aktualizacje". Obrazy maszyny wirtualnej platformy Microsoft Azure często są odświeżane; jednak ważne aktualizacje może stać się dostępne z **usługi Windows Update** po ostatniego odświeżenia obrazu maszyny Wirtualnej.

## <a name="example-deployment-topologies"></a>Przykładowe topologie wdrażania
Poniżej przedstawiono przykład wdrożenia, których użyć maszyny wirtualne Microsoft Azure. Topologie na diagramach te są tylko niektóre możliwe topologie, których można używać z funkcji analizy Biznesowej programu SQL Server i maszyny wirtualne Microsoft Azure.

### <a name="single-virtual-machine"></a>Jednej maszyny wirtualnej
Analysis Services, usług Reporting Services, aparatu bazy danych programu SQL Server i źródeł danych na jednej maszynie wirtualnej.

![Scenariusz 1 maszynę wirtualną raz analizy biznesowej](./media/virtual-machines-windows-classic-ps-sql-bi/IC650108.gif)

### <a name="two-virtual-machines"></a>Dwie maszyny wirtualne
* Usługi Analysis Services, usługi Reporting Services i aparat bazy danych programu SQL Server na jednej maszynie wirtualnej. To wdrożenie obejmuje baz danych serwera raportów.
* Źródła danych na drugim maszyny Wirtualnej. Drugi maszyny Wirtualnej zawiera aparat bazy danych programu SQL Server jako źródła danych.

![Scenariusz 2 maszyn wirtualnych iaas analizy biznesowej](./media/virtual-machines-windows-classic-ps-sql-bi/IC650109.gif)

### <a name="mixed-azure--data-on-azure-sql-database"></a>Mieszane Azure — dane w bazie danych Azure SQL
* Usługi Analysis Services, usługi Reporting Services i aparat bazy danych programu SQL Server na jednej maszynie wirtualnej. To wdrożenie obejmuje baz danych serwera raportów.
* Źródło danych jest baza danych Azure SQL.

![Maszyna wirtualna scenariusze analizy biznesowej iaas i AzureSQL jako źródło danych](./media/virtual-machines-windows-classic-ps-sql-bi/IC650110.gif)

### <a name="hybrid-data-on-premises"></a>Hybrydowe — danych w sieci lokalnej
* W to Przykładowe wdrożenie usług Analysis Services usługi Reporting Services i aparatu bazy danych programu SQL Server uruchomiony na jednej maszynie wirtualnej. Maszyna wirtualna obsługuje bazy danych serwera raportów. Maszyna wirtualna jest dołączona do domeny lokalnej za pośrednictwem sieci wirtualnej platformy Azure lub innych VPN, tunelowania rozwiązania.
* Źródło danych działa lokalnie.

![iaas analizy biznesowej scenariusze maszyny wirtualnej i lokalnego źródła danych](./media/virtual-machines-windows-classic-ps-sql-bi/IC654384.gif)

## <a name="reporting-services-native-mode-configuration"></a>Konfiguracji usługi raportowania w trybie macierzystym
Obraz galerii maszyny wirtualnej dla programu SQL Server zawiera tryb macierzysty usług Reporting zainstalowany, jednak serwer raportów nie jest skonfigurowany. Kroki opisane w tej sekcji konfiguracji serwera raportów usług Reporting Services. Aby uzyskać szczegółowe informacje na temat konfigurowania trybu macierzystego usług raportowania, zobacz [zainstalować Reporting Services macierzysty tryb raportu serwera (SSRS)](https://msdn.microsoft.com/library/ms143711.aspx).

> [!NOTE]
> Podobne zawartości, który używa skryptów środowiska Windows PowerShell do konfigurowania serwera raportów, zobacz [utworzyć Azure maszyny Wirtualnej z macierzysty tryb serwera raportów w programie PowerShell](../classic/ps-sql-report.md).

### <a name="connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager"></a>Połącz z maszyną wirtualną, a następnie uruchom Menedżera konfiguracji usług raportowania
Istnieją dwa wspólne przepływy pracy podłączania do maszyny wirtualnej platformy Azure:

* Połącz w, kliknij nazwę maszyny wirtualnej, a następnie kliknij przycisk **Connect**. Otwiera połączenie pulpitu zdalnego i nazwa komputera jest wypełniane automatycznie.
  
    ![Połącz z maszyną wirtualną azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC650112.gif)
* Nawiązać połączenia z maszyną wirtualną z podłączania pulpitu zdalnego systemu Windows. W interfejsie użytkownika pulpitu zdalnego:
  
  1. Typ **nazwa usługi w chmurze** jako nazwy komputera.
  2. Wpisz, dwukropek (:) i numerem portu publicznego, którą skonfigurowano dla zdalnego pulpitu punktu końcowego TCP.
     
      Myservice.cloudapp.net:63133
     
      Aby uzyskać więcej informacji, zobacz [co to jest usługa w chmurze?](https://azure.microsoft.com/manage/services/cloud-services/what-is-a-cloud-service/).


**Uruchom Menedżera konfiguracji usług raportowania**

W **systemu Windows Server 2012/2016**:

1. Z **Start** ekranu, wpisz **usług Reporting Services** Aby wyświetlić listę aplikacji.
2. Kliknij prawym przyciskiem myszy **Reporting Services Configuration Manager** i kliknij przycisk **Uruchom jako Administrator**.

W **systemu Windows Server 2008 R2**:

1. Kliknij przycisk **Start**, a następnie kliknij przycisk **wszystkie programy**.
2. Kliknij przycisk **programu Microsoft SQL Server 2016**.
3. Kliknij przycisk **narzędzia do konfiguracji**.
4. Kliknij prawym przyciskiem myszy **Reporting Services Configuration Manager** i kliknij przycisk **Uruchom jako Administrator**.

Lub:

1. Kliknij przycisk **Start**.
2. W **Wyszukaj programy i pliki** typu okna dialogowego **usługi reporting services**. Jeśli maszyna wirtualna działa w systemie Windows Server 2012, wpisz **usługi reporting services** na ekranie Start systemu Windows Server 2012.
3. Kliknij prawym przyciskiem myszy **Reporting Services Configuration Manager** i kliknij przycisk **Uruchom jako Administrator**.
   
    ![Wyszukaj menedżera konfiguracji usług ssrs](./media/virtual-machines-windows-classic-ps-sql-bi/IC650113.gif)

### <a name="configure-reporting-services"></a>Konfigurowanie usług Reporting Services
**Konto usługi i adres URL usługi sieci web:**

1. Sprawdź **nazwy serwera** jest nazwa serwera i kliknij przycisk **Connect**.
2. Należy pamiętać, pustym **Nazwa bazy danych serwera raportów**. Baza danych została utworzona, po zakończeniu konfiguracji.
3. Sprawdź **stan serwera raportów** jest **uruchomiono**. Jeśli chcesz sprawdzić, czy usługa w Menedżerze serwera systemu Windows, usługa jest **programu SQL Server Reporting Services** usługi systemu Windows.
4. Kliknij przycisk **konta usługi** i zmienić konto, zgodnie z potrzebami. Jeśli maszyna wirtualna jest używana w środowisku przyłączone do domeny z systemem innym niż wbudowane **ReportServer** konta jest wystarczająca. Aby uzyskać więcej informacji na koncie usługi, zobacz [konta usługi](https://msdn.microsoft.com/library/ms189964.aspx).
5. Kliknij przycisk **adres URL usługi sieci Web** w okienku po lewej stronie.
6. Kliknij przycisk **Zastosuj** można skonfigurować wartości domyślne.
7. Uwaga **raport adresy URL usługi sieci Web serwera**. Należy zauważyć, domyślny port TCP 80 i jest częścią adresu URL. W kolejnym kroku utworzysz punkt końcowy maszyny wirtualnej Azure firmy Microsoft za pośrednictwem portu.
8. W **wyniki** okienka, sprawdź akcje została ukończona pomyślnie.

**Baza danych:**

1. Kliknij przycisk **bazy danych** w okienku po lewej stronie.
2. Kliknij przycisk **zmienić bazę danych**.
3. Sprawdź **Utwórz nową bazę danych serwera raportów** jest zaznaczone, a następnie kliknij przycisk Dalej.
4. Sprawdź **nazwy serwera** i kliknij przycisk **Testuj połączenie**.
5. Jeśli wynik wynosi **pomyślnie nawiązano połączenie testowe**, kliknij przycisk **OK** , a następnie kliknij przycisk **dalej**.
6. Nazwa bazy danych jest **ReportServer** i **tryb serwera raportów** jest **natywnego** kliknięcie **dalej**.
7. Kliknij przycisk **dalej** na **poświadczenia** strony.
8. Kliknij przycisk **dalej** na **Podsumowanie** strony.
9. Kliknij przycisk **dalej** na **postępu i Zakończ** strony.

**Sieci Web portalu adres URL lub adres URL Menedżera raportów 2012 i 2014:**

1. Kliknij przycisk **adres URL portalu sieci Web**, lub **adres URL Menedżera raportów** 2014 i 2012, w okienku po lewej stronie.
2. Kliknij przycisk **Zastosuj**.
3. W **wyniki** okienka, sprawdź akcje została ukończona pomyślnie.
4. Kliknij przycisk **zakończenia**.

Uzyskać informacji dotyczących uprawnień serwera raportów, zobacz [udzielanie uprawnień na serwerze raportów trybu macierzystego](https://msdn.microsoft.com/library/ms156014.aspx).

### <a name="browse-to-the-local-report-manager"></a>Przejdź do lokalnego Menedżera raportów
Aby sprawdzić konfigurację, przejdź do Menedżera raportów na maszynie Wirtualnej.

1. Na Maszynie wirtualnej Uruchom program Internet Explorer z uprawnieniami administratora.
2. Przejdź do http://localhost/reports na maszynie Wirtualnej.

### <a name="to-connect-to-remote-web-portal-or-report-manager-for-2014-and-2012"></a>Nawiązywanie połączenia zdalnego w sieci web portalu lub Menedżera raportów 2014 i 2012
Jeśli chcesz połączyć z portalu sieci web lub Menedżera raportów 2014 i 2012 na maszynie wirtualnej z komputera zdalnego, należy utworzyć nową maszynę wirtualną punktu końcowego TCP. Domyślnie serwer raportów nasłuchuje żądań HTTP na **port 80**. Jeśli konfigurujesz adresów URL serwera raportów do używania innego portu, należy określić ten numer portu w poniższych instrukcjach.

1. Tworzenie punktu końcowego maszyny wirtualnej z TCP Port 80. Aby uzyskać więcej informacji, zobacz, [punkty końcowe maszyny wirtualnej i porty zapory](#virtual-machine-endpoints-and-firewall-ports) części tego dokumentu.
2. Otwórz port 80 w zaporze maszyny wirtualnej.
3. Przejdź do portalu sieci web lub zgłosić manager przy użyciu maszyny wirtualnej Azure **nazwy DNS** jako nazwę serwera w adresie URL. Na przykład:
   
    **Serwer raportów**: http://uebi.cloudapp.net/reportserver **portalu sieci Web**:   http://uebi.cloudapp.net/reports
   
    [Skonfiguruj zaporę dostępu do serwera raportów](https://msdn.microsoft.com/library/bb934283.aspx)

### <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Aby utworzyć i opublikować raporty do maszyny wirtualnej platformy Azure
W poniższej tabeli przedstawiono niektóre opcje, aby opublikować istniejących raportów z komputera lokalnego na serwerze raportów hostowanych na maszynie wirtualnej Microsoft Azure:

* **Report Builder**: maszyny wirtualnej zawiera kliknięcie — raz wersji programu Microsoft SQL Server Report Builder for SQL 2014 i 2012. Uruchomienie raportu konstruktora pierwszy na maszynie wirtualnej z programu SQL 2016:
  
  1. Uruchom przeglądarkę z uprawnieniami administracyjnymi.
  2. Przejdź do portalu sieci web na maszynie wirtualnej, a następnie wybierz **Pobierz** ikonę w prawym górnym rogu.
  3. Wybierz **Report Builder**.
     
     Aby uzyskać więcej informacji, zobacz [uruchomić program Report Builder](https://msdn.microsoft.com/library/ms159221.aspx).
* **SQL Server Data Tools**: maszyna wirtualna: SQL Server Data Tools jest zainstalowany na maszynie wirtualnej i może służyć do tworzenia **projektów serwera raportów** i raporty na maszynie wirtualnej. SQL Server Data Tools można opublikować raporty do serwera raportów na maszynie wirtualnej.
* **Program SQL Server Data Tools: Zdalny**: na komputerze lokalnym, Utwórz projekt usług Reporting Services w programie SQL Server Data Tools, który zawiera raporty usług Reporting Services. Skonfiguruj projekt w celu nawiązania połączenia adres URL usługi sieci web.
  
    ![Właściwości projektu narzędzia SSDT dla projektu usług SSRS](./media/virtual-machines-windows-classic-ps-sql-bi/IC650114.gif)
* Utwórz. Wirtualny dysk twardy dysk twardy zawiera raporty, a następnie przekaż i dołączyć dysku.
  
  1. Utwórz. Dysk VHD na komputerze lokalnym, który zawiera raporty.
  2. Utwórz i zainstaluj certyfikat zarządzania.
  3. Przekaż plik VHD na platformę Azure przy użyciu polecenia cmdlet Add-AzureVHD [tworzenie i przekazywanie serwera wirtualnego dysku twardego Windows Azure](../classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
  4. Dołączanie dysku do maszyny wirtualnej.

## <a name="install-other-sql-server-services-and-features"></a>Instalowanie innych usług SQL Server i funkcji
Aby zainstalować dodatkowy serwer SQL usług, takich jak usługi Analysis Services w trybie tabelarycznym, uruchom Kreatora instalacji serwera SQL. Pliki instalacyjne są na lokalnym dysku maszyny wirtualnej.

1. Kliknij przycisk **Start** , a następnie kliknij przycisk **wszystkie programy**.
2. Kliknij przycisk **Microsoft SQL Server 2016**, **programu Microsoft SQL Server 2014** lub **programu Microsoft SQL Server 2012** , a następnie kliknij przycisk **narzędzia do konfiguracji** .
3. Kliknij przycisk **Centrum instalacji programu SQL Server**.

Lub uruchom C:\SQLServer_13.0_full\setup.exe, C:\SQLServer_12.0_full\setup.exe lub C:\SQLServer_11.0_full\setup.exe

> [!NOTE]
> Po uruchomieniu Instalatora programu SQL Server po raz pierwszy więcej plików instalacji może zostać pobrana i wymaga ponownego uruchomienia maszyny wirtualnej i ponownie uruchomić Instalatora programu SQL Server.
> 
> Jeśli musisz dostosować wielokrotnie obraz maszyny wirtualnej z programu Microsoft Azure, należy rozważyć utworzenie obrazu programu SQL Server. Funkcje programu SysPrep usług analizy został włączony CU2 dodatku SP1 dla programu SQL Server 2012. Aby uzyskać więcej informacji, zobacz [zagadnienia dotyczące instalowania programu SQL Server przy użyciu narzędzia SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) i [Obsługa programu Sysprep dla ról serwera](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).
> 
> 

### <a name="to-install-analysis-services-tabular-mode"></a>Do zainstalowania w trybie tabelarycznym Analysis Services
Kroki opisane w tej sekcji **Podsumuj** instalacji usług Analysis Services trybie tabelarycznym. Aby uzyskać więcej informacji zobacz następujące tematy:

* [Instalowanie usług Analysis Services w trybie tabelarycznym](https://msdn.microsoft.com/library/hh231722.aspx)
* [Tabelaryczne modelowania (samouczek Adventure Works)](https://msdn.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**Aby zainstalować trybie tabelarycznym Analysis Services:**

1. W Kreatorze instalacji programu SQL Server kliknij **instalacji** w lewym okienku, a następnie kliknij przycisk **Instalacja autonomiczna nowego programu SQL server lub dodać funkcje do istniejącej instalacji**.
   
   * Jeśli widzisz **przeglądanie w poszukiwaniu folderu**, przejdź do c:\SQLServer_13.0_full, c:\SQLServer_12.0_full lub c:\SQLServer_11.0_full, a następnie kliknij przycisk **Ok**.
2. Kliknij przycisk **dalej** na stronie aktualizacje produktu.
3. Na **typu instalacji** wybierz pozycję **nowej instalacji programu SQL Server** i kliknij przycisk **dalej**.
4. Na **Rola Instalatora** kliknij przycisk **instalacja funkcji programu SQL Server**.
5. Na **wybór funkcji** kliknij przycisk **usług Analysis Services**.
6. Na **Konfiguracja wystąpienia** wpisz nazwę opisową, takich jak **tabelaryczny** do **wystąpienia o nazwie** i **identyfikator wystąpienia** pola tekstowe .
7. Na **Konfiguracja usługi Analysis Services** wybierz pozycję **trybie tabelarycznym**. Dodaj bieżącego użytkownika do listy uprawnień administracyjnych.
8. Wypełnij i zamknąć kreatora instalacji programu SQL Server.

## <a name="analysis-services-configuration"></a>Konfiguracja usługi Analysis Services
### <a name="remote-access-to-analysis-services-server"></a>Dostęp zdalny do serwera usług Analysis Services
Serwer usług Analysis Services obsługuje tylko uwierzytelnianie systemu windows. Aby uzyskać dostęp do usług Analysis Services zdalnie za pośrednictwem aplikacji klienckich, takich jak SQL Server Management Studio lub SQL Server Data Tools, maszyny wirtualnej musi należeć do lokalnej domeny, przy użyciu sieci wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz [sieci wirtualnej Azure](../../../virtual-network/virtual-networks-overview.md).

A **domyślnego wystąpienia** usług Analysis Services nasłuchuje na porcie TCP **2383**. Otwórz port w zaporze maszyn wirtualnych. Klastrowane nazwane wystąpienie usług Analysis Services również odbiera na porcie **2383**.

Aby uzyskać **nazwane wystąpienie** usług Analysis Services, usługi SQL Server Browser jest wymagane do zarządzania dostępem do portu. Konfiguracja domyślna przeglądarka SQL Server jest port **2382**.

W zaporze maszyn wirtualnych, należy otworzyć port **2382** i utworzyć statyczne Analysis Services o nazwie wystąpienia portu.

1. Aby sprawdzić porty, które są już używane w maszynę Wirtualną i jakie procesy wykorzystują porty, uruchom następujące polecenia z uprawnieniami administracyjnymi:
   
        netstat /ao
2. Użyj SQL Server Management Studio i utworzyć statycznych usług Analysis Services o nazwie wystąpienia portu, aktualizując "Port" wartość w tabelarycznym jako właściwości ogólnych wystąpienia. Aby uzyskać więcej informacji, zobacz "Stały port używany do domyślne lub nazwane wystąpienie" w [skonfigurować Zaporę systemu Windows, Zezwalaj na dostęp do usług analizy](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed).
3. Ponowne uruchomienie wystąpienia tabelaryczne usług Analysis Services.

Aby uzyskać więcej informacji, zobacz, **punkty końcowe maszyny wirtualnej i porty zapory** części tego dokumentu.

## <a name="virtual-machine-endpoints-and-firewall-ports"></a>Punkty końcowe maszyny wirtualnej i porty zapory
Ta sekcja zawiera podsumowanie punkty końcowe maszyny wirtualnej Azure firmy Microsoft do tworzenia i portów, otwieranych w zapór maszyny wirtualnej. Poniższa tabela zawiera podsumowanie **TCP** portów do utworzenia punktów końcowych i porty do otwarcia w zaporze maszyn wirtualnych.

* Jeśli korzystasz z jednej maszyny Wirtualnej i poniższych dwóch elementów są spełnione, nie trzeba tworzyć punkty końcowe maszyny Wirtualnej i nie trzeba otworzyć porty zapory na maszynie Wirtualnej.
  
  * Funkcje programu SQL Server na maszynie Wirtualnej nie zdalnie nawiązać. Ustanawianie połączenia pulpitu zdalnego do maszyny Wirtualnej i uzyskiwania dostępu do funkcji programu SQL Server lokalnie na maszynie Wirtualnej nie jest uważana za połączenia zdalnego z funkcji programu SQL Server.
  * Maszyna wirtualna nie przyłączyć do domeny lokalnej za pośrednictwem sieci wirtualnej platformy Azure lub innego rozwiązania tunelowania sieci VPN.
* Jeśli maszyna wirtualna nie jest przyłączony do domeny, ale chcesz zdalnie nawiązać funkcji programu SQL Server na maszynie Wirtualnej:
  
  * Otwieranie portów w zaporze na maszynie Wirtualnej.
  * Utwórz maszynę wirtualną punktów końcowych dostrzeżone portów (*).
* Jeśli maszyna wirtualna jest dołączona do domeny przy użyciu tunelu VPN, takie jak sieci wirtualnych Azure, punktów końcowych, które nie są wymagane. Jednak należy otworzyć porty zapory na maszynie Wirtualnej.
  
  | Port | Typ | Opis |
  | --- | --- | --- |
  | **80** |TCP |Serwer raportów dostępu zdalnego (*). |
  | **1433** |TCP |SQL Server Management Studio (*). |
  | **1434** |UDP |SQL Server Browser. Jest to potrzebne w przypadku maszyny Wirtualnej w ramach przyłączony do domeny. |
  | **2382** |TCP |SQL Server Browser. |
  | **2383** |TCP |Wystąpienie programu SQL Server Analysis Services domyślne i nazwane wystąpienia klastra. |
  | **Zdefiniowane przez użytkownika** |TCP |Utwórz statycznych Analysis Services o nazwie wystąpienia port numer portu, którego możesz wybrać, a następnie odblokuj numer portu w zaporze. |

Aby uzyskać więcej informacji na temat tworzenia punktów końcowych zobacz następujące tematy:

* Utwórz punkty końcowe:[jak skonfigurować punkty końcowe z maszyną wirtualną](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Program SQL Server: Zobacz sekcję "Cała konfiguracja kroki, aby połączyć z maszyną wirtualną, używając programu SQL Server Management Studio" [inicjowania obsługi maszyny wirtualnej programu SQL Server na platformie Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

Na poniższym diagramie przedstawiono porty do otwarcia w zaporze maszyny Wirtualnej, aby umożliwić uzyskiwanie zdalnego dostępu do funkcji i składników na maszynie Wirtualnej.

![portów, otwieranych w celu analizy biznesowej aplikacji w maszynach wirtualnych platformy Azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC654385.gif)

## <a name="resources"></a>Zasoby
* Przejrzyj zasady udzielania pomocy technicznej dla oprogramowania Microsoft server w środowisku maszyny wirtualnej platformy Azure. Poniższy temat zawiera podsumowanie obsługę funkcji, takich jak funkcja BitLocker, klaster trybu Failover i równoważenia obciążenia sieciowego. [Obsługa oprogramowania serwera firmy Microsoft dla maszyn wirtualnych platformy Azure](http://support.microsoft.com/kb/2721672).
* [Program SQL Server na maszynach wirtualnych platformy Azure — omówienie](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Inicjowanie obsługi administracyjnej maszyny wirtualnej programu SQL Server na platformie Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Jak można dołączyć dysku danych do maszyny wirtualnej](../classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Migrowanie bazy danych do programu SQL Server na maszynie Wirtualnej platformy Azure](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)
* [Określić wystąpienie usług analizy w trybie serwera](https://msdn.microsoft.com/library/gg471594.aspx)
* [Modelowania wielowymiarowego (samouczek Adventure Works)](https://technet.microsoft.com/library/ms170208.aspx)
* [Centrum dokumentacji platformy Azure](https://azure.microsoft.com/documentation/)
* [Przy użyciu usługi Power BI w środowisku hybrydowym](https://msdn.microsoft.com/library/dn798994.aspx)

> [!NOTE]
> [Prześlij opinię i informacje kontaktowe za pomocą programu Microsoft SQL Server Connect](https://connect.microsoft.com/SQLServer/Feedback)

### <a name="community-content"></a>Zawartość społeczności
* [Zarządzanie bazą danych Azure SQL przy użyciu programu PowerShell](http://blogs.msdn.com/b/windowsazure/archive/2013/02/07/windows-azure-sql-database-management-with-powershell.aspx)

