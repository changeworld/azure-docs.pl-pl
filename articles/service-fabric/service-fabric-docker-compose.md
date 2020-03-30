---
title: Wersja dorozjudowa usługi Azure Service Fabric Comone Deployment Preview
description: Usługa Azure Service Fabric akceptuje format docker compose, aby ułatwić organizowanie istniejących kontenerów przy użyciu sieci szkieletowej usług. Ta obsługa jest obecnie w wersji zapoznawczej.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: f84dd0ecb7a4002182c8455bfd86354d794a6f7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282460"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Obsługa wdrażania tworzenia docker w sieci szkieletowej usługi Azure (wersja zapoznawcza)

Docker używa pliku [docker-compose.yml](https://docs.docker.com/compose) do definiowania aplikacji z wieloma kontenerami. Aby ułatwić klientom zaznajomionym z platformą Docker organizowanie istniejących aplikacji kontenerów w sieci szkieletowej usług Azure, uwzględniliśmy obsługę w wersji zapoznawczej wdrożenia docker compose natywnie na platformie. Usługa Sieci szkieletowej może akceptować pliki w wersji 3 i nowszej. `docker-compose.yml` 

Ponieważ ta obsługa jest w wersji zapoznawczej, tylko podzbiór compose dyrektyw jest obsługiwany.

Aby użyć tej wersji zapoznawczej, utwórz klaster w wersji 5.7 lub większej środowiska uruchomieniowego sieci szkieletowej usług za pośrednictwem witryny Azure portal wraz z odpowiednim zestawem SDK. 

> [!NOTE]
> Ta funkcja jest w wersji zapoznawczej i nie jest obsługiwana w produkcji.
> Poniższe przykłady są oparte na wersji 6.0 środowiska wykonawczego i SDK w wersji 2.8.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Wdrażanie pliku compose docker w sieci szkieletowej usług

Następujące polecenia tworzą aplikację sieci szkieletowej usług (o nazwie `fabric:/TestContainerApp`), którą można monitorować i zarządzać jak każda inna aplikacja sieci szkieletowej usług. Można użyć określonej nazwy aplikacji dla kwerend dotyczących kondycji.
Sieci szkieletowej usług rozpoznaje "DeploymentName" jako identyfikator wdrożenia compose.

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Utwórz wdrożenie redagowania sieci szkieletowej usług z pliku docker-compose.yml, uruchamiając następujące polecenie w programie PowerShell:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName`i `RegistryPassword` odnoszą się do nazwy użytkownika i hasła rejestru kontenerów. Po zakończeniu wdrożenia można sprawdzić jego stan za pomocą następującego polecenia:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

Aby usunąć wdrożenie Redpose za pośrednictwem programu PowerShell, użyj następującego polecenia:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

Aby rozpocząć uaktualnianie wdrożenia compose za pośrednictwem programu PowerShell, użyj następującego polecenia:

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

Aby wycofać uaktualnienie wdrożenia compose za pośrednictwem programu PowerShell, należy użyć następującego polecenia:

```powershell
Start-ServiceFabricComposeDeploymentRollback -DeploymentName TestContainerApp
```

Po zaakceptowaniu uaktualnienia postęp uaktualnienia można śledzić za pomocą następującego polecenia:

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Użyj interfejsu wiersza polecenia sieci szkieletowej usługi Azure (sfctl)

Alternatywnie można użyć następującego polecenia interfejsu wiersza polecenia sieci szkieletowej usług:

```shell
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

Po utworzeniu wdrożenia można sprawdzić jego stan za pomocą następującego polecenia:

```shell
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Aby usunąć wdrożenie Redpose, użyj następującego polecenia:

```shell
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Aby rozpocząć uaktualnienie wdrażania compose, użyj następującego polecenia:

```shell
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

Aby wycofać uaktualnienie wdrożenia compose, należy użyć następującego polecenia:

```shell
sfctl compose upgrade-rollback --deployment-name TestContainerApp [ --timeout ]
```

Po zaakceptowaniu uaktualnienia postęp uaktualnienia można śledzić za pomocą następującego polecenia:

```shell
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Obsługiwane dyrektywy redagowania

Ten podgląd obsługuje podzbiór opcji konfiguracji z formatu Compose w wersji 3, w tym następujące elementy podstawowe:

* Usługi > wdrażania replik >
* > wdrażania > > usług
* Usługi > wdrażanie > limitów > zasobów
    * -cpu-udziały
    * -pamięć
    * -zamiana pamięci
* Usługi > polecenia
* Usługi > środowisko
* Usługi > porty
* Obraz > usług
* Usługi > izolacji (tylko dla systemu Windows)
* Sterownik > rejestrowania usług >
* Usługi > opcje > > > sterowników
* Wolumin & wdrożenia > woluminu

Skonfiguruj klaster do wymuszania limitów zasobów, zgodnie z opisem w [zarządzania zasobami sieci szkieletowej usług](service-fabric-resource-governance.md). Wszystkie inne dyrektywy dokowania są nieobsługiwały dla tej wersji zapoznawczej.

### <a name="ports-section"></a>Sekcja Porty

Określ protokół http lub https w sekcji Porty, który będzie używany przez odbiornik usług sieci szkieletowej usług. Zapewni to, że protokół punktu końcowego jest poprawnie opublikowany za pomocą usługi nazewnictwa, aby umożliwić odwrotny serwer proxy do przesyłania dalej żądań:
* Aby przekierować do niezabezpieczonych usług redagowania sieci szkieletowej usług, należy określić **/http**. Na przykład - **"80:80/http"**.
* Aby trasować do bezpiecznego usługi redagowania sieci szkieletowej usług, należy określić **/https**. Na przykład - **"443:443/https"**.

> [!NOTE]
> Składnia sekcji /http i /https Ports jest specyficzna dla sieci szkieletowej usług w celu zarejestrowania prawidłowego adresu URL odbiornika sieci szkieletowej usług.  Jeśli składnia pliku docker jest programowo sprawdzana, może to spowodować błąd sprawdzania poprawności.

## <a name="servicednsname-computation"></a>Obliczenia ServiceDnsName

Jeśli nazwa usługi określona w pliku Redpose jest w pełni kwalifikowaną nazwą domeny (oznacza to, że zawiera `<ServiceName>` kropkę [.]), nazwa DNS zarejestrowana przez sieci szkieletowej usług jest (w tym kropka). Jeśli nie, każdy segment ścieżki w nazwie aplikacji staje się etykietą domeny w nazwie DNS usługi, a pierwszy segment ścieżki staje się etykietą domeny najwyższego poziomu.

Na przykład, jeśli określona `fabric:/SampleApp/MyComposeApp` `<ServiceName>.MyComposeApp.SampleApp` nazwa aplikacji jest , będzie zarejestrowana nazwa DNS.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Redagowanie wdrożenia (definicja wystąpienia) w porównaniu z modelem aplikacji sieci szkieletowej usług (definicja typu)

Plik docker-compose.yml opisuje wdrażalny zestaw kontenerów, w tym ich właściwości i konfiguracje.
Na przykład plik może zawierać zmienne środowiskowe i porty. W pliku docker-compose.yml można również określić parametry wdrożenia, takie jak ograniczenia umieszczania, limity zasobów i nazwy DNS.

[Model aplikacji sieci szkieletowej usług](service-fabric-application-model.md) używa typów usług i typów aplikacji, gdzie można mieć wiele wystąpień aplikacji tego samego typu. Na przykład można mieć jedno wystąpienie aplikacji na klienta. Ten model oparty na typie obsługuje wiele wersji tego samego typu aplikacji, który jest zarejestrowany w czasie wykonywania.

Na przykład klient A może mieć wystąpienia aplikacji z typem 1.0 AppTypeA, a klient B może mieć inną aplikację wystąpienia z tego samego typu i wersji. Typy aplikacji w manifestach aplikacji i określić nazwę aplikacji i parametry wdrożenia podczas tworzenia aplikacji.

Mimo że ten model oferuje elastyczność, planujemy również obsługiwać prostszy, oparty na wystąpieniu model wdrażania, w którym typy są niejawne z pliku manifestu. W tym modelu każda aplikacja pobiera swój własny niezależny manifest. Wyświetlamy podgląd tego wysiłku, dodając obsługę docker-compose.yml, który jest formatem wdrażania opartym na wystąpieniu.

## <a name="next-steps"></a>Następne kroki

* Przeczytaj o [modelu aplikacji sieci szkieletowej usług](service-fabric-application-model.md)
* [Get started with Service Fabric CLI](service-fabric-cli.md) (Wprowadzenie do interfejsu wiersza polecenia usługi Service Fabric)
