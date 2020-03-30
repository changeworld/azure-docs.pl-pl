---
title: Korzystanie z usługi Azure API Management z mikrousługami wdrożonymi w usłudze Azure Kubernetes | Dokumenty firmy Microsoft
description: W tym artykule opisano opcje wdrażania usługi API Management w usłudze AKS
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/14/2019
ms.author: apimpm
ms.openlocfilehash: 1d6773b4daac256234c33bf50fb3736d585ac505
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75480998"
---
# <a name="use-azure-api-management-with-microservices-deployed-in-azure-kubernetes-service"></a>Korzystanie z usługi Azure API Management z mikrousługami wdrożonymi w usłudze Azure Kubernetes

Mikrousługi są idealne do tworzenia interfejsów API. Za [pomocą usługi Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) (AKS) można szybko wdrożyć i obsługiwać [architekturę opartą na mikrousługach](https://docs.microsoft.com/azure/architecture/guide/architecture-styles/microservices) w chmurze. Następnie można wykorzystać [usługę Azure API Management](https://aka.ms/apimrocks) (API Management) do publikowania mikrousług jako interfejsów API do zużycia wewnętrznego i zewnętrznego. W tym artykule opisano opcje wdrażania zarządzania interfejsami API w usłudze AKS. Zakłada podstawową wiedzę na temat kubernetes, zarządzania interfejsami API i sieci platformy Azure. 

## <a name="background"></a>Tło

Podczas publikowania mikrousług jako interfejsów API do użycia, może być trudne do zarządzania komunikacją między mikrousług i klientów, którzy z nich korzystają. Istnieje wiele przekrojowych problemów, takich jak uwierzytelnianie, autoryzacja, ograniczanie przepustowości, buforowanie, przekształcanie i monitorowanie. Te obawy są prawidłowe niezależnie od tego, czy mikrousługi są narażone na klientów wewnętrznych lub zewnętrznych. 

Wzorzec [bramy interfejsu API](https://docs.microsoft.com/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern) rozwiązuje te problemy. Brama interfejsu API służy jako drzwi frontowe do mikrousług, oddziela klientów od mikrousług, dodaje dodatkową warstwę zabezpieczeń i zmniejsza złożoność mikrousług, usuwając obciążenie związane z obsługą problemów przekrojowych. 

[Usługa Azure API Management](https://aka.ms/apimrocks) to gotowe rozwiązanie do rozwiązywania potrzeb bramy interfejsu API. Można szybko utworzyć spójną i nowoczesną bramę dla mikrousług i opublikować je jako interfejsy API. Jako rozwiązanie do zarządzania interfejsem API w pełnym cyklu życia zapewnia również dodatkowe możliwości, w tym samoobsługowy portal deweloperski do odnajdowania interfejsu API, zarządzania cyklem życia interfejsu API i analizy interfejsu API.

Gdy są używane razem, AKS i api Management zapewniają platformę do wdrażania, publikowania, zabezpieczania, monitorowania i zarządzania interfejsami API opartymi na mikrousługach. W tym artykule przejdziemy przez kilka opcji wdrażania usługi AKS w połączeniu z zarządzaniem interfejsem API. 

## <a name="kubernetes-services-and-apis"></a>Usługi i interfejsy API kubernetes

W klastrze Kubernetes kontenery są wdrażane w [zasobnikach](https://kubernetes.io/docs/concepts/workloads/pods/pod/), które są efemeryczne i mają cykl życia. Gdy węzeł procesu roboczego umiera, zasobników uruchomionych w węźle są tracone. W związku z tym adres IP zasobnika można zmienić w dowolnym momencie. Nie możemy polegać na nim, aby komunikować się z kapsułą. 

Aby rozwiązać ten problem, Firma Kubernetes wprowadziła koncepcję [Usług](https://kubernetes.io/docs/concepts/services-networking/service/). Usługa Kubernetes jest warstwą abstrakcji, która definiuje grupę logiki zasobników i umożliwia ekspozycję ruchu zewnętrznego, równoważenie obciążenia i odnajdowanie usług dla tych zasobników. 

Gdy jesteśmy gotowi do opublikowania naszych mikrousług jako interfejsów API za pośrednictwem zarządzania interfejsami API, musimy zastanowić się, jak mapować nasze usługi w usłudze Kubernetes na interfejsy API w usłudze API Management. Nie ma żadnych ustalonych reguł. To zależy od sposobu projektowania i partycjonowania możliwości biznesowych lub domen na mikrousługi na początku. Na przykład jeśli zasobniki za Usługą jest odpowiedzialny za wszystkie operacje na danym zasobie (np. Klient), Usługa może być mapowane do jednego interfejsu API. Jeśli operacje na zasobie są podzielone na wiele mikrousług (np. GetOrder, PlaceOrder), a następnie wiele usług może być logicznie agregowane w jednym interfejsie API w zarządzaniu interfejsem API (patrz rys. 1). 

Mapowania mogą również ewoluować. Ponieważ usługa API Management tworzy fasadę przed mikrousługami, umożliwia nam refaktoryzację i odpowiedni rozmiar naszych mikrousług w czasie. 

![Mapowanie usług na interfejsy API](./media/api-management-aks/service-api-mapping.png)

## <a name="deploy-api-management-in-front-of-aks"></a>Wdrażanie zarządzania interfejsami API przed usługą AKS

Istnieje kilka opcji wdrażania zarządzania interfejsami API przed klastrem AKS. 

Podczas gdy klaster AKS jest zawsze wdrażany w sieci wirtualnej (VNet), wystąpienie zarządzania interfejsami API nie jest wymagane do wdrożenia w sieci wirtualnej. Gdy zarządzanie interfejsami API nie znajduje się w sieci wirtualnej klastra, klaster AKS musi publikować publiczne punkty końcowe dla zarządzania interfejsami API, aby się z nimi połączyć. W takim przypadku istnieje potrzeba zabezpieczenia połączenia między zarządzaniem interfejsem API a usługą AKS. Innymi słowy musimy upewnić się, że klaster można uzyskać tylko za pośrednictwem zarządzania interfejsami API. Przejdźmy przez opcje. 

### <a name="option-1-expose-services-publicly"></a>Opcja 1: Ujawnianie usług publicznie

Usługi w klastrze AKS mogą być udostępniane publicznie przy użyciu [typów usługi](https://docs.microsoft.com/azure/aks/concepts-network) NodePort, LoadBalancer lub ExternalName. W takim przypadku Usługi są dostępne bezpośrednio z publicznego Internetu. Po wdrożeniu zarządzania interfejsami API przed klastrem, musimy upewnić się, że cały ruch przychodzący przechodzi przez zarządzanie interfejsem API, stosując uwierzytelnianie w mikrousługach. Na przykład zarządzanie interfejsem API może zawierać token dostępu w każdym żądaniu złożonym do klastra. Każda mikrousługa jest odpowiedzialny za sprawdzanie poprawności tokenu przed przetworzeniem żądania. 


Może to być najprostsza opcja wdrażania zarządzania interfejsami API przed usługą AKS, zwłaszcza jeśli masz już logikę uwierzytelniania zaimplementowane w mikrousługach. 

![Publikowanie usług bezpośrednio](./media/api-management-aks/direct.png)

Plusy:
* Łatwa konfiguracja po stronie zarządzania interfejsami API, ponieważ nie trzeba jej wstrzykiwać do sieci wirtualnej klastra
* Brak zmian po stronie AKS, jeśli usługi są już publicznie widoczne, a logika uwierzytelniania już istnieje w mikrousługach

Minusy:
* Potencjalne zagrożenie bezpieczeństwa wynikające z publicznej widoczności punktów końcowych usługi
* Brak punktu wejścia dla przychodzącego ruchu klastrowego
* Komplikuje mikrousługi za pomocą zduplikowanej logiki uwierzytelniania

### <a name="option-2-install-an-ingress-controller"></a>Opcja 2: Instalowanie kontrolera transferu danych przychodzących

Chociaż opcja 1 może być łatwiejsza, ma znaczące wady, jak wspomniano powyżej. Jeśli wystąpienie usługi API Management nie znajduje się w sieci wirtualnej klastra, wzajemne uwierzytelnianie TLS (mTLS) jest niezawodnym sposobem zapewnienia, że ruch jest bezpieczny i zaufany w obu kierunkach między wystąpieniem zarządzania interfejsami API a klastrem AKS. 

Wzajemne uwierzytelnianie TLS jest [natywnie obsługiwane](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates) przez zarządzanie interfejsem API i można je włączyć w usłudze Kubernetes, [instalując kontroler transferu danych przychodzących](https://docs.microsoft.com/azure/aks/ingress-own-tls) (rys. 3). W rezultacie uwierzytelnianie zostanie wykonane w kontrolerze transferu danych przychodzących, co upraszcza mikrousługi. Ponadto można dodać adresy IP usługi API Management do listy dozwolonych przez ruch przychodzący, aby upewnić się, że tylko usługa API Management ma dostęp do klastra.  

 
![Publikowanie za pośrednictwem kontrolera transferu danych przychodzących](./media/api-management-aks/ingress-controller.png)


Plusy:
* Łatwa konfiguracja po stronie zarządzania interfejsami API, ponieważ nie trzeba wstrzyknąć jej do sieci wirtualnej klastra, a usługa mTLS jest obsługiwana natywnie
* Centralizuje ochronę ruchu przychodzącego klastra w warstwie Kontroler transferu danych przychodzących
* Zmniejsza ryzyko związane z bezpieczeństwem, minimalizując publicznie widoczne punkty końcowe klastra

Minusy:
* Zwiększa złożoność konfiguracji klastra dzięki dodatkowej pracy związanej z instalowaniem, konfigurowaniem i utrzymywaniem kontrolera transferu danych przychodzących oraz zarządzanie certyfikatami używanymi dla usługi mTLS
* Zagrożenie bezpieczeństwa wynikające z publicznego widoczności punktu końcowego kontrolera transferu danych przychodzących


Podczas publikowania interfejsów API za pośrednictwem usługi API Management, jest to łatwe i wspólne do bezpiecznego dostępu do tych interfejsów API przy użyciu kluczy subskrypcji. Deweloperzy, którzy muszą korzystać z opublikowanych interfejsów API, muszą dołączyć prawidłowy klucz subskrypcji w żądaniach HTTP podczas wykonywania połączeń z tymi interfejsami API. W przeciwnym razie wywołania są natychmiast odrzucane przez bramę zarządzania interfejsem API. Nie są one przekazywane do usług zaplecza.

Aby uzyskać klucz subskrypcji dostępu do interfejsów API, wymagana jest subskrypcja. Subskrypcja jest zasadniczo nazwany kontener dla pary kluczy subskrypcji. Deweloperzy, którzy muszą korzystać z opublikowanych interfejsów API, mogą uzyskać subskrypcje. I nie potrzebują zatwierdzenia przez wydawców interfejsu API. Wydawcy interfejsu API mogą również tworzyć subskrypcje bezpośrednio dla konsumentów interfejsu API.

### <a name="option-3-deploy-apim-inside-the-cluster-vnet"></a>Opcja 3: Wdrażanie interfejsu APIM w sieci wirtualnej klastra

W niektórych przypadkach klienci z ograniczeniami regulacyjnymi lub rygorystycznymi wymogami bezpieczeństwa mogą znaleźć wariant 1 i 2 nierentowne rozwiązania ze względu na publicznie narażone punkty końcowe. W innych klastra AKS i aplikacje, które korzystają z mikrousług może znajdować się w tej samej sieci wirtualnej, w związku z tym nie ma powodu, aby udostępnić klastra publicznie, jak cały ruch interfejsu API pozostanie w sieci wirtualnej. W tych scenariuszach można wdrożyć zarządzanie interfejsami API w sieci wirtualnej klastra. [Warstwa Usługi Premium zarządzania interfejsami API](https://aka.ms/apimpricing) obsługuje wdrażanie sieci wirtualnej. 

Istnieją dwa tryby [wdrażania zarządzania interfejsami API w sieci wirtualnej](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet) — zewnętrzne i wewnętrzne. 

Jeśli konsumenci interfejsu API nie znajdują się w sieci wirtualnej klastra, należy użyć trybu zewnętrznego (rys. 4). W tym trybie brama zarządzania interfejsami API jest wtryskiana do sieci wirtualnej klastra, ale dostępna z publicznego Internetu za pośrednictwem zewnętrznego modułu równoważenia obciążenia. Pomaga całkowicie ukryć klaster, a jednocześnie zezwolić klientom zewnętrznym na korzystanie z mikrousług. Ponadto można użyć funkcji sieci platformy Azure, takich jak sieciowe grupy zabezpieczeń (NSG), aby ograniczyć ruch sieciowy.

![Tryb zewnętrznej sieci wirtualnej](./media/api-management-aks/vnet-external.png)

Jeśli wszyscy konsumenci interfejsu API znajdują się w sieci wirtualnej klastra, tryb wewnętrzny (rys. 5) może być używany. W tym trybie brama zarządzania interfejsami API jest wstrzykiwana do sieci wirtualnej klastra i dostępna tylko z poziomu tej sieci wirtualnej za pośrednictwem wewnętrznego modułu równoważenia obciążenia. Nie ma możliwości dotarcia do bramy zarządzania interfejsami API lub klastra AKS z publicznego Internetu. 

![Wewnętrzny tryb sieci wirtualnej](./media/api-management-aks/vnet-internal.png)

 W obu przypadkach klaster AKS nie jest publicznie widoczny. W porównaniu z opcją 2 kontroler przychodzących może nie być konieczny. W zależności od scenariusza i konfiguracji uwierzytelnianie może nadal być wymagane między zarządzaniem interfejsem API a mikrousługami. Na przykład jeśli usługa Service Mesh zostanie przyjęta, zawsze wymaga wzajemnego uwierzytelniania TLS. 

Plusy:
* Najbezpieczniejsza opcja, ponieważ klaster AKS nie ma publicznego punktu końcowego
* Upraszcza konfigurację klastra, ponieważ nie ma publicznego punktu końcowego
* Możliwość ukrycia zarówno zarządzania interfejsami API, jak i AKS wewnątrz sieci wirtualnej za pomocą trybu wewnętrznego
* Możliwość kontrolowania ruchu sieciowego przy użyciu funkcji sieci platformy Azure, takich jak sieciowe grupy zabezpieczeń (NSG)

Minusy:
* Zwiększa złożoność wdrażania i konfigurowania zarządzania interfejsami API do pracy wewnątrz sieci wirtualnej

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [pojęciach dotyczących sieci dla aplikacji w aks](https://docs.microsoft.com/azure/aks/concepts-network)
* Dowiedz się więcej o [używaniu funkcji Zarządzanie interfejsami API w sieciach wirtualnych](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)





