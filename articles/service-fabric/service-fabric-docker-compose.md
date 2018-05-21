---
title: Usługa Azure sieci szkieletowej z rozwiązania Docker Compose Podgląd wdrożenia
description: Sieć szkieletowa usług Azure akceptuje rozwiązania Docker Compose format, aby ułatwić organizowania istniejących kontenerów przy użyciu sieci szkieletowej usług. Ta obsługa jest obecnie w przeglądzie.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 318898598617ba54b3f8f777bfeca0223d0d296c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Obsługa wdrażania Redaguj docker w sieci szkieletowej usług Azure (wersja zapoznawcza)

Używa docker [docker-compose.yml](https://docs.docker.com/compose) pliku do definiowania wielu kontenera aplikacji. Aby ułatwić klientom zapoznać się z rozwiązaniem Docker do organizowania istniejących aplikacji kontenera na sieć szkieletowa usług Azure, Przedstawiliśmy Podgląd obsługę wdrażania rozwiązania Docker Compose natywnie przez platformę. Sieć szkieletowa usług może zaakceptować wersji 3 lub nowszej `docker-compose.yml` plików. 

Ponieważ ta obsługa jest dostępna w wersji zapoznawczej, jest obsługiwana tylko podzestaw Redaguj dyrektywy. Na przykład nie są obsługiwane uaktualnienia aplikacji. Można zawsze usunąć i wdrażania aplikacji, a nie jej uaktualnienie.

Aby korzystać z tej wersji zapoznawczej, tworzenia klastra w wersji 5.7 lub nowszej środowiska uruchomieniowego platformy Service Fabric za pośrednictwem portalu Azure oraz odpowiedniego zestawu SDK. 

> [!NOTE]
> Ta funkcja jest w wersji zapoznawczej i nie jest obsługiwana w środowisku produkcyjnym.
> Poniższe przykłady są oparte na wersji środowiska wykonawczego w wersji 6.0 i zestawu SDK w wersji 2.8.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Wdrażanie rozwiązania Docker Compose pliku w sieci szkieletowej usług

Następujące polecenia Utwórz aplikację usługi sieć szkieletowa (o nazwie `fabric:/TestContainerApp`), które można monitorować i zarządzać, takich jak innych aplikacji sieci szkieletowej usług. Określona nazwa aplikacji dla zapytań o kondycję można użyć.
Sieci szkieletowej usług "DeploymentName" jest rozpoznawany jako identyfikator wdrożenia tworzenia.

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Utwórz wdrożenie usługi sieć szkieletowa tworzą z plik docker-compose.yml, uruchamiając następujące polecenie w programie PowerShell:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` i `RegistryPassword` odwoływać się do kontenera rejestru nazwy i hasła użytkownika. Po zakończeniu wdrożenia jej stan można sprawdzić za pomocą następującego polecenia:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

Aby usunąć tworzenia wdrożenia za pomocą programu PowerShell, użyj następującego polecenia:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

Aby uruchomić uaktualniania wdrożenia Redaguj, za pomocą programu PowerShell, użyj następującego polecenia:

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

Po zaakceptowaniu uaktualnienia postęp uaktualnienia można śledzić za pomocą następującego polecenia:

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -Deployment TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Użyj usługi sieci szkieletowej interfejsu wiersza polecenia Azure (sfctl)

Można również można użyć następującego polecenia interfejsu wiersza polecenia usługi sieci szkieletowej:

```azurecli
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

Po utworzeniu wdrożenia jej stan można sprawdzić za pomocą następującego polecenia:

```azurecli
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Aby usunąć tworzenia wdrożenia, użyj następującego polecenia:

```azurecli
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Można uruchomić uaktualnienia wdrożenia Redaguj, użyj następującego polecenia:

```azurecli
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

Po zaakceptowaniu uaktualnienia postęp uaktualnienia można śledzić za pomocą następującego polecenia:

```azurecli
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Obsługiwane dyrektywy tworzenia

Ta wersja zapoznawcza obsługuje podzbiór z formatu Redaguj w wersji 3, w tym następujące elementy podstawowe opcje konfiguracji:

* Usługi > Wdrażanie > repliki
* Usługi > Wdrażanie > umieszczania > ograniczenia
* Usługi > Wdrażanie > Zasoby > limity
    * udziały procesorów
    * -pamięci
    * -pamięci-wymiany
* Usługi > poleceń
* Usługi > środowiska
* Usługi > portów
* Usługi > obrazu
* Usługi > izolację (tylko system Windows)
* Usługi > Rejestrowanie > sterownika
* Usługi > Rejestrowanie > sterownika > Opcje
* Wolumin & wdrażanie > woluminu

Konfigurowanie klastra na potrzeby wymuszania ograniczenia zasobów, zgodnie z opisem w [ładu zasobów sieci szkieletowej usług](service-fabric-resource-governance.md). Inne rozwiązania Docker Compose dyrektywy nie są obsługiwane dla tej wersji zapoznawczej.

## <a name="servicednsname-computation"></a>ServiceDnsName obliczeń

Jeśli nazwa usługi określona w pliku tworzenia jest w pełni kwalifikowaną nazwą domeny (to znaczy zawiera kropkę [.]), nazwa DNS zarejestrowane przez sieć szkieletowa usług to `<ServiceName>` (w tym kropki (.)). Jeśli nie, każdy z segmentów ścieżki w nazwie aplikacji staje się etykieta domeny, w polu Nazwa DNS usługi z pierwszy segment ścieżki, staje się etykieta domeny najwyższego poziomu.

Na przykład, jeśli określona nazwa aplikacji jest `fabric:/SampleApp/MyComposeApp`, `<ServiceName>.MyComposeApp.SampleApp` byłoby zarejestrowanej nazwy DNS.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Utworzenie wdrożenia (wystąpienie definicji) i sieci szkieletowej usług app model (definicja typu)

Plik docker-compose.yml opisuje zestaw możliwych do wdrożenia kontenerów, łącznie z ich właściwości i konfiguracji.
Na przykład plik może zawierać zmienne środowiskowe i portów. Można również określić parametry wdrażania, takie jak ograniczenia dotyczące umieszczania, limity zasobów i nazwy DNS w plik docker-compose.yml.

[Model aplikacji usługi sieć szkieletowa](service-fabric-application-model.md) używa usługi typy i aplikacji, gdzie masz wiele wystąpień aplikacji tego samego typu. Na przykład można mieć jedno wystąpienie aplikacji na klienta. Ten model na podstawie typu obsługuje wiele wersji tego samego typu aplikacji, który został zarejestrowany za pomocą środowiska uruchomieniowego.

Na przykład klient A może mieć utworzyć wystąpienia typu 1.0 AppTypeA aplikacji, a klient B może mieć inną aplikację utworzone z tego samego typu i wersji. Definiowanie typów aplikacji w manifestach aplikacji i określić parametry nazwy i wdrażania aplikacji, podczas tworzenia aplikacji.

Chociaż ten model zapewnia elastyczność, będziemy również planowania obsługi modelu wdrażania prostszy, na podstawie wystąpienia, których typy są niejawne z pliku manifestu. W tym modelu każda aplikacja uzyskuje niezależne manifeście. Firma Microsoft przeglądania tym wysiłku przez dodanie obsługi docker-compose.yml, który jest formatem wdrożenia na podstawie wystąpienia.

## <a name="next-steps"></a>Kolejne kroki

* Przeczytaj na [modelu aplikacji sieci szkieletowej usług](service-fabric-application-model.md)
* [Get started with Service Fabric CLI](service-fabric-cli.md) (Wprowadzenie do interfejsu wiersza polecenia usługi Service Fabric)
