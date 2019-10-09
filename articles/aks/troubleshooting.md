---
title: Rozwiązywanie typowych problemów z usługą Azure Kubernetes
description: Dowiedz się, jak rozwiązywać typowe problemy związane z korzystaniem z usługi Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: d2561b1882ea612f29c0ff0eeb4bd6614403c9ff
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025482"
---
# <a name="aks-troubleshooting"></a>Rozwiązywanie problemów z AKS

W przypadku tworzenia klastrów usługi Azure Kubernetes Service (AKS) lub zarządzania nimi czasami mogą wystąpić problemy. W tym artykule opisano niektóre typowe problemy i kroki rozwiązywania problemów.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Ogólnie rzecz biorąc, gdzie można znaleźć informacje o problemach z debugowaniem Kubernetes?

Wypróbuj [oficjalny przewodnik dotyczący rozwiązywania problemów z klastrami Kubernetes](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Istnieje również [Przewodnik rozwiązywania problemów](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)Opublikowany przez inżyniera firmy Microsoft w celu rozwiązywania problemów z planami, węzłami, klastrami i innymi funkcjami.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Otrzymuję błąd "Przekroczono limit przydziału" podczas tworzenia lub uaktualniania. Co mamy zrobić? 

Musisz [zażądać rdzeni](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Co to jest ustawienie maksymalnej liczby elementów w poszczególnych węzłach dla AKS?

Ustawienie Maksymalna liczba sztuk na węzeł domyślnie jest 30, Jeśli klaster AKS jest wdrażany w Azure Portal.
Ustawienie maksymalny rozmiar poszczególnych węzłów domyślnie 110 w przypadku wdrażania klastra AKS w interfejsie wiersza polecenia platformy Azure. (Upewnij się, że używasz najnowszej wersji interfejsu wiersza polecenia platformy Azure). To ustawienie domyślne można zmienić przy użyciu flagi `–-max-pods` w poleceniu `az aks create`.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Otrzymuję błąd insufficientSubnetSize podczas wdrażania klastra AKS przy użyciu zaawansowanej sieci. Co mamy zrobić?

Jeśli używana jest usługa Azure CNI (Advanced Network), AKS przydzieli adres IP na podstawie skonfigurowanego węzła "Max-". Liczba węzłów w klastrze AKS może być w dowolnym miejscu od 1 do 110. W oparciu o skonfigurowaną maksymalną liczbę zasobników na węzeł rozmiar podsieci powinien być większy niż "iloczyn liczby węzłów i maks. pod na węzeł". Poniższe podstawowe równanie zawiera opis:

Rozmiar podsieci > liczbę węzłów w klastrze (biorąc pod uwagę przyszłe wymagania dotyczące skalowania) * Maksymalna liczba zasobników na węzeł.

Aby uzyskać więcej informacji, zobacz [Planowanie adresów IP w klastrze](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Mój pod jest zablokowany w trybie CrashLoopBackOff. Co mamy zrobić?

Może istnieć różne przyczyny zablokowania w tym trybie. Możesz zajrzeć do:

* Samego siebie, przy użyciu `kubectl describe pod <pod-name>`.
* Dzienniki przy użyciu `kubectl log <pod-name>`.

Aby uzyskać więcej informacji na temat rozwiązywania problemów, zobacz [debugowanie aplikacji](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Próbuję włączyć funkcję RBAC w istniejącym klastrze. Jak to zrobić?

Niestety włączenie kontroli dostępu opartej na rolach (RBAC) w istniejących klastrach nie jest obecnie obsługiwane. Należy jawnie utworzyć nowe klastry. W przypadku korzystania z interfejsu wiersza polecenia RBAC jest domyślnie włączone. Jeśli używasz portalu AKS, przycisk przełączania umożliwiający włączenie RBAC jest dostępny w przepływie pracy tworzenia.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Po utworzeniu klastra z włączoną funkcją RBAC przy użyciu interfejsu wiersza polecenia platformy Azure z wartościami domyślnymi lub Azure Portal, a teraz widzimy wiele ostrzeżeń na pulpicie nawigacyjnym Kubernetes. Pulpit nawigacyjny służący do pracy bez żadnych ostrzeżeń. Co mamy zrobić?

Przyczyną ostrzeżeń na pulpicie nawigacyjnym jest to, że klaster jest teraz włączony przy użyciu RBAC i dostęp do niego jest domyślnie wyłączony. Ogólnie rzecz biorąc, to podejście jest dobrym rozwiązaniem, ponieważ domyślne narażenie pulpitu nawigacyjnego na wszystkich użytkowników klastra może prowadzić do zagrożeń bezpieczeństwa. Jeśli nadal chcesz włączyć pulpit nawigacyjny, postępuj zgodnie z instrukcjami w [tym wpisie w blogu](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Nie można nawiązać połączenia z pulpitem nawigacyjnym. Co mamy zrobić?

Najprostszym sposobem uzyskania dostępu do usługi poza klastrem jest uruchomienie `kubectl proxy`, które serwery proxy żądania wysyłane do portu localhost 8001 do serwera interfejsu API Kubernetes. Z tego miejsca serwer interfejsu API może być serwerem proxy usługi: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Jeśli nie widzisz pulpitu nawigacyjnego Kubernetes, sprawdź, czy `kube-proxy` pod działaniem w przestrzeni nazw `kube-system`. Jeśli nie jest w stanie uruchomionym, Usuń element pod, a zostanie uruchomiony ponownie.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Nie mogę pobrać dzienników przy użyciu dzienników polecenia kubectl lub nie mogę nawiązać połączenia z serwerem interfejsu API. Otrzymuję komunikat "błąd z serwera: błąd podczas wybierania numeru zaplecza: wybierz TCP...". Co mamy zrobić?

Upewnij się, że domyślna grupa zabezpieczeń sieci nie jest modyfikowana i że dla połączenia z serwerem interfejsu API jest otwarty zarówno port 22, jak i 9000. Sprawdź, czy `tunnelfront` pod jest uruchomiona w przestrzeni nazw *polecenia-system* przy użyciu polecenia `kubectl get pods --namespace kube-system`. Jeśli tak nie jest, Wymuś usunięcie elementu pod i zostanie on ponownie uruchomiony.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Próbuję uaktualnić lub skalować i uzyskać "komunikat: zmiana właściwości" elementu imagereference "jest niedozwolona. Jak mogę rozwiązać ten problem?

Ten błąd może być spowodowany modyfikacją tagów w węzłach agenta wewnątrz klastra AKS. Modyfikowanie i usuwanie tagów oraz innych właściwości zasobów w grupie zasobów MC_ * może prowadzić do nieoczekiwanych wyników. Modyfikacja zasobów w grupie MC_ * w klastrze AKS powoduje przerwanie celu poziomu usługi (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Otrzymuję błędy, które są w stanie awarii mojego klastra, a uaktualnienie lub skalowanie nie będzie działało, dopóki nie zostanie naprawione

*Ta pomoc w rozwiązywaniu problemów jest skierowana z https://aka.ms/aks-cluster-failed*

Ten błąd występuje, gdy klastry wchodzą w stan niepowodzenia z wielu powodów. Postępuj zgodnie z poniższymi instrukcjami, aby rozwiązać stan awarii klastra przed ponowną próbą wykonania poprzedniej operacji zakończonej niepowodzeniem:

1. Dopóki klaster nie będzie w stanie `failed`, operacje `upgrade` i `scale` nie powiedzie się. Typowe problemy główne i rozwiązania obejmują:
    * Skalowanie z **niewystarczającym limitem przydziału obliczeń (CRP)** . Aby rozwiązać ten problem, należy najpierw skalować klaster z powrotem do stanu stabilnego celu w ramach limitu przydziału. Następnie wykonaj następujące [kroki, aby zażądać zwiększenia przydziału obliczeń](../azure-supportability/resource-manager-core-quotas-request.md) przed ponowną próbą skalowania w górę poza początkowymi limitami przydziału.
    * Skalowanie klastra przy użyciu zaawansowanej sieci i **niewystarczającej liczby zasobów podsieci (sieci)** . Aby rozwiązać ten problem, należy najpierw skalować klaster z powrotem do stanu stabilnego celu w ramach limitu przydziału. Następnie wykonaj [następujące kroki, aby zażądać zwiększenia przydziału zasobów](../azure-resource-manager/resource-manager-quota-errors.md#solution) przed ponowną próbą skalowania w górę poza początkowymi limitami przydziału.
2. Po usunięciu podstawowej przyczyny niepowodzenia uaktualnienia klaster powinien działać w stanie sukces. Po zweryfikowaniu stanu, ponów próbę wykonania oryginalnej operacji.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Otrzymuję błędy podczas próby uaktualnienia lub skalowania stanu, w którym mój klaster jest aktualnie uaktualniany lub nie przeprowadzono uaktualnienia

*Ta pomoc w rozwiązywaniu problemów jest skierowana z https://aka.ms/aks-pending-upgrade*

Operacje uaktualniania i skalowania w klastrze z pojedynczą pulą węzłów lub klastra z [wieloma pulami węzłów](use-multiple-node-pools.md) wzajemnie się wykluczają. Klaster ani Pula węzłów nie mogą być jednocześnie uaktualniane i skalowane. W zamian każdy typ operacji musi zakończyć się w odniesieniu do zasobu docelowego przed następnym żądaniem tego samego zasobu. W związku z tym operacje są ograniczone w przypadku wystąpienia lub próby aktywnego uaktualnienia lub operacji skalowania, a następnie niepowodzenie. 

Aby ułatwić zdiagnozowanie problemu z uruchomieniem `az aks show -g myResourceGroup -n myAKSCluster -o table` w celu pobrania szczegółowego stanu w klastrze. W oparciu o wynik:

* Jeśli klaster jest aktywnie uaktualniany, poczekaj na zakończenie operacji. Jeśli zakończyło się pomyślnie, ponów próbę wykonania poprzedniej operacji zakończonej niepowodzeniem.
* Jeśli uaktualnienie nie powiodło się, wykonaj kroki opisane w poprzedniej sekcji.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Czy mogę przenieść klaster na inną subskrypcję lub moją subskrypcję z moim klastrem do nowej dzierżawy?

Jeśli klaster AKS został przeniesiony do innej subskrypcji lub klastra będącego właścicielem subskrypcji do nowej dzierżawy, klaster utraci funkcjonalność ze względu na utratę przypisań ról i uprawnień podmiotów usługi. Usługa **AKS nie obsługuje przesuwania klastrów między subskrypcjami ani dzierżawcami** z powodu tego ograniczenia.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Otrzymuję błędy podczas próby użycia funkcji, które wymagają zestawów skalowania maszyn wirtualnych

*Ta pomoc w rozwiązywaniu problemów jest skierowana z aka.ms/aks-vmss-enablement*

Mogą pojawić się błędy wskazujące, że klaster AKS nie znajduje się w zestawie skalowania maszyn wirtualnych, na przykład w poniższym przykładzie:

**Nieznanej obiektu agentpool "nieznanej obiektu agentpool" ustawił automatyczne skalowanie jako włączone, ale nie znajduje się na Virtual Machine Scale Sets**

Aby korzystać z funkcji, takich jak automatyczne skalowanie klastra lub pule wielu węzłów, należy utworzyć klastry AKS korzystające z zestawów skalowania maszyn wirtualnych. Błędy są zwracane, jeśli spróbujesz użyć funkcji, które są zależne od zestawów skalowania maszyn wirtualnych, oraz dla klastra AKS z regularnym zestawem skalowania maszyn wirtualnych.

Postępuj zgodnie z instrukcjami *przed rozpoczęciem* w odpowiednim dokumencie, aby poprawnie utworzyć klaster AKS:

* [Korzystanie z automatycznego skalowania klastra](cluster-autoscaler.md)
* [Tworzenie i używanie pul wielu węzłów](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Jakie ograniczenia nazewnictwa są wymuszane dla zasobów AKS i parametrów?

*Ta pomoc w rozwiązywaniu problemów jest skierowana z aka.ms/aks-naming-rules*

Ograniczenia nazewnictwa są implementowane przez platformę Azure i AKS. Jeśli nazwa zasobu lub parametr przerywa jedno z tych ograniczeń, zwracany jest błąd, który prosi o podanie innych danych wejściowych. Stosuje się następujące typowe wskazówki dotyczące nazewnictwa:

* Nazwa grupy zasobów AKS *MC_* łączy nazwę grupy zasobów i nazwę zasobu. Automatycznie wygenerowana składnia `MC_resourceGroupName_resourceName_AzureRegion` nie może być większa niż 80 znaków. W razie konieczności Zmniejsz długość nazwy grupy zasobów lub nazwę klastra AKS.
* *DnsPrefix* musi rozpoczynać się i kończyć wartościami alfanumerycznymi. Prawidłowe znaki to wartości alfanumeryczne i łączniki (-). *DnsPrefix* nie może zawierać znaków specjalnych, takich jak kropka (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Otrzymuję błędy podczas próby utworzenia, zaktualizowania, skalowania, usunięcia lub uaktualnienia klastra, ta operacja nie jest dozwolona, ponieważ inna operacja jest w toku.

*Ta pomoc w rozwiązywaniu problemów jest skierowana z aka.ms/aks-pending-operation*

Operacje klastra są ograniczone, gdy Poprzednia operacja jest nadal w toku. Aby pobrać szczegółowy stan klastra, użyj polecenia `az aks show -g myResourceGroup -n myAKSCluster -o table`. W razie konieczności Użyj własnej grupy zasobów i nazwy klastra AKS.

Na podstawie danych wyjściowych stanu klastra:

* Jeśli klaster jest w stanie aprowizacji *innym niż* *powodzenie lub nieudany*, poczekaj na zakończenie operacji (*uaktualnianie/aktualizowanie/tworzenie/skalowanie/usuwanie/Migrowanie*). Po zakończeniu poprzedniej operacji ponów próbę wykonania ostatniej operacji klastra.

* Jeśli uaktualnienie nie powiodło się, wykonaj kroki opisane w temacie jak pojawiają się [błędy, w których klaster jest w stanie niepowodzenia, a uaktualnienie lub skalowanie nie będzie działało do momentu jego naprawienia](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>Otrzymuję błędy, których nazwa główna usługi nie została znaleziona podczas próby utworzenia nowego klastra bez przechodzenia w istniejącym klastrze.

Podczas tworzenia klastra AKS wymaga jednostki usługi do tworzenia zasobów w Twoim imieniu. Usługa AKS oferuje możliwość utworzenia nowego elementu podczas tworzenia klastra, ale wymaga Azure Active Directory w pełni propagowanie nowej jednostki usługi w odpowiednim czasie w celu pomyślnego utworzenia klastra. Gdy Propagacja trwa zbyt długo, klaster nie będzie mógł wykonać walidacji, ponieważ nie może znaleźć dostępnej jednostki usługi. 

Wykonaj następujące obejścia tego problemu:
1. Użyj istniejącej jednostki usługi, która została już rozpropagowana w regionach i istnieje do przekazania do AKS podczas tworzenia klastra.
2. W przypadku korzystania ze skryptów automatyzacji należy dodać opóźnienia czasu między utworzeniem jednostki usługi i tworzeniem klastra AKS.
3. Jeśli używasz Azure Portal, Wróć do ustawień klastra podczas tworzenia i ponów próbę wykonania strony walidacji po kilku minutach.

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>Otrzymuję błędy po ograniczeniu ruchu wychodzącego

W przypadku ograniczania ruchu wychodzącego z klastra AKS są [wymagane i opcjonalne zalecane](limit-egress-traffic.md) porty wyjściowe/reguły sieci oraz nazwy FQDN/aplikacji dla AKS. Jeśli ustawienia są w konflikcie z dowolną z tych reguł, uruchomienie pewnych poleceń `kubectl` może nie być możliwe. Podczas tworzenia klastra AKS mogą pojawić się także błędy.

Sprawdź, czy ustawienia nie powodują konfliktu z żadnym z wymaganych lub opcjonalnymi zalecanymi portami wychodzącymi/regułami sieciowymi oraz nazwą FQDN/reguł aplikacji.
