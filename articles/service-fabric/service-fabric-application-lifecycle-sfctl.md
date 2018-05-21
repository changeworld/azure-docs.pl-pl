---
title: Zarządzanie aplikacjami sieć szkieletowa usług Azure przy użyciu interfejsu wiersza polecenia Azure Service Fabric
description: Dowiedz się, jak wdrożyć i usuwać aplikacje z klastra usługi sieć szkieletowa usług Azure przy użyciu interfejsu wiersza polecenia Azure Service Fabric
services: service-fabric
author: Christina-Kang
manager: timlt
ms.service: service-fabric
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: bikang
ms.openlocfilehash: 18edd28943566daa382a1d742f25aaf9cc99792b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli"></a>Zarządzanie aplikacją sieci szkieletowej usług Azure przy użyciu interfejsu wiersza polecenia Azure Service Fabric

Dowiedz się, jak tworzyć i usuwać aplikacje, które są uruchomione w klastrze usługi sieć szkieletowa usług Azure.

## <a name="prerequisites"></a>Wymagania wstępne

* Instalowanie usługi sieci szkieletowej interfejsu wiersza polecenia. Następnie wybierz klaster sieci szkieletowej usług. Aby uzyskać więcej informacji, zobacz [wprowadzenie do usługi sieci szkieletowej interfejsu wiersza polecenia](service-fabric-cli.md).

* Ma gotowa do wdrożenia pakietu aplikacji sieci szkieletowej usług. Aby uzyskać więcej informacji o sposobie pakiet aplikacji i autor, przeczytaj o [model aplikacji usługi sieć szkieletowa](service-fabric-application-model.md).

## <a name="overview"></a>Przegląd

Aby wdrożyć nową aplikację, wykonaj następujące kroki:

1. Przekaż pakiet aplikacji do magazynu obrazów platformy Service Fabric.
2. Udostępnianie typu aplikacji.
3. Usuń zawartość magazynu obrazów.
4. Określ i tworzenia aplikacji.
5. Określ i utworzyć usług.

Aby usunąć istniejącą aplikację, wykonaj następujące kroki:

1. Usuń aplikację.
2. Typ aplikacji skojarzone wstrzymał obsługi administracyjnej.

## <a name="deploy-a-new-application"></a>Wdrażanie nowej aplikacji

Aby wdrożyć nową aplikację, należy wykonać następujące zadania:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Przekaż nowy pakiet aplikacji do magazynu obrazów

Przed utworzeniem aplikacji, przekaż pakiet aplikacji do magazynu obrazów platformy Service Fabric.

Na przykład, jeśli pakiet aplikacji jest w `app_package_dir` katalogu, użyj następujących poleceń do przekazania katalogu:

```azurecli
sfctl application upload --path ~/app_package_dir
```

W przypadku dużych pakietów aplikacji, można określić `--show-progress` opcję, aby wyświetlić postęp przekazywania.

### <a name="provision-the-application-type"></a>Typ aplikacji do udostępniania

Po zakończeniu przekazywania udostępnienia aplikacji. Aby zainicjować obsługę aplikacji, użyj następującego polecenia:

```azurecli
sfctl application provision --application-type-build-path app_package_dir
```

Wartość `application-type-build-path` jest nazwę katalogu, w którym przekazać pakiet aplikacji.

### <a name="delete-the-application-package"></a>Usuwanie pakietu aplikacji

Zalecane jest, usuń pakiet aplikacji, po pomyślnym zarejestrowaniu aplikacji.  Usunięcie pakietów aplikacji w sklepie obrazu zwolni zasoby systemowe.  Utrzymywanie pakietów aplikacji nieużywane korzysta z magazynu danych na dysku i prowadzi do problemów z wydajnością aplikacji. 

Aby usunąć pakiet aplikacji w sklepie obrazu, użyj następującego polecenia:

```azurecli
sfctl store delete --content-path app_package_dir
```

`content-path` musi być nazwą katalogu, który został przekazany podczas tworzenia aplikacji.

### <a name="create-an-application-from-an-application-type"></a>Utworzenie aplikacji na podstawie typu aplikacji

Po udostępnieniem aplikacji użyj następującego polecenia, aby utworzyć aplikację:

```azurecli
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` jest to nazwa, który ma być używany dla danego wystąpienia aplikacji. W manifeście aplikacji poprzednio udostępnione można uzyskać dodatkowe parametry.

Nazwa aplikacji musi rozpoczynać się od prefiksu `fabric:/`.

### <a name="create-services-for-the-new-application"></a>Tworzenie usługi dla nowej aplikacji

Po utworzeniu aplikacji, należy utworzyć usług z aplikacji. W poniższym przykładzie utworzymy nowe usługi bezstanowej z naszej aplikacji. Usługi, które można utworzyć na podstawie aplikacji są definiowane w manifestu usługi, w pakiecie aplikacji wcześniej zainicjowana.

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

Aby sprawdzić, czy usługa jest w dobrej kondycji, należy użyć poleceń podobne do pobrania kondycji usługi i aplikacji:

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

### <a name="unprovision-the-application-type"></a>Cofnij aprowizację typu aplikacji

Po usunięciu aplikacji można anulować udostępnienia typ aplikacji, jeśli nie są już potrzebne. Wstrzymał obsługi administracyjnej typ aplikacji, użyj następującego polecenia:

```azurecli
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

Nazwa typu i wersji typu muszą być zgodne, nazwa i wersja w manifeście aplikacji wcześniej zainicjowana.

## <a name="upgrade-application"></a>Uaktualnianie aplikacji

Po utworzeniu aplikacji, można powtórzyć ten sam zestaw czynności, aby udostępnić drugi wersji aplikacji. Następnie Uaktualnianie aplikacji usługi Service Fabric może przejść do drugiego wersją aplikacji. Aby uzyskać więcej informacji, zobacz dokumentację na [uaktualnień aplikacji usługi sieć szkieletowa](service-fabric-application-upgrade.md).

Aby wykonać uaktualnienie, najpierw Zapewnij następnej wersji aplikacji, jak poprzednio przy użyciu tych samych poleceń:

```azurecli
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Zalecane jest następnie monitorowanych automatyczne uaktualnianie, uruchomić uaktualnienie, uruchamiając następujące polecenie:

```azurecli
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

Uaktualnienia zastąpienie istniejących parametrów z dowolnego zestaw jest określony. Parametry aplikacji powinien zostać przekazany jako argumenty do polecenia uaktualniania, w razie potrzeby. Parametry aplikacji powinien być kodowany jako obiekt JSON.

Aby pobrać wszystkie wcześniej określone parametry, można użyć `sfctl application info` polecenia.

Podczas uaktualniania aplikacji jest w toku, stan można pobrać przy użyciu `sfctl application upgrade-status` polecenia.

Ponadto jeśli uaktualnianie jest w toku i musi zostać anulowana, możesz użyć `sfctl application upgrade-rollback` można wycofać uaktualnienie.

## <a name="next-steps"></a>Kolejne kroki

* [Podstawy usługi sieci szkieletowej interfejsu wiersza polecenia](service-fabric-cli.md)
* [Wprowadzenie do korzystania z usługi Service Fabric w systemie Linux](service-fabric-get-started-linux.md)
* [Uruchamianie uaktualniania aplikacji sieci szkieletowej usług](service-fabric-application-upgrade.md)
