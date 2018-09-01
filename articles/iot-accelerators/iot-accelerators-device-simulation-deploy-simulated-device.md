---
title: IoT wdrażać niestandardowe symulowanych urządzeń — Azure | Dokumentacja firmy Microsoft
description: Ten poradnik pokazuje, jak wdrażać niestandardowe symulowanych urządzeń do symulacji urządzenia akceleratora rozwiązań.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/14/2018
ms.topic: conceptual
ms.openlocfilehash: e51d0330c3ed804bff8cdb06265bb8c39141733f
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43345233"
---
# <a name="deploy-a-new-simulated-device"></a>Wdrażanie nowego urządzenia symulowanego

Zdalne monitorowanie i symulacji urządzenia akceleratorów rozwiązania zarówno pozwalają zdefiniować symulowanych urządzeń. W tym artykule przedstawiono sposób wdrażania Chłodnica dostosowanego typu urządzenia i nowego typu urządzenia żarówki akcelerator rozwiązań symulacji urządzenia.

Kroki opisane w tym artykule przyjęto założenie, ukończono [tworzenie i testowanie nowych symulowanych urządzeń](iot-accelerators-remote-monitoring-create-simulated-device.md) porad prowadzi, mają pliki, które definiują dostosowane Chłodnica i nowych typów urządzeń ikony żarówki.

Kroki opisane w tym przewodniku pokazano, jak do:

1. Dostęp do systemu plików maszyny wirtualnej, który jest hostem akcelerator rozwiązań symulacji urządzenia za pomocą protokołu SSH.

1. Konfigurowanie platformy Docker można załadować modeli urządzeń z lokalizacji poza kontener platformy Docker.

1. Uruchamianie symulacji za pomocą urządzeń niestandardowych plików modelu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Aby wykonać kroki opisane w tym przewodniku z instrukcjami, konieczne jest aktywna subskrypcja platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać instrukcje opisane w tym przewodniku, należy:

- Wdrożone wystąpienie programu [akcelerator rozwiązań symulacji urządzenia](https://www.azureiotsolutions.com/Accelerators#solutions/types/DS).
- Lokalny **bash** na uruchamianie powłoki `ssh` i `scp` poleceń. W Windows, łatwe do zainstalowania **bash** polega na zainstalowaniu poprawki [git](https://git-scm.com/download/win).
- Niestandardowe pliki modeli, takie jak te opisane w [tworzenie i testowanie nowych symulowanych urządzeń](iot-accelerators-remote-monitoring-create-simulated-device.md).

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Konfigurowanie platformy Docker

W tej sekcji skonfigurujesz Docker, aby załadować pliki modelu urządzenia z **/tmp/devicemodels** folder na maszynie wirtualnej, a nie z wewnątrz kontenera platformy Docker. Uruchom polecenia w tej sekcji w **bash** skorupach na komputerze lokalnym:

1. Korzystanie z protokołu SSH, aby nawiązać połączenie z maszyną wirtualną na platformie Azure na maszynie lokalnej. Poniższego polecenia założono publiczny adres IP maszyny wirtualnej **vikxv maszyny wirtualnej** jest **104.41.128.108** — Zastąp tę wartość z publicznym adresem IP swojej maszyny wirtualnej z poprzedniej sekcji:

   ```sh
    ssh azureuser@104.41.128.108
    ```

    Postępuj zgodnie z monitami, aby zalogować się do maszyny wirtualnej przy użyciu hasła, które można ustawić w poprzedniej sekcji.

1. Skonfiguruj usługę symulacji urządzenia można załadować modeli urządzeń z zewnątrz kontenera. Najpierw otwórz plik konfiguracji platformy Docker:

    ```sh
    sudo nano /app/docker-compose.yml
    ```

    Znajdź ustawienia **devicesimulation** kontenera i edytowanie **woluminów** ustawienia, jak pokazano w poniższym fragmencie kodu:

    ```yml
    # To mount custom device models, upload the files to the VM, edit and uncomment the following line:
          - /tmp/devicemodels:/app/webservice/data/devicemodels:ro
    # To mount a custom service configuration, create a custom file, edit and uncomment the following line:
    #     - /app/custom-device-simulation-appsettings.ini:/app/webservice/appsettings.ini:ro
    ```

    Zapisz zmiany.

1. Tworzenie folderów, które mają być przechowywane pliki JSON i skrypt:

    ```sh
    sudo mkdir -p /tmp/devicemodels/scripts
    ```

    Zachowaj **bash** okna z poziomu sesji protokołu SSH, Otwórz.

1. Skopiuj pliki modelu urządzeń niestandardowych do maszyny wirtualnej. Uruchom następujące polecenie w innym **bash** skorupach na komputerze, na którym zostały utworzone modele niestandardowe. Przejdź do folderu lokalnego, który zawiera pliki JSON model urządzenia. W poniższych poleceniach założono, publiczny adres IP maszyny wirtualnej jest **104.41.128.108** — Zastąp tę wartość z publicznym adresem IP swojej maszyny wirtualnej. Wprowadź hasło maszyny wirtualnej po wyświetleniu monitu:

    ```sh
    scp *json azureuser@104.41.128.108:/tmp/devicemodels
    cd scripts
    scp *js azureuser@104.41.128.108:/tmp/devicemodels/scripts
    ```

1. Uruchom ponownie kontener platformy Docker symulacji urządzenia, aby użyć nowych modeli urządzeń. Uruchom następujące polecenia **bash** skorupach z Otwórz sesję SSH z maszyną wirtualną:

    ```sh
    sudo /app/start.sh
    ```

    Jeśli chcesz wyświetlić stan uruchomione kontenery platformy Docker i ich identyfikatory kontenerów, użyj następującego polecenia:

    ```sh
    docker ps
    ```

    Jeśli chcesz wyświetlić dziennik z kontenera symulacji urządzenia, uruchom następujące polecenie. Zastąp identyfikator kontenera identyfikator kontenera symulacji urządzenia:

    ```sh
    docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>Uruchamianie symulacji

Teraz możesz uruchomić symulację przy użyciu modeli urządzeń niestandardowych:

1. Uruchomienie symulacji urządzenia internetowego interfejsu użytkownika z [akceleratorów rozwiązań IoT Azure Microsoft](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Użyj interfejsu użytkownika sieci web, aby skonfigurować i uruchomić symulację przy użyciu jednej z Twoich urządzeń niestandardowych modeli.

1. Po uruchomieniu symulację, kliknij przycisk **metryki widok IoT Hub w witrynie Azure portal** do monitorowania symulacji. Alternatywnie można użyć następującego polecenia wiersza polecenia platformy Azure do monitorowania urządzenia do chmury ruchu. Zastąp nazwę Centrum IoT hub z Twoją nazwą Centrum:

    ```azurecli-interactive
    az iot hub monitor-events -n contoso-simulation9dc75
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz dalsze działanie, akcelerator rozwiązania do symulacji urządzenia powinien pozostać wdrożony.

Jeśli akcelerator rozwiązań nie są już potrzebne, usuń go z [Aprowizowane rozwiązania](https://www.azureiotsolutions.com/Accelerators#dashboard) strony, wybierając ją, a następnie klikając pozycję **Usuń rozwiązanie**.

## <a name="next-steps"></a>Kolejne kroki

W przewodniku pokazano, jak wdrażać modele niestandardowe do symulacji urządzenia akceleratora rozwiązań. Sugerowane następnym krokiem jest, aby dowiedzieć się więcej o [schematu modelu urządzenia](iot-accelerators-device-simulation-device-schema.md).
