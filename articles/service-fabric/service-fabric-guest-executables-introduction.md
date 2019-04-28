---
title: Wdrażanie istniejącego pliku wykonywalnego w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o pakowanie istniejącą aplikację jako plik wykonywalny gościa, aby można było wdrożyć w klastrze usługi Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/15/2018
ms.author: aljo
ms.openlocfilehash: b7efeb1b4d83f6a6b372f73a7c0a5ca9bffdc052
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946675"
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Wdrażanie istniejącego pliku wykonywalnego w usłudze Service Fabric
Dowolnego typu kodu, takich jak Node.js, Java lub C++ w usłudze Azure Service Fabric można uruchomić jako usługę. Usługa Service Fabric odnosi się do tych typów usług jako plików wykonywalnych gościa.

Pliki wykonywalne gościa są traktowane przez usługę Service Fabric, takich jak usługi bezstanowej. W rezultacie są umieszczane w węzłach w klastrze, na podstawie dostępności i innych metryk. W tym artykule opisano jak pakowanie i wdrażanie pliku wykonywalnego gościa w klastrze usługi Service Fabric przy użyciu programu Visual Studio lub narzędzie wiersza polecenia.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Korzyści wynikające z uruchamiania pliku wykonywalnego w usłudze Service Fabric gościa
Ma kilka zalet do uruchomionego pliku wykonywalnego w klastrze usługi Service Fabric gościa:

* Wysoka dostępność. Aplikacje, które są uruchamiane w usłudze Service Fabric są udostępniane bardzo. Usługi Service Fabric zapewnia, że są uruchomione wystąpienia aplikacji.
* Monitorowanie kondycji. Monitorowanie kondycji usługi Service Fabric wykrywa, czy aplikacja działa i udostępnia informacje diagnostyczne, jeśli wystąpi awaria.   
* Zarządzanie cyklem życia aplikacji. Oprócz podawania uaktualnienia bez przestojów, Usługa Service Fabric zapewnia automatyczne przywrócenie poprzedniej wersji, jeśli istnieje zdarzenie kondycji zły zgłoszone podczas uaktualniania.    
* Gęstość. Możesz uruchomić wiele aplikacji w klastrze, co eliminuje potrzebę stosowania każdej aplikacji uruchamianych na sprzęcie swój własny.
* Odnajdowanie: Przy użyciu interfejsu REST możesz wywołać usługi nazewnictwa Service Fabric, aby znaleźć inne usługi w klastrze. 

## <a name="samples"></a>Przykłady
* [Przykład pakowanie i wdrażanie wykonywalnej gościa](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Przykład dwóch gościa pliki wykonywalne (C# i nodejs) podczas komunikowania się za pośrednictwem usługi nazewnictwa przy użyciu usługi REST](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Omówienie aplikacji i plików manifestu usługi
W ramach wdrażania wykonywalnej gościa, jest grupowaniu można sprawdzić modelu pakowaniem i wdrażaniem usługi Service Fabric, zgodnie z opisem w [model aplikacji](service-fabric-application-model.md). Model tworzenia pakietów usługi Service Fabric opiera się na dwóch plikach XML: manifestów aplikacji i usługi. Definicja schematu dla plików ApplicationManifest.xml i ServiceManifest.xml został zainstalowany przy użyciu zestawu Service Fabric SDK do *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Manifest aplikacji** manifest aplikacji jest używany do opisu aplikacji. Wyświetla listę usług, które go tworzą i innych parametrów, które są używane do definiowania sposobu co najmniej jednej usługi powinny być wdrażane, takie jak liczba wystąpień.

  W usłudze Service Fabric aplikacja jest jednostką wdrażanie i uaktualnianie. Można uaktualnić aplikację jako pojedyncza jednostka, w której zarządzane potencjalnych awarii i wycofywanie zmian, potencjalne. Usługa Service Fabric gwarantuje, że proces uaktualniania jest operacja się powiedzie, lub, jeśli uaktualnienie nie powiedzie się, nie należy pozostawić aplikacji w stan nieznany lub niestabilny.
* **Manifest usługi** manifestu usługi zawiera opis składników usługi. Obejmuje to dane, takie jak nazwa i typ usługi, a jej kodem i konfiguracją. Manifestu usługi zawiera również pewne dodatkowe parametry, które może służyć do konfigurowania usługi po jej wdrożeniu.

## <a name="application-package-file-structure"></a>Struktura pliku pakietu aplikacji
Aby wdrożyć aplikację w usłudze Service Fabric, aplikacji powinien być zgodny strukturę katalogów wstępnie zdefiniowane. Oto przykład tej struktury.

```
|-- ApplicationPackageRoot
    |-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```

ApplicationPackageRoot zawiera plik ApplicationManifest.xml, który definiuje aplikacji. Podkatalog dla poszczególnych usług zawartych w aplikacji jest używany do zawierają wszystkie artefakty, których wymaga usługi. Te podkatalogi są ServiceManifest.xml i zazwyczaj następujące czynności:

* *Kod*. Ten katalog zawiera w kodzie usługi.
* *Config*. Ten katalog zawiera plik Settings.xml (i innych plików, jeśli to konieczne), usługa dostęp w czasie wykonywania do pobrania określonych ustawień konfiguracji.
* *Dane*. Jest to kolejnego katalogu do przechowywania dodatkowych danych lokalnych, które usługa może być konieczne. Dane powinny używane do przechowywania tylko danych tymczasowych. Usługa Service Fabric skopiuj lub nie replikować zmiany do katalogu danych, jeśli usługa musi ulec zmianie (na przykład podczas trybu failover).

> [!NOTE]
> Nie ma konieczności tworzenia `config` i `data` katalogów, jeśli nie są potrzebne.
>
>

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły, aby uzyskać powiązane informacje i zadania.
* [Wdrażanie aplikacji wykonywalnej gościa](service-fabric-deploy-existing-app.md)
* [Wdrażanie wielu aplikacji wykonywalnych gości](service-fabric-deploy-multiple-apps.md)
* [Tworzenie pierwszej aplikacji wykonywalnych gościa za pomocą programu Visual Studio](quickstart-guest-app.md)
* [Przykład pakowanie i wdrażanie wykonywalnej gościa](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), łącznie z łączem do wstępną narzędzia pakowania
* [Przykład dwóch gościa pliki wykonywalne (C# i nodejs) podczas komunikowania się za pośrednictwem usługi nazewnictwa przy użyciu usługi REST](https://github.com/Azure-Samples/service-fabric-containers)

