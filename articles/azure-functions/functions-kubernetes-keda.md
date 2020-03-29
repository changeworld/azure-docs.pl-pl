---
title: Usługi Azure w usłudze Kubernetes z keda
description: Dowiedz się, jak uruchamiać usługi Azure Functions w usłudze Kubernetes w chmurze lub lokalnie przy użyciu skalowania automatycznego opartego na narzędziach KEDA, opartych na usłudze Kubernetes.
author: jeffhollan
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 2c06fdba8f60243acf4e0fabd23df8b832c210db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301679"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Usługi Azure w usłudze Kubernetes z keda

Środowisko uruchomieniowe usługi Azure Functions zapewnia elastyczność w hostingu, gdzie i jak chcesz.  [Skalowanie](https://keda.sh) automatyczne oparte na zdarzeniach (Kubernetes) bezproblemowo łączy się ze środowiska uruchomieniowego i narzędziami usługi Azure Functions, aby zapewnić skalowanie sterowane zdarzeniami w usłudze Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Jak działają funkcje oparte na użądliach Kubernetes

Usługa Azure Functions składa się z dwóch kluczowych składników: środowiska uruchomieniowego i kontrolera skalowania.  Środowisko wykonawcze Functions uruchamia i wykonuje kod.  Środowisko wykonawcze zawiera logikę, jak wyzwalać, rejestrować i zarządzać wykonaniami funkcji.  Środowisko uruchomieniowe usługi Azure Functions można uruchomić *w dowolnym miejscu.*  Drugi składnik jest kontrolerem skalowania.  Kontroler skalowania monitoruje szybkość zdarzeń, które są kierowane na funkcję i proaktywnie skaluje liczbę wystąpień uruchomionych aplikacji.  Aby dowiedzieć się więcej, zobacz [Skala i hosting usług Azure Functions](functions-scale.md).

Funkcje oparte na usłudze Kubernetes udostępnia środowisko uruchomieniowe funkcji w [kontenerze platformy Docker](functions-create-function-linux-custom-image.md) ze skalowaniem opartym na zdarzeniach za pośrednictwem funkcji KEDA.  KEDA można skalować w do 0 wystąpień (gdy nie występują żadne zdarzenia) i out do *n* wystąpień. W tym celu, uwidaczniając metryki niestandardowe dla skalowania automatycznego Kubernetes (Skalowanie zasobników poziomych).  Korzystanie z kontenerów funkcji z KEDA umożliwia replikację funkcji bezserwerowych w dowolnym klastrze Kubernetes.  Te funkcje można również wdrożyć przy użyciu [funkcji węzłów wirtualnych usług Azure Kubernetes (AKS)](../aks/virtual-nodes-cli.md) dla infrastruktury bezserwerowej.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Zarządzanie KEDA i funkcje w Kubernetes

Aby uruchomić funkcje w klastrze kubernetes, należy zainstalować składnik KEDA. Ten składnik można zainstalować przy użyciu [narzędzia Azure Functions Core Tools](functions-run-local.md).

### <a name="installing-with-helm"></a>Instalacja za pomocą helma

Istnieją różne sposoby instalowania KEDA w dowolnym klastrze Kubernetes, w tym Helm.  Opcje wdrażania są udokumentowane w [witrynie KEDA](https://keda.sh/deploy/).

## <a name="deploying-a-function-app-to-kubernetes"></a>Wdrażanie aplikacji funkcji w ud.

Dowolną aplikację funkcji można wdrożyć w klastrze Kubernetes z systemem KEDA.  Ponieważ funkcje są uruchamiane w kontenerze `Dockerfile`platformy Docker, projekt wymaga pliku .  Jeśli jeszcze go nie ma, można dodać plik Dockerfile, uruchamiając następujące polecenie w katalogu głównym projektu funkcji:

```cli
func init --docker-only
```

Aby utworzyć obraz i wdrożyć funkcje w umiań Kubernetes, uruchom następujące polecenie:

> [!NOTE]
> Podstawowe narzędzia będą korzystać z interfejsu wiersza polecenia platformy docker do tworzenia i publikowania obrazu. Pamiętaj, aby docker był już zainstalowany `docker login`i połączony z kontem za pomocą pliku .

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Zamień `<name-of-function-deployment>` na nazwę aplikacji funkcyjnej.

Spowoduje to utworzenie `Deployment` zasobu Kubernetes, zasobu `ScaledObject` i `Secrets`, który `local.settings.json` zawiera zmienne środowiskowe zaimportowane z pliku.

### <a name="deploying-a-function-app-from-a-private-registry"></a>Wdrażanie aplikacji funkcji z rejestru prywatnego

Powyższy przepływ działa również dla rejestrów prywatnych.  Jeśli ściągasz obraz kontenera z rejestru `--pull-secret` prywatnego, dołącz flagę, która odwołuje się do klucza tajnego Kubernetes przechowując poświadczenia rejestru prywatnego podczas uruchamiania `func kubernetes deploy`.

## <a name="removing-a-function-app-from-kubernetes"></a>Usuwanie aplikacji funkcji z aplikacji Kubernetes

Po wdrożeniu można usunąć funkcję, `Deployment`usuwając skojarzone , `ScaledObject`, `Secrets` utworzone.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Odinstalowywanie KEDA z Kubernetes

Kroki odinstalowania KEDA są udokumentowane [na stronie KEDA](https://keda.sh/deploy/).

## <a name="supported-triggers-in-keda"></a>Obsługiwane wyzwalacze w KEDA

Narzędzie KEDA obsługuje następujące wyzwalacze funkcji platformy Azure:

* [Kolejki usługi Azure Storage](functions-bindings-storage-queue.md)
* [Kolejki usługi Azure Service Bus](functions-bindings-service-bus.md)
* [Azure Event / IoT Hubs](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [Kolejka RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>Obsługa wyzwalacza HTTP

Można użyć usługi Azure Functions, które ujawniają wyzwalacze HTTP, ale KEDA nie zarządza nimi bezpośrednio.  Możesz wykorzystać wyzwalacz prometeusza KEDA do [skalowania funkcji platformy Azure HTTP od 1 do *n* wystąpień.](https://dev.to/anirudhgarg_99/scale-up-and-down-a-http-triggered-function-app-in-kubernetes-using-keda-4m42)

## <a name="next-steps"></a>Następne kroki
Więcej informacji zawierają następujące zasoby:

* [Tworzenie funkcji przy użyciu obrazu niestandardowego](functions-create-function-linux-custom-image.md)
* [Kodowanie i testowanie usługi Azure Functions lokalnie](functions-develop-local.md)
* [Jak działa plan użycia funkcji platformy Azure](functions-scale.md)