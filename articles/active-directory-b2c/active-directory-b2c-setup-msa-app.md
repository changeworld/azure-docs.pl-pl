---
title: Konfigurowanie rejestrowania i zaloguj się przy użyciu konta Microsoft — Azure Active Directory B2C
description: Klientom rejestracji i logowania za pomocą konta Microsoft w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 63aa93e72d76d430fc7e026478d91e496f27eae6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055111"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą konta Microsoft, za pomocą usługi Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Tworzenie aplikacji konta Microsoft

Aby użyć konta Microsoft jako [dostawcy tożsamości](active-directory-b2c-reference-oidc.md) w usłudze Azure Active Directory (Azure AD) B2C, musisz utworzyć aplikację w dzierżawie, który go reprezentuje. Jeśli nie masz jeszcze konta Microsoft, możesz pobrać na stronie [ https://www.live.com/ ](https://www.live.com/).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Wybierz **wszystkich usług** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz **rejestracje aplikacji**.
1. Wybierz **nowej rejestracji**
1. Wprowadź **nazwa** dla aplikacji. Na przykład *MSAapp1*.
1. W obszarze **obsługiwane typy kont**, wybierz opcję **kont w dowolnym katalogu organizacji i osobistych kont Microsoft (np. Skype, Xbox, Outlook.com)** . Ta opcja jest przeznaczona dla najszerszego zbiór tożsamości firmy Microsoft.

   Aby uzyskać więcej informacji na temat opcji typu innego konta, zobacz [Szybki Start: Rejestrowanie aplikacji z platformą tożsamości Microsoft](../active-directory/develop/quickstart-register-app.md).
1. W obszarze **identyfikator URI przekierowania (opcjonalnie)** , wybierz opcję **Web** i wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` w polu tekstowym. Zastąp `your-tenant-name` nazwą dzierżawy usługi Azure AD B2C.
1. Wybierz **zarejestrować**
1. Rekord **identyfikator aplikacji (klienta)** wyświetlane na stronie przeglądu aplikacji. Jest to potrzebne podczas konfigurowania dostawcy tożsamości w następnej sekcji.
1. Wybierz **certyfikaty i klucze tajne**
1. Kliknij przycisk **nowy wpis tajny klienta**
1. Wprowadź **opis** dla wpisu tajnego, na przykład *hasło aplikacji 1*, a następnie kliknij przycisk **Dodaj**.
1. Zapisz hasło aplikacji wyświetlone w **wartość** kolumny. Jest to potrzebne podczas konfigurowania dostawcy tożsamości w następnej sekcji.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Konfigurowanie konta Microsoft jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera Twojej dzierżawy.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
1. Podaj **nazwa**. Na przykład, wprowadź *MSA*.
1. Wybierz **typ dostawcy tożsamości**, wybierz opcję **Account Microsoft**i kliknij przycisk **OK**.
1. Wybierz **skonfiguruj tego dostawcę tożsamości** i wprowadź identyfikator aplikacji (klienta), zapisane wcześniej w **identyfikator klienta** tekst pola, a następnie wprowadź klucz tajny klienta, które są rejestrowane w **klienta klucz tajny** pola tekstowego.
1. Kliknij przycisk **OK** a następnie kliknij przycisk **Utwórz** można zapisać konfiguracji konta Microsoft.
