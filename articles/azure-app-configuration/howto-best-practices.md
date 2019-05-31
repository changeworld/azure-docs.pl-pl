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
ms.openlocfilehash: 3d9a597e7ced631627a121f3f0757e472f9a4bae
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393591"
---
# <a name="azure-app-configuration-best-practices"></a>Konfiguracja aplikacji najlepszych rozwiązań dotyczących platformy Azure

W tym artykule omówiono typowe wzorce i najlepsze rozwiązania, podczas korzystania z konfiguracji aplikacji platformy Azure.

## <a name="key-groupings"></a>Grupy kluczy

Konfiguracja aplikacji są dostępne dwie opcje do organizowania kluczy:

* Prefiksy klucza
* Etykiety

Jedną lub obie opcje służy do grupowania klucze.

*Klucz prefiksy* są tymi częściami początku kluczy. Zestaw kluczy można grupować logicznie przy użyciu tego samego prefiksu w nazwach. Prefiksy może zawierać wiele składników połączone przez ogranicznik, takie jak `/`, podobny do ścieżki adresu URL, w celu utworzenia przestrzeni nazw. Takie hierarchie są przydatne, gdy w przypadku przechowywania kluczy dla wielu aplikacji, składników usług i środowisk, w jednym magazynie konfiguracji aplikacji.

Ważne jest, aby pamiętać o są klucze kodu aplikacji odwołań do pobierania wartości odpowiednie ustawienia. Nie powinno się zmieniać klucze; w przeciwnym razie trzeba będzie zmodyfikować za każdym razem, się dzieje w kodzie.

*Etykiety* są atrybut kluczy. Służą one do utworzenia wariantów klucza. Na przykład możesz przypisać etykiety do wielu wersji klucza. Wersja może być iteracji, środowiska lub inne informacje kontekstowe. Twoja aplikacja może zażądać zupełnie innego zestawu wartości klucza, określając inną etykietę. Co w efekcie wszystkie odwołania kluczy nie ulegają zmianie w kodzie.

## <a name="key-value-compositions"></a>Kompozycje pary klucz wartość

Konfiguracja aplikacji traktuje wszystkie klucze przechowywane z niego jako niezależne podmioty. Konfiguracja aplikacji nie próby wywnioskować żadnych relacji między kluczami lub dziedziczy wartości kluczy, w oparciu o ich hierarchią w. Możesz jednak agregować wiele zestawów kluczy, za pomocą etykiet, w połączeniu z odpowiedniej konfiguracji układania w kodzie aplikacji.

Przyjrzyjmy się przykładowi. Załóżmy, że mają ustawienie o nazwie **Asset1**, którego wartość może się różnić w zależności od środowiska programowania. Możesz utworzyć klucz o nazwie "Asset1" puste etykiety i etykiety o nazwie "Programowanie". W pierwszej etykiecie, umieść wartość domyślną dla **Asset1**, i umieścić określoną wartość dla "Programowanie" w tym drugim.

W kodzie należy najpierw pobrać wartości klucza, bez żadnych etykiet, a następnie pobrać ten sam zestaw wartości klucza po raz drugi z etykietą "Programowanie". Podczas pobierania wartości po raz drugi poprzednie wartości kluczy zostaną zastąpione. Systemu konfiguracji platformy .NET Core umożliwia "stos" wiele zestawów danych konfiguracji na siebie. Jeśli klucz istnieje w więcej niż jeden zestaw, używana jest ostatni zestaw, który go zawiera. Modern framework programowania, takich jak .NET Core umożliwia skorzystanie z tej możliwości stosu za darmo, jeśli dostawca natywnego konfiguracji umożliwia dostęp do konfiguracji aplikacji. Poniższy fragment kodu przedstawia, jak można implementować układania w aplikacji platformy .NET Core:

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

Na dostęp do magazynu konfiguracji aplikacji, używając parametrów połączenia, który jest dostępny w witrynie Azure portal. Ponieważ parametry połączenia zawierają informacji o poświadczeniach, były uważane za klucze tajne. Tych kluczy tajnych, które muszą być przechowywane w usłudze Azure Key Vault, a kod musi uwierzytelniać się Key Vault, aby je pobrać.

Lepszym rozwiązaniem jest użycie funkcji zarządzanych tożsamości w usłudze Azure Active Directory. Z zarządzanych tożsamości należy tylko konfigurację aplikacji punktu końcowego adresu URL do ładowania początkowego dostępu do magazynu konfiguracji aplikacji. Adres URL można osadzać w kodzie aplikacji (na przykład w *appsettings.json* pliku). Zobacz [integracji z usługą Azure managed tożsamości](howto-integrate-azure-managed-service-identity.md) Aby uzyskać szczegółowe informacje.

## <a name="app-or-function-access-to-app-configuration"></a>Aplikacja lub funkcja dostęp do konfiguracji aplikacji

Można zapewnić dostęp do konfiguracji aplikacji dla aplikacji sieci web lub funkcji za pomocą jednej z następujących metod:

* W witrynie Azure portal należy wprowadzić parametry połączenia ze swoim magazynem konfiguracji aplikacji w ustawieniach aplikacji w usłudze App Service.
* Store parametry połączenia ze swoim magazynem konfiguracji aplikacji w usłudze Key Vault i [odwoływać się do niego z usługi App Service](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references).
* Użyj usługi Azure managed tożsamości dostępu do magazynu konfiguracji aplikacji. Aby uzyskać więcej informacji, zobacz [integracji z usługą Azure managed tożsamości](howto-integrate-azure-managed-service-identity.md).
* Wypychanie konfiguracji z konfiguracji aplikacji w usłudze App Service. Konfiguracja aplikacji udostępnia funkcję eksportu (w witrynie Azure portal i interfejsu wiersza polecenia platformy Azure), która wysyła dane bezpośrednio do usługi App Service. Przy użyciu tej metody nie trzeba zmieniać kodu aplikacji na wszystkich.

## <a name="next-steps"></a>Kolejne kroki

* [Klucze i wartości](./concept-key-value.md)
