---
title: Inteligentne wersje routingu i oprogramowania Kanaryjskie z Istio w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak korzystać z usługi Istio w celu zapewnienia inteligentnego routingu i wdrażania wydań oprogramowania Kanaryjskie w klastrze usługi Azure Kubernetes Service (AKS)
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 4a695957c287e69ff6b40e5a01254a729eaae441
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273003"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Używanie inteligentnych wersji routingu i oprogramowania Kanaryjskie z Istio w usłudze Azure Kubernetes Service (AKS)

[Istio][istio-github] to siatka usługi typu "open source", która udostępnia kluczowy zestaw funkcji dla mikrousług w klastrze Kubernetes. Te funkcje obejmują zarządzanie ruchem, tożsamość usługi i zabezpieczenia, wymuszanie zasad oraz ich przestrzeganie. Aby uzyskać więcej informacji na temat Istio, zobacz oficjalny dokument dotyczący [Istio?][istio-docs-concepts] .

W tym artykule przedstawiono sposób korzystania z funkcji zarządzania ruchem w programie Istio. Przykładowa aplikacja do głosowania AKS służy do eksplorowania inteligentnych wersji routingu i oprogramowania Kanaryjskie.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Wdrażanie aplikacji
> * Aktualizowanie aplikacji
> * Wdrażanie aplikacji w wersji kanaryjskiej
> * Finalizowanie wdrożenia

## <a name="before-you-begin"></a>Przed rozpoczęciem

> [!NOTE]
> Ten scenariusz został przetestowany pod kątem wersji Istio `1.3.2`.

W krokach przedstawionych w tym artykule przyjęto założenie, że utworzono klaster AKS (Kubernetes `1.13` lub nowszy z włączoną funkcją RBAC) oraz nawiązano połączenie `kubectl` z klastrem. Wymagany jest również Istio zainstalowany w klastrze.

Jeśli potrzebujesz pomocy dotyczącej któregokolwiek z tych elementów, zobacz Przewodnik [Szybki Start AKS][aks-quickstart] i [Install Istio in AKS][istio-install] .

## <a name="about-this-application-scenario"></a>Informacje o tym scenariuszu aplikacji

Przykładowa aplikacja do głosowania AKS udostępnia dwie opcje głosowania (**koty** lub **psy**) dla użytkowników. Istnieje składnik magazynu, który zachowuje liczbę głosów dla każdej opcji. Ponadto istnieje składnik analityczny, który zawiera szczegółowe informacje na temat rzutowania głosów dla każdej opcji.

W tym scenariuszu aplikacji Zacznij od wdrożenia wersji `1.0` aplikacji do głosowania i wersji `1.0` składnika analizy. Składnik analizy oferuje proste liczniki liczby głosów. Aplikacja do głosowania i składnik analizy współdziałają z wersją `1.0` składnika magazynu, która jest obsługiwana przez Redis.

Składnik analityczny jest uaktualniany do wersji `1.1`, która zapewnia liczby, a teraz sumy i wartości procentowe.

Podzbiór użytkowników, którzy przetestowali wersję `2.0` aplikacji za pośrednictwem wersji systemu kanaryjskiej. Ta nowa wersja używa składnika magazynu, który jest objęty bazą danych MySQL.

Jeśli masz pewność, że wersja `2.0` działa zgodnie z oczekiwaniami w podzbiorze użytkowników, przeprowadzisz `2.0` wersji do wszystkich użytkowników.

## <a name="deploy-the-application"></a>Wdrażanie aplikacji

Zacznijmy od wdrożenia aplikacji do klastra usługi Azure Kubernetes Service (AKS). Na poniższym diagramie przedstawiono działania wykonywane na końcu tej sekcji — wersja `1.0` wszystkich składników z żądaniami przychodzącymi, które są realizowane za pośrednictwem bramy Istioal:

![Składniki aplikacji do głosowania i routingu AKS.](media/servicemesh/istio/scenario-routing-components-01.png)

