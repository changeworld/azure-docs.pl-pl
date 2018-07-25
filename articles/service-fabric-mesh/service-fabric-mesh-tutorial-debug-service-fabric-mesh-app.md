---
title: 'Samouczek: debugowanie aplikacji internetowej usługi Azure Service Fabric Mesh | Microsoft Docs'
description: W tym samouczku zdebugujemy aplikację usługi Azure Service Fabric Mesh uruchomioną w klastrze lokalnym.
services: service-fabric-mesh
documentationcenter: .net
author: TylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: e39e5861c0606c43a869fe02a7de2dc9b6f489ea
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125657"
---
# <a name="tutorial-debug-a-service-fabric-mesh-web-application"></a>Samouczek: debugowanie aplikacji internetowej usługi Service Fabric Mesh

Ten samouczek to druga część serii. Przedstawiono w nim sposób debugowania aplikacji internetowej usługi Azure Service Fabric Mesh w lokalnym klastrze projektowym.

Ten samouczek zawiera następujące informacje:

> [!div class="checklist"]
> * Co się dzieje w przypadku utworzenia aplikacji usługi Azure Service Fabric Mesh
> * Jak ustawić punkt przerwania umożliwiający zaobserwowanie wywołania między usługami

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * [Tworzenie aplikacji usługi Service Fabric Mesh](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * Lokalne debugowanie aplikacji
> * [Publikowanie aplikacji na platformie Azure](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem możesz [utworzyć bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Upewnij się, że masz [skonfigurowane środowisko projektowe](service-fabric-mesh-howto-setup-developer-environment-sdk.md) z zainstalowanym środowiskiem uruchomieniowym usługi Service Fabric, zestawem SDK, platformą Docker i programem Visual Studio 2017.

## <a name="download-the-to-do-sample-application"></a>Pobieranie przykładowej aplikacji z zadaniami do wykonania

Jeśli nie utworzono przykładowej aplikacji z zadaniami do wykonania w [pierwszej części tej serii samouczków](service-fabric-mesh-tutorial-create-dotnetcore.md), można ją pobrać. W oknie polecenia uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na komputer lokalny.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

Aplikacja znajduje się w katalogu `src\todolistapp`.

## <a name="build-and-debug-on-your-local-cluster"></a>Kompilowanie i debugowanie w klastrze lokalnym

Zaraz po załadowaniu projektu obraz platformy Docker jest automatycznie kompilowany i wdrażany w klastrze lokalnym. Ten proces może chwilę potrwać. Aby monitorować postęp w okienku **Dane wyjściowe** programu Visual Studio, wybierz opcję **Service Fabric Tools** na liście rozwijanej **Pokaż dane wyjściowe z:**.

Naciśnij klawisz **F5**, aby skompilować usługę i uruchomić ją lokalnie. W przypadku lokalnego uruchamiania i debugowania projektu w programie Visual Studio są wykonywane następujące działania:

* Zapewnienie uruchomienia platformy Docker for Windows i wybrania systemu Windows jako systemu operacyjnego kontenera.
* Pobranie wszystkich brakujących obrazów podstawowych platformy Docker. Może to potrwać jakiś czas.
* Skompilowanie lub ponowne skompilowanie obrazu platformy Docker używanego do hostowania projektu kodu.
* Wdrożenie i uruchomienie kontenera w lokalnym klastrze projektowym usługi Service Fabric.
* Uruchomienie usług z uwzględnieniem wszystkich ustawionych punktów przerwania.

Po zakończeniu lokalnego wdrożenia i uruchomieniu aplikacji w programie Visual Studio zostanie otwarte okno przeglądarki z wyświetloną domyślną, przykładową stroną internetową.

**Wskazówki dotyczące debugowania**

* Jeśli pojawi się błąd **Nie uruchomiono klastra lokalnego usługi Service Fabric**, upewnij się, że uruchomiono menedżera klastra lokalnego usługi (SLCM), a następnie kliknij prawym przyciskiem myszy ikonę menedżera SLCM na pasku zadań i kliknij polecenie **Uruchom klaster lokalny**. Po uruchomieniu klastra wróć do programu Visual Studio i naciśnij klawisz **F5**.
* Jeśli po uruchomieniu aplikacji pojawi się błąd **404**, prawdopodobnie oznacza to, że zmienne środowiskowe w pliku **service.yaml** są nieprawidłowe. Upewnij się, że zmienne `AppName`, `ApiHostPort` i `ServiceName` są ustawione zgodnie z instrukcjami zawartymi w sekcji [Ustawianie zmiennych środowiskowych](#set-environment-variables).
* Jeśli wystąpią błędy kompilacji pliku **service.yaml**, upewnij się, że do wcięcia wierszy użyto spacji, a nie znaków tabulacji.

### <a name="debug-in-visual-studio"></a>Debugowanie w programie Visual Studio

Podczas debugowania aplikacji usługi Service Fabric Mesh w programie Visual Studio używany jest lokalny klaster projektowy usługi Service Fabric. Aby zobaczyć, jak elementy do wykonania są pobierane z usługi zaplecza, przeprowadź debugowanie metody OnGet().
1. W projekcie **WebFrontEnd** otwórz plik **Strony** > **Index.cshtml** > **Index.cshtml.cs** i ustaw punkt przerwania w metodzie **Get** (wiersz 17).
2. W projekcie **ToDoService** otwórz plik **TodoController.cs** i ustaw punkt przerwania w metodzie **OnGet** (wiersz 15).
3. Odśwież stronę w przeglądarce. Działanie aplikacji zostanie zatrzymane w punkcie przerwania w metodzie `OnGet()` frontonu internetowego. Możesz zbadać wartość zmiennej `backendUrl`, aby sprawdzić, w jaki sposób zmienne środowiskowe zdefiniowane w pliku **service.yaml** zostały połączone w adres URL używany do komunikacji z usługą zaplecza.
4. Pomiń (F10) wywołanie `client.GetAsync(backendUrl).GetAwaiter().GetResult())`. Działanie aplikacji zatrzyma się w punkcie przerwania `Get()` kontrolera. W tej metodzie możesz zobaczyć, jak lista elementów do wykonania jest pobierana z listy w pamięci.
5. Gdy skończysz, zatrzymaj debugowanie projektu w programie Visual Studio, naciskając klawisze **Shift+F5**.
 
## <a name="next-steps"></a>Następne kroki

W tej części samouczka przedstawiono informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Co się dzieje w przypadku utworzenia aplikacji usługi Azure Service Fabric Mesh
> * Jak ustawić punkt przerwania umożliwiający zaobserwowanie wywołania między usługami

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Publikowanie aplikacji internetowej usługi Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)