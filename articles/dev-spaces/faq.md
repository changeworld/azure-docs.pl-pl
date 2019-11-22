---
title: Często zadawane pytania dotyczące Azure Dev Spaces
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: conceptual
description: Znajdź odpowiedzi na niektóre często zadawane pytania dotyczące Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s '
ms.openlocfilehash: 3c7335f1656d304d231c2146c8b7496ea43f0b4c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280235"
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

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Czy można używać Azure Dev Spaces w klastrach AKS z włączonymi zakresami adresów IP autoryzowanych serwerów interfejsu API?

Tak, można użyć Azure Dev Spaces w klastrach AKS z włączonymi [zakresami adresów IP autoryzowanych przez serwer interfejsu API][aks-auth-range] . Podczas [tworzenia][aks-auth-range-create] klastra należy [zezwolić na dodatkowe zakresy w zależności od regionu][aks-auth-range-ranges]. Można również [zaktualizować][aks-auth-range-update] istniejący klaster, aby zezwalał na te dodatkowe zakresy.

### <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Czy można używać Azure Dev Spaces w klastrach AKS z ograniczonym ruchem wychodzącym dla węzłów klastra?

Tak, można użyć Azure Dev Spaces w klastrach AKS z [ograniczonym ruchem wychodzącym dla węzłów klastra][aks-restrict-egress-traffic] włączonych po dodaniu następujących nazw FQDN:

| NAZWA FQDN                                    | Port      | Użycie      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Aby ściągnąć obrazy z systemem Linux Alpine i innymi Azure Dev Spaces |
| gcr.io | HTTP: 443 | Aby ściągnąć obrazy Helm/er|
| storage.googleapis.com | HTTP: 443 | Aby ściągnąć obrazy Helm/er|


[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: about.md#supported-regions-and-configurations
[windows-containers]: how-to/run-dev-spaces-windows-containers.md