---
title: Pojęcia — sieć w usługach Azure Kubernetes (AKS)
description: Informacje dotyczące sieci w usłudze Azure Kubernetes Service (AKS), m.in. podstawowe i zaawansowane sieci kontrolery transferu danych przychodzących, moduły równoważenia obciążenia i statycznych adresów IP.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: iainfou
ms.openlocfilehash: 62ba98f221041d5bbf9bb095a02d052218eb0fd0
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49381164"
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

W usłudze Kubernetes *usług* pogrupowania zasobników, aby zezwolić na potrzeby bezpośredniego dostępu za pośrednictwem adresu IP lub nazwę DNS i na określonym porcie. Możesz też dystrybuować ruch przy użyciu *moduł równoważenia obciążenia*. Bardziej złożonym routingiem ruchu aplikacji, również można osiągnąć za pomocą *kontrolerów ruch przychodzący*. Zabezpieczenia i filtrowanie ruchu sieciowego do zasobników jest możliwe przy użyciu rozwiązania Kubernetes *zasad sieciowych*.

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

- *Podstawowe* sieci - sieci zasoby są tworzone i konfigurowane jako klaster AKS jest wdrażany.
- *Zaawansowane* sieci — klaster AKS jest podłączony do istniejących zasobów sieci wirtualnej i konfiguracji.

### <a name="basic-networking"></a>Podstawowe operacje sieciowe

*Podstawowe* sieci — opcja jest domyślnie skonfigurowany do tworzenia klastra AKS. Platforma Azure zarządza konfiguracją sieci klastra i zasobników. Sieć podstawowa jest odpowiednia w przypadku wdrożeń, które nie wymagają konfiguracji niestandardowa sieć wirtualna. Za pomocą sieci podstawowych, nie można zdefiniować konfigurację sieci, takich jak nazwy podsieci lub zakres adresów IP, które są przypisane do klastra usługi AKS.

Węzły w klastrze AKS skonfigurowany dla podstawowych sieci [wtyczki kubenet] [ kubenet] wtyczka platformy Kubernetes.

Sieć podstawowa zawiera następujące funkcje:

- Udostępnianie zewnętrznie lub wewnętrznie usługa Kubernetes za pomocą usługi Azure Load Balancer.
- Zasobników dostęp do zasobów w publicznym Internecie.

### <a name="advanced-networking"></a>Zaawansowane funkcje sieciowe

*Zaawansowane* sieci umieszcza zasobników w sieci wirtualnej platformy Azure, które konfigurujesz. Ta sieć wirtualna zapewnia automatyczne łączność z innymi zasobami platformy Azure i integracja z usługą bogaty zestaw funkcji. Zaawansowane funkcje sieciowe jest odpowiednia w przypadku wdrożeń, które wymagają zastosowania takiej konfiguracji określonej sieci wirtualnej, takie jak używać istniejącej podsieci i łączności. Za pomocą zaawansowanych sieci, można zdefiniować te nazwy podsieci i zakresy adresów IP.

Węzły w klastrze AKS skonfigurowane dla zaawansowanych sieci [wtyczki Azure Container Networking interfejsu (CNI)] [ cni-networking] wtyczka platformy Kubernetes.

![Diagram przedstawiający dwa węzły o mostki łączenia każdego do pojedynczej sieci wirtualnej platformy Azure][advanced-networking-diagram]

Zaawansowane funkcje sieciowe oferuje następujące funkcje za pośrednictwem sieci podstawowe:

- Wdrażanie klastra usługi AKS w istniejącej sieci wirtualnej platformy Azure lub Utwórz nową sieć wirtualną i podsieć dla klastra.
- Każdy zasobnik w klastrze jest przypisany adres IP w sieci wirtualnej. Zasobników mogą bezpośrednio komunikować się z innymi zasobników w klastrze i innych węzłów w sieci wirtualnej.
- Zasobnik można połączyć z innymi usługami w równorzędnej sieci wirtualnej, w tym do sieci lokalnej za pośrednictwem usługi ExpressRoute i lokacja lokacja (S2S) połączenia sieci VPN. Zasobniki są również dostępne ze środowiska lokalnego.
- Zasobników w podsieci, które mają włączone punkty końcowe usługi można bezpiecznie łączyć się z usługami platformy Azure, takich jak Azure Storage i bazą danych SQL.
- Można utworzyć trasy zdefiniowane przez użytkownika (UDR) do kierowania ruchu z zasobników do wirtualnego urządzenia sieciowego.

