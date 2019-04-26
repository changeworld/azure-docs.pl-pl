---
title: Inteligentnego routingu i kanarkowe wydania przy użyciu Istio w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak za pomocą Istio zapewniają routing inteligentne i wdrożyć kanarkowe wydania w klastrze usługi Azure Kubernetes Service (AKS)
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 12/03/2018
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 0a4e5e7e310a9949ee59291c2032eafda46955a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60465942"
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

W krokach szczegółowo opisanych w tym artykule przyjęto założenie, został utworzony klaster AKS (Kubernetes 1.10 i nowszych, przy użyciu RBAC włączone), a `kubectl` połączenia z klastrem. Należy również Istio zainstalowany w klastrze.

Jeśli potrzebujesz pomocy przy użyciu dowolnego z tych elementów, zobacz [szybkiego startu usługi AKS] [ aks-quickstart] i [zainstalować Istio w usłudze AKS][istio-install].

## <a name="about-this-application-scenario"></a>Temat tego scenariusza aplikacji

Przykładową aplikację głosowania AKS zapewnia dwie opcje głosowania (koty lub psy) dla użytkowników. Brak składnika magazynu, która utrwala liczba głosów dla każdej opcji. Ponadto jest składnik analizy, który zawiera szczegółowe informacje dotyczące głosów dla każdej opcji.

W tym artykule, możesz uruchomić wdrażania wersji *1.0* aplikacja do głosowania, a wersja *1.0* składnika analizy. Składnik analizy zapewnia proste liczby liczba głosów. Aplikacja do głosowania i składnik analizy wchodzą w interakcję z wersją *1.0* składnika magazynu, która jest przechowywana w pamięci podręcznej Redis.

Można uaktualnić do wersji składnika analytics *1.1*, które zapewnia liczniki i sumy teraz i wartości procentowe.

Podzbiór użytkowników wersji testowej *2.0* aplikacji za pośrednictwem wydania canary. Korzysta z tej nowej wersji składnika magazynu, która jest wspierana przez bazę danych MySQL.

Gdy masz pewność, ta wersja *2.0* działa zgodnie z oczekiwaniami na podzbiór użytkowników, możesz wdrożyć wersji *2.0* do wszystkich użytkowników.

## <a name="deploy-the-application"></a>Wdrażanie aplikacji

Zacznijmy od wdrożenia aplikacji do klastra Azure Kubernetes Service (AKS). Na poniższym diagramie przedstawiono, co działa do końca tej sekcji — wersja *1.0* wszystkich składników za pomocą przychodzących żądań obsługiwanych za pośrednictwem bramy ruch przychodzący Istio:

![AKS głosowania składniki aplikacji i routing.](media/istio/components-and-routing-01.png)

Artefakty muszą skorzystać z tego artykułu są dostępne w [Azure-Samples/aks aplikacją do głosowania —] [ github-azure-sample] repozytorium GitHub. Możesz pobrać artefakty lub sklonuj repozytorium w następujący sposób:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Zmiany do następującego folderu w repozytorium pobrane / sklonowany i uruchom wszystkie kolejne kroki z tego folderu:

```console
cd scenarios/intelligent-routing-with-istio
```

Najpierw utworzyć przestrzeń nazw w klastrze AKS dla przykładowej usługi AKS głosowania aplikacji o nazwie *głosowania* w następujący sposób:

```console
kubectl create namespace voting
```

Etykieta przestrzeni nazw z `istio-injection=enabled`. Ta etykieta powoduje, że Istio automatycznie iniekcję proxy istio jako przyczepkami, do wszystkich zasobników w tej przestrzeni nazw.

```console
kubectl label namespace voting istio-injection=enabled
```

Teraz Utwórzmy składników dla aplikacji do głosowania w usłudze AKS. Tworzenie tych składników *głosowania* przestrzeni nazwy utworzonej w poprzednim kroku.

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

Następujące przykładowe dane wyjściowe pokazuje, że zasoby zostały pomyślnie utworzone:

```
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

```console
kubectl get pods -n voting
```

Następujące przykładowe dane wyjściowe pokazuje, istnieją trzy wystąpienia *aplikacją do głosowania* zasobników i pojedyncze wystąpienie *głosowania analytics* i *głosowania magazynu* zasobników. Każda z zasobników ma dwa kontenery. Jedną z tych kontenerów jest składnikiem, a drugi to *istio proxy*:

```
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-669f99dcc8-lzh7k   2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-bdmld         2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-gcrng         2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-strzc         2/2       Running   0          1m
voting-storage-1-0-7954799d96-5fv9r     2/2       Running   0          1m
```

Aby wyświetlić informacje o zasobnik, użyj [kubectl opisują zasobnika][kubectl-describe]. Zastąp nazwę zasobnik w własnego klastra usługi AKS z danych wyjściowych poprzedniej nazwy zasobnika:

```console
kubectl describe pod voting-app-1-0-6c65c4bdd4-bdmld --namespace voting
```

*Istio proxy* kontenera automatycznie wstrzyknięciu przez Istio do zarządzania ruchu sieciowego do i z składniki, jak pokazano w następujących przykładowych danych wyjściowych:

```
[...]
Containers:
  voting-app:
    Image:         mcr.microsoft.com/aks/samples/voting/app:1.0
    ...
  istio-proxy:
    Image:         dockerhub.azk8s.cn/istio/proxyv2:1.0.4
