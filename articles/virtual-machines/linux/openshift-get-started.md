---
title: OpenShift w usłudze Azure — omówienie | Dokumentacja firmy Microsoft
description: Omówienie platformy OpenShift na platformie Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: c8ebd10544331d3b7d887124542fe4ee2753c714
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233146"
---
# <a name="openshift-in-azure"></a>Usługa OpenShift na platformie Azure

OpenShift to platforma aplikacji kontenera otwartej i rozszerzalnej prawdziwym platformy Docker i Kubernetes przedsiębiorstwa.  

OpenShift obejmuje Kubernetes orkiestracji kontenerów i zarządzania. Dodaje skoncentrowany na deweloperach i skoncentrowane na operacje narzędzi Włącz tego:

- Szybkie opracowywanie aplikacji.
- Łatwe wdrażanie i skalowanie.
- Długoterminowe obsługi cyklu życia dla aplikacji i zespołów.

Brak dostępnych wiele wersji platformy OpenShift.  Z tych wersji tylko dwa są obecnie dostępne dla klientów do wdrożenia na platformie Azure: OpenShift Container Platform i OKD (dawniej platformy OpenShift Origin).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift. jest to w pełni zarządzana oferta platformy OpenShift działających na platformie Azure. Ta usługa jest wspólnie zarządzane i obsługiwane przez firmę Microsoft i Red Hat. Pełną dokumentację można [tutaj](https://docs.microsoft.com/azure/openshift/). 

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Platforma kontenera jest przedsiębiorstw [komercyjnej wersji](https://www.openshift.com) z i obsługiwane przez firmy Red Hat. W tej wersji klienci zakupu niezbędnych uprawnień dla rozwiązania OpenShift Container Platform i jest odpowiedzialny za instalację i zarządzanie całą infrastrukturą.

Ponieważ klienci "własnością" tej platformy, mogą ją zainstalować w ich lokalnym centrum danych lub w chmurze publicznej (np. Azure).

## <a name="okd"></a>OKD

Jest OKD [typu open-source](https://www.okd.io/) nadrzędnego projektu OpenShift, który jest wspieranym przez społeczność. OKD można zainstalować na CentOS i Red Hat Enterprise Linux (RHEL).

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie wspólne wymagania wstępne dla rozwiązania OpenShift na platformie Azure](./openshift-prerequisites.md)
- [Wdrażanie rozwiązania OpenShift Container Platform na platformie Azure](./openshift-container-platform.md)
- [Wdrażanie oferty Samozarządzanej portalu Marketplace platformy kontenera OpenShift](./openshift-marketplace-self-managed.md)
- [Wdrażanie platformy OpenShift w usłudze Azure Stack](./openshift-azure-stack.md)
- [Po wdrożeniu zadania](./openshift-post-deployment.md)
- [Rozwiązywanie problemów z wdrożenia platformy OpenShift](./openshift-troubleshooting.md)
