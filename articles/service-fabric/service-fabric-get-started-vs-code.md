---
title: Usługa Azure Service Fabric z wprowadzeniem kodu VS
description: Ten artykuł jest omówieniem tworzenia aplikacji sieci szkieletowej usług przy użyciu kodu programu Visual Studio.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 17bff459a0399d39c6bfdd772ad16d0b5b2f6771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258488"
---
# <a name="service-fabric-for-visual-studio-code"></a>Sieci szkieletowej usług dla programu Visual Studio Code

[Rozszerzenie Niezawodne usługi sieci szkieletowej usług dla programu VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) udostępnia narzędzia niezbędne do tworzenia, tworzenia i debugowania aplikacji sieci szkieletowej usług w systemach operacyjnych Windows, Linux i macOS.

Ten artykuł zawiera omówienie wymagań i konfiguracji rozszerzenia, a także użycie różnych poleceń, które są dostarczane przez rozszerzenie. 

> [!IMPORTANT]
> Aplikacje Java sieci szkieletowej usług można rozwijać na komputerach z systemem Windows, ale można je wdrożyć tylko w klastrach systemu Azure Linux. Debugowanie aplikacji Java nie jest obsługiwane w systemie Windows.

## <a name="prerequisites"></a>Wymagania wstępne

Następujące wymagania wstępne muszą być zainstalowane we wszystkich środowiskach.