[...]
```

Nie można połączyć z głosowania aplikacji, dopóki nie zostanie utworzony Istio [bramy] [ istio-reference-gateway] i [usług wirtualnych][istio-reference-virtualservice]. Te zasoby Istio kierować ruch z bramy domyślnej Istio ruch przychodzący do naszej aplikacji.

> [!NOTE]
> A *bramy* jest składnikiem na brzegu sieci usługi, który odbiera przychodzący lub wychodzący ruch protokołu HTTP i TCP.
>
> A *usług wirtualnych* definiuje zestaw reguł rozsyłania dla co najmniej jednej usługi docelowej.

Użyj `istioctl` klienta binarne do wdrożenia bramy i usługa wirtualnych yaml. Podobnie jak w przypadku `kubectl apply` polecenia, pamiętaj, aby określić który te zasoby są wdrażane w przestrzeni nazw.

```console
istioctl create -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

Uzyskaj adres IP bramy ruch przychodzący Istio, używając następującego polecenia:

```console
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

Następujące przykładowe dane wyjściowe wyświetla adres IP bramy transferu danych przychodzących:

```
52.187.250.239
```

Otwórz przeglądarkę i wklej adres IP. Przykładową aplikację głosowania AKS jest wyświetlany.

![Jest uruchomiona w naszym Istio aplikacja do głosowania w usłudze AKS włączone klastra AKS.](media/istio/deploy-app-01.png)

Informacje w dolnej części ekranu pokazuje, że aplikacja korzysta z wersji *1.0* z *aplikacją do głosowania* i wersji *1.0* (Redis) jako opcji magazynu.

## <a name="update-the-application"></a>Aktualizowanie aplikacji

Firma Microsoft umożliwia wdrażanie nowej wersji składnika usługi analytics. Nowa wersja *1.1* wyświetlane sumy i wartości procentowych, oprócz liczbę elementów w każdej kategorii.

Na poniższym diagramie przedstawiono, co działa na końcu tej sekcji — tylko wersja *1.1* z naszych *głosowania analytics* składnik ma ruch kierowany od *aplikacją do głosowania* składnik. Mimo że wersji *1.0* z naszych *głosowania analytics* składnik będzie nadal działać i odwołuje się do niej *głosowania analytics* proxy Istio nie zezwalaj na ruch usługi do i z niego.

![AKS głosowania składniki aplikacji i routing.](media/istio/components-and-routing-02.png)

Zajmijmy się wdrożeniem wersji *1.1* z *głosowania analytics* składnika. Utwórz ten składnik w *głosowania* przestrzeni nazw:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

Otwórz przykładowe dane, których AKS aplikacja do głosowania w przeglądarce ponownie przy użyciu adresu IP bramy ruch przychodzący Istio uzyskanego w poprzednim kroku.

Przeglądarka przełącza między dwoma widokami, pokazano poniżej. Ponieważ używasz usługi Kubernetes [usługi] [ kubernetes-service] dla *głosowania analytics* składnika za pomocą selektora pojedynczej etykiecie (`app: voting-analytics`), domyślnie korzysta z rozwiązania Kubernetes działanie okrężne między zasobników, które odpowiadają selektora. W tym przypadku jest zarówno wersja *1.0* i *1.1* z Twojej *głosowania analytics* zasobników.

![Wersja 1.0 składnika analytics działające w naszej aplikacji do głosowania w usłudze AKS.](media/istio/deploy-app-01.png)

![Wersja 1.1 składnika analytics działające w naszej aplikacji do głosowania w usłudze AKS.](media/istio/update-app-01.png)

Można wizualizować przełączanie między dwoma wersjami *głosowania analytics* składnika w następujący sposób. Pamiętaj, aby użyć adresu IP własnej Istio bramy transferu danych przychodzących.

```console
INGRESS_IP=52.187.250.239
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
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

Teraz możemy zablokować ruch tylko z wersji *1.1* z *głosowania analytics* składnika i do wersji *1.0* z *głosowania magazynu* składnik. Następnie zdefiniuj reguły routingu dla wszystkich innych składników.

