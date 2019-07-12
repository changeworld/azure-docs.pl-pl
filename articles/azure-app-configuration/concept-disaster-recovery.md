---
title: Usługa Azure App Configuration odporności i odzyskiwania po awarii | Dokumentacja firmy Microsoft
description: Przegląd sposobu implementacji odporności i odzyskiwania po awarii przy użyciu konfiguracji aplikacji usługi Azure.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 05/29/2019
ms.author: yegu
ms.openlocfilehash: c05957cda16c96b841433483a90429aab2b4d22d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706502"
---
# <a name="resiliency-and-disaster-recovery"></a>Odporność i odzyskiwanie po awarii

Obecnie konfiguracji aplikacji platformy Azure jest to usługa regionalna. Każdy magazyn konfiguracji jest tworzony w danym regionie platformy Azure. Awaria obejmujących cały region ma wpływ na wszystkie magazyny, w tym regionie. Konfiguracja aplikacji nie oferuje automatyczny tryb failover do innego regionu. Ten artykuł zawiera ogólne wskazówki dotyczące jak można użyć wielu magazynów konfiguracji różnych regionach platformy Azure zwiększyć odporność geograficznie aplikacji.

## <a name="high-availability-architecture"></a>Architektura wysokiej dostępności

Do osiągnięcia nadmiarowość między regionami, należy utworzyć wiele konfiguracji sklepów z aplikacjami w różnych regionach. W przypadku takiej konfiguracji Twoja aplikacja ma co najmniej jedną dodatkową konfigurację Sklepu rezerwowe przypadku magazynu podstawowego staje się niedostępny. Na poniższym diagramie przedstawiono topologię między aplikacją i jej magazynów głównych i dodatkowych konfiguracji:

![Geograficznie nadmiarowych magazynach](./media/geo-redundant-app-configuration-stores.png)

Aplikacja ładuje jego konfigurację z obu podstawowych i pomocniczych magazynów równolegle. W ten sposób zwiększa prawdopodobieństwo pomyślnie pobierania danych konfiguracji. Możesz teraz musi zachować synchronizację danych w obu sklepów. W poniższych sekcjach opisano, jak tworzyć geograficznie odporności w aplikacji.

## <a name="failover-between-configuration-stores"></a>Tryb failover między magazynami konfiguracji

Aplikacja nie jest technicznie rzecz biorąc, wykonywanie przejścia w tryb failover. Obejmuje ona próbę pobrania tego samego zestawu danych konfiguracji z dwóch magazynów konfiguracji aplikacji jednocześnie. Rozmieść swój kod, aby najpierw ładuje z pomocniczego magazynu, a następnie zapiszesz podstawowego. Takie podejście zapewnia dane konfiguracji w magazynie podstawowy ma pierwszeństwo, zawsze wtedy, gdy jest ona dostępna. Poniższy fragment kodu przedstawia, w jaki sposób wdrożyć to rozwiązanie w interfejsie wiersza polecenia platformy .NET Core:

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
    }
```

Zwróć uwagę `optional` parametr przekazywany do `AddAzureAppConfiguration` funkcji. Po ustawieniu `true`, ten parametr zapobiega aplikację kończy się niepowodzeniem kontynuować, jeśli funkcja nie można załadować danych konfiguracji.

## <a name="synchronization-between-configuration-stores"></a>Synchronizacja między magazynami konfiguracji

Jest ważne, że wszystkie sklepach konfiguracji magazynu geograficznie nadmiarowego mają tego samego zestawu danych. Możesz użyć **wyeksportować** funkcji w konfiguracji aplikacji, aby skopiować dane z magazynu podstawowego do pomocniczego na żądanie. Ta funkcja jest dostępna za pośrednictwem witryny Azure portal i interfejsu wiersza polecenia.

W witrynie Azure portal możesz wypchnąć zmiany do innego magazynu konfiguracji, wykonując następujące kroki.

1. Przejdź do **Import/Export** , a następnie wybierz pozycję **wyeksportować** > **konfiguracji aplikacji** > **docelowej**  >  **Wybierz zasób**.

2. W otwartym bloku nowe Określ subskrypcji, grupy zasobów i nazwę zasobu magazynu pomocniczego, a następnie wybierz **Zastosuj**.

3. Interfejs użytkownika jest aktualizowany, dzięki czemu można wybrać, jakie dane konfiguracji, którą chcesz wyeksportować do magazynu pomocniczego. Możesz pozostawić domyślnej wartości godziny, ponieważ jest i ustawiane **z etykiety** i **do etykiety** taką samą wartość. Wybierz przycisk **Zastosuj**.

4. Powtórz poprzednie kroki dla wszystkich zmian konfiguracji.

Aby zautomatyzować ten proces eksportu, należy użyć wiersza polecenia platformy Azure. Następujące polecenie pokazuje, jak wyeksportować zmian w konfiguracji jednego z magazynu podstawowego do pomocniczej:

    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób Wzbogać swoje aplikacje do osiągnięcia odporności geograficznie podczas wykonywania dla konfiguracji aplikacji. Możesz również osadzić dane konfiguracyjne z konfiguracji aplikacji w czasie kompilacji lub wdrożenia. Aby uzyskać więcej informacji, zobacz [Integracja z potokiem ciągłej integracji/ciągłego Dostarczania](./integrate-ci-cd-pipeline.md).