* [Kod programu Visual Studio](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [SDK sieci szkieletowej usług](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)
* Generatory Yeoman - zainstaluj odpowiednie generatory dla twojej aplikacji

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Następujące wymagania wstępne muszą być zainstalowane dla programowania java:

* [Java SDK](https://aka.ms/azure-jdks) (wersja 1.8)
* [Gradle](https://gradle.org/install/)
* [Debuger dla rozszerzenia kodu Java VS](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) Potrzebne do debugowania usług Java. Debugowanie usług Java jest obsługiwane tylko w systemie Linux. Można zainstalować, klikając ikonę Rozszerzenia na **pasku aktywności** w programie VS Code i wyszukując rozszerzenie lub w portalu VS Code Marketplace.

Następujące wymagania wstępne muszą być zainstalowane dla rozwoju .NET Core/C#:

* [.NET Core](https://www.microsoft.com/net/learn/get-started) (wersja 2.0.0 lub nowsza)
* [C# dla programu Visual Studio Code (obsługiwane przez OmniSharp) rozszerzenie kodu VS](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) Potrzebne do debugowania usług C#. Można zainstalować, klikając ikonę Rozszerzenia na **pasku aktywności** w programie VS Code i wyszukując rozszerzenie lub w portalu VS Code Marketplace.

## <a name="setup"></a>Konfiguracja

1. Otwórz kod VS.
2. Kliknij ikonę Rozszerzenia na pasku **aktywności** po lewej stronie programu VS Code. Wyszukaj hasło "Tkanina usługowa". Kliknij **przycisk Zainstaluj** dla rozszerzenia niezawodne usługi sieci szkieletowej usług.

## <a name="commands"></a>Polecenia
Rozszerzenie niezawodne usługi sieci szkieletowej usług dla programu VS Code zawiera wiele poleceń ułatwiające deweloperom tworzenie i wdrażanie projektów sieci szkieletowej usług. Polecenia można wywołać z **palety poleceń,** naciskając , `(Ctrl + Shift + p)`wpisując nazwę polecenia na pasku wejściowym i wybierając żądane polecenie z listy wierszy. 

* Sieć szkieletowa usług: tworzenie aplikacji 
* Sieci szkieletowej usług: publikowanie aplikacji 
* Sieci szkieletowej usług: wdrażanie aplikacji 
* Sieci szkieletowej usług: Usuń aplikację  
* Sieci szkieletowej usług: tworzenie aplikacji 
* Tkanina usługowa: Czysta aplikacja 

### <a name="service-fabric-create-application"></a>Sieć szkieletowa usług: tworzenie aplikacji

Polecenie **Sieć szkieletowa usług: Tworzenie aplikacji** tworzy nową aplikację sieci szkieletowej usług w bieżącym obszarze roboczym. W zależności od tego, które generatory yeoman są zainstalowane na komputerze deweloperskim, można utworzyć kilka typów aplikacji sieci szkieletowej usług, w tym Java, C#, Container i Guest projektów. 

1.  Wybierz polecenie **Sieć szkieletowa usług: Utwórz aplikację**
2.  Wybierz typ nowej aplikacji sieci szkieletowej usług. 
3.  Wprowadź nazwę aplikacji, którą chcesz utworzyć
3.  Wybierz typ usługi, którą chcesz dodać do aplikacji sieci szkieletowej usług. 
4.  Postępuj zgodnie z instrukcjami, aby nadać nazwę usłudze. 
5.  Nowa aplikacja sieci szkieletowej usług pojawi się w obszarze roboczym.
6.  Otwórz nowy folder aplikacji, aby stał się folderem głównym w obszarze roboczym. Można kontynuować wykonywanie poleceń z tego miejsca.

### <a name="service-fabric-add-service"></a>Sieci szkieletowej usług: Dodaj usługę
Polecenie **Service Fabric: Add Service** dodaje nową usługę do istniejącej aplikacji sieci szkieletowej usług. Aplikacja, do których zostanie dodana usługa, musi być katalogiem głównym obszaru roboczego. 

1.  Wybierz polecenie **Sieci szkieletowej usług: Dodaj usługę.**
2.  Wybierz typ bieżącej aplikacji sieci szkieletowej usług. 
3.  Wybierz typ usługi, którą chcesz dodać do aplikacji sieci szkieletowej usług. 
4.  Postępuj zgodnie z instrukcjami, aby nadać nazwę usłudze. 
5.  Nowa usługa pojawi się w katalogu projektu. 

### <a name="service-fabric-publish-application"></a>Sieci szkieletowej usług: publikowanie aplikacji
Polecenie **Sieci szkieletowej usług: Publikowanie aplikacji** wdraża aplikację sieci szkieletowej usług w klastrze zdalnym. Klaster docelowy może być klasterem bezpiecznym lub niezabezpieczonym. Jeśli parametry nie są ustawione w pliku Cloud.json, aplikacja jest wdrażana w klastrze lokalnym.

1.  Przy pierwszym łożeniu aplikacji plik Cloud.json jest generowany w katalogu projektu.
2.  Wprowadź wartości klastra, z którego chcesz się połączyć, w pliku Cloud.json.
3.  Wybierz polecenie **Sieci szkieletowej usług: Publikuj aplikację.**
4.  Wyświetl klaster docelowy za pomocą Eksploratora sieci szkieletowej usług, aby potwierdzić, że aplikacja została zainstalowana. 

### <a name="service-fabric-deploy-application-localhost"></a>Sieć szkieletowa usług: wdrażanie aplikacji (localhost)
**Polecenie Sieć szkieletowa usług: Wdrażanie aplikacji** wdraża aplikację sieci szkieletowej usług w klastrze lokalnym. Upewnij się, że klaster lokalny jest uruchomiony przed użyciem polecenia. 

1. Wybierz polecenie **Sieci szkieletowej usług: Wdrażanie aplikacji**
2. Wyświetl klaster lokalny za pomocą\/Eksploratora sieci szkieletowej usług (http: /localhost:19080/Explorer), aby potwierdzić, że aplikacja została zainstalowana. Może to zająć trochę czasu, więc bądź cierpliwy.
3. Można również użyć **polecenia Sieci szkieletowej usług: Publikuj aplikację** bez parametrów ustawionych w pliku Cloud.json do wdrożenia w klastrze lokalnym.

> [!NOTE]
> Wdrażanie aplikacji Java w klastrze lokalnym nie jest obsługiwane na komputerach z systemem Windows.

### <a name="service-fabric-remove-application"></a>Sieci szkieletowej usług: Usuń aplikację
Polecenie **Sieci szkieletowej usług: Usuń aplikację** usuwa aplikację sieci szkieletowej usług z klastra, który został wcześniej wdrożony przy użyciu rozszerzenia kodu programu VS. 

1.  Wybierz polecenie **Sieci szkieletowej usług: Usuń aplikację.**
2.  Wyświetl klaster za pomocą Eksploratora sieci szkieletowej usług, aby potwierdzić, że aplikacja została usunięta. Może to zająć trochę czasu, więc bądź cierpliwy.

### <a name="service-fabric-build-application"></a>Sieci szkieletowej usług: tworzenie aplikacji
Polecenie **Sieci szkieletowej usług: Kompilacja aplikacji** może tworzyć aplikacje sieci szkieletowej usług Java lub C#. 

1.  Przed wykonaniem tego polecenia upewnij się, że znajdujesz się w folderze głównym aplikacji. Polecenie identyfikuje typ aplikacji (C# lub Java) i odpowiednio tworzy aplikację.
2.  Wybierz polecenie **Sieci szkieletowej usług: Tworzenie aplikacji.**
3.  Dane wyjściowe procesu kompilacji są zapisywane na zintegrowanym terminalu.

### <a name="service-fabric-clean-application"></a>Tkanina usługowa: Czysta aplikacja
Polecenie **Sieci szkieletowej usług: Czysta aplikacja** usuwa wszystkie pliki jar i biblioteki macierzyste, które zostały wygenerowane przez kompilację. Obowiązuje tylko dla aplikacji Java. 

1.  Przed wykonaniem tego polecenia upewnij się, że znajdujesz się w folderze głównym aplikacji. 
2.  Wybierz polecenie **Usługodawca: Czysta aplikacja.**
3.  Wyjście czystego procesu jest zapisywane na zintegrowanym terminalu.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [tworzyć i debugować aplikacje sieci szkieletowej usług C# za pomocą programu VS Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Dowiedz się, jak [tworzyć i debugować aplikacje sieci szkieletowej usług Java za pomocą programu VS Code](./service-fabric-develop-java-applications-with-vs-code.md).
