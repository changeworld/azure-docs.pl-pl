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
ms.date: 03/01/2019
ms.author: haroldw
ms.openlocfilehash: 826085df8d928cab0a05527be8c464af5f4e9180
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002519"
---
# <a name="openshift-in-azure"></a>OpenShift na platformie Azure

OpenShift to platforma aplikacji kontenera otwartej i rozszerzalnej prawdziwym platformy Docker i Kubernetes przedsiębiorstwa.  

OpenShift obejmuje Kubernetes orkiestracji kontenerów i zarządzania. Dodaje skoncentrowany na deweloperach i skoncentrowane na operacje narzędzi Włącz tego:

- Szybkie opracowywanie aplikacji.
- Łatwe wdrażanie i skalowanie.
- Długoterminowe obsługi cyklu życia dla aplikacji i zespołów.

Brak dostępnych wiele wersji platformy OpenShift:

- OpenShift Container Platform
- Platforma OpenShift na platformie Azure (w pełni zarządzane OpenShift mieszczących się wokół koniec CY2019 P1)
- OKD (dawniej platformy OpenShift Origin)
- W wersji dedykowanej platformy OpenShift
- OpenShift Online

Tylko dwa pięć wersji omówione w tym artykule są obecnie dostępne dla klientów do wdrożenia na platformie Azure: OpenShift Container Platform i OKD.

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Platforma kontenera jest przedsiębiorstw [komercyjnej wersji](https://www.openshift.com) z i obsługiwane przez firmy Red Hat. W tej wersji klienci zakupu niezbędnych uprawnień dla rozwiązania OpenShift Container Platform i jest odpowiedzialny za instalację i zarządzanie całą infrastrukturą.

Ponieważ klienci "własnością" tej platformy, mogą ją zainstalować w ich lokalnym centrum danych lub w chmurze publicznej (np. Azure).

## <a name="openshift-on-azure"></a>OpenShift na platformie Azure

Platforma OpenShift na platformie Azure jest w pełni zarządzana oferta platformy OpenShift działających na platformie Azure. Ta usługa jest wspólnie zarządzane i obsługiwane przez firmę Microsoft i Red Hat. Klaster zostanie wdrożony w subskrypcji platformy Azure przez klienta. Usługa jest obecnie dostępna w prywatnej wersji zapoznawczej i zostanie GA koniec CY2019 P1. W przypadku klientów wziąć udział w prywatnej wersji zapoznawczej, prosimy o wypełnienie [formularz nominacji](https://aka.ms/openshiftazureinterest).  Więcej informacje będą dostarczane jako oferty pobiera bliżej GA.

## <a name="okd-formerly-openshift-origin"></a>OKD (dawniej platformy OpenShift Origin)

Jest OKD [typu open-source](https://www.okd.io/) nadrzędnego projektu OpenShift, który jest wspieranym przez społeczność. OKD można zainstalować na CentOS i Red Hat Enterprise Linux (RHEL).

## <a name="openshift-dedicated"></a>W wersji dedykowanej platformy OpenShift

Dedykowany jest zarządzana przez firmy Red Hat *pojedynczej dzierżawy* OpenShift, który używa OpenShift Container Platform. Red Hat zarządza wszystkimi podstawowej infrastruktury (maszyny wirtualne, klaster OpenShift, sieć, Magazyn itp.). Klaster jest przeznaczony dla jednego klienta i działa w chmurze publicznej (np. Azure). Począwszy od klastra zawiera cztery węzły aplikacji i wszelkie koszty są rocznych i płatnej z góry.

## <a name="openshift-online"></a>OpenShift Online

Online jest zarządzana przez firmy Red Hat *wielodostępnych* OpenShift, używającej platformę kontenerów. Red Hat zarządza wszystkimi podstawowej infrastruktury (na przykład maszyny wirtualne, OpenShift klastra, sieci i magazynu). 

Z tą wersją klienta służy do wdrażania kontenerów, ale nie ma kontroli nad hosta, którego uruchamianie kontenerów. Ponieważ Online jest wielodostępne, kontenerów może znajdować się na tej samej hostów maszyn wirtualnych w postaci kontenerów od innych klientów. Koszt jest na kontener.

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie wspólne wymagania wstępne dla rozwiązania OpenShift na platformie Azure](./openshift-prerequisites.md)
- [Wdrażanie rozwiązania OpenShift Container Platform na platformie Azure](./openshift-container-platform.md)
- [Wdrażanie OKD na platformie Azure](./openshift-okd.md)
- [Wdrażanie platformy OpenShift w usłudze Azure Stack](./openshift-azure-stack.md)
- [Po wdrożeniu zadania](./openshift-post-deployment.md)
- [Rozwiązywanie problemów z wdrożenia platformy OpenShift](./openshift-troubleshooting.md)
