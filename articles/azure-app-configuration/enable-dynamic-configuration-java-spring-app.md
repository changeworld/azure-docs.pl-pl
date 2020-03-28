---
title: Używanie konfiguracji dynamicznej w aplikacji Spring Boot
titleSuffix: Azure App Configuration
description: Dowiedz się, jak dynamicznie aktualizować dane konfiguracyjne aplikacji Spring Boot
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 3/5/2020
ms.author: lcozzens
ms.openlocfilehash: 37c832e3b6d1430da0b45558c9632f0486a7233b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79216757"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Samouczek: Używanie konfiguracji dynamicznej w aplikacji Java Spring

Biblioteka klienta wiosennego rozruchu konfiguracji aplikacji obsługuje aktualizowanie zestawu ustawień konfiguracji na żądanie, bez konieczności ponownego uruchamiania aplikacji. Biblioteka klienta buforuje każde ustawienie, aby uniknąć zbyt wielu wywołań do magazynu konfiguracji. Operacja odświeżania nie aktualizuje wartości, dopóki wartość buforowana nie wygaśnie, nawet jeśli wartość została zmieniona w magazynie konfiguracji. Domyślny czas wygaśnięcia dla każdego żądania wynosi 30 sekund. W razie potrzeby można go zastąpić.

Możesz sprawdzić zaktualizowane ustawienia na `AppConfigurationRefresh`żądanie, wywołując metodę 's. `refreshConfigurations()`

Alternatywnie można użyć `spring-cloud-azure-appconfiguration-config-web` pakietu, który ma zależność `spring-web` do obsługi automatycznego odświeżania.

## <a name="use-automated-refresh"></a>Korzystanie z automatycznego odświeżania

Aby użyć automatycznego odświeżania, zacznij od aplikacji Spring Boot korzystającej z konfiguracji aplikacji, takiej jak aplikacja utworzona, wykonując [szybki start szybkiego rozruchu spring boot dla konfiguracji aplikacji](quickstart-java-spring-app.md).

Następnie otwórz plik *pom.xml* w edytorze tekstu `<dependency>` `spring-cloud-azure-appconfiguration-config-web`i dodaj for .

**Wiosenna chmura 1.1.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

**Wiosenna chmura 1.2.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

Zapisz plik, a następnie skompiluj i uruchom aplikację w zwykły sposób.

## <a name="next-steps"></a>Następne kroki

W tym samouczku włączono aplikację Spring Boot, aby dynamicznie odświeżać ustawienia konfiguracji z konfiguracji aplikacji. Aby dowiedzieć się, jak użyć tożsamości zarządzanej platformy Azure, aby usprawnić dostęp do konfiguracji aplikacji, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Integracja tożsamości zarządzanej](./howto-integrate-azure-managed-service-identity.md)