Artefakty, które należy wykonać wraz z tym artykułem, są dostępne w repozytorium [Azure-Samples/AKS-głosujących aplikacji][github-azure-sample] . Możesz pobrać artefakty lub sklonować repozytorium w następujący sposób:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Przejdź do następującego folderu w obszarze pobrane/Sklonowane repozytorium i uruchom wszystkie kolejne kroki z tego folderu:

```console
cd aks-voting-app/scenarios/intelligent-routing-with-istio
```

Najpierw utwórz przestrzeń nazw w klastrze AKS dla przykładowej aplikacji do głosowania AKS o nazwie `voting` w następujący sposób:

```console
kubectl create namespace voting
```

Oznacz przestrzeń nazw `istio-injection=enabled`. Ta etykieta instruuje Istio, aby automatycznie wstrzyknąć Istio-proxy jako przyczepki do wszystkich swoich zasobników w tej przestrzeni nazw.

```console
kubectl label namespace voting istio-injection=enabled
```

Teraz Utwórzmy składniki aplikacji do głosowania AKS. Utwórz te składniki w `voting` przestrzeni nazw utworzonej w poprzednim kroku.

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

Następujące przykładowe dane wyjściowe przedstawiają tworzone zasoby:

```output
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio ma pewne konkretne wymagania dotyczące jednek i usług. Aby uzyskać więcej informacji, zapoznaj się z dokumentacją dotyczącą [wymagań Istio dotyczących usługi i usług][istio-requirements-pods-and-services].

Aby wyświetlić utworzone zasobniki, użyj polecenia [polecenia kubectl Get][kubectl-get] -Binding w następujący sposób:

```console
kubectl get pods -n voting --show-labels
```

Następujące przykładowe dane wyjściowe pokazują, że istnieją trzy wystąpienia `voting-app` pod i jednego wystąpienia zarówno `voting-analytics`, jak i `voting-storage`. Każdy z tych zasobników ma dwa kontenery. Jeden z tych kontenerów jest składnikiem, a drugi to `istio-proxy`:

```output
NAME                                    READY     STATUS    RESTARTS   AGE   LABELS
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s   app=voting-analytics,pod-template-hash=57c7fccb44,version=1.0
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s   app=voting-storage,pod-template-hash=5d8fcc89c4,version=1.0
```

Aby wyświetlić informacje o tym pod, użyjemy polecenia [polecenia kubectl opisz pod][kubectl-describe] z selektorami etykiet, aby wybrać `voting-analytics` pod. Przefiltruje dane wyjściowe, aby wyświetlić szczegóły dwóch kontenerów znajdujących się w obszarze:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-powershell.md)]

::: zone-end

Nie można nawiązać połączenia z aplikacją do głosowania, dopóki nie utworzysz [bramy][istio-reference-gateway] Istio i [usługi wirtualnej][istio-reference-virtualservice]. Te zasoby Istio kierują ruch z domyślnej bramy usługi Istio do naszej aplikacji.

> [!NOTE]
> **Brama** to składnik na granicy siatki usługi, który odbiera przychodzące lub wychodzące ruch HTTP i TCP.
> 
> **Usługa wirtualna** definiuje zestaw reguł routingu dla co najmniej jednej usługi docelowej.

Użyj `kubectl apply` polecenie, aby wdrożyć bramę i usługę wirtualną YAML. Pamiętaj, aby określić przestrzeń nazw, w której są wdrażane te zasoby.

```console
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

Następujące przykładowe dane wyjściowe przedstawiają utworzoną nową bramę i usługę wirtualną:

```output
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Uzyskaj adres IP bramy Istio ingresing przy użyciu następującego polecenia:

```output
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

Następujące przykładowe dane wyjściowe przedstawiają adres IP bramy transferu danych przychodzących:

```output
20.188.211.19
```