> * A *usług wirtualnych* definiuje zestaw reguł rozsyłania dla co najmniej jednej usługi docelowej.
> * A *Reguła docelowa* definiuje zasady ruchu i określonych wersji.
> * A *zasad* definiuje, jakie metody uwierzytelniania mogą być akceptowane na workload(s).

Możesz użyć `istioctl` klienta binarne, aby zamienić definicję usług wirtualnych, na Twojej *aplikacją do głosowania* i Dodaj [docelowy reguły] [ istio-reference-destinationrule] i [ Wirtualne usługi] [ istio-reference-virtualservice] dla innych składników.

Możesz również dodać [zasad] [ istio-reference-policy] do *głosowania* przestrzeni nazw, aby upewnić się, że wszystkie komunikacji między usługami jest zabezpieczony za pomocą protokołu TLS wzajemnego i certyfikatów klientów.

Ponieważ ma istniejącej definicji usług wirtualnych dla *aplikacją do głosowania* możesz zastąpić, użyj `istioctl replace` polecenia w następujący sposób:

```console
istioctl replace -f istio/step-2a-update-voting-app-virtualservice.yaml --namespace voting
```

Następujące przykładowe dane wyjściowe pokazuje zaktualizowano Istio usługi wirtualnych:

```
Updated config virtual-service/voting/voting-app to revision 141902
```

Następnie użyj `istioctl create` polecenie, aby dodać nowe zasady i również nowe reguły docelowego i wirtualnych usług dla wszystkich innych składników.

* Zasady zawierają `peers.mtls.mode` równa `STRICT` aby upewnić się, że wzajemne TLS są wymuszane między usługami w ramach *głosowania* przestrzeni nazw.
* Można również ustawić `trafficPolicy.tls.mode` do `ISTIO_MUTUAL` w naszych zasadach docelowej. Istio udostępnia usługi za pomocą silnej tożsamości i zabezpiecza komunikację między usługami przy użyciu protokołu TLS wzajemnego i certyfikatów klientów, które Istio niewidocznie zarządza.

```console
istioctl create -f istio/step-2b-add-routing-for-all-components.yaml --namespace voting
```

Następujące przykładowe dane wyjściowe pokazuje nowe zasady, a docelowy reguły, a wirtualne usługi zostały utworzone pomyślnie:

```
Created config policy/voting/default to revision 142118
Created config destination-rule/voting/voting-app at revision 142119
Created config destination-rule/voting/voting-analytics at revision 142120
Created config virtual-service/voting/voting-analytics at revision 142121
Created config destination-rule/voting/voting-storage at revision 142122
Created config virtual-service/voting/voting-storage at revision 142123
```

Jeśli otworzysz aplikację głosowania AKS w przeglądarce ponownie tylko nową wersję *1.1* z *głosowania analytics* składnik jest używany przez *aplikacją do głosowania* składnika.

![Wersja 1.1 składnika analytics działające w naszej aplikacji do głosowania w usłudze AKS.](media/istio/update-app-01.png)

Można łatwiej wizualizować, że teraz są możemy tylko kierowane do wersji *1.1* z Twojej *głosowania analytics* składnika w następujący sposób. Pamiętaj, aby korzystać z adresu IP bramy Istio transferu danych przychodzących.

Można wizualizować, że teraz są możesz tylko kierowane do wersji *1.1* z Twojej *głosowania analytics* składnika w następujący sposób. Pamiętaj, aby użyć adresu IP własnej bramy ruch przychodzący Istio:

```azurecli
INGRESS_IP=52.187.250.239
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

Następujące przykładowe dane wyjściowe przedstawia istotne części zwrócone witryny sieci web:

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Upewnij się, że Istio używa wzajemnego protokół TLS w celu zagwarantowania bezpiecznej komunikacji między każdej z naszych usług. Następujące polecenia, sprawdź ustawienia protokołu TLS dla każdego *aplikacją do głosowania* usług:

```console
istioctl authn tls-check voting-app.voting.svc.cluster.local
istioctl authn tls-check voting-analytics.voting.svc.cluster.local
istioctl authn tls-check voting-storage.voting.svc.cluster.local
```

To następujące przykładowe dane wyjściowe pokazuje, czy wzajemne TLS są wymuszane dla wszystkich usług za pośrednictwem zasadach i regułach miejsce docelowe:

```
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>Wdrażanie wydania canary aplikacji

Teraz Zajmijmy się wdrożeniem nowej wersji *2.0* z *aplikacją do głosowania*, *głosowania analytics*, i *głosowania magazynu* składników. Nowy *głosowania magazynu* składnika użyj MySQL zamiast Redis i *aplikacją do głosowania* i *głosowania analizy* składniki są aktualizowane, aby umożliwić im użyć tego nowego *głosowania magazynu* składnika.

