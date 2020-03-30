---
title: Konfigurowanie rejestracji i logowania się za pomocą konta LinkedIn
titleSuffix: Azure AD B2C
description: Zapewnij rejestrację i zaloguj się do klientów za pomocą kont LinkedIn w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d32eb80ffe296d86164a6d27a1b7a28181357243
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188104"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowanie się za pomocą konta LinkedIn przy użyciu usługi Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Tworzenie aplikacji LinkedIn

Aby użyć konta LinkedIn jako [dostawcy tożsamości](authorization-code-flow.md) w usłudze Azure Active Directory B2C (Azure AD B2C), należy utworzyć aplikację w dzierżawie, która go reprezentuje. Jeśli nie masz jeszcze konta LinkedIn, możesz zarejestrować się w [https://www.linkedin.com/](https://www.linkedin.com/)pliku .

1. Zaloguj się w [witrynie LinkedIn Developers](https://www.developer.linkedin.com/) za pomocą poświadczeń konta LinkedIn.
1. Wybierz **pozycję Moje aplikacje**, a następnie kliknij pozycję **Utwórz aplikację**.
1. Wprowadź **nazwę firmy,** **nazwę aplikacji,** opis **aplikacji,** **logo aplikacji,** **użycie aplikacji,** **adres URL witryny sieci Web,** **firmową pośpęć**i **telefon służbowy.**
1. Zaakceptuj **Warunki użytkowania interfejsu API LinkedIn** i kliknij przycisk **Prześlij**.
1. Skopiuj wartości identyfikatora **klienta** i **klucza tajnego klienta**. Można je znaleźć w obszarze **Klucze uwierzytelniania**. Oba będą potrzebne do skonfigurowania usługi LinkedIn jako dostawcy tożsamości w dzierżawie. **Klucz tajny klienta** jest ważnym poświadczeniem zabezpieczeń.
1. Wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` **adresy URL autoryzowanego przekierowania**. Zamień `your-tenant-name` na nazwę dzierżawy. Należy użyć wszystkich małych liter podczas wprowadzania nazwy dzierżawy, nawet jeśli dzierżawca jest zdefiniowany za pomocą wielkich liter w usłudze Azure AD B2C. Wybierz pozycję **Dodaj**, a następnie kliknij pozycję **Aktualizuj**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Konfigurowanie konta LinkedIn jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C, wybierając filtr **subskrypcja katalog +** w górnym menu i wybierając katalog zawierający dzierżawę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz **pozycję Dostawcy tożsamości**, a następnie wybierz pozycję **LinkedIn**.
1. Wprowadź **nazwę**. Na przykład *LinkedIn*.
1. W przypadku **identyfikatora klienta**wprowadź identyfikator klienta aplikacji LinkedIn, który został utworzony wcześniej.
1. W przypadku **klucza tajnego Klienta**wprowadź zarejestrowany klucz tajny klienta.
1. Wybierz **pozycję Zapisz**.

## <a name="migration-from-v10-to-v20"></a>Migracja z wersji 1.0 do wersji 2.0

LinkedIn niedawno [zaktualizował swoje interfejsy API z wersji 1.0 do wersji 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). W ramach migracji usługa Azure AD B2C może uzyskać pełną nazwę użytkownika linkedin podczas rejestracji. Jeśli adres e-mail jest jednym z atrybutów zebranych podczas rejestracji, użytkownik musi ręcznie wprowadzić adres e-mail i zweryfikować go.
