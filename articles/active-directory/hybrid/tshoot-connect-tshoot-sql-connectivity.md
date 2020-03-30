---
title: 'Usługa Azure AD Connect: rozwiązywanie problemów z łącznością SQL | Dokumenty firmy Microsoft'
description: W tym artykule wyjaśniono, jak rozwiązywać problemy z łącznością SQL, które występują z usługą Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cde94dce13eeb7536f72fb0dcd937265960c7314
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70842691"
---
# <a name="troubleshoot-sql-connectivity-issues-with-azure-ad-connect"></a>Troubleshoot SQL connectivity issues with Azure AD Connect (Rozwiązywanie problemów z połączeniami SQL w programie Azure AD Connect)
W tym artykule wyjaśniono, jak rozwiązywać problemy z łącznością między usługą Azure AD Connect a programem SQL Server. 

Poniższy zrzut ekranu przedstawia typowy błąd, jeśli nie można odnaleźć programu SQL Server.

![Błąd SQL](./media/tshoot-connect-tshoot-sql-connectivity/sql1.png)

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
Otwieranie okna programu PowerShell i importowanie modułu programu ADSyncTools Powershell

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\Tools\AdSyncTools.psm1" 
```

>[!NOTE]
>Install-Module wymaga aktualizacji do [programu PowerShell 5.0 (WMF 5.0)](https://www.microsoft.com/download/details.aspx?id=50395) lub nowszej;  
Lub zainstaluj [pakiety Moduły Programu PowerShell Preview - marzec 2016 dla programu PowerShell 3.0/4.0](/powershell/module/PackageManagement) 

- **Pokaż wszystkie polecenia:**`Get-Command -Module AdSyncTools` 
- **Wykonywanie funkcji powershell:** `Connect-ADSyncDatabase` z następującymi parametrami
    - Serwera. Nazwa programu SQL Server.
    - Wystąpienie. (Opcjonalnie) Nazwa wystąpienia programu SQL Server i opcjonalnie numer portu, którego chcesz użyć. Nie należy określać tego parametru, aby użyć wystąpienia domyślnego.
    - Nazwę użytkownika. (Opcjonalnie) Konto użytkownika, z które ma się połączyć. Jeśli pozostanie pusty, zostanie użyty aktualnie zalogowany użytkownik. Jeśli łączysz się ze zdalnym programem SQL Server, powinno to być konto usługi niestandardowej utworzone dla łączności SQL usługi Azure ADConnect. Usługa Azure AD Connect używa konta usługi synchronizacji usługi Azure AD Connect do uwierzytelniania na zdalnym serwerze SQL.
    - Hasło. (Opcjonalnie) Hasło do podanej nazwy użytkownika.

Ta funkcja programu PowerShell spróbuje powiązać z określonym programem SQL Server i wystąpieniem przy użyciu poświadczeń przekazanych w programie OR, użyj poświadczeń bieżącego użytkownika. Jeśli nie można odnaleźć programu SQL Server, skrypt spróbuje połączyć się z usługą przeglądarki SQL w celu określenia włączonych protokołów i portów.

Przykład przy użyciu tylko nazwy serwera:
```
PS C:\Program Files\Microsoft Azure Active Directory Connect\Tools> import-module .\AdSyncTools.psm1

PS C:\Program Files\Microsoft Azure Active Directory Connect\Tools> Connect-AdSyncDatabase -Server SQL1
Resolving server address : SQL1
    InterNetworkV6 : fe80::6c90:a995:3e70:ef74%17
    InterNetworkV6 : 2001:4898:e0:66:6c90:a995:3e70:ef74
    InterNetwork : 10.91.26.143

Attempting to connect to SQL1 using a TCP binding for the default instance.
   Data Source=tcp:SQL1\;Integrated Security=True.ConnectionString
   Successfully connected.


StatisticsEnabled                : False
AccessToken                      : 
ConnectionString                 : Data Source=tcp:SQL1\;Integrated Security=True
ConnectionTimeout                : 15
Database                         : master
DataSource                       : tcp:SQL1\
PacketSize                       : 8000
ClientConnectionId               : 23e06ef2-0a38-4f5f-9291-da931de40375
ServerVersion                    : 13.00.4474
State                            : Open
WorkstationId                    : SQL1
Credential                       : 
FireInfoMessageEventOnUserErrors : False
Site                             : 
Container                        : 




