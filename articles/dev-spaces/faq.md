---
title: Często zadawane pytania dotyczące Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/25/2019
ms.topic: conceptual
description: Znajdź odpowiedzi na niektóre często zadawane pytania dotyczące Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s '
ms.openlocfilehash: de44bf323b300a4467dc8b30555798f557898494
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71305979"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Często zadawane pytania dotyczące Azure Dev Spaces

Dotyczy to często zadawanych pytań dotyczących Azure Dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Które regiony platformy Azure są obecnie dostępne Azure Dev Spaces?

Aby uzyskać pełną listę dostępnych regionów, zobacz [Obsługiwane regiony i konfiguracje][supported-regions].

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Czy mogę używać Azure Dev Spaces bez publicznego adresu IP?

Nie, nie można zainicjować obsługi administracyjnej Azure Dev Spaces w klastrze AKS bez publicznego adresu IP. Publiczny adres IP jest [wymagany przez Azure dev Spaces do routingu][dev-spaces-routing].

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Czy mogę użyć własnych danych przychodzących z Azure Dev Spaces?

Tak, możesz skonfigurować własny ruch przychodzący po stronie Azure Dev Spaces tworzy. Na przykład można użyć [traefik][ingress-traefik].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Czy mogę używać protokołu HTTPS z Azure Dev Spaces?

Tak, możesz skonfigurować własny ruch przychodzący przy użyciu protokołu HTTPS za pomocą [traefik][ingress-https-traefik].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Czy mogę używać Azure Dev Spaces w klastrze korzystającym z CNI zamiast korzystającą wtyczki kubenet? 

Tak, można użyć Azure Dev Spaces w klastrze AKS, który używa CNI do obsługi sieci. Na przykład można użyć Azure Dev Spaces w klastrze AKS z [istniejącymi kontenerami systemu Windows][windows-containers], które używają CNI do obsługi sieci.

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Czy mogę używać Azure Dev Spaces z kontenerami systemu Windows?

Obecnie Azure Dev Spaces ma być uruchamiany tylko w przypadku systemów Linux i w węzłach, ale można uruchomić Azure Dev Spaces w klastrze AKS z [istniejącymi kontenerami systemu Windows][windows-containers].


[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: about.md#supported-regions-and-configurations
[windows-containers]: how-to/run-dev-spaces-windows-containers.md