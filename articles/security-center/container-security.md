---
title: Zabezpieczenia kontenerów w usłudze Azure Security Center | Dokumenty firmy Microsoft
description: Dowiedz się więcej o funkcjach zabezpieczeń kontenerów usługi Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: d46e2a9820ec0c45d197f135428f1ace712b2fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125141"
---
# <a name="container-security-in-security-center"></a>Zabezpieczenia kontenerów w Centrum zabezpieczeń

Usługa Azure Security Center to natywne rozwiązanie platformy Azure dla zabezpieczeń kontenerów. Usługa Security Center to również optymalne środowisko pojedynczego okienka szkła dla bezpieczeństwa obciążeń w chmurze, maszyn wirtualnych, serwerów i kontenerów.

W tym artykule opisano, jak usługa Security Center pomaga ulepszać, monitorować i utrzymywać bezpieczeństwo kontenerów i ich aplikacji. Dowiesz się, jak usługa Security Center pomaga w podstawowych aspektach zabezpieczeń kontenerów:

* Zarządzanie lukami w zabezpieczeniach
* Utwardzanie środowiska pojemnika
* Ochrona środowiska uruchomieniowego

[![Karta zabezpieczeń kontenera usługi Azure Security Center](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

Aby uzyskać instrukcje dotyczące korzystania z tych funkcji, zobacz [Monitorowanie zabezpieczeń kontenerów](monitor-container-security.md).

## <a name="vulnerability-management---scanning-container-images"></a>Zarządzanie lukami w zabezpieczeniach — skanowanie obrazów kontenerów
Aby monitorować rejestr kontenerów platformy Azure oparty na usłudze ARM, upewnij się, że korzystasz ze standardowej warstwy usługi Security Center (patrz [cennik).](/azure/security-center/security-center-pricing) Następnie włącz opcjonalny pakiet rejestrów kontenerów. Po wypchnięciu nowego obrazu usługa Security Center skanuje obraz za pomocą skanera od wiodącego w branży dostawcy skanowania luk w zabezpieczeniach Qualys.

Po znalezieniu problemów — przez Qualys lub Security Center — otrzymasz powiadomienie na pulpicie nawigacyjnym centrum zabezpieczeń. W przypadku każdej usterki usługa Security Center zawiera zalecenia umożliwiające podjęcie działań wraz z klasyfikacją ważności i wskazówkami dotyczącymi rozwiązywania problemu. Szczegółowe informacje na temat zaleceń centrum zabezpieczeń dotyczących kontenerów można znaleźć na [liście zaleceń .](recommendations-reference.md#recs-containers)

Usługa Security Center filtruje i klasyfikuje wyniki ze skanera. Gdy obraz jest w dobrej kondycji, Centrum zabezpieczeń oznacza go jako taki. Usługa Security Center generuje zalecenia dotyczące zabezpieczeń tylko dla obrazów, które mają problemy do rozwiązania. Powiadamiając tylko wtedy, gdy występują problemy, usługa Security Center zmniejsza ryzyko niechcianych alertów informacyjnych.

## <a name="environment-hardening"></a>Utwardzanie środowiska

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Ciągłe monitorowanie konfiguracji platformy Docker
Usługa Azure Security Center identyfikuje niezarządzane kontenery hostowane na maszynach wirtualnych z systemem IaaS z systemem Linux lub innych komputerach z systemem Linux z kontenerami platformy Docker. Usługa Security Center stale ocenia konfiguracje tych kontenerów. Następnie porównuje je z [Center for Internet Security (CIS) Docker Benchmark](https://www.cisecurity.org/benchmark/docker/).

Usługa Security Center zawiera cały zestaw reguł wzorca platformy docker CIS i ostrzega, jeśli kontenery nie spełniają żadnych formantów. Po znalezieniu błędnych konfiguracji usługa Security Center generuje zalecenia dotyczące zabezpieczeń. Strona **rekomendacji** służy do wyświetlania zaleceń i rozwiązywania problemów. Zobaczysz również zalecenia na karcie **Kontenery,** która wyświetla wszystkie maszyny wirtualne wdrożone za pomocą platformy Docker. 

Aby uzyskać szczegółowe informacje na temat odpowiednich zaleceń centrum zabezpieczeń, które mogą pojawić się dla tej funkcji, zobacz [sekcję kontenera](recommendations-reference.md#recs-containers) w tabeli rekomendacji odwołania.

Podczas eksplorowania problemów z zabezpieczeniami maszyny Wirtualnej centrum zabezpieczeń udostępnia dodatkowe informacje o kontenerach na komputerze. Takie informacje obejmują wersję platformy Docker i liczbę obrazów uruchomionych na hoście. 

>[!NOTE]
> Te kontrole wzorcowe WNP nie będą uruchamiane na wystąpieniach zarządzanych przez AKS lub maszynach wirtualnych zarządzanych przez Databricks.

### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Ciągłe monitorowanie klastrów kubernetes
Usługa Security Center współpracuje z usługą Azure Kubernetes Service (AKS), usługą aranżacji zarządzanych kontenerów firmy Microsoft w celu tworzenia, wdrażania i zarządzania aplikacjami konteneryzowanymi.

Usługa AKS zapewnia mechanizmy kontroli zabezpieczeń i wgląd w poziom zabezpieczeń klastrów. Usługa Security Center używa następujących funkcji do:
* Ciągłe monitorowanie konfiguracji klastrów AKS
* Generowanie zaleceń dotyczących zabezpieczeń zgodnych ze standardami branżowymi

Aby uzyskać szczegółowe informacje na temat odpowiednich zaleceń centrum zabezpieczeń, które mogą pojawić się dla tej funkcji, zobacz [sekcję kontenera](recommendations-reference.md#recs-containers) w tabeli rekomendacji odwołania.

## <a name="run-time-protection---real-time-threat-detection"></a>Ochrona w czasie wykonywania — wykrywanie zagrożeń w czasie rzeczywistym

Usługa Security Center zapewnia wykrywanie zagrożeń w czasie rzeczywistym dla środowisk konteneryzowanych i generuje alerty o podejrzanych działaniach. Te informacje pozwalają na szybkie rozwiązywanie problemów dotyczących zabezpieczeń i poprawę bezpieczeństwa kontenerów.

Wykrywamy zagrożenia na poziomie hosta i klastra AKS. Aby uzyskać szczegółowe informacje, zobacz [wykrywanie zagrożeń dla kontenerów platformy Azure.](threat-protection.md#azure-containers)


## <a name="container-security-faq"></a>Zabezpieczyć kontener – często zadawane pytania

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Jakie typy obrazów można skanować w usłudze Azure Security Center?
Centrum zabezpieczeń skanuje obrazy oparte na systemie operacyjnym Linux, które zapewniają dostęp do powłoki. 

Skaner Qualys nie obsługuje super minimalistycznych obrazów, takich jak obrazy [podstawek platformy Docker](https://hub.docker.com/_/scratch/) lub obrazy "Bez dystrybucji", które zawierają tylko aplikację i jej zależności środowiska uruchomieniowego bez menedżera pakietów, powłoki lub systemu operacyjnego.

### <a name="how-does-azure-security-center-scan-an-image"></a>W jaki sposób usługa Azure Security Center skanuje obraz?
Obraz zostanie pobrany z rejestru. Następnie jest uruchamiany w izolowanej piaskownicy ze skanerem Qualys, który wyodrębnia listę znanych luk w zabezpieczeniach.

Usługa Security Center filtruje i klasyfikuje wyniki ze skanera. Gdy obraz jest w dobrej kondycji, Centrum zabezpieczeń oznacza go jako taki. Usługa Security Center generuje zalecenia dotyczące zabezpieczeń tylko dla obrazów, które mają problemy do rozwiązania. Powiadamiając tylko wtedy, gdy występują problemy, usługa Security Center zmniejsza ryzyko niechcianych alertów informacyjnych.

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Jak często usługa Azure Security Center skanuje moje obrazy?
Skanowanie obrazów jest wyzwalane przy każdym naciśnięciu.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Czy mogę uzyskać wyniki skanowania za pośrednictwem interfejsu REST API?
Tak. Wyniki są w obszarze [Podoceń Reszta API](/rest/api/securitycenter/subassessments/list/). Ponadto można użyć usługi Azure Resource Graph (ARG), kusto-jak interfejs API dla wszystkich zasobów: kwerenda można pobrać określonego skanowania.
 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o zabezpieczeniach kontenerów w usłudze Azure Security Center, zobacz następujące powiązane artykuły:

* Aby wyświetlić poziom zabezpieczeń zasobów związanych z kontenerem, zobacz sekcję kontenery [w sekcji Chroń swoje maszyny i aplikacje](security-center-virtual-machine-protection.md#containers).

* Szczegóły [integracji z usługą Azure Kubernetes](azure-kubernetes-service-integration.md)

* Szczegóły [integracji z rejestrem kontenerów platformy Azure](azure-container-registry-integration.md)