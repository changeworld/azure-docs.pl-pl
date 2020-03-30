---
title: Zarządzanie aplikacjami w programie Visual Studio
description: Program Visual Studio służy do tworzenia, opracowywania, pakowania, wdrażania i debugowania aplikacji i usług sieci szkieletowej usług Azure.
author: mikkelhegn
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: mikhegn
ms.openlocfilehash: d6734f5da0fb7e5c9052b26b55b2d90b068bdbbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614336"
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Używanie programu Visual Studio w celu uproszczenia pisania aplikacji sieci szkieletowej usług i zarządzania nimi
Za pomocą programu Visual Studio można zarządzać aplikacjami i usługami sieci szkieletowej usług Azure. Po [skonfigurowaniu środowiska programistycznego](service-fabric-get-started.md)można użyć programu Visual Studio do tworzenia aplikacji sieci szkieletowej usług, dodawania usług lub pakowania, rejestrowania i wdrażania aplikacji w lokalnym klastrze deweloperskim.

## <a name="deploy-your-service-fabric-application"></a>Wdrażanie aplikacji sieci szkieletowej usług
Domyślnie wdrażanie aplikacji łączy następujące kroki w jedną prostą operację:

1. Tworzenie pakietu aplikacji
2. Przesyłanie pakietu aplikacji do magazynu obrazów
3. Rejestrowanie typu aplikacji
4. Usuwanie wszystkich uruchomionych wystąpień aplikacji
5. Tworzenie wystąpienia aplikacji

W programie Visual Studio naciśnięcie **klawisza F5** wdraża aplikację i dołącza debuger do wszystkich wystąpień aplikacji. Za pomocą **klawiszy Ctrl+F5** można wdrożyć aplikację bez debugowania lub opublikować w klastrze lokalnym lub zdalnym przy użyciu profilu publikowania.

### <a name="application-debug-mode"></a>Tryb debugowania aplikacji
Visual Studio zapewniają właściwość o nazwie **tryb debugowania aplikacji**, który kontroluje, jak chcesz Visual Studios do obsługi wdrożenia aplikacji w ramach debugowania.

#### <a name="to-set-the-application-debug-mode-property"></a>Aby ustawić właściwość Tryb debugowania aplikacji
1. W menu skrótów projektu aplikacji sieci szkieletowej usług (*.sfproj) wybierz polecenie **Właściwości** (lub naciśnij klawisz **F4).**
2. W oknie **Właściwości** ustaw **właściwość Tryb debugowania aplikacji.**

![Ustaw właściwość trybu debugowania aplikacji][debugmodeproperty]

#### <a name="application-debug-modes"></a>Tryby debugowania aplikacji

1. **Odśwież aplikację** Ten tryb umożliwia szybką zmianę i debugowanie kodu i obsługuje edycję statycznych plików internetowych podczas debugowania. Ten tryb działa tylko wtedy, gdy lokalny klaster programistyczny jest w trybie 1-node. Jest to domyślny tryb debugowania aplikacji.
2. **Usuń aplikację** powoduje, że aplikacja ma zostać usunięta po zakończeniu sesji debugowania.
3. **Automatyczne uaktualnianie** Aplikacja jest nadal uruchamiana po zakończeniu sesji debugowania. Następna sesja debugowania będzie traktować wdrożenie jako uaktualnienie. Proces uaktualniania zachowuje wszystkie dane wprowadzone w poprzedniej sesji debugowania.
4. **Zachowaj aplikację** Aplikacja działa w klastrze po zakończeniu sesji debugowania. Na początku następnej sesji debugowania aplikacja zostanie usunięta.

Dla **automatycznego uaktualniania** dane są zachowywane przez zastosowanie możliwości uaktualniania aplikacji sieci szkieletowej usług. Aby uzyskać więcej informacji na temat uaktualniania aplikacji i sposobu przeprowadzania uaktualnienia w rzeczywistym środowisku, zobacz [Uaktualnianie aplikacji sieci szkieletowej usług](service-fabric-application-upgrade.md).

## <a name="add-a-service-to-your-service-fabric-application"></a>Dodawanie usługi do aplikacji sieci szkieletowej usług
Można dodać nowe usługi do aplikacji, aby rozszerzyć jego funkcjonalność. Aby upewnić się, że usługa jest uwzględniona w pakiecie aplikacji, dodaj usługę za pośrednictwem elementu menu **Nowa usługa sieci szkieletowej...** .

![Dodawanie nowej usługi sieci szkieletowej usług][newservice]

Wybierz typ projektu sieci szkieletowej usług, aby dodać do aplikacji i określ nazwę usługi.  Zobacz [Wybieranie struktury dla usługi,](service-fabric-choose-framework.md) aby ułatwić podjęcie decyzji, jakiego typu usługi użyć.

![Wybierz typ projektu usługi sieci szkieletowej usług, który chcesz dodać do aplikacji][addserviceproject]

Nowa usługa zostanie dodana do rozwiązania i istniejącego pakietu aplikacji. Odwołania do usługi i domyślne wystąpienie usługi zostaną dodane do manifestu aplikacji, co spowoduje, że usługa zostanie utworzona i uruchomiona przy następnym wdrożeniu aplikacji.

![Nowa usługa zostanie dodana do manifestu aplikacji][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Pakowanie aplikacji sieci szkieletowej usług
Aby wdrożyć aplikację i jej usługi w klastrze, należy utworzyć pakiet aplikacji.  Pakiet organizuje manifest aplikacji, manifesty usługi i inne niezbędne pliki w określonym układzie.  Visual Studio konfiguruje pakiet w folderze projektu aplikacji i zarządza nim w katalogu "pkg".  Kliknięcie **przycisku Pakiet** z menu kontekstowego **aplikacji** tworzy lub aktualizuje pakiet aplikacji.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Usuwanie aplikacji i typów aplikacji przy użyciu Eksploratora chmury
Podstawowe operacje zarządzania klastrami można wykonywać z poziomu programu Visual Studio przy użyciu Eksploratora chmury, który można uruchomić z menu **Widok.** Na przykład można usunąć aplikacje i unprovision typów aplikacji na klastrach lokalnych lub zdalnych.

![Usuwanie aplikacji][removeapplication]

> [!TIP]
> Aby uzyskać bogatszą funkcję zarządzania klastrami, zobacz [Wizualizacja klastra za pomocą Eksploratora sieci szkieletowej usług](service-fabric-visualizing-your-cluster.md).
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Następne kroki
* [Model aplikacji sieci szkieletowej usług](service-fabric-application-model.md)
* [Wdrażanie aplikacji sieci szkieletowej usług](service-fabric-deploy-remove-applications.md)
* [Zarządzanie parametrami aplikacji dla wielu środowisk](service-fabric-manage-multiple-environment-app-configuration.md)
* [Debugowanie aplikacji sieci szkieletowej usług](service-fabric-debugging-your-application.md)
* [Wizualizacja klastra przy użyciu Eksploratora sieci szkieletowej usług](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png
