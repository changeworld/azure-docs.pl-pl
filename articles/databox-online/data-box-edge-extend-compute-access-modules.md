---
title: Zarządzanie siecią obliczeniową w usłudze Azure Data Box Edge w celu uzyskania dostępu do modułów| Dokumenty firmy Microsoft
description: W tym artykule opisano sposób rozszerzenia sieci obliczeniowej na urządzeniu Data Box Edge, aby uzyskać dostęp do modułów za pośrednictwem zewnętrznego adresu IP.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 907647725dd6795b3b6482476de7442fbbf66114
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65917238"
---
# <a name="enable-compute-network-on-your-azure-data-box-edge"></a>Włączanie sieci obliczeniowej w usłudze Azure Data Box Edge

W tym artykule opisano, jak moduły uruchomione w usłudze Azure Data Box Edge mogą uzyskiwać dostęp do sieci obliczeniowej włączonej na urządzeniu.

Aby skonfigurować sieć, należy wykonać następujące czynności:

- Włączanie interfejsu sieciowego na urządzeniu data box edge do obliczenia
- Dodawanie modułu w celu uzyskania dostępu do sieci obliczeniowej w witrynie Data Box Edge
- Sprawdź, czy moduł może uzyskać dostęp do włączonego interfejsu sieciowego

W tym samouczku użyjesz modułu aplikacji serwera www, aby zademonstrować scenariusz.

## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz, będziesz potrzebować:

- Ukończono urządzenie Data Box Edge z konfiguracją urządzenia.
- Ukończono **konfigurowanie** kroku obliczeniowego zgodnie [z samouczka: Przekształcanie danych za pomocą usługi Azure Data Box Edge](data-box-edge-deploy-configure-compute-advanced.md#configure-compute) na urządzeniu. Urządzenie powinno mieć skojarzony zasób Usługi IoT Hub, urządzenie IoT i urządzenie usługi IoT Edge.

## <a name="enable-network-interface-for-compute"></a>Włączanie interfejsu sieciowego dla obliczeń

Aby uzyskać dostęp do modułów uruchomionych na urządzeniu za pośrednictwem sieci zewnętrznej, musisz przypisać adres IP do interfejsu sieciowego urządzenia. Te ustawienia obliczeniowe można zarządzać za pomocą lokalnego interfejsu użytkownika sieci Web.

Aby skonfigurować ustawienia obliczeń, należy wykonać następujące kroki w lokalnym interfejsie użytkownika sieci Web.

1. W lokalnym interfejsie użytkownika sieci Web przejdź do **ustawień konfiguracji > obliczeń**.  

2. **Włącz** interfejs sieciowy, którego chcesz użyć, aby połączyć się z modułem obliczeniowym, który będzie uruchamiany na urządzeniu.

    - Jeśli używasz statycznych adresów IP, wprowadź adres IP interfejsu sieciowego.
    - W przypadku korzystania z usługi DHCP adresy IP są przypisywane automatycznie. W tym przykładzie użyto protokołu DHCP.

    ![Włączanie ustawień obliczeniowych 1](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. Wybierz **pozycję Zastosuj,** aby zastosować ustawienia. Zanotuj adres IP przypisany do interfejsu sieciowego w przypadku korzystania z usługi DHCP.

    ![Włączanie ustawień obliczeniowych](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Dodawanie modułu aplikacji serwera www

Aby dodać moduł aplikacji serwera www na urządzeniu data box edge, należy wykonać następujące czynności.

1. Przejdź do zasobu Usługi IoT Hub skojarzonego z urządzeniem Data Box Edge, a następnie wybierz **pozycję Urządzenie Usługi IoT Edge**.
2. Wybierz urządzenie Usługi IoT Edge skojarzone z urządzeniem Data Box Edge. W obszarze **Szczegóły urządzenia**wybierz pozycję **Ustaw moduły**. Na **dodaj moduły**wybierz **+ Dodaj,** a następnie wybierz opcję **IoT Edge Module**.
3. W bloku **modułów niestandardowych IoT Edge:**

    1. Określ **nazwę** modułu aplikacji serwera sieci web, który chcesz wdrożyć.
    2. Podaj **identyfikator URI obrazu** dla obrazu modułu. Moduł pasujący do podanej nazwy i tagów jest pobierany. W takim `nginx:stable` przypadku pociągnie stabilny obraz nginx (oznaczony jako stabilny) z publicznego [repozytorium docker](https://hub.docker.com/_/nginx/).
    3. W **kontenerze Opcje tworzenia**wklej następujący przykładowy kod:  

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

        Ta konfiguracja umożliwia dostęp do modułu przy użyciu adresu IP sieci obliczeniowej przez *http* na porcie TCP 8080 (przy czym domyślny port serwera www to 80).

        ![Określanie informacji o porcie w bloku modułu niestandardowego usługi IoT Edge](media/data-box-edge-extend-compute-access-modules/module-information.png)

    4. Wybierz **pozycję Zapisz**.

## <a name="verify-module-access"></a>Weryfikowanie dostępu do modułu

1. Sprawdź, czy moduł został pomyślnie wdrożony i jest uruchomiony. Na stronie **Szczegóły urządzenia** na karcie **Moduły** powinien być uruchomiony stan środowiska wykonawczego **modułu**.  
2. Połącz się z modułem aplikacji serwera www. Otwórz okno przeglądarki i wpisz:

    `http://<compute-network-IP-address>:8080`

    Powinieneś zobaczyć, że aplikacja serwera www jest uruchomiona.

    ![Weryfikowanie połączenia z modułem na określonym porcie](media/data-box-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [Zarządzać użytkownikami w witrynie Azure Portal](data-box-edge-manage-users.md).
