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
ms.openlocfilehash: 234625825c1d9729d4f06f2bb0c96325cdd81f22
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329349"
---
# <a name="quickstart-create-a-sql-server-2017-windows-virtual-machine-in-the-azure-portal"></a>Szybki start: Tworzenie maszyny wirtualnej systemu Windows z programem SQL Server 2017 w witrynie Azure Portal

> [!div class="op_single_selector"]
> * [Windows](quickstart-sql-vm-create-portal.md)
> * [Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)

Ten przewodnik Szybki start przeprowadza użytkownika przez proces tworzenia maszyny wirtualnej z programem SQL Server w witrynie Azure Portal.

> [!TIP]
> W tym przewodniku Szybki start przedstawiono ścieżkę szybkiego aprowizowania maszyny wirtualnej SQL i nawiązywania z nią połączenia. Aby uzyskać więcej informacji na temat innych opcji aprowizowania maszyn wirtualnych SQL, zobacz [Provisioning guide for Windows SQL Server VMs in the Azure portal (Przewodnik aprowizowania maszyn wirtualnych systemu Windows z programem SQL Server w witrynie Azure Portal)](virtual-machines-windows-portal-sql-server-provision.md).

> [!TIP]
> Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md).

## <a id="subscription"></a> Uzyskiwanie subskrypcji platformy Azure

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a id="select"></a> Wybieranie obrazu maszyny wirtualnej z programem SQL Server

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu swojego konta.

1. W witrynie Azure Portal kliknij pozycję **Utwórz zasób**. 

1. W polu wyszukiwania wpisz ciąg **SQL Server 2017 Developer w systemie Windows Server 2016**, a następnie naciśnij klawisz Enter.

1. Wybierz obraz **Free SQL Server License: SQL Server 2017 Developer on Windows Server 2016** (Bezpłatna licencja programu SQL Server: SQL Server 2017 Developer w systemie Windows Server 2016).

   ![Nowe okno wyszukiwania](./media/quickstart-sql-vm-create-portal/newsearch.png)

   > [!TIP]
   > W tym samouczku jest używana wersja Developer — w pełni funkcjonalna wersja programu SQL Server, bezpłatna w przypadku zastosowań obejmujących tworzenie i testowanie. Opłaty dotyczą tylko kosztów obsługi maszyny wirtualnej. Aby uzyskać więcej informacji na temat cen, zobacz [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Wskazówki dotyczące cen maszyn wirtualnych platformy Azure z programem SQL Server).

1. Kliknij pozycję **Utwórz**.

## <a id="configure"></a> Podawanie podstawowych informacji

W oknie **Podstawowe** podaj następujące informacje:

1. W polu **Nazwa** wprowadź unikatową nazwę maszyny wirtualnej. 

1. W polu **Nazwa użytkownika** wprowadź nazwę dla konta administratora lokalnego na maszynie wirtualnej.

1. Podaj silne hasło w polu **Hasło**.

1. Wprowadź nową nazwę **grupy zasobów**. Ta grupa ułatwia zarządzanie wszystkimi zasobami skojarzonymi z maszyną wirtualną.

1. Zweryfikuj inne ustawienia domyślne, a następnie kliknij przycisk **OK**, aby kontynuować.

   ![Okno podstawowych ustawień SQL](./media/quickstart-sql-vm-create-portal/azure-sql-basic.png)

## <a name="choose-virtual-machine-size"></a>Wybieranie rozmiaru maszyny wirtualnej

