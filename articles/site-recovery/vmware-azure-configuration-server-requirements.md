---
title: Wymagania dotyczące serwera konfiguracji odzyskiwania po awarii programu VMware na platformę Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano wymagania i pomocy technicznej, podczas wdrażania serwera konfiguracji na potrzeby odzyskiwania po awarii programu VMware na platformę Azure za pomocą usługi Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 94f410b7bd3b7c2eb3d7d6a9316323092010338e
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418336"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Wymagania dotyczące serwera konfiguracji odzyskiwania po awarii programu VMware do platformy Azure

Wdrażanie serwera konfiguracji w środowisku lokalnym, korzystając z [usługi Azure Site Recovery](site-recovery-overview.md) do odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure.

- Konfiguracja współrzędne międzyserwerowej komunikacji lokalnych programu VMware i platformą Azure. Umożliwia także zarządzanie replikacją danych.
- [Dowiedz się więcej](vmware-azure-architecture.md) dotyczące składników serwera konfiguracji i procesów.

## <a name="configuration-server-deployment"></a>Wdrożenie serwera konfiguracji

Do odzyskiwania po awarii maszyn wirtualnych programu VMware do platformy Azure możesz wdrożyć serwer konfiguracji jako Maszynę wirtualną VMware.

- Usługa Site Recovery zawiera szablon OVA możesz pobrać z witryny Azure portal i zaimportować na serwer vCenter, aby skonfigurować serwer konfiguracji maszyny Wirtualnej.
- Podczas wdrażania serwera konfiguracji za pomocą szablonu OVA maszyna wirtualna spełnia wymagania wymienione w tym artykule automatycznie.
- Zdecydowanie zaleca się skonfigurować serwer konfiguracji za pomocą szablonu OVA. Jednak jeśli podczas konfigurowania odzyskiwania po awarii dla maszyn wirtualnych VMware i nie można użyć szablonu OVA, można wdrożyć za pomocą serwera konfiguracji [tymi instrukcjami](physical-azure-set-up-source.md).
- Jeśli wdrażasz serwera konfiguracji na potrzeby odzyskiwania po awarii lokalnych maszyn fizycznych do platformy Azure, postępuj zgodnie z instrukcjami [w tym artykule](physical-azure-set-up-source.md). 


## <a name="hardware-requirements"></a>Wymagania sprzętowe

**Składnik** | **Wymaganie** 
--- | ---
Rdzenie procesora CPU | 8 
Pamięć RAM | 16 GB
Liczba dysków | 3, w tym dysku systemu operacyjnego, dysk pamięci podręcznej serwera przetwarzania i dysk przechowywania na potrzeby powrotu po awarii 
Wolne miejsce na dysku (pamięć podręczna serwera przetwarzania) | 600 GB
Wolnego miejsca na dysku (dysk przechowywania) | 600 GB

## <a name="software-requirements"></a>Wymagania dotyczące oprogramowania

**Składnik** | **Wymaganie** 
--- | ---
System operacyjny | Windows Server 2012 R2 <br> Windows Server 2016
Ustawienia regionalne systemu operacyjnego | Angielski (en-us)
Role systemu Windows Server | Nie włączaj tych ról: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Zasady grupy | Nie włączaj tych zasad grupy: <br> -Zapobiegaj dostępowi do wiersza polecenia. <br> -Uniemożliwić dostęp do narzędzi edycji rejestru. <br> — Logika zaufania dla plików załączników. <br> -Włącz wykonywanie skryptu. <br> [Dowiedz się więcej](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Brak przeniosła istniejące wcześniej domyślnej witryny sieci Web <br> -Brak przeniosła istniejące wcześniej witryny sieci Web/aplikacja nasłuchuje na porcie 443 <br>-Włącz [uwierzytelnianie anonimowe](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Włącz [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) ustawienie 

## <a name="network-requirements"></a>Wymagania dotyczące sieci

**Składnik** | **Wymaganie** 
--- | --- 
Typ adresu IP | Static 
Dostęp do Internetu | Serwer musi mieć dostęp do tych adresów URL (bezpośrednio lub za pośrednictwem serwera proxy): <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> -https:\//management.azure.com <br> - *.services.visualstudio.com <br> - time.nist.gov <br> - time.windows.com <br> OVF musi również mieć dostęp do następujących adresów URL: <br> -https:\//login.microsoftonline.com <br> -https:\//secure.aadcdn.microsoftonline-p.com <br> -https:\//login.live.com  <br> -https:\//auth.gfx.ms <br> - https:\//graph.windows.net <br> -https:\//login.windows.net <br> -https:\//www.live.com <br> -https:\//www.microsoft.com <br> - https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi 
Porty | 443 (organizowanie kanału sterowania)<br>9443 (transport danych) 
Typ karty NIC | Innego VMXNET3 (Jeśli na serwerze konfiguracji maszyny Wirtualnej VMware)

## <a name="required-software"></a>Wymagane oprogramowanie

**Składnik** | **Wymaganie** 
--- | ---
Program VMware vSphere PowerCLI | [Interfejs PowerCLI w wersji 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) powinien być zainstalowany, jeśli serwer konfiguracji jest uruchomiony na maszynie Wirtualnej VMware.
MYSQL | Powinien być zainstalowany MySQL. Można zainstalować ręcznie lub Usługa Site Recovery można go zainstalować.

## <a name="sizing-and-capacity-requirements"></a>Zmiana rozmiaru i wymagania dotyczące pojemności

Poniższa tabela zawiera podsumowanie wymagań w zakresie pojemności dla serwera konfiguracji. Jeśli replikujesz wielu maszyn wirtualnych VMware, należy przejrzeć [zagadnienia dotyczące planowania pojemności](site-recovery-plan-capacity-vmware.md)i uruchom [planista wdrażania usługi Azure Site Recovery](site-recovery-deployment-planner.md) narzędzia dla oprogramowania VMWare replication.read 

**Składnik** | **Wymaganie** 
--- | ---

| **CPU** | **Pamięć** | **Dysk pamięci podręcznej** | **Współczynnik zmian danych** | **Zreplikowane maszyny** |
| --- | --- | --- | --- | --- |
| 8 wirtualnych procesorów CPU<br/><br/> gniazda 2 * 4 rdzenie \@ 2,5 GHz | 16 GB | 300 GB | 500 GB lub mniej | Wartości mniejszej niż 100 maszyn |
| 12 procesorów wirtualnych Vcpu<br/><br/> 2 socks * 6 rdzeni \@ 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100-150 maszyn |
| 16 procesorów wirtualnych Vcpu<br/><br/> 2 socks * 8 rdzeni \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 – 200 maszyn | 



## <a name="next-steps"></a>Kolejne kroki
Konfigurowanie odzyskiwania po awarii [maszyny wirtualne VMware](vmware-azure-tutorial.md) na platformie Azure.
