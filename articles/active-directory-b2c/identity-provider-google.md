---
title: Konfigurowanie rejestracji i logowanie się za pomocą konta Google
titleSuffix: Azure AD B2C
description: Zapewnij klientom rejestrację i logowanie się do kont Google w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 48955caddb64069f897078f5e47066d9f11d119b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188144"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowanie się za pomocą konta Google przy użyciu usługi Azure Active Directory B2C

## <a name="create-a-google-application"></a>Tworzenie aplikacji Google

Aby używać konta Google jako [dostawcy tożsamości](authorization-code-flow.md) w usłudze Azure Active Directory B2C (Azure AD B2C), musisz utworzyć aplikację w Konsoli Google Dla Deweloperów. Jeśli nie masz jeszcze konta Google, możesz [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)zarejestrować się w pliku .

1. Zaloguj się w [Konsoli Programistów Google](https://console.developers.google.com/) za pomocą danych logowania do konta Google.
1. W lewym górnym rogu strony wybierz listę projektów, a następnie wybierz pozycję **Nowy projekt**.
1. Wprowadź **nazwę projektu**, wybierz pozycję **Utwórz**.
1. Upewnij się, że używasz nowego projektu, wybierając pozycję rozwijaną projektu w lewym górnym rogu ekranu, wybierz projekt według nazwy, a następnie wybierz pozycję **Otwórz**.
1. Wybierz **ekran zgody OAuth** w lewym menu, wybierz pozycję **Zewnętrzne**, a następnie wybierz pozycję **Utwórz**.
Wprowadź **nazwę** aplikacji. Wprowadź *b2clogin.com* w sekcji **Autoryzowane domeny** i wybierz pozycję **Zapisz**.
1. Wybierz **pozycję Poświadczenia** w menu po lewej stronie, a następnie wybierz pozycję **Utwórz identyfikator** > **klienta Oauth**.
1. W obszarze **Typ aplikacji**wybierz pozycję Aplikacja **sieci Web**.
1. Wprowadź **nazwę** aplikacji, wprowadź `https://your-tenant-name.b2clogin.com` w **polu Źródła autoryzowanych javascript oraz** `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` w **autoryzowanych identyfikatorach URI przekierowania**. Zamień `your-tenant-name` na nazwę dzierżawy. Należy użyć wszystkich małych liter podczas wprowadzania nazwy dzierżawy, nawet jeśli dzierżawca jest zdefiniowany za pomocą wielkich liter w usłudze Azure AD B2C.
1. Kliknij przycisk **Utwórz**.
1. Skopiuj wartości **identyfikatora klienta** i **klucza tajnego klienta**. Oba jednych i drugich będą potrzebne do skonfigurowania Google jako dostawcy tożsamości w dzierżawie. **Klucz tajny klienta** jest ważnym poświadczeniem zabezpieczeń.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Konfigurowanie konta Google jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C, wybierając filtr **subskrypcja katalog +** w górnym menu i wybierając katalog zawierający dzierżawę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz **dostawców tożsamości,** a następnie wybierz **Google**.
1. Wprowadź **nazwę**. Na przykład *Google*.
1. W przypadku identyfikatora **klienta**wprowadź identyfikator klienta aplikacji Google, który został utworzony wcześniej.
1. W przypadku **klucza tajnego Klienta**wprowadź zarejestrowany klucz tajny klienta.
1. Wybierz **pozycję Zapisz**.
