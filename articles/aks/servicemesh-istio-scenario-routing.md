---
title: Inteligentne routing i wersje kanaryjskie z istio w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak używać programu Istio do dostarczania inteligentnego routingu i wdrażania wersji kanarek w klastrze usługi Azure Kubernetes (AKS)
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 4a695957c287e69ff6b40e5a01254a729eaae441
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273003"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Użyj inteligentnego routingu i wersji kanaryjskich z Istio w usłudze Azure Kubernetes Service (AKS)

[Istio][istio-github] to siatka usługi typu open source, która zapewnia kluczowy zestaw funkcji w mikrousługach w klastrze Kubernetes. Funkcje te obejmują zarządzanie ruchem, tożsamość usługi i bezpieczeństwo, wymuszanie zasad i obserwowalność. Aby uzyskać więcej informacji na temat Istio, zobacz [oficjalnej Dokumentacji Istio?][istio-docs-concepts]

W tym artykule pokazano, jak korzystać z funkcji zarządzania ruchem istio. Przykładowa aplikacja do głosowania AKS służy do eksplorowania inteligentnego routingu i wersji kanarek.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Wdrażanie aplikacji
> * Aktualizowanie aplikacji
> * Rozwałkować kanarek zwolnienie aplikacji
> * Finalizowanie wdrożenia

## <a name="before-you-begin"></a>Przed rozpoczęciem

> [!NOTE]
> Ten scenariusz został przetestowany w `1.3.2`wersji Istio .

Kroki opisane w tym artykule zakładają, że utworzono `1.13` klaster AKS (Kubernetes i `kubectl` powyżej, z włączoną funkcją RBAC) i nawiązałeś połączenie z klastrem. Musisz również Istio zainstalowany w klastrze.

Jeśli potrzebujesz pomocy z dowolnym z tych elementów, zobacz [szybki start usługi AKS][aks-quickstart] i zainstaluj istio w wskazówkach [AKS.][istio-install]

## <a name="about-this-application-scenario"></a>Informacje o tym scenariuszu aplikacji

Przykładowa aplikacja do głosowania AKS udostępnia użytkownikom dwie opcje głosowania (**Koty** lub **Psy).** Istnieje składnik magazynu, który utrzymuje liczbę głosów dla każdej opcji. Ponadto istnieje składnik analizy, który zawiera szczegółowe informacje wokół głosów oddanych dla każdej opcji.

W tym scenariuszu aplikacji można `1.0` rozpocząć od wdrożenia `1.0` wersji aplikacji do głosowania i wersji składnika analizy. Składnik analizy zapewnia proste liczby głosów. Aplikacja do głosowania i składnik `1.0` analizy interakcji z wersją składnika magazynu, który jest wspierany przez Redis.

Uaktualnienie składnika analizy `1.1`do wersji , która zawiera liczby, a teraz sumy i wartości procentowe.

Podzbiór użytkowników `2.0` testuje wersję aplikacji za pośrednictwem wersji kanaryjnej. Ta nowa wersja używa składnika magazynu, który jest wspierany przez bazę danych MySQL.

Gdy masz pewność, `2.0` że wersja działa zgodnie z oczekiwaniami na `2.0` podgrupie użytkowników, możesz wdrożyć wersję dla wszystkich użytkowników.

## <a name="deploy-the-application"></a>Wdrażanie aplikacji

Zacznijmy od wdrożenia aplikacji w klastrze usługi Azure Kubernetes (AKS). Na poniższym diagramie przedstawiono, co `1.0` jest uruchamiane do końca tej sekcji — wersja wszystkich składników z żądaniami przychodzącymi obsługiwanymi za pośrednictwem bramy transferu danych przychodzących Istio:

![Składniki aplikacji AKS voting i routingu.](media/servicemesh/istio/scenario-routing-components-01.png)

Artefakty, które należy wykonać wraz z tym artykułem są dostępne w repozytorium GitHub [usługi Azure-Samples/aks-voting.The][github-azure-sample] artifacts you need to follow along with this article are available in the Azure-Samples/aks-voting-app GitHub repo. Artefakty można pobrać lub sklonować repozytorium w następujący sposób:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Zmień do następującego folderu w pobranym / sklonowanym repozytorium i uruchom wszystkie kolejne kroki z tego folderu:

```console
cd aks-voting-app/scenarios/intelligent-routing-with-istio
```

Najpierw utwórz obszar nazw w klastrze AKS dla przykładowej aplikacji do głosowania AKS o następującej `voting` nazwie:

```console
kubectl create namespace voting
```

