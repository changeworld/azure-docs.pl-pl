---
title: Sieć szkieletowa usług Azure z kodem VS wprowadzenie | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie tworzenia aplikacji platformy Service Fabric przy użyciu programu Visual Studio Code.
services: service-fabric
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: v-jamebr
ms.openlocfilehash: 367829c269bd1d96e6aa5fab1be008483a4ab5ab
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37116067"
---
# <a name="service-fabric-for-visual-studio-code"></a>Sieć szkieletowa usług dla kodu programu Visual Studio

[Rozszerzenia usługi sieci szkieletowej niezawodnych usług VS kodu](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) udostępnia narzędzia niezbędne do tworzenia, tworzenie i debugowanie aplikacji sieci szkieletowej usług w systemach operacyjnych Windows, Linux lub macOS.

Ten artykuł zawiera omówienie wymagań i konfiguracji rozszerzenia, a także użycia poszczególnych poleceń, które są udostępniane przez rozszerzenie. 

> [!IMPORTANT]
> Usługa sieci szkieletowej Java aplikacje mogą być opracowane na komputerach z systemem Windows, ale mogą być rozmieszczane tylko klastry z systemem Linux Azure. Debugowanie aplikacji Java nie jest obsługiwane w systemie Windows.

## <a name="prerequisites"></a>Wymagania wstępne

Następujące wymagania wstępne musi być zainstalowany na wszystkich środowisk.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [Usługa SDK sieci szkieletowej](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)
* Generatory narzędzia yeoman — zainstalować odpowiednie generatory aplikacji

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Do tworzenia aplikacji Java muszą być zainstalowane następujące wymagania wstępne:

