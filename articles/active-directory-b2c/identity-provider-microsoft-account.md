---
title: Konfigurowanie rejestracji i logowanie się za pomocą konta Microsoft
titleSuffix: Azure AD B2C
description: Zapewnij rejestrację i zaloguj się do klientów za pomocą kont Microsoft w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 25784eb161a860398b0741d1d20375cabd1c4eca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188022"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowanie się za pomocą konta Microsoft przy użyciu usługi Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Tworzenie aplikacji konta Microsoft

Aby użyć konta Microsoft jako [dostawcy tożsamości](openid-connect.md) w usłudze Azure Active Directory B2C (Azure AD B2C), należy utworzyć aplikację w dzierżawie usługi Azure AD. Dzierżawa usługi Azure AD nie jest taka sama jak dzierżawa usługi Azure AD B2C. Jeśli nie masz jeszcze konta Microsoft, możesz je [https://www.live.com/](https://www.live.com/)uzyskać w programie .

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD, wybierając filtr **subskrypcja Katalog +** w górnym menu i wybierając katalog zawierający dzierżawę usługi Azure AD.
1. Wybierz **pozycję Wszystkie usługi** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz pozycję **Rejestracje aplikacji.**
1. Wybierz **pozycję Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *MSAapp1*.
1. W obszarze **Obsługiwane typy kont**wybierz **pozycję Konta w dowolnym katalogu organizacyjnym i osobistych kontach Microsoft (np Outlook.com.** Ta opcja jest przeznaczona dla najszerszego zestawu tożsamości firmy Microsoft.

   Aby uzyskać więcej informacji na temat różnych typów kont, zobacz [Szybki start: Rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](../active-directory/develop/quickstart-register-app.md).
1. W obszarze **Przekierowanie identyfikatora URI (opcjonalnie)** wybierz pozycję **Web** i wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` w polu tekstowym. Zamień `your-tenant-name` nazwę dzierżawy usługi Azure AD B2C.
1. Wybierz **zarejestruj się**
1. Zarejestruj **identyfikator aplikacji (klienta)** wyświetlany na stronie Przegląd aplikacji. Jest to potrzebne podczas konfigurowania dostawcy tożsamości w następnej sekcji.
1. Wybieranie **wpisów tajnych certyfikatów &**
1. Kliknij **pozycję Nowy klucz tajny klienta**
1. Wprowadź **opis klucza** tajnego, na przykład *hasło aplikacji 1*, a następnie kliknij przycisk **Dodaj**.
1. Zapisz hasło aplikacji wyświetlane w kolumnie **Wartość.** Jest to potrzebne podczas konfigurowania dostawcy tożsamości w następnej sekcji.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Konfigurowanie konta Microsoft jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C, wybierając filtr **subskrypcja katalog +** w górnym menu i wybierając katalog zawierający dzierżawę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz **pozycję Dostawcy tożsamości**, a następnie wybierz pozycję Konto **Microsoft**.
1. Wprowadź **nazwę**. Na przykład *MSA*.
1. W przypadku **identyfikatora klienta**wprowadź identyfikator aplikacji (klienta) aplikacji usługi Azure AD, który został utworzony wcześniej.
1. W przypadku **klucza tajnego klienta**wprowadź zarejestrowany klucz tajny klienta.
1. Wybierz **pozycję Zapisz**.
