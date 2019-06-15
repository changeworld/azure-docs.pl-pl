---
title: Inteligentnego routingu i kanarkowe wydania przy użyciu Istio w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak za pomocą Istio zapewniają routing inteligentne i wdrożyć kanarkowe wydania w klastrze usługi Azure Kubernetes Service (AKS)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: bd660a2b6ffb96478c3170cc7013ff22518b758f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64702207"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Używanie inteligentnego routingu i kanarkowe Istio w usłudze Azure Kubernetes Service (AKS)

[Istio] [ istio-github] jest siatki usługi typu open source, zapewniająca klucza zestaw funkcji w mikrousług w klastrze Kubernetes. Te funkcje obejmują zarządzanie ruchem, tożsamość usługi i zabezpieczeń, wymuszanie zasad i observability. Aby uzyskać więcej informacji na temat Istio można znaleźć w oficjalnej [co to jest Istio?] [ istio-docs-concepts] dokumentacji.

W tym artykule pokazano, jak korzystać z funkcji zarządzania ruchu z Istio. Przykładową aplikację głosowania AKS jest używany do zbadania inteligentnego routingu i kanarkowe wydania.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Wdrażanie aplikacji
> * Aktualizowanie aplikacji
> * Wdrażanie wydania canary aplikacji
> * Zakończ proces wdrażania

## <a name="before-you-begin"></a>Przed rozpoczęciem

> [!NOTE]
> Ten scenariusz był testowany wersji Istio `1.1.3`.

W krokach szczegółowo opisanych w tym artykule przyjęto założenie, został utworzony klaster AKS (Kubernetes `1.11` i nowszych, przy użyciu RBAC włączone), a `kubectl` połączenia z klastrem. Należy także Istio zainstalowany w klastrze.

Jeśli potrzebujesz pomocy przy użyciu dowolnego z tych elementów, zobacz [szybkiego startu usługi AKS] [ aks-quickstart] i [Istio Zainstaluj w usłudze AKS] [ istio-install] wskazówki.

## <a name="about-this-application-scenario"></a>Temat tego scenariusza aplikacji

Przykładową aplikację głosowania AKS oferuje dwie opcje głosowania (**koty** lub **psy**) do użytkowników. Brak składnika magazynu, która utrwala liczba głosów dla każdej opcji. Ponadto jest składnik analizy, który zawiera szczegółowe informacje dotyczące głosów dla każdej opcji.

W tym scenariuszu aplikacja rozpoczyna się od wdrażania wersji `1.0` aplikacja do głosowania, a wersja `1.0` składnika analizy. Składnik analizy zapewnia proste liczby liczba głosów. Aplikacja do głosowania i składnik analizy wchodzą w interakcję z wersją `1.0` składnika magazynu, która jest przechowywana w pamięci podręcznej Redis.

Składnik analizy można uaktualnić do wersji `1.1`, które zapewnia liczniki i sumy teraz i wartości procentowe.

Podzbiór użytkowników wersji testowej `2.0` aplikacji za pośrednictwem wydania canary. Korzysta z tej nowej wersji składnika magazynu, która jest wspierana przez bazę danych MySQL.

Gdy masz pewność, ta wersja `2.0` działa zgodnie z oczekiwaniami na podzbiór użytkowników, możesz wdrożyć wersji `2.0` do wszystkich użytkowników.

## <a name="deploy-the-application"></a>Wdrażanie aplikacji

Zacznijmy od wdrożenia aplikacji do klastra Azure Kubernetes Service (AKS). Na poniższym diagramie przedstawiono, co działa do końca tej sekcji — wersja `1.0` wszystkich składników za pomocą przychodzących żądań obsługiwanych za pośrednictwem bramy ruch przychodzący Istio:

![AKS głosowania składniki aplikacji i routing.](media/istio/components-and-routing-01.png)

