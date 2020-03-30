---
title: Konfigurowanie P2S — wystąpienie zarządzane
description: Połącz się z wystąpieniem zarządzanym bazy danych SQL azure przy użyciu programu SQL Server Management Studio przy użyciu połączenia typu punkt-lokacja z lokalnego komputera klienckiego.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova, jovanpop
ms.date: 03/13/2019
ms.openlocfilehash: 30b2ba92174996ea2bae34e7553a3258d8ebee27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268888"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-an-azure-sql-database-managed-instance-from-on-premises"></a>Szybki start: konfigurowanie połączenia typu "punkt-lokacja" z wystąpieniem zarządzanym bazy danych SQL platformy Azure z lokalnego

Ten przewodnik Szybki start pokazuje, jak połączyć się z wystąpieniem zarządzanym bazy danych SQL azure przy użyciu [programu SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) z lokalnego komputera klienckiego za pośrednictwem połączenia typu punkt-lokacja. Aby uzyskać informacje o połączeniach typu punkt-lokacja, zobacz [Informacje o sieci VPN typu punkt-lokacja](../vpn-gateway/point-to-site-about.md)

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start:

- Używa zasobów utworzonych [Utwórz wystąpienie zarządzane](sql-database-managed-instance-get-started.md) jako punkt wyjścia.
- Wymaga programów PowerShell 5.1 i AZ PowerShell 1.4.0 lub nowszych na lokalnym komputerze klienckim. W razie potrzeby zapoznaj się z instrukcjami [instalowania modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module).
- Wymaga najnowszej wersji [programu SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) na lokalnym komputerze klienckim.

## <a name="attach-a-vpn-gateway-to-your-managed-instance-virtual-network"></a>Dołączanie bramy sieci VPN do sieci wirtualnej wystąpienia zarządzanego

1. Otwórz program PowerShell na lokalnym komputerze klienckim.

2. Skopiuj ten skrypt programu PowerShell. Ten skrypt dołącza bramę sieci VPN do sieci wirtualnej wystąpienia zarządzanego utworzonego w przewodniku Szybki start [tworzenia wystąpienia zarządzanego.](sql-database-managed-instance-get-started.md) Ten skrypt używa modułu Az programu Azure PowerShell i wykona następujące czynności dla hostów opartych na systemie Windows lub Linux:

   - Tworzy i instaluje certyfikaty na komputerze klienckim
   - Oblicza przyszły zakres adresów IP podsieci bramy sieci VPN
   - Tworzy sieć GatewaySubnet
   - Wdraża szablon usługi Azure Resource Manager, który dołącza bramę sieci VPN do podsieci sieci VPN

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

3. Wklej skrypt w oknie programu PowerShell i podaj wymagane parametry. Wartości dla `<subscriptionId>` `<resourceGroup>`programu `<virtualNetworkName>` , i powinny być zgodne z wartościami używanymi w przewodniku Szybki start [tworzenia wystąpienia zarządzanego.](sql-database-managed-instance-get-started.md) Wartość dla `<certificateNamePrefix>` może być ciąg wyboru.

4. Wykonywanie skryptu programu PowerShell.

> [!IMPORTANT]
> Nie kontynuuj, dopóki skrypt programu PowerShell nie zostanie ukończony.

## <a name="create-a-vpn-connection-to-your-managed-instance"></a>Tworzenie połączenia sieci VPN z wystąpieniem zarządzanym

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Otwórz grupę zasobów, w której utworzono bramę sieci wirtualnej, a następnie otwórz zasób bramy sieci wirtualnej.
3. Wybierz **pozycję Konfiguracja punktu do lokacji,** a następnie wybierz pozycję **Pobierz klienta sieci VPN**.

    ![Pobierz klienta sieci VPN](./media/sql-database-managed-instance-configure-p2s/download-vpn-client.png)  
4. Na lokalnym komputerze klienckim wyodrębnij pliki z pliku zip, a następnie otwórz folder z wyodrębnianych plików.
5. Otwórz folder**WindowsAmd64** i otwórz plik **VpnClientSetupAmd64.exe.**
6. Jeśli zostanie wyświetlony komunikat z systemem **Windows chronionym komputerem,** kliknij pozycję **Więcej informacji,** a następnie kliknij pozycję **Uruchom mimo to**.

    ![Instalowanie klienta sieci VPN](./media/sql-database-managed-instance-configure-p2s/vpn-client-defender.png)\
7. W oknie dialogowym Kontrola konta użytkownika kliknij przycisk **Tak**, aby kontynuować.
8. W oknie dialogowym odwołującym się do sieci wirtualnej wybierz pozycję **Tak,** aby zainstalować klienta sieci VPN dla sieci wirtualnej.

## <a name="connect-to-the-vpn-connection"></a>Łączenie się z połączeniem sieci VPN

1. Przejdź do **sieci VPN** w sieci **& Internet** na komputerze klienckim lokalnym i wybierz sieć wirtualną wystąpienia zarządzanego, aby ustanowić połączenie z tą siecią wirtualną. Na poniższej ilustracji sieci wirtualnej jest nazwany **MyNewVNet**.

    ![Połączenie VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection.png)  
2. Wybierz przycisk **Połącz**.
3. W oknie dialogowym wybierz pozycję **Połącz**.

    ![Połączenie VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection2.png)  
4. Po wyświetleniu monitu, że Menedżer połączeń potrzebuje podwyższonych uprawnień, aby zaktualizować tabelę tras, wybierz pozycję **Kontynuuj**.
5. Wybierz **pozycję Tak** w oknie dialogowym Kontrola konta użytkownika, aby kontynuować.

   Nawiązano połączenie sieci VPN z siecią wirtualną wystąpienia zarządzanego.

    ![Połączenie VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection-succeeded.png)  

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Łączenie się z wystąpieniem zarządzanym za pomocą usługi SSMS

1. Na lokalnym komputerze klienckim otwórz program SQL Server Management Studio (SSMS).
2. W oknie dialogowym **Łączenie z serwerem** wprowadź w pełni kwalifikowaną **nazwę hosta** wystąpienia zarządzanego w polu **Nazwa serwera.**
3. Wybierz **pozycję Uwierzytelnianie programu SQL Server**, podaj nazwę użytkownika i hasło, a następnie wybierz pozycję **Połącz**.

    ![nawiązywanie połączenia w programie ssms](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

Po nawiązaniu połączenia można wyświetlić bazy danych systemu i użytkowników w węźle Bazy danych. Można również wyświetlać różne obiekty w węzłach Zabezpieczenia, Obiekty serwera, Replikacja, Zarządzanie, Agent programu SQL Server i XEvent Profiler.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać przewodnik Szybki start pokazujący, jak połączyć się z maszyny wirtualnej platformy Azure, zobacz [Konfigurowanie połączenia typu punkt-lokacja](sql-database-managed-instance-configure-p2s.md).
- Aby uzyskać omówienie opcji połączenia dla aplikacji, zobacz artykuł [Łączenie aplikacji z wystąpieniem zarządzanym](sql-database-managed-instance-connect-app.md).
- Aby przywrócić istniejącą bazę danych programu SQL Server z lokalnego wystąpienia do wystąpienia zarządzanego, można użyć [usługi migracji bazy danych Azure Database (DMS) do migracji](../dms/tutorial-sql-server-to-managed-instance.md) lub polecenia [T-SQL RESTORE,](sql-database-managed-instance-get-started-restore.md) aby przywrócić z pliku kopii zapasowej bazy danych.
