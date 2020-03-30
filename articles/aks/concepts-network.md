---
title: Pojęcia — sieć w usługach Azure Kubernetes (AKS)
description: Dowiedz się więcej o sieci w usłudze Azure Kubernetes Service (AKS), w tym w sieciach Kubenet i Azure CNI, kontrolerach danych przychodzących, modułach równoważenia obciążenia i statycznych adresach IP.
ms.topic: conceptual
ms.date: 02/28/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 5800254ab44b5b0f1048ce2200f90c06a8d1666a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253938"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Pojęcia dotyczące sieci aplikacji w usłudze Azure Kubernetes Service (AKS)

W podejściu mikrousług opartych na kontenerach do tworzenia aplikacji składniki aplikacji muszą współpracować w celu przetworzenia ich zadań. Kubernetes udostępnia różne zasoby, które umożliwiają komunikację tej aplikacji. Można łączyć się z aplikacjami i udostępniać je wewnętrznie lub zewnętrznie. Aby tworzyć aplikacje o wysokiej dostępności, można zrównoważyć swoje aplikacje. Bardziej złożone aplikacje mogą wymagać konfiguracji ruchu przychodzącego dla zakończenia SSL/TLS lub routingu wielu składników. Ze względów bezpieczeństwa może być również konieczne ograniczenie przepływu ruchu sieciowego do lub między zasobnikami i węzłami.

W tym artykule przedstawiono podstawowe pojęcia, które zapewniają sieci do aplikacji w aks:

