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
description: Szybkie opracowywanie Kubernetes z kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, usługa Azure Kubernetes, kontenerów
manager: douge
ms.openlocfilehash: 371bb9195266f3511d115de2532e6b64f49ef26f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów

Ten przewodnik zawiera informacje o typowych problemów, które mogą mieć używając spacji deweloperów usługi Azure.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Błąd "powyżej błąd połączenia lub odłączyć/reset przed nagłówki"
Ten błąd może zostać wyświetlony podczas próby uzyskania dostępu do usługi. Na przykład po przejściu do adresu URL usługi w przeglądarce. 

### <a name="reason"></a>Przyczyna 
Port kontenera nie jest dostępna. Może to być spowodowane: 
* Kontener nadal trwa są wbudowane i wdrożony. Może to mieć miejsce, jeśli uruchomisz `azds up` lub uruchomienia debugera, a następnie spróbuj dostęp kontenera, zanim została pomyślnie wdrożona.
* Konfiguracja portów nie jest spójna z plik Dockerfile, wykres Helm i każdy kod serwera, który zostaje otwarty port.

### <a name="try"></a>Wypróbuj:
1. Jeśli kontener jest w trakcie wbudowane/wdrażany, możesz poczekaj 2-3 sekundy i spróbuj ponownie uzyskać dostęp do usługi. 
1. Sprawdź konfigurację portu. Numery portów określonej powinna być **identyczne** w poniższych zasobów:
    * **Plik Dockerfile:** określony przez `EXPOSE` instrukcji.
    * **[Wykres Helm](https://docs.helm.sh):** określony przez `externalPort` i `internalPort` wartości dla usługi (często znajduje się w `values.yml` pliku),
    * Wszystkie porty są otwarte w kodzie aplikacji, na przykład w środowisku Node.js: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Nie znaleziono pliku konfiguracji
Uruchomienie `azds up` i występuje następujący błąd: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Przyczyna
Należy uruchomić `azds up` z katalogu głównego kodu chcesz uruchomić, a musi inicjować folder kodu do uruchomienia spacjami deweloperów platformy Azure.

### <a name="try"></a>Wypróbuj:
1. Zmień bieżący katalog na folder główny zawierający kodu usługi. 
1. Jeśli nie ma pliku azds.yaml w folderze kodu, uruchom `azds prep` do generowania zasobów Docker, Kubernetes i spacje deweloperów platformy Azure.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Błąd: "potoku program został nieoczekiwanie zakończony z kodem 126" azds"."
Uruchamianie debugera kodzie VS czasami może spowodować błąd. Jest to znany problem.

### <a name="try"></a>Wypróbuj:
1. Zamknij i otwórz ponownie kodzie VS.
2. Ponownie naciśnij klawisz F5.


## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>Debugowanie błędów "skonfigurowana debugowania"środowisko coreclr"nie obsługuje typu"
Uruchomienie debugera kodzie VS zgłasza błąd: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Przyczyna
Nie ma rozszerzenia kodzie VS platformy Azure deweloperów spacje zainstalowane na komputerze deweloperskim.

### <a name="try"></a>Wypróbuj:
Zainstaluj [kodzie VS rozszerzenie Azure deweloperów spacji](get-started-netcore.md).

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>Nie można odnaleźć nazwy typu lub przestrzeni nazw "MojaBiblioteka"

### <a name="reason"></a>Przyczyna 
Kontekst kompilacji jest domyślnie na poziomie projektu/usługi, w związku z tym projektu biblioteki, którego używasz nie można odnaleźć.

### <a name="try"></a>Wypróbuj:
Co trzeba zrobić:
1. Zmodyfikuj azds.yaml Ustaw kontekst kompilacji do poziomu rozwiązania.
2. Zmodyfikuj plik Dockerfile i Dockerfile.develop pliki do odwoływania się do plików csproj poprawnie, względem nowy kontekst kompilacji.
3. Umieść plik .dockerignore obok pliku sln i modyfikować w razie potrzeby.

Na przykład można znaleźć https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftconnectedenvironmentregisteraction-authorization-error"></a>Błąd autoryzacji "Microsoft.ConnectedEnvironment/register/action"
Może pojawić następujący błąd, gdy są zarządzanie przestrzenią deweloperów platformy Azure i korzystania z subskrypcji platformy Azure, dla którego nie ma właściciela lub współautora dostępu
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.ConnectedEnvironment/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Przyczyna
Wybrana subskrypcja platformy Azure nie został zarejestrowany Microsoft.ConnectedEnvironment przestrzeni nazw.

### <a name="try"></a>Wypróbuj:
Właścicielem lub współautorem dostęp do subskrypcji platformy Azure można uruchomić następujące polecenie wiersza polecenia platformy Azure, aby ręcznie zarejestrować Microsoft.ConnectedEnvironment przestrzeni nazw:

```cmd
az provider register --namespace Microsoft.ConnectedEnvironment
```

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Spacje deweloperów platformy Azure nie powiodła się Mój istniejący plik Dockerfile umożliwia tworzenie kontenera 

### <a name="reason"></a>Przyczyna
Spacje deweloperów platformy Azure można skonfigurować wskaż plik Dockerfile określonych w projekcie. Wygląda na to, czy spacje deweloperów Azure nie jest używany plik Dockerfile spodziewasz się tworzenie kontenerów, może być konieczne jawnie stwierdzić, gdzie jest spacje deweloperów usługi Azure. 

### <a name="try"></a>Wypróbuj:
Otwórz `azds.yaml` pliku, który został wygenerowany przez Azure deweloperów spacje w projekcie. Użyj `configurations->develop->build->dockerfile` dyrektywy wskaż plik Dockerfile, którego chcesz użyć:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```