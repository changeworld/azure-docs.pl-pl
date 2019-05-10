---
title: Konfiguracja aplikacji najlepszych rozwiązań dotyczących platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak najlepiej używać konfiguracji aplikacji platformy Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: d1275a48de5cad9321186ba20860d853b8ce55ad
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413616"
---
# <a name="azure-app-configuration-best-practices"></a>Konfiguracja aplikacji najlepszych rozwiązań dotyczących platformy Azure

W tym artykule omówiono typowe wzorce i rozwiązania, korzystając z konfiguracji aplikacji platformy Azure.

## <a name="key-groupings"></a>Grupy kluczy

Konfiguracja aplikacji są dostępne dwie opcje do organizowania klucze: klucz prefiksów lub etykiety. Można użyć jednego lub obu.

Prefiksy klucza to początku części kluczy. Zestaw kluczy można grupować logicznie przy użyciu tego samego prefiksu w nazwach. Prefiksy może zawierać wiele składników połączonych ze sobą według ogranicznika takich jak `/`, podobny do ścieżki adresu URL, w celu utworzenia przestrzeni nazw. Takie hierarchie są przydatne, gdy będą przechowywane klucze dla wielu aplikacji, składników usług i środowisk, w jednym magazynie konfiguracji aplikacji. Ważne jest, aby pamiętać o są klucze kodu aplikacji odwołań do pobierania wartości odpowiednie ustawienia. Klucz nie należy zmieniać. w przeciwnym razie trzeba będzie zmodyfikować kod, za każdym razem, się dzieje.

Etykiety jest atrybut kluczy. Są one używane do tworzenia wariantów klucza. Na przykład możesz przypisać etykiety do wielu wersji klucza. Może być nieco, iteracji, środowiska lub inne informacje kontekstowe. Twoja aplikacja może zażądać zupełnie innego zestawu wartości klucza po prostu, określając inną etykietę. Wszystkie odwołania kluczy może pozostać bez zmian.

## <a name="key-value-compositions"></a>Kompozycje pary klucz wartość

Konfiguracja aplikacji traktuje wszystkie klucze przechowywane z niego jako niezależne podmioty. Nie próbuj wywnioskować żadnych relacji między kluczami ani dziedziczy wartości kluczy, w oparciu o ich hierarchią w. Można zagregować wiele zestawów kluczy, jednak przy użyciu etykiet połączone z odpowiedniej konfiguracji układania w kodzie aplikacji.

Przyjrzyjmy się przykładowi. Mają ustawienie **Asset1** o wartości mogą się różnić dla środowiska "Programowanie". Można utworzyć klucza o nazwie "Asset1" puste etykiety i etykiety o nazwie "Programowanie". Umieść wartość domyślną dla **Asset1** poprzednie i określoną wartość dla "Programowanie" w tym drugim. W kodzie należy najpierw pobrać wartości klucza bez wszelkie etykiety, a następnie te z etykietą "Programowanie", aby zastąpić wszelkie poprzednie wartości tych samych kluczy. Korzystając z nowoczesnego środowiska programowania, takiego jak .NET Core, możesz uzyskać tę możliwość stosu za darmo, jeśli dostawca natywnego konfiguracji umożliwia dostęp do konfiguracji aplikacji. Poniższy fragment kodu pokazuje, jak można implementować układania w aplikacji platformy .NET Core.

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Use(KeyFilter.Any, LabelFilter.Null)
           .Use(KeyFilter.Any, "Development");
});
```

## <a name="app-configuration-bootstrap"></a>Bootstrap konfiguracji aplikacji

Na dostęp do magazynu konfiguracji aplikacji, używając parametrów połączenia, który jest dostępny w witrynie Azure portal. Parametry połączenia zawierają informacje o poświadczeniu i będą traktowane jako wpisy tajne. Muszą one znajdować się w usłudze Key Vault. Lepszym rozwiązaniem jest korzystanie z systemu Azure z tożsamości zarządzanej. Przy użyciu tej metody wystarczy adresu URL punktu końcowego konfiguracji aplikacji do ładowania początkowego dostępu do magazynu konfiguracji. Adres URL można osadzać w kodzie aplikacji (na przykład w *appsettings.json* pliku). Zobacz [integracji z usługą Azure managed tożsamości](howto-integrate-azure-managed-service-identity.md) Aby uzyskać więcej informacji.

## <a name="web-app-or-function-access-to-app-configuration"></a>Aplikacja sieci Web lub funkcji dostęp do konfiguracji aplikacji

Możesz wprowadzić parametry połączenia ze swoim magazynem konfiguracji aplikacji do ustawień aplikacji usługi App Service w witrynie Azure portal. Można również przechowywać go w usłudze Key Vault i [odwoływać się do niego z usługi App Service](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references). Możesz również użyć usługi Azure managed tożsamości dostępu do magazynu konfiguracji. Zobacz [integracji z usługą Azure managed tożsamości](howto-integrate-azure-managed-service-identity.md) Aby uzyskać więcej informacji.

Alternatywnie możesz wypychać konfiguracji z konfiguracji aplikacji w usłudze App Service. Konfiguracja aplikacji udostępnia funkcję eksportu (w witrynie Azure portal i interfejs wiersza polecenia), która wysyła dane bezpośrednio do usługi App Service. Przy użyciu tej metody nie musisz zmieniać kodu aplikacji na wszystkich.

## <a name="next-steps"></a>Kolejne kroki

* [Klucze i wartości](./concept-key-value.md)
