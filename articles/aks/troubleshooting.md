---
title: Rozwiązywanie typowych problemów w usłudze Azure Kubernetes Service
description: Dowiedz się, jak do rozwiązywania oraz usuwania typowych problemów w przypadku korzystania z usługi Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: c20f2cc03565ce861dfc6317be8459fdafeef0bf
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384109"
---
# <a name="aks-troubleshooting"></a>Rozwiązywanie problemów z usługi AKS
Podczas tworzenia lub Menedżer usłudze AKS klastrów, czasami mogą wystąpić problemy. Ten artykuł szczegółowo opisuje niektóre typowe problemy i kroki rozwiązywania problemów.

### <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-issues"></a>Ogólnie rzecz biorąc, gdzie można znaleźć informacje o debugowaniu problemów Kubernetes?

[W tym miejscu](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/) to oficjalne link do rozwiązywania problemów z klastrów kubernetes.
[W tym miejscu](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md) łącze do przewodnik rozwiązywania problemów, opublikowane przez inżynier z firmy Microsoft dotyczące rozwiązywania problemów z zasobników, węzły, klastrów, itp.

### <a name="i-am-getting-a-quota-exceeded-error-during-create-or-upgrade-what-should-i-do"></a>Otrzymuję błąd przekroczenia limitu przydziału podczas tworzenia lub uaktualniania. Co mam zrobić? 

Konieczne będzie żądanie rdzeni [tutaj](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

### <a name="what-is-the-max-pods-per-node-setting-for-aks"></a>Co to jest maksymalny zasobników na ustawienie node dla usługi AKS?

Maksymalna zasobników w każdym węźle są domyślnie 30 w przypadku wdrożenia klastra usługi AKS w witrynie Azure portal.
Maksymalna zasobników w każdym węźle są domyślnie 110 Jeśli musisz wdrożyć klaster usługi AKS w interfejsie wiersza polecenia platformy Azure. (Upewnij się, że używasz najnowszej wersji interfejsu wiersza polecenia platformy Azure). To ustawienie domyślne można zmienić za pomocą maksymalna — węzły-na-flagi zasobnik w usłudze az aks create polecenie.

### <a name="i-am-getting-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Otrzymuję błąd "insufficientSubnetSize" podczas wdrażania klastra usługi AKS z zaawansowany siecią. Co mam zrobić?

W opcji Niestandardowa sieć wirtualna wybrana dla sieci podczas AKS tworzy wtyczki Azure CNI jest używany dla usługi IPAM. Liczba węzłów w klastrze AKS może być dowolnym miejscu od 1 do 100. Na podstawie 2) powyżej podsieci rozmiar powinien być większy niż iloczyn liczby węzłów i maksymalna zasobnika na rozmiar podsieci węzła > Liczba węzłów w klastrze * maksymalna liczba zasobników w każdym węźle.

### <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Moje zasobnika została zablokowana w trybie "CrashLoopBackOff". Co mam zrobić?

Może to być różne przyczyny zasobnika ustawiany jest zablokowana w trybie. Możesz chcieć zbadać 
* Zasobnik, sama przy użyciu `kubectl describe pod <pod-name>`
* Dzienniki przy użyciu  `kubectl log <pod-name>`

### <a name="i-am-trying-to-enable-rbac-on-an-existing-cluster-can-you-tell-me-how-i-can-do-that"></a>Próbuję włączyć RBAC w istniejącym klastrze. Czy możesz podać jak mogę to zrobić?

Niestety włączenie funkcji RBAC w istniejących klastrach nie jest obsługiwane w tej chwili. Należy jawnie tworzyć nowych klastrów. Jeśli używasz interfejsu wiersza polecenia, RBAC jest domyślnie włączona, aby ją włączyć przycisk przełącznika jest dostępne w portalu usługi AKS Tworzenie przepływu pracy.

### <a name="i-created-a-cluster-using-the-azure-cli-with-defaults-or-the-azure-portal-with-rbac-enabled-and-numerous-warnings-in-the-kubernetes-dashboard-the-dashboard-used-to-work-before-without-any-warnings-what-should-i-do"></a>Utworzono klastra przy użyciu wiersza polecenia platformy Azure przy użyciu wartości domyślnych lub witryny Azure portal przy użyciu RBAC włączone i wiele ostrzeżeń w pulpit nawigacyjny platformy kubernetes. Pulpit nawigacyjny używany do pracy przed bez żadnych ostrzeżeń. Co mam zrobić?

