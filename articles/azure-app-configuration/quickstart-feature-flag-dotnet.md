---
title: Szybki start do dodawania flag funkcji do aplikacji .NET Framework | Dokumenty firmy Microsoft | Dokumenty firmy Microsoft
description: Szybki start do dodawania flag funkcji do aplikacji platformy .NET Framework i zarządzania nimi w konfiguracji aplikacji platformy Azure
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: 5ea9749c07aadc7037e753160e9b053992bebae2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77619313"
---
# <a name="quickstart-add-feature-flags-to-a-net-framework-app"></a>Szybki start: dodawanie flag funkcji do aplikacji .NET Framework

W tym przewodniku Szybki start można włączyć konfigurację aplikacji platformy Azure do aplikacji .NET Framework, aby utworzyć kompleksową implementację zarządzania funkcjami. Za pomocą usługi Konfiguracja aplikacji można centralnie przechowywać wszystkie flagi funkcji i kontrolować ich stany. 

Biblioteki zarządzania funkcjami platformy .NET rozszerzają strukturę o kompleksową obsługę flag funkcji. Biblioteki te są zbudowane na systemie konfiguracji platformy .NET. Bezproblemowo integrują się z konfiguracją aplikacji za pośrednictwem dostawcy konfiguracji platformy .NET.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.8](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Tworzenie sklepu konfiguracji aplikacji

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Wybierz **pozycję Menedżer** > operacji **+Dodaj,** aby dodać flagę operacji o nazwie `Beta`.

    > [!div class="mx-imgBorder"]
    > ![Włącz flagę funkcji o nazwie Beta](media/add-beta-feature-flag.png)

    Zostaw `label` niezdefiniowane na razie.

## <a name="create-a-net-console-app"></a>Tworzenie aplikacji konsolowej platformy .NET

1. Uruchom program Visual Studio i wybierz pozycję **Plik** > **nowego** > **projektu**.

1. W **programie Tworzenie nowego projektu**filtruj typ projektu **konsoli** i kliknij aplikację Console **App (.NET Framework).** Kliknij przycisk **alej**.

1. W **obszarze Konfiguruj nowy projekt**wprowadź nazwę projektu. W obszarze **Framework**wybierz opcję **.NET Framework 4.8** lub nowszą. Kliknij przycisk **Utwórz**.

## <a name="connect-to-an-app-configuration-store"></a>Łączenie się ze sklepem konfiguracji aplikacji

1. Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Zarządzaj pakietami NuGet**. Na karcie **Przeglądaj** wyszukaj i dodaj do projektu następujące pakiety NuGet. Jeśli nie możesz ich znaleźć, zaznacz pole wyboru **Dołącz wydanie wstępne.**

    ```
    Microsoft.Extensions.DependencyInjection
    Microsoft.Extensions.Configuration.AzureAppConfiguration
    Microsoft.FeatureManagement
    ```

1. Otwórz *Program.cs* i dodaj następujące instrukcje:

    ```csharp
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    using Microsoft.FeatureManagement;
    ```

1. Zaktualizuj `Main` metodę, aby połączyć się z konfiguracją `UseFeatureFlags` aplikacji, określając opcję, tak aby flagi funkcji były pobierane. Następnie wyświetl komunikat, `Beta` jeśli flaga obiektu jest włączona.

    ```csharp
        public static async Task Main(string[] args)
        {         
            IConfigurationRoot configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                           .UseFeatureFlags();
                }).Build();

            IServiceCollection services = new ServiceCollection();

            services.AddSingleton<IConfiguration>(configuration).AddFeatureManagement();

            using (ServiceProvider serviceProvider = services.BuildServiceProvider())
            {
                IFeatureManager featureManager = serviceProvider.GetRequiredService<IFeatureManager>();

                if (await featureManager.IsEnabledAsync("Beta"))
                {
                    Console.WriteLine("Welcome to the beta!");
                }
            }

            Console.WriteLine("Hello World!");
        }
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Ustaw zmienną środowiskową o nazwie **ConnectionString** na ciąg połączenia magazynu konfiguracji aplikacji. Jeśli używasz wiersza polecenia systemu Windows, uruchom następujące polecenie:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Jeśli używasz programu Windows PowerShell, uruchom następujące polecenie:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Uruchom ponownie program Visual Studio, aby umożliwić zmianę, aby wejść w życie. 

1. Naciśnij klawisze Ctrl + F5, aby utworzyć i uruchomić aplikację konsoli.

    ![Aplikacja z włączoną flagą funkcji](./media/quickstarts/dotnet-app-feature-flag.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono flagę funkcji w konfiguracji aplikacji i użyto jej z aplikacją konsoli .NET Framework. Aby dowiedzieć się, jak dynamicznie aktualizować flagi funkcji i inne wartości konfiguracji bez ponownego uruchamiania aplikacji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Włącz konfigurację dynamiczną](./enable-dynamic-configuration-dotnet.md)
