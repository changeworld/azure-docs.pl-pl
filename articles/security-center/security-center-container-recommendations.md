---
title: Zalecenia dotyczące kontenerów w usłudze Azure Security Center | Dokumenty firmy Microsoft
description: W tym dokumencie opisano zalecenia usługi Azure Security Center dotyczące ochrony kontenerów.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2e76c7f7-a3dd-4d9f-add9-7e0e10e9324d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: memildin
ms.openlocfilehash: b1e1402f58b103570d6a98a5f9a01c8168abf749
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77912366"
---
# <a name="understand-azure-security-center-container-recommendations"></a>Omówienie rekomendacji dotyczących kontenera usługi Azure Security Center

Podczas migracji aplikacji monolitowych w celu uruchamiania krytycznych, konteneryzowanych aplikacji natywnych w chmurze w środowisku produkcyjnym można korzystać z funkcji kontenerów, w tym łatwego i szybkiego wdrażania i aktualizacji. Ponieważ liczba wdrożonych kontenerów nadal rośnie, rozwiązania zabezpieczeń muszą być stosowane, aby zapewnić wgląd w stan zabezpieczeń kontenerów i chronić je przed zagrożeniami.

Usługa Azure Security Center udostępnia następujące funkcje ułatwiające zabezpieczanie kontenerów:

- **Wgląd w kontenery hostowane na komputerach IaaS Linux**<br>W usłudze Azure Security Center na karcie Kontenery są wyświetlane wszystkie maszyny wirtualne wdrożone za pomocą platformy Docker. Podczas eksplorowania problemów z zabezpieczeniami na maszynie wirtualnej usługa Security Center udostępnia dodatkowe informacje związane z kontenerami na komputerze, takie jak wersja platformy Docker i liczba obrazów uruchomionych na hoście.

    ![karta kontenera](./media/security-center-container-recommendations/docker-recommendation.png)


- **Zalecenia dotyczące zabezpieczeń oparte na benchmarku CIS dla platformy Docker**<br>Usługa Security Center skanuje konfiguracje platformy Docker i zapewnia wgląd w błędy konfiguracji poprzez dostarczenie listy wszystkich ocenionych reguł zakończonych niepowodzeniem. Usługa Security Center zawiera wskazówki ułatwiające szybkie rozwiązywanie tych problemów i oszczędność czasu. Usługa Security Center stale ocenia konfiguracje platformy Docker i udostępnia Ci ich najnowszy stan.

    ![karta kontenera](./media/security-center-container-recommendations/container-cis-benchmark.png)

- **Ochrona przed zagrożeniami kontenerów w czasie rzeczywistym**<br> Usługa Security Center zapewnia ochronę przed zagrożeniami w czasie rzeczywistym dla kontenerów na komputerach z systemem Linux ze składnikiem AuditD. Alerty identyfikują kilka podejrzanych działań platformy Docker, takich jak utworzenie kontenera uprzywilejowanego na hoście, wskazanie serwera Secure Shell (SSH) działającego wewnątrz kontenera platformy Docker lub użycie górników kryptograficznych. Te informacje pozwalają na szybkie rozwiązywanie problemów dotyczących zabezpieczeń i poprawę bezpieczeństwa kontenerów.

    ![karta kontenera](./media/security-center-container-recommendations/docker-threat-detection.png)

## <a name="recommendations"></a>Zalecenia
Skorzystaj z poniższych tabel jako odwołania, aby ułatwić zrozumienie dostępnych kontenerów hostowanych na komputerach IaaS z systemem Linux i ocenę zabezpieczeń ich konfiguracji platformy Docker.

| Zalecenie | Opis | Korekty |
| --- | --- | --- |
|Usuwanie luk w zabezpieczeniach kontenerów |Usuwanie luk w zabezpieczeniach kontenerów na podstawie najlepszych rozwiązań dotyczących konfiguracji.| Aby skorygować luki w zabezpieczeniach kontenerów:<br>1. Przejrzyj listę nieudanych reguł.<br>2. Napraw każdą regułę zgodnie z określonymi instrukcjami.|


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o zaleceniach dotyczących innych typów zasobów platformy Azure, zobacz następujące elementy:

* [Monitorowanie tożsamości i dostępu w usłudze Azure Security Center](security-center-identity-access.md)
* [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)
* [Ochrona usługi SQL platformy Azure w usłudze Azure Security Center](security-center-sql-service-recommendations.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ochrona maszyn i aplikacji w usłudze Azure Security Center](security-center-virtual-machine-protection.md)
* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.