Oznacz obszar nazw `istio-injection=enabled`etykietą . Ta etykieta nakazuje Istio, aby automatycznie wstrzyknąć istio-proxy jako sidecars do wszystkich zasobników w tej przestrzeni nazw.

```console
kubectl label namespace voting istio-injection=enabled
```

Teraz utwórzmy składniki aplikacji AKS Voting. Utwórz te `voting` składniki w obszarze nazw utworzonym w poprzednim kroku.

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

Poniższe przykładowe dane wyjściowe pokazują tworzone zasoby:

```output
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio ma pewne szczególne wymagania dotyczące zasobników i usług. Aby uzyskać więcej informacji, zobacz [dokumentację Istio Requirements for Strąki i usługi][istio-requirements-pods-and-services].

Aby wyświetlić zasobników, które zostały utworzone, użyj [polecenia kubectl get strąki][kubectl-get] w następujący sposób:

```console
kubectl get pods -n voting --show-labels
```

Poniższe przykładowe dane wyjściowe pokazuje, że istnieją trzy wystąpienia `voting-app` zasobnika i pojedyncze wystąpienie zarówno `voting-analytics` zasobników, jak i `voting-storage` zasobników. Każdy z zasobników ma dwa pojemniki. Jednym z tych pojemników jest komponent, `istio-proxy`a drugim jest:

```output
NAME                                    READY     STATUS    RESTARTS   AGE   LABELS
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s   app=voting-analytics,pod-template-hash=57c7fccb44,version=1.0
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s   app=voting-storage,pod-template-hash=5d8fcc89c4,version=1.0
```

Aby wyświetlić informacje o zasobniku, użyjemy [polecenia kubectl describe pod][kubectl-describe] z selektorami etykiet, aby wybrać `voting-analytics` zasobnik. Przefiltrujemy dane wyjściowe, aby wyświetlić szczegóły dwóch kontenerów znajdujących się w zasobniku:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-powershell.md)]

::: zone-end

Nie można połączyć się z aplikacją do głosowania, dopóki nie utworzysz [bramy][istio-reference-gateway] Istio i [usługi wirtualnej][istio-reference-virtualservice]. Te zasoby Istio kierują ruch z domyślnej bramy przychodzącej Istio do naszej aplikacji.

> [!NOTE]
> **Brama** jest składnikiem na krawędzi siatki usługi, który odbiera przychodzący lub wychodzący ruch HTTP i TCP.
> 
> **Usługa wirtualna** definiuje zestaw reguł routingu dla jednej lub więcej usług docelowych.

Użyj `kubectl apply` polecenia, aby wdrożyć yaml bramy i usługi wirtualnej. Pamiętaj, aby określić obszar nazw, w których te zasoby są wdrażane.

```console
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

Poniższy przykładowy wynik przedstawia nową bramę i usługę wirtualną, która jest tworzona:

```output
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Uzyskaj adres IP bramy istio ingress za pomocą następującego polecenia:

```output
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

Poniższy przykładowy wynik przedstawia adres IP bramy przychodzącej:

```output
20.188.211.19
```

Otwórz przeglądarkę i wklej adres IP. Zostanie wyświetlona przykładowa aplikacja do głosowania AKS.

