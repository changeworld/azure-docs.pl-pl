---
title: Inteligentne wersje routingu i oprogramowania Kanaryjskie z Istio w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak korzystać z usługi Istio w celu zapewnienia inteligentnego routingu i wdrażania wydań oprogramowania Kanaryjskie w klastrze usługi Azure Kubernetes Service (AKS)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: 7baa2adbd615a449c73e70e1b96524fc1e18b25d
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000172"
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
> Ten scenariusz został przetestowany w stosunku `1.1.3`do wersji Istio.

W krokach przedstawionych w tym artykule przyjęto założenie, że utworzono `1.11` klaster AKS (Kubernetes lub nowszy z włączoną funkcją RBAC) `kubectl` i nawiązano połączenie z klastrem. Wymagany jest również Istio zainstalowany w klastrze.

Jeśli potrzebujesz pomocy dotyczącej któregokolwiek z tych elementów, zobacz Przewodnik [Szybki Start AKS][aks-quickstart] i [Install Istio in AKS][istio-install] .

## <a name="about-this-application-scenario"></a>Informacje o tym scenariuszu aplikacji

Przykładowa aplikacja do głosowania AKS udostępnia dwie opcje głosowania (**koty** lub **psy**) dla użytkowników. Istnieje składnik magazynu, który zachowuje liczbę głosów dla każdej opcji. Ponadto istnieje składnik analityczny, który zawiera szczegółowe informacje na temat rzutowania głosów dla każdej opcji.

W tym scenariuszu aplikacji Zacznij od wdrożenia wersji `1.0` aplikacji do głosowania i wersji `1.0` składnika analitycznego. Składnik analizy oferuje proste liczniki liczby głosów. Aplikacja do głosowania i składnik analizy współdziałają `1.0` z wersją składnika magazynu, która jest obsługiwana przez Redis.

Składnik analityczny jest uaktualniany do `1.1`wersji, która zapewnia liczby, a teraz sumuje i wartości procentowe.

Podzbiór użytkowników, którzy testują wersję `2.0` aplikacji za pośrednictwem wersji systemu kanaryjskiej. Ta nowa wersja używa składnika magazynu, który jest objęty bazą danych MySQL.

Po upewnieniu się, że `2.0` wersja działa zgodnie z oczekiwaniami w podzbiorze użytkowników, możesz wycofać wersję `2.0` do wszystkich użytkowników.

## <a name="deploy-the-application"></a>Wdrażanie aplikacji

Zacznijmy od wdrożenia aplikacji do klastra usługi Azure Kubernetes Service (AKS). Na poniższym diagramie przedstawiono działania wykonywane na końcu tej sekcji — wersja `1.0` wszystkich składników z żądaniami przychodzącymi, które są realizowane za pośrednictwem bramy Istioal:

![Składniki aplikacji do głosowania i routingu AKS.](media/istio/components-and-routing-01.png)

Artefakty, które należy wykonać wraz z tym artykułem, są dostępne w repozytorium [Azure-Samples/AKS-głosujących aplikacji][github-azure-sample] . Możesz pobrać artefakty lub sklonować repozytorium w następujący sposób:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Przejdź do następującego folderu w obszarze pobrane/Sklonowane repozytorium i uruchom wszystkie kolejne kroki z tego folderu:

```console
cd scenarios/intelligent-routing-with-istio
```

Najpierw utwórz przestrzeń nazw w klastrze AKS dla przykładowej aplikacji do głosowania AKS o nazwie `voting` w następujący sposób:

```azurecli
kubectl create namespace voting
```

Oznacz przestrzeń nazw za `istio-injection=enabled`pomocą. Ta etykieta instruuje Istio, aby automatycznie wstrzyknąć Istio-proxy jako przyczepki do wszystkich swoich zasobników w tej przestrzeni nazw.

```azurecli
kubectl label namespace voting istio-injection=enabled
```

Teraz Utwórzmy składniki aplikacji do głosowania AKS. Utwórz te składniki w `voting` przestrzeni nazw utworzonej w poprzednim kroku.

```azurecli
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

Następujące przykładowe dane wyjściowe przedstawiają tworzone zasoby:

```console
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

```azurecli
kubectl get pods -n voting
```

