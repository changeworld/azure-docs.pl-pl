---
title: Tworzenie maszyny wirtualnej systemu Windows z programem SQL Server w portalu | Microsoft Docs
description: W tym samouczku pokazano sposób tworzenia maszyny wirtualnej z programem SQL Server 2017 i systemem Windows na platformie Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 07/11/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 801a6fc0602882d1af49c06bafcfd51942e6da2e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75965653"
---
# <a name="quickstart-create-a-sql-server-2017-windows-virtual-machine-in-the-azure-portal"></a>Szybki start: tworzenie maszyny wirtualnej systemu Windows z programem SQL Server 2017 w witrynie Azure Portal

> [!div class="op_single_selector"]
> * [Windows](quickstart-sql-vm-create-portal.md)
> * [Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)

Ten przewodnik Szybki start przeprowadza użytkownika przez proces tworzenia maszyny wirtualnej z programem SQL Server w witrynie Azure Portal.


  > [!TIP]
  > - W tym przewodniku Szybki start przedstawiono ścieżkę szybkiego aprowizowania maszyny wirtualnej SQL i nawiązywania z nią połączenia. Aby uzyskać więcej informacji na temat innych opcji aprowizowania maszyn wirtualnych SQL, zobacz [Provisioning guide for Windows SQL Server VMs in the Azure portal (Przewodnik aprowizowania maszyn wirtualnych systemu Windows z programem SQL Server w witrynie Azure Portal)](virtual-machines-windows-portal-sql-server-provision.md).
  > - Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md).

## <a name="get-an-azure-subscription"></a><a id="subscription"></a>Uzyskaj subskrypcję platformy Azure

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="select-a-sql-server-vm-image"></a><a id="select"></a> Wybieranie obrazu maszyny wirtualnej z programem SQL Server

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu swojego konta.

1. Wybierz **sql platformy Azure** w menu po lewej stronie witryny Azure portal. Jeśli **usługi Azure SQL** nie ma na liście, wybierz wszystkie **usługi**, a następnie wpisz sql *azure* w polu wyszukiwania.
1. Wybierz **+Dodaj,** aby otworzyć stronę **opcji Wybierz wdrożenie SQL.** Dodatkowe informacje można wyświetlić, wybierając pozycję **Pokaż szczegóły** na kafelku **maszyn wirtualnych SQL.**
1. Wybierz z listy **rozwijanej bezpłatną licencję programu SQL Server: SQL Server 2017 Developer w systemie Windows Server 2016.**

   ![Nowe okno wyszukiwania](./media/quickstart-sql-vm-create-portal/select-sql-2017-vm-image.png)

1. Wybierz **pozycję Utwórz**.

   ![Nowe okno wyszukiwania](./media/quickstart-sql-vm-create-portal/create-sql-2017-vm-image.png)

## <a name="provide-basic-details"></a><a id="configure"></a> Podawanie podstawowych informacji

Na karcie **Podstawy podaj** następujące informacje:

1. W sekcji **Szczegóły projektu** wybierz subskrypcję platformy Azure, a następnie wybierz pozycję **Utwórz nowy,** aby utworzyć nową grupę zasobów. Wpisz _SQLVM-RG_ dla nazwy.

   ![Subskrypcja](media/quickstart-sql-vm-create-portal/basics-project-details.png)

