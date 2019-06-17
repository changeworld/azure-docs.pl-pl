---
title: Usługa Azure Functions na platformie Kubernetes przy użyciu KEDA
description: Dowiedz się, jak uruchomić usługi Azure Functions w usłudze Kubernetes w chmurze lub lokalnie przy użyciu KEDA, automatyczne skalowanie na podstawie Kubernetes oparte na zdarzeniach.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: Azure functions, funkcje, przetwarzanie zdarzeń, obliczanie dynamiczne, architektura bezserwerowa, kubernetes
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: jehollan
ms.openlocfilehash: c82ed7aa841f53f5c81f3281ed1b09926e565e75
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65077623"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Usługa Azure Functions na platformie Kubernetes przy użyciu KEDA

Środowisko uruchomieniowe usługi Azure Functions zapewnia elastyczność w hostingu, gdzie i w jaki sposób.  [KEDA](https://github.com/kedacore/kore) (Kubernetes — zdarzeń opartych na automatycznego skalowania na podstawie) bezproblemowo współdziała z środowiska uruchomieniowego usługi Azure Functions i narzędzia w celu zapewnienia skalowania oparte na zdarzeniach w usłudze Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Jak Kubernetes na podstawie pracy funkcji

Usługa Azure Functions składa się z dwóch kluczowych składników: środowiska uruchomieniowego i kontroler skali.  Środowisko uruchomieniowe usługi Functions działa i wykonuje kod.  Środowisko uruchomieniowe zawiera logikę na temat wyzwalacza, logowania i zarządzania wykonań funkcji.  Drugim składnikiem jest kontrolerem skali.  Kontroler skalowania monitoruje częstość występowania zdarzeń, które są przeznaczone dla funkcji i aktywnie skaluje liczbę wystąpień aplikacji.  Aby dowiedzieć się więcej, zobacz [usługi Azure Functions, skalowanie i hosting](functions-scale.md).

Na podstawie Kubernetes funkcji zapewnia środowisko uruchomieniowe usługi Functions w [kontener platformy Docker](functions-create-function-linux-custom-image.md) przy użyciu oparte na zdarzeniach skalowania za pośrednictwem KEDA.  KEDA można skalować w dół do 0 wystąpienia (gdy są wykonywane żadne zdarzenia) i maksymalnie *n* wystąpień. Dzieje się tak, zapewniając metryki niestandardowe dla skalowania automatycznego platformy Kubernetes (skalowanie w poziomie zasobników).  Używanie funkcji kontenerów za pomocą KEDA umożliwia replikowanie możliwości funkcji bezserwerowej w dowolnym klastrze Kubernetes.  Te funkcje można także wdrożyć za pomocą [wirtualnych węzłów usługi Kubernetes usługi Azure (AKS)](../aks/virtual-nodes-cli.md) funkcję bezserwerową infrastrukturą.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Zarządzanie KEDA i funkcji w usłudze Kubernetes

Aby uruchamiać funkcje w klastrze platformy Kubernetes, należy zainstalować składnik KEDA. Można to zrobić za pomocą tego składnika [podstawowych narzędzi usługi Azure Functions](functions-run-local.md).

### <a name="installing-with-the-azure-functions-core-tools"></a>Instalowanie za pomocą usługi Azure Functions, podstawowe narzędzia

Domyślnie podstawowe narzędzia instaluje składniki KEDA i Osiris, które obsługują oparte na zdarzeniach i skalowania HTTP, odpowiednio.  Instalacja programu używa `kubectl` uruchomione w bieżącym kontekście.

Zainstaluj KEDA w klastrze za pomocą następującego polecenia instalacji:

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>Wdrażanie aplikacji funkcji w usłudze Kubernetes

Każda aplikacja funkcji można wdrożyć w klastrze Kubernetes z systemem KEDA.  Ponieważ funkcje są uruchomione w kontenerze platformy Docker, projekt musi `Dockerfile`.  Jeśli go nie ma jeszcze jeden, można dodać pliku Dockerfile, uruchamiając następujące polecenie w katalogu głównym projektu usługi Functions:

```cli
func init --docker-only
```

Aby utworzyć obraz i wdrażania tworzonych funkcji w usłudze Kubernetes, uruchom następujące polecenie:

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Zastąp `<name-of-function-deployment>` nazwę aplikacji funkcji.

Spowoduje to utworzenie rozwiązania Kubernetes `Deployment` zasobu, `ScaledObject` zasobu, i `Secrets`, w tym zmiennych środowiskowych zaimportowane z usługi `local.settings.json` pliku.

## <a name="removing-a-function-app-from-kubernetes"></a>Usuwanie aplikacji funkcji z usługi Kubernetes

Po wdrażania należy usunąć funkcję, usuwając skojarzone `Deployment`, `ScaledObject`, `Secrets` utworzone.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Odinstalowywanie KEDA z rozwiązania Kubernetes

Można uruchom następujące polecenie podstawowe w narzędzia, aby usunąć KEDA z klastrem Kubernetes:

```cli
func kubernetes remove --namespace keda
```

## <a name="supported-triggers-in-keda"></a>Obsługiwane wyzwalaczy w KEDA

KEDA jest obecnie dostępna w wersji beta z obsługą następujących wyzwalaczy funkcji platformy Azure:

* [Azure Storage Queues](functions-bindings-storage-queue.md)
* [Kolejki usługi Azure Service Bus](functions-bindings-service-bus.md)
* [HTTP](functions-bindings-http-webhook.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)

## <a name="next-steps"></a>Następne kroki
Więcej informacji zawierają następujące zasoby:

* [Tworzenie funkcji przy użyciu niestandardowego obrazu](functions-create-function-linux-custom-image.md)
* [Kodowanie i testowanie usługi Azure Functions lokalnie](functions-develop-local.md)
* [Jak działa w planie zużycie funkcji platformy Azure](functions-scale.md)