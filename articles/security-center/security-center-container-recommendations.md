---
title: Zalecenia kontenera w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: W tym dokumencie wyjaśniono zaleceń Centrum zabezpieczeń Azure, jak chronić swoje kontenery.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 2e76c7f7-a3dd-4d9f-add9-7e0e10e9324d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: rkarlin
ms.openlocfilehash: 782c769bc7825dc9b6bd3ba3b8e36885bf150eaa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60705301"
---
# <a name="understand-azure-security-center-container-recommendations"></a>Omówienie zaleceń kontenera usługi Azure Security Center

Podczas migracji aplikacji monolitu do uruchamiania o kluczowym znaczeniu konteneryzowanych aplikacji natywnych dla chmury w środowisku produkcyjnym, należy korzystać z zalet funkcji kontenerów, w tym proste i szybkie wdrażanie i aktualizacja. Jak liczba kontenerów wdrożonych stale rośnie, rozwiązania w zakresie zabezpieczeń należy w celu zapewniają wgląd w stan zabezpieczeń kontenerów i ich ochronę przed zagrożeniami.

Usługa Azure Security Center oferuje następujące funkcje, które ułatwiają zabezpieczanie kontenerów:

- **Wgląd w kontenerach hostowanych w infrastrukturze IaaS maszyn z systemem Linux**<br>W usłudze Azure Security Center na karcie kontenerów Wyświetla wszystkich maszyn wirtualnych wdrożonych za pomocą platformy Docker. Podczas eksplorowania problemy dotyczące zabezpieczeń na maszynie wirtualnej, usługa Security Center zapewnia dodatkowe informacje związane z kontenerami na maszynie, na przykład wersja platformy Docker i liczbę obrazów uruchomiona na hoście.

    ![karty kontenera](./media/security-center-container-recommendations/docker-recommendation.png)


- **Zalecenia dotyczące zabezpieczeń oparte na wzorzec konfiguracji (ci) dla platformy Docker**<br>Usługa Security Center skanuje konfiguracje platformy Docker i zapewnia wgląd w błędy konfiguracji poprzez dostarczenie listy wszystkich ocenionych reguł zakończonych niepowodzeniem. Usługa Security Center zapewnia wskazówek, aby pomóc Ci szybko rozwiązać te problemy i zaoszczędzić czas. Usługa Security Center stale ocenia konfiguracje platformy Docker i udostępnia Ci ich najnowszy stan.

    ![karty kontenera](./media/security-center-container-recommendations/container-cis-benchmark.png)

- **Wykrywanie zagrożeń w czasie rzeczywistym kontenera**<br> Usługa Security Center zapewnia wykrywanie zagrożeń w czasie rzeczywistym dla kontenerów na maszynach z systemem Linux za pomocą składnika wykorzystują. Alerty zidentyfikować kilka podejrzanych działań platformy Docker, takich jak tworzenie uprzywilejowanych kontenera na hoście, wskazanie serwera protokołu Secure Shell (SSH) uruchomionej w kontenerze platformy Docker lub korzystanie z usług kryptograficznych wyszukiwarek. Te informacje pozwalają na szybkie rozwiązywanie problemów dotyczących zabezpieczeń i poprawę bezpieczeństwa kontenerów.

    ![karty kontenera](./media/security-center-container-recommendations/docker-threat-detection.png)

## <a name="recommendations"></a>Zalecenia
Aby lepiej zrozumieć dostępne kontenery hostowanych na maszynach z systemem IaaS Linux i ocenę zabezpieczeń ich konfiguracji platformy Docker, użyj poniższe tabele zawierają jako odwołanie.

| Zalecenie | Opis | Korygowanie |
| --- | --- | --- |
|Korygowanie luk w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów |Korygowanie luk w zabezpieczeniach w kontenerze konfiguracji zabezpieczeń oparte na najlepszymi rozwiązaniami konfiguracyjnymi.| Aby korygowanie luk w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów:<br>1. Przejrzyj listę reguł nie powiodło się.<br>2. Usuń każdą regułę, zgodnie z instrukcjami określony.|


## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat zalecenia, które mają zastosowanie do innych typów zasobów platformy Azure, zobacz następujące tematy:

* [Monitorowanie tożsamości i dostępu w usłudze Azure Security Center](security-center-identity-access.md)
* [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)
* [Ochrona usługi Azure SQL w usłudze Azure Security Center](security-center-sql-service-recommendations.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ochrona maszyn i aplikacji w usłudze Azure Security Center](security-center-virtual-machine-protection.md)
* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.

