---
title: Wdrażanie istniejącego pliku wykonywalnego na sieć szkieletowa usług Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o pakowania istniejącą aplikację jako gość pliku wykonywalnego, dlatego może on zostać wdrożony do klastra usługi sieć szkieletowa usług.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/15/2018
ms.author: mfussell
ms.openlocfilehash: cdaf3dae12c2c9da1f6bcbebbff560b98e62bade
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Wdrażanie istniejącego pliku wykonywalnego sieci szkieletowej usług
Możesz uruchomić dowolnego typu kodu, np. Node.js, Java lub C++ w sieci szkieletowej usług Azure, jako usługa. Sieć szkieletowa usług odwołuje się do tych typów usług jako pliki wykonywalne gościa.

Pliki wykonywalne gościa są traktowane przez sieć szkieletowa usług takich jak usług bezstanowych. W związku z tym są umieszczane w węzłach w klastrze, w oparciu o dostępności i inne metryki. W tym artykule opisano pakietu i wdrażanie pliku wykonywalnego gościa do klastra usługi sieć szkieletowa usług za pomocą programu Visual Studio lub narzędzia wiersza polecenia.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Zalety uruchamiania Gość pliku wykonywalnego w sieci szkieletowej usług
Ma kilka zalet do uruchomienia pliku wykonywalnego w klastrze usługi sieć szkieletowa Gość:

* Wysoka dostępność. Aplikacje uruchamiane w sieci szkieletowej usług są zyskuje dużą dostępność. Sieć szkieletowa usług zapewnia uruchomionych wystąpień aplikacji.
* Monitorowanie kondycji. Monitorowanie kondycji sieci szkieletowej usług wykrywa, czy aplikacja działa i udostępnia informacje diagnostyczne, w przypadku awarii.   
* Zarządzanie cyklem życia aplikacji. Oprócz zapewnienia uaktualnienia bez przestojów, sieci szkieletowej usług umożliwia automatyczne przywrócenie poprzedniej wersji, jeśli istnieje zdarzenie kondycji zły zgłoszone podczas uaktualniania.    
* Gęstości. Wiele aplikacji można uruchomić w klastrze, która eliminuje potrzebę dla każdej aplikacji do uruchamiania na jego własnej sprzętu.
* Odnajdowanie: Za pomocą usługi REST można wywołać usługi Usługa nazewnictwa sieci szkieletowej, aby znaleźć inne usługi w klastrze. 

## <a name="samples"></a>Przykłady
* [Przykład dla pakowanie i wdrażanie pliku wykonywalnego gościa](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Przykład dwóch gościa pliki wykonywalne (C# i nodejs) podczas komunikacji za pośrednictwem usługi nazw za pomocą usługi REST](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Omówienie aplikacji i pliki manifestu usługi
W ramach wdrażania pliku wykonywalnego gościa, warto poznać modelu pakowania i wdrażania usługi sieć szkieletowa usług, zgodnie z opisem w [model aplikacji](service-fabric-application-model.md). Model pakowania sieci szkieletowej usług opiera się na dwa pliki XML: manifestów aplikacji i usług. Definicja schematu dla pliku ApplicationManifest.xml i ServiceManifest.xml plików jest instalowany z zestawu SDK sieci szkieletowej usług w *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Manifest aplikacji** manifest aplikacji jest używany do opisu aplikacji. Wyświetla listę usług, które go tworzą i innych parametrów, które są używane do definiowania sposobu co najmniej jednej usługi powinny zostać wdrożone, takie jak liczba wystąpień.

  W sieci szkieletowej usług aplikacji jest jednostką wdrożenia i uaktualnienia. Aplikację można aktualizować jako pojedyncza jednostka, w której zarządzane potencjalnych awarii i wycofywanie potencjalne zmian. Sieć szkieletowa usług gwarantuje, że proces uaktualniania jest pomyślnie, lub Jeśli uaktualnienie nie powiedzie się, nie pozostawi aplikacji w stan nieznany lub niestabilny.
* **Manifest usługi** manifestu usługi opisano składniki usługi. Zawiera dane, takie jak nazwa i typ usługi, kodu i konfiguracji. Manifest usługi zawiera również pewne dodatkowe parametry, których można użyć do skonfigurowania usługi, po wdrożeniu.

## <a name="application-package-file-structure"></a>Struktura pliku pakietu aplikacji
Aby wdrożyć aplikację sieci szkieletowej usług, aplikacji powinien być zgodny struktury katalogów wstępnie zdefiniowane. Oto przykład tej struktury.

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

ApplicationPackageRoot zawiera pliku ApplicationManifest.xml, który definiuje aplikacji. Podkatalog dla poszczególnych usług zawartych w aplikacji jest używany do zawierają wszystkie artefakty wymagane przez usługę. Te podkatalogi są ServiceManifest.xml i zwykle następujące:

* *Kod*. Ten katalog zawiera kodu usługi.
* *Config*. Ten katalog zawiera pliku Settings.xml (i innych plików, jeśli to konieczne) czy usługa może uzyskać dostęp w czasie wykonywania można pobrać ustawień określonej konfiguracji.
* *Dane*. Jest to dodatkowe katalog do przechowywania dodatkowych danych lokalnych, które usługa może być konieczne. Danych powinna być używana do przechowywania tylko danych tymczasowych. Sieć szkieletowa usług nie skopiować lub replikować zmiany do katalogu danych, jeśli usługi musi być przemieszczane (na przykład w trybie failover).

> [!NOTE]
> Nie trzeba tworzyć `config` i `data` katalogów, jeśli nie są potrzebne.
>
>

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły dla zadania i powiązane informacje.
* [Wdrażanie aplikacji wykonywalnej gościa](service-fabric-deploy-existing-app.md)
* [Wdrażanie wielu aplikacji wykonywalnych gości](service-fabric-deploy-multiple-apps.md)
* [Tworzenie pierwszej aplikacji pliku wykonywalnego gościa za pomocą programu Visual Studio](quickstart-guest-app.md)
* [Przykład dla pakowanie i wdrażanie pliku wykonywalnego gościa](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), łącznie z łączem do wstępną narzędzia pakowania
* [Przykład dwóch gościa pliki wykonywalne (C# i nodejs) podczas komunikacji za pośrednictwem usługi nazw za pomocą usługi REST](https://github.com/Azure-Samples/service-fabric-containers)

