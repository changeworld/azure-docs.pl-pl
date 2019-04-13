---
title: Tworzenie maszyny wirtualnej systemu Windows z programem SQL Server w portalu | Microsoft Docs
description: W tym samouczku pokazano sposób tworzenia maszyny wirtualnej z programem SQL Server 2017 i systemem Windows na platformie Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c9e62cf3dfc259d7064be9fd6b80cad0140336eb
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549273"
---
# <a name="quickstart-create-a-sql-server-2017-windows-virtual-machine-in-the-azure-portal"></a>Szybki start: Tworzenie maszyny wirtualnej systemu Windows z programem SQL Server 2017 w witrynie Azure Portal

> [!div class="op_single_selector"]
> * [Windows](quickstart-sql-vm-create-portal.md)
> * [Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)

Ten przewodnik Szybki start przeprowadza użytkownika przez proces tworzenia maszyny wirtualnej z programem SQL Server w witrynie Azure Portal.


  > [!TIP]
  > - W tym przewodniku Szybki start przedstawiono ścieżkę szybkiego aprowizowania maszyny wirtualnej SQL i nawiązywania z nią połączenia. Aby uzyskać więcej informacji na temat innych opcji aprowizowania maszyn wirtualnych SQL, zobacz [Provisioning guide for Windows SQL Server VMs in the Azure portal (Przewodnik aprowizowania maszyn wirtualnych systemu Windows z programem SQL Server w witrynie Azure Portal)](virtual-machines-windows-portal-sql-server-provision.md).
  > - Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md).

## <a id="subscription"></a> Uzyskiwanie subskrypcji platformy Azure

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a id="select"></a> Wybieranie obrazu maszyny wirtualnej z programem SQL Server

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu swojego konta.

1. W witrynie Azure Portal wybierz polecenie **Utwórz zasób**. 

1. W polu wyszukiwania wpisz `SQL Server 2017 Developer on Windows Server 2016`, i naciśnij klawisz ENTER.

1. Wybierz obraz **Free SQL Server License: SQL Server 2017 Developer on Windows Server 2016** (Bezpłatna licencja programu SQL Server: SQL Server 2017 Developer w systemie Windows Server 2016). W tym samouczku jest używana wersja Developer — w pełni funkcjonalna wersja programu SQL Server, bezpłatna w przypadku zastosowań obejmujących tworzenie i testowanie. Opłaty dotyczą tylko kosztów obsługi maszyny wirtualnej. Aby uzyskać więcej informacji na temat cen, zobacz [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Wskazówki dotyczące cen maszyn wirtualnych platformy Azure z programem SQL Server).

   ![Nowe okno wyszukiwania](./media/quickstart-sql-vm-create-portal/newsearch.png)


1. Wybierz pozycję **Utwórz**.

## <a id="configure"></a> Podawanie podstawowych informacji

Na **podstawy** kartę, należy podać następujące informacje:

1. W **Project Details** sekcji, wybierz swoją subskrypcję platformy Azure, a następnie wybierz **Utwórz nową** do tworzenia nowej grupy zasobów. Typ _SQLVM-RG_ dla nazwy.

   ![Subskrypcja](media/quickstart-sql-vm-create-portal/basics-project-details.png)

