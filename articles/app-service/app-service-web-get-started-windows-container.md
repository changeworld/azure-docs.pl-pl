---
title: 'Szybki start: kontener systemu Windows (wersja zapoznawcza)'
description: Wdrażanie pierwszego niestandardowego kontenera systemu Windows w usłudze Azure App Service. Skorzystaj z konteneryzacji, dostosuj kontener systemu Windows tak, jak ci się podoba.
ms.topic: quickstart
ms.date: 08/30/2019
ms.custom: mvc, seodec18
ms.openlocfilehash: cd6b78e5fd824cc013cc946d23677237923f485e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80047117"
---
# <a name="run-a-custom-windows-container-in-azure-preview"></a>Uruchamianie niestandardowego kontenera systemu Windows na platformie Azure (wersja zapoznawcza)

[Usługa Azure App Service](overview.md) udostępnia wstępnie zdefiniowane stosy aplikacji w systemie Windows, takie jak ASP.NET lub Node.js, działające w usługach IIS. Wstępnie skonfigurowane środowisko systemu Windows blokuje system operacyjny przed dostępem administracyjnym, instalacjami oprogramowania, zmianami w globalnej pamięci podręcznej zestawów itd. Aby uzyskać więcej informacji, zobacz [Funkcje systemu operacyjnego w usłudze Azure App Service](operating-system-functionality.md). Jeśli aplikacja wymaga szerszego dostępu niż ten, na który pozwala wstępnie skonfigurowane środowisko, możesz wdrożyć niestandardowy kontener systemu Windows.

