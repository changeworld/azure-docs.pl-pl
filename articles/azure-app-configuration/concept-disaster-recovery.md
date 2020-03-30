---
title: Odporność konfiguracji aplikacji platformy Azure i odzyskiwanie po awarii
description: Odchylenie sposobu implementacji odporności i odzyskiwania po awarii za pomocą konfiguracji aplikacji platformy Azure.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 96ef09ac081aa328014217592a7fcd3ed6314c0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523768"
---
# <a name="resiliency-and-disaster-recovery"></a>Odporność i odzyskiwanie po awarii

Obecnie konfiguracja aplikacji platformy Azure jest usługą regionalną. Każdy magazyn konfiguracji jest tworzony w określonym regionie platformy Azure. Awaria w całym regionie dotyczy wszystkich magazynów w tym regionie. Konfiguracja aplikacji nie oferuje automatycznego trybu failover w innym regionie. Ten artykuł zawiera ogólne wskazówki dotyczące sposobu używania wielu magazynów konfiguracji w regionach platformy Azure w celu zwiększenia odporności geograficznej aplikacji.

## <a name="high-availability-architecture"></a>Architektura o wysokiej dostępności

Aby zrealizować nadmiarowość między regionami, należy utworzyć wiele magazynów konfiguracji aplikacji w różnych regionach. W tej konfiguracji aplikacja ma co najmniej jeden dodatkowy magazyn konfiguracji, aby wrócić, jeśli magazyn podstawowy staje się niedostępny. Na poniższym diagramie przedstawiono topologię między aplikacją a jej podstawowymi i pomocniczymi magazynami konfiguracji:

![Sklepy z nadmiarowe](./media/geo-redundant-app-configuration-stores.png)

Aplikacja ładuje swoją konfigurację z magazynów podstawowych i pomocniczych równolegle. W ten sposób zwiększa szansę na pomyślne uzyskanie danych konfiguracji. Użytkownik jest odpowiedzialny za synchronizację danych w obu sklepach. W poniższych sekcjach wyjaśniono, jak można tworzyć odporność geograficzną w aplikacji.

## <a name="failover-between-configuration-stores"></a>Tryb failover między magazynami konfiguracji

Technicznie aplikacja nie wykonuje pracy awaryjnej. Próbuje pobrać ten sam zestaw danych konfiguracyjnych z dwóch magazynów konfiguracji aplikacji jednocześnie. Rozmieść kod tak, aby ładuł się najpierw z magazynu pomocniczego, a następnie z magazynu podstawowego. Takie podejście gwarantuje, że dane konfiguracji w magazynie podstawowym ma pierwszeństwo, gdy jest dostępny. Poniższy fragment kodu pokazuje, jak można zaimplementować ten układ w .NET Core:

#### <a name="net-core-2x"></a>[.NET Rdzeń 2.x](#tab/core2x)

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                  .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
        })
        .UseStartup<Startup>();
    
```

#### <a name="net-core-3x"></a>[.NET Rdzeń 3.x](#tab/core3x)

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                    .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
            })
            .UseStartup<Startup>());
```
---

Zwróć `optional` uwagę, że `AddAzureAppConfiguration` parametr przeszedł do funkcji. Po ustawieniu `true`na , ten parametr uniemożliwia aplikacji niekontynuowanie, jeśli funkcja nie może załadować danych konfiguracyjnych.

## <a name="synchronization-between-configuration-stores"></a>Synchronizacja między magazynami konfiguracji

Ważne jest, aby wszystkie magazyny konfiguracji geograficznie nadmiarowe miały ten sam zestaw danych. Za pomocą funkcji **Eksportuj** w konfiguracji aplikacji można kopiować dane z magazynu podstawowego do magazynu pomocniczego na żądanie. Ta funkcja jest dostępna za pośrednictwem witryny Azure portal i interfejsu wiersza polecenia.

Z witryny Azure portal można wypchnąć zmiany do innego magazynu konfiguracji, wykonując następujące kroki.

1. Przejdź do karty **Importuj/Eksportuj** i wybierz pozycję **Eksportuj** > **miejsce docelowe** > **konfiguracji** > aplikacji**Wybierz zasób**.

1. W nowym bloku, który zostanie otwarty, określ subskrypcję, grupę zasobów i nazwę zasobu magazynu dodatkowego, a następnie wybierz pozycję **Zastosuj**.

1. Interfejs użytkownika jest aktualizowany, dzięki czemu można wybrać, jakie dane konfiguracji mają być eksportowane do magazynu dodatkowego. Można pozostawić domyślną wartość czasu, jak jest i ustawić zarówno **od etykiety** i **do etykiety** do tej samej wartości. Wybierz przycisk **Zastosuj**.

1. Powtórz poprzednie kroki dla wszystkich zmian konfiguracji.

Aby zautomatyzować ten proces eksportowania, użyj interfejsu wiersza polecenia platformy Azure. Następujące polecenie pokazuje, jak wyeksportować pojedynczą zmianę konfiguracji z magazynu podstawowego do pomocniczego:

```azurecli
    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}
```

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak rozszerzyć aplikację, aby osiągnąć odporność geograficzną podczas wykonywania konfiguracji aplikacji. Można również osadzić dane konfiguracji z konfiguracji aplikacji w czasie kompilacji lub wdrażania. Aby uzyskać więcej informacji, zobacz [Integrowanie z potokiem ciągłej integracji/ciągłego wdrażania](./integrate-ci-cd-pipeline.md).
