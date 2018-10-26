---
title: OpenShift w usłudze Azure — omówienie | Dokumentacja firmy Microsoft
description: Omówienie platformy OpenShift na platformie Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: d68215359d50ac153d6df3bbcce5a9b6171698bb
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085443"
---
# <a name="openshift-in-azure"></a>OpenShift na platformie Azure

OpenShift to platforma aplikacji kontenera otwartej i rozszerzalnej prawdziwym platformy Docker i Kubernetes przedsiębiorstwa.  

OpenShift obejmuje Kubernetes orkiestracji kontenerów i zarządzania. Dodaje skoncentrowany na deweloperach i skoncentrowane na operacje narzędzi Włącz tego:

- Szybkie opracowywanie aplikacji.
- Łatwe wdrażanie i skalowanie.
- Długoterminowe obsługi cyklu życia dla aplikacji i zespołów.

Brak dostępnych wiele wersji platformy OpenShift:

- OpenShift Container Platform
- Platforma OpenShift na platformie Azure (w pełni zarządzane OpenShift przychodzących na początku CY2019)
- OKD (dawniej platformy OpenShift Origin)
- W wersji dedykowanej platformy OpenShift
- OpenShift w trybie Online

Pięć wersji omówione w tym artykule tylko dwa są obecnie dostępne dla klientów do wdrożenia na platformie Azure: OpenShift Container Platform i OKD.

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Platforma kontenera jest przedsiębiorstw [komercyjnej wersji](https://www.openshift.com) z i obsługiwane przez firmy Red Hat. W tej wersji klienci zakupu niezbędnych uprawnień dla rozwiązania OpenShift Container Platform i jest odpowiedzialny za instalację i zarządzanie całą infrastrukturą.

Ponieważ klienci "własnością" tej platformy, mogą ją zainstalować w ich lokalnym centrum danych lub w chmurze publicznej (np. Azure, AWS lub Google).

## <a name="openshift-on-azure"></a>OpenShift na platformie Azure

Platforma OpenShift na platformie Azure jest w pełni zarządzana oferta platformy OpenShift działających na platformie Azure. Ta usługa jest wspólnie zarządzane i obsługiwane przez firmę Microsoft i Red Hat. Klaster zostanie wdrożony w subskrypcji platformy Azure przez klienta. Usługa jest obecnie dostępna w prywatnej wersji zapoznawczej i zostanie GA w wczesnych CY 2019 r. Więcej informacje będą dostarczane jako oferty pobiera bliżej GA.

## <a name="okd-formerly-openshift-origin"></a>OKD (dawniej platformy OpenShift Origin)

Jest OKD [typu open-source](https://www.okd.io/) nadrzędnego projektu OpenShift, który jest wspieranym przez społeczność. OKD można zainstalować na CentOS i Red Hat Enterprise Linux (RHEL).

## <a name="openshift-dedicated"></a>W wersji dedykowanej platformy OpenShift

Dedykowany jest zarządzana przez firmy Red Hat *pojedynczej dzierżawy* OpenShift, który używa OpenShift Container Platform. Red Hat zarządza wszystkimi podstawowej infrastruktury (maszyny wirtualne, klaster OpenShift, sieć, Magazyn itp.). Klaster jest przeznaczony dla jednego klienta i działa w chmurze publicznej (np. usług AWS lub Google). Począwszy od klastra zawiera cztery węzły aplikacji i wszelkie koszty są rocznych i płatnej z góry.

## <a name="openshift-online"></a>OpenShift w trybie Online

Online jest zarządzana przez firmy Red Hat *wielodostępnych* OpenShift, używającej platformę kontenerów. Red Hat zarządza wszystkimi podstawowej infrastruktury (na przykład maszyny wirtualne, OpenShift klastra, sieci i magazynu). 

Z tą wersją klienta służy do wdrażania kontenerów, ale nie ma kontroli nad hosta, którego uruchamianie kontenerów. Ponieważ Online jest wielodostępne, kontenerów może znajdować się na tej samej hostów maszyn wirtualnych w postaci kontenerów od innych klientów. Koszt jest na kontener.

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie wspólne wymagania wstępne dla rozwiązania OpenShift na platformie Azure](./openshift-prerequisites.md)
- [Wdrażanie rozwiązania OpenShift Container Platform na platformie Azure](./openshift-container-platform.md)
- [Wdrażanie OKD na platformie Azure](./openshift-okd.md)
- [Wdrażanie platformy OpenShift w usłudze Azure Stack](./openshift-azure-stack.md)
- [Po wdrożeniu zadania](./openshift-post-deployment.md)
- [Rozwiązywanie problemów z wdrożenia platformy OpenShift](./openshift-troubleshooting.md)
