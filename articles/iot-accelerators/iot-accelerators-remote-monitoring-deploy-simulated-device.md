---
title: IoT wdraża niestandardowe symulowane urządzenia — Platforma Azure | Dokumenty firmy Microsoft
description: W tym przewodniku przedstawiono sposób wdrażania niestandardowych symulowanych urządzeń w akceleratorze rozwiązań do zdalnego monitorowania.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/15/2018
ms.topic: conceptual
ms.openlocfilehash: 7cbab38db859935c9f4490d79a131d6c9a7e302b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66427571"
---
# <a name="deploy-a-new-simulated-device"></a>Wdrażanie nowego symulowanego urządzenia

Akceleratory rozwiązań do zdalnego monitorowania i symulacji urządzeń umożliwiają definiowanie własnych symulowanych urządzeń. W tym artykule pokazano, jak wdrożyć niestandardowy typ urządzenia agregatu chłodniczego i nowy typ urządzenia żarówki do akceleratora rozwiązania zdalnego monitorowania.

Kroki opisane w tym artykule zakładają, że ukończono [tworzenie i testowanie nowego symulowanego urządzenia,](iot-accelerators-remote-monitoring-create-simulated-device.md) aby prowadzić i mieć pliki, które definiują niestandardowy agregat chłodniczy i nowe typy urządzeń żarówki.

Kroki opisane w tym przewodniku pokazują, jak:

1. Użyj SSH, aby uzyskać dostęp do systemu plików maszyny wirtualnej, na której znajduje się akcelerator rozwiązań zdalnego monitorowania.

1. Skonfiguruj program Docker, aby załadować modele urządzeń z lokalizacji poza kontenerem platformy Docker.

1. Uruchom akcelerator rozwiązań do zdalnego monitorowania przy użyciu niestandardowych plików modelu urządzenia.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Aby wykonać kroki opisane w tym przewodniku, potrzebujesz aktywnej subskrypcji platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby postępować zgodnie z tym przewodnikiem, potrzebujesz:

