---
title: Tworzenie środowiska deweloperskiego Kubernetes w chmurze | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: ghogen
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: quickstart
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: 9c9a485a5c59342149027798e118b97b7305c640
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361536"
---
# <a name="quickstart-create-a-kubernetes-development-environment-with-azure-dev-spaces-nodejs"></a>Szybki start: tworzenie środowiska deweloperskiego Kubernetes za pomocą usługi Azure Dev Spaces (Node.js)


[!INCLUDE[](includes/learning-objectives.md)]

[!INCLUDE[](includes/see-troubleshooting.md)]

Teraz możesz przystąpić do tworzenia środowiska deweloperskiego Kubernetes na platformie Azure.

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="install-the-azure-cli"></a>Zainstaluj interfejs wiersza polecenia platformy Azure
Usługa Azure Dev Spaces wymaga minimalnej konfiguracji komputera lokalnego. Większość ustawień środowiska deweloperskiego jest przechowywana w chmurze i współdzielona z innymi użytkownikami. Zacznij od pobrania i uruchomienia [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). 

> [!IMPORTANT]
> Jeśli masz już zainstalowany interfejs wiersza polecenia platformy Azure, upewnij się, że używasz wersji 2.0.32 lub nowszej.

[!INCLUDE[](includes/sign-into-azure.md)]

[!INCLUDE[](includes/use-dev-spaces.md)]

[!INCLUDE[](includes/install-vscode-extension.md)]

Czekając na utworzenie środowiska, możesz zacząć pisać kod.

## <a name="create-a-nodejs-container-in-kubernetes"></a>Tworzenie kontenera Node.js w środowisku Kubernetes

W tej sekcji utworzysz aplikację internetową Node.js i uruchomisz ją w kontenerze w środowisku Kubernetes.

### <a name="create-a-nodejs-web-app"></a>Tworzenie aplikacji internetowej w technologii Node.js
Aby pobrać kod, w serwisie GitHub otwórz stronę https://github.com/Azure/dev-spaces i wybierz pozycję **Clone or Download** (Sklonuj lub pobierz). Repozytorium GitHub zostanie pobrane do środowiska lokalnego. Kod używany w tym przewodniku znajduje się w lokalizacji `samples/nodejs/getting-started/webfrontend`.

[!INCLUDE[](includes/azds-prep.md)]

[!INCLUDE[](includes/build-run-k8s-cli.md)]

### <a name="update-a-content-file"></a>Aktualizowanie pliku zawartości
Usługa Azure Dev Spaces umożliwia nie tylko uruchamianie kodu w środowisku Kubernetes — pozwala też szybko i wielokrotnie wyświetlać efekt zmian wprowadzonych w kodzie w środowisku Kubernetes w chmurze.

1. Znajdź plik `./public/index.html` i zmień kod HTML. Na przykład zmień kolor tła strony na odcień koloru niebieskiego:

    ```html
    <body style="background-color: #95B9C7; margin-left:10px; margin-right:10px;">
    ```

2. Zapisz plik. Po chwili w oknie terminalu pojawi się komunikat informujący o tym, że plik w uruchomionym kontenerze został zaktualizowany.
1. Otwórz przeglądarkę i odśwież stronę. Powinna być widoczna zmiana koloru.

Co się stało? Modyfikacje plików zawartości, takich jak HTML i CSS, nie wymagają ponownego uruchomienia procesu Node.js. Z tego względu aktywne polecenie `azds up` automatycznie synchronizuje wszystkie zmodyfikowane pliki zawartości bezpośrednio z uruchomionym kontenerem na platformie Azure, co umożliwia szybki podgląd zmian zawartości.

### <a name="test-from-a-mobile-device"></a>Testowanie na urządzeniu przenośnym
Interfejs użytkownika aplikacji internetowej nie jest poprawnie wyświetlany na ekranach małych na urządzeń przenośnych.

Aby rozwiązać ten problem, dodaj metatag `viewport`:
1. Otwórz plik `./public/index.html`.
1. Dodaj metatag `viewport` do istniejącego elementu `head`:

    ```html
    <head>
        <!-- Add this line -->
        <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    ```

1. Zapisz plik.
1. Odśwież przeglądarkę na urządzeniu. Teraz aplikacja internetowa powinna być wyświetlana poprawnie. 

Jak widać, niektóre problemy występują dopiero podczas testów na urządzeniach, dla których jest przeznaczona aplikacja. Rozwiązanie VS Azure Dev Spaces pozwala szybko modyfikować kod i weryfikować zmiany na urządzeniach docelowych.

### <a name="update-a-code-file"></a>Aktualizowanie pliku kodu
Aktualizacja plików kodu po stronie serwera wymaga nieco więcej pracy, ponieważ aplikacja Node.js musi zostać uruchomiona ponownie.

1. W oknie terminalu naciśnij klawisze `Ctrl+C`, aby zatrzymać polecenie `azds up`.
1. Otwórz plik kodu o nazwie `server.js` i zmodyfikuj komunikat powitalny usługi: 

    ```javascript
    res.send('Hello from webfrontend running in Azure!');
    ```

3. Zapisz plik.
1. Uruchom polecenie `azds up` w oknie terminalu. 

To spowoduje ponowne skompilowanie obrazu kontenera i ponowne wdrożenie planu narzędzia Helm. Załaduj ponownie stronę przeglądarki, aby zobaczyć efekt zmian w kodzie.

