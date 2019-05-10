---
title: Samouczek dotyczący używania konfiguracji aplikacji platformy Azure do zarządzania flag funkcji | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak zarządzać flag funkcji oddzielnie z aplikacji przy użyciu konfiguracji aplikacji platformy Azure
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
ms.openlocfilehash: d995a2e9f0d05dc3b0853036e8fb3c04ccdfab96
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412343"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Samouczek: Zarządzanie flagami funkcji w konfiguracji aplikacji platformy Azure

Można przechowywać wszystkie flagi funkcji w konfiguracji aplikacji platformy Azure i administrować nimi z jednego miejsca. Ma ona portalu użytkownika o nazwie **funkcji Menedżera**, która jest zaprojektowany specjalnie dla flag funkcji. Ponadto Konfiguracja aplikacji obsługuje schemat danych flagi funkcji platformy .NET Core natywnie.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Definiowanie i zarządzanie nimi flagi funkcji w konfiguracji aplikacji.
> * Flagi funkcji dostępu z poziomu aplikacji.

## <a name="create-feature-flags"></a>Tworzenie flag funkcji

**Funkcji Menedżera** w witrynie Azure portal dla konfiguracji aplikacji udostępnia interfejs użytkownika do tworzenia i zarządzania funkcją flag używasz w aplikacji. Wykonaj poniższe kroki, aby dodać nową flagę funkcji.

1. Wybierz **funkcji Menedżera** > **+ Utwórz** dodać flagi funkcji.

    ![Lista flagi funkcji](./media/azure-app-configuration-feature-flags.png)

2. Wprowadź unikatową nazwę klucza flagi funkcji. Należy tę nazwę, aby odwoływać się Flaga w kodzie.

3. Opcjonalnie określić flagę funkcji bardziej przyjaznego dla człowieka.

4. Ustaw początkowy stan flagi funkcji. Zazwyczaj jest to po prostu *na* lub *poza*.

    ![Utwórz flagi funkcji](./media/azure-app-configuration-feature-flag-create.png)

5. Jeśli stan jest wyświetlany *na*możesz określić wszelkie dodatkowe warunek, aby go za pomocą **Dodaj filtr**. Wprowadź klucz filtru wbudowanych lub niestandardowych i skojarz parametry. Dostępne są następujące wbudowane filtry:

    | Klucz | Parametry JSON |
    |---|---|
    | Microsoft.Percentage | {"Value": 0 – 100%} |
    | Microsoft.TimeWindow | {"Start": Czas UTC, "Koniec": Czas UTC} |

    ![Filtr flagi funkcji](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Aktualizowanie stanów flagi funkcji

Wykonaj poniższe kroki, aby zmienić wartość stanu flagi funkcji.

1. Wybierz **funkcji Menedżera**.

2. Kliknij pozycję **...**   >  **Edytuj** po prawej stronie flagę funkcji, którą chcesz zmodyfikować.

3. Ustaw nowy stan flagi funkcji.

## <a name="access-feature-flags"></a>Dostęp do flag funkcji

Utworzone przez flag funkcji **funkcji Menedżera** są przechowywane i pobierane jako regularne wartości klucza. Znajdowały się w obszarze prefiks przestrzeni nazw specjalnych *. appconfig.featureflag*. Można wyświetlić podstawowy klucz wartości przy użyciu **Eksplorator konfiguracji**. Twoja aplikacja może pobrać je przy użyciu dostawcy konfiguracji konfiguracji aplikacji, zestawów SDK, rozszerzenia wiersza polecenia i interfejsów API REST.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób zarządzania flag funkcji i ich stany przy użyciu konfiguracji aplikacji. Zobacz następujące zasoby, aby uzyskać więcej informacji dotyczących obsługi różnych funkcji zarządzania w konfiguracji i aplikacji platformy ASP.NET Core.

* [Użyj flagi funkcji w aplikacji ASP.NET Core](./use-feature-flags-dotnet-core.md)