- Wdrożone wystąpienie [akceleratora rozwiązań do zdalnego monitorowania](https://www.azureiotsolutions.com/Accelerators#solutions/types/RM2).
- Lokalna **powłoka** bash `ssh` `scp` do uruchamiania i poleceń. W systemie Windows, łatwy sposób, aby zainstalować **bash** jest [zainstalowanie git](https://git-scm.com/download/win).
- Niestandardowe pliki modelu urządzenia, takie jak te opisane w [obszarze Tworzenie i testowanie nowego symulowanego urządzenia.](iot-accelerators-remote-monitoring-create-simulated-device.md)

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Konfigurowanie platformy Docker

W tej sekcji można skonfigurować platformy Docker, aby załadować pliki modelu urządzenia z **folderu /tmp/devicemodels** na maszynie wirtualnej, a nie z wewnątrz kontenera platformy Docker. Uruchom polecenia w tej sekcji w powłoce **bash** na komputerze lokalnym:

W tej sekcji można skonfigurować platformy Docker, aby załadować pliki modelu urządzenia z **folderu /tmp/devicemodels** na maszynie wirtualnej, a nie z wewnątrz kontenera platformy Docker. Uruchom polecenia w tej sekcji w powłoce **bash** na komputerze lokalnym:

1. Użyj SSH, aby połączyć się z maszyną wirtualną na platformie Azure z komputera lokalnego. Następujące polecenie przyjmuje publiczny adres IP maszyny wirtualnej **vm-vikxv** jest **104.41.128.108** -- zastąpić tę wartość publicznym adresem IP maszyny wirtualnej z poprzedniej sekcji:

   ```sh
    ssh azureuser@104.41.128.108
    ```

    Postępuj zgodnie z monitami, aby zalogować się do maszyny wirtualnej przy hasłem ustawionym w poprzedniej sekcji.

1. Skonfiguruj usługę symulacji urządzeń, aby załadować modele urządzeń spoza kontenera. Najpierw otwórz plik konfiguracyjny platformy Docker:

    ```sh
    sudo nano /app/docker-compose.yml
    ```

    Znajdź ustawienia kontenera **urządzeniaimulacja** i edytuj ustawienie **woluminów,** jak pokazano w poniższym urywek:

    ```yml
    devicesimulation:
      image: azureiotpcs/device-simulation-dotnet:1.0.0
      networks:
        - default_net
      depends_on:
        - storageadapter
      environment:
        - PCS_KEYVAULT_NAME
        - PCS_AAD_APPID
        - PCS_AAD_APPSECRET
      # How one could mount custom device models
      volumes:
        - /tmp/devicemodels:/app/webservice/data/devicemodels:ro
    ```

    Zapisz zmiany.

1. Skopiuj istniejące pliki modelu urządzenia z kontenera do nowej lokalizacji. Najpierw znajdź identyfikator kontenera dla kontenera symulacji urządzenia:

    ```sh
    sudo docker ps
    ```

    Następnie skopiuj pliki modelu urządzenia do folderu **tmp** na maszynie wirtualnej. Następujące polecenie przyjmuje założenie, że identyfikator kontenera to c378d6878407 — zastąpić tę wartość identyfikatorem kontenera symulacji urządzenia:

    ```sh
    sudo docker cp c378d6878407:/app/webservice/data/devicemodels /tmp
    sudo chown -R azureuser /tmp/devicemodels/
    ```

    Okno **bashu** należy utrzymywać z otwartą sesją SSH.

1. Skopiuj niestandardowe pliki modelu urządzenia do maszyny wirtualnej. Uruchom to polecenie w innej powłoce **bash** na komputerze, na którym utworzono niestandardowe modele urządzeń. Najpierw przejdź do folderu lokalnego zawierającego pliki JSON modelu urządzenia. Następujące polecenia zakładają, że publiczny adres IP maszyny wirtualnej to **104.41.128.108** — zastąpić tę wartość publicznym adresem IP maszyny wirtualnej. Po wyświetleniu monitu wprowadź hasło maszyny wirtualnej:

    ```sh
    scp *json azureuser@104.41.128.108:/tmp/devicemodels
    cd scripts
    scp *js azureuser@104.41.128.108:/tmp/devicemodels/scripts
    ```

1. Uruchom ponownie kontener platformy Docker symulacji urządzenia, aby użyć nowych modeli urządzeń. Uruchom następujące polecenia w powłoce **bash** z otwartą sesją SSH na maszynie wirtualnej:

    ```sh
    sudo /app/start.sh
    ```

    Jeśli chcesz zobaczyć stan uruchomionych kontenerów platformy Docker i ich identyfikatory kontenerów, użyj następującego polecenia:

    ```sh
    sudo docker ps
    ```

    Jeśli chcesz wyświetlić dziennik z kontenera symulacji urządzenia, uruchom następujące polecenie. Wymień identyfikator kontenera na identyfikator kontenera symulacji urządzenia:

    ```sh
    sudo docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>Uruchom symulację

Teraz można używać niestandardowych modeli urządzeń w rozwiązaniu do zdalnego monitorowania:

1. Uruchom pulpit nawigacyjny zdalnego monitorowania z [akceleratorów rozwiązań IoT platformy Microsoft Azure](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Strona **Urządzenia** służy do dodawania symulowanych urządzeń. Po dodaniu nowego symulowanego urządzenia nowe modele urządzeń są dostępne do wyboru.

1. Pulpit nawigacyjny służy do wyświetlania danych telemetrycznych urządzenia i metod urządzenia wywołania.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz zbadać dalej, pozostawić akceleratora rozwiązania zdalnego monitorowania wdrożony.

Jeśli akcelerator rozwiązań nie jest już potrzebny, usuń go ze strony [Aproweuj rozwiązania,](https://www.azureiotsolutions.com/Accelerators#dashboard) zaznaczając go, a następnie klikając pozycję **Usuń rozwiązanie**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku pokazano, jak wdrożyć niestandardowe modele urządzeń w akceleratorze rozwiązań do zdalnego monitorowania. Sugerowanym następnym krokiem jest nauczenie się [podłączania prawdziwego urządzenia do rozwiązania do zdalnego monitorowania.](iot-accelerators-connecting-devices-node.md)