Artefakty muszą skorzystać z tego artykułu są dostępne w [Azure-Samples/aks aplikacją do głosowania —] [ github-azure-sample] repozytorium GitHub. Możesz pobrać artefakty lub sklonuj repozytorium w następujący sposób:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Zmiany do następującego folderu w repozytorium pobrane / sklonowany i uruchom wszystkie kolejne kroki z tego folderu:

```console
cd scenarios/intelligent-routing-with-istio
```

Najpierw utworzyć przestrzeń nazw w klastrze AKS dla przykładowej usługi AKS głosowania aplikacji o nazwie `voting` w następujący sposób:

```azurecli
kubectl create namespace voting
```

Etykieta przestrzeni nazw z `istio-injection=enabled`. Ta etykieta powoduje, że Istio automatycznie iniekcję proxy istio jako przyczepkami, do wszystkich zasobników w tej przestrzeni nazw.

```azurecli
kubectl label namespace voting istio-injection=enabled
```

Teraz Utwórzmy składników dla aplikacji do głosowania w usłudze AKS. Tworzenie tych składników `voting` przestrzeni nazwy utworzonej w poprzednim kroku.

```azurecli
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

Następujące przykładowe dane wyjściowe pokazuje tworzone zasoby:

```console
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio zawiera niektóre szczególne wymagania dotyczące zasobników i usług. Aby uzyskać więcej informacji, zobacz [Istio wymagania dotyczące dokumentacji usług i zasobników][istio-requirements-pods-and-services].

Aby wyświetlić zasobników, które zostały utworzone, użyj [kubectl get pods-] [ kubectl-get] polecenia w następujący sposób:

```azurecli
kubectl get pods -n voting
```

Następujące przykładowe dane wyjściowe pokazuje, istnieją trzy wystąpienia `voting-app` zasobników i pojedyncze wystąpienie `voting-analytics` i `voting-storage` zasobników. Każda z zasobników ma dwa kontenery. Jedną z tych kontenerów jest składnikiem, a drugi to `istio-proxy`:

```console
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s
```

Aby wyświetlić informacje o zasobnik, użyj [kubectl opisują zasobnika][kubectl-describe]. Zastąp nazwę zasobnik w własnego klastra usługi AKS z danych wyjściowych poprzedniej nazwy zasobnika:

```azurecli
kubectl describe pod voting-app-1-0-956756fd-d5w7z --namespace voting
```

`istio-proxy` Kontenera automatycznie wstrzyknięciu przez Istio do zarządzania ruchu sieciowego do i z składniki, jak pokazano w następujących przykładowych danych wyjściowych:

```
[...]
Containers:
  voting-app:
    Image:         mcr.microsoft.com/aks/samples/voting/app:1.0
    ...
  istio-proxy:
    Image:         docker.io/istio/proxyv2:1.1.3
[...]
```

Nie można połączyć z głosowania aplikacji, dopóki nie zostanie utworzony Istio [bramy] [ istio-reference-gateway] i [usług wirtualnych][istio-reference-virtualservice]. Te zasoby Istio kierować ruch z bramy domyślnej Istio ruch przychodzący do naszej aplikacji.

> [!NOTE]
> A **bramy** jest składnikiem na brzegu sieci usługi, który odbiera przychodzący lub wychodzący ruch protokołu HTTP i TCP.
> 
> A **usług wirtualnych** definiuje zestaw reguł rozsyłania dla co najmniej jednej usługi docelowej.

Użyj `kubectl apply` polecenie, aby wdrożyć yaml bramy i usługa wirtualnych. Pamiętaj, aby określić przestrzeń nazw, że te zasoby są wdrażane w.

```azurecli
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

Następujące przykładowe dane wyjściowe pokazuje nowe bramy i usługa wirtualnych tworzonych:

```console
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Uzyskaj adres IP bramy ruch przychodzący Istio, używając następującego polecenia:

