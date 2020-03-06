---
title: Konfigurowanie sieci dla Azure Dev Spaces w różnych topologiach sieci
services: azure-dev-spaces
ms.date: 01/10/2020
ms.topic: conceptual
description: Opisuje wymagania sieciowe dotyczące uruchamiania Azure Dev Spaces w usłudze Azure Kubernetes Services
keywords: Azure Dev Spaces, Spaces dev, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, CNI, korzystającą wtyczki kubenet, SDN, Network
ms.openlocfilehash: 9e32e3b65451dceefaeeaf7faed7c8337797e0b8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389689"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>Konfigurowanie sieci dla Azure Dev Spaces w różnych topologiach sieci

Azure Dev Spaces działa w klastrach usługi Azure Kubernetes Service (AKS) z domyślną konfiguracją sieci. Jeśli chcesz zmienić konfigurację sieciową klastra AKS, na przykład przełączenie klastra za zaporą, użycie sieciowych grup zabezpieczeń lub zastosowanie zasad sieciowych, musisz uwzględnić dodatkowe zagadnienia dotyczące uruchamiania Azure Dev Spaces.

![Konfiguracja sieci wirtualnej](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>Konfiguracje sieci wirtualnej lub podsieci

Klaster AKS może mieć inną konfigurację sieci wirtualnej lub podsieci w celu ograniczenia ruchu przychodzącego lub wychodzącego dla klastra AKS. Na przykład klaster może znajdować się za zaporą, taką jak Zapora platformy Azure, lub można użyć sieciowych grup zabezpieczeń lub ról niestandardowych do ograniczania ruchu sieciowego.

Azure Dev Spaces ma pewne wymagania dotyczące ruchu przychodzącego *i* wychodzącego w sieci, jak *również ruchu* przychodzącego. Jeśli używasz Azure Dev Spaces w klastrze AKS z konfiguracją sieci wirtualnej lub podsieci, która ogranicza ruch dla klastra AKS, należy wykonać następujące czynności związane z ruchem przychodzącym i transferem danych przychodzących i wychodzących, aby Azure Dev Spaces do działa prawidłowo.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Wymagania dotyczące ruchu przychodzącego i wychodzącego w sieci

Azure Dev Spaces wymaga ruchu przychodzącego i wychodzącego dla następujących nazw FQDN:

| NAZWA FQDN                       | Port       | Użycie      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Aby ściągnąć obrazy platformy Docker dla Azure Dev Spaces |
| gcr.io                     | HTTPS: 443 | Aby ściągnąć obrazy Helm dla Azure Dev Spaces |
| storage.googleapis.com     | HTTPS: 443 | Aby ściągnąć obrazy Helm dla Azure Dev Spaces |
| azds-*. azds. IO             | HTTPS: 443 | Aby komunikować się z usługami Azure Dev Spaces zaplecza dla kontrolera Azure Dev Spaces. Dokładną nazwę FQDN można znaleźć w *dataplaneFqdn* w `USERPROFILE\.azds\settings.json` |

Zaktualizuj zaporę lub konfigurację zabezpieczeń, aby zezwalać na ruch sieciowy do i z wszystkich powyższych nazw FQDN. Na przykład jeśli używasz zapory do zabezpieczenia sieci, powyższe nazwy FQDN należy dodać do reguły aplikacji zapory, aby zezwolić na ruch do i z tych domen.

### <a name="ingress-only-network-traffic-requirements"></a>Tylko wymagania dotyczące ruchu sieciowego

Azure Dev Spaces zapewnia Routing na poziomie przestrzeni nazw Kubernetes oraz publiczny dostęp do usług przy użyciu własnej nazwy FQDN. Aby obie te funkcje działały, zaktualizuj konfigurację zapory lub sieci tak, aby zezwalała na publiczny ruch przychodzący do zewnętrznego adresu IP Azure Dev Spaces kontrolera transferu danych przychodzących w klastrze. Alternatywnie można utworzyć [wewnętrzny moduł równoważenia obciążenia][aks-internal-lb] i dodać regułę NAT w zaporze, aby przetłumaczyć publiczny adres IP zapory na adres IP wewnętrznego modułu równoważenia obciążenia. Można również użyć [traefik][traefik-ingress] lub [Nginx][nginx-ingress] , aby utworzyć niestandardowy kontroler transferu danych przychodzących.

## <a name="aks-cluster-network-requirements"></a>Wymagania dotyczące sieci klastrów AKS

AKS umożliwia korzystanie z [zasad sieciowych][aks-network-policies] w celu kontrolowania ruchu przychodzącego i wychodzącego między jednostkami w klastrze, a także ruchem wychodzącym z pod. Azure Dev Spaces ma pewne wymagania dotyczące ruchu przychodzącego *i* wychodzącego w sieci, jak *również ruchu* przychodzącego. Jeśli używasz Azure Dev Spaces w klastrze AKS z zasadami sieci AKS, musisz postępować zgodnie z wymaganiami dotyczącymi ruchu przychodzącego i ruchem przychodzącym, aby funkcja Azure Dev Spaces działała prawidłowo.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Wymagania dotyczące ruchu przychodzącego i wychodzącego w sieci

Azure Dev Spaces pozwala komunikować się bezpośrednio z obszarem w środowisku deweloperskim w klastrze w celu debugowania. Aby ta funkcja działała, należy dodać zasady sieciowe zezwalające na ruch przychodzący i wychodzący do adresów IP infrastruktury Azure Dev Spaces, które [różnią się w zależności od regionu][dev-spaces-ip-auth-range-regions].

### <a name="ingress-only-network-traffic-requirements"></a>Tylko wymagania dotyczące ruchu sieciowego

Azure Dev Spaces udostępnia Routing między przestrzeniami nazw między obszarami. Na przykład przestrzenie nazw z włączonym Azure Dev Spaces mogą mieć relację nadrzędny/podrzędny, co umożliwia kierowanie ruchu sieciowego między jednostkami w przestrzeni nazw nadrzędnych i podrzędnych. Aby ta funkcja działała, należy dodać zasady sieciowe zezwalające na ruch między przestrzeniami nazw, w których jest kierowany ruch sieciowy, taki jak przestrzenie nazw nadrzędny/podrzędny. Ponadto, Jeśli kontroler transferu danych przychodzących jest wdrożony w przestrzeni nazw *azds* , kontroler transferu danych przychodzących musi komunikować się z nazwami urządzeń przystosowanymi przez usługę Azure dev Space w innej przestrzeni nazw. Aby kontroler transferu danych przychodzących działał prawidłowo, ruch sieciowy musi być dozwolony z przestrzeni nazw *azds* do przestrzeni nazw, w której są uruchomione Instrumentacja miara.

## <a name="using-azure-cni"></a>Korzystanie z usługi Azure CNI

Domyślnie klastry AKS są skonfigurowane do używania [korzystającą wtyczki kubenet][aks-kubenet] dla sieci, które współdziałają z Azure dev Spaces. Możesz również skonfigurować klaster AKS, aby korzystał z [interfejsu Azure Container Network Interface (CNI)][aks-cni]. Aby użyć Azure Dev Spaces z platformą Azure CNI w klastrze AKS, Zezwól na przestrzeń adresową sieci wirtualnej i podsieci do 10 prywatnych adresów IP dla wdrożonych przez Azure Dev Spaces. Więcej szczegółowych informacji na temat zezwalania na prywatne adresy IP można znaleźć w [dokumentacji usługi AKS Azure CNI][aks-cni-ip-planning].

## <a name="using-api-server-authorized-ip-ranges"></a>Korzystanie z autoryzowanych zakresów adresów IP serwera interfejsu API

Klastry AKS umożliwiają skonfigurowanie dodatkowych zabezpieczeń, które ograniczają, który adres IP może współdziałać z klastrami, na przykład przy użyciu niestandardowych sieci wirtualnych lub [zabezpieczania dostępu do serwera interfejsu API przy użyciu autoryzowanych zakresów adresów IP][aks-ip-auth-ranges]. Aby użyć Azure Dev Spaces podczas korzystania z tego dodatkowego zabezpieczenia podczas [tworzenia][aks-ip-auth-range-create] klastra, należy [zezwolić na dodatkowe zakresy w zależności od regionu][dev-spaces-ip-auth-range-regions]. Można również [zaktualizować][aks-ip-auth-range-update] istniejący klaster, aby zezwalał na te dodatkowe zakresy. Należy również zezwolić na adres IP wszystkich maszyn deweloperskich, które łączą się z klastrem AKS w celu debugowania w celu nawiązania połączenia z serwerem interfejsu API.

## <a name="using-aks-private-clusters"></a>Używanie prywatnych klastrów AKS

W tej chwili Azure Dev Spaces nie jest obsługiwana w przypadku [prywatnych klastrów AKS][aks-private-clusters].

## <a name="client-requirements"></a>Wymagania dotyczące klienta

Azure Dev Spaces używa narzędzi po stronie klienta, takich jak rozszerzenie interfejsu wiersza polecenia Azure Dev Spaces, rozszerzenia Visual Studio Code i rozszerzenia programu Visual Studio, aby komunikować się z klastrem AKS na potrzeby debugowania. Aby użyć Azure Dev Spaces narzędzia po stronie klienta, Zezwól na ruch z maszyn deweloperskich do domeny *azds-\*. azds.IO* . Zobacz *dataplaneFqdn* w `USERPROFILE\.azds\settings.json` dla dokładnej nazwy FQDN. W przypadku korzystania z [dozwolonych zakresów adresów IP serwera interfejsu API][auth-range-section]należy również zezwolić na adres IP wszystkich maszyn deweloperskich, które łączą się z klastrem AKS w celu debugowania w celu nawiązania połączenia z serwerem interfejsu API.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak Azure Dev Spaces ułatwiają tworzenie bardziej złożonych aplikacji w wielu kontenerach i jak można uprościć programowanie do współpracy, pracując z różnymi wersjami lub gałęziami kodu w różnych miejscach.

> [!div class="nextstepaction"]
> [Programowanie zespołowe w Azure Dev Spaces][team-quickstart]

[aks-cni]: ../aks/configure-azure-cni.md
[aks-cni-ip-planning]: ../aks/configure-azure-cni.md#plan-ip-addressing-for-your-cluster
[aks-kubenet]: ../aks/configure-kubenet.md
[aks-internal-lb]: ../aks/internal-lb.md
[aks-ip-auth-ranges]: ../aks/api-server-authorized-ip-ranges.md
[aks-ip-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-ip-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-network-policies]: ../aks/use-network-policies.md
[aks-private-clusters]: ../aks/private-clusters.md
[auth-range-section]: #using-api-server-authorized-ip-ranges
[dev-spaces-ip-auth-range-regions]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[team-quickstart]: quickstart-team-development.md