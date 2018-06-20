---
title: Tworzenie obszaru deweloperskiego Kubernetes w chmurze | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: ghogen
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
ms.author: ghogen
ms.date: 06/06/2018
ms.topic: quickstart
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: 3802e67503fd546ef71b9c26daddc8ef63cf4bd2
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823230"
---
# <a name="quickstart-create-a-kubernetes-dev-space-with-azure-dev-spaces-net-core-and-vs-code"></a>Szybki start: tworzenie obszaru deweloperskiego Kubernetes za pomocą usługi Azure Dev Spaces (platforma .NET Core i program VS Code)

Ten przewodnik zawiera informacje na temat wykonywania następujących czynności:

- Konfigurowanie usługi Azure Dev Spaces za pomocą zarządzanego klastra Kubernetes na platformie Azure.
- Iteracyjne tworzenie kodu w kontenerach przy użyciu programu VS Code i wiersza polecenia.
- Debugowanie kodu w obszarze deweloperskim z poziomu programu VS Code

> [!Note]
> **Jeśli utkniesz** w dowolnym momencie, zobacz sekcję [Rozwiązywanie problemów](troubleshooting.md) lub prześlij komentarz na tej stronie. Możesz także spróbować skorzystać z bardziej szczegółowego [samouczka](get-started-netcore.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
- [Klaster Kubernetes](https://ms.portal.azure.com/#create/microsoft.aks) z uruchomioną platformą Kubernetes 1.9.6 we Wschodnich stanach USA, Europie Zachodniej lub we Wschodnim regionie Kanady z włączonym **routingiem aplikacji protokołu HTTP**.

  ![Pamiętaj, aby włączyć routing aplikacji protokołu HTTP.](media/common/Kubernetes-Create-Cluster-3.PNG)

- [Program Visual Studio Code](https://code.visualstudio.com/download)

## <a name="set-up-azure-dev-spaces"></a>Konfigurowanie usługi Azure Dev Spaces

1. Zainstaluj [interfejs wiersza polecenia CLI Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) (wersja 2.0.33 lub nowsza).
1. Skonfiguruj usługę Dev Spaces na klastrze AKS:`az aks use-dev-spaces -g MyResourceGroup -n MyAKS`
1. Pobierz [rozszerzenie usługi Azure Dev Spaces](https://aka.ms/get-azds-code) dla programu VS Code.
1. Zainstaluj rozszerzenie: `code --install-extension path-to-downloaded-extension/azds-0.1.1.vsix`

## <a name="build-and-run-code-in-kubernetes"></a>Kompilowanie i uruchamianie kodu na platformie Kubernetes

1. Pobierz przykładowy kod z usługi GitHub: [https://github.com/Azure/dev-spaces](https://github.com/Azure/dev-spaces) 
1. Zmień katalog na folder webfrontend: `cd dev-spaces/samples/dotnetcore/getting-started/webfrontend`
1. Generuj zasoby programu Docker i wykresu Helm: `azds prep --public`
1. Skompiluj i uruchom kod w środowisku AKS. W oknie terminalu uruchom to polecenie z **głównego folderu kodu**, webfrontend: `azds up`
1. Przeskanuj dane wyjściowe konsoli w poszukiwaniu informacji o adresie URL, który został utworzony za pomocą polecenia `up`. Będzie on mieć postać: 

   `Service 'webfrontend' port 'http' is available at <url>` 

   Otwórz ten adres URL w oknie przeglądarki — aplikacja internetowa powinna zostać załadowana. 

### <a name="update-a-content-file"></a>Aktualizowanie pliku zawartości

1. Znajdź plik, np. `./Views/Home/Index.cshtml`, i zmień kod HTML. Na przykład zmień wiersz 70 z `<h2>Application uses</h2>` na `<h2>Hello k8s in Azure!</h2>`
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

W tej sekcji użyjesz programu VS Code do bezpośredniego debugowania naszego kontenera uruchomionego na platformie Azure. Ponadto dowiesz się, jak uzyskać szybszą pętlę edycji, uruchamiania i testowania.

![](./media/common/edit-refresh-see.png)

### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Inicjowanie zasobów debugowania za pomocą rozszerzenia programu VS Code
Najpierw skonfiguruj projekt kodu tak, aby program VS Code komunikował się z obszarem deweloperskim na platformie Azure. Rozszerzenie programu VS Code dla usługi Azure Dev Spaces udostępnia polecenie pomocnika w celu ustawienia konfiguracji debugowania. 

Otwórz okno **Paleta poleceń** (za pomocą menu **Widok | Paleta poleceń**) i przy użyciu autouzupełniania wpisz i wybierz to polecenie: `Azure Dev Spaces: Create configuration files for connected development`. 

Spowoduje to dodanie konfiguracji debugowania dla usługi Azure Dev Spaces w obszarze folderu `.vscode`.

![](./media/common/command-palette.png)

### <a name="select-the-azds-debug-configuration"></a>Wybieranie konfiguracji debugowania w rozwiązaniu AZDS
1. Aby otworzyć widok debugowania, kliknij ikonę debugowania na **pasku działań** w bocznej części programu VS Code.
1. Wybierz pozycję **.NET Core Launch (AZDS)** (Uruchamianie programu .NET Core [AZDS]) jako aktywną konfigurację debugowania.

![](media/get-started-netcore/debug-configuration.png)

> [!Note]
> Jeśli na palecie poleceń nie widać żadnych poleceń usługi Azure Dev Spaces, upewnij się, że masz zainstalowane rozszerzenie VS Code dla usługi Azure Dev Spaces. Upewnij się, że obszar roboczy otwarty w programie VS Code odpowiada folderowi zawierającemu plik azds.yaml.


### <a name="debug-the-container-in-kubernetes"></a>Debugowanie kontenera w środowisku Kubernetes
Po naciśnięciu klawisza **F5** możesz debugować kod w środowisku Kubernetes.

Podobnie jak w przypadku polecenia `up` kod jest synchronizowany z obszarem deweloperskim, a kontener jest kompilowany i wdrażany w środowisku Kubernetes. Oczywiście tym razem debuger jest dołączany do zdalnego kontenera.

> [!Tip]
> Na pasku stanu programu VS Code będzie wyświetlany adres URL, który można kliknąć.

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

**Umiesz już korzystać z metody szybkiego wprowadzania zmian w kodzie i debugowania bezpośrednio w środowisku Kubernetes.**

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak usługa Azure Dev Spaces pomaga tworzyć bardziej złożone aplikacje w wielu kontenerach i jak można uprościć programowanie zespołowe przez pracę z różnymi wersjami lub gałęziami kodu w różnych obszarach. 

> [!div class="nextstepaction"]
> [Working with multiple containers and team development (Praca z wieloma kontenerami i programowanie zespołowe)](get-started-netcore.md#call-a-service-running-in-a-separate-container)