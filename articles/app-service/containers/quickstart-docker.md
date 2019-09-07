---
title: Wdrażanie aplikacji platformy Docker w systemie Linux — Azure App Service
description: Jak wdrożyć obraz platformy Docker w usłudze Azure App Services dla systemu Linux
author: msangapu
ms.author: msangapu
ms.date: 08/28/2019
ms.topic: quickstart
ms.service: app-service
ms.openlocfilehash: 2a7dc477b4cd0be0c50569d84e10cfe1d666eac9
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70392114"
---
# <a name="deploy-to-azure-using-docker"></a>Wdrażanie na platformie Azure przy użyciu platformy Docker

App Service w systemie Linux udostępnia wstępnie zdefiniowane stosy aplikacji w systemie Linux z obsługą języków takich jak .NET, PHP, Node. js i innych. Można także użyć niestandardowego obrazu platformy Docker, aby uruchamiać aplikację internetową na stosie aplikacji, który nie jest zdefiniowany na platformie Azure. W tym przewodniku szybki start przedstawiono sposób wdrażania obrazu z [Azure Container Registry](/azure/container-registry) (ACR) do App Service.

## <a name="prerequisites"></a>Wymagania wstępne

* [Konto platformy Azure](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
* [Docker](https://www.docker.com/community-edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure App Service rozszerzenie vs Code](vscode:extension/ms-azuretools.vscode-azureappservice). To rozszerzenie służy do tworzenia i wdrażania Web Apps systemu Linux na platformie Azure jako usługi (PaaS) oraz zarządzania nimi.
* [Rozszerzenie platformy Docker dla vs Code](vscode:extension/ms-azuretools.vscode-docker). To rozszerzenie służy do uproszczenia zarządzania lokalnymi obrazami i poleceniami platformy Docker oraz do wdrażania wbudowanych obrazów aplikacji na platformie Azure.

## <a name="create-an-image"></a>Tworzenie obrazu

Aby ukończyć ten przewodnik Szybki Start, potrzebny będzie odpowiedni obraz aplikacji sieci Web przechowywany w [Azure Container Registry](/azure/container-registry). Postępuj zgodnie z [instrukcjami w przewodniku szybki start: Utwórz prywatny rejestr kontenerów przy użyciu Azure Portal](/azure/container-registry/container-registry-get-started-portal), ale zamiast `hello-world` obrazu `mcr.microsoft.com/azuredocs/go` Użyj obrazu.

> [!IMPORTANT]
> Należy pamiętać o ustawieniu opcji **użytkownik administrator** , aby **włączyć** ją podczas tworzenia rejestru kontenerów. Można również ustawić ją z sekcji **klucze dostępu** na stronie rejestru w Azure Portal. To ustawienie jest wymagane do App Service dostępu.

## <a name="sign-in"></a>Logowanie

Następnie uruchom VS Code i zaloguj się do konta platformy Azure przy użyciu rozszerzenia App Service. W tym celu wybierz logo platformy Azure na pasku działania, przejdź do Eksploratora **usługi App Service** , a następnie wybierz pozycję **Zaloguj się do platformy Azure** i postępuj zgodnie z instrukcjami.

![Zaloguj się do platformy Azure](./media/quickstart-docker/sign-in.png)

## <a name="check-prerequisites"></a>Sprawdź wymagania wstępne

Teraz możesz sprawdzić, czy zostały zainstalowane i prawidłowo skonfigurowane wszystkie wymagania wstępne.

W VS Code adres e-mail platformy Azure powinien zostać wyświetlony na pasku stanu i w ramach subskrypcji w Eksploratorze **usługi App Service** .

Następnie sprawdź, czy zainstalowano i uruchomiono platformę Docker. Następujące polecenie spowoduje wyświetlenie wersji platformy Docker, jeśli jest uruchomiona.

```bash
docker --version
```

Na koniec upewnij się, że Azure Container Registry jest połączona. Aby to zrobić, wybierz logo platformy Docker na pasku działania, a następnie przejdź do **rejestrów**.

![Rejestry](./media/quickstart-docker/registries.png)

## <a name="deploy-the-image-to-azure-app-service"></a>Wdróż obraz do Azure App Service

Teraz, gdy wszystko jest skonfigurowane, możesz wdrożyć obraz do [Azure App Service](https://azure.microsoft.com/services/app-service/) bezpośrednio z Eksploratora rozszerzeń platformy Docker.

Znajdź obraz w węźle **rejestry** w Eksploratorze **platformy Docker** i rozwiń go, aby wyświetlić jego Tagi. Kliknij prawym przyciskiem myszy tag, a następnie wybierz polecenie **Wdróż obraz do Azure App Service**.

W tym miejscu postępuj zgodnie z monitami, aby wybrać subskrypcję, globalnie unikatową nazwę aplikacji, grupę zasobów i plan App Service. Wybierz pozycję **B1 podstawowa** dla warstwy cenowej i regionu.

Po wdrożeniu aplikacja jest dostępna pod adresem `http://<app name>.azurewebsites.net`.

**Grupa zasobów** to nazwana Kolekcja wszystkich zasobów aplikacji na platformie Azure. Na przykład grupa zasobów może zawierać odwołanie do witryny sieci Web, bazy danych i funkcji platformy Azure.

**Plan App Service** definiuje zasoby fizyczne, które będą używane do hostowania witryny sieci Web. Ten przewodnik Szybki Start korzysta z **podstawowego** planu hostingu w infrastrukturze systemu **Linux** , co oznacza, że witryna będzie hostowana na komputerze z systemem Linux oraz w innych witrynach sieci Web. Jeśli zaczniesz od planu **Basic** , możesz użyć Azure Portal do skalowania w górę, aby Twoja witryna była jedyną lokacją uruchomioną na komputerze.

## <a name="browse-the-website"></a>Przeglądanie witryny sieci Web

Panel **dane wyjściowe** zostanie otwarty podczas wdrażania, aby wskazać stan operacji. Po zakończeniu operacji Znajdź aplikację utworzoną w Eksploratorze **usługi App Service** , kliknij ją prawym przyciskiem myszy, a następnie wybierz pozycję **Przeglądaj witrynę sieci Web** , aby otworzyć witrynę w przeglądarce.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=quickstart-docker&step=deploy-app)

## <a name="next-steps"></a>Następne kroki

Gratulacje, udało Ci się ukończyć ten przewodnik Szybki Start.

Następnie zapoznaj się z innymi rozszerzeniami platformy Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Narzędzia interfejsu wiersza polecenia platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Narzędzia Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Lub Pobierz je wszystkie, instalując pakiet rozszerzeń [narzędzi platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) .