Następujące przykładowe dane wyjściowe pokazują, że istnieją trzy wystąpienia elementu `voting-app` a i jednego wystąpienia. `voting-analytics` `voting-storage` Każdy z tych zasobników ma dwa kontenery. Jeden z tych kontenerów jest składnikiem, a drugi to `istio-proxy`:

```console
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s
```

Aby wyświetlić informacje dotyczące pod, użyj [polecenia kubectl opisywany pod][kubectl-describe]. Zastąp nazwę pod nazwą elementu znajdującego się w klastrze AKS z poprzednich danych wyjściowych:

```azurecli
kubectl describe pod voting-app-1-0-956756fd-d5w7z --namespace voting
```

`istio-proxy` Kontener został automatycznie wprowadzony przez Istio do zarządzania ruchem sieciowym do i ze składników, jak pokazano w następujących przykładowych danych wyjściowych:

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

Nie można nawiązać połączenia z aplikacją do głosowania, dopóki nie utworzysz [bramy][istio-reference-gateway] Istio i [usługi wirtualnej][istio-reference-virtualservice]. Te zasoby Istio kierują ruch z domyślnej bramy usługi Istio do naszej aplikacji.

> [!NOTE]
> **Brama** to składnik na granicy siatki usługi, który odbiera przychodzące lub wychodzące ruch HTTP i TCP.
> 
> **Usługa wirtualna** definiuje zestaw reguł routingu dla co najmniej jednej usługi docelowej.

`kubectl apply` Użyj polecenia, aby wdrożyć bramę i usługę wirtualną YAML. Pamiętaj, aby określić przestrzeń nazw, w której są wdrażane te zasoby.

```azurecli
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

Następujące przykładowe dane wyjściowe przedstawiają utworzoną nową bramę i usługę wirtualną:

```console
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Uzyskaj adres IP bramy Istio ingresing przy użyciu następującego polecenia:

```azurecli
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

Następujące przykładowe dane wyjściowe przedstawiają adres IP bramy transferu danych przychodzących:

```
20.188.211.19
```

Otwórz przeglądarkę i wklej adres IP. Zostanie wyświetlona Przykładowa aplikacja do głosowania AKS.

![Aplikacja do głosowania AKS uruchomiona w naszym Istio włączonym klastrze AKS.](media/istio/deploy-app-01.png)

Informacje w dolnej części ekranu pokazują, że aplikacja używa `1.0` wersji programu `voting-app` i `1.0` programu `voting-storage` (Redis).

## <a name="update-the-application"></a>Aktualizowanie aplikacji

Wdróżmy nową wersję składnika analizy. W tej nowej `1.1` wersji są wyświetlane sumy i wartości procentowe, a także liczba dla każdej kategorii.

Na poniższym diagramie przedstawiono, co będzie działać na końcu tej sekcji. wersja `1.1` naszego `voting-analytics` składnika `voting-app` zawiera ruch kierowany ze składnika. Mimo że wersja `1.0` naszego `voting-analytics` składnika nadal jest uruchomiona `voting-analytics` i odwołuje się do niej usługa, serwery proxy Istio nie zezwalają na ruch do i z niego.

![Składniki aplikacji do głosowania i routingu AKS.](media/istio/components-and-routing-02.png)

Wdróżmy wersję `1.1` `voting-analytics` składnika. Utwórz ten składnik w `voting` przestrzeni nazw:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

Następujące przykładowe dane wyjściowe przedstawiają tworzone zasoby:

```console
deployment.apps/voting-analytics-1-1 created
```

Ponownie otwórz przykładową aplikację do głosowania w AKS w przeglądarce przy użyciu adresu IP bramy transferu danych przychodzących Istio uzyskanej w poprzednim kroku.

Twoja przeglądarka jest alternatywna dla dwóch widoków przedstawionych poniżej. Ponieważ używasz [usługi][kubernetes-service] Kubernetes dla `voting-analytics` składnika z tylko jednym selektorem etykiet (`app: voting-analytics`), Kubernetes używa domyślnego zachowania Round-Robin między obszarami, które pasują do tego selektora. W tym przypadku jest to zarówno wersja `1.0` , jak i `1.1` w `voting-analytics` Twoim zasobniku.

![Wersja 1,0 składnika analitycznego działającego w naszej aplikacji do głosowania AKS.](media/istio/deploy-app-01.png)

![Wersja 1,1 składnika analitycznego działającego w naszej aplikacji do głosowania AKS.](media/istio/update-app-01.png)

Można wizualizować przełączanie między dwiema wersjami `voting-analytics` składnika w następujący sposób. Pamiętaj, aby użyć adresu IP własnej bramy Istio ingresd.

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

Poniższe przykładowe dane wyjściowe przedstawiają odpowiednią część zwróconej witryny sieci Web w miarę przełączania lokacji między wersjami:

```
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Zablokuj ruch do wersji 1,1 aplikacji

