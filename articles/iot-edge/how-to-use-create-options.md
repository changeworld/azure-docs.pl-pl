---
title: Napisz createOptions dla modułów — Usługa Azure IoT Edge | Dokumenty firmy Microsoft
description: Jak używać createOptions w manifeście wdrożenia do konfigurowania modułów w czasie wykonywania
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c07e161042a497a232cbd5e3f11128893a095381
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550346"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>Jak skonfigurować opcje tworzenia kontenerów dla modułów Usługi IoT Edge

Parametr **createOptions** w manifeście wdrażania umożliwia skonfigurowanie kontenerów modułu w czasie wykonywania. Ten parametr rozszerza kontrolę nad modułami i umożliwia wykonywanie zadań, takich jak zezwalanie lub ograniczanie dostępu modułu do zasobów urządzenia hosta lub konfigurowanie sieci.

Moduły IoT Edge są implementowane jako kontenery zgodne z docker na urządzeniu IoT Edge. Docker oferuje wiele opcji tworzenia kontenerów, a te opcje dotyczą również modułów IoT Edge. Aby uzyskać więcej informacji, zobacz [Opcje tworzenia kontenera platformy Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="format-create-options"></a>Formatowanie opcji tworzenia

Manifest wdrażania usługi IoT Edge akceptuje opcje tworzenia sformatowane jako JSON. Na przykład należy wziąć udział w opcjach tworzenia, które są automatycznie uwzględniane dla każdego modułu edgeHub:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "5671/tcp": [
        {
          "HostPort": "5671"
        }
      ],
      "8883/tcp": [
        {
          "HostPort": "8883"
        }
      ],
      "443/tcp": [
        {
          "HostPort": "443"
        }
      ]
    }
  }
}
```

W tym przykładzie edgeHub używa **Parametru HostConfig.PortBindings** do mapowania narażonych portów w kontenerze na port na urządzeniu hosta.

Jeśli używasz rozszerzeń narzędzi Azure IoT tools dla programu Visual Studio lub Visual Studio Code, możesz napisać opcje tworzenia w formacie JSON w pliku **deployment.template.json.** Następnie, gdy użyjesz rozszerzenia do utworzenia rozwiązania usługi IoT Edge lub wygenerowania manifestu wdrożenia, będzie on akcesji JSON dla Ciebie w formacie, który oczekuje środowiska wykonawczego usługi IoT Edge. Przykład:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

Jedną z wskazówek do pisania `docker inspect` opcji tworzenia jest użycie polecenia. W ramach procesu rozwoju uruchom moduł lokalnie przy użyciu programu `docker run <container name>`. Gdy moduł działa tak, jak chcesz, `docker inspect <container name>`uruchom program . To polecenie wyprowadza szczegóły modułu w formacie JSON. Znajdź skonfigurowane parametry i skopiuj JSON. Przykład:

[![Wyniki kontroli platformy docker edgeHub](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png)](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>Typowe scenariusze

Opcje tworzenia kontenera umożliwiają wiele scenariuszy, ale oto niektóre, które pojawiają się najczęściej podczas tworzenia rozwiązań usługi IoT Edge:

* [Zapewnianie modułom dostępu do pamięci masowej hosta](how-to-access-host-storage-from-module.md)
* [Mapowanie portu hosta do portu modułu](#map-host-port-to-module-port)
* [Ograniczanie użycia pamięci modułu i procesora](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>Mapowanie portu hosta do portu modułu

Jeśli moduł musi komunikować się z usługą poza rozwiązaniem usługi IoT Edge i nie używa routingu wiadomości, aby to zrobić, należy zamapować port hosta do portu modułu.

>[!TIP]
>To mapowanie portów nie jest wymagane do komunikacji moduł-moduł na tym samym urządzeniu. Jeśli moduł A musi zbadać interfejs API hostowany w module B, może to zrobić bez mapowania portów. Moduł B musi uwidaczniać port w pliku `EXPOSE 8080`dockerfile, na przykład: . Następnie moduł A może wysyłać zapytania do API `http://ModuleB:8080/api`przy użyciu nazwy modułu B, na przykład: .

Najpierw upewnij się, że port wewnątrz modułu jest narażony na nasłuchiwanych połączeń. Można to zrobić za pomocą instrukcji [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) w pliku dockerfile. Na przykład `EXPOSE 8080`. Instrukcja expose domyślnie do protokołu TCP, jeśli nie jest określony, lub można określić UDP.

Następnie użyj **ustawienia PortBindings** w grupie **HostConfig** [kontenera Platformy Docker, aby](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) zamapować narażony port w module na port na urządzeniu hosta. Na przykład jeśli narażony port 8080 wewnątrz modułu i chcesz zamapować go na port 80 urządzenia hosta, opcje tworzenia w pliku template.json będą wyglądać następująco:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "8080/tcp": [
        {
          "HostPort": "80"
        }
      ]
    }
  }
}
```

Po stringified dla manifestu wdrożenia, ta sama konfiguracja będzie wyglądać następująco:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>Ograniczanie użycia pamięci modułu i procesora

Można zadeklarować, ile zasobów hosta moduł może używać. Ten formant jest przydatne, aby upewnić się, że jeden moduł nie może zużywać zbyt dużo pamięci lub użycia procesora CPU i zapobiec innych procesów z systemem na urządzeniu. Te ustawienia można zarządzać za pomocą [kontenera Platformy Docker tworzenie opcji](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) w grupie **HostConfig,** w tym:

* **Pamięć**: Limit pamięci w bajtach. Na przykład 268435456 bajtów = 256 MB.
* **MemorySwap**: Całkowity limit pamięci (pamięć + zamiana). Na przykład 536870912 bajtów = 512 MB
* **CpuPeriod:** Długość okresu procesora w mikrosekundach. Wartość domyślna to 100000, więc na przykład wartość 25000 ogranicza kontener do 25% zasobów procesora.

W formacie template.json te wartości będą wyglądać następująco:

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

Po stringified dla manifestu końcowego wdrożenia, te wartości będą wyglądać następująco:

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej przykładów tworzenia opcji w akcji, zobacz następujące przykłady IoT Edge:

* [Custom Vision i Azure IoT Edge na Raspberry Pi 3](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi)
* [Przykład magazynu obiektów blob usługi Azure IoT Edge](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)