*Aplikacją do głosowania* składnik obsługuje teraz funkcje flagi funkcji. Flaga ta funkcja umożliwia testowanie możliwości wydania canary Istio dla podzbioru użytkowników.

Na poniższym diagramie przedstawiono, co działa na końcu tej sekcji.

* Wersja *1.0* z *aplikacją do głosowania* składnika, wersja *1.1* z *głosowania analytics* składników i wersji  *1.0* z *głosowania magazynu* składników są w stanie komunikować się ze sobą.
* Wersja *2.0* z *aplikacją do głosowania* składnika, wersja *2.0* z *głosowania analytics* składników i wersji  *w wersji 2.0* z *głosowania magazynu* składników są w stanie komunikować się ze sobą.
* Wersja *2.0* z *aplikacją do głosowania* składnika tylko są dostępne dla użytkowników, którzy mają ustawioną flagą określonych funkcji. Ta zmiana jest zarządzana przy użyciu flagi funkcji za pośrednictwem pliku cookie.

![AKS głosowania składniki aplikacji i routing.](media/istio/components-and-routing-03.png)

Najpierw należy zaktualizować Istio docelowy reguły i wirtualnych usług w celu zaspokojenia tych nowych składników. Te aktualizacje upewnij się, że nie kierować ruchem niepoprawnie do nowych składników użytkowników nie uzyskasz nieoczekiwanym dostępem:

```console
istioctl replace -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

Następujące przykładowe dane wyjściowe pokazuje, że pomyślnie Zaktualizowano reguły docelowego i wirtualnych usług:

```
Updated config destination-rule/voting/voting-app to revision 150930
Updated config virtual-service/voting/voting-app to revision 150931
Updated config destination-rule/voting/voting-analytics to revision 150937
Updated config virtual-service/voting/voting-analytics to revision 150939
Updated config destination-rule/voting/voting-storage to revision 150940
Updated config virtual-service/voting/voting-storage to revision 150941
```

Następnie Dodajmy obiekty usługi Kubernetes z nową wersją *2.0* składników. Możesz także zaktualizować *głosowania magazynu* usługi, aby uwzględnić *3306* porcie MySQL:

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

Następujące przykładowe dane wyjściowe zawierają obiekty usługi Kubernetes jest pomyślnie uaktualniona lub utworzone:

```
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Poczekaj, aż wszystkie wersje *2.0* zasobniki zaczną działać. Użyj [kubectl get pods-] [ kubectl-get] polecenie, aby wyświetlić wszystkie zasobników w *głosowania* przestrzeni nazw:

```azurecli
kubectl get pods --namespace voting
```

Teraz powinno być możliwe przełączyć się między wersją *1.0* i wersji *2.0* (canary) aplikacji do głosowania. Przełącz flagę funkcji, w dolnej części ekranu, ustawia plik cookie. Ten plik cookie jest używany przez *aplikacją do głosowania* usługa wirtualnych w celu przekierowania użytkowników do nowej wersji *2.0*.

![Wersja 1.0 aplikacji do głosowania w usłudze AKS — flagę funkcji, który nie jest ustawiona.](media/istio/canary-release-01.png)

![Wersja 2.0 aplikacji do głosowania w usłudze AKS — ustawiona jest flaga funkcji.](media/istio/canary-release-02.png)

Liczba głosów różnią się między wersjami aplikacji. Różnica ta wyróżnia, że używasz dwóch różnych magazynu zaplecza.

## <a name="finalize-the-rollout"></a>Zakończ proces wdrażania

Po pomyślnym przetestowaniu wydania canary, zaktualizować *aplikacją do głosowania* usługa wirtualnego do kierowania ruchu do wersji *2.0* z *aplikacją do głosowania* składnika. Wszyscy użytkownicy widzą wersji *2.0* aplikacji, niezależnie od tego, czy flaga funkcji jest ustawiona lub nie:

![AKS głosowania składniki aplikacji i routing.](media/istio/components-and-routing-04.png)

Zaktualizuj wszystkie reguły docelowej do usunięcia wersje składników nie ma już aktywne. Następnie należy zaktualizować wirtualne usługi można zatrzymać odwołujące się do tych wersji.

Ponieważ jest już cały ruch do wszystkich starszych wersji składników, możesz teraz bezpiecznie usunąć wszystkie wdrożenia dla tych składników.

![AKS głosowania składniki aplikacji i routing.](media/istio/components-and-routing-05.png)

Nową wersję aplikacji do głosowania AKS teraz zostały pomyślnie udostępniona.

## <a name="next-steps"></a>Kolejne kroki

Możesz eksplorować inne scenariusze przy użyciu [Przykładowa aplikacja Bookinfo Istio][istio-bookinfo-example].

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/spec-requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#Gateway
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#VirtualService
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#DestinationRule
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./istio-install.md