`1.1` Teraz Zablokuj ruch tylko do wersji `voting-analytics` składnika `voting-storage` i wersji `1.0` składnika. Następnie zdefiniuj reguły routingu dla wszystkich innych składników programu.

> * **Usługa wirtualna** definiuje zestaw reguł routingu dla co najmniej jednej usługi docelowej.
> * **Reguła docelowa** określa zasady ruchu i zasady dotyczące wersji.
> * **Zasady** definiują metody uwierzytelniania, które można zaakceptować w przypadku obciążeń.

Użyj polecenia, aby zamienić definicję usługi wirtualnej `voting-app` na i dodać [reguły docelowe][istio-reference-destinationrule] i [usługi wirtualne][istio-reference-virtualservice] dla innych składników. `kubectl apply` Należy dodać [zasady][istio-reference-policy] do `voting` przestrzeni nazw, aby upewnić się, że wszystkie połączenia między usługami są zabezpieczone przy użyciu wzajemnego protokołu TLS i certyfikatów klienta.

* Zasady zostały `peers.mtls.mode` `STRICT` ustawione tak, aby zapewnić, że wzajemna TLS jest wymuszana `voting` między usługami w ramach przestrzeni nazw.
* Ustawimy `trafficPolicy.tls.mode` również dla `ISTIO_MUTUAL` wszystkich naszych reguł docelowych. Usługa Istio zapewnia usługi z silnymi tożsamościami i zabezpiecza komunikację między usługami przy użyciu protokołu TLS i certyfikatów klientów, które są w sposób niewidoczny do zarządzania.

```azurecli
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

Następujące przykładowe dane wyjściowe przedstawiają nowe zasady, reguły docelowe i zaktualizowane/utworzone usługi wirtualne:

```console
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Po ponownym otwarciu aplikacji do głosowania w programie AKS w przeglądarce zostanie użyta `1.1` `voting-app` tylko nowa wersja `voting-analytics` składnika.

![Wersja 1,1 składnika analitycznego działającego w naszej aplikacji do głosowania AKS.](media/istio/update-app-01.png)

Możesz wizualizować, że jesteś teraz kierowany do wersji `1.1` `voting-analytics` składnika tylko w następujący sposób. Pamiętaj, aby użyć adresu IP własnej bramy Istio Ingres:

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

Poniższe przykładowe dane wyjściowe przedstawiają odpowiednią część zwróconej witryny sieci Web:

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Potwierdźmy, że usługa Istio używa wzajemnego protokołu TLS do zabezpieczania komunikacji między poszczególnymi usługami. W tym celu użyjemy polecenia [authn TLS-Check][istioctl-authn-tls-check] w `istioctl` pliku binarnym klienta, który ma następującą formę:.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Ten zestaw poleceń zapewnia informacje o dostępie do określonych usług, ze wszystkich zestawów, które znajdują się w przestrzeni nazw i pasują do zestawu etykiet:

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

Poniższe przykładowe dane wyjściowe pokazują, że dla każdego z naszych zapytań zostało wymuszone wzajemne szyfrowanie TLS. W danych wyjściowych znajdują się również zasady i zasady dotyczące zasad, które wymuszają wzajemne zaufanie TLS:

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

## <a name="roll-out-a-canary-release-of-the-application"></a>Wdrażanie aplikacji w wersji kanaryjskiej

Teraz wdróżmy nową wersję `2.0` `voting-app`składników, `voting-analytics`i `voting-storage` . Nowy `voting-storage` składnik używa programu MySQL zamiast Redis, `voting-app` a składniki i `voting-analytics` są aktualizowane, aby umożliwić im korzystanie z tego nowego `voting-storage` składnika.

`voting-app` Składnik obsługuje teraz funkcję flagi funkcji. Ta flaga funkcji umożliwia przetestowanie możliwości wersji programu Kanaryjskie Istio dla podzbioru użytkowników.

Na poniższym diagramie przedstawiono działanie, które zostanie uruchomione na końcu tej sekcji.