Aby uzyskać więcej informacji, zobacz [skonfiguruj zaawansowane sieci dla klastra usługi AKS][aks-configure-advanced-networking].

## <a name="ingress-controllers"></a>Ruch przychodzący kontrolerów

Podczas tworzenia usługi typu usługi równoważenia obciążenia jest tworzony bazowego zasobu modułu równoważenia obciążenia platformy Azure. Moduł równoważenia obciążenia jest skonfigurowany do dystrybucji ruchu do zasobników w usłudze na danym porcie. Moduł równoważenia obciążenia działa tylko w warstwie 4 - usługa nie rozpoznaje rzeczywiste aplikacje i nie może wprowadzać żadnych dodatkowych kwestii dotyczących routingu.

*Ruch przychodzący kontrolerów* działa w warstwie 7, a następnie użyć bardziej inteligentne i reguł w celu dystrybucji ruchu aplikacji. Zazwyczaj kontrolera danych przychodzących jest używane do kierowania ruchu HTTP do różnych aplikacji na podstawie przychodzącego adresu URL.

![Diagram przedstawiający przepływ ruchu przychodzącego w klastrze AKS][aks-ingress]

W usłudze AKS możesz utworzyć zasób usługi transferu danych przychodzących przy użyciu polecenia podobnego NGINX lub użyć funkcji routing aplikacji protokołu HTTP w usłudze AKS. Po włączeniu routing aplikacji protokołu HTTP dla klastra usługi AKS, platforma Azure tworzy kontroler danych przychodzących i *DNS zewnętrznego* kontrolera. Podczas tworzenia nowych zasobów ruch przychodzący w usłudze Kubernetes wymagane rekordy A systemu DNS są tworzone w strefie DNS specyficzne dla klastra. Aby uzyskać więcej informacji, zobacz [wdrożenia, routing aplikacji protokołu HTTP][aks-http-routing].

Kolejną funkcją typowych danych przychodzących jest kończenia żądań SSL/TLS. Dla aplikacji sieci web dużych dostępne za pośrednictwem protokołu HTTPS kończenie żądań protokołu TLS mogą być obsługiwane przez zasób danych przychodzących, a nie w samej aplikacji. Aby zapewnić automatyczne generowanie certyfikacji TLS i konfiguracji, można skonfigurować na korzystanie z dostawców takich jak umożliwia szyfrowanie zasobu transferu danych przychodzących. Aby uzyskać więcej informacji na temat konfigurowania kontrolera danych przychodzących NGINX za pomocą umożliwia szyfrowanie, zobacz [ruch przychodzący i protokół TLS][aks-ingress-tls].

## <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

Zabezpieczenia grupy Filtr ruchu sieciowego dla maszyn wirtualnych, takich jak węzłów AKS. Podczas tworzenia usług, takich jak usługi równoważenia obciążenia, platforma Azure automatycznie skonfiguruje wszystkie reguły sieciowej grupy zabezpieczeń, które są potrzebne. Nie należy ręcznie konfigurować reguły sieciowej grupy zabezpieczeń do filtrowania ruchu do zasobników w klastrze AKS. Zdefiniuj wszystkie wymagane porty i przekazywanie jako część Twojego manifestów usługi Kubernetes i pozwolić platformie Azure, Utwórz lub zaktualizuj odpowiednie zasady.

Domyślną sieciową grupę zabezpieczeń, które istnieją reguły dla ruchu, takich jak SSH. Te reguły domyślne mają zastosowanie do zarządzania klastrem i rozwiązywaniu problemów z dostępem. Usunięcie tych reguł domyślnych może spowodować problemy z zarządzaniem AKS i przerywa cel poziomu usług (SLO).

## <a name="next-steps"></a>Kolejne kroki

Rozpoczynanie pracy z usługą AKS sieci, zobacz [tworzenie i konfigurowanie zaawansowanych sieci dla klastra usługi AKS][aks-configure-advanced-networking].

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
[aks-configure-advanced-networking]: configure-advanced-networking.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md