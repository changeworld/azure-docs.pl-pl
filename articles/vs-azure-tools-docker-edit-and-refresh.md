---
title: Debugowanie aplikacji w kontenerze Docker lokalnym | Dokumentacja firmy Microsoft
description: Modyfikowanie aplikacji, która jest uruchomiona w kontenerze Docker lokalnego, Odśwież kontenera za pomocą edycji i Odśwież i ustaw punkty przerwania debugowania
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: ''
ms.assetid: 480e3062-aae7-48ef-9701-e4f9ea041382
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/22/2016
ms.author: mlearned
ms.openlocfilehash: 7df8bb2dd61466fd9af6492277d54cf0ffdfeb0b
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2017
ms.locfileid: "26781398"
---
# <a name="debugging-apps-in-a-local-docker-container"></a>Debugowanie aplikacji w lokalnym kontenerze platformy Docker
## <a name="overview"></a>Przegląd
Visual Studio Tools for Docker zapewnia spójny sposób rozwijać w i weryfikowanie aplikacji lokalnie w kontenerze Docker systemu Linux.
Nie trzeba ponownie uruchomić kontenera zawsze, gdy należy zmienić kod.
W tym artykule przedstawiono sposób Użyj funkcji "Edytuj i Odśwież", aby uruchomić aplikację sieci Web platformy ASP.NET Core w kontenerze Docker lokalnym, wprowadź niezbędne zmiany, a następnie odśwież przeglądarkę, aby zobaczyć wpływ zmian.
W tym artykule przedstawiono również sposób ustawić punkty przerwania dla debugowania.

> [!NOTE]
> Obsługa kontenera systemu Windows zostanie udostępniona w przyszłej wersji
>
>

## <a name="prerequisites"></a>Wymagania wstępne
Muszą być zainstalowane następujące narzędzia.

* [Najnowszą wersję programu Visual Studio](https://www.visualstudio.com/downloads/)
* [Microsoft ASP.NET Core 1.0 SDK](https://go.microsoft.com/fwlink/?LinkID=809122)

Aby uruchomić kontenery Docker lokalnie, należy klienta lokalnego docker.
Można użyć [przybornika Docker](https://www.docker.com/products/docker-toolbox), co wymaga funkcji Hyper-V jest wyłączona lub można użyć [Docker dla systemu Windows](https://www.docker.com/get-docker), który korzysta z funkcji Hyper-V i wymaga systemu Windows 10.

Jeśli przy użyciu rozwiązania Docker przybornika, konieczne będzie [Konfigurowanie klienta platformy Docker](vs-azure-tools-docker-setup.md)

## <a name="1-create-a-web-app"></a>1. Tworzenie aplikacji sieci Web
[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. Dodawanie obsługi Docker
[!INCLUDE [Add docker support](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-edit-your-code-and-refresh"></a>3. Edytuj kod i odświeżanie
Aby szybko przejść zmian, można uruchomić aplikację w kontenerze i kontynuować wprowadzanie zmian, ich wyświetlania, jak w przypadku z programem IIS Express.

1. Ustaw dla konfiguracji rozwiązania `Debug` i naciśnij klawisz  **&lt;CTRL + F5 >** do tworzenia obrazu docker i uruchom lokalnie.

    Po kontenera obraz został utworzony i działa w kontenerze Docker, Visual Studio uruchamia aplikację sieci Web w domyślnej przeglądarce.
    Jeśli używasz przeglądarki Microsoft Edge lub w przeciwnym razie zawiera błędy, zobacz [Rozwiązywanie problemów](vs-azure-tools-docker-troubleshooting-docker-errors.md) sekcji.
2. Przejdź do strony informacje, czyli gdzie zamierzamy naszych zmiany.
3. Wróć do programu Visual Studio i otworzyć `Views\Home\About.cshtml`.
4. Dodaj następującą zawartość HTML na końcu pliku, a następnie zapisz zmiany.

    ```
    <h1>Hello from a Docker Container!</h1>
    ```
5. Po ukończeniu kompilacji platformy .NET i wyświetlić te wiersze jest wyświetlany w oknie danych wyjściowych, wrócić do przeglądarki, a następnie odśwież stronę informacje.

   ```
   Now listening on: http://*:80
   Application started. Press Ctrl+C to shut down
   ```
6. Zmiany zostały zastosowane!

## <a name="4-debug-with-breakpoints"></a>4. Debugowanie przy użyciu punktów przerwania
Często zmiany będzie potrzebna jest dalsza inspekcję, wykorzystując funkcje debugowania programu Visual Studio.

1. Wróć do programu Visual Studio i Otwórz`Controllers\HomeController.cs`
2. Zamień zawartość metody About() następujące czynności:

   ```
   string message = "Your application description page from within a Container";
   ViewData["Message"] = message;
   ````
3. Ustaw punkt przerwania z lewej strony `string message`... wiersza.
4. Trafienia  **&lt;F5 >** można rozpocząć debugowania.
5. Przejdź do strony informacje do trafiony punkt przerwania.
6. Przełącz się do programu Visual Studio, aby wyświetlić punkt przerwania i sprawdzić wartość komunikatu.

   ![][2]

## <a name="summary"></a>Podsumowanie
Z [Visual Studio 2015 Tools for Docker](https://aka.ms/DockerToolsForVS), możesz uzyskać produktywność lokalnie, Praca z wzrostu produkcji rozwijających się w kontenerze Docker.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
[Rozwiązywanie problemów z tworzenia Docker Visual Studio](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## <a name="more-about-docker-with-visual-studio-windows-and-azure"></a>Więcej informacji na temat rozwiązania Docker z programu Visual Studio, Windows i Azure
* [Docker Tools for Visual Studio](http://aka.ms/dockertoolsforvs) — tworzenie kodu platformy .NET Core w kontenerze
* [Docker Tools for Visual Studio Team Services](http://aka.ms/dockertoolsforvsts) — tworzenie i wdrażanie kontenerów docker
* [Docker Tools for Visual Studio Code](http://aka.ms/dockertoolsforvscode) -języka usługi do edycji plików docker z scenariuszy e2e pochodzących
* [Informacje o kontenerze Windows](http://aka.ms/containers)— informacje o systemie Windows Server i Nano Server
* [Usługa kontenera platformy Azure](https://azure.microsoft.com/services/container-service/) - [zawartości usługi kontenera platformy Azure](http://aka.ms/AzureContainerService)
* Więcej przykładów dotyczących pracy z rozwiązaniem Docker, zobacz [pracy z rozwiązaniem Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) z [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect [pokaz](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Aby uzyskać więcej materiałów szybkiego startu z w wersji demonstracyjnej witryny HealthClinic.biz, zobacz [Azure Developer Tools Quickstarts](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts) (Materiały szybkiego startu narzędzi deweloperskich platformy Azure).

## <a name="various-docker-tools"></a>Różne narzędzia Docker
[Niektóre narzędzia dużą docker (blog Steve'a Lasker)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## <a name="good-articles"></a>Artykuły dobra
[Wprowadzenie do Mikrousług z NGINX](https://www.nginx.com/blog/introduction-to-microservices/)

## <a name="presentations"></a>Prezentacje
* [Steve Lasker: VS Wyszków 2016 - Docker e2e na żywo](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
* [Wprowadzenie do platformy ASP.NET Core @ kompilacji 2016 - gdzie możesz w pokaz](https://channel9.msdn.com/Events/Build/2016/B810)
* [Tworzenie aplikacji .NET w kontenerach, Channel 9](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png