* `1.0` Wersjaskładnika`voting-analytics` , wersja `1.1` składnika i wersja`1.0` składnika ,sąwstaniekomunikowaćsięzesobą.`voting-storage` `voting-app`
* `2.0` Wersjaskładnika`voting-analytics` , wersja `2.0` składnika i wersja`2.0` składnika ,sąwstaniekomunikowaćsięzesobą.`voting-storage` `voting-app`
* Wersja `2.0`składnikajestdostępna tylkodlaużytkowników,którzymająokreślonąflagęfunkcji.`voting-app` Ta zmiana jest zarządzana przy użyciu flagi funkcji za pośrednictwem pliku cookie.

![Składniki aplikacji do głosowania i routingu AKS.](media/istio/components-and-routing-03.png)

Najpierw należy zaktualizować reguły docelowe Istio i usługi wirtualne, aby były one przeznaczone dla nowych składników. Te aktualizacje gwarantują, że nie są nieprawidłowo kierowane ruch do nowych składników, a użytkownicy nie otrzymują nieoczekiwanego dostępu:

```azurecli
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

Następujące przykładowe dane wyjściowe przedstawiają aktualizowane reguły docelowe i usługi wirtualne:

```console
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Następnie Dodajmy obiekty Kubernetes dla nowych składników wersji `2.0` . Należy również zaktualizować `voting-storage` usługę w celu `3306` uwzględnienia portu dla programu MySQL:

```azurecli
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

Następujące przykładowe dane wyjściowe pokazują, że obiekty Kubernetes zostały pomyślnie zaktualizowane lub utworzone:

```console
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Poczekaj, aż `2.0` wszystkie te zasobniki zostaną uruchomione. Użyj [polecenia kubectl Get][kubectl-get] — polecenie, aby wyświetlić wszystkie zasobniki w `voting` przestrzeni nazw:

```azurecli
kubectl get pods --namespace voting
```

Teraz powinno być możliwe przełączenie między wersją `1.0` i wersją `2.0` (Wyspy Kanaryjskie) aplikacji do głosowania. Flaga funkcji przełącznika w dolnej części ekranu ustawia plik cookie. Ten plik cookie jest używany przez `voting-app` usługę wirtualną do przesyłania użytkownikom nowej wersji. `2.0`

![NIE ustawiono wersji 1,0 aplikacji do głosowania AKS — flaga funkcji.](media/istio/canary-release-01.png)

![ZOSTANIE ustawiona wersja 2,0 flagi "aplikacja do głosowania AKS".](media/istio/canary-release-02.png)

Liczby głosu są różne w zależności od wersji aplikacji. Ta różnica oznacza, że korzystasz z dwóch różnych punktów końcowych magazynu.

## <a name="finalize-the-rollout"></a>Finalizowanie wdrożenia

Po pomyślnym przetestowaniu wersji programu kanaryjskiej `voting-app` zaktualizuj usługę wirtualną, aby kierować cały ruch `2.0` do wersji `voting-app` składnika. Wszyscy użytkownicy zobaczą wersję `2.0` aplikacji, niezależnie od tego, czy flaga funkcji jest ustawiona, czy nie:

![Składniki aplikacji do głosowania i routingu AKS.](media/istio/components-and-routing-04.png)

Zaktualizuj wszystkie reguły docelowe, aby usunąć wersje składników, które nie powinny już być aktywne. Następnie zaktualizuj wszystkie usługi wirtualne, aby zakończyć odwoływanie się do tych wersji.

Ponieważ nie ma już żadnego ruchu do żadnej ze starszych wersji składników, można teraz bezpiecznie usunąć wszystkie wdrożenia dla tych składników.

![Składniki aplikacji do głosowania i routingu AKS.](media/istio/components-and-routing-05.png)

Pomyślnie przeprowadzono nową wersję aplikacji do głosowania AKS.

## <a name="clean-up"></a>Czyszczenie 

W tym scenariuszu możesz usunąć AKSą aplikację do głosowania z klastra AKS, usuwając `voting` przestrzeń nazw w następujący sposób:

```azurecli
kubectl delete namespace voting
```

Następujące przykładowe dane wyjściowe pokazują, że wszystkie składniki aplikacji do głosowania AKS zostały usunięte z klastra AKS.

```console
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
[istio-install]: ./istio-install.md
