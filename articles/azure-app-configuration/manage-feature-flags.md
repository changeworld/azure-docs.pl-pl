---
title: 'Samouczek: Zarządzanie flagami funkcji za pomocą funkcji za pomocą funkcji'
titleSuffix: Azure App Configuration
description: W tym samouczku dowiesz się, jak zarządzać flagami funkcji oddzielnie od aplikacji przy użyciu konfiguracji aplikacji platformy Azure.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: ccab8014000f9f684249bf2c1f800f74c92e7369
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76899370"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Samouczek: Zarządzanie flagami funkcji w konfiguracji aplikacji platformy Azure

W konfiguracji aplikacji platformy Azure można przechowywać wszystkie flagi obiektów i administrować nimi z jednego miejsca. Konfiguracja aplikacji ma interfejs użytkownika portalu o nazwie **Menedżer funkcji,** który został zaprojektowany specjalnie dla flag funkcji. Konfiguracja aplikacji obsługuje również natywnie schemat danych flagi funkcji .NET Core.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Definiowanie flag funkcji i zarządzanie nimi w konfiguracji aplikacji.
> * Dostęp do flag funkcji z aplikacji.

## <a name="create-feature-flags"></a>Tworzenie flag operacji

Menedżer funkcji w witrynie Azure portal dla konfiguracji aplikacji udostępnia interfejs użytkownika do tworzenia i zarządzania flagami funkcji, których używasz w aplikacjach.

Aby dodać nową flagę funkcji:

1. Wybierz **pozycję Menedżer** > operacji **+Dodaj,** aby dodać flagę operacji.

    ![Lista flag funkcji](./media/azure-app-configuration-feature-flags.png)

1. Wprowadź unikatową nazwę klucza dla flagi obiektu. Ta nazwa jest potrzebna do odwoływania się do flagi w kodzie.

1. Jeśli chcesz, nadaj opis flagi funkcji.

1. Ustaw stan początkowy flagi operacji. Ten stan jest zwykle *wyłączony* lub *włączony*. *Stan Włączone* zmienia się na *Warunkowy* po dodaniu filtru do flagi operacji.

    ![Tworzenie flagi funkcji](./media/azure-app-configuration-feature-flag-create.png)

1. Gdy stan jest *włączony*, wybierz **+Dodaj filtr,** aby określić wszelkie dodatkowe warunki, aby zakwalifikować stan. Wprowadź wbudowany lub niestandardowy klucz filtru, a następnie wybierz **+Dodaj parametr,** aby skojarzyć jeden lub więcej parametrów z filtrem. Wbudowane filtry obejmują:

    | Klucz | Parametry JSON |
    |---|---|
    | Microsoft.Percentage | {"Wartość": 0-100 procent} |
    | Microsoft.TimeWindow | {"Start": czas UTC, "Koniec": czas UTC} |

    ![Filtr flagi funkcji](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Aktualizowanie stanów flagi funkcji

Aby zmienić wartość stanu flagi obiektu:

1. Wybierz **Menedżera funkcji**.

1. Po prawej stronie flagi elementu, którą chcesz zmodyfikować, wybierz wielokropek (**...**), a następnie wybierz pozycję **Edytuj**.

1. Ustaw nowy stan flagi operacji.

## <a name="access-feature-flags"></a>Flagi funkcji dostępu

Flagi funkcji utworzone przez Menedżera funkcji są przechowywane i pobierane jako zwykłe wartości kluczy. Są one przechowywane w specjalnym prefiksie `.appconfig.featureflag`obszaru nazw . Aby wyświetlić podstawowe wartości kluczy, użyj Eksploratora konfiguracji. Aplikacja może pobrać te wartości przy użyciu dostawców konfiguracji konfiguracji aplikacji, sdków, rozszerzeń wiersza polecenia i interfejsów API REST.

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak zarządzać flagami funkcji i ich stanami przy użyciu konfiguracji aplikacji. Aby uzyskać więcej informacji na temat obsługi zarządzania funkcjami w konfiguracji aplikacji i ASP.NET Core, zobacz następujący artykuł:

* [Używanie flag funkcji w aplikacji ASP.NET Core](./use-feature-flags-dotnet-core.md)
