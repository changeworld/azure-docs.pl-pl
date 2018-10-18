---
title: Tworzenie obszaru deweloperskiego Kubernetes w chmurze | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: stepro
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
ms.author: stephpr
ms.date: 09/26/2018
ms.topic: quickstart
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: mmontwil
ms.openlocfilehash: 3d7244144567b3303d383f0dc29a51af302b3bcf
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585027"
---
# <a name="quickstart-create-a-kubernetes-dev-space-with-azure-dev-spaces-java-and-vs-code"></a>Szybki start: tworzenie obszaru deweloperskiego Kubernetes za pomocą usługi Azure Dev Spaces (język Java i program VS Code)

Ten przewodnik zawiera informacje na temat wykonywania następujących czynności:

- Konfigurowanie usługi Azure Dev Spaces za pomocą zarządzanego klastra Kubernetes na platformie Azure.
- Iteracyjne tworzenie kodu w kontenerach przy użyciu programu VS Code i wiersza polecenia.
- Debugowanie kodu w obszarze deweloperskim z poziomu programu VS Code.

> [!Note]
> **Jeśli utkniesz** w dowolnym momencie, zobacz sekcję [Rozwiązywanie problemów](troubleshooting.md) lub prześlij komentarz na tej stronie. Możesz również spróbować wykonać instrukcje z bardziej szczegółowego [samouczka](get-started-netcore.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
- [Program Visual Studio Code](https://code.visualstudio.com/download)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) w wersji 2.0.43 lub nowszej.
- Klaster Kubernetes z uruchomioną platformą Kubernetes 1.10.3 lub nowszą w regionach EastUS, CentralUS, WestUS2, WestEurope, CanadaCentral lub CanadaEast z włączonym **routingiem aplikacji protokołu HTTP**.

    ```cmd
    az group create --name MyResourceGroup --region <region>
    az aks create -g MyResourceGroup -n myAKS --location <region> --kubernetes-version 1.11.2 --enable-addons http_application_routing
    ```

## <a name="set-up-azure-dev-spaces"></a>Konfigurowanie usługi Azure Dev Spaces

1. Skonfiguruj usługę Dev Spaces na klastrze AKS:`az aks use-dev-spaces -g MyResourceGroup -n MyAKS`
1. Pobierz [rozszerzenie usługi Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) dla programu VS Code. Kliknij pozycję Zainstaluj najpierw na stronie rozszerzenia w witrynie Marketplace, a następnie w programie VS Code.
1. Pobierz rozszerzenie [Java Debugger for Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) dla programu VS Code. Kliknij pozycję Zainstaluj najpierw na stronie rozszerzenia w witrynie Marketplace, a następnie w programie VS Code.

## <a name="build-and-run-code-in-kubernetes"></a>Kompilowanie i uruchamianie kodu na platformie Kubernetes

1. Pobierz przykładowy kod z usługi GitHub: [https://github.com/Azure/dev-spaces](https://github.com/Azure/dev-spaces) 
1. Zmień katalog na folder webfrontend: `cd dev-spaces/samples/java/getting-started/webfrontend`
1. Generuj zasoby programu Docker i wykresu Helm: `azds prep --public`
1. Skompiluj i uruchom kod w środowisku AKS. W oknie terminala z poziomu **folderu webfrontend** uruchom to polecenie: `azds up`
1. Przeskanuj dane wyjściowe konsoli w poszukiwaniu informacji o adresie URL, który został utworzony za pomocą polecenia `up`. Będzie on mieć postać:

   ```output
    (pending registration) Service 'webfrontend' port 'http' will be available at <url>
    Service 'webfrontend' port 80 (TCP) is available at http://localhost:<port>
   ```

   Otwórz ten adres URL w oknie przeglądarki — aplikacja internetowa powinna zostać załadowana.

   > [!Note]
   > Przy pierwszym uruchomieniu przygotowanie publicznego serwera DNS może potrwać kilka minut. Jeśli publiczny adres URL nie zostanie rozpoznany, możesz użyć alternatywnego adresu URL http://localhost:<portnumber>, który jest wyświetlany w danych wyjściowych konsoli. Jeśli używasz adresu URL hosta lokalnego, może się wydawać, że kontener działa lokalnie, ale faktycznie jest on uruchamiany w usłudze AKS. Dla Twojej wygody i ułatwienia interakcji z usługą z komputera lokalnego usługa Azure Dev Spaces tworzy tymczasowy tunel SSH do kontenera uruchomionego na platformie Azure. Później, gdy rekord DNS będzie gotowy, możesz wrócić i wypróbować publiczny adres URL.

### <a name="update-a-code-file"></a>Aktualizowanie pliku kodu

1. W oknie terminalu naciśnij klawisze `Ctrl+C`, aby zatrzymać polecenie `azds up`.
1. Otwórz plik kodu o nazwie `src/main/java/com/ms/sample/webfrontend/Application.java` i zmodyfikuj komunikat powitalny: `return "Hello from webfrontend in Azure!";`
1. Zapisz plik.
1. Uruchom polecenie `azds up` w oknie terminalu.

Spowoduje to ponowne skompilowanie obrazu kontenera i ponowne wdrożenie planu narzędzia Helm. Aby zobaczyć efekt zmian kodu w uruchomionej aplikacji, po prostu odśwież przeglądarkę.

Oprócz tego dostępna jest jeszcze *szybsza metoda* opracowywania kodu. Omówimy ją w następnej sekcji.

## <a name="debug-a-container-in-kubernetes"></a>Debugowanie kontenera w środowisku Kubernetes

W tej sekcji użyjesz programu VS Code do bezpośredniego debugowania naszego kontenera uruchomionego na platformie Azure. Ponadto dowiesz się, jak uzyskać szybszą pętlę edycji, uruchamiania i testowania.

![](./media/common/edit-refresh-see.png)

### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Inicjowanie zasobów debugowania za pomocą rozszerzenia programu VS Code
Najpierw skonfiguruj projekt kodu tak, aby program VS Code komunikował się z obszarem deweloperskim na platformie Azure. Rozszerzenie programu VS Code dla usługi Azure Dev Spaces udostępnia polecenie pomocnika w celu ustawienia konfiguracji debugowania.

Otwórz okno **Paleta poleceń** (za pomocą menu **Widok | Paleta poleceń**) i przy użyciu autouzupełniania wpisz i wybierz to polecenie: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.

Spowoduje to dodanie konfiguracji debugowania dla usługi Azure Dev Spaces w obszarze folderu `.vscode`.

![](./media/common/command-palette.png)

### <a name="select-the-azds-debug-configuration"></a>Wybieranie konfiguracji debugowania w rozwiązaniu AZDS
1. Aby otworzyć widok debugowania, kliknij ikonę debugowania na **pasku działań** w bocznej części programu VS Code.
1. Wybierz pozycję **Launch Java Program (AZDS)** (Uruchom program Java — AZDS) jako aktywną konfigurację debugowania.

![](media/get-started-java/debug-configuration.png)

> [!Note]
> Jeśli na palecie poleceń nie widać żadnych poleceń usługi Azure Dev Spaces, upewnij się, że masz zainstalowane rozszerzenie VS Code dla usługi Azure Dev Spaces. Upewnij się, że obszar roboczy otwarty w programie VS Code odpowiada folderowi zawierającemu plik azds.yaml.

### <a name="debug-the-container-in-kubernetes"></a>Debugowanie kontenera w środowisku Kubernetes
Po naciśnięciu klawisza **F5** możesz debugować kod w środowisku Kubernetes.

Podobnie jak w przypadku polecenia `up` kod jest synchronizowany z obszarem deweloperskim, a kontener jest kompilowany i wdrażany w środowisku Kubernetes. Oczywiście tym razem debuger jest dołączany do zdalnego kontenera.

> [!Tip]
> Na pasku stanu programu VS Code będzie wyświetlany adres URL, który można kliknąć.

Ustaw punkt przerwania w pliku kodu po stronie serwera, na przykład w obrębie funkcji `greeting()` w pliku źródłowym `src/main/java/com/ms/sample/webfrontend/Application.java`. Odświeżenie strony w przeglądarce powoduje aktywowanie punktu przerwania.

Masz pełny dostęp do informacji debugowania, takich jak stos wywołań, zmienne lokalne, informacje o wyjątkach itd., zupełnie jakby kod był wykonywany lokalnie.

### <a name="edit-code-and-refresh"></a>Edytowanie kodu i odświeżanie
Przy aktywnym debugerze wprowadź zmianę w kodzie. Na przykład zmodyfikuj wiadomość powitalną w pliku `src/main/java/com/ms/sample/webfrontend/Application.java`. 

```java
public String greeting()
{
    return "I'm debugging Java code in Azure!";
}
```

Zapisz plik i w okienku **Debug actions** (Akcje debugowania) kliknij przycisk **Refresh** (Odśwież). 

![](media/get-started-java/debug-action-refresh.png)

Zamiast ponownego kompilowania i wdrażania nowego obrazu kontenera przy każdej zmianie kodu, co często zajmuje dużo czasu, usługa Azure Dev Spaces przyrostowo ponownie kompiluje kod w istniejącym kontenerze, co przyspiesza działanie pętli edytowania/debugowania.

Odśwież aplikację internetową w przeglądarce. Twój komunikat powinien zostać wyświetlony w interfejsie użytkownika.

**Umiesz już korzystać z metody szybkiego wprowadzania zmian w kodzie i debugowania bezpośrednio w środowisku Kubernetes.**

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak usługa Azure Dev Spaces pomaga tworzyć bardziej złożone aplikacje w wielu kontenerach i jak można uprościć programowanie zespołowe przez pracę z różnymi wersjami lub gałęziami kodu w różnych obszarach.

> [!div class="nextstepaction"]
> [Working with multiple containers and team development (Praca z wieloma kontenerami i programowanie zespołowe)](team-development-java.md)