* [Zestaw SDK Java](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (w wersji 1.8)
* [Narzędzia Gradle](https://gradle.org/install/)
* [Debuger dla kodu języka Java VS rozszerzenia](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) wymagane do debugowania usług Java. Debugowanie usług Java jest obsługiwany w systemie Linux tylko. Można zainstalować, klikając ikonę rozszerzenia w **działania pasek** w kodzie VS i wyszukiwania rozszerzenia lub z witryny Marketplace kodu programu VS.

Muszą być zainstalowane następujące wymagania wstępne platformy .NET Core / programowania w języku C#:

* [Oprogramowanie .NET core](https://www.microsoft.com/net/learn/get-started) (wersji 2.0.0 lub nowszy)
* [C# dla rozszerzenia Visual Studio Code (obsługiwane przez OmniSharp) kodzie VS](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) wymagane do debugowania usług C#. Można zainstalować, klikając ikonę rozszerzenia w **działania pasek** w kodzie VS i wyszukiwania rozszerzenia lub z witryny Marketplace kodu programu VS.

## <a name="setup"></a>Konfigurowanie

1. Otwórz VS kodu.
2. Kliknij ikonę rozszerzenia w **działania pasek** po lewej stronie kodzie VS. Wyszukaj "Sieci szkieletowej usług". Kliknij przycisk **zainstalować** rozszerzenia usługi sieci szkieletowej niezawodne usługi.

## <a name="commands"></a>Polecenia
Rozszerzenie usługi sieci szkieletowej niezawodnych usług VS kodu udostępnia wiele poleceń, aby pomóc deweloperom tworzenie i wdrażanie projektów sieci szkieletowej usług. Możesz wywołać poleceń z **palety polecenia** naciskając `(Ctrl + Shift + p)`, wpisując nazwę polecenia na pasku wejściowego i wybranie odpowiedniego polecenia z listy monitu. 

* Usługa sieci szkieletowej: Tworzenie aplikacji 
* Usługa sieci szkieletowej: Publikowanie aplikacji 
* Usługa sieci szkieletowej: Wdrażanie aplikacji 
* Usługa sieci szkieletowej: Usuwanie aplikacji  
* Usługa sieci szkieletowej: Tworzenie aplikacji 
* Usługi Service Fabric: Wyczyść aplikacji 

### <a name="service-fabric-create-application"></a>Usługa sieci szkieletowej: Tworzenie aplikacji

**Usługi Service Fabric: tworzenie aplikacji** polecenie tworzy nową aplikację platformy Service Fabric w bieżącym obszarze roboczym. W zależności od tego, które narzędzia yeoman generatory są zainstalowane na komputerze deweloperskim można utworzyć kilka typów aplikacji sieci szkieletowej usług, w tym projekty języka Java, C#, kontenera i gościa. 

1.  Wybierz **usługi Service Fabric: Dodaj usługę** polecenia
2.  Wybierz typ dla nowej aplikacji sieci szkieletowej usług. 
3.  Wprowadź nazwę aplikacji, którą chcesz utworzyć
3.  Wybierz typ usługi, którą chcesz dodać do aplikacji sieci szkieletowej usług. 
4.  Postępuj zgodnie z monitami, aby nazwa usługi. 
5.  Nowa aplikacja usługi Service Fabric pojawi się w obszarze roboczym.
6.  Otwórz nowy folder aplikacji tak, aby stał się folderu głównego w obszarze roboczym. Można kontynuować wykonywania poleceń w tym miejscu.

### <a name="service-fabric-add-service"></a>Usługa sieci szkieletowej: Dodawanie usługi
**Usługi Service Fabric: Dodaj usługę** polecenie dodaje nową usługę do istniejącej aplikacji sieci szkieletowej usług. Usługa nie zostanie dodany do aplikacji musi być katalog główny obszaru roboczego. 

1.  Wybierz **usługi Service Fabric: Dodaj usługę** polecenia.
2.  Wybierz typ bieżącej aplikacji sieci szkieletowej usług. 
3.  Wybierz typ usługi, którą chcesz dodać do aplikacji sieci szkieletowej usług. 
4.  Postępuj zgodnie z monitami, aby nazwa usługi. 
5.  Nowa usługa pojawia się w katalogu projektu. 

### <a name="service-fabric-publish-application"></a>Usługa sieci szkieletowej: Publikowanie aplikacji
**Usługi Service Fabric: publikowanie aplikacji** polecenia wdraża aplikacji w zdalnym klastrze usługi sieć szkieletowa usług. Klaster docelowy może być bezpiecznej lub niezabezpieczone klastra. Jeśli nie ustawiono parametrów w Cloud.json, aplikacja jest wdrażana na lokalny klaster.

1.  Przy pierwszym wbudowanej aplikacji pliku Cloud.json jest generowany w katalogu projektu.
2.  Wprowadź wartości dla klastra, który chcesz połączyć się w pliku Cloud.json.
3.  Wybierz **usługi Service Fabric: publikowanie aplikacji** polecenia.
4.  Wyświetl klastra docelowego o Service Fabric Explorer, aby upewnić się, że aplikacja została zainstalowana. 

### <a name="service-fabric-deploy-application-localhost"></a>Usługa sieci szkieletowej: Wdrażanie aplikacji (Localhost)
**Usługi Service Fabric: Wdrażanie aplikacji** polecenia wdraża aplikacji sieci szkieletowej usług w klastrze lokalnym. Upewnij się, że w klastrze lokalnym działa przed użyciem polecenia. 

1.  Wybierz **usługi Service Fabric: Wdrażanie aplikacji** polecenia
2.  Wyświetl klastra lokalnego z Eksploratora usługi sieć szkieletowa (http://localhost:19080/Explorer) aby upewnić się, że aplikacja została zainstalowana. Może to zająć trochę czasu, dlatego cierpliwość.
3.  Można również użyć **usługi Service Fabric: publikowanie aplikacji** polecenia bez parametrów ustawione w pliku Cloud.json do wdrożenia z lokalnym klastrem.

> [!NOTE]
> Wdrożenie aplikacji Java na lokalny klaster nie jest obsługiwana na komputerach z systemem Windows.

### <a name="service-fabric-remove-application"></a>Usługa sieci szkieletowej: Usuwanie aplikacji
**Usługi Service Fabric: usuwanie aplikacji** polecenia powoduje usunięcie aplikacji usługi Service Fabric z klastra, aby była wcześniej wdrożona przy użyciu rozszerzenia kodzie VS. 

1.  Wybierz **usługi Service Fabric: usuwanie aplikacji** polecenia.
2.  Wyświetl klaster z Service Fabric Explorer, aby upewnić się, że aplikacja została usunięta. Może to zająć trochę czasu, dlatego cierpliwość.

### <a name="service-fabric-build-application"></a>Usługa sieci szkieletowej: Tworzenie aplikacji
**Usługi Service Fabric: usuwanie aplikacji** polecenia można tworzyć aplikacje Java lub C# sieci szkieletowej usług. 

1.  Upewnij się, że znajdują się w folderze głównym aplikacji przed wykonaniem tego polecenia. Polecenie identyfikuje typu aplikacji (C# lub języka Java) i w związku z tym tworzy aplikacji.
2.  Wybierz **usługi Service Fabric: tworzenie aplikacji** polecenia.
3.  Dane wyjściowe z procesu kompilacji są zapisywane w zintegrowanym terminala.

### <a name="service-fabric-clean-application"></a>Usługi Service Fabric: Wyczyść aplikacji
**Usługi Service Fabric: czystą aplikacji** polecenie usuwa wszystkie pliki jar i natywnych bibliotek, które zostały wygenerowane przez kompilację. Dotyczy tylko aplikacji Java. 

1.  Upewnij się, że znajdują się w folderze głównym aplikacji przed wykonaniem tego polecenia. 
2.  Wybierz **usługi Service Fabric: czystą aplikacji** polecenia.
3.  Dane wyjściowe czystą procesu są zapisywane w zintegrowanym terminala.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [tworzenie i debugowanie aplikacji sieci szkieletowej usług C# za pomocą kodu VS](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Dowiedz się, jak [opracowywania i debugowania aplikacji Java sieci szkieletowej usług za pomocą kodu VS](./service-fabric-develop-java-applications-with-vs-code.md).
