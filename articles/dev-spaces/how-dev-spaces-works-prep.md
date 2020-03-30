---
title: Jak działa przygotowanie projektu dla usługi Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: W tym artykule opisano, jak działa przygotowanie projektu za pomocą usługi Azure Dev Spaces
keywords: azds.yaml, Usługi Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontenery
ms.openlocfilehash: 24a54fffdc8e94493d2a4a9aeb1c5f02dcd192b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241636"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>Jak działa przygotowanie projektu dla usługi Azure Dev Spaces

Usługa Azure Dev Spaces oferuje wiele sposobów szybkiego iteracji i debugowania aplikacji Kubernetes oraz współpracy z zespołem nad klastrem usługi Azure Kubernetes (AKS). Przestrzenie deweloperów mogą generować dockerfiles i wykresy helm dla projektu. Dev Spaces tworzy również i używa pliku konfiguracji do wdrażania, uruchamiania i debugowania aplikacji Kubernetes w uzywu usługi AKS. Wszystkie te pliki znajdują się z kodem aplikacji i mogą być dodawane do systemu kontroli wersji.

W tym artykule opisano, co się dzieje, aby przygotować projekt do uruchomienia w usłudze AKS z dev spaces.

## <a name="prepare-your-code"></a>Przygotuj swój kod

Aby uruchomić aplikację w przestrzeni deweloperów, musi być konteneryzowany i należy zdefiniować, jak należy wdrożyć w usłudze Kubernetes. Aby konteneryzować aplikację, potrzebujesz pliku Dockerfile. Aby zdefiniować sposób wdrażania aplikacji w u użytkownika Kubernetes, potrzebny jest [wykres Helm](https://docs.helm.sh/). Aby pomóc w tworzeniu wykresu Dockerfile i Helm dla aplikacji, `prep` narzędzia po stronie klienta zapewniają polecenie:

```cmd
azds prep --enable-ingress
```

Polecenie `prep` będzie przeglądać pliki w projekcie i spróbuj utworzyć wykres Dockerfile i Helm do uruchamiania aplikacji w usłudze Kubernetes. Obecnie polecenie `prep` wygeneruje wykres Dockerfile i Helm z następującymi językami:

* Java
* Node.js
* .NET Core

*Należy* uruchomić `prep` polecenie z katalogu zawierającego kod źródłowy. Uruchamianie `prep` polecenia z poprawnego katalogu umożliwia narzędzi po stronie klienta do identyfikowania języka i utworzyć odpowiedni plik Dockerfile do konteneryzacji aplikacji. Można również uruchomić `prep` polecenie z katalogu zawierającego plik *pom.xml* dla projektów Java.

Jeśli `prep` polecenie zostanie uruchomione z katalogu, który nie zawiera kodu źródłowego, narzędzie po stronie klienta nie wygeneruje pliku Dockerfile. Pojawi się również komunikat o błędzie: *Nie można wygenerować pliku dockerfile z powodu nieobsługiwałego języka.* Ten błąd występuje również wtedy, gdy narzędzia po stronie klienta nie rozpoznają typu projektu.

Po uruchomieniu `prep` polecenia można określić flagę. `--enable-ingress` Ta flaga informuje kontrolera, aby utworzyć punkt końcowy dostępny dla Internetu dla tej usługi. Jeśli ta flaga nie zostanie określona, usługa będzie dostępna tylko z poziomu klastra lub tunelu hosta lokalnego utworzonego przez narzędzie po stronie klienta. To zachowanie można włączyć lub `prep` wyłączyć po uruchomieniu polecenia, aktualizując wygenerowany wykres Helm.

Polecenie `prep` nie zastąpi żadnych istniejących dockerfiles lub wykresy helm masz w projekcie. Jeśli istniejący wykres Dockerfile lub Helm używa tej samej `prep` konwencji nazewnictwa, co pliki generowane przez polecenie, `prep` polecenie pominie generowanie tych plików. W przeciwnym `prep` razie polecenie wygeneruje własny wykres Dockerfile lub Helm wraz z istniejącymi plikami.

> [!IMPORTANT]
> Usługa Azure Dev Spaces używa wykresu Dockerfile i Helm dla projektu do tworzenia i uruchamiania kodu, ale można zmodyfikować te pliki, jeśli chcesz zmienić sposób tworzenia i uruchamiania projektu.

Polecenie `prep` spowoduje również `azds.yaml` wygenerowanie pliku w katalogu głównym projektu. Usługa Azure Dev Spaces używa tego pliku do tworzenia, instalowania, konfigurowania i uruchamiania aplikacji. Ten plik konfiguracyjny zawiera listę lokalizacji wykresu Dockerfile i Helm, a także zapewnia dodatkową konfigurację na wierzchu tych artefaktów.

Oto przykład pliku azds.yaml utworzonego przy użyciu [przykładowej aplikacji .NET Core:](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend)

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
        # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
        # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
        # For more information see https://aka.ms/devspaces/routing
        - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
```

Plik `azds.yaml` wygenerowany `prep` przez polecenie jest przeznaczony do pracy dla prostego, pojedynczego scenariusza rozwoju projektu. Jeśli określony projekt ma zwiększoną złożoność, może być konieczne `prep` zaktualizowanie tego pliku po uruchomieniu polecenia. Na przykład projekt może wymagać pewnych zmian w procesie kompilacji lub uruchamiania na podstawie potrzeb deweloperskich lub debugowania. W projekcie może być również wiele aplikacji, które wymagają wielu procesów kompilacji lub innej zawartości kompilacji.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat uruchamiania kodu w obszarze deweloperskim, zobacz [Jak działa uruchamianie kodu za pomocą usługi Azure Dev Spaces.][how-it-works-up]

Aby rozpocząć korzystanie z usługi Azure Dev Spaces w celu przygotowania projektu dla obszaru deweloperów platformy Azure, zobacz następujące przewodniki Szybki start:

* [Szybka iteracji i debugowania z Visual Studio Code i Java][quickstart-java]
* [Szybko iterować i debugować za pomocą programu Visual Studio Code i .NET][quickstart-netcore]
* [Szybka iteracji i debugowania z Visual Studio Code i Node.js][quickstart-node]
* [Szybkie iterowanie i debugowanie za pomocą programów Visual Studio i .NET Core][quickstart-vs]
* [Używanie interfejsu wiersza polecenia do tworzenia aplikacji w ud.][quickstart-cli]

[how-it-works-up]: how-dev-spaces-works-up.md
[quickstart-cli]: quickstart-cli.md
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md