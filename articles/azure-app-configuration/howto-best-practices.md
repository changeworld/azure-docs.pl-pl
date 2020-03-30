---
title: Najważniejsze wskazówki dotyczące konfiguracji aplikacji platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak najlepiej korzystać z konfiguracji aplikacji platformy Azure
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: df56f53b64a35737700529b80c004efeb31eaabc
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348661"
---
# <a name="azure-app-configuration-best-practices"></a>Najważniejsze wskazówki dotyczące konfiguracji aplikacji platformy Azure

W tym artykule omówiono typowe wzorce i najlepsze rozwiązania podczas korzystania z konfiguracji aplikacji platformy Azure.

## <a name="key-groupings"></a>Grupowanie kluczy

Konfiguracja aplikacji udostępnia dwie opcje organizowania kluczy:

* Prefiksy kluczy
* Etykiety

Do grupowania kluczy można użyć jednej lub obu opcji.

*Prefiksy kluczy* są początkowymi częściami kluczy. Można logicznie grupować zestaw kluczy przy użyciu tego samego prefiksu w ich nazwach. Prefiksy mogą zawierać wiele składników połączonych `/`ogranicznikiem, takich jak , podobnie jak ścieżka adresu URL, w celu utworzenia obszaru nazw. Takie hierarchie są przydatne podczas przechowywania kluczy dla wielu aplikacji, usług składników i środowisk w jednym magazynie konfiguracji aplikacji.

Ważną rzeczą, o której należy pamiętać, jest to, że klucze są odwołania do kodu aplikacji, aby pobrać wartości odpowiednich ustawień. Klucze nie powinny się zmieniać, w przeciwnym razie trzeba będzie zmodyfikować kod za każdym razem, gdy to się stanie.

*Etykiety* są atrybutem w kluczach. Są one używane do tworzenia wariantów klucza. Na przykład można przypisać etykiety do wielu wersji klucza. Wersja może być iteracji, środowiska lub innych informacji kontekstowych. Aplikacja może zażądać zupełnie inny zestaw wartości kluczy, określając inną etykietę. W rezultacie wszystkie odwołania do kluczy pozostają niezmienione w kodzie.

## <a name="key-value-compositions"></a>Kompozycje klucz-wartość

Konfiguracja aplikacji traktuje wszystkie klucze przechowywane z nim jako niezależne jednostki. Konfiguracja aplikacji nie próbuje wywnioskować żadnej relacji między kluczami lub dziedziczyć wartości klucza na podstawie ich hierarchii. Można jednak agregować wiele zestawów kluczy przy użyciu etykiet w połączeniu z prawidłową konfiguracją układania w kodzie aplikacji.

Spójrzmy na przykład. Załóżmy, że masz ustawienie o nazwie **Asset1**, którego wartość może się różnić w zależności od środowiska programistycznego. Tworzysz klucz o nazwie "Asset1" z pustą etykietą i etykietą o nazwie "Rozwój". W pierwszej etykiecie należy umieścić wartość domyślną dla **zasobu1**i umieścić określoną wartość dla "Rozwoju" w drugim.

W kodzie najpierw pobrać wartości klucza bez żadnych etykiet, a następnie pobrać ten sam zestaw wartości klucza po raz drugi z etykietą "Rozwoju". Podczas pobierania wartości po raz drugi, poprzednie wartości kluczy są zastępowane. System konfiguracji .NET Core umożliwia "układanie" wielu zestawów danych konfiguracyjnych jeden na drugim. Jeśli klucz istnieje w więcej niż jednym zestawie, używany jest ostatni zestaw, który go zawiera. Dzięki nowoczesnej platformie programowania, takiej jak .NET Core, otrzymasz tę funkcję układania bezpłatnie, jeśli używasz natywnego dostawcy konfiguracji, aby uzyskać dostęp do konfiguracji aplikacji. Poniższy fragment kodu pokazuje, jak można zaimplementować układanie w aplikacji .NET Core:

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

[Użyj etykiet, aby włączyć różne konfiguracje dla różnych środowisk](./howto-labels-aspnet-core.md) zawiera pełny przykład.

## <a name="app-configuration-bootstrap"></a>Bootstrap konfiguracji aplikacji

