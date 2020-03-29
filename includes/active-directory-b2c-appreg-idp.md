---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 80e5166775b0cf5acbfce32e61d91c0889e3b086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186367"
---
Aby zarejestrować aplikację w dzierżawie usługi Azure AD B2C, można użyć **bieżącego środowiska aplikacji** lub naszego nowego środowiska rejestracji aplikacji ujednoliconej **(w wersji zapoznawczej).** [Dowiedz się więcej na temat nowego środowiska](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikacje](#tab/applications/)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz filtr **subskrypcja katalog +** w górnym menu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Możesz też wybrać **wszystkie usługi** i wyszukać i wybrać pozycję Azure **AD B2C**.
1. Wybierz **pozycję Aplikacje**, a następnie wybierz pozycję **Dodaj**.
1. Wprowadź nazwę aplikacji. Na przykład *testapp1*.
1. W przypadku **aplikacji Web App / interfejsu API sieci Web**wybierz pozycję **Tak**.
1. W przypadku **adresu URL odpowiedzi**wprowadź`https://jwt.ms`
1. Wybierz **pozycję Utwórz**.

#### <a name="app-registrations-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz filtr **subskrypcja katalog +** w górnym menu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Możesz też wybrać **wszystkie usługi** i wyszukać i wybrać pozycję Azure **AD B2C**.
1. Wybierz **pozycję Rejestracje aplikacji (Wersja zapoznawcza),** a następnie wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *testapp1*.
1. Wybierz **konta w dowolnym katalogu organizacyjnym lub dowolnym dostawcy tożsamości**.
1. W obszarze **Przekierowanie identyfikatora URI**wybierz pozycję **Web**, a następnie wprowadź `https://jwt.ms` pole tekstowe adresu URL.
1. W obszarze **Uprawnienia**zaznacz pole wyboru *Udzielaj zgody administratora na otwieranie i offline_access uprawnienia.*
1. Wybierz pozycję **Zarejestruj**.

Po zakończeniu rejestracji aplikacji włącz niejawny przepływ dotacji:

1. W obszarze **Zarządzanie**wybierz pozycję **Uwierzytelnianie**.
1. Wybierz **wypróbuj nowe środowisko** (jeśli jest to pokazane).
1. W obszarze **Niejawna dotacja**wybierz pola wyboru **Tokeny dostępu** i **Tokeny Identyfikatorów.**
1. Wybierz **pozycję Zapisz**.