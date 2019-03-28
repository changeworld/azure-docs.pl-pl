---
title: Rozwiązywanie typowych problemów w usłudze Azure Kubernetes Service
description: Dowiedz się, jak do rozwiązywania oraz usuwania typowych problemów w przypadku korzystania z usługi Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 56d91d7801c576064b941ac6089a52e74b4a3b7b
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540928"
---
# <a name="aks-troubleshooting"></a>Rozwiązywanie problemów z usługi AKS

Podczas tworzenia lub zarządzanie klastrami usługi Azure Kubernetes Service (AKS) może czasami wystąpić problemy. Ten artykuł szczegółowo opisuje niektóre typowe problemy i kroki rozwiązywania problemów.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Ogólnie rzecz biorąc, gdzie można znaleźć informacje o debugowaniu problemów Kubernetes?

Spróbuj [oficjalny przewodnik rozwiązywania problemów z klastrów Kubernetes](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Istnieje również [przewodnik rozwiązywania problemów z](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)opublikowanymi przez inżynier z firmy Microsoft do rozwiązywania problemów zasobników, węzły, klastrów i innych funkcji.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Otrzymuję błąd "Przekroczono limit przydziału" podczas tworzenia lub uaktualniania. Co mam zrobić? 

Musisz [żądania rdzeni](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Co to jest ustawienie maksymalne zasobników na węzeł dla usługi AKS?

Ustawienie maksymalne zasobników każdego węzła jest domyślnie 30, jeśli musisz wdrożyć klaster usługi AKS w witrynie Azure portal.
Ustawienie maksymalne zasobników każdego węzła jest 110 domyślnie, jeśli musisz wdrożyć klaster usługi AKS w interfejsie wiersza polecenia platformy Azure. (Upewnij się, że używasz najnowszej wersji interfejsu wiersza polecenia platformy Azure). To ustawienie domyślne można zmienić za pomocą `–-max-pods` znacznik w `az aks create` polecenia.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Otrzymuję błąd insufficientSubnetSize podczas wdrażania klastra usługi AKS z zaawansowany siecią. Co mam zrobić?

Użycie wtyczki Azure CNI (zaawansowany siecią) AKS preallocates adresów IP oparty na "max zasobników" na węzeł skonfigurowane. Liczba węzłów w klastrze AKS może być dowolnym między 1 a 110. Na podstawie skonfigurowanego zasobników max na węzeł, rozmiar podsieci powinien być większy niż "product liczbę węzłów i maksymalna pod każdym węźle". Następujące podstawowe równanie przedstawia to:

Rozmiar podsieci > Liczba węzłów w klastrze (biorąc pod uwagę przyszłych wymagań skalowania) * max zasobniki w każdym węźle.

Aby uzyskać więcej informacji, zobacz [adresowania IP planowanie na potrzeby klastra](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Moje zasobnika została zablokowana w trybie CrashLoopBackOff. Co mam zrobić?

Może to być różne przyczyny zasobnika ustawiany jest zablokowana w trybie. Użytkownik może wyglądać na:

* Zasobnik, za pomocą `kubectl describe pod <pod-name>`.
* Dzienniki, korzystając z `kubectl log <pod-name>`.

Aby uzyskać więcej informacji na temat rozwiązywania problemów pod zobacz [debugowania aplikacji](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Próbuję włączyć RBAC w istniejącym klastrze. Jak można zrobić?

Niestety włączania kontroli dostępu opartej na rolach (RBAC) na istniejących klastrów nie jest obsługiwana w tej chwili. Należy jawnie utworzyć nowych klastrów. Jeśli używasz interfejsu wiersza polecenia, RBAC jest domyślnie włączona. Jeśli używasz portalu usługi AKS, przycisk przełączania, aby umożliwić RBAC jest dostępna w przepływ pracy tworzenia.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Po utworzeniu klastra przy użyciu RBAC włączana przy użyciu dowolnego wiersza polecenia platformy Azure przy użyciu wartości domyślnych lub witryny Azure portal, a teraz zobaczyć wiele ostrzeżenia na pulpicie nawigacyjnym platformy Kubernetes. Pulpit nawigacyjny pozwala działać bez ostrzeżenia. Co mam zrobić?

Przyczyna ostrzeżenia na pulpicie nawigacyjnym jest klastra jest teraz włączone wraz z RBAC i do niego dostęp ma domyślnie wyłączone. Ogólnie rzecz biorąc to podejście jest dobrym rozwiązaniem, ponieważ narażenia domyślnego pulpitu nawigacyjnego dla wszystkich użytkowników klastra może prowadzić do zagrożenia bezpieczeństwa. Jeśli nadal chcesz włączyć Pulpit nawigacyjny, wykonaj kroki opisane w [ten wpis w blogu](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Nie można nawiązać pulpitu nawigacyjnego. Co mam zrobić?

Najprostszym sposobem uzyskania dostępu do usługi spoza klastra jest uruchomienie `kubectl proxy`, które serwery proxy żądania wysyłane do localhost port 8001 do serwera interfejsu API rozwiązania Kubernetes. W efekcie serwera interfejsu API można serwera proxy do usługi: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Jeśli nie widzisz pulpit nawigacyjny platformy Kubernetes, sprawdź, czy `kube-proxy` zasobnik jest uruchomiony w `kube-system` przestrzeni nazw. Jeśli nie jest w stanie uruchomienia, usunąć zasobnik i zostanie uruchomiona ponownie.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Nie mogę uzyskać dzienniki przy użyciu narzędzia kubectl dzienników lub nie można nawiązać połączenia serwera interfejsu API. Otrzymuję "błąd z serwera: błąd podczas wybierania zaplecza: wybierania tcp...". Co mam zrobić?

Upewnij się, że nie jest modyfikowana domyślną sieciową grupę zabezpieczeń i że port 22 jest otwarty dla połączenia do serwera interfejsu API. Sprawdź, czy `tunnelfront` zasobnik jest uruchomiony w *systemu kubernetes* za pomocą nazw `kubectl get pods --namespace kube-system` polecenia. Jeśli nie, Wymuś usunięcie zasobnik i zostanie uruchomiony ponownie.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>I próbuję uaktualnienia lub skalowania i są zwracane "komunikat: Nie można zmienić właściwości "imageReference" "Wystąpił błąd. Jak rozwiązać ten problem?

Użytkownik może się pojawiać ten błąd ponieważ znaczniki węzły agenta w ramach klastra usługi AKS został zmodyfikowany. Modyfikowanie i usuwanie tagów i innych właściwości zasobów w grupie zasobów MC_ * może prowadzić do nieoczekiwanych wyników. Modyfikowanie zasobów w grupie MC_ * w usłudze AKS klastra przerywa cel poziomu usług (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Otrzymuję błędy, które Mój klaster jest w stanie niepowodzenia i uaktualnianie i skalowanie nie będzie działać do czasu jego naprawienia

*Tej pomocy dotyczące rozwiązywania problemów jest przekierowywany z https://aka.ms/aks-cluster-failed*

Ten błąd występuje, gdy klastry przejść w stan nie powiodło się kilka przyczyn. Wykonaj poniższe kroki, aby rozwiązać Nazwa stanu klastra nie powiodło się przed ponowieniem próby wykonania operacji wcześniej zakończonej niepowodzeniem:

1. Dopóki nie jest klastrem `failed` stanu, `upgrade` i `scale` operacji nie powiodło się. Typowe problemy głównego i rozwiązania obejmują:
    * Skalowanie za pomocą **przydział usługi compute niewystarczające (CRP)**. Aby rozwiązać problem, należy najpierw skalowania klastra do stanu stabilne cel w ramach limitu przydziału. Następnie postępuj zgodnie z tymi [zwiększyć kroki, aby żądać przydziału obliczeniowych](../azure-supportability/resource-manager-core-quotas-request.md) przed przystąpieniem do skalowania w górę ponownie limitów przydziału początkowej poza nim.
    * Skalowanie klastra za pomocą zaawansowanych sieci i **zasoby podsieci niewystarczające (sieć)**. Aby rozwiązać problem, należy najpierw skalowania klastra do stanu stabilne cel w ramach limitu przydziału. Następnie postępuj zgodnie z [zwiększyć te kroki, aby żądać przydziału zasobów](../azure-resource-manager/resource-manager-quota-errors.md#solution) przed przystąpieniem do skalowania w górę ponownie limitów przydziału początkowej poza nim.
2. Po usunięciu podstawowych przyczyn niepowodzenia uaktualniania klastra powinna być w stanie sukces. Po zweryfikowaniu stanie sukces, spróbuj ponownie wykonać operację.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Otrzymuję błędy podczas próby uaktualnienia lub skali, do stanu klastra jest aktualnie jest uaktualniony lub uaktualnienie nie powiodło się

*Tej pomocy dotyczące rozwiązywania problemów jest przekierowywany z https://aka.ms/aks-pending-upgrade*

Operacje klastra są ograniczone, podczas aktywnego uaktualniania operacje są wykonywane lub podjęto próbę uaktualnienia, ale następnie nie powiodło się. Aby zdiagnozować problem, uruchom `az aks show -g myResourceGroup -n myAKSCluster -o table` można pobrać szczegółowe informacje o tym w klastrze. Na podstawie wyniku:

* Jeśli klaster jest aktywnie uaktualnienie, poczekaj, aż do zakończenia operacji. Jeśli zakończyło się pomyślnie, spróbuj ponownie operacji wcześniej zakończonej niepowodzeniem.
* W przypadku niepowodzenia uaktualniania klastra, wykonaj kroki opisane powyżej

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Czy mogę przenieść mój klaster na inną subskrypcję lub subskrypcję z klastra do nowego dzierżawcy

Klaster AKS zostały przeniesione do innej subskrypcji lub klastra będącego właścicielem subskrypcji do nowego dzierżawcy, klastra spowoduje utratę funkcji z powodu utraty przypisań ról i uprawnień podmiotów zabezpieczeń usługi. **AKS nie obsługuje przenoszenia klastrów w subskrypcji i dzierżaw** ze względu na to ograniczenie.
