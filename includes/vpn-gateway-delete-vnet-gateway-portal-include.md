---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d36d2be59010c47348a8e196b28d87e5b967868e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183102"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Krok 1: Przejdź do bramy sieci wirtualnej

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do **pozycji Wszystkie zasoby**. 
2. Aby otworzyć stronę bramy sieci wirtualnej, przejdź do bramy sieci wirtualnej, którą chcesz usunąć, i kliknij ją.

### <a name="step-2-delete-connections"></a>Krok 2: Usuwanie połączeń

1. Na stronie bramy sieci wirtualnej kliknij pozycję **Połączenia,** aby wyświetlić wszystkie połączenia z bramą.
2. Kliknij **"..."w** wierszu nazwy połączenia, a następnie wybierz pozycję **Usuń** z listy rozwijanej.
3. Kliknij **przycisk Tak,** aby potwierdzić, że chcesz usunąć połączenie. Jeśli masz wiele połączeń, usuń każde połączenie.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Krok 3: Usuwanie bramy sieci wirtualnej

Należy pamiętać, że jeśli oprócz konfiguracji S2S masz konfigurację P2S do tej sieci wirtualnej, usunięcie bramy sieci wirtualnej spowoduje automatyczne odłączenie wszystkich klientów P2S bez ostrzeżenia.

1. Na stronie bramy sieci wirtualnej kliknij pozycję **Przegląd**.
2. Na stronie **Przegląd** kliknij pozycję **Usuń,** aby usunąć bramę.
