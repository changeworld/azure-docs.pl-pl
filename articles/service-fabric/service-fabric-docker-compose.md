---
title: Usługa Azure Service Fabric Docker Compose wdrożenia (wersja zapoznawcza)
description: Usługa Azure Service Fabric akceptuje format narzędzia Docker Compose, aby ułatwić organizowanie istniejących kontenerów za pomocą usługi Service Fabric. Ta funkcja jest obecnie w wersji zapoznawczej.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: aljo, subramar
ms.openlocfilehash: da86ed9a3e6979bd1dc05aef6ef70c7b8533a8c1
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661398"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Obsługa wdrażania Compose platformy docker w usłudze Azure Service Fabric (wersja zapoznawcza)

Platforma docker korzysta [docker-compose.yml](https://docs.docker.com/compose) definiowania wielokontenerowych aplikacji w pliku. Aby ułatwić klientom zapoznać się z platformy Docker do organizowania istniejących aplikacji kontenerowych w usłudze Azure Service Fabric, wprowadzono obsługę narzędzia Docker Compose wdrożenia w wersji zapoznawczej natywnie na platformie. Usługa Service Fabric może akceptować wersji 3 i nowszych `docker-compose.yml` plików. 

Ponieważ ta obsługa jest dostępna w wersji zapoznawczej, jest obsługiwane tylko podzbiór dyrektyw narzędzia. Na przykład nie są obsługiwane uaktualnienia aplikacji. Można zawsze usunąć i wdrażania aplikacji, zamiast uaktualniać ich.

Aby użyć tej wersji zapoznawczej, tworzenie klastra przy użyciu wersji 5.7 lub nowszej środowiska uruchomieniowego usługi Service Fabric w witrynie Azure portal oraz odpowiedniego zestawu SDK. 

> [!NOTE]
> Ta funkcja jest dostępna w wersji zapoznawczej i nie jest obsługiwana w środowisku produkcyjnym.
> Poniższe przykłady są oparte na wersji środowiska uruchomieniowego w wersji 6.0 i zestaw SDK w wersji 2.8.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Wdróż plik narzędzia Docker Compose w usłudze Service Fabric

Następujące polecenia tworzą aplikację usługi Service Fabric (o nazwie `fabric:/TestContainerApp`), które można monitorować i zarządzać jak wszystkie inne aplikacje usługi Service Fabric. Można użyć nazwy określonej aplikacji dla zapytań o kondycję.
Usługa Service Fabric "DeploymentName" jest rozpoznawany jako identyfikator wdrożenia Compose.

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Utwórz wdrożenie usługi Service Fabric Compose z pliku docker-compose.yml, uruchamiając następujące polecenie w programie PowerShell:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` i `RegistryPassword` odnoszą się do nazwy rejestru kontenerów i hasła użytkownika. Po zakończeniu wdrożenia jej stan można sprawdzić za pomocą następującego polecenia:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

Aby usunąć wdrożenia Compose za pomocą programu PowerShell, użyj następującego polecenia:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

Aby rozpocząć uaktualnianie wdrożenia Compose, za pomocą programu PowerShell, użyj następującego polecenia:

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

Można wycofać wdrożenie redagowania uaktualnianie za pomocą programu PowerShell, użyj następującego polecenia:

```powershell
Start-ServiceFabricComposeDeploymentRollback -DeploymentName TestContainerApp
```

Po zaakceptowaniu uaktualnienia postęp uaktualniania można można śledzić przy użyciu następującego polecenia:

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Użyj wiersza polecenia platformy Azure usługi Service Fabric (sfctl)

Alternatywnie można użyć następującego polecenia interfejsu wiersza polecenia usługi Service Fabric:

```azurecli
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

Po utworzeniu wdrożenia jej stan można sprawdzić za pomocą następującego polecenia:

```azurecli
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Aby usunąć wdrożenia Compose, użyj następującego polecenia:

```azurecli
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Aby rozpocząć uaktualnianie wdrożenia Compose, użyj następującego polecenia:

```azurecli
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

Można wycofać wdrożenie redagowania uaktualnienie, użyj następującego polecenia:

```azurecli
sfctl compose upgrade-rollback --deployment-name TestContainerApp [ --timeout ]
```

Po zaakceptowaniu uaktualnienia postęp uaktualniania można można śledzić przy użyciu następującego polecenia:

```azurecli
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Obsługiwane dyrektyw narzędzia

Ta wersja zapoznawcza obsługuje podzbiór opcje konfiguracji z formatu Compose w wersji 3, w tym następujących elementów podstawowych:

* Usługi > Wdrażanie > repliki
* Usługi > Wdrażanie > umieszczania > ograniczenia
* Usługi > Wdrażanie > Zasoby > limitów
    * udziały - procesor cpu
    * -pamięć
    * -memory-swap
* Usługi > poleceń
* Usługi > środowisko
* Usługi > portów
* Usługi > obrazu
* Usługi > izolację (tylko Windows)
* Usługi > Rejestrowanie > sterownika
* Usługi > Rejestrowanie > sterownika > Opcje
* Wolumin & wdrażanie > woluminu

Konfigurowanie klastra na potrzeby wymuszania limitów zasobów zgodnie z opisem w [zarządzanie zasobami usługi Service Fabric](service-fabric-resource-governance.md). Inne narzędzia Docker Compose dyrektywy nie są obsługiwane dla tej wersji zapoznawczej.

### <a name="ports-section"></a>Sekcji dotyczącej portów

W sekcji dotyczącej portów, który będzie używany przez odbiornik usługi Service Fabric, należy określić protokół http lub https. Daje to pewność, że protokół punkt końcowy został poprawnie opublikowany za pomocą usługi nazewnictwa, aby zezwolić na zwrotny serwer proxy do przekazywania żądań:
* Aby przekierowywanie do niezabezpieczonych usługi Service Fabric Compose, należy określić **/http**. Na przykład- **"80:80 / http"**.
* Aby rozesłać do bezpiecznych usług usługi Service Fabric Compose, należy określić **/https**. Na przykład- **"443:443 / https"**.

> [!NOTE]
> Składnia sekcji /http i porty /https jest specyficzne dla usługi Service Fabric, aby zarejestrować poprawny adres URL odbiornik usługi Service Fabric.  Jeśli narzędzia Docker compose programowego sprawdzania poprawności składni pliku, może to spowodować błąd sprawdzania poprawności.

## <a name="servicednsname-computation"></a>Obliczenie ServiceDnsName

Jeśli nazwa usługi określona w pliku Compose jest w pełni kwalifikowanej nazwy domeny (czyli zawiera pojedynczego znaku kropki [.]), nazwa DNS zarejestrowana przez usługę Service Fabric jest `<ServiceName>` (w tym kropki (.)). W przeciwnym razie każdy segment ścieżki w nazwie aplikacji staje się etykiety domeny w polu Nazwa DNS usługi za pomocą pierwszy segment ścieżki, staje się etykiety domeny najwyższego poziomu.

Na przykład, jeśli określona nazwa aplikacji jest `fabric:/SampleApp/MyComposeApp`, `<ServiceName>.MyComposeApp.SampleApp` będzie zarejestrowanej nazwy DNS.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Wdrażanie (definicja wystąpienia) redagowania i modelu aplikacji usługi Service Fabric (definicja typu)

Plik docker-compose.yml w tym artykule opisano zestaw możliwych do wdrożenia kontenerów, w tym ich właściwości i konfiguracji.
Na przykład plik może zawierać zmienne środowiskowe i portów. Parametry wdrożenia, takie jak ograniczeniami dotyczącymi umieszczania, limity zasobów i nazwy DNS, można również określić w pliku docker-compose.yml.

[Modelu aplikacji usługi Service Fabric](service-fabric-application-model.md) używa usługi typów i typów aplikacji, w którym masz wiele wystąpień aplikacji tego samego typu. Na przykład można mieć jedno wystąpienie aplikacji na klienta. Ten model na podstawie typu obsługiwanych wiele wersji tego samego typu aplikacji, która jest zarejestrowana w środowisku uruchomieniowym.

Na przykład klient element może mieć utworzone za pomocą typu 1.0 AppTypeA aplikacji, a klient B może mieć inną aplikację utworzone za pomocą tego samego typu i wersji. Definiowanie typów aplikacji w manifestach aplikacji, a następnie określ parametry name i wdrażania aplikacji, podczas tworzenia aplikacji.

Mimo że ten model zapewnia elastyczność, planujemy również obsługiwać model wdrożenia prostszy, oparta na wystąpieniach, gdzie typy są niejawne z pliku manifestu. W tym modelu poszczególne aplikacje uzyskują niezależnych manifest. Firma Microsoft udostępnia w wersji zapoznawczej ten nakład pracy, dodając obsługę platformy docker-compose.yml, czyli na format wdrożenie oparte na wystąpienie.

## <a name="next-steps"></a>Kolejne kroki

* Czytaj [modelu aplikacji usługi Service Fabric](service-fabric-application-model.md)
* [Get started with Service Fabric CLI](service-fabric-cli.md) (Wprowadzenie do interfejsu wiersza polecenia usługi Service Fabric)