```azurecli
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

Następujące przykładowe dane wyjściowe wyświetla adres IP bramy transferu danych przychodzących:

```
20.188.211.19
```

Otwórz przeglądarkę i wklej adres IP. Przykładową aplikację głosowania AKS jest wyświetlany.

![Jest uruchomiona w naszym Istio aplikacja do głosowania w usłudze AKS włączone klastra AKS.](media/istio/deploy-app-01.png)

Informacje w dolnej części ekranu pokazuje, że aplikacja korzysta z wersji `1.0` z `voting-app` i wersji `1.0` z `voting-storage` (Redis).

## <a name="update-the-application"></a>Aktualizowanie aplikacji

Firma Microsoft umożliwia wdrażanie nowej wersji składnika usługi analytics. Nowa wersja `1.1` wyświetlane sumy i wartości procentowych, oprócz liczbę elementów w każdej kategorii.

Na poniższym diagramie przedstawiono, jakie będą uruchomione na końcu tej sekcji — tylko wersja `1.1` z naszych `voting-analytics` składnik ma ruch kierowany od `voting-app` składnika. Mimo że wersji `1.0` z naszych `voting-analytics` składnik będzie nadal działać i odwołuje się do niej `voting-analytics` usługi proxy Istio nie zezwalaj na ruch do i z niego.

![AKS głosowania składniki aplikacji i routing.](media/istio/components-and-routing-02.png)

Zajmijmy się wdrożeniem wersji `1.1` z `voting-analytics` składnika. Utwórz ten składnik w `voting` przestrzeni nazw:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

Następujące przykładowe dane wyjściowe pokazuje tworzone zasoby:

```console
deployment.apps/voting-analytics-1-1 created
```

Otwórz przykładowe dane, których AKS aplikacja do głosowania w przeglądarce ponownie przy użyciu adresu IP bramy ruch przychodzący Istio uzyskanego w poprzednim kroku.

Przeglądarka przełącza między dwoma widokami, pokazano poniżej. Ponieważ używasz usługi Kubernetes [usługi] [ kubernetes-service] dla `voting-analytics` składnika za pomocą selektora pojedynczej etykiecie (`app: voting-analytics`), domyślne zachowanie okrężnego między korzysta z rozwiązania Kubernetes zasobników, które odpowiadają selektora. W tym przypadku jest zarówno wersja `1.0` i `1.1` z Twojej `voting-analytics` zasobników.

![Wersja 1.0 składnika analytics działające w naszej aplikacji do głosowania w usłudze AKS.](media/istio/deploy-app-01.png)

![Wersja 1.1 składnika analytics działające w naszej aplikacji do głosowania w usłudze AKS.](media/istio/update-app-01.png)

Można wizualizować przełączanie między dwoma wersjami `voting-analytics` składnika w następujący sposób. Pamiętaj, aby użyć adresu IP własnej Istio bramy transferu danych przychodzących.

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

PowerShell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
```

Następujące przykładowe dane wyjściowe przedstawia istotne części zwrócone witryny sieci web jako przełączniki lokacji między wersjami:

```
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Zablokowanie ruchu do aplikacji w wersji 1.1

Teraz możemy zablokować ruch tylko z wersji `1.1` z `voting-analytics` składnika i do wersji `1.0` z `voting-storage` składnika. Następnie zdefiniuj reguły routingu dla wszystkich innych składników.

> * A **usług wirtualnych** definiuje zestaw reguł rozsyłania dla co najmniej jednej usługi docelowej.
> * A **Reguła docelowa** definiuje zasady ruchu i określonych wersji.
> * A **zasad** definiuje, jakie metody uwierzytelniania mogą być akceptowane na workload(s).

Użyj `kubectl apply` polecenie, aby zamienić definicję usługi wirtualnych na użytkownika `voting-app` i Dodaj [docelowy reguły] [ istio-reference-destinationrule] i [wirtualnych usług] [ istio-reference-virtualservice] dla innych składników. Należy dodać [zasad] [ istio-reference-policy] do `voting` przestrzeni nazw, aby upewnić się, że wszystkie komunikacji między usługami jest zabezpieczony za pomocą protokołu TLS wzajemnego i certyfikatów klientów.

* Zasady zawierają `peers.mtls.mode` równa `STRICT` aby upewnić się, że wzajemne TLS są wymuszane między usługami w ramach `voting` przestrzeni nazw.
* Dodatkowo ustawimy `trafficPolicy.tls.mode` do `ISTIO_MUTUAL` w naszych zasadach docelowej. Istio udostępnia usługi za pomocą silnej tożsamości i zabezpiecza komunikację między usługami przy użyciu protokołu TLS wzajemnego i certyfikatów klientów, które Istio niewidocznie zarządza.

```azurecli
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

