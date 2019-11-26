---
title: Provision virtual machine with Azure portal
description: This how-to guide describes your options for creating Windows SQL Server 2017 virtual machines in the Azure portal.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 28f00db3b604534be5ff9cee79c0aacc41f066b5
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74464153"
---
# <a name="how-to-provision-a-windows-sql-server-virtual-machine-in-the-azure-portal"></a>How to provision a Windows SQL Server virtual machine in the Azure portal

This guide provides details on the different options available when you create a Windows SQL Server virtual machine in the Azure portal. This article covers more configuration options than the [SQL Server VM quickstart](quickstart-sql-vm-create-portal.md), which goes more through one possible provisioning task. 

Use this guide to create your own SQL Server VM. Or, use it as a reference for the available options in the Azure portal.

> [!TIP]
> Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a id="select"></a> SQL Server virtual machine gallery images

When you create a SQL Server virtual machine, you can select one of several pre-configured images from the virtual machine gallery. The following steps demonstrate how to select one of the SQL Server 2017 images.

1. Select **Azure SQL** in the left-hand menu of the Azure portal. If **Azure SQL** is not in the list, select **All services**, then type Azure SQL in the search box. (Optional) Select the star next to **Azure SQL** to favorite it and add it as an item in the left-hand navigation. 
1. Select **+ Add** to open the **Select SQL deployment option** page. You can view additional information by selecting **Show details**. 
1. Type `2017` in the SQL Server image search box on the **SQL virtual machines** tile, and then select **Free SQL Server License: SQL Server 2017 Developer on Windows Server 2016** from the drop-down. 


   ![Select SQL VM image](media/virtual-machines-windows-portal-sql-server-provision/select-sql-vm-image-portal.png)

   > [!TIP]
   > The Developer edition is used in this walkthrough because it is a full-featured, free edition of SQL Server for development testing. Opłaty dotyczą tylko kosztów obsługi maszyny wirtualnej. However, you are free to choose any of the images to use in this walkthrough. For a description of available images, see the [SQL Server Windows Virtual Machines overview](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

   > [!TIP]
   > Licensing costs for SQL Server are incorporated into the per-second pricing of the VM you create and varies by edition and cores. However, SQL Server Developer edition is free for development/testing (not production), and SQL Express is free for lightweight workloads (less than 1 GB of memory, less than 10 GB of storage). You can also bring-your-own-license (BYOL) and pay only for the VM. Nazwy tych obrazów mają prefiks {BYOL}. 
   >
   > Aby uzyskać więcej informacji na temat tych opcji, zobacz [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Wskazówki dotyczące cen maszyn wirtualnych platformy Azure z programem SQL Server).


1. Wybierz pozycję **Utwórz**.


## <a name="1-configure-basic-settings"></a>1. Configure basic settings


On the **Basics** tab, provide the following information:

* Under **Project Details**, make sure the correct subscription is selected. 
*  In the **Resource group** section, either select an existing resource group from the list or choose **Create new** to create a new resource group. Grupa zasobów to kolekcja powiązanych zasobów platformy Azure (maszyny wirtualne, konta magazynu, sieci wirtualne itp.). 

    ![Subskrypcja](media/quickstart-sql-vm-create-portal/basics-project-details.png)

  > [!NOTE]
  > Nowa grupa zasobów jest przydatna, jeśli tylko testujesz lub poznajesz wdrożenia programu SQL Server na platformie Azure. Po zakończeniu testu usuń grupę zasobów, aby automatycznie usunąć maszynę wirtualną i wszystkie skojarzone z nią zasoby. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md).


