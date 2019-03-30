---
title: Samouczek dotyczący uaktualniania aplikacji usługi Service Fabric | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono proces wdrażania aplikacji usługi Service Fabric, zmiana kodu i wprowadza uaktualnienie przy użyciu programu Visual Studio.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: a3181a7a-9ab1-4216-b07a-05b79bd826a4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 8fe0bf9c8827b7248195f89377176fd834845e32
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663676"
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Samouczek uaktualniania aplikacji usługi Service Fabric za pomocą programu Visual Studio
> [!div class="op_single_selector"]
> * [Program PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Program Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Usługa Azure Service Fabric upraszcza proces uaktualniania aplikacji w chmurze, zapewniając uaktualniane są tylko zmienione usługi i że kondycji aplikacji jest monitorowana przez cały proces uaktualniania. Powoduje także automatyczne wycofanie aplikacji do poprzedniej wersji, w razie napotkania problemów. Uaktualnienia aplikacji usługi Service Fabric są *żadnych przestojów*, ponieważ można uaktualnić aplikację bez przerw w dostępności. W tym samouczku opisano, jak wykonać uaktualnienie stopniowe z programu Visual Studio.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>Krok 1: Tworzenie i publikowanie przykładowej obiektów wizualnych
Najpierw pobierz [obiektów wizualnych](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) aplikacji z usługi GitHub. Następnie tworzyć i publikować aplikację, klikając prawym przyciskiem myszy projekt aplikacji **VisualObjects**i wybierając polecenie **Publikuj** polecenia w elemencie menu usługi Service Fabric.

![Menu kontekstowe dla aplikacji usługi Service Fabric][image1]

Wybranie **publikowania** wyświetlenie wyskakującego okienka, a użytkownik może ustawić **docelowy profil** do **PublishProfiles\Local.xml**. Okno powinno wyglądać następująco przed kliknięciem przycisku **Publikuj**.

![Publikowanie aplikacji usługi Service Fabric][image2]

Teraz możesz kliknąć pozycję **Publikuj** w oknie dialogowym. Możesz użyć [narzędzia Service Fabric Explorer, aby wyświetlić klaster i aplikacja](service-fabric-visualizing-your-cluster.md). Aplikacja obiektów wizualnych ma usługi sieci web, które możesz przejść do, wpisując [ http://localhost:8081/visualobjects/ ](http://localhost:8081/visualobjects/) na pasku adresu przeglądarki.  Powinien zostać wyświetlony 10 zmiennoprzecinkowy obiektów wizualnych poruszanie się na ekranie.

**UWAGA:** Jeśli wdrażana `Cloud.xml` profilu (usługi Azure Service Fabric), aplikacja powinna być dostępna na **http://{ServiceFabricName}. { Region}.cloudapp.Azure.com:8081/visualobjects/**. Upewnij się, że masz `8081/TCP` skonfigurowany w module równoważenia obciążenia (Znajdź modułu równoważenia obciążenia w tej samej grupie zasobów, gdy wystąpienie usługi Service Fabric).

## <a name="step-2-update-the-visual-objects-sample"></a>Krok 2: Aktualizacja przykładu obiektów wizualnych
Może się okazać, że z wersją, która została wdrożona w kroku 1, obiektów wizualnych nie Obróć. Teraz uaktualnić tę aplikację do jednego gdzie również obrócić obiektów wizualnych.

Wybierz projekt VisualObjects.ActorService w ramach rozwiązania VisualObjects, a następnie otwórz **VisualObjectActor.cs** pliku. W tym pliku, przejdź do metody `MoveObject`, komentarz `visualObject.Move(false)`i usuń znaczniki komentarza `visualObject.Move(true)`. Ta zmiana kodu obraca obiekty po uaktualnieniu usługi.  **Możesz teraz utworzyć (nie ponowną kompilację) rozwiązanie**, które kompilacje zmodyfikowane projekty. Jeśli wybierzesz *ponowna kompilacja*, musisz zaktualizować wersje dla wszystkich projektów.

Musimy również wersja naszej aplikacji. Aby wprowadzić zmiany wersji po kliknięciu prawym przyciskiem myszy **VisualObjects** projektu, można użyć programu Visual Studio **Edytuj wersje manifestu** opcji. Wybranie tej opcji wyświetlenie okna dialogowego wersje edition w następujący sposób:

![Przechowywanie wersji, okno dialogowe][image3]

Zaktualizuj wersje zmodyfikowane projekty i ich pakiety kodu wraz z aplikacji do wersji 2.0.0. Po dokonaniu zmian manifestu powinien wyglądać podobnie do następującej (pogrubienie fragmenty pokazują zmiany):

![Aktualizacja wersji][image4]

Narzędzia programu Visual Studio można wykonać automatyczne zbiorcze wersje po wybraniu **automatycznie Aktualizuj wersje aplikacji i usług**. Jeśli używasz [SemVer](http://www.semver.org), musisz zaktualizować kod i/lub konfiguracji pakietu wersji autonomicznej, czy opcja jest zaznaczona.

Zapisz zmiany i sprawdź teraz **Uaktualnij aplikację** pole.

## <a name="step-3--upgrade-your-application"></a>Krok 3:  Uaktualnianie aplikacji
Zapoznaj się z [parametry uaktualniania aplikacji](service-fabric-application-upgrade-parameters.md) i [procesu uaktualniania](service-fabric-application-upgrade.md) uzyskać dobre zrozumienie różnych parametry uaktualniania, limity czasu i kryterium kondycji, które mogą być stosowane. W tym przewodniku kryterium oceny kondycji usługi jest domyślnie ustawiona (tryb niemonitorowane). Te ustawienia można skonfigurować, wybierając **Konfigurowanie ustawień uaktualniania** , a następnie zmodyfikowanie parametrów, zgodnie z potrzebami.

Teraz jesteśmy gotowi do rozpoczęcia uaktualnienia aplikacji, wybierając **Publikuj**. Ta opcja uaktualnienia aplikacji w wersji 2.0.0, w którym obracanie obiektów. Usługa Service Fabric uaktualniania jednej domeny aktualizacji jednocześnie (niektóre obiekty są aktualizowane w pierwszej kolejności, a następnie przez inne osoby), a usługa jest nadal dostępny podczas uaktualniania. Dostęp do usługi mogą zostać sprawdzone za pośrednictwem sieci klienta (przeglądarki).  

Teraz kontynuowane uaktualnienia aplikacji, można go monitorować przy użyciu narzędzia Service Fabric Explorer za pomocą **uaktualnienia w toku** kartę w aplikacji.

W ciągu kilku minut powinny zostać uaktualnione wszystkich domen aktualizacji (ukończono), a następnie w oknie danych wyjściowych programu Visual Studio należy również podać uaktualnienia lokacji. I powinna zauważyć, że *wszystkich* obiektów wizualnych w oknie przeglądarki są teraz obracanie!

Możesz chcieć spróbuj zmienić wersje i przenoszenie z wersji 2.0.0 do wersji 3.0.0 w charakterze ćwiczenia, lub nawet w wersji 2.0.0 do wersji 1.0.0. Poeksperymentuj z limitów czasu i zasad dotyczących kondycji z siebie uczynić je znasz. W przypadku wdrażania w klastrze platformy Azure, w przeciwieństwie do klastra lokalnego, parametry używane może być mogą się różnić. Firma Microsoft zaleca konserwatywnie Ustaw limity czasu.

## <a name="next-steps"></a>Kolejne kroki
[Uaktualnianie aplikacji przy użyciu programu PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi uaktualnienie aplikacji przy użyciu programu PowerShell.

Kontrolowanie sposobu uaktualnieniu aplikacji przy użyciu [parametry uaktualniania](service-fabric-application-upgrade-parameters.md).

Uzyskania uaktualnień aplikacji zgodnych poznanie sposobu używania [serializacja danych](service-fabric-application-upgrade-data-serialization.md).

Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [Tematy zaawansowane](service-fabric-application-upgrade-advanced.md).

Rozwiązywanie typowych problemów podczas uaktualniania aplikacji korzystając z procedury opisanej w [Rozwiązywanie problemów z uaktualnieniami aplikacji](service-fabric-application-upgrade-troubleshooting.md).

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
