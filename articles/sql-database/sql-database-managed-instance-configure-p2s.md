---
title: Konfigurowanie P2S — wystąpienie zarządzane usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Nawiązać połączenia bazy danych wystąpienia zarządzanego Azure SQL przy użyciu programu SQL Server Management Studio za pomocą połączenia punkt lokacja z poziomu komputera klienckiego w środowisku lokalnym.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab, bonova, jovanpop
manager: craigg
ms.date: 11/01/2018
ms.openlocfilehash: 8579eccfade83b3b3a016fc84429a914fbccd584
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2018
ms.locfileid: "50912275"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-an-azure-sql-database-managed-instance-from-on-premises"></a>Szybki Start: Konfigurowanie połączenia typu punkt lokacja do wystąpienia usługi Azure SQL Database Managed ze środowiska lokalnego

Ten przewodnik Szybki Start pokazano, jak nawiązać połączenie z wystąpieniem zarządzanym usługi Azure SQL Database przy użyciu [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) z komputera klienckiego w środowisku lokalnym za pośrednictwem połączenia punkt lokacja. Aby uzyskać informacje na temat połączeń typu punkt lokacja, zobacz [sieci VPN typu punkt-lokacja — informacje](../vpn-gateway/point-to-site-about.md)

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start:

- Wykorzystuje początkowe zasoby utworzone w przewodniku Szybki start: [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).
- Wymaga środowiska PowerShell 5.1 i programu Azure PowerShell 5.4.2 lub nowszej komputer kliencki w środowisku lokalnym.
- Wymaga najnowszej wersji [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) na komputerze klienckim w środowisku lokalnym

## <a name="attach-a-vpn-gateway-to-your-managed-instance-virtual-network"></a>Dołącz bramy sieci VPN do sieci wirtualnej wystąpienia zarządzanego

1. Otwórz program Powershell na komputerze klienckim w środowisku lokalnym.
2. Skopiuj i wklej ten skrypt programu PowerShell. Ten skrypt dołącza bramy sieci VPN do sieci wirtualnej wystąpienia zarządzanego, który został utworzony w [utworzysz wystąpienie zarządzane](sql-database-managed-instance-get-started.md) Szybki Start. Ten skrypt wykonuje następujące trzy kroki:

   - Tworzy i instalowania certyfikatów na komputerze klienckim
   - Oblicza przyszłych zakres adresów IP podsieci bramy sieci VPN
   - Tworzy podsieci GatewaySubnet
   - Służy do wdrażania szablonu usługi Azure Resource Manager, który dołącza bramy sieci VPN do podsieci sieci VPN

     ```powershell
     $scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/attach-vpn-gateway'

     $parameters = @{
       subscriptionId = '<subscriptionId>'
       resourceGroupName = '<resourceGroupName>'
       virtualNetworkName = '<virtualNetworkName>'
       certificateNamePrefix  = '<certificateNamePrefix>'
       }

     Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/attachVPNGateway.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters, $scriptUrlBase
     ```

3. Podaj żądane parametry w skrypcie programu PowerShell. Wartości `<subscriptionId>`, `<resourceGroup>` i `<virtualNetworkName>` powinna być zgodna z tymi, które są używane w [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md) Szybki Start. Wartość `<certificateNamePrefix>` może być ciągiem wybranych przez użytkownika.

4. Uruchom skrypt programu PowerShell.

## <a name="create-a-vpn-connection-to-your-managed-instance"></a>Tworzenie połączenia sieci VPN do wystąpienia zarządzanego

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Otwórz grupę zasobów, w którym utworzono bramę sieci wirtualnej, a następnie otwórz zasobu bramy sieci wirtualnej.

    ![zasobu bramy sieci wirtualnej](./media/sql-database-managed-instance-configure-p2s/vnet-gateway.png)  

3. Kliknij przycisk **Point-to-site configuration** a następnie kliknij przycisk **klienta VPN Pobierz**.

    ![Pobieranie klienta VPN](./media/sql-database-managed-instance-configure-p2s/download-vpn-client.png)  
4. Wyodrębnij pliki z archiwum zip, a następnie otwórz wyodrębniony folder.
5. Przejdź do folderu WindowsAmd64 i Otwórz **VpnClientSetupAmd64.exe** pliku.
6. Jeśli zostanie wyświetlony **Windows chroniony komputer PC** , kliknij przycisk **więcej informacji o** a następnie kliknij przycisk **Uruchom mimo to**.

    ![Zainstaluj klienta sieci VPN](./media/sql-database-managed-instance-configure-p2s/vpn-client-defender.png)\
7. Kliknij przycisk **tak** w oknie Kontrola konta użytkownika, aby kontynuować.
8. W oknie dialogowym MyNewVNet kliknij **tak** do zainstalowania klienta sieci Vpn w taki sposób, aby uzyskać MyNewVNet.

## <a name="connect-to-the-vpn-connection"></a>Nawiązywanie połączenia z siecią VPN

1. Przejdź do połączeń sieci VPN na komputerze klienckim, a następnie kliknij przycisk **MyNewVNet** nawiązać połączenie z tą siecią wirtualną.

    ![Połączenie VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection.png)  
2. Kliknij przycisk **Połącz**.
3. W oknie dialogowym MyNewVNet kliknij **Connect**.

    ![Połączenie VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection2.png)  
4. Po wyświetleniu tego Menedżera połączeń potrzeb podniesionych uprawnień, aby zaktualizować tabeli tras, kliknij przycisk **Kontynuuj**.
5. Kliknij przycisk **tak** w oknie Kontrola konta użytkownika, aby kontynuować.

    ![Połączenie VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection-succeeded.png)  

   Ustanowieniu połączenia sieci VPN do sieci wirtualnej wystąpienia zarządzanego.

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Nawiązywanie połączenia z wystąpieniem zarządzanym za pomocą programu SSMS

1. Na komputerze klienckim w środowisku lokalnym Otwórz program SQL Server Management Studio (SSMS).
2. W **Połącz z serwerem** okna dialogowego wprowadź w pełni kwalifikowaną **nazwy hosta** wystąpienia zarządzanego w **nazwy serwera** wybierz opcję **programu SQL Server Uwierzytelnianie**login i hasło, a następnie kliknij przycisk **Connect**.

    ![nawiązywanie połączenia w programie ssms](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

Po nawiązaniu połączenia możesz wyświetlić systemowe bazy danych oraz bazy danych użytkownika w węźle Bazy danych, a także różne obiekty w węzłach Zabezpieczenia, Obiekty serwera, Replikacja, Zarządzanie, Agent programu SQL Server oraz Profiler systemu XEvent.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać szybki start przedstawiający sposób nawiązywania połączeń z maszyną wirtualną platformy Azure, zobacz [Konfigurowanie połączenia typu punkt lokacja](sql-database-managed-instance-configure-p2s.md)
- Aby uzyskać omówienie opcji połączenia dla aplikacji, zobacz artykuł [Connect your applications to Managed Instance](sql-database-managed-instance-connect-app.md) (Łączenie aplikacji z wystąpieniem zarządzanym).
- Aby przywrócić istniejącą bazę danych programu SQL ze środowiska lokalnego do wystąpienia zarządzanego, możesz użyć [usługi Azure Database Migration Service (DMS) na potrzeby migracji](../dms/tutorial-sql-server-to-managed-instance.md) lub [polecenia T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) w celu przywrócenia z pliku kopii zapasowej bazy danych.
