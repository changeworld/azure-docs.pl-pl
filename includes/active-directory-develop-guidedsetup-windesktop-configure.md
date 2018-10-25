---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: b26b88d0e089217fa9915bdbdcb8f913731bcc67
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988212"
---
## <a name="register-your-application"></a>Rejestrowanie aplikacji

Aplikację można zarejestrować na dwa sposoby.

### <a name="option-1-express-mode"></a>Opcja 1: Tryb ekspresowy

Aplikację można szybko zarejestrować, wykonując następujące czynności:
1. Przejdź do [portalu rejestracji aplikacji firmy Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure).

2. Wybierz **Dodaj aplikację**.

3. W polu **Application Name** (Nazwa aplikacji) wprowadź nazwę aplikacji.

4. Upewnij się, że **instrukcje konfiguracji** pole wyboru jest wybrany, a następnie wybierz **Utwórz**.

5. Postępuj zgodnie z instrukcjami dotyczącymi uzyskiwania Identyfikatora aplikacji, a następnie wklej go w kodzie.

### <a name="option-2-advanced-mode"></a>Opcja 2: Tryb zaawansowany

Aby zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
1. Jeśli nie została jeszcze zarejestrowana aplikacja, przejdź do strony [portalu rejestracji aplikacji Microsoft](https://apps.dev.microsoft.com/portal/register-app).

2. Wybierz **Dodaj aplikację**.

3. W polu **Application Name** (Nazwa aplikacji) wprowadź nazwę aplikacji.

4. Upewnij się, że pole **Guided Setup** (Konfiguracja z przewodnikiem) jest wyczyszczone, a następnie wybierz pozycję **Create** (Utwórz).

5. Wybierz kolejno pozycje **Add platform** (Dodaj platformę), **Native Application** (Aplikacja natywna) i **Save** (Zapisz).

6. W **identyfikator aplikacji** Skopiuj identyfikator GUID.

7. Przejdź do programu Visual Studio, otwórz *App.xaml.cs* pliku, a następnie zastąp `your_client_id_here` identyfikatorem aplikacji, który właśnie zarejestrowany i skopiować.

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```
