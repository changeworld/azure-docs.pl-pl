---
title: IoT wdrażać niestandardowe symulowanych urządzeń — Azure | Dokumentacja firmy Microsoft
description: Ten poradnik pokazuje, jak wdrażać niestandardowe symulowane urządzenia do akceleratora rozwiązania monitorowania zdalnego.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/15/2018
ms.topic: conceptual
ms.openlocfilehash: 7cbab38db859935c9f4490d79a131d6c9a7e302b
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427571"
---
# <a name="deploy-a-new-simulated-device"></a>Wdrażanie nowego urządzenia symulowanego

Zdalne monitorowanie i symulacji urządzenia akceleratorów rozwiązania zarówno pozwalają zdefiniować symulowanych urządzeń. W tym artykule przedstawiono sposób wdrażania Chłodnica dostosowanego typu urządzenia i nowego typu urządzenia żarówki do akceleratora rozwiązania monitorowania zdalnego.

Kroki opisane w tym artykule przyjęto założenie, ukończono [tworzenie i testowanie nowych symulowanych urządzeń](iot-accelerators-remote-monitoring-create-simulated-device.md) porad prowadzi, mają pliki, które definiują dostosowane Chłodnica i nowych typów urządzeń ikony żarówki.

Kroki opisane w tym przewodniku pokazano, jak do:

1. Dostęp do systemu plików z maszyny wirtualnej, która hostuje akcelerator rozwiązań zdalne monitorowanie za pomocą protokołu SSH.

1. Konfigurowanie platformy Docker można załadować modeli urządzeń z lokalizacji poza kontener platformy Docker.

1. Uruchom przy użyciu plików modelu niestandardowego urządzenia akceleratora rozwiązań monitorowania zdalnego.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Aby wykonać kroki opisane w tym przewodniku z instrukcjami, konieczne jest aktywna subskrypcja platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać instrukcje opisane w tym przewodniku, należy:

- Wdrożone wystąpienie programu [zdalne monitorowanie akcelerator rozwiązań](https://www.azureiotsolutions.com/Accelerators#solutions/types/RM2).
- Lokalny **bash** na uruchamianie powłoki `ssh` i `scp` poleceń. W Windows, łatwe do zainstalowania **bash** polega na zainstalowaniu poprawki [git](https://git-scm.com/download/win).
- Niestandardowe pliki modeli, takie jak te opisane w [tworzenie i testowanie nowych symulowanych urządzeń](iot-accelerators-remote-monitoring-create-simulated-device.md).

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Konfigurowanie platformy Docker

W tej sekcji skonfigurujesz Docker, aby załadować pliki modelu urządzenia z **/tmp/devicemodels** folder na maszynie wirtualnej, a nie z wewnątrz kontenera platformy Docker. Uruchom polecenia w tej sekcji w **bash** skorupach na komputerze lokalnym:

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

1. Kopiowanie istniejących plików modelu urządzenia z kontenera do nowej lokalizacji. Po pierwsze Znajdź identyfikator kontenera dla kontenera symulacji urządzenia:

    ```sh
    sudo docker ps
    ```

    Następnie skopiuj pliki modelu urządzenia, aby **tmp** folder na maszynie wirtualnej. Następujące polecenia przyjęto założenie, identyfikator kontenera to c378d6878407 — Zastąp tę wartość za pomocą Identyfikatora kontenera symulacji urządzenia:

    ```sh
    sudo docker cp c378d6878407:/app/webservice/data/devicemodels /tmp
    sudo chown -R azureuser /tmp/devicemodels/
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
    sudo docker ps
    ```

    Jeśli chcesz wyświetlić dziennik z kontenera symulacji urządzenia, uruchom następujące polecenie. Zastąp identyfikator kontenera identyfikator kontenera symulacji urządzenia:

    ```sh
    sudo docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>Uruchamianie symulacji

Modele niestandardowe można teraz użyć w rozwiązaniu do zdalnego monitorowania:

1. Uruchamianie monitorowania zdalnego pulpitu nawigacyjnego z [akceleratorów rozwiązań IoT Azure Microsoft](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Użyj **urządzeń** stronę, aby dodać symulowane urządzenia. Podczas dodawania nowego urządzenia symulowanego do nowych modeli urządzenia są dostępne do wyboru.

1. Pulpit nawigacyjny umożliwia wyświetlanie danych telemetrycznych z urządzenia i wywoływać metody na urządzeniu.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz dalszego zbadania, należy pozostawić wdrożyć akcelerator rozwiązań monitorowania zdalnego.

Jeśli akcelerator rozwiązań nie są już potrzebne, usuń go z [Aprowizowane rozwiązania](https://www.azureiotsolutions.com/Accelerators#dashboard) strony, wybierając ją, a następnie klikając pozycję **Usuń rozwiązanie**.

## <a name="next-steps"></a>Kolejne kroki

W przewodniku pokazano, jak wdrażać modele niestandardowe do akceleratora rozwiązania monitorowania zdalnego. Sugerowane następnym krokiem jest, aby dowiedzieć się, jak [połączyć prawdziwe urządzenie do rozwiązania do zdalnego monitorowania](iot-accelerators-connecting-devices-node.md).
