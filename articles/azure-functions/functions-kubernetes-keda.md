---
title: Azure Functions w Kubernetes z KEDA
description: Dowiedz się, jak uruchamiać Azure Functions w Kubernetes w chmurze lub lokalnie przy użyciu funkcji automatycznego skalowania opartego na zdarzeniach KEDA, Kubernetes.
author: jeffhollan
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 9978bd567b1b07e8dd0e22e1f02834626281a5dd
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/13/2020
ms.locfileid: "75920678"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Azure Functions w Kubernetes z KEDA

Środowisko uruchomieniowe Azure Functions zapewnia elastyczność hostingu, gdzie i w jaki sposób.  Pary [KEDA](https://keda.sh) (Skalowanie automatyczne oparte na zdarzeniach Kubernetes) bezproblemowo z Azure Functions środowiska uruchomieniowego i narzędzi, aby zapewnić skalowanie oparte na zdarzeniach w Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Jak działają funkcje oparte na Kubernetes

Usługa Azure Functions składa się z dwóch głównych składników: środowiska uruchomieniowego i kontrolera skali.  Środowisko uruchomieniowe funkcji działa i wykonuje swój kod.  Środowisko uruchomieniowe zawiera logikę na temat wyzwalania, rejestrowania i zarządzania wykonaniami funkcji.  Środowisko uruchomieniowe Azure Functions można uruchamiać w *dowolnym miejscu*.  Drugi składnik jest kontrolerem skalowania.  Kontroler skalowania monitoruje częstotliwość zdarzeń, które są ukierunkowane na funkcję, i aktywnie skaluje liczbę wystąpień, w których uruchomiono aplikację.  Aby dowiedzieć się więcej, zobacz [Azure Functions skalowanie i hosting](functions-scale.md).

Funkcje oparte na Kubernetes udostępniają środowisko uruchomieniowe funkcji w [kontenerze platformy Docker](functions-create-function-linux-custom-image.md) ze skalowaniem opartym na zdarzeniach za pośrednictwem KEDA.  KEDA może skalować w dół do 0 wystąpień (gdy nie są wykonywane żadne zdarzenia) i do *n* wystąpień. Robi to poprzez udostępnienie niestandardowych metryk dla skalowania automatycznego (Kubernetes).  Używanie kontenerów funkcji z KEDA umożliwia replikowanie funkcji bezserwerowych w dowolnym klastrze Kubernetes.  Te funkcje można również wdrożyć przy użyciu funkcji [węzłów wirtualnych usługi Azure Kubernetes Services (AKS)](../aks/virtual-nodes-cli.md) dla infrastruktury bezserwerowej.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Zarządzanie KEDA i funkcjami w Kubernetes

Aby uruchomić funkcje w klastrze Kubernetes, należy zainstalować składnik KEDA. Ten składnik można zainstalować przy użyciu [Azure Functions Core Tools](functions-run-local.md).

### <a name="installing-with-the-azure-functions-core-tools"></a>Instalowanie przy użyciu Azure Functions Core Tools

Domyślnie podstawowe narzędzia instalują zarówno składniki KEDA, jak i Osiris, które obsługują odpowiednio skalowanie oparte na zdarzeniach i HTTP.  Instalacja używa `kubectl` uruchomione w bieżącym kontekście.

Zainstaluj KEDA w klastrze, uruchamiając następujące polecenie instalacji:

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>Wdrażanie aplikacji funkcji do Kubernetes

Każdą aplikację funkcji można wdrożyć w klastrze Kubernetes z systemem KEDA.  Ponieważ funkcje działają w kontenerze platformy Docker, projekt wymaga `Dockerfile`.  Jeśli jeszcze nie istnieje, możesz dodać pliku dockerfile, uruchamiając następujące polecenie w katalogu głównym projektu funkcji:

```cli
func init --docker-only
```

Aby skompilować obraz i wdrożyć funkcje w usłudze Kubernetes, uruchom następujące polecenie:

> [!NOTE]
> Podstawowe narzędzia będą korzystać z interfejsu wiersza polecenia platformy Docker do kompilowania i publikowania obrazu. Upewnij się, że Aparat Docker został już zainstalowany i połączony z kontem przy użyciu `docker login`.

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Zastąp `<name-of-function-deployment>` nazwą aplikacji funkcji.

Spowoduje to utworzenie zasobu Kubernetes `Deployment`, zasobu `ScaledObject` i `Secrets`, który zawiera zmienne środowiskowe zaimportowane z pliku `local.settings.json`.

### <a name="deploying-a-function-app-from-a-private-registry"></a>Wdrażanie aplikacji funkcji z rejestru prywatnego

Powyższy przepływ działa również w przypadku rejestrów prywatnych.  Jeśli pobierasz obraz kontenera z rejestru prywatnego, Dołącz flagę `--pull-secret`, która odwołuje się do wpisu tajnego Kubernetes z poświadczeniami rejestru prywatnego podczas uruchamiania `func kubernetes deploy`.

## <a name="removing-a-function-app-from-kubernetes"></a>Usuwanie aplikacji funkcji z Kubernetes

Po wdrożeniu można usunąć funkcję, usuwając skojarzone `Deployment`, `ScaledObject``Secrets` utworzone.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Odinstalowywanie KEDA z Kubernetes

Aby usunąć KEDA z klastra Kubernetes, można uruchomić następujące podstawowe narzędzia:

```cli
func kubernetes remove --namespace keda
```

## <a name="supported-triggers-in-keda"></a>Obsługiwane Wyzwalacze w KEDA

KEDA obsługuje następujące wyzwalacze usługi Azure Functions:

* [Kolejki usługi Azure Storage](functions-bindings-storage-queue.md)
* [Kolejki Azure Service Bus](functions-bindings-service-bus.md)
* [Centra zdarzeń/IoT platformy Azure](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [Kolejka RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>Obsługa wyzwalacza HTTP

Można użyć Azure Functions, które uwidaczniają wyzwalacze HTTP, ale KEDA nie zarządzać nimi bezpośrednio.  Azure Functions Core Tools zainstaluje powiązany projekt, Osiris, który umożliwia skalowanie punktów końcowych HTTP z przedziału od 0 do 1.  Skalowanie od 1 do *n* będzie oparte na tradycyjnych zasadach skalowania Kubernetes.

## <a name="next-steps"></a>Następne kroki
Więcej informacji zawierają następujące zasoby:

* [Tworzenie funkcji przy użyciu obrazu niestandardowego](functions-create-function-linux-custom-image.md)
* [Kodowanie i testowanie usługi Azure Functions lokalnie](functions-develop-local.md)
* [Jak działa plan zużycia funkcji platformy Azure](functions-scale.md)