1. W kroku **Rozmiar** wybierz rozmiar maszyny wirtualnej w oknie **Wybierz rozmiar**.

   W ramach tego przewodnika Szybki start wybierz pozycję **D2S_V3**. W portalu wyświetlany jest szacowany miesięczny koszt maszyny w przypadku ciągłego użycia (bez uwzględnienia kosztów licencjonowania programu SQL Server). Należy pamiętać, że z wersją Developer Edition nie są związane żadne dodatkowe koszty licencjonowania dla programu SQL Server. Aby uzyskać bardziej szczegółowe informacje dotyczące cen, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

   > [!TIP]
   > Maszyna o rozmiarze **D2S_V3** pozwala zaoszczędzić koszty związane z testowaniem. Jednak w przypadku obciążeń produkcyjnych zobacz zalecane rozmiary i konfiguracje maszyn w artykule [Najlepsze rozwiązania w zakresie wydajności dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

1. Kliknij pozycję **Wybierz**, aby kontynuować.

## <a name="configure-optional-features"></a>Konfigurowanie funkcji opcjonalnych

1. W oknie **Ustawienia** wybierz port **RDP (3389)** z listy **Select public inbound ports** (Wybierz publiczne porty wejściowe), jeśli chcesz łączyć pulpit zdalny z maszyną wirtualną.

   ![Porty wejściowe](./media/quickstart-sql-vm-create-portal/inbound-ports.png)

   > [!NOTE]
   > Możesz wybrać port **MS SQL (1433)**, aby mieć zdalny dostęp do programu SQL Server. Nie jest to jednak konieczne, ponieważ krok **Ustawienia programu SQL Server** również zawiera tę opcję. Jeśli wybierzesz w tym kroku port 1433, będzie on otwarty bez względu na wybór dokonany w kroku **Ustawienia programu SQL Server**.

1. Kliknij przycisk **OK**, aby zapisać zmiany i przejść dalej.

## <a name="sql-server-settings"></a>Ustawienia programu SQL Server

W oknie **Ustawienia programu SQL Server** skonfiguruj następujące opcje.

1. Z listy rozwijanej **Łączność z serwerem SQL** wybierz pozycję **Publiczna (Internet)**. Umożliwi to połączenia programu SQL Server za pośrednictwem Internetu.

1. Zmień numer **portu** na **1401** w celu uniknięcia używania dobrze znanego numeru portu w scenariuszu publicznym.

1. W obszarze **Uwierzytelnianie SQL** kliknij pozycję **Włącz**. Identyfikator logowania SQL jest ustawiony na tą samą nazwę użytkownika i hasło, które zostały skonfigurowane na potrzeby maszyny wirtualnej.

1. W razie potrzeby zmień dowolne inne ustawienia, a następnie kliknij przycisk **OK**, aby ukończyć konfigurację maszyny wirtualnej z programem SQL Server.

   ![Ustawienia programu SQL Server](./media/quickstart-sql-vm-create-portal/sql-settings.png)

## <a name="create-the-sql-server-vm"></a>Tworzenie maszyny wirtualnej z programem SQL Server

W oknie **Podsumowanie** przejrzyj podsumowanie i kliknij pozycję **Zakup**, aby utworzyć wystąpienie programu SQL Server, grupę zasobów i zasoby określone dla tej maszyny wirtualnej.

Możesz monitorować wdrożenie z poziomu witryny Azure Portal. Przycisk **Powiadomienia** w górnej części ekranu służy do wyświetlania podstawowych informacji dotyczących stanu wdrożenia.

> [!TIP]
> Wdrażanie maszyny wirtualnej systemu Windows z programem SQL Server może potrwać kilka minut.

## <a name="connect-to-sql-server"></a>Ustanawianie połączenia z programem SQL Server

1. W portalu znajdź **publiczny adres IP** maszyny wirtualnej w sekcji **Omówienie** właściwości maszyny wirtualnej.

1. Na innym komputerze podłączonym do Internetu otwórz program SQL Server Management Studio (SSMS).

   > [!TIP]
   > Jeśli nie masz programu SQL Server Management Studio, możesz pobrać go [tutaj](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

1. W oknie dialogowym **Connect to Server** (Łączenie z serwerem) lub **Connect to Database Engine** (Łączenie z aparatem bazy danych) edytuj wartość **Server name** (Nazwa serwera). Wprowadź publiczny adres IP maszyny wirtualnej. Następnie dodaj przecinek i port niestandardowy, **1401**, określony podczas konfigurowania nowej maszyny wirtualnej. Na przykład `11.22.33.444,1401`.

1. W polu **Authentication** (Uwierzytelnianie) wybierz opcję **SQL Server Authentication** (Uwierzytelnianie programu SQL Server).

1. W polu **Login** (Logowanie) wpisz nazwę prawidłowego identyfikatora logowania SQL.

1. W polu **Password** (Hasło) wpisz hasło logowania.

1. Kliknij przycisk **Connect** (Połącz).

    ![nawiązywanie połączenia w programie ssms](./media/quickstart-sql-vm-create-portal/ssms-connect.png)

## <a id="remotedesktop"></a> Zdalne logowanie się do maszyny wirtualnej

Wykonaj następujące kroki, aby ustanowić połączenie z maszyną wirtualną programu SQL Server przy użyciu pulpitu zdalnego:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Po ustanowieniu połączenia z maszyną wirtualną programu SQL Server możesz uruchomić narzędzie SQL Server Management Studio i ustanowić połączenie przy użyciu uwierzytelniania systemu Windows i poświadczeń administratora lokalnego. Jeśli opcja Uwierzytelnianie programu SQL Server została włączona, możesz też ustanowić połączenie przy użyciu uwierzytelniania SQL oraz identyfikatora logowania i hasła skonfigurowanego podczas aprowizowania.

Korzystając z dostępu do maszyny, możesz bezpośrednio zmienić ustawienia maszyny i programu SQL Server, w zależności od wymagań. Na przykład możesz skonfigurować ustawienia zapory lub zmienić ustawienia konfiguracji programu SQL Server.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli maszyna wirtualna SQL ma działać w sposób ciągły, możesz uniknąć niepotrzebnych opłat, zatrzymując ją, gdy jest nieużywana. Możesz również trwale usunąć wszystkie zasoby skojarzone z maszyną wirtualną, usuwając skojarzone z nią grupy zasobów z poziomu portalu. Spowoduje to również trwałe usunięcie maszyny wirtualnej, dlatego tego polecenia należy używać z rozwagą. Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami platformy Azure za pośrednictwem portalu](../../../azure-resource-manager/resource-group-portal.md).

## <a name="next-steps"></a>Następne kroki

W tym samouczku Szybki start utworzono maszynę wirtualną z programem SQL Server 2017 w witrynie Azure Portal. Aby dowiedzieć się więcej o sposobie przeprowadzania migracji danych do nowego serwera SQL Server, zobacz następujący artykuł.

> [!div class="nextstepaction"]
> [Migrowanie bazy danych do maszyny wirtualnej SQL](virtual-machines-windows-migrate-sql.md)