PS C:\Program Files\Microsoft Azure Active Directory Connect\Tools> 
```

Przykład przy użyciu numeru wystąpienia i portu, które nie istnieją:

```
PS C:\Program Files\Microsoft Azure Active Directory Connect\tools> Connect-AdSyncDatabase -Server SQL1 -Instance "INSTANCE1"
Resolving server address : SQL1
    InterNetworkV6 : fe80::6c90:a995:3e70:ef74%17
    InterNetworkV6 : 2001:4898:e0:66:6c90:a995:3e70:ef74
    InterNetwork : 10.91.26.143

Attempting to connect to SQL1\INSTANCE1 using a TCP binding.
   Data Source=tcp:SQL1\INSTANCE1;Integrated Security=True.ConnectionString
Connect-AdSyncDatabase : Unable to connect using a TCP binding.  A network-related or instance-specific error occurred while establishing a connection 
to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow 
remote connections. (provider: SQL Network Interfaces, error: 26 - Error Locating Server/Instance Specified)
At line:1 char:1
+ Connect-AdSyncDatabase -Server SQL1 -Instance "INSTANCE1"
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ConnectionError: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,Connect-AdSyncDatabase
 

TROUBLESHOOTING: Attempting to query the SQL Server Browser service configuration on SQL1.
Get-ADSyncSQLBrowserInstances : Unable to read the SQL Server Browser configuration. An existing connection was forcibly closed by the remote host. 
Ensure port 1434 (UDP) is open on SQL1 and the SQL Server Browser service is running. 
At C:\Program Files\Microsoft Azure Active Directory Connect\tools\AdSyncTools.psm1:1717 char:18
+     $instances = Get-ADSyncSQLBrowserInstances $Server
+                  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ConnectionError: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,Get-ADSyncSQLBrowserInstances
 


WHAT TO TRY NEXT:

Each SQL instance must be bound to an explicit static TCP port and paired with an inbound firewall rule on SQL1 to allow connection. Enable the SQL Se
rver Browser service temporarily on the SQL server and run this cmdLet again to further troubleshoot the issue. Alternatively use the SQL Server Configur
ation Manager on SQL1 to verify the instance name and TCP/IP port assignment manually. 

You must specify both the instance name and the port to connect when the SQL Server Browser service is not running. An inbound firewall rule on SQL1 is required for the associated port.
    Example: 'MySQLInstance,1234' where 1234 has a matching firewall rule.

PS C:\Program Files\Microsoft Azure Active Directory Connect\tools> 
PS C:\Program Files\Microsoft Azure Active Directory Connect\tools> Connect-AdSyncDatabase -Server SQL1 -Instance "INSTANCE1,99"
Resolving server address : SQL1
    InterNetworkV6 : fe80::6c90:a995:3e70:ef74%17
    InterNetworkV6 : 2001:4898:e0:66:6c90:a995:3e70:ef74
    InterNetwork : 10.91.26.143

Attempting to connect to SQL1\INSTANCE1,99 using a TCP binding.
   Data Source=tcp:SQL1\INSTANCE1,99;Integrated Security=True.ConnectionString
Connect-AdSyncDatabase : Unable to connect using a TCP binding.  A network-related or instance-specific error occurred while establishing a connection 
to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: TCP Provider, error: 0 - The remote computer refused the network connection.)
At line:1 char:1
+ Connect-AdSyncDatabase -Server SQL1 -Instance "INSTANCE1,99"
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ConnectionError: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,Connect-AdSyncDatabase
 

TROUBLESHOOTING: Attempting to query the SQL Server Browser service configuration on SQL1.
SQL browser response contained 2 instances.
Verifying protocol bindings and port connectivity.
MSSQLSERVER     : Enabled - port 1433 is assigned and reachable through the firewall
INSTANCE1       : Blocked - the inbound firewall rule for port 58379 is missing or disabled


WHAT TO TRY NEXT:

Each SQL instance must be bound to an explicit static TCP port and paired with an
inbound firewall rule on SQL1 to allow connection. Review the TcpStatus field
for each instance and take corrective action.


InstanceName : MSSQLSERVER
tcp          : 1433
TcpStatus    : Enabled - port 1433 is assigned and reachable through the firewall

InstanceName : INSTANCE1
tcp          : 58379
TcpStatus    : Blocked - the inbound firewall rule for port 58379 is missing or disabled




PS C:\Program Files\Microsoft Azure Active Directory Connect\tools>  
```

## <a name="next-steps"></a>Następne kroki
- [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
-  [Łączność z usługą Azure AD za pośrednictwem programu Azure AD Connect](tshoot-connect-connectivity.md)
