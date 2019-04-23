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
ms.openlocfilehash: 5eaee4f932c4e42f6fed3d839314346b3a93f360
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799542"
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
   - W sekcji **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft (na przykład Skype, Xbox, Outlook.com)**.
   - Wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
1. Na liście stron dla aplikacji wybierz pozycję **Uwierzytelnianie**.
1. W sekcji **Identyfikatory URI przekierowania** znajdź sekcję **Sugerowane identyfikatory URI przekierowania dla klientów publicznych (urządzenia przenośne, komputery)**, a następnie wybierz pozycję **"urn: ietf:wg:oauth:2.0:oob**.
1. Wybierz pozycję **Zapisz**.
1. Przejdź do programu Visual Studio, otwórz *App.xaml.cs* pliku, a następnie zastąp `Enter_the_Application_Id_here` identyfikatorem aplikacji, który właśnie zarejestrowany i skopiować.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```
