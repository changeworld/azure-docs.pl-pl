---
title: Debugowanie aplikacji w lokalnym kontenerze platformy Docker | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zmodyfikować aplikację, która jest uruchomiona w lokalnym kontenerze Docker, odśwież kontener za pomocą edytowania i odświeżania i ustawiania punktów przerwania do debugowania
services: azure-container-service
documentationcenter: na
author: ghogen
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
ms.openlocfilehash: 07a7c1e11d8ca20ff4f42abcb84961cb7cd9e0e1
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44298220"
---
# <a name="debugging-apps-in-a-local-docker-container"></a>Debugowanie aplikacji w lokalnym kontenerze platformy Docker
## <a name="overview"></a>Przegląd
Program Visual Studio 2017 zapewnia spójny sposób programowania w kontenerze platformy Docker w systemie Linux i zweryfikowania aplikacji w środowisku lokalnym.
Nie trzeba ponownie uruchomić kontener każdorazowo, gdy wprowadzeniu zmiana w kodzie.
W tym artykule pokazano, jak funkcja "Edytuj i Odśwież" Aby uruchomić aplikację sieci Web platformy ASP.NET Core w lokalnym kontenerze Docker, wprowadź niezbędne zmiany, a następnie odśwież przeglądarkę, aby zobaczyć zmiany.
W tym artykule przedstawiono również sposób ustawiania punktów przerwania do debugowania.

> [!NOTE]
> Obsługa kontenerów Windows zostanie udostępniona w przyszłej wersji
>
>

## <a name="prerequisites"></a>Wymagania wstępne
Muszą być zainstalowane następujące narzędzia.

* [Visual Studio 2017](https://www.visualstudio.com/downloads/)
* [Zestaw SDK Microsoft ASP.NET Core 1.0](https://go.microsoft.com/fwlink/?LinkID=809122)

Do uruchamiania kontenerów platformy Docker lokalnie, należy klient lokalnej platformy docker.
Możesz użyć [przybornika Docker](https://www.docker.com/products/docker-toolbox), które wymagają funkcji Hyper-V można wyłączyć lub można użyć [Docker for Windows](https://www.docker.com/get-docker), które korzysta z funkcji Hyper-V i wymaga systemu Windows 10.

Jeśli używasz przybornika platformy Docker, konieczne będzie [Konfigurowanie klienta platformy Docker](vs-azure-tools-docker-setup.md)

## <a name="1-create-a-web-app"></a>1. Tworzenie aplikacji internetowej
[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. Dodawanie obsługi platformy Docker
[!INCLUDE [Add docker support](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-edit-your-code-and-refresh"></a>3. Edytuj kod i odświeżanie
Możesz szybko iterować zmiany, możesz uruchomić aplikację w kontenerze i kontynuować wprowadzanie zmian, ich wyświetlania, jak w przypadku usługi IIS Express.

1. Ustaw konfigurację rozwiązania `Debug` i naciśnij klawisz  **&lt;klawiszy CTRL + F5 >** Aby skompilować obraz platformy docker i uruchomić go lokalnie.

    Gdy obraz kontenera został skompilowany i jest uruchomiona w kontenerze platformy Docker, Visual Studio uruchamia aplikację sieci Web w domyślnej przeglądarce.
    Jeśli używasz przeglądarki Microsoft Edge lub w przeciwnym razie błędami, zobacz [Rozwiązywanie problemów](vs-azure-tools-docker-troubleshooting-docker-errors.md) sekcji.
2. Przejdź do strony informacje, czyli, gdzie zamierzamy nasze zmiany.
3. Wróć do programu Visual Studio i Otwórz `Views\Home\About.cshtml`.
4. Dodaj następującą zawartość HTML do końca pliku, a następnie zapisz zmiany.

    ```
    <h1>Hello from a Docker Container!</h1>
    ```
5. Wyświetlanie w oknie danych wyjściowych po ukończeniu kompilacji platformy .NET i wyświetlić te wiersze, wróć do przeglądarki, a następnie odśwież stronę informacje.

   ```
   Now listening on: http://*:80
   Application started. Press Ctrl+C to shut down
   ```
6. Zmiany zostały zastosowane!

## <a name="4-debug-with-breakpoints"></a>4. Debugować z punktami przerwania
Często zmian będzie potrzebna jest dalsza inspekcji, korzystając z funkcji debugowania programu Visual Studio.

1. Wróć do programu Visual Studio i Otwórz `Controllers\HomeController.cs`
2. Zastąp zawartość metody About() następujących czynności:

   ```
   string message = "Your application description page from within a Container";
   ViewData["Message"] = message;
   ````
3. Ustaw punkt przerwania na lewo od `string message`... wiersza.
4. Trafienia  **&lt;F5 >** można rozpocząć debugowania.
5. Przejdź do strony informacje, aby trafić punkt przerwania.
6. Przełącz się do programu Visual Studio, aby wyświetlić punkt przerwania i sprawdzić wartość komunikatu.

   ![][2]

## <a name="summary"></a>Podsumowanie
Dzięki obsłudze platformy Docker w programie Visual Studio 2017 możesz uzyskać wydajność pracy lokalnie, za pomocą realizmu produkcji rozwijających się w kontenerze platformy Docker.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
[Rozwiązywanie problemów z programowanie Docker programu Visual Studio](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## <a name="more-about-docker-with-visual-studio-windows-and-azure"></a>Więcej informacji na temat platformy Docker przy użyciu programu Visual Studio, Windows i platformy Azure
* [Narzędzia aparatu docker dla usługi Azure DevOps](http://aka.ms/dockertoolsforvsts) — umożliwia tworzenie i wdrażanie kontenerów aparatu docker
* [Narzędzia aparatu docker dla programu Visual Studio Code](http://aka.ms/dockertoolsforvscode) — usługi w języka do edycji plików docker przy użyciu scenariuszy e2e mieszczących się
* [Informacje o kontenerze Windows](http://aka.ms/containers)— informacje o systemie Windows Server i serwera Nano Server
* [Usługa Azure Container Service](https://azure.microsoft.com/services/container-service/) - [zawartości usługi kontenera platformy Azure](http://aka.ms/AzureContainerService)
* Aby uzyskać więcej przykładów korzystania z platformy Docker, zobacz [pracy z platformą Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) z [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect witryny [pokaz](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Aby uzyskać więcej materiałów szybkiego startu z w wersji demonstracyjnej witryny HealthClinic.biz, zobacz [Azure Developer Tools Quickstarts](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts) (Materiały szybkiego startu narzędzi deweloperskich platformy Azure).

## <a name="various-docker-tools"></a>Różne narzędzia programu Docker
[Niektóre narzędzia docker doskonałe (blog Steve'a Lasker)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## <a name="good-articles"></a>Dobre artykuły
[Wprowadzenie do Mikrousług z NGINX](https://www.nginx.com/blog/introduction-to-microservices/)

## <a name="presentations"></a>Prezentacje
* [Steve Lasker: VS Live Las Vegas 2016 - e2e platformy Docker](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
* [Wprowadzenie do platformy ASP.NET Core @ build 2016 — której możesz w wersji demonstracyjnej](https://channel9.msdn.com/Events/Build/2016/B810)
* [Tworzenie aplikacji .NET w kontenerach, Channel 9](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png
