---
title: Pojęcia — sieć w usługach Azure Kubernetes (AKS)
description: Informacje dotyczące sieci w usłudze Azure Kubernetes Service (AKS), m.in. wtyczki kubenet i wtyczki Azure CNI sieci kontrolery transferu danych przychodzących, moduły równoważenia obciążenia i statycznych adresów IP.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: iainfou
ms.openlocfilehash: afb7acda67eb5818ace8169dc4e98fb86bdbeaa7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442000"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Koncepcji sieci dla aplikacji w usłudze Azure Kubernetes Service (AKS)

W ramach podejścia do tworzenia aplikacji opartych na kontenerach mikrousług składniki aplikacji muszą współpracować do przetwarzania swoich zadań. Usługa Kubernetes zapewnia różnych zasobów, które umożliwiają komunikację z aplikacjami. Można nawiązać połączenie i udostępnianie aplikacji wewnętrznie lub zewnętrznie. Aby tworzyć aplikacje o wysokiej dostępności, można załadować saldo aplikacji. Bardziej złożone aplikacje mogą wymagać konfiguracji ruch przychodzący na kończenie żądań SSL/TLS lub routingu z wielu składników. Ze względów bezpieczeństwa może być również konieczne ograniczanie przepływu ruchu sieciowego do lub między zasobników i węzłów.

W tym artykule przedstawiono podstawowe pojęcia, które zapewniają sieci do aplikacji w usłudze AKS:

