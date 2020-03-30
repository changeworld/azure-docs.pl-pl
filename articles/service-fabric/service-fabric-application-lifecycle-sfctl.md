---
title: Zarządzanie aplikacjami sieci szkieletowej usług Azure przy użyciu sfctl
description: Dowiedz się, jak wdrażać i usuwać aplikacje z klastra sieci szkieletowej usług Azure przy użyciu interfejsu wiersza polecenia sieci szkieletowej usługi Azure
author: Christina-Kang
ms.topic: conceptual
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 7d361d44c349bc7a6e3c041f78d00ad66182fa15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259073"
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli-sfctl"></a>Zarządzanie aplikacją sieci szkieletowej usług Azure przy użyciu interfejsu wiersza polecenia sieci szkieletowej usługi Azure (sfctl)

Dowiedz się, jak tworzyć i usuwać aplikacje uruchomione w klastrze sieci szkieletowej usług Azure.

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj interfejsu wiersza polecenia sieci szkieletowej usług. Następnie wybierz klaster sieci szkieletowej usług. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do interfejsu wiersza polecenia sieci szkieletowej usług](service-fabric-cli.md).

* Przygotuj pakiet aplikacji sieci szkieletowej usług do wdrożenia. Aby uzyskać więcej informacji na temat tworzenia i pakowania aplikacji, przeczytaj o [modelu aplikacji sieci szkieletowej usług](service-fabric-application-model.md).

## <a name="overview"></a>Omówienie

Aby wdrożyć nową aplikację, wykonaj następujące kroki:

1. Przekaż pakiet aplikacji do magazynu obrazów sieci szkieletowej usług.
2. Aprowizuj typ aplikacji.
3. Usuń zawartość magazynu obrazów.
4. Określ i utwórz aplikację.
5. Określ i utwórz usługi.

Aby usunąć istniejącą aplikację, wykonaj następujące kroki:

1. Usuń aplikację.
2. Anulowanie obsługi administracyjnej skojarzonego typu aplikacji.

## <a name="deploy-a-new-application"></a>Wdrażanie nowej aplikacji

Aby wdrożyć nową aplikację, wykonaj następujące zadania:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Przekazywanie nowego pakietu aplikacji do magazynu obrazów

Przed utworzeniem aplikacji należy przekazać pakiet aplikacji do magazynu obrazów sieci szkieletowej usług.

Na przykład jeśli pakiet aplikacji `app_package_dir` znajduje się w katalogu, użyj następujących poleceń, aby przekazać katalog:

```shell
sfctl application upload --path ~/app_package_dir
```

W przypadku dużych pakietów `--show-progress` aplikacji można określić opcję wyświetlania postępu przekazywania.

### <a name="provision-the-application-type"></a>Aprowizuj typ aplikacji

Po zakończeniu przekazywania aprowizować aplikację. Aby aprowizować aplikację, należy użyć następującego polecenia:

```shell
sfctl application provision --application-type-build-path app_package_dir
```

Wartość jest `application-type-build-path` nazwa katalogu, w którym przesłano pakiet aplikacji.

### <a name="delete-the-application-package"></a>Usuwanie pakietu aplikacji

Zaleca się usunięcie pakietu aplikacji po pomyślnym zarejestrowaniu aplikacji.  Usuwanie pakietów aplikacji z magazynu obrazów zwalnia zasoby systemowe.  Utrzymywanie nieużywanych pakietów aplikacji zużywa magazyn dysku i prowadzi do problemów z wydajnością aplikacji. 

Aby usunąć pakiet aplikacji ze sklepu z obrazami, użyj następującego polecenia:

```shell
sfctl store delete --content-path app_package_dir
```

`content-path`musi być nazwą katalogu przekazanego podczas tworzenia aplikacji.

### <a name="create-an-application-from-an-application-type"></a>Tworzenie aplikacji na podstawie typu aplikacji

