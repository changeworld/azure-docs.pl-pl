---
title: Azure Security Center i Azure Container Registry
description: Dowiedz się więcej o integracji Azure Security Center z usługą Azure Container Registry
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/19/2019
ms.author: memildin
ms.openlocfilehash: 4cc88e7c04d10907a9a6386b1266eb8031d60926
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552682"
---
# <a name="azure-container-registry-integration-with-security-center-preview"></a>Integracja Azure Container Registry z Security Center (wersja zapoznawcza)

Azure Container Registry (ACR) to zarządzana usługa rejestru prywatnego platformy Docker, która przechowuje obrazy kontenerów dla wdrożeń platformy Azure i zarządza nimi w rejestrze centralnym. Jest ona oparta na rejestrze platformy Docker open source 2,0.

Aby uzyskać dokładniejszy wgląd w luki w zabezpieczeniach rejestru i obrazów, użytkownicy warstwy Standardowa Azure Security Center mogą włączyć opcjonalny pakiet rejestrów kontenerów. Koszt korzystania z tej funkcji jest naliczany na obrazie, a nie na skanowaniu. Aby uzyskać więcej informacji, zobacz [cennik](security-center-pricing.md). Po włączeniu pakietu Security Center automatycznie skanuje obrazy w rejestrze za każdym razem, gdy obraz jest wypychany do rejestru.

> [!NOTE]
> Security Center pierwsze skanowanie rejestru nastąpi tylko po włączeniu pakietu rejestrów kontenerów, a obraz zostanie wypychany do rejestru.

Po zakończeniu skanowania (zwykle po około 10 minutach) wyniki są dostępne w Security Center zalecenia w następujący sposób:

[![przykład Azure Security Center zalecenia dotyczące luk w zabezpieczeniach odnalezionych w obrazie hostowanym Azure Container Registry (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Zalety integracji

Security Center identyfikuje rejestry ACR w ramach subskrypcji i bezproblemowo udostępnia:

* **Skanowanie w poszukiwaniu luk w zabezpieczeniach platformy Azure** w przypadku wszystkich wypychanych obrazów systemu Linux. Security Center skanuje obraz przy użyciu skanera z wiodącej w branży skanowania dostawcy, Qualys. To rozwiązanie natywne jest w sposób ciągły zintegrowane.

* **Zalecenia dotyczące zabezpieczeń** obrazów systemu Linux z znanymi lukami w zabezpieczeniach. Security Center zawiera szczegółowe informacje o każdej raportowanej luce w zabezpieczeniach i klasyfikacji ważności. Ponadto zawiera wskazówki dotyczące sposobu korygowania określonych luk w zabezpieczeniach w każdym obrazie wypychanym do rejestru.

![Ogólne omówienie Azure Security Center i Azure Container Registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o funkcjach zabezpieczeń kontenera Security Center, zobacz:

* [Zabezpieczenia Azure Security Center i kontenera](container-security.md)

* [Integracja z usługą Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* [Ochrona maszyny wirtualnej](security-center-virtual-machine-protection.md) — opis zaleceń dotyczących Security Center
