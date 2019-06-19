---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: c4dc3d422e18d9ee41bf16ac3e6f22c3d7e466d7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183407"
---
## <a name="register-your-application"></a>Rejestrowanie aplikacji

Aplikację można zarejestrować na dwa sposoby.

### <a name="option-1-express-mode"></a>Opcja 1: Tryb ekspresowy

Aplikację można szybko zarejestrować, wykonując następujące czynności:
1. Przejdź do [portal Azure — rejestrowanie aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację za pomocą tylko jednego kliknięcia.

### <a name="option-2-advanced-mode"></a>Opcja 2: Tryb zaawansowany

Aby zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
1. Przejdź do platforma tożsamości firmy Microsoft dla deweloperów [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) strony.
1. Wybierz **nowej rejestracji**.
   - W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `Win-App-calling-MsGraph`.
   - W sekcji **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft (na przykład Skype, Xbox, Outlook.com)** .
   - Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
1. Na liście stron dla aplikacji wybierz pozycję **Uwierzytelnianie**.
   1. W **identyfikatory URI przekierowań** sekcji na liście identyfikatory URI przekierowań:
   1. W **typu** wybierz kolumny **klientem publicznym (mobilnych i klasycznych)** .
   1. Wprowadź `urn:ietf:wg:oauth:2.0:oob` w **identyfikator URI PRZEKIEROWANIA** kolumny.
1. Wybierz pozycję **Zapisz**.
1. Przejdź do programu Visual Studio, otwórz *App.xaml.cs* pliku, a następnie zastąp `Enter_the_Application_Id_here` identyfikatorem aplikacji, który właśnie zarejestrowany i skopiować.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```
