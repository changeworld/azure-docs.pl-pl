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
ms.reviewer: sstein, carlrab, bonova, jovanpop
manager: craigg
ms.date: 03/13/2019
ms.openlocfilehash: 9ca9506ad9ca826349b2815e504e9ba8bf976faa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60702071"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-an-azure-sql-database-managed-instance-from-on-premises"></a>Szybki start: Konfigurowanie połączenia typu punkt lokacja do wystąpienia usługi Azure SQL Database Managed ze środowiska lokalnego

Ten przewodnik Szybki Start pokazano, jak nawiązać połączenie z wystąpieniem zarządzanym usługi Azure SQL Database przy użyciu [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) z komputera klienckiego w środowisku lokalnym za pośrednictwem połączenia punkt lokacja. Aby uzyskać informacje na temat połączeń typu punkt lokacja, zobacz [sieci VPN typu punkt-lokacja — informacje](../vpn-gateway/point-to-site-about.md)

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start:

- Używa zasobów utworzonych [utworzysz wystąpienie zarządzane](sql-database-managed-instance-get-started.md) jako punktu początkowego.
- Wymaga środowiska PowerShell 5.1 i AZ PowerShell 1.4.0 lub nowszy na komputerze klienckim w środowisku lokalnym. Jeśli to konieczne, zapoznaj się z instrukcjami dla [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module).
- Wymaga najnowszej wersji [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) na komputerze klienckim w środowisku lokalnym.

## <a name="attach-a-vpn-gateway-to-your-managed-instance-virtual-network"></a>Dołącz bramy sieci VPN do sieci wirtualnej wystąpienia zarządzanego

1. Otwórz program PowerShell na komputerze klienckim w środowisku lokalnym.

2. Skopiuj ten skrypt programu PowerShell. Ten skrypt dołącza bramy sieci VPN do sieci wirtualnej wystąpienia zarządzanego, który został utworzony w [utworzysz wystąpienie zarządzane](sql-database-managed-instance-get-started.md) Szybki Start. Ten skrypt wykonuje następujące czynności:

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

     Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/attachVPNGatewayAz.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters, $scriptUrlBase
     ```

     > [!IMPORTANT]
     > Aby użyć modułu Azure PowerShell Resource Manager, a nie modułu Az, należy użyć następującego polecenia cmdlet: `attachVPNGateway.ps1` zamiast `attachVPNGatewayAz.ps1` polecenia cmdlet.

3. Wklej skrypt w oknie programu PowerShell i podaj wymagane parametry. Wartości `<subscriptionId>`, `<resourceGroup>`, i `<virtualNetworkName>` powinna być zgodna z tymi, które były używane [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md) Szybki Start. Wartość `<certificateNamePrefix>` może być ciągiem wybranych przez użytkownika.

4. Uruchom skrypt programu PowerShell.

> [!IMPORTANT]
> Nie należy kontynuować do momentu ukończenia skrypt programu PowerShell.

## <a name="create-a-vpn-connection-to-your-managed-instance"></a>Tworzenie połączenia sieci VPN do wystąpienia zarządzanego

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Otwórz grupę zasobów, w którym utworzono bramę sieci wirtualnej, a następnie otwórz zasobu bramy sieci wirtualnej.
3. Wybierz **Point-to-site configuration** , a następnie wybierz **klienta VPN Pobierz**.

    ![Pobieranie klienta VPN](./media/sql-database-managed-instance-configure-p2s/download-vpn-client.png)  
4. Na komputerze klienckim w środowisku lokalnym Wyodrębnij pliki z archiwum zip, a następnie otwórz folder z wyodrębnionych plików.
5. Otwórz "**WindowsAmd64** folder i Otwórz **VpnClientSetupAmd64.exe** pliku.
6. Jeśli zostanie wyświetlony **Windows chroniony komputer PC** , kliknij przycisk **więcej informacji o** a następnie kliknij przycisk **Uruchom mimo to**.

    ![Zainstaluj klienta sieci VPN](./media/sql-database-managed-instance-configure-p2s/vpn-client-defender.png)\
7. W oknie Kontrola konta użytkownika kliknij przycisk **tak** aby kontynuować.
8. W oknie dialogowym odwołujące się do sieci wirtualnej, wybierz **tak** do zainstalowania klienta sieci VPN dla sieci wirtualnej.

## <a name="connect-to-the-vpn-connection"></a>Nawiązywanie połączenia z siecią VPN

1. Przejdź do **VPN** w **sieć i Internet** na swoim komputerze klienckim w środowisku lokalnym i Wybieranie sieci wirtualnej wystąpienia zarządzanego, aby nawiązać połączenie z tą siecią wirtualną. Na poniższej ilustracji sieci wirtualnej o nazwie **MyNewVNet**.

    ![Połączenie VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection.png)  
2. Wybierz przycisk **Połącz**.
3. W oknie dialogowym wybierz **Connect**.

    ![Połączenie VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection2.png)  
4. Po wyświetleniu monitu, że Menedżer połączeń wymaga podniesionych uprawnień, aby zaktualizować tabeli tras, wybierz **Kontynuuj**.
5. Wybierz **tak** w oknie Kontrola konta użytkownika, aby kontynuować.

   Zostaną utworzone połączenie sieci VPN do sieci wirtualnej wystąpienia zarządzanego.

    ![Połączenie VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection-succeeded.png)  

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Nawiązywanie połączenia z wystąpieniem zarządzanym za pomocą programu SSMS

1. Na komputerze klienckim w środowisku lokalnym Otwórz program SQL Server Management Studio (SSMS).
2. W **Połącz z serwerem** okna dialogowego wprowadź w pełni kwalifikowaną **nazwy hosta** wystąpienia zarządzanego w **nazwy serwera** pole.
3. Wybierz **uwierzytelniania programu SQL Server**, podaj nazwę użytkownika i hasło, a następnie wybierz **Connect**.

    ![nawiązywanie połączenia w programie ssms](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

Po nawiązaniu połączenia, mogą wyświetlać systemu i użytkownika bazy danych, w węźle bazy danych. Można również wyświetlić różne obiekty w węzłach zabezpieczenia, obiekty serwera, replikacji, zarządzania, Agent programu SQL Server i Profiler systemu XEvent.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać szybki start przedstawiający sposób nawiązywania połączeń z maszyną wirtualną platformy Azure, zobacz [Konfigurowanie połączenia typu punkt lokacja](sql-database-managed-instance-configure-p2s.md).
- Aby uzyskać omówienie opcji połączenia dla aplikacji, zobacz artykuł [Łączenie aplikacji z wystąpieniem zarządzanym](sql-database-managed-instance-connect-app.md).
- Aby przywrócić istniejącą bazę danych programu SQL Server ze środowiska lokalnego do wystąpienia zarządzanego, można użyć [usługi Azure Database Migration Service (DMS) do migracji](../dms/tutorial-sql-server-to-managed-instance.md) lub [polecenia języka T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) przywracania z Plik kopii zapasowej bazy danych.
