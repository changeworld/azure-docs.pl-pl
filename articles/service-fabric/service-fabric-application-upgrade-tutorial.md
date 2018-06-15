---
title: Samouczek uaktualniania aplikacji sieci szkieletowej usług | Dokumentacja firmy Microsoft
description: Ten artykuł przeprowadzi Cię przez proces wdrażania aplikacji usługi Service Fabric, zmiana kodu i wprowadza uaktualnienia w programie Visual Studio.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: a3181a7a-9ab1-4216-b07a-05b79bd826a4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 0a94dc91308f21d9e2b440f9638829ee03a8b903
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205199"
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Samouczek uaktualniania aplikacji sieci szkieletowej usług za pomocą programu Visual Studio
> [!div class="op_single_selector"]
> * [Program PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Program Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Sieć szkieletowa usług Azure upraszcza proces uaktualniania aplikacji w chmurze w celu zapewnienia uaktualnienia tylko zmienione usługi, i że kondycja aplikacji jest monitorowane w trakcie procesu uaktualniania. Automatycznie wycofywaniu aplikacji z poprzednią wersją w przypadku napotkania problemów. Uaktualnienia aplikacji sieci szkieletowej usług są *przestój Zero*, ponieważ aplikacja można uaktualnić bez przestojów. W tym samouczku przedstawiono sposób wykonania uaktualnienia stopniowego z programu Visual Studio.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>Krok 1: Tworzenie i publikowanie przykład Visual obiektów
Najpierw pobierz [obiektów Visual](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) aplikacji z usługi GitHub. Następnie utworzyć i opublikować aplikację, klikając prawym przyciskiem myszy projekt aplikacji **VisualObjects**i wybierając **publikowania** polecenia w elemencie menu sieci szkieletowej usług.

![Menu kontekstowe dla aplikacji sieci szkieletowej usług][image1]

Wybieranie **publikowania** powoduje wyświetlenie okna podręcznego, i można ustawić **docelowego profilu** do **PublishProfiles\Local.xml**. Okno powinna wyglądać następująco przed kliknięciem przycisku **publikowania**.

![Publikowanie aplikacji sieci szkieletowej usług][image2]

Teraz możesz kliknąć **publikowania** w oknie dialogowym. Można użyć [Service Fabric Explorer, aby wyświetlić klastra i aplikacji](service-fabric-visualizing-your-cluster.md). Aplikacja obiektów Visual ma usługi sieci web, który można przejść do wpisując [ http://localhost:8081/visualobjects/ ](http://localhost:8081/visualobjects/) na pasku adresu przeglądarki.  Powinny pojawić się 10 obiektów visual przestawnych przenoszenia na ekranie.

**Uwaga:** wdrażania do `Cloud.xml` profilu (sieć szkieletowa usług Azure,) aplikacja powinna być dostępna w **http://{ServiceFabricName}. { Region}.cloudapp.Azure.com:8081/visualobjects/**. Upewnij się, że masz `8081/TCP` skonfigurowany w usłudze równoważenia obciążenia (znaleźć usługi równoważenia obciążenia w tej samej grupie zasobów, gdy wystąpienie usługi sieć szkieletowa).

## <a name="step-2-update-the-visual-objects-sample"></a>Krok 2: Zaktualizuj próbka obiektów Visual
Można zauważyć, że przy użyciu wersji, który został wdrożony w kroku 1, obiekt visual nie Obróć. Umożliwia uaktualnienie tej aplikacji na taki, gdzie obiekty widoczne również obracać.

Wybierz projekt VisualObjects.ActorService w ramach rozwiązania VisualObjects i Otwórz **VisualObjectActor.cs** pliku. W tym pliku, przejdź do metody `MoveObject`, komentarz `visualObject.Move(false)`i Usuń komentarz `visualObject.Move(true)`. Ta zmiana kodu obraca obiekty po uaktualnieniu usługi.  **Teraz można tworzyć (nie kompilację) rozwiązania**, która tworzy zmodyfikowane projekty. W przypadku wybrania *odbudowanie wszystkiego*, musisz zaktualizować wersje we wszystkich projektach.

Również musimy wersji aplikacji. Aby wprowadzić zmiany wersji po kliknięciu prawym przyciskiem myszy na **VisualObjects** projektu, można użyć programu Visual Studio **edytować wersji manifestu** opcji. Wybranie tej opcji wyświetlenie okna dialogowego dla wersji edition w następujący sposób:

![Okno dialogowe kontroli wersji][image3]

Wersje aktualizacji dla zmodyfikowanych projektów i ich pakiety kodu, wraz z aplikacji do wersji 2.0.0. Po wprowadzeniu zmian plik manifestu powinien wyglądać następująco (pogrubienie części Pokaż zmiany):

![Aktualizowanie wersji][image4]

Visual Studio tools możliwość automatycznego pakiety zbiorcze wersji po wybraniu **automatycznie Aktualizuj wersje aplikacji i usługi**. Jeśli używasz [programu SemVer](http://www.semver.org), musisz zaktualizować kod i/lub konfiguracji pakietu wersji tylko, jeśli opcja jest zaznaczona.

Zapisz zmiany, a teraz sprawdzić **uaktualnienie aplikacji** pole.

## <a name="step-3--upgrade-your-application"></a>Krok 3: Uaktualnienie aplikacji
Zapoznaj się z [parametry uaktualniania aplikacji](service-fabric-application-upgrade-parameters.md) i [procesu uaktualniania](service-fabric-application-upgrade.md) można uzyskać dobrą znajomością różnych parametrów uaktualnienia, limity czasu i kryterium kondycji, które mogą być stosowane. W ramach tego przewodnika kryterium oceny kondycji usługi ma ustawioną wartość domyślna (tryb niemonitorowane). Te ustawienia można skonfigurować, wybierając **skonfigurować ustawienia uaktualnienia** , a następnie modyfikując parametry zgodnie z potrzebami.

Teraz możemy mają ustawione uruchom operację uaktualniania aplikacji przez wybranie **publikowania**. Ta opcja uaktualnia aplikację do wersji 2.0.0, w którym Obróć obiektów. Sieć szkieletowa usług uaktualnia jednej domeny aktualizacji w czasie (niektóre obiekty są aktualizowane w pierwszej kolejności, zostały wykonane przez innych użytkowników), a usługa jest nadal dostępny podczas uaktualniania. Dostęp do usługi można sprawdzić za pomocą klienta (przeglądarka).  

Teraz kontynuowane uaktualniania aplikacji, można go monitorować z Eksploratora usługi sieć szkieletowa, za pomocą **uaktualnień w toku** kartę w aplikacji.

Za kilka minut można uaktualnić wszystkie domeny aktualizacji (ukończone), a następnie w oknie danych wyjściowych programu Visual Studio należy również podać zakończeniu uaktualniania. I który należy odnaleźć *wszystkie* visual obiektów w oknie przeglądarki są teraz obracanie!

Chcesz spróbować zmienić wersje i przenoszenie z wersji 2.0.0 na wersję 3.0.0 jako wykonywania, lub nawet wersji 2.0.0 wersji 1.0.0. Odtwarzanie z limitów czasu i zasady dotyczące kondycji z siebie uczynić z nich korzystać. Podczas wdrażania na klastrze platformy Azure, w przeciwieństwie do klastra lokalnego, parametry używane może być mogą się różnić. Firma Microsoft zaleca konserwatywnie Ustaw limity czasu.

## <a name="next-steps"></a>Kolejne kroki
[Uaktualnianie aplikacji przy użyciu programu PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi Cię przez proces uaktualnienia aplikacji przy użyciu programu PowerShell.

Kontrolowanie, jak aplikacja zostanie uaktualniony przy użyciu [parametry uaktualnienia](service-fabric-application-upgrade-parameters.md).

Uzyskania uaktualnień aplikacji zgodnych przez poznanie [szeregowanie danych](service-fabric-application-upgrade-data-serialization.md).

Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [Tematy zaawansowane](service-fabric-application-upgrade-advanced.md).

Rozwiązywania typowych problemów w uaktualnień aplikacji, korzystając z procedury opisanej w [Rozwiązywanie problemów z uaktualnieniami aplikacji](service-fabric-application-upgrade-troubleshooting.md).

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
