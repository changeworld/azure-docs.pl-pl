---
title: Wymagania dotyczące serwera konfiguracji dotyczące odzyskiwania po awarii oprogramowania VMware na platformę Azure przy użyciu Azure Site Recovery | Microsoft Docs
description: W tym artykule opisano obsługę i wymagania dotyczące wdrażania serwera konfiguracji na potrzeby odzyskiwania po awarii oprogramowania VMware na platformie Azure przy użyciu Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 86fa817128dc89eb97bee18f4f8a6de1f650c265
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814303"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Wymagania dotyczące serwera konfiguracji dotyczące odzyskiwania po awarii oprogramowania VMware na platformę Azure

Lokalny serwer konfiguracji jest wdrażany w przypadku używania [Azure Site Recovery](site-recovery-overview.md) na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure.

- Serwer konfiguracji koordynuje komunikację między lokalnym programem VMware i platformą Azure. Umożliwia również zarządzanie replikacją danych.
- [Dowiedz się więcej](vmware-azure-architecture.md) o składnikach i procesach serwera konfiguracji.

## <a name="configuration-server-deployment"></a>Wdrożenie serwera konfiguracji

W przypadku odzyskiwania po awarii maszyn wirtualnych VMware na platformę Azure należy wdrożyć serwer konfiguracji jako maszynę wirtualną VMware.

- Site Recovery udostępnia szablon komórki jajowe pobrany z Azure Portal i zaimportowania go do vCenter Server w celu skonfigurowania maszyny wirtualnej serwera konfiguracji.
- Podczas wdrażania serwera konfiguracji przy użyciu szablonu komórki jajowe, maszyna wirtualna automatycznie spełnia wymagania wymienione w tym artykule.
- Zdecydowanie zalecamy skonfigurowanie serwera konfiguracji przy użyciu szablonu komórki jajowe. Jeśli jednak konfigurujesz odzyskiwanie po awarii dla maszyn wirtualnych VMware i nie można użyć szablonu komórki jajowe, możesz wdrożyć serwer konfiguracji, korzystając z [poniższych instrukcji](physical-azure-set-up-source.md).
- Jeśli wdrażasz serwer konfiguracji na potrzeby odzyskiwania po awarii lokalnych maszyn fizycznych na platformie Azure, postępuj zgodnie z instrukcjami w [tym artykule](physical-azure-set-up-source.md). 


## <a name="hardware-requirements"></a>Wymagania sprzętowe

**Składnik** | **Wymaganie** 
--- | ---
Rdzenie procesora CPU | 8 
Pamięć RAM | 16 GB
Liczba dysków | 3, w tym dysk systemu operacyjnego, dysk pamięci podręcznej serwera przetwarzania i dysk przechowywania na potrzeby powrotu po awarii 
Wolne miejsce na dysku (pamięć podręczna serwera przetwarzania) | 600 GB
Wolne miejsce na dysku (dysk przechowywania) | 600 GB

## <a name="software-requirements"></a>Wymagania dotyczące oprogramowania

**Składnik** | **Wymaganie** 
--- | ---
System operacyjny | Windows Server 2012 R2 <br> Windows Server 2016
Ustawienia regionalne systemu operacyjnego | Angielski (en-us)
Role systemu Windows Server | Nie należy włączać tych ról: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Zasady grupy | Nie włączaj tych zasad grupy: <br> -Zapobiegaj dostępowi do wiersza polecenia. <br> — Uniemożliwia dostęp do narzędzi do edytowania rejestru. <br> — Logika zaufania dla plików załączników. <br> — Włącz wykonywanie skryptu. <br> [Dowiedz się więcej](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Brak istniejącej domyślnej witryny sieci Web <br> — Żadna istniejąca witryna sieci Web/aplikacja nie nasłuchuje na porcie 443 <br>-Włącz [uwierzytelnianie anonimowe](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Włącz ustawienie [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 

## <a name="network-requirements"></a>Wymagania dotyczące sieci

**Składnik** | **Wymaganie** 
--- | --- 
Typ adresu IP | Static 
Dostęp do Internetu | Serwer musi mieć dostęp do tych adresów URL (bezpośrednio lub za pośrednictwem serwera proxy): <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> -https:\//Management.Azure.com <br> -*. services.visualstudio.com <br> - time.nist.gov <br> - time.windows.com <br> OVF wymaga również dostępu do następujących adresów URL: <br> -https:\//login.microsoftonline.com <br> -https:\//Secure.aadcdn.microsoftonline-p.com <br> -https:\//login.Live.com  <br> -https:\//auth.gfx.MS <br> -https:\//Graph.Windows.NET <br> -https:\//login.Windows.NET <br> -https:\//www.Live.com <br> -https:\//www.Microsoft.com <br> - https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi 
Porty | 443 (organizowanie kanału sterowania)<br>9443 (transport danych) 
Typ karty sieciowej | VMXNET3 (Jeśli serwer konfiguracji jest maszyną wirtualną VMware)

## <a name="required-software"></a>Wymagane oprogramowanie

**Składnik** | **Wymaganie** 
--- | ---
VMware vSphere PowerCLI | Należy zainstalować [PowerCLI w wersji 6,0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) , jeśli serwer konfiguracji jest uruchomiony na maszynie wirtualnej VMware.
MYSQL | Należy zainstalować MySQL. Można zainstalować go ręcznie lub Site Recovery może go zainstalować.

## <a name="sizing-and-capacity-requirements"></a>Wymagania dotyczące wielkości i pojemności

Poniższa tabela zawiera podsumowanie wymagań dotyczących pojemności dla serwera konfiguracji. W przypadku replikowania wielu maszyn wirtualnych VMware należy zapoznać się z [zagadnieniami dotyczącymi planowania pojemności](site-recovery-plan-capacity-vmware.md)i uruchomić narzędzie [planista wdrażania usługi Azure Site Recovery](site-recovery-deployment-planner.md) na potrzeby replikacji oprogramowania VMware. Przeczytaj 

**Składnik** | **Wymaganie** 
--- | ---

| **CPU** | **Pamięć** | **Dysk pamięci podręcznej** | **Szybkość zmian danych** | **Zreplikowane maszyny** |
| --- | --- | --- | --- | --- |
| 8 procesorów wirtualnych vCPU<br/><br/> 2 gniazda * 4 rdzenie \@ 2,5 GHz | 16 GB | 300 GB | 500 GB lub mniej | Do 100 maszyn |
| 12 procesorów wirtualnych vCPU<br/><br/> 2 SOCKS * 6 rdzeni \@ 2,5 GHz | 18 GB | 600 GB | 500 GB — 1 TB | 100 do 150 maszyn |
| 16 procesorów wirtualnych vCPU<br/><br/> 2 SOCKS * 8 rdzeni \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150-200 maszyn | 



## <a name="next-steps"></a>Następne kroki
Skonfiguruj odzyskiwanie po awarii [maszyn wirtualnych VMware](vmware-azure-tutorial.md) na platformie Azure.
