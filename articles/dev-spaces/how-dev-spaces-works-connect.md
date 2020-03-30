---
title: Jak działa podłączanie komputera deweloperskiego do klastra AKS
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: W tym artykule opisano procesy związane z używaniem usługi Azure Dev Spaces do łączenia komputera dewelopera z klastrem usługi Azure Kubernetes
keywords: Miejsca deweloperów platformy Azure, przestrzenie deweloperów, platforma do dokowania, sieci Kubernetes, platforma Azure, usługa AKS, usługa Azure Kubernetes, kontenery
ms.openlocfilehash: a74a5a623006ccd64441023c2c4bc9ad3dcb517e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241714"
---
# <a name="how-connecting-your-development-computer-to-your-aks-cluster-works"></a>Jak działa podłączanie komputera deweloperskiego do klastra AKS

Za pomocą usługi Azure Dev Spaces można podłączyć komputer dewelopera do klastra AKS, umożliwiając uruchamianie i debugowanie kodu na komputerze deweloperskim, tak jakby był uruchomiony w klastrze. Usługa Azure Dev Spaces przekierowuje ruch między połączonym klastrem AKS, uruchamiając zasobnik w klastrze, który działa jako agent zdalny, aby przekierować ruch między komputerem deweloperskim a klastrem. To przekierowanie ruchu umożliwia kod na komputerze deweloperskim i usługach uruchomionych w klastrze AKS do komunikowania się tak, jakby były w tym samym klastrze AKS. To połączenie umożliwia również uruchamianie i debugowanie kodu z kontenerem lub bez niego na komputerze deweloperskim. Podłączenie komputera dewelopera do klastra pomaga szybko tworzyć aplikację i przeprowadzać kompleksowe testy.

## <a name="connecting-to-your-cluster"></a>Łączenie się z klastrem

Połączenie z istniejącym klastrem AKS przy użyciu [programu Visual Studio Code][vs-code] z rozszerzeniem Azure Dev [Spaces][azds-vs-code] zainstalowanym w systemie MacOS lub Windows 10. Podczas ustanawiania połączenia, masz możliwość przekierowania całego ruchu do iz nowego lub istniejącego zasobnika w klastrze do komputera deweloperskiego.

> [!NOTE]
> Podczas korzystania z programu Visual Studio Code do łączenia się z klastrem rozszerzenie Usługi Azure Dev Spaces umożliwia przekierowanie usługi do komputera dewelopera. Ta opcja jest wygodnym sposobem identyfikowania zasobnika do przekierowania. Wszystkie przekierowania między klastrem AKS a komputerem deweloperskim jest dla zasobnika.

Łączenie się z klastrem nie wymaga włączenia usługi Azure Dev Spaces w klastrze. Zamiast tego, gdy rozszerzenie usługi Azure Dev Spaces ustanawia połączenie z klastrem, to:

* Zastępuje kontener w zasobniku w klastrze AKS kontenerem agenta zdalnego, który przekierowuje ruch do komputera deweloperskiego. Podczas przekierowywania nowego zasobnika usługa Azure Dev Spaces tworzy nową zasobnik w klastrze usługi AKS za pomocą agenta zdalnego.
* Uruchamia [port kubectl do przodu][kubectl-port-forward] na komputerze deweloperskim, aby przekazywać ruch z komputera dewelopera do agenta zdalnego działającego w klastrze.
* Zbiera informacje o środowisku z klastra przy użyciu agenta zdalnego. Te informacje o środowisku obejmują zmienne środowiskowe, widoczne usługi, instalacje woluminów i instalacje tajne.
* Konfiguruje środowisko w terminalu kodu programu Visual Studio, dzięki czemu usługa na komputerze deweloperskim może uzyskać dostęp do tych samych zmiennych, jak gdyby była uruchomiona w klastrze.  
* Aktualizuje plik hostów do mapowania usług w klastrze AKS do lokalnych adresów IP na komputerze deweloperskim. Te wpisy plików hosts umożliwiają kod uruchomiony na komputerze deweloperskim do żądania do innych usług uruchomionych w klastrze. Aby zaktualizować plik hosts, usługa Azure Dev Spaces poprosi o dostęp administratora na komputerze deweloperskim podczas łączenia się z klastrem.

