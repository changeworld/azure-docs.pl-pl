---
title: Konfigurowanie sieci dla usługi Azure Dev Spaces w różnych topologiach sieci
services: azure-dev-spaces
ms.date: 03/17/2020
ms.topic: conceptual
description: Zawiera opis wymagań dotyczących sieci przy uruchamianiu usługi Azure Dev Spaces w usługach Azure Kubernetes
keywords: Usługi Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, CNI, kubenet, SDN, network
ms.openlocfilehash: 82d046aa36fe9caf6337aa7f58ca0db525062283
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240568"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>Konfigurowanie sieci dla usługi Azure Dev Spaces w różnych topologiach sieci

Usługa Azure Dev Spaces jest uruchamiana w klastrach usługi Azure Kubernetes (AKS) z domyślną konfiguracją sieci. Jeśli chcesz zmienić konfigurację sieci klastra AKS, na przykład umieszczenie klastra za zaporą, przy użyciu sieciowych grup zabezpieczeń lub przy użyciu zasad sieciowych, musisz uwzględnić dodatkowe zagadnienia dotyczące uruchamiania usługi Azure Dev Spaces.

![Konfiguracja sieci wirtualnej](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>Konfiguracje sieci wirtualnej lub podsieci

Klaster usługi AKS może mieć inną konfigurację sieci wirtualnej lub podsieci w celu ograniczenia ruchu przychodzącego lub wychodzącego dla klastra AKS. Na przykład klaster może znajdować się za zaporą, taką jak Zapora azure, lub można użyć sieciowych grup zabezpieczeń lub ról niestandardowych do ograniczania ruchu sieciowego.

Usługa Azure Dev Spaces ma pewne wymagania dotyczące ruchu *sieciowego przychodzącego i wychodzącego,* a także ruchu *tylko przychodzących.* Jeśli usługa Azure Dev Spaces jest korzystać z usługi Azure Dev Spaces w klastrze AKS z konfiguracją sieci wirtualnej lub podsieci, która ogranicza ruch dla klastra AKS, należy przestrzegać następujących wymagań dotyczących ruchu przychodzącego i ruchu wychodzącego, aby usługa Azure Dev Spaces prawidłowo działać.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Wymagania dotyczące ruchu sieciowego przychodzącego i wychodzącego

Usługa Azure Dev Spaces wymaga ruchu przychodzącego i wychodzącego dla następujących sieci FQDN:

| Nazwa FQDN                       | Port       | Użycie      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | Protokół HTTPS: 443 | Aby pobierać obrazy dokowane dla usługi Azure Dev Spaces |
| gcr.io                     | Protokół HTTPS: 443 | Aby wyciągnąć obrazy sterów dla usługi Azure Dev Spaces |
| storage.googleapis.com     | Protokół HTTPS: 443 | Aby wyciągnąć obrazy sterów dla usługi Azure Dev Spaces |
| azds-*.azds.io             | Protokół HTTPS: 443 | Aby komunikować się z usługami zaplecza usługi Azure Dev Spaces dla kontrolera azure dev spaces. Dokładną FQDN można znaleźć w *dataplaneFqdn* w`USERPROFILE\.azds\settings.json` |

Zaktualizuj konfigurację zapory lub zabezpieczeń, aby zezwolić na ruch sieciowy do i ze wszystkich powyższych sieci FQDN. Jeśli na przykład używasz zapory do zabezpieczania sieci, powyższe nazwy FQDN powinny zostać dodane do reguły aplikacji zapory, aby zezwolić na ruch do i z tych domen.

### <a name="ingress-only-network-traffic-requirements"></a>Wymagania dotyczące ruchu sieciowego tylko przy ruchu sieciowego

Usługa Azure Dev Spaces zapewnia routing na poziomie obszaru nazw usługi Kubernetes, a także publiczny dostęp do usług przy użyciu własnej nazwy FQDN. Aby obie te funkcje działały, zaktualizuj zaporę lub konfigurację sieci, aby umożliwić publiczny przystawek do zewnętrznego adresu IP kontrolera transferu danych przychodzących usługi Azure Dev Spaces w klastrze. Alternatywnie można utworzyć [wewnętrzny moduł równoważenia obciążenia][aks-internal-lb] i dodać regułę NAT w zaporze, aby przetłumaczyć publiczny adres IP zapory na adres IP wewnętrznego modułu równoważenia obciążenia. Można również użyć [traefik][traefik-ingress] lub [NGINX,][nginx-ingress] aby utworzyć niestandardowy kontroler transferu danych przychodzących.

## <a name="aks-cluster-network-requirements"></a>Wymagania sieci klastra AKS

Usługa AKS umożliwia używanie [zasad sieciowych][aks-network-policies] do kontrolowania ruchu przychodzącego i wychodzącego między zasobnikami w klastrze, a także ruchu wychodzącego z zasobnika. Usługa Azure Dev Spaces ma pewne wymagania dotyczące ruchu *sieciowego przychodzącego i wychodzącego,* a także ruchu *tylko przychodzących.* Jeśli używasz usługi Azure Dev Spaces w klastrze AKS z zasadami sieciowymi usługi AKS, należy wykonać następujące wymagania dotyczące ruchu przychodzącego i ruchu wychodzącego, aby usługa Azure Dev Spaces działała poprawnie.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Wymagania dotyczące ruchu sieciowego przychodzącego i wychodzącego

Usługa Azure Dev Spaces umożliwia komunikację bezpośrednio z zasobnikiem w obszarze deweloperskim w klastrze do debugowania. Aby ta funkcja działała, dodaj zasadę sieciową, która umożliwia komunikację przychodzącą i wychodzącą z adresami IP infrastruktury Azure Dev Spaces, które [różnią się w zależności od regionu.][dev-spaces-ip-auth-range-regions]

### <a name="ingress-only-network-traffic-requirements"></a>Wymagania dotyczące ruchu sieciowego tylko przy ruchu sieciowego

Usługa Azure Dev Spaces zapewnia routing między zasobnikami między obszarami nazw. Na przykład obszary nazw z włączoną usługą Azure Dev Spaces mogą mieć relację nadrzędny/podrzędny, która umożliwia kierowanie ruchu sieciowego między zasobnikami między obszarami nazw nadrzędnymi i podrzędnymi. Usługa Azure Dev Spaces udostępnia również punkty końcowe usługi przy użyciu własnej funkcji FQDN. Aby skonfigurować różne sposoby ujawniania usług i wpływa na routing poziomu obszaru nazw, zobacz [Korzystanie z różnych opcji punktu końcowego][endpoint-options].

## <a name="using-azure-cni"></a>Korzystanie z usługi Azure CNI

Domyślnie klastry AKS są skonfigurowane do [używania kubenet][aks-kubenet] do sieci, która współpracuje z usługi Azure Dev Spaces. Klaster AKS można również skonfigurować tak, aby używał [interfejsu sieci kontenerów azure (CNI).][aks-cni] Aby korzystać z usługi Azure Dev Spaces z usługą Azure CNI w klastrze AKS, należy zezwolić na przestrzenie adresów sieci wirtualnej i podsieci do 10 prywatnych adresów IP dla zasobników wdrożonych przez usługi Azure Dev Spaces. Więcej szczegółów na temat zezwalania na prywatne adresy IP są dostępne w [dokumentacji CNI usługi AKS Azure][aks-cni-ip-planning].

## <a name="using-api-server-authorized-ip-ranges"></a>Korzystanie z autoryzowanych zakresów adresów IP serwera INTERFEJSU API

Klastry AKS umożliwiają skonfigurowanie dodatkowych zabezpieczeń, które ograniczają adres IP, który może wchodzić w interakcje z klastrami, na przykład przy użyciu niestandardowych sieci wirtualnych lub [zabezpieczaniu dostępu do serwera interfejsu API przy użyciu autoryzowanych zakresów adresów IP][aks-ip-auth-ranges]. Aby używać usługi Azure Dev Spaces podczas korzystania z tego dodatkowego zabezpieczenia podczas [tworzenia][aks-ip-auth-range-create] klastra, należy [zezwolić na dodatkowe zakresy na podstawie regionu][dev-spaces-ip-auth-range-regions]. Można również [zaktualizować][aks-ip-auth-range-update] istniejący klaster, aby zezwolić na te dodatkowe zakresy. Należy również zezwolić adres IP wszystkich maszyn deweloperskich, które łączą się z klastrem AKS do debugowania, aby połączyć się z serwerem interfejsu API.

## <a name="using-aks-private-clusters"></a>Korzystanie z klastrów prywatnych usługi AKS

Obecnie usługa Azure Dev Spaces nie jest obsługiwana w [klastrach prywatnych usługi AKS.][aks-private-clusters]

## <a name="using-different-endpoint-options"></a>Korzystanie z różnych opcji punktu końcowego

Usługa Azure Dev Spaces ma możliwość udostępnienia punktów końcowych dla usług uruchomionych w usłudze AKS. Po włączeniu usługi Azure Dev Spaces w klastrze dostępne są następujące opcje konfigurowania typu punktu końcowego dla klastra:

* *Publiczny* punkt końcowy, który jest domyślny, wdraża kontroler transferu danych przychodzących z publicznym adresem IP. Publiczny adres IP jest zarejestrowany w systemie DNS klastra, umożliwiając publiczny dostęp do usług przy użyciu adresu URL. Ten adres URL `azds list-uris`można wyświetlić za pomocą programu .
* *Prywatny* punkt końcowy wdraża kontroler transferu danych przychodzących z prywatnym adresem IP. W przypadku prywatnego adresu IP moduł równoważenia obciążenia klastra jest dostępny tylko z sieci wirtualnej klastra. Prywatny adres IP modułu równoważenia obciążenia jest zarejestrowany w systemie DNS klastra, dzięki czemu usługi wewnątrz sieci wirtualnej klastra są dostępne przy użyciu adresu URL. Ten adres URL `azds list-uris`można wyświetlić za pomocą programu .
* Ustawienie *brak* dla opcji punktu końcowego powoduje, że nie ma kontrolera transferu danych przychodzących do wdrożenia. Bez wdrożonego kontrolera transferu danych przychodzących [możliwości routingu usługi Azure Dev Spaces][dev-spaces-routing] nie będą działać. Opcjonalnie można zaimplementować własne rozwiązanie kontrolera transferu danych przychodzących przy użyciu [traefik][traefik-ingress] lub [NGINX][nginx-ingress], co pozwoli na ponowne ponowne pracę funkcji routingu.

Aby skonfigurować opcję punktu końcowego, użyj *-e* lub *--endpoint* podczas włączania usługi Azure Dev Spaces w klastrze. Przykład:

> [!NOTE]
> Opcja punktu końcowego wymaga, aby była uruchomiona platforma Azure CLI w wersji 2.2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS -e private
```

## <a name="client-requirements"></a>Wymagania dotyczące klientów

Usługa Azure Dev Spaces używa narzędzi po stronie klienta, takich jak rozszerzenie interfejsu wiersza polecenia azure dev spaces, rozszerzenie kodu programu Visual Studio i rozszerzenie programu Visual Studio do komunikowania się z klastrem AKS w celu debugowania. Aby użyć narzędzia po stronie klienta usługi Azure Dev Spaces, zezwalaj na ruch z maszyn deweloperskich do domeny *azds-\*.azds.io.* Zobacz *dataplaneFqdn* w `USERPROFILE\.azds\settings.json` dokładnie FQDN. W przypadku korzystania z [autoryzowanych zakresów adresów IP serwera interfejsu API][auth-range-section]należy również zezwolić na adres IP wszystkich maszyn programistów, które łączą się z klastrem AKS w celu debugowania, aby połączyć się z serwerem interfejsu API.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak usługa Azure Dev Spaces pomaga tworzyć bardziej złożone aplikacje w wielu kontenerach i jak można uprościć tworzenie współpracy, pracując z różnymi wersjami lub gałęziami kodu w różnych przestrzeniach.

> [!div class="nextstepaction"]
> [Tworzenie zespołów w usłudze Azure Dev Spaces][team-quickstart]

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
[azure-cli-install]: /cli/azure/install-azure-cli
[dev-spaces-ip-auth-range-regions]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[dev-spaces-routing]: how-dev-spaces-works-routing.md
[endpoint-options]: #using-different-endpoint-options
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[team-quickstart]: quickstart-team-development.md