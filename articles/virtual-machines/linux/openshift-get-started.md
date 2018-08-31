---
title: OpenShift w usłudze Azure — omówienie | Dokumentacja firmy Microsoft
description: Omówienie platformy OpenShift na platformie Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
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
ms.openlocfilehash: e3ab060c1cea28f83c18dc89aeea7716ec86572a
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190347"
---
# <a name="openshift-in-azure"></a>OpenShift na platformie Azure

OpenShift to platforma aplikacji kontenera otwartej i rozszerzalnej prawdziwym platformy Docker i Kubernetes przedsiębiorstwa.  

OpenShift obejmuje Kubernetes orkiestracji kontenerów i zarządzania. Dodaje dla deweloperów i operacji skoncentrowane na narzędzia, które umożliwiają:

- Szybkie opracowywanie aplikacji.
- Łatwe wdrażanie i skalowanie.
- Długoterminowe obsługi cyklu życia dla aplikacji i zespołów.

Brak dostępnych wiele wersji platformy OpenShift:

- OKD (dawniej platformy OpenShift Origin)
- OpenShift Container Platform
- OpenShift w trybie Online
- W wersji dedykowanej platformy OpenShift

Cztery wersji omówione w tym artykule tylko dwa są dostępne dla klientów do wdrożenia na platformie Azure: platformy OpenShift Origin i OpenShift Container Platform.

## <a name="okd-formerly-openshift-origin"></a>OKD (dawniej platformy OpenShift Origin)

Jest OKD [typu open-source](https://www.okd.io/) nadrzędnego projektu OpenShift, który jest wspieranym przez społeczność. OKD można zainstalować na CentOS i Red Hat Enterprise Linux (RHEL).

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Platforma kontenera jest przedsiębiorstw [komercyjnej wersji](https://www.openshift.com) z i obsługiwane przez firmy Red Hat. W tej wersji klienci zakupu niezbędnych uprawnień dla rozwiązania OpenShift Container Platform i jest odpowiedzialny za instalację i zarządzanie całą infrastrukturą.

Ponieważ klienci "własnością" tej platformy, mogą ją zainstalować w ich lokalnym centrum danych lub w chmurze publicznej (np. Azure, AWS lub Google).

## <a name="openshift-online"></a>OpenShift w trybie Online

Online jest zarządzana przez firmy Red Hat *wielodostępnych* OpenShift, używającej platformę kontenerów. Red Hat zarządza wszystkimi podstawowej infrastruktury (na przykład maszyny wirtualne, OpenShift klastra, sieci i magazynu). 

Z tą wersją klienta służy do wdrażania kontenerów, ale nie ma kontroli nad hosta, którego uruchamianie kontenerów. Ponieważ Online jest wielodostępne, kontenerów może znajdować się na tej samej hostów maszyn wirtualnych w postaci kontenerów od innych klientów. Koszt jest na kontener.

## <a name="openshift-dedicated"></a>W wersji dedykowanej platformy OpenShift

Dedykowany jest zarządzana przez firmy Red Hat *pojedynczej dzierżawy* OpenShift, używającej platformę kontenerów. Red Hat zarządza wszystkimi podstawowej infrastruktury (maszyny wirtualne, klaster OpenShift, sieć, Magazyn itp.). Klaster jest przeznaczony dla jednego klienta i działa w chmurze publicznej (np. usług AWS lub Google dzięki platformie Azure zostaną dodane w 2018 r.). Począwszy od klastra zawiera cztery węzły aplikacji 48 000 USD rocznie (płatność na początku).

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie wspólne wymagania wstępne dla rozwiązania OpenShift na platformie Azure](./openshift-prerequisites.md)
- [Wdrażanie platformy OpenShift Origin na platformie Azure](./openshift-origin.md)
- [Wdrażanie rozwiązania OpenShift Container Platform na platformie Azure](./openshift-container-platform.md)
- [Po wdrożeniu zadania](./openshift-post-deployment.md)
- [Rozwiązywanie problemów z wdrożenia platformy OpenShift](./openshift-troubleshooting.md)
