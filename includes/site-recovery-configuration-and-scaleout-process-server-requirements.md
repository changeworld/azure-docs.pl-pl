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
ms.openlocfilehash: 1aaec104e9130eeef723c6505e04e3317271566b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234263"
---
**Wymagania dotyczące konfiguracji i przetwarzania serwera**


## <a name="hardware-requirements"></a>Wymagania sprzętowe

**Składnik** | **Wymaganie** 
--- | ---
Rdzenie procesora CPU | 8 
Pamięć RAM | 16 GB
Liczba dysków | 3, w tym dysk systemu operacyjnego, dysk pamięci podręcznej serwera przetwarzania i dysk przechowywania po awarii 
Wolne miejsce na dysku (pamięć podręczna serwera przetwarzania) | 600 GB
Wolne miejsce na dysku (dysk retencyjny) | 600 GB
 | 

## <a name="software-requirements"></a>Wymagania dotyczące oprogramowania

**Składnik** | **Wymaganie** 
--- | ---
System operacyjny | Windows Server 2012 R2 <br> Windows Server 2016
Ustawienia regionalne systemu operacyjnego | Angielski (en-*)
Role systemu Windows Server | Nie włączaj tych ról: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Zasady grupy | Nie włączaj tych zasad grupy: <br> - Uniemożliwić dostęp do wiersza polecenia. <br> - Uniemożliwić dostęp do narzędzi do edycji rejestru. <br> - Logika zaufania dla załączników plików. <br> - Włącz wykonywanie skryptów. <br> [Dowiedz się więcej](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Brak istniejącej wcześniej domyślnej strony internetowej <br> - Brak istniejącej strony internetowej / nasłuchiwania aplikacji na porcie 443 <br>- Włącz [uwierzytelnianie anonimowe](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Włącz ustawienie [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 
FIPS (Federalne Standardy Przetwarzania Informacji) | Nie włączaj trybu FIPS
|

## <a name="network-requirements"></a>Wymagania dotyczące sieci

**Składnik** | **Wymaganie** 
--- | --- 
Typ adresu IP | Statyczny 
Porty | 443 (organizowanie kanału sterowania)<br>9443 (transport danych) 
Typ karty sieciowej | VMXNET3 (jeśli serwer konfiguracji jest VMware VMM)
 |
**Dostęp do Internetu** (serwer potrzebuje dostępu do następujących adresów URL, bezpośrednio lub za pośrednictwem serwera proxy):|
\*.backup.windowsazure.com | Służy do replikowanego transferu i koordynacji danych
\*.store.core.windows.net | Służy do replikowanego transferu i koordynacji danych
\*.blob.core.windows.net | Służy do uzyskiwania dostępu do konta magazynu, które przechowuje replikowane dane
\*.hypervrecoverymanager.windowsazure.com | Używane do operacji zarządzania replikacją i koordynacji
https:\//management.azure.com | Używane do operacji zarządzania replikacją i koordynacji 
*.services.visualstudio.com | Używane do celów telemetrycznych (opcjonalnie)
time.nist.gov | Służy do sprawdzania synchronizacji czasu między czasem systemowym a globalnym
time.windows.com | Służy do sprawdzania synchronizacji czasu między czasem systemowym a globalnym
| <ul> <li> https:\//login.microsoftonline.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https:\//login.live.com </li><li> https:\//graph.windows.net </li><li> https:\//login.windows.net </li><li> https:\//www.live.com </li><li> https:\//www.microsoft.com </li></ul> | Konfiguracja OVF potrzebuje dostępu do tych adresów URL. Są one używane do kontroli dostępu i zarządzania tożsamościami przez usługę Azure Active Directory.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | Aby zakończyć pobieranie MySQL. </br> W kilku regionach pobieranie może zostać przekierowane do adresu URL sieci CDN. Upewnij się, że adres URL sieci CDN jest również na białej liście, jeśli to konieczne.
|

## <a name="required-software"></a>Wymagane oprogramowanie

**Składnik** | **Wymaganie** 
--- | ---
VMware vSphere PowerCLI | [PowerCLI w wersji 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) powinien być zainstalowany, jeśli serwer konfiguracji jest uruchomiony na maszynie wirtualnej VMware.
Mysql | MySQL powinien być zainstalowany. Można zainstalować ręcznie lub Site Recovery można go zainstalować. (Więcej informacji można znaleźć w [celu skonfigurowania ustawień)](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings)
|

## <a name="sizing-and-capacity-requirements"></a>Wymagania dotyczące rozmiaru i pojemności

W poniższej tabeli podsumowano wymagania dotyczące pojemności serwera konfiguracji. Jeśli replikujesz wiele maszyn wirtualnych VMware, zapoznaj [się z zagadnieniami planowania pojemności](../articles/site-recovery/site-recovery-plan-capacity-vmware.md) i uruchom narzędzie Azure Site Recovery Deployment [Planner](../articles/site-recovery/site-recovery-deployment-planner.md).


**Procesora** | **Pamięci** | **Dysk pamięci podręcznej** | **Szybkość zmiany danych** | **Zreplikowane maszyny**
--- | --- | --- | --- | ---
8 procesorów wirtualnych<br/><br/> 2 gniazda * 4 \@ rdzenie 2,5 GHz | 16 GB | 300 GB | 500 GB lub mniej | < 100 maszyn
12 procesorów wirtualnych<br/><br/> 2 skarpetki * \@ 6 rdzeni 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | Od 100 do 150 maszyn
16 procesorów wirtualnych<br/><br/> 2 skarpetki * \@ 8 rdzeni 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 -200 maszyn
|