Następujące przykładowe dane wyjściowe pokazuje nowe zasady, miejsce docelowe zasady i wirtualnych usług, które są utworzone/zaktualizowane:

```console
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Jeśli otworzysz aplikację do głosowania w usłudze AKS w przeglądarce ponownie tylko nową wersję `1.1` z `voting-analytics` składnik jest używany przez `voting-app` składnika.

![Wersja 1.1 składnika analytics działające w naszej aplikacji do głosowania w usłudze AKS.](media/istio/update-app-01.png)

Można wizualizować, że teraz są możesz tylko kierowane do wersji `1.1` z Twojej `voting-analytics` składnika w następujący sposób. Pamiętaj, aby użyć adresu IP własnej bramy ruch przychodzący Istio:

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

PowerShell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
```

Następujące przykładowe dane wyjściowe przedstawia istotne części zwrócone witryny sieci web:

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Przejdźmy teraz upewnij się, że Istio korzysta z protokołu TLS wzajemnego do zabezpieczenia komunikacji między każdej z naszych usług. W tym firma Microsoft użyje [sprawdzenie protokołu tls authn] [ istioctl-authn-tls-check] polecenie `istioctl` klienta binarny, który ma następującą postać.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Ten zbiór poleceń zawierają informacje o dostęp do określonych usług, od wszystkich zasobników, znajdują się w przestrzeni nazw, które odpowiada zestaw etykiet:

Bash

```bash
# mTLS configuration between each of the istio ingress pods and the voting-app service
kubectl get pod -n istio-system -l app=istio-ingressgateway | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.istio-system voting-app.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-analytics service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-analytics.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-storage service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
kubectl get pod -n voting -l app=voting-analytics,version=1.1 | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local
```

PowerShell

```powershell
# mTLS configuration between each of the istio ingress pods and the voting-app service
(kubectl get pod -n istio-system -l app=istio-ingressgateway | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".istio-system") voting-app.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-analytics service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-analytics.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-analytics,version=1.1 | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }
```

To następujące przykładowe dane wyjściowe pokazuje, że wzajemne TLS jest wymuszane dla każdej z naszych zapytania powyżej. Dane wyjściowe pokazują również zasadach i regułach docelowego, który wymusza wzajemne TLS:

```console
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

## <a name="roll-out-a-canary-release-of-the-application"></a>Wdrażanie wydania canary aplikacji

Teraz Zajmijmy się wdrożeniem nowej wersji `2.0` z `voting-app`, `voting-analytics`, i `voting-storage` składników. Nowy `voting-storage` składnika użyj MySQL zamiast Redis i `voting-app` i `voting-analytics` składniki są aktualizowane, aby umożliwić im skorzystać z tej nowe `voting-storage` składnika.

`voting-app` Składnik obsługuje teraz funkcje flagi funkcji. Flaga ta funkcja umożliwia testowanie możliwości wydania canary Istio dla podzbioru użytkowników.

Na poniższym diagramie przedstawiono, jakie będziesz mieć uruchomiony na końcu tej sekcji.

* Wersja `1.0` z `voting-app` składnika, wersja `1.1` z `voting-analytics` składników i wersji `1.0` z `voting-storage` składnika są w stanie komunikować się ze sobą.
* Wersja `2.0` z `voting-app` składnika, wersja `2.0` z `voting-analytics` składników i wersji `2.0` z `voting-storage` składnika są w stanie komunikować się ze sobą.
* Wersja `2.0` z `voting-app` składnika tylko są dostępne dla użytkowników, którzy mają ustawioną flagą określonych funkcji. Ta zmiana jest zarządzana przy użyciu flagi funkcji za pośrednictwem pliku cookie.

![AKS głosowania składniki aplikacji i routing.](media/istio/components-and-routing-03.png)

Najpierw należy zaktualizować Istio docelowy reguły i wirtualnych usług w celu zaspokojenia tych nowych składników. Te aktualizacje upewnij się, że nie kierować ruchem niepoprawnie do nowych składników użytkowników nie uzyskasz nieoczekiwanym dostępem:

```azurecli
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

