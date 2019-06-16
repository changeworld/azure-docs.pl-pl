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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66157418"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Krok 1: Przejdź do bramy sieci wirtualnej

1. W [witryny Azure portal](https://portal.azure.com), przejdź do **wszystkie zasoby**. 
2. Aby otworzyć stronę bramy sieci wirtualnej, przejdź do bramy sieci wirtualnej, który chcesz usunąć i kliknij ją.

### <a name="step-2-delete-connections"></a>Krok 2: Usuwanie połączeń

1. Na stronie bramy sieci wirtualnej, kliknij przycisk **połączeń** Aby wyświetlić wszystkie połączenia z bramą.
2. Kliknij przycisk **"..."** w wierszu nazwę połączenia, następnie wybierz pozycję **Usuń** z listy rozwijanej.
3. Kliknij przycisk **tak** aby upewnić się, że chcesz usunąć połączenie. Jeśli masz wiele połączeń, należy usunąć każdego połączenia.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Krok 3: Usuwanie bramy sieci wirtualnej

Należy pamiętać, że jeśli konfiguracji P2S w tej sieci wirtualnej oprócz konfigurację S2S, usuwanie bramy sieci wirtualnej zostanie automatycznie odłączyć wszystkich klientów P2S bez ostrzeżenia.

1. Na stronie bramy sieci wirtualnej kliknij **Przegląd**.
2. Na **Przegląd** kliknij **Usuń** Aby usunąć bramę.
