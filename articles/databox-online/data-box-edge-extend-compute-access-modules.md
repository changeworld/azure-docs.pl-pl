---
title: Zarządzania siecią obliczeniowych usługi Azure Data Box Edge modułów dostępu | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak rozszerzyć sieci obliczeniowych na pole danych krawędzi dostępu modułów za pomocą zewnętrzny adres IP.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 907647725dd6795b3b6482476de7442fbbf66114
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65917238"
---
# <a name="enable-compute-network-on-your-azure-data-box-edge"></a>Włącz sieć obliczeń na krawędzi sieci Azure Data Box

W tym artykule opisano, jak modułów uruchomionych na krawędzi sieci Azure Data Box dostęp do sieci obliczeniowych, włączone na urządzeniu.

Aby skonfigurować sieci, należy wykonać następujące czynności:

- Włączanie interfejsu sieciowego na urządzeniu z systemem krawędź pola danych dla środowiska obliczeniowego
- Dodaj moduł do dostępu do sieci obliczeniowych na krawędzi sieci pole danych
- Sprawdź, że moduł może uzyskiwać dostęp do interfejsu sieciowego włączony

W tym samouczku użyjesz modułu aplikacji serwera internetowego do zaprezentowania scenariusza.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy:

- Urządzenie krawędź pola danych za pomocą urządzenia została ukończona.
- Ukończono **Konfigurowanie obliczeń** krok zgodnie [samouczka: Przekształcanie danych za pomocą usługi Azure Data Box Edge](data-box-edge-deploy-configure-compute-advanced.md#configure-compute) na urządzeniu. Twoje urządzenie powinno mieć skojarzony zasób usługi IoT Hub, IoT device i urządzenia usługi IoT Edge.

## <a name="enable-network-interface-for-compute"></a>Włączanie interfejsu sieciowego dla środowiska obliczeniowego

Dostępu modułów na urządzeniu z za pośrednictwem sieci zewnętrznej, należy przypisać adres IP do interfejsu sieciowego na urządzeniu. Możesz zarządzać te obliczenia ustawienia z Twojego lokalnego internetowego interfejsu użytkownika.

Wykonaj następujące kroki w lokalnej sieci web UI, aby skonfigurować ustawienia obliczeń.

1. W lokalnym internetowym interfejsie użytkownika, przejdź do **konfiguracji > Compute ustawienia**.  

2. **Włącz** interfejsu sieciowego, który ma być używana do łączenia moduł obliczeniowy, która będzie uruchamiana na urządzeniu.

    - Jeśli używasz statycznych adresów IP, wprowadź adres IP dla interfejsu sieciowego.
    - Jeśli używasz protokołu DHCP, adresy IP są automatycznie przypisywane. W tym przykładzie użyto protokołu DHCP.

    ![Włącz ustawienia obliczeń 1](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. Wybierz **Zastosuj** mają zostać zastosowane ustawienia. Zanotuj adres IP przypisany do interfejsu sieciowego, jeśli za pomocą protokołu DHCP.

    ![Włącz ustawienia obliczeń](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Dodaj moduł aplikacji serwer sieci Web

Wykonaj następujące kroki, aby dodać moduł aplikacji serwer sieci Web na urządzeniu z systemem krawędź pola danych.

1. Przejdź do zasobu usługi IoT Hub, skojarzone z urządzenia usługi Edge pole danych, a następnie wybierz pozycję **urządzenie usługi IoT Edge**.
2. Wybierz urządzenie usługi IoT Edge, skojarzone z urządzenia usługi Edge pola danych. Na **szczegóły urządzenia**, wybierz opcję **Ustaw moduły**. Na **Dodaj moduły**, wybierz opcję **+ Dodaj** , a następnie wybierz **moduł usługi IoT Edge**.
3. W **niestandardowe moduły usługi IoT Edge** bloku:

    1. Określ **nazwa** dla modułu aplikacji serwer sieci Web, którą chcesz wdrożyć.
    2. Podaj **identyfikator URI obrazu** do obrazu modułu. Moduł pasujących do podanej nazwy i tagi są pobierane. W tym przypadku `nginx:stable` ściąga obraz stabilny nginx (oznaczone jako wersja stabilna) publicznie [repozytorium platformy Docker](https://hub.docker.com/_/nginx/).
    3. W **opcje tworzenia kontenera**, wklej następujący kod:  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        Ta konfiguracja pozwala uzyskiwać dostęp do modułu przy użyciu adresu IP sieci obliczeniowych za pośrednictwem *http* na porcie TCP 8080 (przy użyciu domyślnego portu serwera internetowego jest 80).

        ![Określ informacje o porcie w bloku niestandardowego modułu usługi IoT Edge](media/data-box-edge-extend-compute-access-modules/module-information.png)

    4. Wybierz pozycję **Zapisz**.

## <a name="verify-module-access"></a>Sprawdź dostęp do modułu

1. Sprawdź moduł zostanie pomyślnie wdrożona i jest uruchomiona. Na **szczegóły urządzenia** stronie **modułów** karcie stan czasu wykonywania modułu powinna być **systemem**.  
2. Łączenie modułu aplikacji serwera sieci web. Otwórz okno przeglądarki i wpisz:

    `http://<compute-network-IP-address>:8080`

    Powinieneś zobaczyć, czy serwer sieci Web aplikacji jest uruchomiona.

    ![Weryfikowanie połączenia z modułem za pośrednictwem określonego portu](media/data-box-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [Zarządzać użytkownikami w witrynie Azure Portal](data-box-edge-manage-users.md).
