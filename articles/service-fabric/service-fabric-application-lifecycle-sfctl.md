---
title: Zarządzanie aplikacjami usługi Azure Service Fabric przy użyciu wiersza polecenia platformy Azure usługi Service Fabric (sfctl)
description: Dowiedz się, jak wdrożyć, a także usunięcie aplikacji z klastra usługi Azure Service Fabric przy użyciu interfejsu wiersza polecenia usługi Azure Service Fabric
services: service-fabric
author: Christina-Kang
manager: chackdan
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 9b0f785a6a43f984708645084a8a8036326d3d24
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60621381"
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli-sfctl"></a>Zarządzanie aplikacją usługi Azure Service Fabric przy użyciu wiersza polecenia platformy Azure usługi Service Fabric (sfctl)

Dowiedz się, jak tworzyć i usuwać aplikacje, które są uruchomione w klastrze usługi Azure Service Fabric.

## <a name="prerequisites"></a>Wymagania wstępne

* Nainstalovat sadu Service Fabric CLI. Następnie wybierz klaster usługi Service Fabric. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z interfejsem wiersza polecenia usługi Service Fabric](service-fabric-cli.md).

* Należy mieć pakiet aplikacji usługi Service Fabric jest gotowa do wdrożenia. Aby uzyskać więcej informacji na temat autora i pakietów aplikacji, przeczytaj temat [modelu aplikacji usługi Service Fabric](service-fabric-application-model.md).

## <a name="overview"></a>Omówienie

Aby wdrożyć nową aplikację, wykonaj następujące kroki:

1. Przekazywanie pakietu aplikacji do magazynu obrazów usługi Service Fabric.
2. Aprowizacji typu aplikacji.
3. Usuń zawartość magazynu obrazów.
4. Określ i tworzenia aplikacji.
5. Określ i tworzenie usług.

Aby usunąć istniejącą aplikację, wykonaj następujące kroki:

1. Usuń aplikację.
2. Cofanie aprowizacji typu aplikacji skojarzone.

## <a name="deploy-a-new-application"></a>Wdrażanie nowej aplikacji

Aby wdrożyć nową aplikację, należy wykonać następujące zadania:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Przekaż nowy pakiet aplikacji do magazynu obrazów

Przed przystąpieniem do tworzenia aplikacji, należy przekazać pakiet aplikacji do magazynu obrazów usługi Service Fabric.

Na przykład, jeśli pakietu aplikacji znajduje się w `app_package_dir` katalogu, użyj następujących poleceń do przekazania do katalogu:

```azurecli
sfctl application upload --path ~/app_package_dir
```

W przypadku dużych pakietów aplikacji, możesz określić `--show-progress` opcję, aby wyświetlić postęp przekazywania.

### <a name="provision-the-application-type"></a>Aprowizacji typu aplikacji

Po zakończeniu przekazywania inicjować obsługę aplikacji. Aby udostępnić aplikację, użyj następującego polecenia:

```azurecli
sfctl application provision --application-type-build-path app_package_dir
```

Wartość `application-type-build-path` to nazwa katalogu, do którego został przekazany do pakietu aplikacji.

### <a name="delete-the-application-package"></a>Usuwanie pakietu aplikacji

Zalecane jest, usuń pakiet aplikacji, po pomyślnym zarejestrowaniu aplikacji.  Usuwanie pakietów aplikacji w sklepie obraz zwolnienie zasobów systemowych.  Utrzymywanie pakiety aplikacji nieużywane wykorzystuje Magazyn dyskowy i prowadzi do problemów z wydajnością aplikacji. 

Aby usunąć pakiet aplikacji z magazynu obrazów, użyj następującego polecenia:

```azurecli
sfctl store delete --content-path app_package_dir
```

`content-path` musi być nazwą katalogu, który został przekazany, podczas tworzenia aplikacji.

### <a name="create-an-application-from-an-application-type"></a>Tworzenie aplikacji na podstawie typu aplikacji

