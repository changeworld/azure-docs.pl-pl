---
title: Często zadawane pytania dotyczące Azure Dev Spaces
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: conceptual
description: Znajdź odpowiedzi na niektóre często zadawane pytania dotyczące Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s '
ms.openlocfilehash: c904ae5809a36859ba6428bf026c9016a1a8f747
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867192"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Często zadawane pytania dotyczące Azure Dev Spaces

Dotyczy to często zadawanych pytań dotyczących Azure Dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Które regiony platformy Azure są obecnie dostępne Azure Dev Spaces?

Aby uzyskać pełną listę dostępnych regionów, zobacz sekcję [Obsługiwane regiony][supported-regions] .

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Czy mogę używać Azure Dev Spaces bez publicznego adresu IP?

Nie, nie można zainicjować obsługi administracyjnej Azure Dev Spaces w klastrze AKS bez publicznego adresu IP. Publiczny adres IP jest [wymagany przez Azure dev Spaces do routingu][dev-spaces-routing].

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Czy mogę użyć własnych danych przychodzących z Azure Dev Spaces?

Tak, możesz skonfigurować własny ruch przychodzący po stronie Azure Dev Spaces tworzy. Na przykład można użyć [traefik][ingress-traefik].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Czy mogę używać protokołu HTTPS z Azure Dev Spaces?

Tak, możesz skonfigurować własny ruch przychodzący przy użyciu protokołu HTTPS za pomocą [traefik][ingress-https-traefik].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Czy mogę używać Azure Dev Spaces w klastrze korzystającym z CNI zamiast korzystającą wtyczki kubenet? 

Tak, można użyć Azure Dev Spaces w klastrze AKS, który używa CNI do obsługi sieci. Na przykład można użyć Azure Dev Spaces w klastrze AKS z [istniejącymi kontenerami systemu Windows][windows-containers], które używają CNI do obsługi sieci. Więcej informacji na temat korzystania z CNI dla sieci z Azure Dev Spaces jest dostępna [tutaj](configure-networking.md#using-azure-container-networking-with-azure-dev-spaces).

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Czy mogę używać Azure Dev Spaces z kontenerami systemu Windows?

Obecnie Azure Dev Spaces ma być uruchamiany tylko w przypadku systemów Linux i w węzłach, ale można uruchomić Azure Dev Spaces w klastrze AKS z [istniejącymi kontenerami systemu Windows][windows-containers].

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Czy można używać Azure Dev Spaces w klastrach AKS z włączonymi zakresami adresów IP autoryzowanych serwerów interfejsu API?

Tak, można użyć Azure Dev Spaces w klastrach AKS z włączonymi [zakresami adresów IP autoryzowanych przez serwer interfejsu API][aks-auth-range] . Więcej informacji na temat korzystania z klastrów AKS z dozwolonymi zakresami adresów IP serwera interfejsu API w Azure Dev Spaces jest dostępny [tutaj](configure-networking.md#using-api-server-authorized-ip-ranges-with-azure-dev-spaces).

### <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Czy można używać Azure Dev Spaces w klastrach AKS z ograniczonym ruchem wychodzącym dla węzłów klastra?

Tak, można użyć Azure Dev Spaces w klastrach AKS z [ograniczonym ruchem wychodzącym dla węzłów klastra][aks-restrict-egress-traffic] włączonych po dodaniu prawidłowych nazw FQDN. Więcej informacji na temat używania klastrów AKS z ograniczonym ruchem wychodzącym dla węzłów klastra z włączonym Azure Dev Spaces jest dostępny [tutaj](configure-networking.md#ingress-and-egress-network-traffic-requirements).

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[windows-containers]: how-to/run-dev-spaces-windows-containers.md