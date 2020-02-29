---
title: Zalecenia dotyczące kontenera w Azure Security Center | Microsoft Docs
description: W tym dokumencie opisano Azure Security Center zalecenia dotyczące ochrony kontenerów.
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
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912366"
---
# <a name="understand-azure-security-center-container-recommendations"></a>Omówienie zaleceń dotyczących kontenera Azure Security Center

Podczas migrowania aplikacji monolitu w celu uruchamiania aplikacji w chmurze o kluczowym znaczeniu w środowisku produkcyjnym można korzystać z funkcji kontenerów, w tym łatwe i szybkie wdrażanie i aktualizowanie. Wraz ze wzrostem liczby wdrożonych kontenerów nadal trzeba mieć na celu zapewnienie wglądu w stan zabezpieczeń kontenerów i ochronę przed zagrożeniami.

Azure Security Center zapewnia następujące funkcje, które ułatwiają Zabezpieczanie kontenerów:

- **Wgląd w kontenery hostowane na maszynach z systemem Linux IaaS**<br>W Azure Security Center na karcie kontenery są wyświetlane wszystkie maszyny wirtualne wdrożone przy użyciu platformy Docker. Podczas eksplorowania problemów z zabezpieczeniami na maszynie wirtualnej Security Center zawiera dodatkowe informacje dotyczące kontenerów na komputerze, takie jak wersja platformy Docker i Liczba obrazów uruchomionych na hoście.

    ![Karta kontener](./media/security-center-container-recommendations/docker-recommendation.png)


- **Zalecenia dotyczące zabezpieczeń oparte na teście usług CIS dla platformy Docker**<br>Usługa Security Center skanuje konfiguracje platformy Docker i zapewnia wgląd w błędy konfiguracji poprzez dostarczenie listy wszystkich ocenionych reguł zakończonych niepowodzeniem. Security Center zawiera wytyczne ułatwiające szybkie rozwiązywanie tych problemów i oszczędność czasu. Usługa Security Center stale ocenia konfiguracje platformy Docker i udostępnia Ci ich najnowszy stan.

    ![Karta kontener](./media/security-center-container-recommendations/container-cis-benchmark.png)

- **Ochrona przed zagrożeniami kontenera w czasie rzeczywistym**<br> Security Center zapewnia ochronę przed zagrożeniami w czasie rzeczywistym dla kontenerów na komputerach z systemem Linux z poddawanym inspekcją składnikiem. Alerty identyfikują kilka podejrzanych działań platformy Docker, takich jak tworzenie kontenera uprzywilejowanego na hoście, wskazanie serwera Secure Shell (SSH) działającego wewnątrz kontenera Docker lub użycie kryptografii wyszukiwarek używa tego. Te informacje pozwalają na szybkie rozwiązywanie problemów dotyczących zabezpieczeń i poprawę bezpieczeństwa kontenerów.

    ![Karta kontener](./media/security-center-container-recommendations/docker-threat-detection.png)

## <a name="recommendations"></a>Zalecenia
Poniższe tabele zawierają informacje ułatwiające zrozumienie dostępnych kontenerów hostowanych na maszynach z systemem Linux IaaS oraz ocenę zabezpieczeń ich konfiguracji platformy Docker.

| Zalecenie | Opis | Korygowanie |
| --- | --- | --- |
|Koryguj luki w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów |Koryguj luki w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów na podstawie najlepszych rozwiązań konfiguracyjnych.| Aby skorygować luki w zabezpieczeniach konfiguracji kontenerów:<br>1. Przejrzyj listę reguł zakończonych niepowodzeniem.<br>2. napraw każdą regułę zgodnie z określonymi instrukcjami.|


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat zalecenia, które mają zastosowanie do innych typów zasobów platformy Azure, zobacz następujące tematy:

* [Monitorowanie tożsamości i dostępu w usłudze Azure Security Center](security-center-identity-access.md)
* [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)
* [Ochrona usługi Azure SQL w Azure Security Center](security-center-sql-service-recommendations.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ochrona maszyn i aplikacji w usłudze Azure Security Center](security-center-virtual-machine-protection.md)
* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.