1. W obszarze **wystąpienia szczegóły**:
    1. Typ _SQLVM_ dla **nazwę maszyny wirtualnej**. 
    1. Wybierz lokalizację dla Twojego **Region**. 
    1. Na potrzeby tego przewodnika Szybki Start pozostaw **opcji dostępności** równa _nie nadmiarowości infrastruktury wymagane_. Aby dowiedzieć się więcej informacji na temat opcji dostępności, zobacz [regiony platformy Azure i dostępność](../../windows/regions-and-availability.md). 
    1. W **obraz** listy wybierz _Bezpłatna licencja programu SQL Server: SQL Server 2017 Developer w systemie Windows Server 2016_. 
    1. Możliwość **Zmień rozmiar** dla **rozmiar** maszyny wirtualnej, a następnie wybierz pozycję **podstawowa A2** oferty. Pamiętaj wyczyścić zasoby po zakończeniu korzystania z nich, aby uniknąć wszelkich nieoczekiwanych opłat. 

   ![Szczegóły wystąpienia](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

1. W obszarze **konta administratora**, podaj nazwę użytkownika, taki jak _azureuser_ i hasła. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Konto administratora](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

1. W obszarze **reguły portów wejściowych**, wybierz **Zezwalaj na wybranych portach** , a następnie wybierz **protokołu RDP (3389)** z listy rozwijanej. 

   ![Reguły portów wejściowych](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)

## <a name="sql-server-settings"></a>Ustawienia programu SQL Server

Na **ustawień programu SQL Server** skonfiguruj następujące opcje:

1. W obszarze **zabezpieczeń i sieci**, wybierz opcję _publiczne (Internet_) dla **łączność z serwerem SQL** i zmień port na `1401` celu uniknięcia używania dobrze znanego numeru portu w tym scenariuszu publicznych. 
1. W obszarze **uwierzytelniania SQL**, wybierz opcję **Włącz**. Identyfikator logowania SQL jest ustawiony na tą samą nazwę użytkownika i hasło, które zostały skonfigurowane na potrzeby maszyny wirtualnej. Użyj ustawień domyślnych dla **integracji usługi Azure Key Vault** i **konfiguracji magazynu**.  

   ![Ustawienia zabezpieczeń programu SQL server](media/quickstart-sql-vm-create-portal/sql-server-settings.png)

1. Zmień inne ustawienia w razie potrzeby, a następnie wybierz **przeglądu + Utwórz**. 

   ![Przeglądanie + tworzenie](media/quickstart-sql-vm-create-portal/review-create.png)


## <a name="create-the-sql-server-vm"></a>Tworzenie maszyny wirtualnej z programem SQL Server

Na **Przejrzyj + Utwórz** kartę, Przejrzyj podsumowanie, a następnie wybierz **Utwórz** do utworzenia programu SQL Server, grupę zasobów i zasoby określone dla tej maszyny Wirtualnej.

Możesz monitorować wdrożenie z poziomu witryny Azure Portal. Przycisk **Powiadomienia** w górnej części ekranu służy do wyświetlania podstawowych informacji dotyczących stanu wdrożenia. Wdrożenie może potrwać kilka minut. 

## <a name="connect-to-sql-server"></a>Ustanawianie połączenia z programem SQL Server

1. W portalu Znajdź **publiczny adres IP** z usługi SQL Server VM in **Przegląd** sekcji właściwości maszyny wirtualnej.

1. Na innym komputerze podłączonym do Internetu, otwórz [programu SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).


1. W oknie dialogowym **Connect to Server** (Łączenie z serwerem) lub **Connect to Database Engine** (Łączenie z aparatem bazy danych) edytuj wartość **Server name** (Nazwa serwera). Wprowadź publiczny adres IP maszyny wirtualnej. Następnie dodaj przecinek i port niestandardowy, **1401**, określony podczas konfigurowania nowej maszyny wirtualnej. Na przykład `11.22.33.444,1401`.

1. W polu **Authentication** (Uwierzytelnianie) wybierz opcję **SQL Server Authentication** (Uwierzytelnianie programu SQL Server).

1. W polu **Login** (Logowanie) wpisz nazwę prawidłowego identyfikatora logowania SQL.

1. W polu **Password** (Hasło) wpisz hasło logowania.

1. Wybierz przycisk **Połącz**.

    ![nawiązywanie połączenia w programie ssms](./media/quickstart-sql-vm-create-portal/ssms-connect.png)

## <a id="remotedesktop"></a> Zdalne logowanie się do maszyny wirtualnej

Wykonaj następujące kroki, aby ustanowić połączenie z maszyną wirtualną programu SQL Server przy użyciu pulpitu zdalnego:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Po ustanowieniu połączenia z maszyną wirtualną programu SQL Server możesz uruchomić narzędzie SQL Server Management Studio i ustanowić połączenie przy użyciu uwierzytelniania systemu Windows i poświadczeń administratora lokalnego. Jeśli opcja Uwierzytelnianie programu SQL Server została włączona, możesz też ustanowić połączenie przy użyciu uwierzytelniania SQL oraz identyfikatora logowania i hasła skonfigurowanego podczas aprowizowania.

Korzystając z dostępu do maszyny, możesz bezpośrednio zmienić ustawienia maszyny i programu SQL Server, w zależności od wymagań. Na przykład możesz skonfigurować ustawienia zapory lub zmienić ustawienia konfiguracji programu SQL Server.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli maszyna wirtualna SQL ma działać w sposób ciągły, możesz uniknąć niepotrzebnych opłat, zatrzymując ją, gdy jest nieużywana. Możesz również trwale usunąć wszystkie zasoby skojarzone z maszyną wirtualną, usuwając skojarzone z nią grupy zasobów z poziomu portalu. Spowoduje to również trwałe usunięcie maszyny wirtualnej, dlatego tego polecenia należy używać z rozwagą. Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami platformy Azure za pośrednictwem portalu](../../../azure-resource-manager/manage-resource-groups-portal.md).


## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start utworzono maszynę wirtualną programu SQL Server 2017 w witrynie Azure portal. Aby dowiedzieć się więcej o sposobie przeprowadzania migracji danych do nowego serwera SQL Server, zobacz następujący artykuł.

> [!div class="nextstepaction"]
> [Migrowanie bazy danych do maszyny wirtualnej SQL](virtual-machines-windows-migrate-sql.md)
