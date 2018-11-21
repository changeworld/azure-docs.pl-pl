---
title: Nie można połączyć z usługą Azure Virtual Machines, ponieważ portu RDP nie jest włączone w sieciowych grupach zabezpieczeń | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązać problem, w którym RDP zakończy się niepowodzeniem z powodu konfiguracji sieciowej grupy zabezpieczeń w witrynie Azure portal | Dokumentacja firmy Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 89af30533e10df0968b60039d7ea15886e89bc25
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2018
ms.locfileid: "52266928"
---
#  <a name="cannot-rdp-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>Nie można nawiązać połączenia RDP z maszyny Wirtualnej, ponieważ portu RDP nie jest włączona w sieciowej grupie zabezpieczeń

W tym artykule pokazano, jak rozwiązać problem, w którym nie można dołączyć do usługi Azure Windows Virtual Machines (VMs), ponieważ port nie jest włączone w sieciowej grupie zabezpieczeń.


> [!NOTE] 
> Platforma Azure ma dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Resource Manager i model klasyczny](../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule opisano, przy użyciu modelu wdrażania usługi Resource Manager, w którym firma Microsoft zaleca używanie w przypadku nowych wdrożeń zamiast klasycznego modelu wdrażania. 

## <a name="symptom"></a>Objaw

Nie można wprowadzać protokołu RDP (Remote Desktop) połączenie z maszyną wirtualną na platformie Azure z powodu portu RDP nie jest otwarty w sieciowej grupie zabezpieczeń.

## <a name="solution"></a>Rozwiązanie 

Podczas tworzenia nowej maszyny Wirtualnej cały ruch z Internetu jest blokowany domyślnie. 

Aby włączyć port protokołu RDP w sieciowej grupie zabezpieczeń, wykonaj następujące kroki:
1. Zaloguj się w [portalu Azure](https://portal.azure.com).
2. W **maszyn wirtualnych**, maszynę Wirtualną, która ma problem. 
3. W **ustawienia**, wybierz opcję **sieć**. 
4. W **reguły portów wejściowych**, sprawdź, jeśli port dla protokołu RDP jest poprawnie ustawiony. Poniżej znajduje się przykład konfiguracji. 

    **Priorytet**: 300 </br>
    **Port**: 3389 </br>
    **Nazwa**: Port_3389 </br>
    **Port**: 3389 </br>
    **Protokół**: TCP </br>
    **Źródło**: wszystkie </br>
    **Miejsca docelowe**: wszystkie </br>
    **Akcja**: Zezwalaj </br>

W określeniu źródłowy adres IP, to ustawienie zezwala na ruch tylko z określonych adresów IP lub zakres adresów IP, nawiązać połączenia z maszyną Wirtualną. Upewnij się, że komputer, na którym są używane do inicjowania sesji protokołu RDP znajduje się w zakresie.

Aby uzyskać więcej informacji na temat sieciowej grupy zabezpieczeń, zobacz [sieciowej grupy zabezpieczeń](../../virtual-network/security-overview.md).

> [!NOTE]
> Port 3389 protokołu RDP jest uwidaczniany w Internecie. Jest to zalecane tylko do testowania. W środowiskach produkcyjnych zalecamy używanie sieci VPN lub połączenia prywatnego.

## <a name="next-steps"></a>Kolejne kroki

Jeżeli port protokołu RDP jest już włączona w sieciowej grupie zabezpieczeń, zobacz [rozwiązać błąd ogólnego protokołu RDP na maszynie Wirtualnej platformy Azure](./troubleshoot-rdp-general-error.md).