Ten przewodnik Szybki start pokazuje, jak wdrożyć aplikację ASP.NET w obrazie systemu Windows do [centrum platformy Docker](https://hub.docker.com/) z programu Visual Studio. Aplikację można uruchomić w kontenerze niestandardowym w usłudze Azure App Service.

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- <a href="https://hub.docker.com/" target="_blank">Załóż konto usługi Docker Hub</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Zainstaluj program Docker for Windows</a>.
- <a href="https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Przełącz platformę Docker na potrzeby uruchamiania kontenerów systemu Windows</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Zainstaluj program Visual Studio 2019</a> z **ASP.NET i tworzenia sieci Web** oraz obciążeń **deweloperskich platformy Azure.** Jeśli program Visual Studio 2019 został już zainstalowany:

    - Zainstaluj najnowsze aktualizacje w programie Visual Studio, wybierając **pozycję Pomoc** > **Sprawdź dostępność aktualizacji**.
    - Dodaj obciążenia w programie Visual Studio, wybierając **pozycję Narzędzia** > **Pobierz narzędzia i funkcje**.

## <a name="create-an-aspnet-web-app"></a>Tworzenie aplikacji internetowej platformy ASP.NET

Utwórz ASP.NET aplikacji sieci web, wykonując następujące kroki:

1. Otwórz program Visual Studio, a następnie wybierz pozycję **Utwórz nowy projekt**.

1. W **obszarze Tworzenie nowego projektu**znajdź i wybierz ASP.NET aplikacji sieci Web **(.NET Framework)** dla języka C#, a następnie wybierz pozycję **Dalej**.

1. W **obszarze Konfiguruj nowy projekt nazwij**aplikację _myFirstAzureWebApp_, a następnie wybierz pozycję **Utwórz**.

   ![Konfigurowanie projektu aplikacji sieci Web](./media/app-service-web-get-started-windows-container/configure-web-app-project-container.png)

1. Na platformie Azure można wdrożyć dowolny typ aplikacji internetowej platformy ASP.NET. W przypadku tego przewodnika Szybki start wybierz szablon **MVC.**

1. Wybierz **pozycję Obsługa platformy Docker**i upewnij się, że uwierzytelnianie jest ustawione na Brak **uwierzytelniania**. Wybierz **pozycję Utwórz**.

   ![Tworzenie ASP.NET aplikacji sieci Web](./media/app-service-web-get-started-windows-container/select-mvc-template-for-container.png)

1. Jeśli plik _Dockerfile_ nie zostanie automatycznie otwarty, otwórz go w **Eksploratorze rozwiązań**.

1. Potrzebny jest [obsługiwany obraz nadrzędny](#use-a-different-parent-image). Zmień obraz nadrzędny, zastępując wiersz `FROM` następującym kodem i zapisując plik:

   ```Dockerfile
   FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
   ```

1. Z menu Visual Studio wybierz **debugowanie** > **start bez debugowania,** aby uruchomić aplikację sieci web lokalnie.

   ![Uruchamianie aplikacji lokalnie](./media/app-service-web-get-started-windows-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>Publikowanie w usłudze Docker Hub

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt **myFirstAzureWebApp** i wybierz polecenie **Publikuj**.

1. Wybierz **pozycję Usługa aplikacji,** a następnie wybierz pozycję **Publikuj**.

1. W obszarze Wybierz **miejsce docelowe publikowania**wybierz pozycję **Rejestr kontenerów** i **Centrum platformy Docker**, a następnie kliknij pozycję **Publikuj**.

   ![Publikowanie ze strony przeglądu projektu](./media/app-service-web-get-started-windows-container/publish-to-docker-vs2019.png)

1. Podaj poświadczenia konta centrum docker i wybierz pozycję **Zapisz**.

   Zaczekaj na zakończenie wdrożenia. Na stronie **Publikowania** jest teraz wyświetlana nazwa repozytorium, której można użyć później.

   ![Publikowanie ze strony przeglądu projektu](./media/app-service-web-get-started-windows-container/published-docker-repository-vs2019.png)

1. Skopiuj tę nazwę repozytorium do późniejszego użycia.

## <a name="create-a-windows-container-app"></a>Tworzenie aplikacji kontenera systemu Windows

1. Zaloguj się do [Portalu Azure]( https://portal.azure.com).

1. W lewym górnym rogu okna witryny Azure Portal wybierz pozycję **Utwórz zasób**.

1. W polu wyszukiwania nad listą zasobów portalu Azure Marketplace wyszukaj **aplikację Web App dla kontenerów**i wybierz pozycję **Utwórz**.

1. W **aplikacji Web App Create**wybierz subskrypcję i **grupę zasobów**. W razie potrzeby można utworzyć nową grupę zasobów.

1. Podaj nazwę aplikacji, na przykład *win-container-demo* i wybierz pozycję **Windows** for **Operating System**. Wybierz **dalej: Docker,** aby kontynuować.

   ![Tworzenie aplikacji sieci Web dla kontenerów](media/app-service-web-get-started-windows-container/create-web-app-continer.png)

1. W **ybranej opcji Źródło obrazu**wybierz pozycję Centrum platformy **Docker** oraz w przypadku **obrazu i znacznika**wprowadź nazwę repozytorium skopiowaną w centrum [publikowania do platformy Docker](#publish-to-docker-hub).

   ![Konfigurowanie aplikacji sieci Web dla kontenerów](media/app-service-web-get-started-windows-container/configure-web-app-continer.png)

    Jeśli masz obraz niestandardowy dla aplikacji internetowej w innym miejscu, na przykład w [usłudze Azure Container Registry](/azure/container-registry/) lub w innym prywatnym repozytorium, możesz skonfigurować go tutaj.

1. Wybierz **pozycję Przejrzyj i utwórz,** a następnie **utwórz** i poczekaj, aż platforma Azure utworzy wymagane zasoby.

## <a name="browse-to-the-container-app"></a>Przechodzenie do aplikacji kontenera

Po zakończeniu operacji platformy Azure zostanie wyświetlone okno powiadomienia.

![Wdrożenie zakończyło się pomyślnie](media/app-service-web-get-started-windows-container/portal-create-finished.png)

1. Kliknij pozycję **Przejdź do zasobu**.

1. W przeglądzie tego zasobu kliknij łącze obok **adresu URL**.

Zostanie otwarta nowa strona przeglądarki:

![Uruchamianie aplikacji kontenera systemu Windows](media/app-service-web-get-started-windows-container/app-starting.png)

Poczekaj kilka minut i spróbuj ponownie, dopóki nie zostanie wyświetlona domyślna strona główna aplikacji ASP.NET:

![Aplikacja kontenera systemu Windows uruchomiona](media/app-service-web-get-started-windows-container/app-running-vs.png)

**Gratulacje!** Uruchamiasz swój pierwszy niestandardowy kontener systemu Windows w usłudze Azure App Service.

## <a name="see-container-start-up-logs"></a>Wyświetlanie dzienników uruchamiania kontenera

Może upłynąć trochę czasu, zanim kontener systemu Windows zostanie załadowany. Aby zobaczyć postęp, przejdź do następującego adresu URL, zastępując * \<app_name>* nazwą aplikacji.
```
https://<app_name>.scm.azurewebsites.net/api/logstream
```

Przesyłane strumieniowo dzienniki wyglądają następująco:

```
2018-07-27T12:03:11  Welcome, you are now connected to log-streaming service.
27/07/2018 12:04:10.978 INFO - Site: win-container-demo - Start container succeeded. Container: facbf6cb214de86e58557a6d073396f640bbe2fdec88f8368695c8d1331fc94b
27/07/2018 12:04:16.767 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.017 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.020 INFO - Site: win-container-demo - Container started successfully
```

## <a name="update-locally-and-redeploy"></a>Lokalne aktualizowanie i ponowne wdrażanie

1. W programie Visual Studio w **Eksploratorze rozwiązań**otwórz **pozycję Widoki** > **Home** > **Index.cshtml**.

1. Znajdź tag HTML `<div class="jumbotron">` u góry i zastąp cały element następującym kodem:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Aby ponownie wdrożyć na platformie Azure, kliknij prawym przyciskiem myszy projekt **myFirstAzureWebApp** w **Eksploratorze rozwiązań** i wybierz polecenie **Publikuj**.

1. Na stronie publikowania wybierz pozycję **Publikuj** i poczekaj na ukończenie procesu publikowania.

1. Aby nakazać usłudze App Service ściągnięcie obrazu z usługi Docker Hub, uruchom ponownie aplikację. Po powrocie na stronę aplikacji w portalu kliknij przycisk **Uruchom ponownie** > **tak**.

   ![Ponowne uruchamianie aplikacji internetowej na platformie Azure](./media/app-service-web-get-started-windows-container/portal-restart-app.png)

Ponownie [przejdź do aplikacji kontenera](#browse-to-the-container-app). Po odświeżeniu strony internetowej najpierw powinna być widoczna strona „Uruchamianie”, a następnie po kilku minutach powinna zostać wyświetlona zaktualizowana strona internetowa.

![Zaktualizowana aplikacja internetowa na platformie Azure](./media/app-service-web-get-started-windows-container/azure-web-app-updated.png)

## <a name="use-a-different-parent-image"></a>Używanie innego obrazu nadrzędnego

Możesz użyć innego niestandardowego obrazu platformy Docker, aby uruchomić aplikację. Należy jednak wybrać odpowiedni [obraz nadrzędny (obraz podstawowy)](https://docs.docker.com/develop/develop-images/baseimages/) dla odpowiedniej struktury:

- Aby wdrożyć aplikacje programu .NET Framework, użyj obrazu nadrzędnego opartego na wersji kanału [LTSC (Long-Term Servicing Channel) systemu](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) Windows Server Core 2019. 
- Aby wdrożyć aplikacje .NET Core, użyj obrazu nadrzędnego opartego na wersji [półrocznego kanału obsługi](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) systemu Windows Server Nano 1809 (SAC). 

Pobieranie obrazu nadrzędnego podczas uruchamiania aplikacji może zająć trochę czasu. Można jednak skrócić czas uruchamiania, korzystając z jednego z następujących obrazów nadrzędnych, które już zostały zbuforowane w usłudze Azure App Service:

- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/):4.7.2-windowsservercore-ltsc2019
- [mcr.microsoft.com/windows/nanoserver](https://hub.docker.com/_/microsoft-windows-nanoserver/):1809 - ten obraz jest podstawowym kontenerem używanym w obrazach Microsoft [ASP.NET Core](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/) Microsoft Windows Nano Server.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Migrowanie do kontenera systemu Windows na platformie Azure](app-service-web-tutorial-windows-containers-custom-fonts.md)