Otwórz przeglądarkę i wklej adres IP. Zostanie wyświetlona Przykładowa aplikacja do głosowania AKS.

![Aplikacja do głosowania AKS uruchomiona w naszym Istio włączonym klastrze AKS.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

Informacje w dolnej części ekranu pokazują, że aplikacja używa wersji `1.0` `voting-app` i wersji `1.0` `voting-storage` (Redis).

## <a name="update-the-application"></a>Aktualizowanie aplikacji

Wdróżmy nową wersję składnika analizy. Ta nowa wersja `1.1` wyświetla sumy i wartości procentowe oprócz liczby dla każdej kategorii.

Na poniższym diagramie przedstawiono, co będzie działać na końcu tej sekcji, `1.1` wersja elementu `voting-analytics` ma ruch kierowany z składnika `voting-app`. Mimo że wersja `1.0` składnika `voting-analytics` nadal działa i jest przywoływana przez usługę `voting-analytics`, serwery proxy Istio nie zezwalają na ruch do i z niego.

![Składniki aplikacji do głosowania i routingu AKS.](media/servicemesh/istio/scenario-routing-components-02.png)

Wdróżmy wersję `1.1` składnika `voting-analytics`. Utwórz ten składnik w przestrzeni nazw `voting`:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

Następujące przykładowe dane wyjściowe przedstawiają tworzone zasoby:

```output
deployment.apps/voting-analytics-1-1 created
```

Ponownie otwórz przykładową aplikację do głosowania w AKS w przeglądarce przy użyciu adresu IP bramy transferu danych przychodzących Istio uzyskanej w poprzednim kroku.

Twoja przeglądarka jest alternatywna dla dwóch widoków przedstawionych poniżej. Ponieważ używasz [usługi][kubernetes-service] Kubernetes dla składnika `voting-analytics` z tylko jednym selektorem etykiet (`app: voting-analytics`), Kubernetes używa domyślnego zachowania Round-Robin między identyfikatorami, które pasują do tego selektora. W tym przypadku jest to wersja `1.0` i `1.1` `voting-analytics`ch.

![Wersja 1,0 składnika analitycznego działającego w naszej aplikacji do głosowania AKS.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

![Wersja 1,1 składnika analitycznego działającego w naszej aplikacji do głosowania AKS.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Można wizualizować przełączanie między dwiema wersjami składnika `voting-analytics` w następujący sposób. Pamiętaj, aby użyć adresu IP własnej bramy Istio ingresd.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

Poniższe przykładowe dane wyjściowe przedstawiają odpowiednią część zwróconej witryny sieci Web w miarę przełączania lokacji między wersjami:

```output
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Zablokuj ruch do wersji 1,1 aplikacji

Teraz Zablokuj ruch tylko do `1.1` wersji składnika `voting-analytics` oraz do wersji `1.0` składnika `voting-storage`. Następnie zdefiniuj reguły routingu dla wszystkich innych składników programu.

> * **Usługa wirtualna** definiuje zestaw reguł routingu dla co najmniej jednej usługi docelowej.
> * **Reguła docelowa** określa zasady ruchu i zasady dotyczące wersji.
> * **Zasady** definiują metody uwierzytelniania, które można zaakceptować w przypadku obciążeń.

Użyj `kubectl apply` polecenie, aby zamienić definicję usługi wirtualnej na `voting-app` i dodać [reguły docelowe][istio-reference-destinationrule] i [usługi wirtualne][istio-reference-virtualservice] dla innych składników. [Zasady][istio-reference-policy] zostaną dodane do przestrzeni nazw `voting`, aby upewnić się, że wszystkie połączenia między usługami są zabezpieczone przy użyciu protokołu TLS i certyfikatów klienta.

* Zasady mają `peers.mtls.mode` ustawione na `STRICT`, aby zapewnić, że wzajemna TLS zostanie wymuszona między usługami w `voting` przestrzeni nazw.
* Ustawimy również `trafficPolicy.tls.mode`, aby `ISTIO_MUTUAL` we wszystkich naszych regułach docelowych. Usługa Istio zapewnia usługi z silnymi tożsamościami i zabezpiecza komunikację między usługami przy użyciu protokołu TLS i certyfikatów klientów, które są w sposób niewidoczny do zarządzania.

```console
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

Następujące przykładowe dane wyjściowe przedstawiają nowe zasady, reguły docelowe i zaktualizowane/utworzone usługi wirtualne:

```output
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Po ponownym otwarciu aplikacji do głosowania AKS w przeglądarce zostanie `voting-app` użyta tylko nowa wersja `1.1` składnika `voting-analytics`.

![Wersja 1,1 składnika analitycznego działającego w naszej aplikacji do głosowania AKS.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Możesz wizualizować, że jesteś teraz kierowany do wersji `1.1` składnika `voting-analytics` w następujący sposób. Pamiętaj, aby użyć adresu IP własnej bramy Istio Ingres:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

Poniższe przykładowe dane wyjściowe przedstawiają odpowiednią część zwróconej witryny sieci Web:

```output
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Potwierdźmy, że usługa Istio używa wzajemnego protokołu TLS do zabezpieczania komunikacji między poszczególnymi usługami. W tym celu użyjemy polecenia [authn TLS-Check][istioctl-authn-tls-check] w pliku binarnym klienta `istioctl`, który ma następującą formę:.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Ten zestaw poleceń zapewnia informacje o dostępie do określonych usług, ze wszystkich zestawów, które znajdują się w przestrzeni nazw i pasują do zestawu etykiet:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-powershell.md)]

::: zone-end

Poniższe przykładowe dane wyjściowe pokazują, że dla każdego z naszych zapytań zostało wymuszone wzajemne szyfrowanie TLS. W danych wyjściowych znajdują się również zasady i zasady dotyczące zasad, które wymuszają wzajemne zaufanie TLS:

```output
# mTLS configuration between istio ingress pods and the voting-app service
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

# mTLS configuration between each of the voting-app pods and the voting-analytics service
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

# mTLS configuration between each of the voting-app pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>Wdrażanie aplikacji w wersji kanaryjskiej

Teraz wdróżmy nową wersję `2.0` składników `voting-app`, `voting-analytics`i `voting-storage`. Nowy składnik `voting-storage` używa programu MySQL zamiast Redis, a składniki `voting-app` i `voting-analytics` zostały zaktualizowane, aby umożliwić im korzystanie z tego nowego składnika `voting-storage`.

Składnik `voting-app` obsługuje teraz funkcję flagi funkcji. Ta flaga funkcji umożliwia przetestowanie możliwości wersji programu Kanaryjskie Istio dla podzbioru użytkowników.

Na poniższym diagramie przedstawiono działanie, które zostanie uruchomione na końcu tej sekcji.

* `1.0` wersji składnika `voting-app`, wersja `1.1` składnika `voting-analytics` i wersja `1.0` składnika `voting-storage` są w stanie komunikować się ze sobą.
* `2.0` wersji składnika `voting-app`, wersja `2.0` składnika `voting-analytics` i wersja `2.0` składnika `voting-storage` są w stanie komunikować się ze sobą.
* `2.0` wersji składnika `voting-app` są dostępne tylko dla użytkowników, którzy mają określoną flagę funkcji. Ta zmiana jest zarządzana przy użyciu flagi funkcji za pośrednictwem pliku cookie.

![Składniki aplikacji do głosowania i routingu AKS.](media/servicemesh/istio/scenario-routing-components-03.png)

Najpierw należy zaktualizować reguły docelowe Istio i usługi wirtualne, aby były one przeznaczone dla nowych składników. Te aktualizacje gwarantują, że nie są nieprawidłowo kierowane ruch do nowych składników, a użytkownicy nie otrzymują nieoczekiwanego dostępu:

```console
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

Następujące przykładowe dane wyjściowe przedstawiają aktualizowane reguły docelowe i usługi wirtualne:

```output
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Następnie Dodajmy obiekty Kubernetes dla nowej wersji `2.0` składników. Należy również zaktualizować usługę `voting-storage`, aby uwzględnić port `3306` dla programu MySQL:

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

Następujące przykładowe dane wyjściowe pokazują, że obiekty Kubernetes zostały pomyślnie zaktualizowane lub utworzone:

```output
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Poczekaj, aż wszystkie wersje `2.0` są uruchomione. Użyj [polecenia kubectl GetBinding][kubectl-get] polecenia z przełącznikiem Watch `-w`, aby obejrzeć zmiany we wszystkich zasobnikach w przestrzeni nazw `voting`:

```console
kubectl get pods --namespace voting -w
```

Teraz powinno być możliwe przełączenie między wersją `1.0` i wersją `2.0` (Kanaryjskie) aplikacji do głosowania. Flaga funkcji przełącznika w dolnej części ekranu ustawia plik cookie. Ten plik cookie jest używany przez `voting-app` usługę wirtualną do przesyłania użytkownikom nowej wersji `2.0`.

![NIE ustawiono wersji 1,0 aplikacji do głosowania AKS — flaga funkcji.](media/servicemesh/istio/scenario-routing-canary-release-01.png)

![ZOSTANIE ustawiona wersja 2,0 flagi "aplikacja do głosowania AKS".](media/servicemesh/istio/scenario-routing-canary-release-02.png)

Liczby głosu są różne w zależności od wersji aplikacji. Ta różnica oznacza, że korzystasz z dwóch różnych punktów końcowych magazynu.

## <a name="finalize-the-rollout"></a>Finalizowanie wdrożenia

Po pomyślnym przetestowaniu wersji programu kanaryjskiej zaktualizuj usługę wirtualną `voting-app`, aby kierować cały ruch do wersji `2.0` składnika `voting-app`. Wszyscy użytkownicy zobaczą wersję `2.0` aplikacji, niezależnie od tego, czy flaga funkcji jest ustawiona, czy nie:

![Składniki aplikacji do głosowania i routingu AKS.](media/servicemesh/istio/scenario-routing-components-04.png)

Zaktualizuj wszystkie reguły docelowe, aby usunąć wersje składników, które nie powinny już być aktywne. Następnie zaktualizuj wszystkie usługi wirtualne, aby zakończyć odwoływanie się do tych wersji.

Ponieważ nie ma już żadnego ruchu do żadnej ze starszych wersji składników, można teraz bezpiecznie usunąć wszystkie wdrożenia dla tych składników.

![Składniki aplikacji do głosowania i routingu AKS.](media/servicemesh/istio/scenario-routing-components-05.png)

Pomyślnie przeprowadzono nową wersję aplikacji do głosowania AKS.

## <a name="clean-up"></a>Czyszczenie 

Aby usunąć AKSą aplikację do głosowania w tym scenariuszu z klastra AKS, Usuń przestrzeń nazw `voting` w następujący sposób:

```console
kubectl delete namespace voting
```

Następujące przykładowe dane wyjściowe pokazują, że wszystkie składniki aplikacji do głosowania AKS zostały usunięte z klastra AKS.

```output
namespace "voting" deleted
```

## <a name="next-steps"></a>Następne kroki

Dodatkowe scenariusze można eksplorować przy użyciu [przykładu aplikacji Istio Bookinfo][istio-bookinfo-example].

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio-github]: https://github.com/istio/istio

[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/prepare/requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/networking/v1alpha3/gateway/
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/networking/v1alpha3/virtual-service/
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/networking/v1alpha3/destination-rule/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[istioctl-authn-tls-check]: https://istio.io/docs/reference/commands/istioctl/#istioctl-authn-tls-check

[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./servicemesh-istio-install.md