* Under **Instance details**:
    1. Enter a unique **Virtual machine name**.  
    1. Choose a location for your **Region**. 
    1. For the purpose of this guide, leave **Availability options** set to _No infrastructure redundancy required_. To find out more information about availability options, see [Availability](../../windows/availability.md). 
    1. In the **Image** list, select _Free SQL Server License: SQL Server 2017 Developer on Windows Server 2016_.  
    1. Choose to **Change size** for the **Size** of the virtual machine and select the **A2 Basic** offering. Be sure to clean up your resources once you're done with them to prevent any unexpected charges. W przypadku obciążeń produkcyjnych zobacz zalecane rozmiary i konfiguracje maszyn w artykule [Najlepsze rozwiązania w zakresie wydajności dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

    ![Instance details](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

> [!IMPORTANT]
> Szacowany koszt miesięczny wyświetlany w oknie **Wybieranie rozmiaru** nie uwzględnia kosztów licencjonowania programu SQL Server. This estimate is the cost of the VM alone. For the Express and Developer editions of SQL Server, this estimate is the total estimated cost. W przypadku innych wersji zobacz [cennik maszyn wirtualnych z systemem Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) i wybierz docelową wersję programu SQL Server. Also see the [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) and [Sizes for virtual machines](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Under **Administrator account**, provide a username and a password. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Konto administratora](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

* Under **Inbound port rules**, choose **Allow selected ports** and then select **RDP (3389)** from the drop-down. 

   ![Reguły portów wejściowych](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. Configure optional features

### <a name="disks"></a>Dyski

On the **Disks** tab, configure your disk options. 

* Under **OS disk type**, select the type of disk you want for your OS from the drop-down. Premium is recommended for production systems but is not available for a Basic VM. To utilize Premium SSD, change the virtual machine size. 
* Under **Advanced**, select **Yes** under use **Managed Disks**.

   > [!NOTE]
   > Firma Microsoft zaleca funkcję Dyski zarządzane dla programu SQL Server. Funkcja Dyski zarządzane obsługuje magazyn w tle. Ponadto jeśli maszyny wirtualne z funkcją Dyski zarządzane znajdują się w tym samym zestawie dostępności, platforma Azure dystrybuuje zasoby magazynu w celu zapewnienia odpowiedniej nadmiarowości. For more information, see [Azure Managed Disks Overview](../managed-disks-overview.md). Aby uzyskać szczegóły dotyczące dysków zarządzanych w zestawie dostępności, zobacz sekcję [Use managed disks for VMs in availability set](../manage-availability.md) (Używanie dysków zarządzanych dla maszyn wirtualnych w zestawie dostępności).

![SQL VM Disk settings](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Networking

On the **Networking** tab, configure your networking options. 

* Create a new **virtual network**, or use an existing vNet for your SQL Server VM. Designate a **Subnet** as well. 

* Under **NIC network security group**, select either a basic security group, or the advanced security group. Choosing the basic option allows you to select inbound ports for the SQL Server VM (the same values that were configured on the **Basic** tab). Selecting the advanced option allows you to choose an existing network security group, or create a new one. 

* You can make other changes to network settings, or keep the default values.

![SQL VM Networking settings](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Monitorowanie

On the **Monitoring** tab, configure monitoring and autoshutdown. 

* Azure enables **Boot diagnostics** by default with the same storage account designated for the VM. You can change these settings here, as well as enabling **OS guest diagnostics**. 
* You can enable **System assigned managed identity** and **autoshutdown** on this tab as well. 

![SQL VM management settings](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. Configure SQL Server settings

On the **SQL Server settings** tab, configure specific settings and optimizations for SQL Server. The settings that you can configure for SQL Server include the following:

| Ustawienie |
| --- |
| [Łączność](#connectivity) |
| [Uwierzytelnianie](#authentication) |
| [Integracja z usługą Azure Key Vault](#azure-key-vault-integration) |
| [Konfiguracja usługi Storage](#storage-configuration) |
| [Automatyczne stosowanie poprawek](#automated-patching) |
| [Automatyczne kopie zapasowe](#automated-backup) |
| [Machine Learning Services](#machine-learning-services) |


### <a name="connectivity"></a>Łączność

W obszarze **Łączność z serwerem SQL** określ żądany typ dostępu do wystąpienia programu SQL Server na tej maszynie wirtualnej. For the purposes of this walkthrough, select **Public (internet)** to allow connections to SQL Server from machines or services on the internet. With this option selected, Azure automatically configures the firewall and the network security group to allow traffic on the port selected.

> [!TIP]
> Domyślnie program SQL Server nasłuchuje na dobrze znanym porcie **1433**. Aby zwiększyć bezpieczeństwo, zmień port w poprzednim oknie dialogowym do nasłuchiwania na porcie innym niż domyślny, takim jak 1401. If you change the port, you must connect using that port from any client tools, such as SSMS.

![SQL VM Security](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-security.png)

Aby ustanowić połączenie z programem SQL Server za pośrednictwem Internetu, musisz również włączyć funkcję Uwierzytelnianie programu SQL Server opisaną w następnej sekcji.

Jeśli nie chcesz zezwolić na połączenia z aparatem bazy danych za pośrednictwem Internetu, wybierz jedną z następujących opcji:

* **Lokalne (tylko wewnątrz maszyny wirtualnej)** , aby zezwolić na połączenia z programem SQL Server tylko z maszyny wirtualnej.
* **Prywatne (wewnątrz usługi Virtual Network)** , aby zezwolić na połączenia z programem SQL Server tylko z maszyn wirtualnych lub usług w tej samej sieci wirtualnej.

Ogólnie rzecz biorąc, można ulepszyć zabezpieczenia, wybierając łączność z najbardziej restrykcyjnymi ograniczeniami akceptowanymi w danym scenariuszu. Jednak wszystkie opcje można zabezpieczyć przy użyciu reguł sieciowej grupy zabezpieczeń i uwierzytelniania SQL/Windows. Możesz edytować sieciową grupę zabezpieczeń po utworzeniu maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Zagadnienia dotyczące zabezpieczeń programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-security.md).



### <a name="authentication"></a>Uwierzytelnianie

If you require SQL Server Authentication, click **Enable** under **SQL authentication** on the **SQL Server settings** tab.

![Uwierzytelnianie programu SQL Server](./media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-authentication.png)

> [!NOTE]
> If you plan to access SQL Server over the internet (the Public connectivity option), you must enable SQL authentication here. Publiczny dostęp do programu SQL Server wymaga użycia uwierzytelniania SQL.

Jeśli włączasz opcję uwierzytelniania programu SQL Server, podaj informacje w polach **Nazwa logowania** i **Hasło**. This login name is configured as a SQL Server Authentication login and member of the **sysadmin** fixed server role. Aby uzyskać więcej informacji na temat trybów uwierzytelniana, zobacz artykuł [Choose an Authentication Mode](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode) (Wybieranie trybu uwierzytelniania).

Jeśli nie włączysz opcji uwierzytelniania programu SQL Server, możesz użyć konta administratora lokalnego na maszynie wirtualnej do ustanawiania połączeń z wystąpieniem programu SQL Server.


### <a name="azure-key-vault-integration"></a>Integracja magazynu kluczy Azure

To store security secrets in Azure for encryption, select **SQL Server settings**, and scroll down to  **Azure key vault integration**. Select **Enable** and fill in the requested information. 

![Integracja magazynu kluczy Azure](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-akv.png)

Poniższa tabela zawiera listę parametrów wymaganych do skonfigurowania integracji magazynu kluczy Azure.

| PARAMETR | OPIS | PRZYKŁAD |
| --- | --- | --- |
| **Adres URL magazynu kluczy** |Lokalizacja magazynu kluczy. |https:\//contosokeyvault.vault.azure.net/ |
| **Nazwa główna** |Nazwa główna usługi Azure Active Directory. Ta nazwa jest również nazywana identyfikatorem klienta. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Główny klucz tajny** |Główny klucz tajny usługi Azure Active Directory. Ten klucz tajny jest również nazywany kluczem tajnym klienta. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Nazwa poświadczenia** |**Nazwa poświadczenia**: integracja usługi Azure Key Vault powoduje utworzenie poświadczenia w programie SQL Server, co umożliwia maszynie wirtualnej dostęp do magazynu kluczy. Wybierz nazwę tego poświadczenia. |moje_poświadczenie_1 |

Aby uzyskać więcej informacji, zobacz [Configure Azure Key Vault Integration for SQL Server on Azure VMs](virtual-machines-windows-ps-sql-keyvault.md) (Konfigurowanie integracji usługi Azure Key Vault dla programu SQL Server na maszynach wirtualnych Azure).

### <a name="storage-configuration"></a>Konfiguracja usługi Storage

On the **SQL Server settings** tab, under **Storage configuration**, select **Change configuration** to open the Performance Optimized Storage Configuration page and specify the storage requirements.

![SQL VM Storage configuration](media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

W obszarze **Usługa Storage zoptymalizowana dla** wybierz jedną z następujących opcji:

* **Ogólne** to ustawienie domyślne obsługujące większość obciążeń.
* **Transactional processing** optimizes the storage for traditional database OLTP workloads.
* **Magazynowanie danych** optymalizuje magazyn dla tradycyjnych obciążeń związanych z analizą i raportami.

![SQL VM Storage configuration](media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

You can choose to leave the values at default, or you can manually change the storage topology to suit your IOPS needs. For more information, see [storage configuration](virtual-machines-windows-sql-server-storage-configuration.md). 

### <a name="sql-server-license"></a>SQL Server License
If you're a Software Assurance customer, you can utilize the [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) to bring your own SQL Server license and save on resources. 

![SQL VM License](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Automatyczne stosowanie poprawek

Opcja **Automatyczne stosowanie poprawek** jest domyślnie włączona. Automatyczne stosowanie poprawek umożliwia platformie Azure automatyczne stosowanie poprawek programu SQL Server i systemu operacyjnego. Określ dzień tygodnia, godzinę i czas trwania okna obsługi. Platforma Azure stosuje poprawki w tym oknie obsługi. Harmonogram okna obsługi korzysta z ustawień regionalnych godziny maszyny wirtualnej. Jeśli nie chcesz, aby platforma Azure automatycznie stosowała poprawki programu SQL Server i systemu operacyjnego, kliknij pozycję **Wyłącz**.  

![SQL VM automated patching](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-automated-patching.png)

Aby uzyskać więcej informacji, zobacz [Automated Patching for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md) (Automatyczne stosowanie poprawek programu SQL Server w usłudze Azure Virtual Machines).

### <a name="automated-backup"></a>Automatyczne kopie zapasowe

Automatyczną obsługę kopii zapasowych baz danych możesz włączyć dla wszystkich baz danych w obszarze **Automatyczne tworzenie kopii zapasowych**. Opcja Automatyczne kopie zapasowe jest domyślnie wyłączona.

Po włączeniu automatycznej obsługi kopii zapasowych SQL możesz skonfigurować poniższe ustawienia:

* Okres przechowywania (dni) kopii zapasowych
* Konto usługi Storage używane dla kopii zapasowych
* Opcja szyfrowania i hasło dla kopii zapasowych
* Bazy danych systemu tworzenia kopii zapasowych
* Konfigurowanie harmonogramu tworzenia kopii zapasowych

Aby szyfrować kopie zapasowe, kliknij pozycję **Włącz**. Następnie określ ustawienie **Hasło**. Platforma Azure tworzy certyfikat do szyfrowania kopii zapasowych i używa określonego hasła do ochrony tego certyfikatu. By default the schedule is set automatically, but you can create a manual schedule by selecting **Manual**. 

![SQL VM automated backups](media/virtual-machines-windows-portal-sql-server-provision/automated-backup.png)

Aby uzyskać więcej informacji, zobacz [Automated Backup for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md) (Automatyczne tworzenie kopii zapasowych dla programu SQL Server w usłudze Azure Virtual Machines).


### <a name="machine-learning-services"></a>Usługi Machine Learning

You have the option to enable [Machine Learning Services](/sql/advanced-analytics/). This option enables you to use machine learning with Python and R in SQL Server 2017. Select **Enable** on the **SQL Server Settings** window.


## <a name="4-review--create"></a>4. Review + create

On the **Review + create** tab, review the summary, and select  **Create** to create SQL Server, resource group, and resources specified for this VM.

Możesz monitorować wdrożenie z poziomu witryny Azure Portal. Przycisk **Powiadomienia** w górnej części ekranu służy do wyświetlania podstawowych informacji dotyczących stanu wdrożenia.

> [!NOTE]
> Aby podać przykład czasu wymaganego do przeprowadzenia wdrożenia, wdrożono maszynę wirtualną SQL dla regionu Wschodnie stany USA z ustawieniami domyślnymi. Przeprowadzenie tego wdrożenia testowego trwało około 12 minut. Wdrażanie może jednak trwać krócej lub dłużej zależnie od regionu i wybranych ustawień.

## <a id="remotedesktop"></a> Otwieranie maszyny wirtualnej przy użyciu Pulpitu zdalnego

Wykonaj następujące kroki, aby ustanowić połączenie z maszyną wirtualną programu SQL Server przy użyciu pulpitu zdalnego:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Po ustanowieniu połączenia z maszyną wirtualną programu SQL Server możesz uruchomić narzędzie SQL Server Management Studio i ustanowić połączenie przy użyciu uwierzytelniania systemu Windows i poświadczeń administratora lokalnego. Jeśli opcja Uwierzytelnianie programu SQL Server została włączona, możesz też ustanowić połączenie przy użyciu uwierzytelniania SQL oraz identyfikatora logowania i hasła skonfigurowanego podczas aprowizowania.

Korzystając z dostępu do maszyny, możesz bezpośrednio zmienić ustawienia maszyny i programu SQL Server, w zależności od wymagań. Na przykład możesz skonfigurować ustawienia zapory lub zmienić ustawienia konfiguracji programu SQL Server.

## <a id="connect"></a> Zdalne ustanawianie połączenia z programem SQL Server

In this walkthrough, you selected **Public** access for the virtual machine and **SQL Server Authentication**. Te ustawienia powodują automatyczne skonfigurowanie maszyny wirtualnej do zezwalania na połączenia z programem SQL Server z dowolnego klienta za pośrednictwem Internetu (zakładając, że ma on poprawny identyfikator logowania SQL).

> [!NOTE]
> Jeśli podczas aprowizacji nie wybrano opcji Publiczne, możesz zmienić ustawienia łączności SQL za pośrednictwem portalu po aprowizacji. Aby uzyskać więcej informacji, zobacz [Zmiana ustawień łączności SQL](virtual-machines-windows-sql-connect.md#change).

The following sections show how to connect over the internet to your SQL Server VM instance.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > This example uses the common port 1433. However, this value will need to be modified if a different port (such as 1401) was specified during the deployment of the SQL Server VM. 


## <a name="next-steps"></a>Następne kroki

Aby uzyskać inne informacje na temat użycia programu SQL Server na platformie Azure, zobacz [SQL Server on Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) (Program SQL Server w usłudze Azure Virtual Machines) i [Frequently Asked Questions](virtual-machines-windows-sql-server-iaas-faq.md) (Często zadawane pytania).