Jeśli w klastrze włączono usługi Azure Dev Spaces, możesz również użyć [przekierowania ruchu oferowanego przez usługę Azure Dev Spaces.][how-it-works-routing] Przekierowanie ruchu oferowane przez usługi Azure Dev Spaces umożliwia łączenie się z kopią usługi uruchomionej w przestrzeni deweloperów podrzędnych. Korzystanie z przestrzeni deweloperów podrzędnych pomaga uniknąć zakłócania innych pracujących w nadrzędnym miejscu deweloperskim, ponieważ tylko przekierowanie ruchu kierowanego na wystąpienie usługi przestrzeni podrzędnej, pozostawiając wystąpienie obszaru nadrzędnego usługi bez modyfikacji.

Po nawiązaniu połączenia z klastrem ruch jest kierowany do komputera dewelopera niezależnie od tego, czy usługa jest uruchomiona na komputerze deweloperskim.

## <a name="running-code-on-your-development-computer"></a>Uruchamianie kodu na komputerze deweloperskim

Po nawiązaniu połączenia z klastrem AKS można uruchomić dowolny kod natywnie na komputerze, bez konteneryzacji. Każdy ruch sieciowy odbierany przez agenta zdalnego jest przekierowywał do portu lokalnego określonego podczas połączenia, dzięki czemu natywnie uruchomiony kod może akceptować i przetwarzać ten ruch. Zmienne środowiskowe, woluminy i wpisy tajne z klastra są udostępniane do kodu uruchomionego na komputerze deweloperskim. Ponadto ze względu na wpisy plików hosts i przekazywanie portów dodane do komputera dewelopera przez usługę Azure Dev Spaces, kod może wysyłać ruch sieciowy do usług uruchomionych w klastrze przy użyciu nazw usług z klastra, a ruch jest przesyłany dalej do usług, które są uruchomione w klastrze.

Ponieważ kod jest uruchomiony na komputerze deweloperskim, masz elastyczność, aby użyć dowolnego narzędzia, które zwykle używasz do tworzenia do uruchamiania kodu i debugowania go. Ruch jest kierowany między komputerem deweloperskim a klastrem przez cały czas nawiązywanie połączenia. To trwałe połączenie umożliwia uruchamianie, zatrzymywania i ponownego uruchamiania kodu tak bardzo, jak trzeba bez konieczności ponownego ustanawiania połączenia.

Ponadto usługa Azure Dev Spaces umożliwia replikowanie zmiennych środowiskowych i plików zainstalowanych dostępnych dla zasobników w klastrze AKS na komputerze deweloperskim za pośrednictwem pliku *azds-local.env.* Za pomocą tego pliku można również tworzyć nowe zmienne środowiskowe i instalacje woluminów.

## <a name="additional-configuration-with-azds-localenv"></a>Dodatkowa konfiguracja z azds-local.env

Plik *azds-local.env* umożliwia replikowanie zmiennych środowiskowych i plików zainstalowanych dostępnych dla zasobników w klastrze AKS. W pliku *azds-local.env* można określić następujące akcje:

* Pobierz wolumin i ustaw ścieżkę do tego woluminu jako zmienną środowiskową.
* Pobierz pojedynczy plik lub zestaw plików z woluminu i zamontuj go na komputerze deweloperskim.
* Udostępnij usługę niezależnie od klastra, z którego masz połączenie.

Oto przykład *pliku azds-local.env:*

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}

# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

Domyślny plik *azds-local.env* nie jest tworzony automatycznie, więc należy ręcznie utworzyć plik w katalogu głównym projektu.

## <a name="diagnostics-and-logging"></a>Diagnostyka i rejestrowanie

Po podłączeniu do klastra AKS dzienniki diagnostyczne z klastra są rejestrowane w [katalogu tymczasowym][azds-tmp-dir]komputera deweloperskiego. Za pomocą programu Visual Studio Code można również użyć polecenia *Pokaż informacje diagnostyczne* do drukowania bieżących zmiennych środowiskowych i wpisów DNS z klastra AKS.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć podłączanie lokalnego komputera deweloperskiego do klastra AKS, zobacz [Łączenie komputera deweloperskiego z klastrem AKS][connect].

[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[connect]: how-to/connect.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[vs-code]: https://code.visualstudio.com/download
