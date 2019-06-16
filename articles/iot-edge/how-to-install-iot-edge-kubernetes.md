---
title: Jak zainstalować usługi IoT Edge na platformie Kubernetes | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat instalowania usługi IoT Edge na platformie Kubernetes przy użyciu lokalnego klastra środowiska deweloperskiego
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 66aca7be9a2df93d846d7e78bc64c93279afc2d1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65160698"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Jak zainstalować usługi IoT Edge w systemie Kubernetes (wersja zapoznawcza)

Usługi IoT Edge można zintegrować z rozwiązaniem Kubernetes przy użyciu go jako warstwa infrastruktury odporne i o wysokiej dostępności. Rejestruje usługi IoT Edge *definicji zasobu niestandardowego* (CRD) przy użyciu serwera interfejsu API platformy Kubernetes. Ponadto zapewnia *Operator* (agent usługi IoT Edge) który uzgadnia zarządzane w chmurze żądanego stanu ze stanem klastra lokalnego. 

Okres istnienia modułu jest zarządzany przez harmonogram Kubernetes, przechowuje dostępności modułu, która wybiera ich umieszczania. Usługi IoT Edge zarządza platformy aplikacji edge, które są uruchomione na górze, stale uzgadnianie żądanego stanu, które są określone w usłudze IoT Hub ze stanem w klastrze usługi edge. Model aplikacji przeglądarki edge jest nadal znanego modelu na podstawie moduły usługi IoT Edge i trasy. Operator agenta usługi IoT Edge wykonuje *automatyczne* translacji Kubernetes natives tworzy takich jak zasobników, wdrożeń usług itp.

Poniżej przedstawiono diagram architektury wysokiego poziomu:

![Architektura rozwiązania kubernetes](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

Każdy składnik wdrożenie edge jest ograniczony do przestrzeni nazw Kubernetes specyficzne dla urządzenia, dzięki czemu można udostępniać te same zasoby klastra między wiele urządzeń brzegowych i ich wdrożenia.

>[!NOTE]
>Usługi IoT Edge w systemie Kubernetes jest [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="install-locally-for-a-quick-test-environment"></a>Zainstaluj lokalnie dla środowiska testowego szybki

### <a name="prerequisites"></a>Wymagania wstępne

* Kubernetes 1,10 lub nowszej. Jeśli nie masz istniejącej konfiguracji klastra, możesz użyć [Minikube](https://kubernetes.io/docs/setup/minikube/) w środowisku lokalnym klastrem. 

* [Polecenie Helm](https://helm.sh/docs/using_helm/#quickstart-guide), Menedżer pakietów platformy Kubernetes.

* [narzędzia kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) umożliwiające wyświetlanie i wchodzenie w interakcje z klastrem.

### <a name="setup-steps"></a>Kroki konfiguracji

1. Rozpocznij **Minikube**

    ``` shell
    minikube start
    ```

1. Inicjowanie **Helm** składnika serwera (*tiller*) w klastrze

    ``` shell
    helm init
    ```

1. Dodaj repozytorium usługi IoT Edge i zaktualizowania instalacji narzędzia helm

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [Tworzenie Centrum IoT](../iot-hub/iot-hub-create-through-portal.md), [zarejestrować urządzenia usługi IoT Edge](how-to-register-device-portal.md)i zanotuj jego parametry połączenia.

1. Zainstaluj iotedged i agenta usługi IoT Edge do klastra

    ```shell
    helm install \
    --name k8s-edge1 \
    --set "deviceConnectionString=replace-with-device-connection-string" \
    edgek8s/edge-kubernetes
    ```
1. Otwórz pulpit nawigacyjny platformy Kubernetes w przeglądarce

    ```shell
    minikube dashboard
    ```

    W obszarze obszary nazw klastra, zostanie wyświetlony jeden dla urządzenia usługi IoT Edge, zgodnie z Konwencją *msiot —\<iothub-name >-\<edgedevice-name >* . Zasobników iotedged i agent usługi IoT Edge należy działa w tej przestrzeni nazw.

1. Dodaj moduł czujnika temperatury symulowane przy użyciu kroków w [wdrażanie modułu](quickstart-linux.md#deploy-a-module) części tego przewodnika Szybki Start. Zarządzanie moduł usługi IoT Edge odbywa się w portalu usługi IoT Hub, podobnie jak inne urządzenie usługi IoT Edge. Wprowadzanie zmian lokalnych do konfiguracji modułu za pomocą narzędzia Kubernetes nie jest zalecane, ponieważ mogą one zostać zastąpione.

1. W ciągu kilku sekund, odświeżanie **zasobników** zostaną wyświetlone na stronie w obszarze nazw urządzenia usługi edge na pulpicie nawigacyjnym Centrum usługi IoT Edge i zasobników symulowanych czujników jako uruchomiona za pomocą usługi IoT Edge hub zasobnika pozyskiwania danych do usługi IoT Hub.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć wszystkie zasoby utworzone przez wdrożenie edge, użyj następującego polecenia z nazwą w kroku 5 w poprzedniej sekcji.

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>Kolejne kroki

### <a name="deploy-as-a-highly-available-edge-gateway"></a>Wdróż jako bramy o wysokiej dostępności usługi edge 

Urządzenie brzegowe w klastrze Kubernetes może służyć jako bramy usługi IoT dla urządzeń z podrzędnym. Może on skonfigurowany, aby była odporna na awarie węzła, zapewniając wysoką dostępność dla wdrożeń usługi edge. Zobacz ten [szczegółowy przewodnik](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes) do użycia usługi IoT Edge w tym scenariuszu.