---
title: Użyj filtrów funkcji, aby włączyć funkcję dla podzbioru użytkowników
titleSuffix: Azure App Configuration
description: Dowiedz się, jak używać filtrów funkcji do włączania funkcji dla podzbioru użytkowników
ms.service: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 181c97615985283011834dcf9145810b1563fb4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057000"
---
# <a name="use-feature-filters-to-enable-a-feature-for-a-subset-of-users"></a>Użyj filtrów funkcji, aby włączyć funkcję dla podzbioru użytkowników

Flagi funkcji umożliwiają aktywowanie lub dezaktywację funkcji w aplikacji. Flaga prostej funkcji jest wł.lub wyłączona. Aplikacja zawsze zachowuje się tak samo. Na przykład można rozwinąć nową operację za flagą elementu. Gdy flaga funkcji jest włączona, wszyscy użytkownicy widzą nową funkcję. Wyłączenie flagi operacji powoduje ukrycie nowej funkcji.

Z kolei _flaga operacji warunkowej_ umożliwia dynamiczne włączanie lub wyłączenie flagi operacji. Aplikacja może zachowywać się inaczej, w zależności od kryteriów flagi funkcji. Załóżmy, że najpierw chcesz wyświetlić nową funkcję niewielkiej podgrupie użytkowników. Flaga funkcji warunkowych umożliwia włączenie flagi funkcji dla niektórych użytkowników, jednocześnie wyłączając ją dla innych. _Filtry funkcji_ określają stan flagi operacji za każdym razem, gdy jest oceniana.

Biblioteka `Microsoft.FeatureManagement` zawiera dwa filtry funkcji:

- `PercentageFilter`włącza flagę operacji na podstawie wartości procentowej.
- `TimeWindowFilter`włącza flagę operacji w określonym przedziale czasu.

Można również utworzyć własny filtr funkcji, który implementuje [interfejs Microsoft.FeatureManagement.IFeatureFilter](/dotnet/api/microsoft.featuremanagement.ifeaturefilter).

## <a name="registering-a-feature-filter"></a>Rejestrowanie filtru funkcji

Filtr funkcji można zarejestrować, wywołując `AddFeatureFilter` metodę, określając nazwę filtru funkcji. Na przykład następujące rejestry `PercentageFilter`kodu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>Konfigurowanie filtru funkcji w konfiguracji aplikacji platformy Azure

Niektóre filtry funkcji mają dodatkowe ustawienia. Na przykład `PercentageFilter` aktywuje obiekt na podstawie wartości procentowej. Ma ustawienie definiujące procent do użycia.

Można skonfigurować te ustawienia dla flag funkcji zdefiniowanych w konfiguracji aplikacji platformy Azure. Na przykład wykonaj następujące `PercentageFilter` kroki, aby użyć, aby włączyć flagę funkcji dla 50% żądań do aplikacji sieci web:

1. Postępuj zgodnie z instrukcjami w [przewodniku Szybki start: dodaj flagi funkcji do aplikacji ASP.NET Core,](./quickstart-feature-flag-aspnet-core.md) aby utworzyć aplikację internetową z flagą funkcji.

1. W witrynie Azure portal przejdź do magazynu konfiguracji i kliknij pozycję **Menedżer funkcji**.

1. Kliknij menu kontekstowe flagi funkcji *beta* utworzonej w przewodniku Szybki start. Kliknij pozycję **Edytuj**.

    > [!div class="mx-imgBorder"]
    > ![Flaga funkcji Edycja wersji beta](./media/edit-beta-feature-flag.png)

1. Na ekranie **Edycja** wybierz przycisk opcji **Na,** jeśli nie jest jeszcze zaznaczony. Następnie kliknij przycisk **Dodaj filtr.** (Etykieta przycisku opcji **On** zmieni się na odczyt **warunkowy).**

1. W polu **Klucz** wprowadź wartość *Microsoft.Percentage*.

    > [!div class="mx-imgBorder"]
    > ![Dodaj filtr operacji](./media/feature-flag-add-filter.png)

1. Kliknij menu kontekstowe obok klawisza filtru funkcji. Kliknij **pozycję Edytuj parametry**.

    > [!div class="mx-imgBorder"]
    > ![Edytowanie parametrów filtru operacji](./media/feature-flag-edit-filter-parameters.png)

1. Umieść wskaźnik myszy pod **nagłówkiem Nazwa,** aby pola tekstowe były wyświetlane w siatce. Wprowadź **nazwę** *wartości* i **wartość** 50. Pole **Wartość** wskazuje procent żądań, dla których ma być włączany filtr operacji.

    > [!div class="mx-imgBorder"]
    > ![Ustawianie parametrów filtru operacji](./media/feature-flag-set-filter-parameters.png)

1. Kliknij **przycisk Zastosuj,** aby powrócić do ekranu **flagi funkcji Edycja.** Następnie kliknij przycisk **Zastosuj** ponownie, aby zapisać ustawienia flagi obiektu.

1. Flaga **Stan** obiektu jest teraz wyświetlana jako *warunkowa*. Ten stan wskazuje, że flaga funkcji zostanie włączona lub wyłączona na podstawie żądania, na podstawie kryteriów wymuszanych przez filtr funkcji.

    > [!div class="mx-imgBorder"]
    > ![Flaga obiektu warunkowego](./media/feature-flag-filter-enabled.png)

## <a name="feature-filters-in-action"></a>Filtry funkcji w akcji

Aby zobaczyć efekty tej flagi funkcji, uruchom aplikację i naciśnij przycisk **Odśwież** w przeglądarce wiele razy. Zobaczysz, że element *beta* pojawia się na pasku narzędzi przez około 50% czasu. Jest ukryta przez resztę czasu, `PercentageFilter` ponieważ dezaktywuje funkcję *Beta* dla podzbioru żądań. W poniższym filmie przedstawiono to zachowanie w działaniu.

> [!div class="mx-imgBorder"]
> ![PercentageFilter w akcji](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Omówienie zarządzania funkcjami](./concept-feature-management.md)
