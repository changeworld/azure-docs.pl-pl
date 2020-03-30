---
title: 'Szybki start: uruchamianie niestandardowego kontenera systemu Linux'
description: Wprowadzenie do kontenerów systemu Linux w usłudze Azure App Service przez wdrożenie pierwszego kontenera niestandardowego przy użyciu rejestratorów kontenerów platformy Azure.
author: msangapu-msft
ms.author: msangapu
ms.date: 08/28/2019
ms.topic: quickstart
ms.openlocfilehash: 62e6b007e89fc6be726d3d971ca838770db9cb6e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75422146"
---
# <a name="deploy-a-custom-linux-container-to-azure-app-service"></a>Wdrażanie niestandardowego kontenera systemu Linux w usłudze Azure App Service

Usługa App Service w systemie Linux zapewnia wstępnie zdefiniowane stosy aplikacji w systemie Linux z obsługą języków takich jak .NET, PHP, Node.js i innych. Można także użyć niestandardowego obrazu platformy Docker, aby uruchamiać aplikację internetową na stosie aplikacji, który nie jest zdefiniowany na platformie Azure. Ten przewodnik Szybki start pokazuje, jak wdrożyć obraz z [rejestru kontenerów platformy Azure](/azure/container-registry) (ACR) do usługi App Service.

## <a name="prerequisites"></a>Wymagania wstępne

* [Konto platformy Azure](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
* [Docker](https://www.docker.com/community-edition)
* [Kod programu Visual Studio](https://code.visualstudio.com/)
* [Rozszerzenie usługi Azure App Service dla vs code](vscode:extension/ms-azuretools.vscode-azureappservice). To rozszerzenie służy do tworzenia, zarządzania i wdrażania aplikacji sieci Web systemu Linux na platformie Azure Platform as a Service (PaaS).
* [Rozszerzenie platformy Docker dla programu VS Code](vscode:extension/ms-azuretools.vscode-docker). Tego rozszerzenia można użyć, aby uprościć zarządzanie lokalnymi obrazami i poleceniami platformy Docker oraz wdrożyć wbudowane obrazy aplikacji na platformie Azure.

## <a name="create-an-image"></a>Tworzenie obrazu

Aby ukończyć ten szybki start, potrzebny jest odpowiedni obraz aplikacji sieci Web przechowywany w [rejestrze kontenerów platformy Azure.](/azure/container-registry) Postępuj zgodnie z instrukcjami w [przewodniku Szybki start: Utwórz rejestr kontenerów prywatnych przy użyciu witryny Azure portal](/azure/container-registry/container-registry-get-started-portal), ale użyj `mcr.microsoft.com/azuredocs/go` obrazu zamiast `hello-world` obrazu. W celach informacyjnych [przykładowy plik dockerfile znajduje się w repozytorium przykładów platformy Azure](https://github.com/Azure-Samples/go-docs-hello-world).

> [!IMPORTANT]
> Pamiętaj, aby ustawić opcję **Użytkownik administracyjny** na **Włącz** podczas tworzenia rejestru kontenerów. Można również ustawić go z sekcji **klucze dostępu** na stronie rejestru w witrynie Azure portal. To ustawienie jest wymagane dla dostępu do usługi App Service.

## <a name="sign-in"></a>Logowanie

Następnie uruchom program VS Code i zaloguj się do konta platformy Azure przy użyciu rozszerzenia usługi App Service. Aby to zrobić, wybierz logo platformy Azure na pasku aktywności, przejdź do Eksploratora **USŁUGI APP** SERVICE, a następnie wybierz pozycję Zaloguj się **na platformę Azure** i postępuj zgodnie z instrukcjami.

![zaloguj się na platformie Azure](./media/quickstart-docker/sign-in.png)

## <a name="check-prerequisites"></a>Sprawdzanie wymagań wstępnych

Teraz możesz sprawdzić, czy masz wszystkie wymagania wstępne zainstalowane i poprawnie skonfigurowane.

W programie VS Code powinien zostać wyświetlony twój adres e-mail platformy Azure na pasku stanu i subskrypcji w Eksploratorze **USŁUGI APP SERVICE.**

Następnie sprawdź, czy masz zainstalowany i uruchomiony program Docker. Następujące polecenie wyświetli wersję platformy Docker, jeśli jest uruchomiona.

```bash
docker --version
```

Na koniec upewnij się, że twój rejestr kontenerów platformy Azure jest połączony. Aby to zrobić, wybierz logo platformy Docker na pasku aktywności, a następnie przejdź do **pozycji REGISTRIES**.

![Rejestry](./media/quickstart-docker/registries.png)

## <a name="deploy-the-image-to-azure-app-service"></a>Wdrażanie obrazu w usłudze Azure App Service

Teraz, gdy wszystko jest skonfigurowane, można wdrożyć obraz w [usłudze Azure App Service](https://azure.microsoft.com/services/app-service/) bezpośrednio z Eksploratora rozszerzeń platformy Docker.

Znajdź obraz w węźle **Rejestry** w eksploratorze **DOCKER** i rozwiń go, aby wyświetlić jego tagi. Kliknij prawym przyciskiem myszy znacznik, a następnie wybierz polecenie **Wdrażanie obrazu w usłudze Azure App Service**.

W tym miejscu postępuj zgodnie z instrukcjami, aby wybrać subskrypcję, globalnie unikatową nazwę aplikacji, grupę zasobów i plan usługi aplikacji. Wybierz **B1 Basic** dla warstwy cenowej i regionu.

Po wdrożeniu aplikacja `http://<app name>.azurewebsites.net`jest dostępna pod adresem .

**Grupa zasobów** to nazwana kolekcja wszystkich zasobów aplikacji na platformie Azure. Na przykład grupa zasobów może zawierać odwołanie do witryny sieci Web, bazy danych i funkcji platformy Azure.

**Plan usługi aplikacji** definiuje zasoby fizyczne, które będą używane do hostowania witryny sieci Web. Ten szybki start używa **podstawowego** planu hostingowego w infrastrukturze **Systemu Linux,** co oznacza, że witryna będzie hostowana na komputerze z systemem Linux obok innych stron internetowych. Jeśli zaczniesz od planu **Basic,** możesz użyć witryny Azure Portal do skalowania w górę, tak aby Twoja była jedyną lokacją uruchomień na komputerze.

## <a name="browse-the-website"></a>Przeglądanie witryny sieci Web

Panel **Dane wyjściowe** zostanie otwarty podczas wdrażania, aby wskazać stan operacji. Po zakończeniu operacji znajdź aplikację utworzoną w Eksploratorze **USŁUGI APP SERVICE,** kliknij ją prawym przyciskiem myszy, a następnie wybierz pozycję **Przeglądaj witrynę sieci Web,** aby otworzyć witrynę w przeglądarce.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=quickstart-docker&step=deploy-app)

## <a name="next-steps"></a>Następne kroki

Gratulacje, pomyślnie ukończyłeś ten szybki start!

Następnie zapoznaj się z innymi rozszerzeniami platformy Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Narzędzia interfejsu wiersza polecenia platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Narzędzia usługi Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Możesz też uzyskać je wszystkie, instalując pakiet rozszerzeń [narzędzi Azure Tools.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)
