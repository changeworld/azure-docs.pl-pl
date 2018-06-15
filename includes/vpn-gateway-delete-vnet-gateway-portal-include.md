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
ms.openlocfilehash: 5b2aa7fedbc203c50796a0e0c8d9cdb3895ae6c1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30198149"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Krok 1: Przejdź do bramy sieci wirtualnej

1. W [portalu Azure](https://portal.azure.com), przejdź do **wszystkie zasoby**. 
2. Aby otworzyć stronę bramy sieci wirtualnej, przejdź do bramy sieci wirtualnej, który chcesz usunąć i kliknij ją.

### <a name="step-2-delete-connections"></a>Krok 2: Usuwanie połączeń

1. Na stronie dla bramy sieci wirtualnej, kliknij przycisk **połączeń** Aby wyświetlić wszystkie połączenia z bramą.
2. Kliknij przycisk **"..."** w wierszu nazwę połączenia, następnie wybierz **usunąć** z listy rozwijanej.
3. Kliknij przycisk **tak** aby upewnić się, że chcesz usunąć połączenie. Jeśli masz wiele połączeń, należy usunąć każdego połączenia.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Krok 3: Usuń bramę sieci wirtualnej

Należy pamiętać, że jeśli konfigurację P2S do tej sieci wirtualnej oprócz konfiguracji S2S, usuwanie bramy sieci wirtualnej zostanie automatycznie Odłącz wszystkich klientów P2S bez ostrzeżenia.

1. Na stronie bramy sieci wirtualnej, kliknij przycisk **omówienie**.
2. Na **omówienie** kliknij przycisk **usunąć** można usunąć bramy.
