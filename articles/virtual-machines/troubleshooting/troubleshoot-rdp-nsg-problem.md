---
title: Nie można połączyć z maszyn wirtualnych platformy Azure, ponieważ nie włączono portów protokołu RDP w sieciowej grupie zabezpieczeń | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązać problem, w którym RDP zakończy się niepowodzeniem ze względu na konfigurację sieciową grupę zabezpieczeń w witrynie Azure portal | Dokumentacja firmy Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: c32612c411f275220f549eea79276fa5a7232fd0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60318939"
---
#  <a name="cannot-connect-remotely-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>Nie można nawiązać połączenia zdalnego maszyny Wirtualnej, ponieważ nie włączono port protokołu RDP w sieciowej grupie zabezpieczeń

W tym artykule wyjaśniono, jak rozwiązać problem, w której nie można połączyć z maszyną wirtualną (VM) Azure Windows ponieważ portów protokołu RDP (Remote Desktop) nie jest włączona w grupie zabezpieczeń sieci (NSG).


> [!NOTE] 
> Platforma Azure oferuje dwa modele wdrażania związane z tworzeniem i pracą z zasobami: [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../../azure-resource-manager/resource-manager-deployment-model.md). Zalecamy użycie modelu wdrażania usługi Resource Manager zamiast klasycznego modelu wdrażania w przypadku nowych wdrożeń. 

## <a name="symptom"></a>Objaw

Nie można wprowadzić z połączeniem RDP z maszyną wirtualną na platformie Azure, ponieważ portu RDP nie jest otwarty w sieciowej grupie zabezpieczeń.

## <a name="solution"></a>Rozwiązanie 

Podczas tworzenia nowej maszyny Wirtualnej cały ruch z Internetu jest blokowany domyślnie. 

Aby włączyć port protokołu RDP w sieciowej grupie zabezpieczeń, wykonaj następujące kroki:
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com).
2. W **maszyn wirtualnych**, wybierz maszynę Wirtualną, którego dotyczy problem. 
3. W **ustawienia**, wybierz opcję **sieć**. 
4. W **reguły portów wejściowych**, sprawdź, czy port dla protokołu RDP jest poprawnie ustawiony. Poniżej znajduje się przykład konfiguracji: 

    **Priorytet**: 300 </br>
    **Port**: 3389 </br>
    **Nazwa**: Port_3389 </br>
    **Port**: 3389 </br>
    **Protokół**: TCP </br>
    **Źródło**: Dowolne </br>
    **Miejsca docelowe**: Dowolne </br>
    **Akcja**: Zezwalaj </br>

Jeśli określisz źródłowy adres IP, to ustawienie zezwala na ruch tylko z określonego adresu IP lub zakres adresów IP, aby nawiązać połączenie z maszyną Wirtualną. Upewnij się, że komputer, na którym używasz, aby rozpocząć sesję RDP znajduje się w zakresie.

Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [sieciowej grupy zabezpieczeń](../../virtual-network/security-overview.md).

> [!NOTE]
> Port 3389 protokołu RDP jest uwidaczniany w Internecie. Dlatego zaleca się, że ten port jest używany tylko w przypadku zalecane do testowania. W środowiskach produkcyjnych firma Microsoft zaleca użycie sieci VPN lub połączenie prywatne.

## <a name="next-steps"></a>Kolejne kroki

Jeżeli port protokołu RDP jest już włączony w sieciowej grupie zabezpieczeń, zobacz [rozwiązać błąd ogólnego protokołu RDP na maszynie Wirtualnej platformy Azure](./troubleshoot-rdp-general-error.md).



