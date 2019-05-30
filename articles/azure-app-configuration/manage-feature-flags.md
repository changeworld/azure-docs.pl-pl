---
title: Samouczek dotyczący używania konfiguracji aplikacji platformy Azure do zarządzania flag funkcji | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak zarządzać flag funkcji oddzielnie z poziomu aplikacji przy użyciu konfiguracji aplikacji platformy Azure.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: b7fbf9add67a45c0db89fc11cee5c10bc537ab63
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393570"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Samouczek: Zarządzanie flagami funkcji w konfiguracji aplikacji platformy Azure

Można przechowywać wszystkie flagi funkcji w konfiguracji aplikacji platformy Azure i administrować nimi z jednego miejsca. Konfiguracja aplikacji ma portalu, interfejsu użytkownika o nazwie **funkcji Menedżera** który jest przeznaczony specjalnie dla flag funkcji. Konfiguracja aplikacji również natywnie obsługuje schemat danych flagi funkcji platformy .NET Core.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Definiowanie i zarządzanie nimi flagi funkcji w konfiguracji aplikacji.
> * Flagi funkcji dostępu z poziomu aplikacji.

## <a name="create-feature-flags"></a>Tworzenie flag funkcji

Menedżer funkcji w witrynie Azure portal dla konfiguracji aplikacji zapewnia interfejs użytkownika do tworzenia i zarządzania flag funkcji, które można używać w aplikacjach.

Aby dodać nową flagę funkcji:

1. Wybierz **funkcji Menedżera** >  **+ Dodaj** dodać flagi funkcji.

    ![Lista flagi funkcji](./media/azure-app-configuration-feature-flags.png)

1. Wprowadź unikatową nazwę klucza flagi funkcji. Należy tę nazwę, aby odwoływać się Flaga w kodzie.

1. Należy podać opis flagi funkcji.

1. Ustaw początkowy stan flagi funkcji. Ten stan jest zwykle *poza* lub *na*. *Na* stan zmieni się na *warunkowego* Jeśli dodasz filtr do flagi funkcji.

    ![Tworzenie flagi funkcji](./media/azure-app-configuration-feature-flag-create.png)

1. Jeśli stan jest wyświetlany *na*, wybierz opcję **+ Dodaj filtr** określić wszelkie dodatkowe warunki, aby zakwalifikować się stan. Wprowadź klucz wbudowanych lub niestandardowych filtr, a następnie wybierz **+ Dodaj parametr** do skojarzenia z jednego lub więcej parametrów z filtrem. Dostępne są następujące wbudowane filtry:

    | Klucz | Parametry JSON |
    |---|---|
    | Microsoft.Percentage | {"Value": 0 – 100%} |
    | Microsoft.TimeWindow | {"Start": Czas UTC, "Koniec": Czas UTC} |

    ![Filtr flagi funkcji](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Aktualizowanie stanów flagi funkcji

Aby zmienić wartość stanu flagi funkcji:

1. Wybierz **funkcji Menedżera**.

1. Po prawej stronie flagę funkcji, którą chcesz zmodyfikować, wybierz przycisk wielokropka ( **...** ), a następnie wybierz pozycję **Edytuj**.

1. Ustaw nowy stan flagi funkcji.

## <a name="access-feature-flags"></a>Dostęp do flag funkcji

Flagi funkcji utworzone przez Menedżera funkcji są przechowywane i pobierane jako regularne wartości klucza. One poddawane prefiks przestrzeni nazw specjalnych `.appconfig.featureflag`. Aby wyświetlić podstawowych wartości kluczy, skorzystaj z Eksploratora konfiguracji. Aplikacja może pobrać te wartości przy użyciu dostawcy konfiguracji konfiguracji aplikacji, zestawów SDK, rozszerzenia wiersza polecenia i interfejsów API REST.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób zarządzania flag funkcji i ich stany przy użyciu konfiguracji aplikacji. Aby uzyskać więcej informacji na temat obsługi funkcji zarządzania w konfiguracji i aplikacji platformy ASP.NET Core zobacz następujący artykuł:

* [Użyj flagi funkcji w aplikacji ASP.NET Core](./use-feature-flags-dotnet-core.md)
