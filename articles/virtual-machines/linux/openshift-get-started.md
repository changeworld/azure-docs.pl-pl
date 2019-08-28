---
title: OpenShift na platformie Azure — omówienie | Microsoft Docs
description: Omówienie OpenShift na platformie Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: 80f0ec57aa287bae9b10d9a4cdb013014aab3332
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70082556"
---
# <a name="openshift-in-azure"></a>Usługa OpenShift na platformie Azure

OpenShift to otwarta i rozszerzalna platforma aplikacji kontenerów, która udostępnia platformę Docker i Kubernetes do przedsiębiorstwa.  

OpenShift obejmuje Kubernetes do organizowania i zarządzania kontenerami. Dodaje narzędzia skoncentrowane na deweloperach i oparte na operacjach, które umożliwiają:

- Szybkie opracowywanie aplikacji.
- Łatwe wdrażanie i skalowanie.
- Długoterminowa konserwacja dla zespołów i aplikacji.

Dostępnych jest wiele wersji OpenShift.  W tych wersjach obecnie klienci mogą wdrażać na platformie Azure tylko dwie te wersje: Platforma kontenera OpenShift i OKD (dawniej pochodzenie OpenShift).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift to w pełni zarządzana oferta OpenShift działającego na platformie Azure. Ta usługa jest wspólnie zarządzana i obsługiwana przez firmę Microsoft i Red Hat. Aby uzyskać więcej informacji, zobacz dokumentację [usługi Azure Red Hat OpenShift](https://docs.microsoft.com/azure/openshift/) .

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Platforma kontenerów to [wersja komercyjna](https://www.openshift.com) przeznaczona dla przedsiębiorstw, obsługiwana przez firmę Red Hat. W tej wersji klienci kupują niezbędne uprawnienia dla platformy kontenera OpenShift i są odpowiedzialni za instalację i zarządzanie całą infrastrukturą.

Ze względu na to, że klienci są własnością całej platformy, mogą ją zainstalować w lokalnych centrach danych lub w chmurze publicznej (na przykład na platformie Azure).

## <a name="okd"></a>OKD

OKD to projekt typu ["open source"](https://www.okd.io/) OpenShift, który jest obsługiwany przez społeczność. OKD można zainstalować w CentOS lub Red Hat Enterprise Linux (RHEL).

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie typowych wymagań wstępnych dotyczących programu OpenShift na platformie Azure](./openshift-prerequisites.md)
- [Wdrażanie platformy kontenera OpenShift na platformie Azure](./openshift-container-platform.md)
- [Wdróż niezarządzaną ofertę portalu OpenShift platformy kontenera](./openshift-marketplace-self-managed.md)
- [Wdróż OpenShift w Azure Stack](./openshift-azure-stack.md)
- [Zadania po wdrożeniu](./openshift-post-deployment.md)
- [Rozwiązywanie problemów z wdrażaniem OpenShift](./openshift-troubleshooting.md)
