---
title: Zabezpieczenia kontenera w Azure Security Center | Microsoft Docs
description: Dowiedz się więcej o funkcjach zabezpieczeń kontenerów Azure Security Center.
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
ms.openlocfilehash: 62728f5b66825eb6698e37bb7ad3adbad831b465
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77615348"
---
# <a name="container-security-in-security-center"></a>Zabezpieczenia kontenera w Security Center

Azure Security Center jest rozwiązaniem natywnym platformy Azure na potrzeby zabezpieczeń kontenerów. Security Center jest również optymalnym jednym okienkiem doświadczenia w zakresie bezpieczeństwa obciążeń, maszyn wirtualnych, serwerów i kontenerów w chmurze.

W tym artykule opisano, jak Security Center pomaga ulepszać, monitorować i obsługiwać zabezpieczenia kontenerów oraz ich aplikacji. Dowiesz się, jak Security Center ułatwiają następujące podstawowe aspekty zabezpieczeń kontenera:

* Zarządzanie lukami w zabezpieczeniach
* Ograniczanie funkcjonalności środowiska kontenera
* Ochrona środowiska uruchomieniowego

[Karta Zabezpieczenia kontenera Azure Security Center ![](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

Aby uzyskać instrukcje dotyczące korzystania z tych funkcji, zobacz [monitorowanie zabezpieczeń kontenerów](monitor-container-security.md).

## <a name="vulnerability-management---scanning-container-images-preview"></a>Zarządzanie lukami w zabezpieczeniach — skanowanie obrazów kontenerów (wersja zapoznawcza)
Aby monitorować Azure Container Registry oparte na usłudze ARM, upewnij się, że korzystasz z warstwy Standardowa Security Center (zobacz [Cennik](/azure/security-center/security-center-pricing)). Następnie Włącz opcjonalny pakiet rejestrów kontenerów. Po wypchnięciu nowego obrazu Security Center skanuje obraz przy użyciu skanera od wiodącego w branży skanowania dostawcy, Qualys.

Po znalezieniu problemów — według Qualys lub Security Center — otrzymasz powiadomienie na pulpicie nawigacyjnym Security Center. W przypadku każdej luki w zabezpieczeniach Security Center zawiera zalecenia z możliwością wykonania akcji, a także klasyfikację ważności oraz wskazówki dotyczące sposobu korygowania problemu. Aby uzyskać szczegółowe informacje na temat zaleceń dotyczących Security Center kontenerów, zobacz [listę referencyjną zaleceń](recommendations-reference.md#recs-containers).

## <a name="environment-hardening"></a>Ograniczanie funkcjonalności środowiska

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Ciągłe monitorowanie konfiguracji platformy Docker
Azure Security Center identyfikuje niezarządzane kontenery hostowane na maszynach wirtualnych z systemem Linux IaaS lub na innych maszynach systemu Linux z uruchomionymi kontenerami Docker Security Center stale ocenia konfiguracje tych kontenerów. Następnie porównuje je ze [wzorcem usługi Docker dla usługi Internet Security (CIS)](https://www.cisecurity.org/benchmark/docker/).

Security Center obejmuje cały zestaw reguł testu wydajnościowego usługi CIS Docker i generuje alert, jeśli kontenery nie spełniają żadnej z tych kontrolek. Po znalezieniu niepożądanych konfiguracji Security Center generuje zalecenia dotyczące zabezpieczeń. Na **stronie zalecenia** można przeglądać zalecenia i rozwiązywać problemy. Zobaczysz również zalecenia na karcie **kontenery** , w których są wyświetlane wszystkie maszyny wirtualne wdrożone przy użyciu platformy Docker. 

Aby uzyskać szczegółowe informacje dotyczące odpowiednich Security Center zaleceń, które mogą pojawić się w przypadku tej funkcji, zobacz [sekcję Container](recommendations-reference.md#recs-containers) w tabeli referencyjnej zalecenia.

Podczas eksplorowania problemów z zabezpieczeniami maszyny wirtualnej Security Center zawiera dodatkowe informacje na temat kontenerów na komputerze. Takie informacje obejmują wersję platformy Docker i liczbę obrazów uruchomionych na hoście. 

>[!NOTE]
> Te testy porównawcze usług CIS nie będą uruchamiane w wystąpieniach zarządzanych przez AKS lub maszynach wirtualnych zarządzanych przez program.

### <a name="continuous-monitoring-of-your-kubernetes-clusters-preview"></a>Ciągłe monitorowanie klastrów Kubernetes (wersja zapoznawcza)
Security Center współpracuje z usługą Azure Kubernetes Service (AKS), zarządzaną usługą aranżacji kontenerów firmy Microsoft na potrzeby opracowywania i wdrażania aplikacji kontenerowych oraz zarządzania nimi.

AKS zapewnia kontrolę zabezpieczeń i wgląd w zabezpieczenia stan klastrów. Security Center używa tych funkcji, aby:
* Stale monitoruj konfigurację klastrów AKS
* Generuj zalecenia dotyczące zabezpieczeń dostosowane do standardów branżowych

Aby uzyskać szczegółowe informacje dotyczące odpowiednich Security Center zaleceń, które mogą pojawić się w przypadku tej funkcji, zobacz [sekcję Container](recommendations-reference.md#recs-containers) w tabeli referencyjnej zalecenia.

## <a name="run-time-protection---real-time-threat-detection"></a>Ochrona w czasie wykonywania — wykrywanie zagrożeń w czasie rzeczywistym

Security Center zapewnia wykrywanie zagrożeń w czasie rzeczywistym w środowiskach kontenerów i generuje alerty dla podejrzanych działań. Te informacje pozwalają na szybkie rozwiązywanie problemów dotyczących zabezpieczeń i poprawę bezpieczeństwa kontenerów.

Wykrywamy zagrożenia na poziomie klastra hosta i AKS. Aby uzyskać szczegółowe informacje, zobacz [Ochrona przed zagrożeniami dla kontenerów platformy Azure](threat-protection.md#azure-containers).


## <a name="container-security-faq"></a>Zabezpieczenia kontenerów — często zadawane pytania

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Jakie typy obrazów mogą Azure Security Center skanowanie?
Security Center skanuje obrazy oparte na systemie operacyjnym Linux, które zapewniają dostęp do powłoki. 

Skaner Qualys nie obsługuje obrazów minimalistyczny, takich jak obrazy wyłuskane [platformy Docker](https://hub.docker.com/_/scratch/) lub obrazy typu "Distroless", które zawierają tylko aplikację i jej zależności środowiska uruchomieniowego (bez Menedżera pakietów, powłoki lub systemu operacyjnego).

### <a name="how-does-we-scan-azure-security-center-scan-an-image"></a>Jak skanuje Azure Security Center skanowanie obrazu?
Obraz jest wyodrębniany z rejestru. Następnie uruchamia się w izolowanej piaskownicy za pomocą skanera Qualys, który wyodrębnia listę znanych luk w zabezpieczeniach.

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Jak często Azure Security Center skanować moje obrazy?
Podczas każdego wypychania są wyzwalane skanowania obrazu.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Czy mogę uzyskać wyniki skanowania za pośrednictwem interfejsu API REST?
Tak. Wyniki są poniżej [interfejsu API REST podocen](/rest/api/securitycenter/subassessments/list/). Ponadto można użyć usługi Azure Resource Graph (ARG), interfejsu API podobnej do Kusto dla wszystkich zasobów: zapytanie może pobrać określone skanowanie.
 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat zabezpieczeń kontenerów w Azure Security Center, zobacz następujące artykuły pokrewne:

* Aby wyświetlić stan zabezpieczeń zasobów związanych z kontenerem, zobacz sekcję kontenery w obszarze [Ochrona maszyn i aplikacji](security-center-virtual-machine-protection.md#containers).

* Szczegóły [integracji z usługą Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* Szczegóły [integracji z Azure Container Registry](azure-container-registry-integration.md)