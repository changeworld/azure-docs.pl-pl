---
title: Nie można połączyć się z maszynami wirtualnymi platformy Azure, ponieważ port RDP nie jest włączony w sieciowej sieciowej sieciowej | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązać problem, w którym prow nie powiedzie się z powodu konfiguracji sieciowej grupy zabezpieczeń w witrynie Azure | Dokumenty firmy Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 6c877690cf27edd73f1d828b8a1dda6f4f34e780
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918176"
---
#  <a name="cannot-connect-remotely-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>Nie można połączyć się zdalnie z maszyną wirtualną, ponieważ port RDP nie jest włączony w sieciowej sieciowej sieciowej

W tym artykule wyjaśniono, jak rozwiązać problem, w którym nie można połączyć się z maszyną wirtualną systemu Azure Windows (VM), ponieważ port protokołu RDP (Remote Desktop Protocol) nie jest włączony w sieciowej grupie zabezpieczeń (NSG).


## <a name="symptom"></a>Objaw

Nie można nawiązać połączenia RDP z maszyną wirtualną na platformie Azure, ponieważ port RDP nie jest otwarty w sieciowej grupie zabezpieczeń.

## <a name="solution"></a>Rozwiązanie 

Podczas tworzenia nowej maszyny Wirtualnej cały ruch z Internetu jest domyślnie blokowany. 

Aby włączyć port RDP w łączeniu tożsamości z numerem bliskim i tożsamości, wykonaj następujące kroki:
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com).
2. W **maszynach wirtualnych**wybierz maszynę wirtualną, która ma problem. 
3. W **obszarze Ustawienia**wybierz pozycję **Sieć**. 
4. W **regułach portów przychodzących**sprawdź, czy port dla protokołu RDP jest ustawiony poprawnie. Oto przykład konfiguracji: 

    **Priorytet**: 300 </br>
    **Nazwa**: Port_3389 </br>
    **Port(Cel podróży)**: 3389 </br>
    **Protokół**: TCP </br>
    **Źródło**: Dowolne </br>
    **Miejsca docelowe**: Dowolne </br>
    **Akcja**: Zezwalaj </br>

Jeśli określisz źródłowy adres IP, to ustawienie zezwala ruchowi tylko z określonego adresu IP lub zakresu adresów IP na połączenie się z maszyną wirtualną. Upewnij się, że komputer używany do uruchamiania sesji RDP znajduje się w zakresie.

Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [grupa zabezpieczeń sieci](../../virtual-network/security-overview.md).

> [!NOTE]
> Port RDP 3389 jest narażony na działanie Internetu. W związku z tym zaleca się używanie tego portu tylko do testowania zalecane. W środowiskach produkcyjnych zaleca się korzystanie z sieci VPN lub połączenia prywatnego.

## <a name="next-steps"></a>Następne kroki

Jeśli port RDP jest już włączony w sieciowej sieciowej sieciowej, zobacz [Rozwiązywanie problemów z ogólnym błędem protokołu RDP w usłudze Azure VM](./troubleshoot-rdp-general-error.md).



