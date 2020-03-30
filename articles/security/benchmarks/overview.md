---
title: Omówienie analizy testów porównawczych zabezpieczeń platformy Azure
description: Omówienie testu porównawczego zabezpieczeń
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: 2f4b034948605b0a53a0320863608d284719a96d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587536"
---
# <a name="overview-of-the-azure-security-benchmark"></a>Omówienie wzoru zabezpieczeń platformy Azure

Usługa Azure Security Benchmark zawiera zalecenia, które ułatwiają zwiększenie bezpieczeństwa aplikacji i danych na platformie Azure.

Ten benchmark koncentruje się na obszarach kontroli zorientowanych na chmurę. Te formanty są zgodne ze znanymi wskaźnikami zabezpieczeń, takimi jak te opisane przez formanty Center for Internet Security (CIS) w wersji 7.1.

Następujące kontrolki są używane w usłudze Azure Security Benchmark: 

- [Bezpieczeństwo sieci](security-control-network-security.md)
- [Rejestrowanie i monitorowanie](security-control-logging-monitoring.md)
- [Tożsamość i kontrola dostępu](security-control-identity-access-control.md)
- [Ochrona danych](security-control-data-protection.md)
- [Zarządzanie lukami w zabezpieczeniach](security-control-vulnerability-management.md)
- [Zarządzanie magazynem i zasobami](security-control-inventory-asset-management.md)
- [Bezpieczna konfiguracja](security-control-secure-configuration.md)
- [Ochrona przed złośliwym oprogramowaniem](security-control-malware-defense.md)
- [Odzyskiwanie danych](security-control-data-recovery.md)
- [Reagowanie na incydenty](security-control-incident-response.md)
- [Testy penetracyjne i ćwiczenia typu „red team”](security-control-penetration-tests-red-team-exercises.md)

Można również pobrać [arkusz kalkulacyjny programu Azure Security Benchmark w wersji 1 excel](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets).

## <a name="azure-security-benchmark-recommendations"></a>Zalecenia dotyczące testów porównawczych zabezpieczeń platformy Azure 

Każde zalecenie zawiera następujące informacje: 

- **Identyfikator platformy Azure:** identyfikator wzorca zabezpieczeń platformy Azure, który odpowiada zaleceniu. 
- **Identyfikatory CIS : Zalecenia(-a)** dotyczące wskaźników CIS, które odpowiadają niniejszemu zaleceniu.  
- **Odpowiedzialność:** Czy klient lub usługodawca (lub oba) jest (są) odpowiedzialne za wdrożenie tego zalecenia. Obowiązki związane z bezpieczeństwem są współużytkowane w chmurze publicznej. Niektóre środki kontroli zabezpieczeń są dostępne tylko dla dostawcy usług w chmurze i dlatego dostawca jest odpowiedzialny za adresowanie tych. Są to ogólne obserwacje — w przypadku niektórych usług odpowiedzialność będzie inna niż wymieniona w usłudze Azure Security Benchmark. Różnice te są opisane w zaleceniach bazowych dla poszczególnych usług. 
- **Szczegóły**: Uzasadnienie zalecenia i linki do wskazówek dotyczących sposobu jego wdrożenia. Jeśli zalecenie jest obsługiwane przez usługę Azure Security Center, te informacje również zostaną wyświetlone.

Z zadowoleniem przyjmujemy szczegółowe opinie i aktywny udział w wysiłkach testów porównawczych zabezpieczeń platformy Azure. Jeśli chcesz przekazać bezpośredni wkład zespołowi Benchmark, wypełnij [https://aka.ms/AzSecBenchmark](https://aka.ms/AzSecBenchmark)formularz na .

## <a name="next-steps"></a>Następne kroki

- Zobacz pierwszą kontrolę zabezpieczeń: [Zabezpieczenia sieciowe](security-control-network-security.md)
- Przeczytaj wprowadzenie do [testów porównawczych zabezpieczeń platformy Azure](introduction.md)
- Pobieranie [arkusza kalkulacyjnego programu Azure Security Benchmark w wersji 1 excel](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets)
