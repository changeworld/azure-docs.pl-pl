---
title: Jak zainstalować IoT Edge na Kubernetes | Microsoft Docs
description: Informacje na temat instalowania IoT Edge na Kubernetes przy użyciu lokalnego środowiska klastra projektowego
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: a453779ffe4ae20acf55510d0ac9f9483763af21
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964835"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Jak zainstalować IoT Edge w Kubernetes (wersja zapoznawcza)

IoT Edge można zintegrować z usługą Kubernetes przy użyciu jej jako odpornej warstwy infrastruktury o wysokiej dostępności. Rejestruje IoT Edge *niestandardowe definicje zasobów* (CRD) z serwerem interfejsu API Kubernetes. Ponadto zapewnia *operatorowi* (IoT Edge Agent), który uzgadnia żądany stan zarządzany przez chmurę ze stanem lokalnego klastra. 

Okres istnienia modułu jest zarządzany przez usługę Kubernetes Scheduler, która zachowuje dostępność modułu i wybiera ich rozmieszczenie. IoT Edge zarządza platformą aplikacji brzegowej działającą na górze, nieustannie uzgadniając żądany stan określony w IoT Hub ze stanem w klastrze brzegowym. Model aplikacji brzegowych nadal jest znanym modelem opartym na IoT Edge modułach i trasach. Operator agenta IoT Edge wykonuje *Automatyczne* tłumaczenie na konstrukcje natywne Kubernetes, takie jak zasobniki, wdrożenia, usługi itd.

Oto diagram architektury wysokiego poziomu:

![Kubernetes Arch](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

Każdy składnik wdrożenia brzegowego jest objęty zakresem przestrzeni nazw Kubernetes specyficzne dla urządzenia, dzięki czemu możliwe jest udostępnianie tych samych zasobów klastra między wieloma urządzeniami brzegowymi i ich wdrożeniami.

>[!NOTE]
>IoT Edge w Kubernetes jest w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="install-locally-for-a-quick-test-environment"></a>Zainstaluj lokalnie dla szybkiego środowiska testowego

### <a name="prerequisites"></a>Wymagania wstępne

* Kubernetes 1,10 lub nowszy. Jeśli nie masz istniejącej konfiguracji klastra, możesz użyć [Minikube](https://kubernetes.io/docs/setup/minikube/) do lokalnego środowiska klastra. 

* [Helm](https://helm.sh/docs/using_helm/#quickstart-guide), Menedżer pakietów Kubernetes.

* [polecenia kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) do wyświetlania klastra i korzystania z niego.

### <a name="setup-steps"></a>Kroki konfiguracji

1. Uruchom **Minikube**

    ``` shell
    minikube start
    ```

1. Inicjowanie składnika serwera **Helm** *(er*) w klastrze

    ``` shell
    helm init
    ```

1. Dodawanie IoT Edge repozytorium i aktualizowanie instalacji Helm

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [Utwórz IoT Hub](../iot-hub/iot-hub-create-through-portal.md), [Zarejestruj urządzenie IoT Edge](how-to-register-device.md)i zanotuj jego parametry połączenia.

1. Instalowanie iotedged i agenta IoT Edge w klastrze

    ```shell
    helm install \
    --name k8s-edge1 \
    --set "deviceConnectionString=replace-with-device-connection-string" \
    edgek8s/edge-kubernetes
    ```
1. Otwórz pulpit nawigacyjny Kubernetes w przeglądarce

    ```shell
    minikube dashboard
    ```

    W obszarze przestrzenie nazw klastra zobaczysz jeden dla IoT Edgego urządzenia po Konwencji *msiot-\<iothub >-\<edgedevice-name >* . Agent IoT Edge i iotedgedy są uruchamiane w tej przestrzeni nazw.

1. Dodaj moduł symulowanego czujnika temperatury przy użyciu kroków z sekcji [wdrażanie modułu](quickstart-linux.md#deploy-a-module) przewodnika Szybki Start. IoT Edge zarządzanie modułami odbywa się z poziomu portalu IoT Hub podobnie jak w przypadku każdego innego urządzenia IoT Edge. Wprowadzanie lokalnych zmian konfiguracji modułu za pomocą narzędzi Kubernetes nie jest zalecane, ponieważ mogą one zostać nadpisywane.

1. W ciągu kilku sekund odświeżanie strony **z** obszarem nazw urządzenia brzegowego na pulpicie nawigacyjnym spowoduje wyświetlenie listy centrów IoT Edge i symulowanych modułów czujnika jako uruchomionych z Centrum IoT Edge w celu pozyskiwania danych w IoT Hub.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć wszystkie zasoby utworzone przez wdrożenie brzegowe, użyj następującego polecenia z nazwą użytą w kroku 5 poprzedniej sekcji.

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>Następne kroki

### <a name="deploy-as-a-highly-available-edge-gateway"></a>Wdróż jako bramę brzegową o wysokiej dostępności 

Urządzenie brzegowe w klastrze Kubernetes może być używane jako brama IoT dla urządzeń podrzędnych. Można ją skonfigurować tak, aby była odporna na awarie węzła w taki sposób, zapewniając wysoką dostępność wdrożeń brzegowych. Ten [szczegółowy przewodnik](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes) zawiera IoT Edge w tym scenariuszu.