Oprócz tego dostępna jest jeszcze *szybsza metoda* opracowywania kodu. Omówimy ją w następnej sekcji. 

## <a name="debug-a-container-in-kubernetes"></a>Debugowanie kontenera w środowisku Kubernetes

[!INCLUDE[](includes/debug-intro.md)]

[!INCLUDE[](includes/init-debug-assets-vscode.md)]

### <a name="select-the-azds-debug-configuration"></a>Wybieranie konfiguracji debugowania w rozwiązaniu AZDS
1. Aby otworzyć widok debugowania, kliknij ikonę debugowania na **pasku działań** w bocznej części programu VS Code.
1. Wybierz pozycję **Launch Program (AZDS)** (Uruchom program [AZDS]) jako aktywną konfigurację debugowania.

![](media/get-started-node/debug-configuration-nodejs.png)

> [!Note]
> Jeśli na palecie poleceń nie widać żadnych poleceń usługi Azure Dev Spaces, upewnij się, że masz zainstalowane rozszerzenie VS Code dla usługi Azure Dev Spaces.

### <a name="debug-the-container-in-kubernetes"></a>Debugowanie kontenera w środowisku Kubernetes
Po naciśnięciu klawisza **F5** możesz debugować kod w środowisku Kubernetes.

Podobnie jak w przypadku polecenia `up` po rozpoczęciu debugowania kod jest synchronizowany ze środowiskiem deweloperskim, a utworzony kontener jest wdrażany w środowisku Kubernetes. Tym razem debuger jest dołączany do zdalnego kontenera.

[!INCLUDE[](includes/tip-vscode-status-bar-url.md)]

Ustaw punkt przerwania w pliku kodu po stronie serwera, na przykład w obrębie wywołania `app.get('/api'...` w pliku `server.js`. Odśwież stronę przeglądarki lub naciśnij przycisk „Say It Again” (Ponów). Powinno nastąpić zatrzymanie w punkcie przerwania, co umożliwia krokowe wykonywanie kodu.

Masz pełny dostęp do informacji debugowania, takich jak stos wywołań, zmienne lokalne, informacje o wyjątkach itd., zupełnie jakby kod był wykonywany lokalnie.

### <a name="edit-code-and-refresh-the-debug-session"></a>Edytowanie kodu i odświeżanie sesji debugowania
Przy aktywnym debugerze wprowadź zmianę w kodzie, na przykład ponownie zmodyfikuj komunikat powitalny:

```javascript
app.get('/api', function (req, res) {
    res.send('**** Hello from webfrontend running in Azure! ****');
});
```

Zapisz plik i w okienku **Debug actions** (Akcje debugowania) kliknij przycisk **Refresh** (Odśwież). 

![](media/get-started-node/debug-action-refresh-nodejs.png)

Zamiast ponownego kompilowania i wdrażania nowego obrazu kontenera przy każdej zmianie kodu, co często zajmuje dużo czasu, usługa Azure Dev Spaces ponownie uruchamia proces Node.js między sesjami debugowania, co przyspiesza działanie pętli edytowania/debugowania.

Odśwież aplikację internetową w przeglądarce lub naciśnij przycisk *Say It Again* (Ponów). Twój komunikat powinien zostać wyświetlony w interfejsie użytkownika.

### <a name="use-nodemon-to-develop-even-faster"></a>Jeszcze szybsze programowanie przy użyciu narzędzia NodeMon
*Nodemon* jest popularnym narzędziem, używanym przez deweloperów do szybkiego tworzenia aplikacji w środowisku Node.js. Zamiast ręcznie ponownie uruchamiać proces Node po każdej zmianie kodu po stronie serwera deweloperzy często tak konfigurują projekt Node, aby narzędzie *nodemon* monitorowało zmiany plików i automatycznie ponownie uruchamiało proces serwera. W tym stylu pracy deweloper tylko odświeża przeglądarkę po wprowadzeniu zmiany w kodzie.

Rozwiązanie Azure Dev Spaces pozwala korzystać z wielu przepływów pracy tworzenia oprogramowania używanych w środowisku lokalnym. Aby to zilustrować, przykładowy projekt `webfrontend` skonfigurowano pod kątem używania narzędzia *nodemon* (jest on skonfigurowany jako zależność programistyczna w pliku `package.json`).

Spróbuj wykonać następujące kroki:
1. Zatrzymaj działanie debugera VS Code.
1. Kliknij ikonę debugowania na **pasku działań** w bocznej części programu VS Code. 
1. Wybierz pozycję **Attach (AZDS)** (Dołącz [AZDS]) jako aktywną konfigurację debugowania.
1. Naciśnij klawisz F5.

W tej konfiguracji kontener uruchamia narzędzie *nodemon*. Po zmianie kodu po stronie serwera narzędzie *nodemon* automatycznie ponownie uruchamia proces Node, tak samo jak podczas programowania w środowisku lokalnym. 
1. Ponownie zmień komunikat powitalny w pliku `server.js` i zapisz ten plik.
1. Odśwież przeglądarkę lub kliknij przycisk *Say It Again* (Ponów), aby zobaczyć efekt wprowadzonych zmian.

**Umiesz już korzystać z metody szybkiego wprowadzania zmian w kodzie i debugowania bezpośrednio w środowisku Kubernetes.**

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Working with multiple containers and team development (Praca z wieloma kontenerami i programowanie zespołowe)](get-started-nodejs.md#call-a-service-running-in-a-separate-container)