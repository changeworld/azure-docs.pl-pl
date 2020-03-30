---
title: Pakowanie istniejącego pliku wykonywalnego do sieci szkieletowej usług Azure
description: Dowiedz się więcej o pakowaniu istniejącej aplikacji jako pliku wykonywalnego gościa, dzięki czemu można ją wdrożyć w klastrze sieci szkieletowej usług.
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 3d7aab28a32effa2caf7b04b830d72e5e3dfda56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457833"
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Wdrażanie istniejącego pliku wykonywalnego w sieci szkieletowej usług
Można uruchomić dowolnego typu kodu, takich jak Node.js, Java lub C++ w usłudze Azure Service Fabric jako usługi. Sieć szkieletowa usług odnosi się do tego typu usług jako pliki wykonywalne gościa.

Pliki wykonywalne gościa są traktowane przez sieć szkieletową usług jak usługi bezstanowe. W rezultacie są one umieszczane w węzłach w klastrze, na podstawie dostępności i innych metryk. W tym artykule opisano sposób pakowania i wdrażania pliku wykonywalnego gościa w klastrze sieci szkieletowej usług przy użyciu programu Visual Studio lub narzędzia wiersza polecenia.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Korzyści z uruchamiania pliku wykonywalnego gościa w sieci szkieletowej usług
Istnieje kilka zalet uruchamiania pliku wykonywalnego gościa w klastrze sieci szkieletowej usług:

* Wysoka dostępność. Aplikacje uruchamiane w sieci szkieletowej usług są udostępniane jako wysoce dostępne. Sieci szkieletowej usług zapewnia, że wystąpienia aplikacji są uruchomione.
* Monitorowanie stanu zdrowia. Monitorowanie kondycji sieci szkieletowej usługi wykrywa, czy aplikacja jest uruchomiona i dostarcza informacji diagnostycznych, jeśli wystąpi błąd.   
* Zarządzanie cyklem życia aplikacji. Oprócz zapewnienia uaktualnień bez przestojów, usługa Service Fabric zapewnia automatyczne wycofywanie do poprzedniej wersji, jeśli podczas uaktualniania jest zgłaszane zdarzenie dotyczące złego stanu zdrowia.    
* Gęstość. Można uruchomić wiele aplikacji w klastrze, co eliminuje potrzebę uruchamiania każdej aplikacji na własnym sprzęcie.
* Wykrywalność: Za pomocą REST można wywołać usługę nazewnictwa sieci szkieletowej usług, aby znaleźć inne usługi w klastrze. 

## <a name="samples"></a>Samples
* [Przykład do pakowania i wdrażania pliku wykonywalnego gościa](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Próbka dwóch plików wykonywalnych gościa (C# i nodejs) komunikujących się za pośrednictwem usługi nazewnictwa przy użyciu rest](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Omówienie plików manifestów aplikacji i usług
W ramach wdrażania pliku wykonywalnego gościa warto zrozumieć model pakowania i wdrażania sieci szkieletowej usług, jak opisano w [modelu aplikacji.](service-fabric-application-model.md) Model pakowania sieci szkieletowej usług opiera się na dwóch plikach XML: manifestach aplikacji i usługi. Definicja schematu plików ApplicationManifest.xml i ServiceManifest.xml jest instalowana przy sdk sieci szkieletowej usług w *języku C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Manifest aplikacji** Manifest aplikacji jest używany do opisania aplikacji. Wyświetla listę usług, które tworzą go i inne parametry, które są używane do definiowania, jak jeden lub więcej usług powinny być wdrożone, takich jak liczba wystąpień.

  W sieci szkieletowej usług aplikacja jest jednostką wdrażania i uaktualniania. Aplikację można uaktualnić jako pojedynczą jednostkę, w której zarządzane są potencjalne awarie i potencjalne wycofywanie. Sieci szkieletowej usług gwarantuje, że proces uaktualniania zakończy się pomyślnie lub, jeśli uaktualnienie nie powiedzie się, nie pozostawia aplikacji w stanie nieznanym lub niestabilnym.
* **Manifest usługi** Manifest usługi opisuje składniki usługi. Zawiera dane, takie jak nazwa i typ usługi oraz jego kod i konfiguracja. Manifest usługi zawiera również niektóre dodatkowe parametry, które mogą służyć do konfigurowania usługi po jej wdrożeniu.

## <a name="application-package-file-structure"></a>Struktura pliku pakietu aplikacji
Aby wdrożyć aplikację do sieci szkieletowej usług, aplikacja powinna być zgodna ze wstępnie zdefiniowaną strukturą katalogów. Poniżej przedstawiono przykład tej struktury.

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

ApplicationPackageRoot zawiera plik ApplicationManifest.xml, który definiuje aplikację. Podkatalog dla każdej usługi zawartej w aplikacji jest używany do zawierania wszystkich artefaktów, które wymaga usługi. Te podkatalogi są ServiceManifest.xml i, zazwyczaj, następujące:

* *Kod*. Ten katalog zawiera kod usługi.
* *Config*. Ten katalog zawiera plik Settings.xml (i inne pliki w razie potrzeby), do których usługa może uzyskać dostęp w czasie wykonywania w celu pobrania określonych ustawień konfiguracji.
* *Dane*. Jest to dodatkowy katalog do przechowywania dodatkowych danych lokalnych, które mogą być potrzebne usługi. Dane powinny być używane do przechowywania tylko danych tymczasowych. Sieci szkieletowej usług nie kopiuje ani nie replikuje zmian w katalogu danych, jeśli usługa musi zostać przeniesiona (na przykład podczas pracy awaryjnej).

> [!NOTE]
> Nie musisz tworzyć `config` katalogów, `data` jeśli ich nie potrzebujesz.
>
>

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły, aby uzyskać powiązane informacje i zadania.
* [Wdrażanie aplikacji wykonywalnej gościa](service-fabric-deploy-existing-app.md)
* [Wdrażanie wielu aplikacji wykonywalnych gości](service-fabric-deploy-multiple-apps.md)
* [Tworzenie pierwszej aplikacji wykonywalnej gościa przy użyciu programu Visual Studio](quickstart-guest-app.md)
* [Przykład do pakowania i wdrażania pliku wykonywalnego gościa,](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)w tym link do wstępnej wersji narzędzia do pakowania
* [Próbka dwóch plików wykonywalnych gościa (C# i nodejs) komunikujących się za pośrednictwem usługi nazewnictwa przy użyciu rest](https://github.com/Azure-Samples/service-fabric-containers)