Po zainicjowaniu aprowizacji aplikacji użyj następującego polecenia, aby nazwać i utworzyć aplikację:

```shell
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name`to nazwa, której chcesz użyć dla wystąpienia aplikacji. Można uzyskać dodatkowe parametry z wcześniej aprowizacji manifestu aplikacji.

Nazwa aplikacji musi zaczynać `fabric:/`się od prefiksu .

### <a name="create-services-for-the-new-application"></a>Tworzenie usług dla nowej aplikacji

Po utworzeniu aplikacji należy utworzyć usługi z aplikacji. W poniższym przykładzie tworzymy nową usługę bezstanową z naszej aplikacji. Usługi, które można utworzyć z aplikacji są zdefiniowane w manifeście usługi w wcześniej aprowizowanym pakiecie aplikacji.

```shell
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Weryfikowanie wdrażania i kondycji aplikacji

Aby sprawdzić, czy wszystko jest w dobrej kondycji, użyj następujących poleceń kondycji:

```shell
sfctl application list
sfctl service list --application-id TestApp
```

Aby sprawdzić, czy usługa jest w dobrej kondycji, należy użyć podobnych poleceń, aby pobrać kondycję zarówno usługi, jak i aplikacji:

```shell
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

Zdrowe usługi i `HealthState` aplikacje `Ok`mają wartość .

## <a name="remove-an-existing-application"></a>Usuwanie istniejącej aplikacji

Aby usunąć aplikację, wykonaj następujące zadania:

### <a name="delete-the-application"></a>Usuwanie aplikacji

Aby usunąć aplikację, użyj następującego polecenia:

```shell
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Anulowanie obsługi administracyjnej typu aplikacji

Po usunięciu aplikacji można anulować aprowizję typu aplikacji, jeśli nie jest już potrzebny. Aby anulować aprowizję typu aplikacji, użyj następującego polecenia:

```shell
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

Nazwa typu i wersja typu musi być zgodna z nazwą i wersją w wcześniej zainicjowanym manifeście aplikacji.

## <a name="upgrade-application"></a>Uaktualnianie aplikacji

Po utworzeniu aplikacji można powtórzyć ten sam zestaw kroków, aby aprowizować drugą wersję aplikacji. Następnie za pomocą uaktualnienia aplikacji sieci szkieletowej usług można przejść do uruchamiania drugiej wersji aplikacji. Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [uaktualnień aplikacji sieci szkieletowej usług](service-fabric-application-upgrade.md).

Aby wykonać uaktualnienie, najpierw aprowizować następną wersję aplikacji przy użyciu tych samych poleceń, co poprzednio:

```shell
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Zaleca się, aby następnie wykonać monitorowaną aktualizację automatyczną, uruchomić uaktualnienie, uruchamiając następujące polecenie:

```shell
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

Uaktualnienia zastępują istniejące parametry niezależnie od określonego zestawu. Parametry aplikacji powinny być przekazywane jako argumenty do polecenia uaktualnienia, jeśli to konieczne. Parametry aplikacji powinny być zakodowane jako obiekt JSON.

Aby pobrać wszystkie parametry wcześniej określone, `sfctl application info` można użyć polecenia.

Gdy uaktualnienie aplikacji jest w toku, stan `sfctl application upgrade-status` można pobrać za pomocą polecenia.

Na koniec, jeśli uaktualnienie jest w toku i musi `sfctl application upgrade-rollback` zostać anulowane, można użyć do wycofania uaktualnienia.

## <a name="next-steps"></a>Następne kroki

* [Podstawowe informacje o interfejsu wiersza sieci szkieletowej usług](service-fabric-cli.md)
* [Wprowadzenie do sieci szkieletowej usług w systemie Linux](service-fabric-get-started-linux.md)
* [Uruchamianie uaktualnienia aplikacji sieci szkieletowej usług](service-fabric-application-upgrade.md)
