---
title: Tworzenie obszaru deweloperskiego Kubernetes w chmurze przy użyciu platformy .NET Core i programu VS Code | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 07/09/2018
ms.topic: tutorial
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: 4da5b42ddd235fa26834e582a911140116692d34
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38299563"
---
# <a name="get-started-on-azure-dev-spaces-with-net-core"></a>Rozpoczęcie pracy w usłudze Azure Dev Spaces za pomocą platformy .NET Core

[!INCLUDE[](includes/learning-objectives.md)]

[!INCLUDE[](includes/see-troubleshooting.md)]

Teraz możesz przystąpić do tworzenia obszaru deweloperskiego Kubernetes na platformie Azure.

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="install-the-azure-cli"></a>Zainstaluj interfejs wiersza polecenia platformy Azure
Usługa Azure Dev Spaces wymaga minimalnej konfiguracji komputera lokalnego. Większość ustawień obszaru deweloperskiego jest przechowywana w chmurze i udostępniana innym użytkownikom. Zacznij od pobrania i uruchomienia [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). 

> [!IMPORTANT]
> Jeśli masz już zainstalowany interfejs wiersza polecenia platformy Azure, upewnij się, że używasz wersji 2.0.38 lub nowszej.

[!INCLUDE[](includes/sign-into-azure.md)]

[!INCLUDE[](includes/use-dev-spaces.md)]

[!INCLUDE[](includes/install-vscode-extension.md)]

Czekając na utworzenie klastra, możesz zacząć pisać kod.

## <a name="create-a-web-app-running-in-a-container"></a>Tworzenie aplikacji internetowej uruchomionej w kontenerze

W tej sekcji utworzysz aplikację internetową ASP.NET Core i uruchomisz ją w kontenerze w środowisku Kubernetes.

