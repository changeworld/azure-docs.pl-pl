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
ms.openlocfilehash: 39e7a62899a7d1d6feb5bfd3b45ad4adc3c996a0
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394027"
---
# <a name="resiliency-and-disaster-recovery"></a>Odporność i odzyskiwanie po awarii

Obecnie konfiguracji aplikacji platformy Azure jest to usługa regionalna. Każdy magazyn konfiguracji jest tworzony w danym regionie platformy Azure. Awaria obejmujących cały region będzie miało wpływ na wszystkie magazyny, w tym regionie. Konfiguracja aplikacji nie oferuje automatycznego trybu failover do innego regionu. Ten artykuł zawiera ogólne wskazówki dotyczące jak można użyć wielu magazynów konfiguracji różnych regionach platformy Azure zwiększyć odporność geograficznie aplikacji.

## <a name="high-availability-architecture"></a>Architektura wysokiej dostępności

Do osiągnięcia nadmiarowość między regionami, należy utworzyć wiele konfiguracji sklepów z aplikacjami w różnych regionach. W przypadku takiej konfiguracji aplikacji będzie mieć co najmniej jedną sklepu dodatkowe czynności konfiguracyjne, aby wrócić na z magazynu podstawowego staje się niedostępny. Poniżej przedstawiono diagram, który ilustruje topologię między aplikacją i jej magazynów głównych i dodatkowych konfiguracji.

![Geograficznie nadmiarowych magazynach](./media/geo-redundant-app-configuration-stores.png)

Aplikacja będzie ładować jego konfigurację z obu podstawowych i pomocniczych magazynów równolegle. W ten sposób zwiększa prawdopodobieństwo pomyślnie znacznie pobierania danych konfiguracji. Możesz teraz musi zachować synchronizację danych w obu sklepów. W poniższych sekcjach opisano, jak tworzyć geograficznie odporności w aplikacji.

## <a name="failover-between-configuration-stores"></a>Tryb failover między magazynami konfiguracji

Z technicznego punktu widzenia aplikacji nie jest wykonywanie pracy w trybie failover. Obejmuje ona próbę jednocześnie pobierać ten sam zestaw danych konfiguracji z dwóch konfiguracji sklepów z aplikacjami. Należy określić kod tak, ładuje najpierw z magazynu pomocniczego najpierw, a następnie zapiszesz podstawowego. To podejście zapewnia dane konfiguracji w magazynie podstawowym wyższy priorytet, ilekroć są one dostępne. Poniższy fragment kodu pokazuje, jak można zaimplementować to platformie .NET Core.

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

Zwróć uwagę `optional` parametr przekazywany do `AddAzureAppConfiguration` funkcji. Po ustawieniu `true`, ten parametr zostanie uniemożliwiają aplikacji kończy się niepowodzeniem kontynuować, jeśli funkcja nie można załadować danych konfiguracji.

## <a name="synchronization-between-configuration-stores"></a>Synchronizacja między magazynami konfiguracji

Jest ważne, że wszystkie sklepach konfiguracji magazynu geograficznie nadmiarowego mają tego samego zestawu danych. Możesz użyć **wyeksportować** funkcji w konfiguracji aplikacji, aby skopiować dane z magazynu podstawowego do pomocniczego na żądanie. Ta funkcja jest dostępna za pośrednictwem witryny Azure portal i interfejsu wiersza polecenia.

W witrynie Azure portal możesz wypchnąć zmiany do innego magazynu konfiguracji, wykonując następujące czynności:

1. Przejdź do **Import/Export** zaznacz **wyeksportować**, wybierz opcję **konfiguracji aplikacji** jako **docelowej** usługi, kliknij przycisk  **Wybierz zasób**.

2. W nowym bloku, który został otwarty w górę, określ subskrypcji, grupy zasobów i nazwę zasobu magazynu pomocniczego, a następnie kliknij przycisk **Zastosuj**.

3. Interfejs użytkownika zostanie zaktualizowany, tak, aby można było wybrać jakie dane konfiguracji, którą chcesz wyeksportować do magazynu pomocniczego. Możesz pozostawić domyślnej wartości godziny, ponieważ jest i ustawiane **z etykiety** i **etykiety** taką samą wartość. Kliknij przycisk **Zastosuj**.

4. Powtórz powyższe kroki dla wszystkich zmian konfiguracji.

Można to zautomatyzować eksportowanie proces, korzystając z wiersza polecenia platformy Azure. Poniższe polecenie pokazuje, jak można wyeksportować zmian w konfiguracji jednego z magazynu podstawowego do regionu pomocniczego.

    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób Wzbogać swoje aplikacje do osiągnięcia odporności geograficznie podczas wykonywania dla konfiguracji aplikacji. Alternatywnie można osadzić danych konfiguracji z konfiguracji aplikacji w czasie kompilacji lub wdrożenia. Aby uzyskać więcej informacji, zobacz [Integracja z potokiem ciągłej integracji/ciągłego Dostarczania](./integrate-ci-cd-pipeline.md).

