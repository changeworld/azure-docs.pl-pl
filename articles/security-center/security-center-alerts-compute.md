---
title: Wykrywanie zagrożeń w przypadku natywnego przetwarzania danych w chmurze w Azure Security Center | Microsoft Docs
description: W tym artykule przedstawiono natywne alerty obliczeniowe w chmurze dostępne w Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: a85ec565077ac229cd24bb7b9cf753015aa56ebf
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76024860"
---
# <a name="threat-detection-for-cloud-native-computing-in-azure-security-center"></a>Wykrywanie zagrożeń w przypadku natywnego przetwarzania danych w chmurze w Azure Security Center

Jako rozwiązanie natywne Azure Security Center ma unikatowy wgląd w dzienniki wewnętrzne na potrzeby identyfikacji metodologii ataków w wielu celach. W tym artykule przedstawiono alerty dostępne dla następujących usług platformy Azure:

* [Azure App Service](#app-services)
* [Kontenery platformy Azure](#azure-containers) 

> [!NOTE]
> Te usługi nie są obecnie dostępne w regionach platformy Azure dla instytucji rządowych i suwerennych.

## Azure App Service<a name="app-services"></a>

Security Center używa skali chmury, aby identyfikować ataki ukierunkowane na aplikacje działające przez App Service. Osoby atakujące mogą wykrywać słabe i luki w zabezpieczeniach aplikacji sieci Web. Przed skierowaniem do określonych środowisk żądania do aplikacji uruchomionych na platformie Azure przechodzą przez kilka bram, gdzie są kontrolowane i rejestrowane. Te dane są następnie używane do identyfikowania luk w zabezpieczeniach i ataków oraz do uczenia się nowych wzorców, które będą używane później.

Korzystając z widoczności platformy Azure jako dostawcy chmury, Security Center analizuje App Service dzienników wewnętrznych w celu zidentyfikowania metodologii ataków na wiele obiektów docelowych. Na przykład metodologia obejmuje szerokie skanowanie i rozproszone ataki. Ten typ ataku zwykle pochodzi z małego podzbioru adresów IP i pokazuje wzorce przeszukiwania do podobnych punktów końcowych na wielu hostach. Ataki są wyszukiwaniem zagrożonej strony lub wtyczki i nie mogą być zidentyfikowane z punktu widzenia jednego hosta.

Jeśli używasz planu App Service opartego na systemie Windows, Security Center również ma dostęp do podstawowych piaskownic i maszyn wirtualnych. Wraz z danymi dzienników wymienionymi powyżej infrastruktura może powiedzieć historię, od nowego ataku w przypadku komputerów klienckich. W związku z tym nawet jeśli Security Center zostanie wdrożona po wykorzystaniu aplikacji sieci Web, może być możliwe wykrycie trwających ataków.

Listę alertów Azure App Service można znaleźć w [tabeli referencyjnej alertów](alerts-reference.md#alerts-azureappserv).

## Kontenery platformy Azure<a name="azure-containers"></a>

Security Center zapewnia wykrywanie zagrożeń w czasie rzeczywistym w środowiskach kontenerów i generuje alerty dla podejrzanych działań. Te informacje pozwalają na szybkie rozwiązywanie problemów dotyczących zabezpieczeń i poprawę bezpieczeństwa kontenerów.

Wykrywamy zagrożenia na różnych poziomach: 

* Agent Security Center na **poziomie hosta** (dostępny w warstwie Standardowa, zobacz [Cennik](security-center-pricing.md) dla szczegółów) monitory Linux dla podejrzanych działań. Agent wyzwala alerty dotyczące podejrzanych działań pochodzących z węzła lub kontenera, w którym jest uruchomiona. Przykłady takich działań obejmują wykrywanie i nawiązywanie powłoki sieci Web przy użyciu znanych podejrzanych adresów IP.

    Aby uzyskać dokładniejszy wgląd w zabezpieczenia środowiska kontenerowego, agent monitoruje analizę specyficzną dla kontenera. Spowoduje to wyzwolenie alertów dotyczących zdarzeń, takich jak tworzenie kontenera uprzywilejowanego, podejrzany dostęp do serwerów interfejsu API i serwery Secure Shell (SSH) działające wewnątrz kontenera Docker.

    >[!NOTE]
    > Jeśli zdecydujesz się nie instalować agentów na hostach, będziesz otrzymywać tylko podzbiór korzyści i alertów dotyczących wykrywania zagrożeń. Nadal będziesz otrzymywać alerty dotyczące analizy sieci i komunikacji ze złośliwymi serwerami.

    Listę alertów na poziomie hosta można znaleźć w [tabeli referencyjnej alertów](alerts-reference.md#alerts-containerhost).


* Na **poziomie klastra AKS**monitorowanie wykrywania zagrożeń opiera się na analizie dzienników inspekcji Kubernetes. Aby włączyć monitorowanie **bez wykorzystania agentów** , Dodaj opcję Kubernetes do subskrypcji na stronie **ustawień & cenowej** (zobacz [Cennik](security-center-pricing.md)). Aby generować alerty na tym poziomie, Security Center monitoruje usługi zarządzane przez AKS przy użyciu dzienników pobranych przez AKS. Przykłady zdarzeń na tym poziomie obejmują uwidocznione pulpity nawigacyjne Kubernetes, tworzenie uprzywilejowanych ról i tworzenie poufnych instalacji.

    >[!NOTE]
    > Security Center generuje alerty wykrywania dla akcji i wdrożeń usługi Azure Kubernetes, które występują po włączeniu opcji Kubernetes w ustawieniach subskrypcji. 

    Listę alertów na poziomie klastra AKS można znaleźć w [tabeli referencyjnej alertów](alerts-reference.md#alerts-akscluster).

Ponadto nasz globalny zespół badaczy ds. zabezpieczeń stale monitoruje zagrożenie w poziomie. Po ich odnalezieniu są dodawane alerty i luki specyficzne dla kontenera.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat planów App Service, zobacz [plany App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).