![Aplikacja AKS Voting działająca w naszym klastrze AKS istio.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

Informacje na dole ekranu pokazuje, że aplikacja `1.0` używa `voting-app` wersji `1.0` `voting-storage` i wersji (Redis).

## <a name="update-the-application"></a>Aktualizowanie aplikacji

Zastosujmy nową wersję składnika analizy. Ta nowa `1.1` wersja wyświetla sumy i wartości procentowe oprócz liczby dla każdej kategorii.

Na poniższym diagramie pokazano, co będzie uruchomione `1.1` na `voting-analytics` końcu tej sekcji — `voting-app` tylko wersja naszego składnika ma ruch kierowany z komponentu. Mimo że `1.0` wersja `voting-analytics` naszego składnika nadal działa `voting-analytics` i odwołuje się do niej usługa, serwery proxy Istio nie zezwalają na ruch do i z niego.

![Składniki aplikacji AKS voting i routingu.](media/servicemesh/istio/scenario-routing-components-02.png)

Zastosujmy `1.1` wersję `voting-analytics` składnika. Utwórz ten `voting` składnik w obszarze nazw:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

Poniższe przykładowe dane wyjściowe pokazują tworzone zasoby:

```output
deployment.apps/voting-analytics-1-1 created
```

Otwórz ponownie przykładową aplikację do głosowania AKS w przeglądarce, używając adresu IP bramy istio ingress uzyskanej w poprzednim kroku.

Twoja przeglądarka zmienia się między dwoma widokami pokazanymi poniżej. Ponieważ używasz [usługi][kubernetes-service] Kubernetes `voting-analytics` dla składnika z tylko`app: voting-analytics`jednym selektorem etykiet ( ), Kubernetes używa domyślnego zachowania okrężnego między zasobnikami, które pasują do tego selektora. W tym przypadku jest `1.0` to `1.1` zarówno `voting-analytics` wersja, jak i zasobników.

![Wersja 1.0 składnika analitycznego uruchomionego w naszej aplikacji do głosowania AKS.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

![Wersja 1.1 składnika analitycznego uruchomionego w naszej aplikacji do głosowania AKS.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Można wizualizować przełączanie między dwiema `voting-analytics` wersjami składnika w następujący sposób. Pamiętaj, aby użyć adresu IP własnej bramy Istio Ingress.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

Poniższy przykładowy wynik przedstawia odpowiednią część zwróconej witryny sieci web, gdy witryna przełącza się między wersjami:

```output
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Blokowanie ruchu do wersji 1.1 aplikacji

Teraz zablokujmy ruch `1.1` tylko `voting-analytics` do wersji `1.0` składnika `voting-storage` i do wersji składnika. Następnie należy zdefiniować reguły routingu dla wszystkich innych składników.

> * **Usługa wirtualna** definiuje zestaw reguł routingu dla jednej lub więcej usług docelowych.
> * **Reguła docelowa** definiuje zasady ruchu i zasady specyficzne dla wersji.
> * Zasady **Policy** określa, jakie metody uwierzytelniania mogą być akceptowane na obciążeniach.

Użyj `kubectl apply` polecenia, aby zastąpić definicję `voting-app` usługi wirtualnej na swoim i dodać [reguły miejsca docelowego][istio-reference-destinationrule] i [usługi wirtualne][istio-reference-virtualservice] dla innych składników. Do obszaru `voting` nazw zostaną dodane [zasady,][istio-reference-policy] aby upewnić się, że cała komunikacja między usługami jest zabezpieczona przy użyciu wzajemnych certyfikatów TLS i klientów.

* Zasady `peers.mtls.mode` ustawiła `STRICT` się, aby upewnić się, że `voting` wzajemne TLS jest wymuszane między usługami w obszarze nazw.
* We wszystkich `trafficPolicy.tls.mode` naszych `ISTIO_MUTUAL` Zasadach docelowych. Istio zapewnia usługi o silnej tożsamości i zabezpiecza komunikację między usługami przy użyciu wzajemnych certyfikatów TLS i klientów, którymi zarządza Istio w sposób przejrzysty.

```console
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

Poniższe przykładowe dane wyjściowe pokazują nowe zasady, reguły miejsca docelowe i usługi wirtualne aktualizowane/tworzone:

```output
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Jeśli ponownie otworzysz aplikację AKS Voting w przeglądarce, `voting-analytics` składnik będzie `voting-app` używany tylko nowa wersja `1.1` składnika.

![Wersja 1.1 składnika analitycznego uruchomionego w naszej aplikacji do głosowania AKS.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Można wizualizować, że teraz są `1.1` teraz `voting-analytics` tylko kierowane do wersji składnika w następujący sposób. Pamiętaj, aby użyć adresu IP własnej bramy Istio Ingress:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

Poniższy przykład wyjściowy przedstawia odpowiednią część zwróconej witryny sieci web:

```output
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Teraz potwierdźmy, że Istio używa wzajemnego TLS do zabezpieczenia komunikacji między każdą z naszych usług. W tym celu użyjemy polecenia [authn tls-check][istioctl-authn-tls-check] na `istioctl` pliku binarnym klienta, które przyjmuje następującą formę.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Ten zestaw poleceń dostarcza informacji o dostępie do określonych usług, ze wszystkich zasobników, które znajdują się w obszarze nazw i pasują do zestawu etykiet:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-powershell.md)]

::: zone-end

W tym przykładzie dane wyjściowe pokazuje, że wzajemne TLS jest wymuszane dla każdego z naszych zapytań powyżej. Dane wyjściowe pokazują również zasady i reguły docelowe, które wymusza wzajemne TLS:

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

## <a name="roll-out-a-canary-release-of-the-application"></a>Rozwałkować kanarek zwolnienie aplikacji

Teraz zastosujmy `2.0` nową `voting-app`wersję `voting-analytics`programu `voting-storage` , i składników. Nowy `voting-storage` składnik używa MySQL zamiast Redis, `voting-app` `voting-analytics` a składniki i składniki są `voting-storage` aktualizowane, aby umożliwić im korzystanie z tego nowego składnika.

Składnik `voting-app` obsługuje teraz funkcje flagi funkcji. Ta flaga funkcji umożliwia przetestowanie możliwości wydania kanaryjnego Istio dla podzbioru użytkowników.

Na poniższym diagramie przedstawiono, co będzie działać na końcu tej sekcji.

* Wersja `1.0` `voting-app` składnika, `1.1` wersja `voting-analytics` składnika `1.0` i `voting-storage` wersja składnika są w stanie komunikować się ze sobą.
* Wersja `2.0` `voting-app` składnika, `2.0` wersja `voting-analytics` składnika `2.0` i `voting-storage` wersja składnika są w stanie komunikować się ze sobą.
* Wersja `2.0` składnika `voting-app` są dostępne tylko dla użytkowników, którzy mają określony zestaw flag funkcji. Ta zmiana jest zarządzana przy użyciu flagi funkcji za pośrednictwem pliku cookie.

![Składniki aplikacji AKS voting i routingu.](media/servicemesh/istio/scenario-routing-components-03.png)

Najpierw zaktualizuj reguły miejsca docelowego Istio i usługi wirtualne, aby zaspokoić potrzeby tych nowych składników. Te aktualizacje zapewniają, że ruch nie jest nieprawidłowo kierowany do nowych składników, a użytkownicy nie uzyskują nieoczekiwanego dostępu:

```console
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

Poniższe przykładowe dane wyjściowe pokazują reguły miejsca docelowego i usługi wirtualne aktualizowane:

```output
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Następnie dodajmy Kubernetes obiektów dla składników `2.0` nowej wersji. Można również `voting-storage` zaktualizować usługę, `3306` aby uwzględnić port dla MySQL:

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

Poniższy przykładowy wynik pokazuje, że obiekty Kubernetes zostały pomyślnie zaktualizowane lub utworzone:

```output
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Poczekaj, aż `2.0` wszystkie zasobniki wersji są uruchomione. Użyj [polecenia kubectl get strąki][kubectl-get] z przełącznikiem zegarka, `-w` `voting` aby obserwować zmiany we wszystkich zasobnikach w obszarze nazw:

```console
kubectl get pods --namespace voting -w
```

Teraz powinieneś być w stanie `1.0` przełączać się między wersją a wersją `2.0` (kanarek) aplikacji do głosowania. Przełącznik flagi funkcji u dołu ekranu ustawia plik cookie. Ten plik cookie `voting-app` jest używany przez usługę wirtualną do kierowania użytkowników do nowej wersji `2.0`.

![Wersja 1.0 aplikacji AKS Voting - flaga funkcji NIE JEST ustawiona.](media/servicemesh/istio/scenario-routing-canary-release-01.png)

![Wersja 2.0 aplikacji AKS Voting - ustawiona flaga funkcji.](media/servicemesh/istio/scenario-routing-canary-release-02.png)

Liczba głosów różni się między wersjami aplikacji. Ta różnica podkreśla, że używasz dwóch różnych zaplecza magazynu.

## <a name="finalize-the-rollout"></a>Finalizowanie wdrożenia

Po pomyślnym przetestowaniu wersji kanarek zaktualizuj usługę wirtualną, `voting-app` aby przekierować cały ruch do wersji `2.0` składnika. `voting-app` Następnie wszyscy użytkownicy zobaczą wersję `2.0` aplikacji, niezależnie od tego, czy flaga funkcji jest ustawiona, czy nie:

![Składniki aplikacji AKS voting i routingu.](media/servicemesh/istio/scenario-routing-components-04.png)

Zaktualizuj wszystkie reguły docelowe, aby usunąć wersje składników, które nie mają być już aktywne. Następnie zaktualizuj wszystkie usługi wirtualne, aby zatrzymać odwoływanie się do tych wersji.

Ponieważ nie ma już żadnego ruchu do żadnej ze starszych wersji składników, można teraz bezpiecznie usunąć wszystkie wdrożenia dla tych składników.

![Składniki aplikacji AKS voting i routingu.](media/servicemesh/istio/scenario-routing-components-05.png)

Teraz pomyślnie wdrożono nową wersję aplikacji do głosowania AKS.

## <a name="clean-up"></a>Czyszczenie 

Aplikację do głosowania AKS, której używaliśmy w tym scenariuszu, można `voting` usunąć z klastra AKS usunięcie obszaru nazw w następujący sposób:

```console
kubectl delete namespace voting
```

Poniższe przykładowe dane wyjściowe pokazują, że wszystkie składniki aplikacji do głosowania AKS zostały usunięte z klastra AKS.

```output
namespace "voting" deleted
```

## <a name="next-steps"></a>Następne kroki

Dodatkowe scenariusze można eksplorować w [przykładzie aplikacji Istio Bookinfo][istio-bookinfo-example].

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