- [Usługi](#services)
- [Sieci wirtualne platformy Azure](#azure-virtual-networks)
- [Ruch przychodzący kontrolerów](#ingress-controllers)
- [Zasady sieciowe](#network-policies)

## <a name="kubernetes-basics"></a>Podstawy platformy Kubernetes

Aby zezwolić na dostęp do aplikacji lub dla składników aplikacji do komunikowania się ze sobą, usługa Kubernetes zapewnia warstwę abstrakcji sieci wirtualnej. Węzły rozwiązania Kubernetes są podłączone do sieci wirtualnej i może zapewnić łączność ruchu przychodzącego i wychodzącego do zasobników. *Serwera proxy klastra kubernetes w usłudze* składnika, który jest uruchamiany w każdym węźle, aby zapewnić, te funkcje sieciowego.

W usłudze Kubernetes *usług* pogrupowania zasobników, aby zezwolić na potrzeby bezpośredniego dostępu za pośrednictwem adresu IP lub nazwę DNS i na określonym porcie. Możesz też dystrybuować ruch przy użyciu *moduł równoważenia obciążenia*. Bardziej złożonym routingiem ruchu aplikacji, również można osiągnąć za pomocą *kontrolerów ruch przychodzący*. Zabezpieczenia i filtrowanie ruchu sieciowego do zasobników jest możliwe przy użyciu rozwiązania Kubernetes *zasad sieciowych* (w wersji zapoznawczej w usłudze AKS).

Platforma Azure pomaga uprościć sieci wirtualnych w przypadku klastrów usługi AKS. Podczas tworzenia modułu równoważenia obciążenia rozwiązania Kubernetes bazowego zasobu modułu równoważenia obciążenia platformy Azure zostało utworzone i skonfigurowane. Podczas otwierania portów sieciowych w celu zasobników skonfigurowano odpowiednie zasady grupy zabezpieczeń sieci platformy Azure. Routing aplikacji protokołu HTTP, aby uzyskać Azure można również skonfigurować *zewnętrznego serwera DNS* jako nowy ruch przychodzący trasy są skonfigurowane.

## <a name="services"></a>Usługi

Aby uprościć konfigurację sieci w przypadku obciążeń aplikacji, korzysta z rozwiązania Kubernetes *usług* logicznie pogrupować zestaw zasobników i zapewniają łączność sieciową. Dostępne są następujące typy usługi:

- **Adres IP klastra** — tworzy wewnętrznego adresu IP do użycia w klastrze AKS. Dobre dla wewnętrznych aplikacji, które obsługują innych obciążeń w klastrze.

    ![Diagram przedstawiający przepływ ruchu IP klastra w klastrze AKS][aks-clusterip]

- **NodePort** — tworzy mapowanie portów na podstawowym węźle, który umożliwia aplikacji są dostępne bezpośrednio z adresu IP węzła i portu.

    ![Diagram przedstawiający przepływ ruchu NodePort w klastrze AKS][aks-nodeport]

- **Moduł równoważenia obciążenia** — tworzy zasób modułu równoważenia obciążenia platformy Azure, konfiguruje zewnętrzny adres IP, a żądane zasobników nawiązanie połączenia z puli zaplecza modułu równoważenia obciążenia. Aby zezwolić na ruch klientów zasięg aplikacji, reguły równoważenia obciążenia są tworzone na żądany ruch na portach. 

    ![Diagram przedstawiający przepływ ruchu modułu równoważenia obciążenia w klastrze AKS][aks-loadbalancer]

    Dodatkową kontrolę i routingu ruchu przychodzącego, możesz zamiast tego użyć [kontrolera danych przychodzących](#ingress-controllers).

- **ExternalName** — tworzy określonego wpisu DNS w celu ułatwienia dostępu w aplikacji.

Adres IP dla usługi równoważenia obciążenia i usługi mogą być przypisywane dynamicznie, lub można określić istniejący statyczny adres IP do użycia. Wewnętrzne i zewnętrzne statyczne adresy IP można przypisać. Ten istniejący statyczny adres IP jest często powiązane wpis DNS.

Zarówno *wewnętrzny* i *zewnętrznych* modułów równoważenia obciążenia mogą być tworzone. Wewnętrzne moduły równoważenia obciążenia są przypisywane tylko prywatny adres IP, dzięki czemu nie jest dostępny z Internetu.

## <a name="azure-virtual-networks"></a>Sieci wirtualne platformy Azure

W usłudze AKS można wdrożyć klaster, który korzysta z jednego z następujących modeli dwóch sieci:

- *Wtyczki Kubenet* sieci - sieci zasobów są zazwyczaj tworzone i konfigurowane jako klaster AKS jest wdrażany.
- *Wtyczki Azure Container Networking interfejsu (CNI)* sieci — klaster AKS jest podłączony do istniejących zasobów sieci wirtualnej i konfiguracji.

### <a name="kubenet-basic-networking"></a>Sieć z wtyczki Kubenet (basic)

*Wtyczki kubenet* sieci — opcja jest domyślnie skonfigurowany do tworzenia klastra AKS. Za pomocą *wtyczki kubenet*, węzły Uzyskaj adres IP z podsieci sieci wirtualnej platformy Azure. Zasobników otrzymują adres IP z logicznie różnymi przestrzeniami adresowymi w podsieci sieci wirtualnej platformy Azure z węzłów. Translator adresów sieciowych (NAT) jest następnie konfigurowana, tak aby zasobników może dotrzeć do zasobów w sieci wirtualnej platformy Azure. Źródłowy adres IP ruchu jest translatora adresów Sieciowych do podstawowego adresu IP węzła zajmie się.

Węzły używają [wtyczki kubenet][kubenet] wtyczka platformy Kubernetes. Można pozwolić, aby utworzyć i konfigurowanie sieci wirtualnych dla Ciebie lub wybrać wdrożenie klastra usługi AKS na istniejącą podsieć sieci wirtualnej platformy Azure. Ponownie tylko węzły otrzymują routingowi adresu IP i zasobników używać translatora adresów Sieciowych do komunikowania się z innymi zasobami poza klastrem usługi AKS. To podejście znacznie zmniejsza liczbę adresów IP, które należy zarezerwować w przestrzeń sieci dla zasobników do użycia.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie sieci dla klastra usługi AKS wtyczki kubenet][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Sieć Azure CNI (zaawansowane)

Za pomocą wtyczki Azure CNI pod każdym pobiera adres IP z podsieci i są dostępne bezpośrednio. Te adresy IP musi być unikatowa w przestrzeń sieci i muszą być planowane z góry. Każdy węzeł ma parametr konfiguracji maksymalna liczba zasobników, które obsługuje. Równoważną liczbę adresów IP w każdym węźle następnie są zarezerwowane na początku dla tego węzła. Takie podejście wymaga planowania, ponieważ w przeciwnym razie może prowadzić do wyczerpania adresu IP lub jest potrzebna ponowna kompilacja klastrów w większej podsieci, w miarę wzrostu wymagań aplikacji.

Węzły używają [wtyczki Azure Container Networking interfejsu (CNI)][cni-networking] wtyczka platformy Kubernetes.

![Diagram przedstawiający dwa węzły o mostki łączenia każdego do pojedynczej sieci wirtualnej platformy Azure][advanced-networking-diagram]

Aby uzyskać więcej informacji, zobacz [wtyczki Azure CNI skonfigurować dla klastra usługi AKS][aks-configure-advanced-networking].

### <a name="compare-network-models"></a>Porównanie modeli sieci

Wtyczki kubenet i wtyczki Azure CNI zapewniają łączność z siecią dla klastry usługi AKS. Istnieją zalety i wady każdej. Na wysokim poziomie obowiązują następujące zastrzeżenia:

* **Wtyczki Kubenet**
    * Oszczędza przestrzeń adresów IP.
    * Używa modułu równoważenia obciążenia wewnętrznej lub zewnętrznej usługi Kubernetes, aby dotrzeć do zasobników z poza klastrem.
    * Należy ręcznie zarządzać i zachować trasy zdefiniowane przez użytkownika (Udr).
    * Maksymalnie 400 węzłów w klastrze.
* **Wtyczki Azure CNI**
    * Zasobników Uzyskaj pełną wirtualnej sieci i bezpośrednio osiągalna z poza klastrem.
    * Wymaga więcej przestrzeni adresów IP.

Oto zachowanie różnice między wtyczki kubenet i wtyczki Azure CNI:

| Możliwości                                                                                   | Kubenet   | Azure CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Wdrażanie klastra w istniejącej lub nowej sieci wirtualnej                                            | Obsługiwane — stosowane ręcznie tras zdefiniowanych przez użytkownika | Obsługiwane |
| Zasobnik zasobnika łączności                                                                         | Obsługiwane | Obsługiwane |
| Łączność maszyn wirtualnych na zasobnik; Maszyny Wirtualnej w tej samej sieci wirtualnej                                          | Działa, gdy inicjowane przez zasobników | Działa w obu kierunkach |
| Łączność maszyn wirtualnych na zasobnik; Maszynę Wirtualną w równorzędnej sieci wirtualnej                                            | Działa, gdy inicjowane przez zasobników | Działa w obu kierunkach |
| Dostęp lokalny przy użyciu sieci VPN lub usługi Express Route                                                | Działa, gdy inicjowane przez zasobników | Działa w obu kierunkach |
| Dostęp do zasobów zabezpieczonych przez punkty końcowe usługi                                             | Obsługiwane | Obsługiwane |
| Udostępnianie usługi Kubernetes przy użyciu kontrolera service, App Gateway lub ruch przychodzący modułu równoważenia obciążenia | Obsługiwane | Obsługiwane |
| Domyślnie usługa Azure DNS i stref prywatnych                                                          | Obsługiwane | Obsługiwane |

### <a name="support-scope-between-network-models"></a>Zakres pomocy technicznej między modelami sieci

Niezależnie od używanego modelu sieci wtyczki kubenet i wtyczki Azure CNI można wdrożyć w jednej z następujących sposobów:

* Platforma Azure umożliwia automatyczne tworzenie i konfigurowanie zasobów sieci wirtualnej, podczas tworzenia klastra usługi AKS.
* Można ręcznie utworzyć i skonfigurować zasoby sieci wirtualnej i Dołącz do tych zasobów, podczas tworzenia klastra usługi AKS.

Funkcje, takie jak punkty końcowe usługi lub tras zdefiniowanych przez użytkownika są obsługiwane zarówno z wtyczki kubenet i wtyczki Azure CNI [obsługuje zasady dla usługi AKS][support-policies] zdefiniować można wprowadzać zmiany. Na przykład:

* Jeśli ręcznie utworzysz zasoby sieci wirtualnej dla klastra usługi AKS, są obsługiwane podczas konfigurowania własnych tras zdefiniowanych przez użytkownika lub punktów końcowych usługi.
* Jeśli platforma Azure automatycznie utworzy zasoby sieci wirtualnej dla klastra usługi AKS, nie jest obsługiwana ręcznie zmienić tych zasobów zarządzanych w usłudze AKS, aby skonfigurować własne tras zdefiniowanych przez użytkownika lub punktów końcowych usługi.

## <a name="ingress-controllers"></a>Ruch przychodzący kontrolerów

Podczas tworzenia usługi typu usługi równoważenia obciążenia jest tworzony bazowego zasobu modułu równoważenia obciążenia platformy Azure. Moduł równoważenia obciążenia jest skonfigurowany do dystrybucji ruchu do zasobników w usłudze na danym porcie. Moduł równoważenia obciążenia działa tylko w warstwie 4 - usługa nie rozpoznaje rzeczywiste aplikacje i nie może wprowadzać żadnych dodatkowych kwestii dotyczących routingu.

*Ruch przychodzący kontrolerów* działa w warstwie 7, a następnie użyć bardziej inteligentne i reguł w celu dystrybucji ruchu aplikacji. Zazwyczaj kontrolera danych przychodzących jest używane do kierowania ruchu HTTP do różnych aplikacji na podstawie przychodzącego adresu URL.

![Diagram przedstawiający przepływ ruchu przychodzącego w klastrze AKS][aks-ingress]

W usłudze AKS możesz utworzyć zasób usługi transferu danych przychodzących przy użyciu polecenia podobnego NGINX lub użyć funkcji routing aplikacji protokołu HTTP w usłudze AKS. Po włączeniu routing aplikacji protokołu HTTP dla klastra usługi AKS, platforma Azure tworzy kontroler danych przychodzących i *DNS zewnętrznego* kontrolera. Podczas tworzenia nowych zasobów ruch przychodzący w usłudze Kubernetes wymagane rekordy A systemu DNS są tworzone w strefie DNS specyficzne dla klastra. Aby uzyskać więcej informacji, zobacz [wdrożenia, routing aplikacji protokołu HTTP][aks-http-routing].

Kolejną funkcją typowych danych przychodzących jest kończenia żądań SSL/TLS. Dla aplikacji sieci web dużych dostępne za pośrednictwem protokołu HTTPS kończenie żądań protokołu TLS mogą być obsługiwane przez zasób danych przychodzących, a nie w samej aplikacji. Aby zapewnić automatyczne generowanie certyfikacji TLS i konfiguracji, można skonfigurować na korzystanie z dostawców takich jak umożliwia szyfrowanie zasobu transferu danych przychodzących. Aby uzyskać więcej informacji na temat konfigurowania kontrolera danych przychodzących NGINX za pomocą umożliwia szyfrowanie, zobacz [ruch przychodzący i protokół TLS][aks-ingress-tls].

Można również skonfigurować kontroler danych przychodzących w taki sposób, aby zachować źródłowy adres IP klienta w odpowiedzi na żądania z kontenerami w klastrze AKS. Jeśli żądanie klienta jest kierowany do kontenera w klastrze usługi AKS przy użyciu sieci kontroler danych przychodzących, oryginalnego źródłowego adres ip tego żądania nie będą dostępne do kontenera docelowego. Po włączeniu *klienta źródłowego adresu IP zachowania*, źródłowy adres IP klienta znajduje się w nagłówku żądania, w obszarze *X-Forwarded-dla*. Jeśli używasz klienta źródłowego adresu IP konserwacji na kontrolerze transferu danych przychodzących, nie można użyć przekazywanego protokołu SSL. Zachowywanie adresu IP źródłowego klienta i przekazywanego SSL mogą być używane z innymi usługami, takie jak *modułu równoważenia obciążenia* typu.

## <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

Sieciowa grupa zabezpieczeń służy do przefiltrowania ruchu dla maszyn wirtualnych, takich jak węzłów AKS. Podczas tworzenia usług, takich jak usługi równoważenia obciążenia, platforma Azure automatycznie skonfiguruje wszystkie reguły sieciowej grupy zabezpieczeń, które są potrzebne. Nie należy ręcznie konfigurować reguły sieciowej grupy zabezpieczeń do filtrowania ruchu do zasobników w klastrze AKS. Zdefiniuj wszystkie wymagane porty i przekazywanie jako część Twojego manifestów usługi Kubernetes i pozwolić platformie Azure, Utwórz lub zaktualizuj odpowiednie zasady. Umożliwia także zasady sieciowe zgodnie z opisem w następnej sekcji, aby automatycznie zastosować reguły filtrowania ruchu do zasobników.

## <a name="network-policies"></a>Zasady sieciowe

Domyślnie wszystkie zasobników w klastrze AKS umożliwia wysyłanie oraz odbieranie ruchu bez ograniczeń. Aby zwiększyć bezpieczeństwo można zdefiniować reguły, które kontrolują przepływu ruchu. Aplikacji zaplecza są dostępne często tylko wymagane frontonu usług lub składników bazy danych dostępnych tylko warstwy aplikacji łączących się z nimi.

Zasady sieci jest funkcją Kubernetes dostępne w usłudze AKS, która umożliwia sterowanie przepływem ruchu między zasobników. Istnieje możliwość blokują lub zezwalają na ruch na podstawie ustawień, takich jak przypisać etykiety, przestrzeń nazw lub ruchu sieciowego port. Sieciowe grupy zabezpieczeń są więcej węzłów AKS, nie zasobników. Użycie zasad sieciowych jest bardziej odpowiedni i natywnych dla chmury sposób kontrolowania przepływu ruchu. Zgodnie z zasobników są tworzone dynamicznie w klastrze AKS, zasady wymagane sieciowe mogą być automatycznie stosowane.

Aby uzyskać więcej informacji, zobacz [bezpieczny ruch między zasobników za pomocą zasad sieciowych w usłudze Azure Kubernetes Service (AKS)][use-network-policies].

## <a name="next-steps"></a>Kolejne kroki

Rozpoczynanie pracy z usługą AKS, sieć, tworzenie i konfigurowanie klastra usługi AKS przy użyciu własnych zakresów adresów IP przy użyciu [wtyczki kubenet][aks-configure-kubenet-networking] or [Azure CNI][aks-configure-advanced-networking].

Najlepsze rozwiązania dotyczące skojarzone, zobacz [najlepsze rozwiązania dotyczące łączności sieciowej i zabezpieczeń w usłudze AKS][operator-best-practices-network].

Dodatkowe informacje na temat podstawowej platformy Kubernetes oraz pojęcia zostały przedstawione z usługi AKS zobacz następujące artykuły:

- [Kubernetes / klastrów usługi AKS i obciążeń][aks-concepts-clusters-workloads]
- [Kubernetes dostępu w usłudze AKS i zarządzania tożsamościami][aks-concepts-identity]
- [Kubernetes / zabezpieczeń usługi AKS][aks-concepts-security]
- [Kubernetes / magazynu usługi AKS][aks-concepts-storage]
- [Kubernetes / skalowanie usługi AKS][aks-concepts-scale]

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