1. W obszarze **Szczegóły wystąpienia:**
    1. Wpisz _SQLVM_ dla **nazwy maszyny wirtualnej**. 
    1. Wybierz lokalizację dla swojego **regionu**. 
    1. Na potrzeby tego przewodnika Szybki start pozostaw **opcje dostępności** ustawione na _Brak nadmiarowości infrastruktury._ Aby uzyskać więcej informacji o opcjach dostępności, zobacz [Dostępność](../../windows/availability.md). 
    1. Na liście **Obraz** wybierz _pozycję Bezpłatna licencja programu SQL Server: SQL Server 2017 Developer w systemie Windows Server 2016_. 
    1. Wybierz opcję **Zmień rozmiar** **rozmiaru** maszyny wirtualnej i wybierz ofertę **A2 Basic.** Pamiętaj, aby wyczyścić swoje zasoby po zakończeniu z nimi, aby zapobiec nieoczekiwanym opłatom. 

   ![Szczegóły wystąpienia](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

1. W obszarze **Konto administratora**podaj nazwę użytkownika, taką jak _azureuser_ i hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Konto administratora](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

1. W obszarze **Reguły portów przychodzących**wybierz pozycję **Zezwalaj na wybrane porty,** a następnie wybierz z listy rozwijanej pozycję **RDP (3389).** 

   ![Reguły portów wejściowych](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)

## <a name="sql-server-settings"></a>Ustawienia programu SQL Server

Na karcie **Ustawienia programu SQL Server** skonfiguruj następujące opcje:

1. W obszarze **Bezpieczeństwo & Sieci**wybierz pozycję Publiczny _(Internet)_ dla łączności `1401` **SQL** i zmień port, aby uniknąć używania dobrze znanego numeru portu w scenariuszu publicznym. 
1. W obszarze **Uwierzytelnianie SQL**wybierz pozycję **Włącz**. Identyfikator logowania SQL jest ustawiony na tą samą nazwę użytkownika i hasło, które zostały skonfigurowane na potrzeby maszyny wirtualnej. Użyj domyślnego ustawienia [**integracji usługi Azure Key Vault**](virtual-machines-windows-ps-sql-keyvault.md). **Konfiguracja magazynu** nie jest dostępna dla podstawowego obrazu maszyny Wirtualnej programu SQL Server, ale w [konfiguracji magazynu](virtual-machines-windows-sql-server-storage-configuration.md#new-vms)można znaleźć więcej informacji na temat dostępnych opcji dla innych obrazów .  

   ![Ustawienia zabezpieczeń serwera SQL](media/quickstart-sql-vm-create-portal/sql-server-settings.png)


1. W razie potrzeby zmień inne ustawienia, a następnie wybierz pozycję **Przejrzyj + utwórz**. 

   ![Recenzja + tworzenie](media/quickstart-sql-vm-create-portal/review-create.png)


## <a name="create-the-sql-server-vm"></a>Tworzenie maszyny wirtualnej z programem SQL Server

Na karcie **Recenzja + tworzenie** przejrzyj podsumowanie i wybierz pozycję **Utwórz,** aby utworzyć program SQL Server, grupę zasobów i zasoby określone dla tej maszyny Wirtualnej.

Możesz monitorować wdrożenie z poziomu witryny Azure Portal. Przycisk **Powiadomienia** w górnej części ekranu służy do wyświetlania podstawowych informacji dotyczących stanu wdrożenia. Wdrożenie może potrwać kilka minut. 

## <a name="connect-to-sql-server"></a>Ustanawianie połączenia z programem SQL Server

1. W portalu znajdź **publiczny adres IP** maszyny Wirtualnej programu SQL Server w sekcji **Przegląd** właściwości maszyny wirtualnej.

1. Na innym komputerze podłączonym do Internetu otwórz [program SQL Server Management Studio (SSMS).](/sql/ssms/download-sql-server-management-studio-ssms)


1. W oknie dialogowym **Connect to Server** (Łączenie z serwerem) lub **Connect to Database Engine** (Łączenie z aparatem bazy danych) edytuj wartość **Server name** (Nazwa serwera). Wprowadź publiczny adres IP maszyny wirtualnej. Następnie dodaj przecinek i port niestandardowy, **1401**, określony podczas konfigurowania nowej maszyny wirtualnej. Na przykład `11.22.33.444,1401`.

1. W polu **Authentication** (Uwierzytelnianie) wybierz opcję **SQL Server Authentication** (Uwierzytelnianie programu SQL Server).

1. W polu **Login** (Logowanie) wpisz nazwę prawidłowego identyfikatora logowania SQL.

1. W polu **Password** (Hasło) wpisz hasło logowania.

1. Wybierz przycisk **Połącz**.

    ![nawiązywanie połączenia w programie ssms](./media/quickstart-sql-vm-create-portal/ssms-connect.png)

## <a name="log-in-to-the-vm-remotely"></a><a id="remotedesktop"></a> Zdalne logowanie się do maszyny wirtualnej

Wykonaj następujące kroki, aby ustanowić połączenie z maszyną wirtualną programu SQL Server przy użyciu pulpitu zdalnego:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Po ustanowieniu połączenia z maszyną wirtualną programu SQL Server możesz uruchomić narzędzie SQL Server Management Studio i ustanowić połączenie przy użyciu uwierzytelniania systemu Windows i poświadczeń administratora lokalnego. Jeśli opcja Uwierzytelnianie programu SQL Server została włączona, możesz też ustanowić połączenie przy użyciu uwierzytelniania SQL oraz identyfikatora logowania i hasła skonfigurowanego podczas aprowizowania.

Korzystając z dostępu do maszyny, możesz bezpośrednio zmienić ustawienia maszyny i programu SQL Server, w zależności od wymagań. Na przykład możesz skonfigurować ustawienia zapory lub zmienić ustawienia konfiguracji programu SQL Server.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli maszyna wirtualna SQL ma działać w sposób ciągły, możesz uniknąć niepotrzebnych opłat, zatrzymując ją, gdy jest nieużywana. Możesz również trwale usunąć wszystkie zasoby skojarzone z maszyną wirtualną, usuwając skojarzone z nią grupy zasobów z poziomu portalu. Spowoduje to również trwałe usunięcie maszyny wirtualnej, dlatego tego polecenia należy używać z rozwagą. Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami platformy Azure za pośrednictwem portalu](../../../azure-resource-manager/management/manage-resource-groups-portal.md).


## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono maszynę wirtualną programu SQL Server 2017 w witrynie Azure portal. Aby dowiedzieć się więcej o sposobie przeprowadzania migracji danych do nowego serwera SQL Server, zobacz następujący artykuł.

> [!div class="nextstepaction"]
> [Migrowanie bazy danych do maszyny wirtualnej SQL](virtual-machines-windows-migrate-sql.md)