Po zainicjowaniu aplikacji, użyj następującego polecenia, aby nazwać i tworzenie aplikacji:

```azurecli
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` jest to nazwa, którą chcesz używać dla wystąpienia aplikacji. Możesz uzyskać dodatkowe parametry, z manifestu aplikacji wcześniej aprowizowane.

Nazwa aplikacji musi rozpoczynać się od prefiksu `fabric:/`.

### <a name="create-services-for-the-new-application"></a>Tworzenie usług dla nowej aplikacji

Po utworzeniu aplikacji tworzenie usług z aplikacji. W poniższym przykładzie utworzymy nową usługę bezstanową z naszej aplikacji. Usługi, które można utworzyć na podstawie aplikacji są definiowane w manifeście usługi w pakiecie aplikacji wcześniej aprowizowane.

```azurecli
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Sprawdź wdrożenie aplikacji i kondycji

Aby sprawdzić, czy wszystko jest w dobrej kondycji, użyj następujących poleceń kondycji:

```azurecli
sfctl application list
sfctl service list --application-id TestApp
```

Aby sprawdzić, czy usługa jest w dobrej kondycji, należy użyć podobnych poleceń można pobrać kondycji usługi i aplikacji:

```azurecli
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

Dobrej kondycji usługi i aplikacje mają `HealthState` wartość `Ok`.

## <a name="remove-an-existing-application"></a>Usuń istniejącą aplikację

Aby usunąć aplikację, należy wykonać następujące zadania:

### <a name="delete-the-application"></a>Usuń aplikację

Aby usunąć aplikację, użyj następującego polecenia:

```azurecli
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Cofanie aprowizacji typu aplikacji

Po usunięciu aplikacji można cofanie aprowizacji typu aplikacji, jeśli nie są już potrzebne. Aby cofanie aprowizacji typu aplikacji, użyj następującego polecenia:

```azurecli
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

Wpisz nazwę i wersję typu muszą być zgodne, nazwa i wersja w manifeście aplikacji wcześniej aprowizowane.

## <a name="upgrade-application"></a>Uaktualnianie aplikacji

Po utworzeniu aplikacji możesz powtórzyć ten sam zestaw czynności, aby aprowizować drugi wersji aplikacji. Następnie Uaktualnianie aplikacji usługi Service Fabric można przejść do drugiego wersją aplikacji. Aby uzyskać więcej informacji, zobacz dokumentację na [uaktualnień aplikacji usługi Service Fabric](service-fabric-application-upgrade.md).

Aby przeprowadzić uaktualnienie, należy najpierw aprowizować następnej wersji aplikacji przy użyciu tych samych poleceń, tak jak poprzednio:

```azurecli
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Zalecane jest następnie wykonywać monitorowanych automatycznego uaktualniania, uruchom uaktualnienie, uruchamiając następujące polecenie:

```azurecli
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

Uaktualnienia zastąpienie istniejących parametrów przy użyciu dowolnych zestaw jest określony. Parametry aplikacji powinien zostać przekazany jako argumenty do polecenie upgrade, jeśli to konieczne. Parametry aplikacji powinien być kodowany jako obiekt JSON.

Aby pobrać wszystkie wcześniej określone parametry, można użyć `sfctl application info` polecenia.

Gdy trwa uaktualnienie aplikacji, można pobrać stanu przy użyciu `sfctl application upgrade-status` polecenia.

Ponadto w przypadku uaktualnienia w toku i musi zostać anulowana, można użyć `sfctl application upgrade-rollback` można wycofać uaktualnienie.

## <a name="next-steps"></a>Kolejne kroki

* [Podstawowe informacje dotyczące interfejsu wiersza polecenia usługi Service Fabric](service-fabric-cli.md)
* [Wprowadzenie do usługi Service Fabric w systemie Linux](service-fabric-get-started-linux.md)
* [Uruchamianie uaktualnienia aplikacji usługi Service Fabric](service-fabric-application-upgrade.md)
