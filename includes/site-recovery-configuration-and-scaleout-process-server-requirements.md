---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/10/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 371cbcc50b574f95e8d9ba4efe79058b2b25a8ba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66127724"
---
**Wymagania dotyczące serwera konfiguracji/przetwarzania**

**Składnik** | **Wymaganie** 
--- | ---
**USTAWIENIA SPRZĘTU** | 
Rdzenie procesora CPU | 8 
Pamięć RAM | 16 GB
Liczba dysków | 3, w tym dysku systemu operacyjnego, dysk pamięci podręcznej serwera przetwarzania i dysk przechowywania na potrzeby powrotu po awarii 
Wolne miejsce na dysku (pamięć podręczna serwera przetwarzania) | 600 GB
Wolnego miejsca na dysku (dysk przechowywania) | 600 GB
 | 
**USTAWIENIA OPROGRAMOWANIA** | 
System operacyjny | Windows Server 2012 R2 <br> Windows Server 2016
Ustawienia regionalne systemu operacyjnego | Angielski (en-us)
Role systemu Windows Server | Nie włączaj tych ról: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Zasady grupy | Nie włączaj tych zasad grupy: <br> -Zapobiegaj dostępowi do wiersza polecenia. <br> -Uniemożliwić dostęp do narzędzi edycji rejestru. <br> — Logika zaufania dla plików załączników. <br> -Włącz wykonywanie skryptu. <br> [Dowiedz się więcej](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Brak wcześniej istniejącej domyślnej witryny sieci Web <br> -Brak przeniosła istniejące wcześniej witryny sieci Web/aplikacja nasłuchuje na porcie 443 <br>-Włącz [uwierzytelnianie anonimowe](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Włącz [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) ustawienie 
| 
**USTAWIENIA SIECI** | 
Typ adresu IP | Static 
Porty | 443 (organizowanie kanału sterowania)<br>9443 (transport danych) 
Typ karty NIC | Innego VMXNET3 (Jeśli na serwerze konfiguracji maszyny Wirtualnej VMware)
 |
**Dostęp do Internetu** (serwer musi mieć dostęp do następujących adresów URL — bezpośrednio lub za pośrednictwem serwera proxy):|
\*.backup.windowsazure.com | Używany do transferowania i koordynacji replikowanych danych.
\*.store.core.windows.net | Używany do transferowania i koordynacji replikowanych danych.
\*.blob.core.windows.net | Umożliwia dostęp do konta magazynu przechowującego zreplikowane dane
\*.hypervrecoverymanager.windowsazure.com | Służy do operacji zarządzania replikacją i koordynacji
https:\//management.azure.com | Służy do operacji zarządzania replikacją i koordynacji 
*.services.visualstudio.com | Używane do celów danych telemetrycznych (jest to opcjonalne)
time.nist.gov | Służą do sprawdzania synchronizacji czasu między systemem i czasem globalnym.
time.windows.com | Służą do sprawdzania synchronizacji czasu między systemem i czasem globalnym.
| <ul> <li> https:\//login.microsoftonline.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https:\//login.live.com </li><li> https:\//graph.windows.net </li><li> https:\//login.windows.net </li><li> https:\//www.live.com </li><li> https:\//www.microsoft.com </li></ul> | Konfigurowanie pakietu OVF musi mieć dostęp do tych adresów URL. Są one używane do kontrolowania dostępu i tożsamości zarządzania przez usługę Azure Active Directory
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Aby zakończyć pobieranie MySQL
|
**OPROGRAMOWANIE DO ZAINSTALOWANIA** | 
Program VMware vSphere PowerCLI | [Interfejs PowerCLI w wersji 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) powinien być zainstalowany, jeśli serwer konfiguracji jest uruchomiony na maszynie Wirtualnej VMware.
MYSQL | Powinien być zainstalowany MySQL. Można zainstalować ręcznie lub Usługa Site Recovery można go zainstalować. (Zobacz [konfigurowania ustawień](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings) Aby uzyskać więcej informacji)

**Określania rozmiaru wymagania dotyczące serwera konfiguracji/przetwarzania**

**CPU** | **Pamięć** | **Dysk pamięci podręcznej** | **Współczynnik zmian danych** | **Zreplikowane maszyny**
--- | --- | --- | --- | ---
8 wirtualnych procesorów CPU<br/><br/> gniazda 2 * 4 rdzenie \@ 2,5 GHz | 16GB | 300 GB | 500 GB lub mniej | < 100 maszyn
12 procesorów wirtualnych Vcpu<br/><br/> 2 socks * 6 rdzeni \@ 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100-150 maszyn
16 procesorów wirtualnych Vcpu<br/><br/> 2 socks * 8 rdzeni \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | -150 200 maszyn

