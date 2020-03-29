---
title: Samouczek uaktualniania aplikacji sieci szkieletowej usługi
description: W tym artykule oględy na temat wdrażania aplikacji sieci szkieletowej usług, zmiany kodu i wdrażania uaktualnienia przy użyciu programu Visual Studio.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: db814b972db1aee56be0858c9ff5d1c382640642
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464825"
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Samouczek uaktualnienia aplikacji sieci szkieletowej usługi przy użyciu programu Visual Studio
> [!div class="op_single_selector"]
> * [Powershell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Usługa Azure Service Fabric upraszcza proces uaktualniania aplikacji w chmurze, zapewniając, że tylko zmienione usługi są uaktualniane, a kondycja aplikacji jest monitorowana w trakcie procesu uaktualniania. To również automatycznie wycofuje aplikację do poprzedniej wersji po napotkaniu problemów. Uaktualnienia aplikacji sieci szkieletowej usług są *zero przestojów,* ponieważ aplikacja może być uaktualniony bez przestojów. W tym samouczku opisano sposób ukończenia uaktualnienia stopniowego z programu Visual Studio.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>Krok 1: Tworzenie i publikowanie przykładu obiektów wizualnych
Najpierw pobierz aplikację [Visual Objects](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) z gitHub. Następnie skompiluj i opublikuj aplikację, klikając prawym przyciskiem myszy projekt aplikacji **VisualObjects**i wybierając polecenie **Publikuj** w elemencie menu sieci szkieletowej usług.

![Menu kontekstowe aplikacji sieci szkieletowej usług][image1]

Wybranie **opcji Publikuj** powoduje wyświetlenie wyskakującego okienka i można ustawić **profil docelowy** na **PublishProfiles\Local.xml**. Okno powinno wyglądać następująco przed kliknięciem **przycisku Publikuj**.

![Publikowanie aplikacji sieci szkieletowej usług][image2]

Teraz możesz kliknąć przycisk **Publikuj** w oknie dialogowym. Za pomocą [Eksploratora sieci szkieletowej usług można wyświetlić klaster i aplikację](service-fabric-visualizing-your-cluster.md). Aplikacja Obiekty wizualne ma usługę sieci web, [http://localhost:8081/visualobjects/](http://localhost:8081/visualobjects/) do której można przejść, wpisując na pasku adresu przeglądarki.  Na ekranie powinno zostać wyświetlonych 10 ruchomych obiektów wizualnych.

**UWAGA:** W przypadku `Cloud.xml` wdrażania w profilu (azure service fabric), aplikacja powinna być dostępna pod adresem **http://{ServiceFabricName}.{ Region}.cloudapp.azure.com:8081/visualobjects/**. Upewnij się, `8081/TCP` że masz skonfigurowany w moduł równoważenia obciążenia (znajdź moduł równoważenia obciążenia w tej samej grupie zasobów co wystąpienie sieci szkieletowej usług).

## <a name="step-2-update-the-visual-objects-sample"></a>Krok 2: Aktualizowanie przykładu obiektów wizualnych
Można zauważyć, że w wersji, która została wdrożona w kroku 1, obiekty wizualne nie obracają się. Uaktualnijmy tę aplikację do tej, w której obiekty wizualne również się obracają.

Wybierz projekt VisualObjects.ActorService w rozwiązaniu VisualObjects i otwórz plik **VisualObjectActor.cs.** W tym pliku przejdź `MoveObject`do metody `visualObject.Move(false)`, komentarz `visualObject.Move(true)`i uncomment . Ta zmiana kodu obraca obiekty po uaktualnieniu usługi.  **Teraz można zbudować (nie odbudować) rozwiązanie**, które tworzy zmodyfikowane projekty. Jeśli wybierzesz *Przebuduj wszystko,* musisz zaktualizować wersje dla wszystkich projektów.

Musimy również wersję naszej aplikacji. Aby wprowadzić zmiany wersji po kliknięciu prawym przyciskiem myszy projektu **VisualObjects,** można użyć opcji Visual Studio **Edytuj wersje manifestacji.** Wybranie tej opcji powoduje wyświetlenie okna dialogowego dla wersji w następujący sposób:

![Okno dialogowe Przechowywanie wersji][image3]

Zaktualizuj wersje zmodyfikowanych projektów i ich pakietów kodu wraz z aplikacją do wersji 2.0.0. Po dokonaniu zmian manifest powinien wyglądać następująco (pogrubione fragmenty pokazują zmiany):

![Aktualizowanie wersji][image4]

Narzędzia programu Visual Studio mogą wykonywać automatyczne zestawienia pakietów zbiorczych wersji po wybraniu **opcji Automatycznie aktualizują wersje aplikacji i usług**. Jeśli używasz [SemVer](http://www.semver.org), należy zaktualizować kod i/lub wersję pakietu konfiguracyjnego samodzielnie, jeśli ta opcja jest zaznaczona.

Zapisz zmiany, a teraz zaznacz pole **Uaktualnij aplikację.**

## <a name="step-3--upgrade-your-application"></a>Krok 3: Uaktualnij aplikację
Zapoznaj się z [parametrami uaktualniania aplikacji](service-fabric-application-upgrade-parameters.md) i [procesem uaktualniania,](service-fabric-application-upgrade.md) aby uzyskać dobre zrozumienie różnych parametrów uaktualnienia, limitów czasu i kryterium kondycji, które można zastosować. W tym instruktażu kryterium oceny kondycji usługi jest ustawione na domyślny (tryb niemonitorowany). Można skonfigurować te ustawienia, wybierając **pozycję Konfiguruj ustawienia uaktualnienia,** a następnie modyfikując parametry zgodnie z potrzebami.

Teraz wszyscy jesteśmy ustawione, aby rozpocząć uaktualnienie aplikacji, wybierając **Publikuj**. Ta opcja uaktualnia aplikację do wersji 2.0.0, w której obiekty obracają się. Usługa Sieci szkieletowej uaktualnia jedną domenę aktualizacji naraz (niektóre obiekty są aktualizowane najpierw, a następnie inne), a usługa pozostaje dostępna podczas uaktualniania. Dostęp do usługi można sprawdzić za pośrednictwem klienta (przeglądarki).  

Teraz w miarę postępu uaktualniania aplikacji można go monitorować za pomocą Eksploratora sieci szkieletowej usług, używając karty **Uaktualnienia w toku** w aplikacjach.

W ciągu kilku minut wszystkie domeny aktualizacji powinny zostać uaktualnione (ukończone), a okno danych wyjściowych programu Visual Studio powinno również określać, że uaktualnienie zostało zakończone. I powinno się okazać, że *wszystkie* obiekty wizualne w oknie przeglądarki są teraz obracanie!

Możesz spróbować zmienić wersje i przejść z wersji 2.0.0 do wersji 3.0.0 jako ćwiczenie, a nawet z wersji 2.0.0 z powrotem do wersji 1.0.0. Graj z limitami czasu i polityką zdrowotną, aby się z nimi zapoznać. Podczas wdrażania w klastrze platformy Azure, w przeciwieństwie do klastra lokalnego, parametry używane mogą się różnić. Zaleca się ustawienie limitów czasu zachowawczo.

## <a name="next-steps"></a>Następne kroki
[Uaktualnianie aplikacji przy użyciu programu PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) przeprowadzi Cię przez uaktualnienie aplikacji przy użyciu programu PowerShell.

Kontroluj sposób uaktualniania aplikacji przy użyciu [parametrów uaktualnienia](service-fabric-application-upgrade-parameters.md).

Upewnij się, że uaktualnienia aplikacji są zgodne, ucząc się, jak używać [serializacji danych.](service-fabric-application-upgrade-data-serialization.md)

Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [tematów zaawansowanych](service-fabric-application-upgrade-advanced.md).

Rozwiąż typowe problemy w uaktualnieniach aplikacji, odwołując się do kroków [rozwiązywania problemów z uaktualnieniami aplikacji](service-fabric-application-upgrade-troubleshooting.md).

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