Przyczyna wyświetlane są ostrzeżenia na pulpicie nawigacyjnym jest teraz jest włączone za pomocą RBAC'ed i do niego dostęp ma domyślnie wyłączone. Ogólnie rzecz biorąc to podejście jest uważany za dobrą praktyką, ponieważ narażenia domyślnego pulpitu nawigacyjnego dla wszystkich użytkowników klastra może prowadzić do zagrożenia bezpieczeństwa. Jeśli nadal chcesz włączyć Pulpit nawigacyjny, postępuj zgodnie z tym [blogu](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/) ją włączyć.

### <a name="i-cant-seem-to-connect-to-the-dashboard-what-should-i-do"></a>Nie mogę nawiązać połączenia z pulpitu nawigacyjnego. Co mam zrobić?

Uzyskiwanie dostępu do usługi poza klastrem najłatwiej przeprowadzić kubectl proxy, który będzie żądania serwera proxy z portem localhost 8001 do serwera interfejsu API rozwiązania Kubernetes. Z tego miejsca apiserver można serwera proxy z usługą: http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#! / węzeł? przestrzeń nazw = default

Jeśli nie widzisz pulpit nawigacyjny platformy kubernetes, sprawdź, czy w przestrzeni nazw kubernetes — system działa pod serwera proxy klastra kubernetes w usłudze. Jeśli nie jest w stanie uruchomienia, usunąć zasobnik i zostanie uruchomiona ponownie.

### <a name="i-could-not-get-logs-using-kubectl-logs-or-cannot-connect-to-the-api-server-getting-the-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>I nie można pobrać dzienniki przy użyciu narzędzia Kubectl dzienników lub nie można nawiązać połączenia wprowadzenie interfejsu api serwera "błąd z serwera: błąd podczas wybierania zaplecza: wybierania tcp...". Co mam zrobić?

Upewnij się, że domyślna sieciowa grupa zabezpieczeń nie jest modyfikowany i port 22 jest otwarty dla połączenia z serwerem interfejsu API. Sprawdź, czy tunnelfront pod działa w przestrzeni nazw systemu kubernetes. Jeśli nie jest dostępne, Wymuś usunięcie go i zostanie uruchomiona ponownie.

### <a name="i-am-trying-to-upgrade-or-scale-and-am-getting-message-changing-property-imagereference-is-not-allowed-error--how-do-i-fix-this-issue"></a>I próbuję uaktualnienia lub skalowania i zwracane, "message": "Zmiana właściwości"imageReference"jest niedozwolone." Błąd.  Jak rozwiązać ten problem?

Istnieje możliwość, otrzymujesz ten błąd, ponieważ zmodyfikowano tagów w węzły agenta w ramach klastra usługi AKS. Modyfikowanie i usuwanie tagów i innych właściwości zasobów w grupie zasobów MC_ * może prowadzić do nieoczekiwanych wyników. Modyfikowanie zasobów w ramach MC_ * w klastrze AKS przerywa poziomu usługi.

### <a name="how-do-i-renew-the-service-principal-secret-on-my-aks-cluster"></a>Jak odnowić klucz tajny jednostki usługi na mój klaster AKS?

Domyślnie klastry usługi AKS są tworzone za pomocą usługi jednostki, która ma czas wygaśnięcia jeden rok. Jak prawie rok datę wygaśnięcia można zresetować poświadczenia, które mają rozszerzenie nazwy głównej usługi dla dodatkowy okres czasu.

Poniższy przykład wykonuje następujące czynności:

1. Pobiera identyfikator jednostki usługi przy użyciu klastra [az aks show](/cli/azure/aks#az-aks-show) polecenia.
1. Wyświetla listę usługi klienta nazwy głównej wpisu tajnego za pomocą [az ad sp credential list](/cli/azure/ad/sp/credential#az-ad-sp-credential-list)
1. Jednostka usługi dla innego przy użyciu jednego roku rozszerza [az ad sp reset poświadczeń](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) polecenia. Klucz tajny klienta jednostki usługi musi pozostać taka sama dla klastra AKS, by działała poprawnie.

```azurecli
# Get the service principal ID of your AKS cluster
sp_id=$(az aks show -g myResourceGroup -n myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)

# Get the existing service principal client secret
key_secret=$(az ad sp credential list --id $sp_id --query [].keyId -o tsv)

# Reset the credentials for your AKS service principal and extend for 1 year
az ad sp credential reset \
    --name $sp_id \
    --password $key_secret \
    --years 1
```
