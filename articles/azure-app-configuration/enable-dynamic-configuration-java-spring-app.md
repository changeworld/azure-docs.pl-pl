---
title: Używanie konfiguracji dynamicznej w aplikacji do rozruchu sprężynowego
titleSuffix: Azure App Configuration
description: Dowiedz się, jak dynamicznie aktualizować dane konfiguracji dla aplikacji do rozruchu sprężynowego
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 3/5/2020
ms.author: lcozzens
ms.openlocfilehash: 37c832e3b6d1430da0b45558c9632f0486a7233b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79216757"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Samouczek: używanie konfiguracji dynamicznej w aplikacji ze sprężyną Java

Biblioteka klienta sieci komputerowej ze sprężyną konfiguracji aplikacji obsługuje aktualizowanie zestawu ustawień konfiguracji na żądanie bez powodowania ponownego uruchomienia aplikacji. Biblioteka klienta buforuje każde ustawienie, aby uniknąć zbyt wielu wywołań magazynu konfiguracji. Operacja odświeżania nie aktualizuje wartości do momentu wygaśnięcia wartości w pamięci podręcznej, nawet jeśli wartość została zmieniona w magazynie konfiguracji. Domyślny czas wygaśnięcia dla każdego żądania wynosi 30 sekund. W razie potrzeby można go zastąpić.

Możesz sprawdzić dostępność zaktualizowanych ustawień na żądanie, wywołując metodę `refreshConfigurations()` `AppConfigurationRefresh`.

Alternatywnie można użyć pakietu `spring-cloud-azure-appconfiguration-config-web`, który ma zależność od `spring-web` do obsługi automatycznego odświeżania.

## <a name="use-automated-refresh"></a>Użyj automatycznego odświeżania

Aby użyć automatycznego odświeżania, Zacznij od aplikacji z rozruchem sprężyny korzystającej z konfiguracji aplikacji, takiej jak utworzona przez Ciebie aplikacja, postępując zgodnie z [przewodnikiem Szybki Start dla konfiguracji aplikacji](quickstart-java-spring-app.md).

Następnie otwórz plik *pliku pom. XML* w edytorze tekstów i Dodaj `<dependency>` dla `spring-cloud-azure-appconfiguration-config-web`.

**Chmura Wiosenna 1.1. x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

**Chmura Wiosenna 1.2. x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

Zapisz plik, a następnie Skompiluj i uruchom aplikację w zwykły sposób.

## <a name="next-steps"></a>Następne kroki

W tym samouczku włączono aplikację rozruchu sprężynowego do dynamicznego odświeżania ustawień konfiguracji z poziomu konfiguracji aplikacji. Aby dowiedzieć się, jak za pomocą tożsamości zarządzanej platformy Azure usprawnić dostęp do konfiguracji aplikacji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanej](./howto-integrate-azure-managed-service-identity.md)