### <a name="create-an-aspnet-core-web-app"></a>Tworzenie aplikacji internetowej ASP.NET Core
Jeśli masz zainstalowaną platformę [.NET Core](https://www.microsoft.com/net), możesz szybko utworzyć aplikację internetową ASP.NET Core w folderze o nazwie `webfrontend`.
    
```cmd
dotnet new mvc --name webfrontend
```

Możesz też **pobrać przykładowy kod z serwisu GitHub**. Otwórz stronę https://github.com/Azure/dev-spaces i wybierz pozycję **Clone or Download** (Sklonuj lub pobierz). Repozytorium GitHub zostanie pobrane do środowiska lokalnego. Kod używany w tym przewodniku znajduje się w lokalizacji `samples/dotnetcore/getting-started/webfrontend`.

[!INCLUDE[](includes/azds-prep.md)]

[!INCLUDE[](includes/build-run-k8s-cli.md)]

### <a name="update-a-content-file"></a>Aktualizowanie pliku zawartości
Usługa Azure Dev Spaces umożliwia nie tylko uruchamianie kodu w środowisku Kubernetes — pozwala też szybko i wielokrotnie wyświetlać efekt zmian wprowadzonych w kodzie w środowisku Kubernetes w chmurze.

1. Znajdź plik `./Views/Home/Index.cshtml` i zmień kod HTML. Na przykład zmień wiersz 70 z `<h2>Application uses</h2>` na `<h2>Hello k8s in Azure!</h2>`
1. Zapisz plik. Po chwili w oknie terminalu pojawi się komunikat informujący o tym, że plik w uruchomionym kontenerze został zaktualizowany.
1. Otwórz przeglądarkę i odśwież stronę. Na stronie powinien być widoczny zaktualizowany kod HTML.

Co się stało? Modyfikacje plików zawartości, takich jak HTML i CSS, nie wymagają ponownej kompilacji w aplikacji internetowej .NET Core. Z tego względu aktywne polecenie `azds up` automatycznie synchronizuje wszystkie zmodyfikowane pliki zawartości z uruchomionym kontenerem na platformie Azure, co umożliwia błyskawiczny podgląd zmian zawartości.

### <a name="update-a-code-file"></a>Aktualizowanie pliku kodu
Aktualizacja plików kodu wymaga nieco więcej pracy, ponieważ trzeba ponownie skompilować aplikację .NET Core i utworzyć zaktualizowane pliki binarne aplikacji.

1. W oknie terminalu naciśnij klawisze `Ctrl+C`, aby zatrzymać polecenie `azds up`.
1. Otwórz plik kodu o nazwie `Controllers/HomeController.cs` i zmień komunikat wyświetlany na stronie Informacje: `ViewData["Message"] = "Your application description page.";`
1. Zapisz plik.
1. Uruchom polecenie `azds up` w oknie terminalu. 

Spowoduje to ponowne skompilowanie obrazu kontenera i ponowne wdrożenie planu narzędzia Helm. Aby zobaczyć efekt zmian kodu w uruchomionej aplikacji, otwórz menu Informacje w aplikacji internetowej.


Oprócz tego dostępna jest jeszcze *szybsza metoda* opracowywania kodu. Omówimy ją w następnej sekcji. 

## <a name="debug-a-container-in-kubernetes"></a>Debugowanie kontenera w środowisku Kubernetes

[!INCLUDE[](includes/debug-intro.md)]

[!INCLUDE[](includes/init-debug-assets-vscode.md)]


### <a name="select-the-azds-debug-configuration"></a>Wybieranie konfiguracji debugowania w rozwiązaniu AZDS
1. Aby otworzyć widok debugowania, kliknij ikonę debugowania na **pasku działań** w bocznej części programu VS Code.
1. Wybierz pozycję **.NET Core Launch (AZDS)** (Uruchamianie programu .NET Core [AZDS]) jako aktywną konfigurację debugowania.

![](media/get-started-netcore/debug-configuration.png)

> [!Note]
> Jeśli na palecie poleceń nie widać żadnych poleceń usługi Azure Dev Spaces, upewnij się, że masz zainstalowane rozszerzenie VS Code dla usługi Azure Dev Spaces. Upewnij się, że obszar roboczy otwarty w programie VS Code odpowiada folderowi zawierającemu plik azds.yaml.


### <a name="debug-the-container-in-kubernetes"></a>Debugowanie kontenera w środowisku Kubernetes
Po naciśnięciu klawisza **F5** możesz debugować kod w środowisku Kubernetes.

Podobnie jak w przypadku polecenia `up` kod jest synchronizowany z obszarem deweloperskim, a kontener jest kompilowany i wdrażany w środowisku Kubernetes. Oczywiście tym razem debuger jest dołączany do zdalnego kontenera.

[!INCLUDE[](includes/tip-vscode-status-bar-url.md)]

Ustaw punkt przerwania w pliku kodu po stronie serwera, na przykład w obrębie funkcji `Index()` w pliku źródłowym `Controllers/HomeController.cs`. Odświeżenie strony w przeglądarce powoduje aktywowanie punktu przerwania.

Masz pełny dostęp do informacji debugowania, takich jak stos wywołań, zmienne lokalne, informacje o wyjątkach itd., zupełnie jakby kod był wykonywany lokalnie.

### <a name="edit-code-and-refresh"></a>Edytowanie kodu i odświeżanie
Przy aktywnym debugerze wprowadź zmianę w kodzie. Na przykład zmodyfikuj komunikat na stronie Informacje w pliku `Controllers/HomeController.cs`. 

```csharp
public IActionResult About()
{
    ViewData["Message"] = "My custom message in the About page.";
    return View();
}
```

Zapisz plik i w okienku **Debug actions** (Akcje debugowania) kliknij przycisk **Refresh** (Odśwież). 

![](media/get-started-netcore/debug-action-refresh.png)

Zamiast ponownego kompilowania i wdrażania nowego obrazu kontenera przy każdej zmianie kodu, co często zajmuje dużo czasu, usługa Azure Dev Spaces przyrostowo ponownie kompiluje kod w istniejącym kontenerze, co przyspiesza działanie pętli edytowania/debugowania.

Odśwież aplikację internetową w przeglądarce, a następnie otwórz stronę Informacje. Twój komunikat powinien zostać wyświetlony w interfejsie użytkownika.

**Umiesz już korzystać z metody szybkiego wprowadzania zmian w kodzie i debugowania bezpośrednio w środowisku Kubernetes.** Następnie zobaczysz, jak utworzyć i wywołać drugi kontener.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Więcej informacji na temat programowania zespołowego](team-development-netcore.md)