Następujące przykładowe dane wyjściowe pokazuje docelowy reguły i wirtualnych usług aktualizacji:

```console
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Następnie Dodajmy obiekty usługi Kubernetes z nową wersją `2.0` składników. Możesz również zaktualizować `voting-storage` usługi, aby uwzględnić `3306` porcie MySQL:

```azurecli
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

Następujące przykładowe dane wyjściowe zawierają obiekty usługi Kubernetes jest pomyślnie uaktualniona lub utworzone:

```console
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Poczekaj, aż wszystkie wersje `2.0` zasobniki zaczną działać. Użyj [kubectl get pods-] [ kubectl-get] polecenie, aby wyświetlić wszystkie zasobników w `voting` przestrzeni nazw:

```azurecli
kubectl get pods --namespace voting
```

Teraz powinno być możliwe przełączyć się między wersją `1.0` i wersji `2.0` (canary) aplikacji do głosowania. Przełącz flagę funkcji, w dolnej części ekranu, ustawia plik cookie. Ten plik cookie jest używany przez `voting-app` usługa wirtualnych w celu przekierowania użytkowników do nowej wersji `2.0`.

![Wersja 1.0 aplikacji do głosowania w usłudze AKS — flagę funkcji, który nie jest ustawiona.](media/istio/canary-release-01.png)

![Wersja 2.0 aplikacji do głosowania w usłudze AKS — ustawiona jest flaga funkcji.](media/istio/canary-release-02.png)

Liczba głosów różnią się między wersjami aplikacji. Różnica ta wyróżnia, że używasz dwóch różnych magazynu zaplecza.

## <a name="finalize-the-rollout"></a>Zakończ proces wdrażania

Po pomyślnym przetestowaniu wydania canary, zaktualizować `voting-app` usługa wirtualnego do kierowania ruchu do wersji `2.0` z `voting-app` składnika. Wszyscy użytkownicy widzą wersji `2.0` aplikacji, niezależnie od tego, czy flaga funkcji jest ustawiona lub nie:

![AKS głosowania składniki aplikacji i routing.](media/istio/components-and-routing-04.png)

Zaktualizuj wszystkie reguły docelowej do usunięcia wersje składników nie ma już aktywne. Następnie należy zaktualizować wirtualne usługi można zatrzymać odwołujące się do tych wersji.

Ponieważ jest już cały ruch do wszystkich starszych wersji składników, możesz teraz bezpiecznie usunąć wszystkie wdrożenia dla tych składników.

![AKS głosowania składniki aplikacji i routing.](media/istio/components-and-routing-05.png)

Nową wersję aplikacji do głosowania AKS teraz zostały pomyślnie udostępniona.

## <a name="clean-up"></a>Czyszczenie 

Możesz usunąć aplikację głosowania AKS użyte w tym scenariuszu z klastra usługi AKS, usuwając `voting` przestrzeni nazw w następujący sposób:

```azurecli
kubectl delete namespace voting
```

Następujące przykładowe dane wyjściowe pokazuje, że wszystkie składniki aplikacji głosowania AKS zostały usunięte z klastra usługi AKS.

```console
namespace "voting" deleted
```

## <a name="next-steps"></a>Kolejne kroki

Możesz eksplorować inne scenariusze przy użyciu [Przykładowa aplikacja Bookinfo Istio][istio-bookinfo-example].

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
[istio-install]: ./istio-install.md
