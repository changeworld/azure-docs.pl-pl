---
title: Usługa Azure Service Fabric za pomocą programu VS Code wprowadzenie | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono omówienie procedury tworzenia aplikacji usługi Service Fabric przy użyciu programu Visual Studio Code.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 1053bde5eb76a9e2731d9663dec97d51c321e907
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500223"
---
# <a name="service-fabric-for-visual-studio-code"></a>Service Fabric for Visual Studio Code

[Rozszerzenie usług Reliable Services usługi Service Fabric dla programu VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) udostępnia narzędzia niezbędne do tworzenia, kompilowania i debugowania aplikacji usługi Service Fabric w systemach operacyjnych Windows, Linux i macOS.

Ten artykuł zawiera omówienie wymagań i instalacji rozszerzenia, a także użycie różnych poleceń, które są dostarczane przez rozszerzenie. 

> [!IMPORTANT]
> Aplikacje Java usługi Service Fabric mogą być tworzone na maszynach Windows, ale mogą być rozmieszczane tylko klastry z systemem Linux platformy Azure. Debugowanie aplikacji Java nie jest obsługiwana na Windows.

## <a name="prerequisites"></a>Wymagania wstępne

Następujące wymagania wstępne, musi być zainstalowany na wszystkich środowisk.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Usługa Git](https://git-scm.com/)
* [Zestaw SDK usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)
* Generatory narzędzia yeoman — instalowanie generatorów odpowiednie dla twojej aplikacji

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Muszą być zainstalowane następujące wstępnie wymagane składniki do tworzenia aplikacji Java:

* [Zestaw SDK platformy Java](https://aka.ms/azure-jdks) (wersja 1.8)
* Narzędzie [Gradle](https://gradle.org/install/)
* [Debuger dla rozszerzenia kodu programu VS Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) potrzebnych do debugowania usług Java. Debugowanie usługi Java jest obsługiwane w systemie Linux tylko. Można to zrobić, klikając ikonę rozszerzenia w **pasku działań** programu VS Code i rozszerzenia lub z programu VS Code Marketplace.

Należy zainstalować następujące wymagania wstępne dla platformy .NET Core /C# programowania:

* [.NET core](https://www.microsoft.com/net/learn/get-started) (w wersji 2.0.0 lub nowszy)
* [C#rozszerzenia programu Visual Studio Code (obsługiwane przez technologię OmniSharp) programu VS Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) potrzebnych do debugowania C# usług. Można to zrobić, klikając ikonę rozszerzenia w **pasku działań** programu VS Code i rozszerzenia lub z programu VS Code Marketplace.

## <a name="setup"></a>Konfigurowanie

1. Open VS Code.
2. Kliknij ikonę rozszerzenia **pasku działań** po lewej stronie programu VS Code. Wyszukaj frazę "Service Fabric". Kliknij przycisk **zainstalować** rozszerzenia usług Reliable Services usługi Service Fabric.

## <a name="commands"></a>Polecenia
Rozszerzenie usług Reliable Services usługi Service Fabric dla programu VS Code zawiera wiele poleceń, aby pomóc deweloperom tworzyć i wdrażać projekty usługi Service Fabric. Można wywołać polecenia z **paletę poleceń** , naciskając klawisz `(Ctrl + Shift + p)`, wpisując nazwę polecenia pasek danych wejściowych, a wybranie odpowiedniego polecenia z listy monitów. 

* Service Fabric: Tworzenie aplikacji 
* Service Fabric: Publikowanie aplikacji 
* Service Fabric: Wdrażanie aplikacji 
* Service Fabric: Usuń aplikację  
* Service Fabric: Tworzenie aplikacji 
* Service Fabric: Czyszczenie aplikacji 

### <a name="service-fabric-create-application"></a>Service Fabric: Tworzenie aplikacji

**Usługi Service Fabric: Tworzenie aplikacji** polecenie tworzy nową aplikację usługi Service Fabric w bieżącym obszarze roboczym. W zależności od tego, które yeoman generatory są zainstalowane na komputerze deweloperskim, można utworzyć kilka typów aplikacji usługi Service Fabric, w tym Java, C#, kontener i projektów gościa. 

1.  Wybierz **usługi Service Fabric: Dodaj usługę** polecenia
2.  Wybierz typ nowej aplikacji usługi Service Fabric. 
3.  Wprowadź nazwę aplikacji, którą chcesz utworzyć
3.  Wybierz typ usługi, którą chcesz dodać do aplikacji usługi Service Fabric. 
4.  Postępuj zgodnie z monitami, aby nazwa usługi. 
5.  Nowa aplikacja usługi Service Fabric zostanie wyświetlony w obszarze roboczym.
6.  Otwórz ten nowy folder aplikacji, tak aby stał się folderze głównym w obszarze roboczym. Można kontynuować wykonywania poleceń w tym miejscu.

### <a name="service-fabric-add-service"></a>Service Fabric: Dodaj usługę
**Usługi Service Fabric: Dodaj usługę** polecenie dodaje nową usługę do istniejącej aplikacji usługi Service Fabric. Usługa zostanie dodana do aplikacji musi być katalog główny, w obszarze roboczym. 

1.  Wybierz **usługi Service Fabric: Dodaj usługę** polecenia.
2.  Wybierz typ obecnie używaną aplikację usługi Service Fabric. 
3.  Wybierz typ usługi, którą chcesz dodać do aplikacji usługi Service Fabric. 
4.  Postępuj zgodnie z monitami, aby nazwa usługi. 
5.  Nowa usługa pojawia się w katalogu projektu. 

### <a name="service-fabric-publish-application"></a>Service Fabric: Publikowanie aplikacji
**Usługi Service Fabric: Opublikuj aplikację** polecenie służy do wdrażania aplikacji usługi Service Fabric w klastrze zdalnym. Klaster docelowy może być bezpieczny lub niezabezpieczonym klastrem. Jeśli nie ustawiono parametrów w Cloud.json, aplikacja jest wdrażana w klastrze lokalnym.

1.  Przy pierwszym aplikacja jest skompilowana, generowany jest plik Cloud.json w katalogu projektu.
2.  Wprowadź wartości dla klastra, który chcesz połączyć się w pliku Cloud.json.
3.  Wybierz **usługi Service Fabric: Opublikuj aplikację** polecenia.
4.  Wyświetl klastra docelowego przy użyciu narzędzia Service Fabric Explorer, aby upewnić się, że aplikacja została zainstalowana. 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric: Wdrażanie aplikacji (Localhost)
**Usługi Service Fabric: Wdrażanie aplikacji** polecenie służy do wdrażania aplikacji usługi Service Fabric w klastrze lokalnym. Upewnij się, że klaster lokalny jest uruchomiony przed użyciem polecenia. 

1. Wybierz **usługi Service Fabric: Wdrażanie aplikacji** polecenia
2. Wyświetlanie klastra lokalnego przy użyciu narzędzia Service Fabric Explorer (http:\//localhost:19080 / Explorer) aby upewnić się, że aplikacja została zainstalowana. To może trochę potrwać, więc o cierpliwość.
3. Można również użyć **usługi Service Fabric: Opublikuj aplikację** polecenia bez parametrów, ustawione w pliku Cloud.json do wdrożenia w lokalnym klastrze.

> [!NOTE]
> Wdrażanie aplikacji Java w klastrze lokalnym nie jest obsługiwane na maszynach Windows.

### <a name="service-fabric-remove-application"></a>Service Fabric: Usuń aplikację
**Usługi Service Fabric: Usuń aplikację** polecenie usuwa aplikację usługi Service Fabric z klastra, aby była wcześniej wdrożona za pomocą rozszerzenia programu VS Code. 

1.  Wybierz **usługi Service Fabric: Usuń aplikację** polecenia.
2.  Wyświetl klastra przy użyciu narzędzia Service Fabric Explorer, aby upewnić się, że aplikacja została usunięta. To może trochę potrwać, więc o cierpliwość.

### <a name="service-fabric-build-application"></a>Service Fabric: Tworzenie aplikacji
**Usługi Service Fabric: Usuń aplikację** polecenia można utworzyć albo Java lub C# aplikacji usługi Service Fabric. 

1.  Upewnij się, że znajdują się w folderze głównym aplikacji przed wykonaniem tego polecenia. To polecenie identyfikuje typ aplikacji (C# lub Java) i w związku z tym kompilacji aplikacji.
2.  Wybierz **usługi Service Fabric: Tworzenie aplikacji** polecenia.
3.  Dane wyjściowe z procesu kompilacji są zapisywane w zintegrowanym terminalu.

### <a name="service-fabric-clean-application"></a>Service Fabric: Czyszczenie aplikacji
**Usługi Service Fabric: Czyszczenie aplikacji** polecenie usuwa wszystkie pliki jar i natywnych bibliotek, które zostały wygenerowane przez kompilację. Prawidłowe dla tylko aplikacji w języku Java. 

1.  Upewnij się, że znajdują się w folderze głównym aplikacji przed wykonaniem tego polecenia. 
2.  Wybierz **usługi Service Fabric: Czyszczenie aplikacji** polecenia.
3.  Dane wyjściowe czyste procesu są zapisywane zintegrowany terminal programu.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [programowanie i debugowanie C# aplikacji usługi Service Fabric za pomocą programu VS Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Dowiedz się, jak [programowanie i debugowanie aplikacji Java usługi Service Fabric za pomocą programu VS Code](./service-fabric-develop-java-applications-with-vs-code.md).