- [Usługi](#services)
- [Sieci wirtualne platformy Azure](#azure-virtual-networks)
- [Kontrolery transferu danych przychodzących](#ingress-controllers)
- [Zasady sieciowe](#network-policies)

## <a name="kubernetes-basics"></a>Podstawy platformy Kubernetes

Aby zezwolić na dostęp do aplikacji lub składników aplikacji do komunikowania się ze sobą, Kubernetes zapewnia warstwę abstrakcji do sieci wirtualnej. Węzły kubernetes są połączone z siecią wirtualną i mogą zapewniać łączność przychodzącą i wychodzącą dla zasobników. Składnik *kube-proxy* jest uruchamiany w każdym węźle w celu zapewnienia tych funkcji sieciowych.

W programie Kubernetes *usługi* logicznie grupują zasobników, aby umożliwić bezpośredni dostęp za pośrednictwem adresu IP lub nazwy DNS i określonego portu. Ruch można również rozprowadzać za pomocą *modułu równoważenia obciążenia*. Bardziej złożone routing ruchu aplikacji można również osiągnąć za pomocą *kontrolerów przychodzących.* Zabezpieczenia i filtrowanie ruchu sieciowego dla zasobników jest możliwe dzięki *zasadom sieciowym*firmy Kubernetes .

Platforma Azure pomaga również uprościć sieci wirtualne dla klastrów AKS. Podczas tworzenia modułu równoważenia obciążenia usługi Kubernetes tworzony i konfigurowany jest podstawowy zasób modułu równoważenia obciążenia platformy Azure. Podczas otwierania portów sieciowych do zasobników skonfigurowane są odpowiednie reguły sieciowej grupy zabezpieczeń platformy Azure. W przypadku routingu aplikacji HTTP platforma Azure może również konfigurować *zewnętrzny system DNS,* ponieważ skonfigurowane są nowe trasy transferu danych przychodzących.

## <a name="services"></a>Usługi

Aby uprościć konfigurację sieci dla obciążeń aplikacji, usługa Kubernetes używa *usług* do logicznego grupowanie zestawu zasobników i zapewniania łączności sieciowej. Dostępne są następujące typy usług:

- **Adres IP klastra** — tworzy wewnętrzny adres IP do użytku w klastrze AKS. Dobre dla aplikacji wewnętrznych, które obsługują inne obciążenia w klastrze.

    ![Diagram przedstawiający przepływ ruchu IP klastra w klastrze AKS][aks-clusterip]

- **NodePort** — tworzy mapowanie portów w węźle źródłowym, który umożliwia dostęp aplikacji bezpośrednio z adresem IP węzła i portem.

    ![Diagram przedstawiający przepływ ruchu NodePort w klastrze AKS][aks-nodeport]

- **LoadBalancer** — tworzy zasób modułu równoważenia obciążenia platformy Azure, konfiguruje zewnętrzny adres IP i łączy żądane zasobniki z pulą wewnętrznej bazy modułów równoważenia obciążenia. Aby umożliwić ruch klientów, aby dotrzeć do aplikacji, reguły równoważenia obciążenia są tworzone na żądanych portach. 

    ![Diagram przedstawiający przepływ ruchu modułu równoważenia obciążenia w klastrze AKS][aks-loadbalancer]

    Aby uzyskać dodatkową kontrolę i routing ruchu przychodzącego, można zamiast tego użyć [kontrolera transferu danych przychodzących.](#ingress-controllers)

- **ExternalName** — tworzy określony wpis DNS dla łatwiejszego dostępu do aplikacji.

Adres IP modułów równoważenia obciążenia i usług można przypisać dynamicznie lub można określić istniejący statyczny adres IP do użycia. Można przypisać zarówno wewnętrzne, jak i zewnętrzne statyczne adresy IP. Ten istniejący statyczny adres IP jest często powiązany z wpisem DNS.

Można utworzyć *wewnętrzne* i *zewnętrzne* moduły równoważenia obciążenia. Wewnętrzne moduły równoważenia obciążenia są przypisywane tylko do prywatnego adresu IP, więc nie można uzyskać do nich dostępu z Internetu.

## <a name="azure-virtual-networks"></a>Sieci wirtualne platformy Azure

W usłudze AKS można wdrożyć klaster, który jest oparty na jednym z następujących modeli sieciowych:

- *Sieć Kubenet* — zasoby sieciowe są zazwyczaj tworzone i konfigurowane w miarę wdrażania klastra AKS.
- *Sieci interfejsu CNI (Azure Container Networking Interface)* — klaster AKS jest połączony z istniejącymi zasobami i konfiguracjami sieci wirtualnej.

### <a name="kubenet-basic-networking"></a>Kubenet (podstawowe) sieci

Opcja sieci *kubenet* jest domyślną konfiguracją tworzenia klastra AKS. Za *pomocą kubenet*węzły otrzymują adres IP z podsieci sieci wirtualnej platformy Azure. Zasobniki uzyskują adresy IP z przestrzeni adresowej, która jest logicznie różna od podsieci sieci wirtualnej platformy Azure, używanej przez węzły. Dzięki skonfigurowaniu translatora adresów sieciowych (NAT) zasobniki mogą uzyskać dostęp do zasobów w sieci wirtualnej platformy Azure. Źródłowy adres IP ruchu jest NAT'd do węzła podstawowy adres IP.

Węzły używają wtyczki [kubenet][kubenet] Kubernetes. Można pozwolić platformie Azure tworzyć i konfigurować sieci wirtualne dla Ciebie lub wybrać wdrożenie klastra AKS w istniejącej podsieci sieci wirtualnej. Ponownie tylko węzły otrzymują routable adres IP, a zasobniki używać TRANSLATORA adresów sieciowych do komunikowania się z innymi zasobami poza klastrem AKS. Takie podejście znacznie zmniejsza liczbę adresów IP, które należy zarezerwować w przestrzeni sieciowej dla zasobników do użycia.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie sieci kubenet dla klastra AKS][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Sieć Azure CNI (zaawansowana)

W sieci Azure CNI każdy zasobnik uzyskuje adres IP z podsieci i jest dostępny bezpośrednio. Te adresy IP muszą być unikatowe w całej przestrzeni sieciowej i muszą być planowane z wyprzedzeniem. Każdy węzeł ma parametr konfiguracji dla maksymalnej liczby zasobników, które obsługuje. Równoważna liczba adresów IP na węzeł są następnie zarezerwowane z góry dla tego węzła. Takie podejście wymaga większego planowania, ponieważ w przeciwnym razie może prowadzić do wyczerpania adresów IP lub konieczności odbudowy klastrów w większej podsieci w miarę wzrostu zapotrzebowania aplikacji.

Węzły używają wtyczki Kubernetes interfejsu azure [container networking interface (CNI).][cni-networking]

![Diagram przedstawiający dwa węzły z mostkami łączącymi każdy z jedną siecią wirtualną platformy Azure][advanced-networking-diagram]

Aby uzyskać więcej informacji, zobacz [Konfigurowanie usługi Azure CNI dla klastra AKS][aks-configure-advanced-networking].

### <a name="compare-network-models"></a>Porównanie modeli sieciowych

Zarówno kubenet, jak i usługa Azure CNI zapewniają łączność sieciową dla klastrów AKS. Istnieją jednak zalety i wady każdego z nich. Na wysokim poziomie stosuje się następujące kwestie:

* **kubenet ( kubenet )**
    * Oszczędza przestrzeń adresową IP.
    * Używa wewnętrznego lub zewnętrznego modułu równoważenia obciążenia kubernetes, aby dotrzeć do zasobników spoza klastra.
    * Należy ręcznie zarządzać i obsługiwać trasy zdefiniowane przez użytkownika (UDR).
    * Maksymalnie 400 węzłów na klaster.
* **Azure CNI**
    * Zasobniki uzyskać pełną łączność sieci wirtualnej i mogą być bezpośrednio osiągnięte za pośrednictwem ich prywatnego adresu IP z podłączonych sieci.
    * Wymaga więcej miejsca na adres IP.

Istnieją następujące różnice w zachowaniu między kubenet i Azure CNI:

| Możliwości                                                                                   | Okręg wyborczy Kubenet   | Azure CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Wdrażanie klastra w istniejącej lub nowej sieci wirtualnej                                            | Obsługiwane — UDR stosowane ręcznie | Obsługiwane |
| Łączność pod-pod                                                                         | Obsługiwane | Obsługiwane |
| Łączność Pod-VM; Maszyna wirtualna w tej samej sieci wirtualnej                                          | Działa po zainicjowaniu przez strąk | Działa w obie strony |
| Łączność Pod-VM; Maszyna wirtualna w sieci wirtualnej równorzędnej                                            | Działa po zainicjowaniu przez strąk | Działa w obie strony |
| Dostęp lokalny za pomocą sieci VPN lub trasy ekspresowej                                                | Działa po zainicjowaniu przez strąk | Działa w obie strony |
| Dostęp do zasobów zabezpieczonych punktami końcowymi usługi                                             | Obsługiwane | Obsługiwane |
| Udostępnianie usług kubernetes przy użyciu usługi równoważenia obciążenia, bramy aplikacji lub kontrolera transferu danych przychodzących | Obsługiwane | Obsługiwane |
| Domyślne strefy DNS i prywatne platformy Azure                                                          | Obsługiwane | Obsługiwane |

Jeśli chodzi o DNS, zarówno z kubenet i Azure CNI wtyczki DNS jest oferowany przez CoreDNS, demona zestaw uruchomiony w AKS. Aby uzyskać więcej informacji na temat coredns w usłudze Kubernetes, zobacz [Dostosowywanie usługi DNS](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/). CoreDNS jest skonfigurowany domyślnie do przekazywania nieznanych domen do węzła serwerów DNS, innymi słowy, do funkcji DNS sieci wirtualnej platformy Azure, w której wdrożono klaster AKS. W związku z tym azure DNS i strefy prywatne będą działać dla zasobników działających w usłudze AKS.

### <a name="support-scope-between-network-models"></a>Zakres obsługi między modelami sieci

Niezależnie od używanego modelu sieciowego zarówno kubenet, jak i azure CNI można wdrożyć w jeden z następujących sposobów:

* Platforma Azure może automatycznie tworzyć i konfigurować zasoby sieci wirtualnej podczas tworzenia klastra AKS.
* Podczas tworzenia klastra AKS można ręcznie utworzyć i skonfigurować zasoby sieci wirtualnej oraz dołączyć je do tych zasobów.

Chociaż możliwości, takie jak punkty końcowe usługi lub UDRs są obsługiwane zarówno w kubenet i Azure CNI, [zasady pomocy technicznej dla usługi AKS][support-policies] definiują, jakie zmiany można wprowadzić. Przykład:

* Jeśli ręcznie utworzysz zasoby sieci wirtualnej dla klastra AKS, jest obsługiwana podczas konfigurowania własnych UDR lub punktów końcowych usługi.
* Jeśli platforma Azure automatycznie tworzy zasoby sieci wirtualnej dla klastra AKS, nie jest obsługiwana ręczna zmiana tych zasobów zarządzanych przez usługę AKS w celu skonfigurowania własnych zasobów UDR lub punktów końcowych usługi.

## <a name="ingress-controllers"></a>ruch przychodzący kontrolerów

Podczas tworzenia usługi typu LoadBalancer tworzony jest podstawowy zasób modułu równoważenia obciążenia platformy Azure. Moduł równoważenia obciążenia jest skonfigurowany do dystrybucji ruchu do zasobników w usłudze na danym porcie. LoadBalancer działa tylko w warstwie 4 — usługa nie jest świadoma rzeczywistych aplikacji i nie może wprowadzać żadnych dodatkowych zagadnień routingu.

*Kontrolery transferu danych przychodzących* działają w warstwie 7 i mogą używać bardziej inteligentnych reguł do dystrybucji ruchu aplikacji. Typowym zastosowaniem kontrolera transferu danych przychodzących jest kierowanie ruchu HTTP do różnych aplikacji na podstawie przychodzącego adresu URL.

![Diagram przedstawiający przepływ ruchu przychodzącego w klastrze AKS][aks-ingress]

W uzytku usługi AKS można utworzyć zasób transferu przychodzącego przy użyciu funkcji NGINX lub użyć funkcji routingu aplikacji AKS HTTP. Po włączeniu routingu aplikacji HTTP dla klastra AKS platforma Azure tworzy kontroler transferu danych przychodzących i kontroler *zewnętrznego DNS.* W miarę tworzenia nowych zasobów przychodzących w umiań Kubernetes wymagane rekordy DNS A są tworzone w strefie DNS specyficznej dla klastra. Aby uzyskać więcej informacji, zobacz [wdrażanie routingu aplikacji HTTP][aks-http-routing].

Inną wspólną cechą ingress jest zakończenie SSL/TLS. W dużych aplikacjach sieci web dostępnych za pośrednictwem protokołu HTTPS zakończenie TLS może być obsługiwane przez zasób transferu danych przychodzących, a nie w samej aplikacji. Aby zapewnić automatyczne generowanie i konfigurację certyfikatów TLS, można skonfigurować zasób transferu danych przychodzących do używania dostawców, takich jak Let's Encrypt. Aby uzyskać więcej informacji na temat konfigurowania kontrolera transferu danych przychodzących NGINX za pomocą let's encrypt, zobacz [Ruch przychodzący i TLS][aks-ingress-tls].

Można również skonfigurować kontroler transferu danych przychodzących, aby zachować adres IP źródła klienta w żądaniach do kontenerów w klastrze AKS. Gdy żądanie klienta jest kierowane do kontenera w klastrze AKS za pośrednictwem kontrolera transferu danych przychodzących, oryginalny źródłowy adres IP tego żądania nie będzie dostępny dla kontenera docelowego. Po włączeniu *zachowania adresu IP źródła klienta*źródłowy adres IP dla klienta jest dostępny w nagłówku żądania w obszarze *X-Forwarded-For*. Jeśli używasz zachowania adresu IP źródła klienta na kontrolerze transferu danych przychodzących, nie można użyć przekazywania protokołu SSL. Zachowanie ip źródła klienta i przekazywania protokołu SSL mogą być używane z innymi usługami, takimi jak *typ LoadBalancer.*

## <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

Sieciowa grupa zabezpieczeń filtruje ruch dla maszyn wirtualnych, takich jak węzły AKS. Podczas tworzenia usług, takich jak LoadBalancer, platforma Azure automatycznie konfiguruje wszystkie reguły sieciowej grupy zabezpieczeń, które są potrzebne. Nie konfiguruj ręcznie reguł sieciowej grupy zabezpieczeń w celu filtrowania ruchu dla zasobników w klastrze AKS. Zdefiniuj wszystkie wymagane porty i przekazywanie dalej w ramach manifestów usługi Kubernetes i pozwól platformie Azure utworzyć lub zaktualizować odpowiednie reguły. Można również użyć zasad sieciowych, jak omówiono w następnej sekcji, aby automatycznie stosować reguły filtrowania ruchu do zasobników.

## <a name="network-policies"></a>Zasady sieciowe

Domyślnie wszystkie zasobniki w klastrze AKS mogą wysyłać i odbierać ruch bez ograniczeń. Aby zwiększyć bezpieczeństwo, można zdefiniować reguły, które kontrolują przepływ ruchu. Aplikacje zaplecza są często udostępniane tylko na wymagane usługi frontonu lub składniki bazy danych są dostępne tylko dla warstw aplikacji, które się z nimi łączą.

Zasady sieciowe to funkcja Kubernetes dostępna w u usługi AKS, która umożliwia kontrolowanie przepływu ruchu między zasobnikami. Można zezwolić lub odmówić ruchu na podstawie ustawień, takich jak przypisane etykiety, obszar nazw lub port ruchu. Sieciowe grupy zabezpieczeń są bardziej dla węzłów AKS, a nie zasobników. Korzystanie z zasad sieciowych jest bardziej odpowiedni, natywny dla chmury sposób kontrolowania przepływu ruchu. Ponieważ zasobniki są tworzone dynamicznie w klastrze AKS, wymagane zasady sieciowe mogą być automatycznie stosowane.

Aby uzyskać więcej informacji, zobacz [Zabezpieczanie ruchu między zasobnikami przy użyciu zasad sieciowych w usłudze Azure Kubernetes Service (AKS)][use-network-policies].

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z sieci AKS, utwórz i skonfiguruj klaster AKS z własnymi zakresami adresów IP przy użyciu [kubenetu][aks-configure-kubenet-networking] lub [usługi Azure CNI][aks-configure-advanced-networking].

Aby zapoznać się z skojarzonymi najlepszymi rozwiązaniami, zobacz [Najważniejsze wskazówki dotyczące łączności sieciowej i zabezpieczeń w u. AKS][operator-best-practices-network].

Aby uzyskać dodatkowe informacje na temat podstawowych pojęć kubernetów i usługi AKS, zobacz następujące artykuły:

- [Klastry i obciążenia Kubernetes / AKS][aks-concepts-clusters-workloads]
- [Dostęp i tożsamość Kubernetes / AKS][aks-concepts-identity]
- [Zabezpieczenia Kubernetes / AKS][aks-concepts-security]
- [Kubernetes / AKS pamięci masowej][aks-concepts-storage]
- [Skala Kubernetes / AKS][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ingress.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
[support-policies]: support-policies.md
