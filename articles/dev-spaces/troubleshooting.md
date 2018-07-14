---
title: Rozwiązywanie problemów z | Dokumentacja firmy Microsoft
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: fdf195d96bb455334cb4e898e560813ee8709a50
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035656"
---
# <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów

Ten przewodnik zawiera informacje o typowych problemów, które mogą mieć w przypadku korzystania z usługi Azure Dev miejsca do magazynowania.

## <a name="error-service-cannot-be-started"></a>Błąd "nie można uruchomić usługi."

Ten błąd może pojawić się po kodzie usługi nie powiedzie się. Przyczyną jest często w kodzie użytkownika. Aby uzyskać więcej informacji diagnostycznych, należy wprowadzić następujące zmiany w Twoich poleceń i ustawień:

W wierszu polecenia:

1. Korzystając z _azds.exe_, użyj--verbose opcji wiersza polecenia i użyj opcji wiersza polecenia dane wyjściowe, aby określić format danych wyjściowych.
 
    ```cmd
    azds up --verbose --output json
    ```

W programie Visual Studio:

1. Otwórz **Narzędzia > Opcje** i w obszarze **projekty i rozwiązania**, wybierz i **kompilowanie i uruchamianie**.
2. Zmień ustawienia **poziom szczegółowości danych wyjściowych kompilacji projektu programu MSBuild** do **szczegółowe** lub **diagnostycznych**.

    ![Zrzut ekranu opcji narzędzi okna dialogowego](media/common/VerbositySetting.PNG)

## <a name="error-required-tools-and-configurations-are-missing"></a>Błąd "Wymagane narzędzia i konfiguracje są niedostępne"

Ten błąd może wystąpić przy uruchamianiu programu VS Code: "[Azure Dev miejsca do magazynowania] narzędzi i konfiguracji, aby kompilować i debugować,"[Nazwa projektu]"Brak wymaganych."
Ten błąd oznacza, że tego azds.exe nie znajduje się w zmiennej środowiskowej PATH, jak pokazano w programie VS Code.

### <a name="try"></a>Wypróbuj:

Uruchom program VS Code z poziomu wiersza polecenia, w którym zmiennej środowiskowej PATH jest prawidłowo.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Błąd "nadrzędne błąd połączenia lub odłącz/reset przed nagłówki"
Podczas próby uzyskania dostępu do usługi, zostanie wyświetlony ten błąd. Na przykład, gdy nastąpi przejście do adresu URL usługi, w przeglądarce. 

### <a name="reason"></a>Przyczyna 
Port kontenera nie jest dostępna. Ten problem może wystąpić, ponieważ: 
* Kontener trwa nadal trwa utworzeniu i wdrożeniu. Ten problem może wystąpić, jeśli uruchomisz `azds up` lub uruchom debuger, a następnie spróbuj dostępu do kontenera zanim została pomyślnie wdrożona.
* Konfiguracja portów nie jest spójność w ramach usługi _pliku Dockerfile_, narzędzia Helm i wszelki kod serwera, który otwiera port.

### <a name="try"></a>Wypróbuj:
1. Jeśli kontener jest właśnie utworzone/wdrażane, możesz odczekaj 2 – 3 sekund, a następnie spróbuj ponownie uzyskać dostęp do usługi. 
1. Sprawdź konfigurację portu. Numery określony port powinien być **identyczne** w poniższych zasobów:
    * **Plik Dockerfile:** określony przez `EXPOSE` instrukcji.
    * **[Narzędzia Helm](https://docs.helm.sh):** określony przez `externalPort` i `internalPort` wartości dla usługi (często znajduje się w `values.yml` pliku),
    * Wszystkie porty są otwarte w kodzie aplikacji, na przykład w środowisku Node.js: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Nie można odnaleźć pliku konfiguracji
Możesz uruchomić `azds up` i występuje następujący błąd: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Przyczyna
Należy uruchomić `azds up` z katalogu głównego kodu chcesz uruchomić, a następnie należy zainicjować folder kodu do uruchomienia przy użyciu usługi Azure Dev miejsca do magazynowania.

### <a name="try"></a>Wypróbuj:
1. Zmień bieżący katalog do folderu głównego zawierającego kod w języku usługi. 
1. Jeśli nie masz _azds.yaml_ pliku w folderze kodu uruchamiania `azds prep` wygenerować zasoby platformy Docker, Kubernetes i usługi Azure Dev miejsca do magazynowania.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Błąd: "programu potoku"azds"nieoczekiwanie zakończył działanie z kodem 126."
Uruchamianie debugera programu VS Code czasami może spowodować błąd. Jest to znany problem.

### <a name="try"></a>Wypróbuj:
1. Zamknij i otwórz ponownie program VS Code.
2. Ponownie naciśnij klawisz F5.


## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>Błąd debugowania "skonfigurowane debugowania typu"coreclr"nie jest obsługiwany"
Uruchamianie debugera programu VS Code zgłasza błąd: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Przyczyna
Nie masz rozszerzenie programu VS Code Azure Dev spacje zainstalowany na komputerze deweloperskim.

### <a name="try"></a>Wypróbuj:
Zainstaluj [rozszerzenie programu VS Code dla usługi Azure Dev miejsca do magazynowania](get-started-netcore.md).

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>Nie można odnaleźć nazwy typu lub przestrzeni nazw "MojaBiblioteka"

### <a name="reason"></a>Przyczyna 
Kontekst kompilacji jest domyślnie na poziomie projektu/usługi, w związku z tym projektu biblioteki, którego używasz nie będzie można znaleźć.

### <a name="try"></a>Wypróbuj:
Co trzeba zrobić:
1. Modyfikowanie _azds.yaml_ pliku, aby ustawić kontekstu kompilacji na poziomie rozwiązania.
2. Modyfikowanie _pliku Dockerfile_ i _Dockerfile.develop_ pliki do odwoływania się do projektu (_.csproj_) pliki poprawnie, względem nowej kompilacji kontekstu.
3. Miejsce _.dockerignore_ pliku obok pliku .sln i modyfikować zgodnie z potrzebami.

Na przykład można znaleźć https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>Błąd autoryzacji "Microsoft.DevSpaces/register/action"
Może być wyświetlony następujący błąd, gdy są zarządzanie przestrzenią deweloperów platformy Azure i pracy z subskrypcją platformy Azure, dla której nie masz dostęp współautora lub właściciela.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Przyczyna
Wybranej subskrypcji platformy Azure nie został zarejestrowany `Microsoft.DevSpaces` przestrzeni nazw.

### <a name="try"></a>Wypróbuj:
Dostęp właściciela lub współautora do subskrypcji platformy Azure można uruchomić następujące polecenie z wiersza polecenia platformy Azure, aby ręcznie zarejestrować `Microsoft.DevSpaces` przestrzeni nazw:

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Usługa Azure Dev spacje wydają się użyć Mój istniejący plik Dockerfile do utworzenia kontenera 

### <a name="reason"></a>Przyczyna
Azure Dev do magazynowania można skonfigurować, aby wskazać konkretną _pliku Dockerfile_ w projekcie. Jeśli wydaje się nie używa usługi Azure Dev miejsca do magazynowania _pliku Dockerfile_ oczekujesz, że tworzenie kontenerów, konieczne może poinformować usługi Azure Dev miejsca do magazynowania, gdzie jest jawnie. 

### <a name="try"></a>Wypróbuj:
Otwórz _azds.yaml_ pliku, który został wygenerowany przez usługi Azure Dev miejsca do magazynowania w projekcie. Użyj `configurations->develop->build->dockerfile` dyrektywy wskaż plik Dockerfile, którego chcesz użyć:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```