Aby uzyskać dostęp do magazynu konfiguracji aplikacji, można użyć jego parametry połączenia, który jest dostępny w witrynie Azure portal. Ponieważ parametry połączenia zawierają informacje o poświadczeniach, są one uważane za wpisy tajne. Te wpisy tajne muszą być przechowywane w usłudze Azure Key Vault, a kod musi być uwierzytelniany w magazynie kluczy, aby je pobrać.

Lepszym rozwiązaniem jest użycie funkcji tożsamości zarządzanych w usłudze Azure Active Directory. W tożsamościach zarządzanych potrzebny jest tylko adres URL punktu końcowego konfiguracji aplikacji, aby uzyskać dostęp do sklepu konfiguracji aplikacji. Adres URL można osadzić w kodzie aplikacji (na przykład w pliku *appsettings.json).* Zobacz [integrowanie z tożsamościami zarządzanymi platformy Azure, aby](howto-integrate-azure-managed-service-identity.md) uzyskać szczegółowe informacje.

## <a name="app-or-function-access-to-app-configuration"></a>Dostęp do aplikacji lub funkcji w konfiguracji aplikacji

Dostęp do konfiguracji aplikacji dla aplikacji lub funkcji sieci Web można zapewnić za pomocą dowolnej z następujących metod:

* Za pośrednictwem portalu Azure wprowadź parametry połączenia do magazynu konfiguracji aplikacji w ustawieniach aplikacji usługi App Service.
* Zapisz parametry połączenia do magazynu konfiguracji aplikacji w magazynie aplikacji w magazynie aplikacji i [odwołujej się do niego z usługi App Service](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references).
* Użyj tożsamości zarządzanych platformy Azure, aby uzyskać dostęp do magazynu konfiguracji aplikacji. Aby uzyskać więcej informacji, zobacz [Integrowanie z tożsamościami zarządzanymi platformy Azure](howto-integrate-azure-managed-service-identity.md).
* Wypychanie konfiguracji z konfiguracji aplikacji do usługi App Service. Konfiguracja aplikacji zapewnia funkcję eksportu (w witrynie Azure portal i interfejsu wiersza polecenia platformy Azure), która wysyła dane bezpośrednio do usługi App Service. Za pomocą tej metody nie trzeba w ogóle zmieniać kodu aplikacji.

## <a name="reduce-requests-made-to-app-configuration"></a>Zmniejsz liczbę żądań kierowanych do konfiguracji aplikacji

Nadmierne żądania do konfiguracji aplikacji może spowodować ograniczenie lub opłaty za nadmiar. Aby zmniejszyć liczbę złożonych wniosków:

* Zwiększ limit czasu odświeżania, zwłaszcza jeśli wartości konfiguracji nie zmieniają się często. Określ nowy limit czasu odświeżania przy użyciu [ `SetCacheExpiration` metody](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.azureappconfigurationrefreshoptions.setcacheexpiration).

* Oglądaj pojedynczy *klucz wartownika,* zamiast oglądać poszczególne klawisze. Odśwież całą konfigurację tylko wtedy, gdy zmieni się klucz wartownika. Zobacz [przykład używanie konfiguracji dynamicznej w aplikacji ASP.NET Core.](enable-dynamic-configuration-aspnet-core.md)

* Użyj usługi Azure Event Grid, aby otrzymywać powiadomienia o zmianach konfiguracji, a nie stale sondowania dla wszelkich zmian. Aby uzyskać więcej informacji, zobacz [Kierowanie zdarzeń konfiguracji aplikacji platformy Azure do punktu końcowego sieci Web](./howto-app-configuration-event.md)

## <a name="importing-configuration-data-into-app-configuration"></a>Importowanie danych konfiguracyjnych do konfiguracji aplikacji

Konfiguracja aplikacji oferuje opcję zbiorczego [importowania](https://aka.ms/azconfig-importexport1) ustawień konfiguracji z bieżących plików konfiguracyjnych przy użyciu witryny Azure portal lub interfejsu wiersza polecenia. Można również użyć tych samych opcji do eksportowania wartości z konfiguracji aplikacji, na przykład między powiązanymi sklepami. Jeśli chcesz skonfigurować ciągłą synchronizację z repozytorium GitHub, możesz użyć naszej [akcji GitHub,](https://aka.ms/azconfig-gha2) aby nadal korzystać z istniejących praktyk kontroli źródła, jednocześnie uzyskując korzyści z konfiguracji aplikacji.

## <a name="next-steps"></a>Następne kroki

* [Klawisze i wartości](./concept-key-value.md)
