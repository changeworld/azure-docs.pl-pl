---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: d3a323a28d92e2a5834b65316d61c7d53a42aacf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183786"
---
Aby zarejestrować aplikację w dzierżawie usługi Azure AD B2C, można użyć **bieżącego środowiska aplikacji** lub naszego nowego środowiska rejestracji aplikacji ujednoliconej **(w wersji zapoznawczej).** [Dowiedz się więcej na temat nowego środowiska](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikacje](#tab/applications/)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz filtr **subskrypcja katalog +** w górnym menu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Możesz też wybrać **wszystkie usługi** i wyszukać i wybrać pozycję Azure **AD B2C**.
1. Wybierz **pozycję Aplikacje**, a następnie wybierz pozycję **Dodaj**.
1. Wprowadź nazwę aplikacji. Na przykład *nativeapp1*.
1. W przypadku **klienta macierzystego**wybierz pozycję **Tak**.
1. Wprowadź **identyfikator URI przekierowania niestandardowego** z unikatowym schematem. Na przykład `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Istnieją dwie ważne kwestie do rozważenia podczas wybierania identyfikatora URI przekierowania:
    * **Unikatowy**: Schemat identyfikatora URI przekierowania musi być unikatowy dla każdej aplikacji. W przykładzie `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` jest to schemat `com.onmicrosoft.contosob2c.exampleapp`. Należy przestrzegać tego wzorca. Jeśli dwie aplikacje mają ten sam schemat, użytkownik ma możliwość wyboru aplikacji. Jeśli użytkownik wybierze niepoprawnie, logowanie kończy się niepowodzeniem.
    * **Ukończono:** Identyfikator URI przekierowania musi mieć zarówno schemat, jak i ścieżkę. Ścieżka musi zawierać co najmniej jeden ukośnik po nazwie domeny. Na przykład `//oauth/` działa, gdy `//oauth` kończy się niepowodzeniem. Nie dołączaj znaków specjalnych w identyfikatorze URI, na przykład podkreślenia.
1. Wybierz **pozycję Utwórz**.

#### <a name="app-registrations-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz filtr **subskrypcja katalog +** w górnym menu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Możesz też wybrać **wszystkie usługi** i wyszukać i wybrać pozycję Azure **AD B2C**.
1. Wybierz **pozycję Rejestracje aplikacji (Wersja zapoznawcza),** a następnie wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *nativeapp1*.
1. W obszarze **Obsługiwane typy kont**wybierz pozycję **Konta w dowolnym katalogu organizacji lub dowolnym dostawcy tożsamości**.
1. W obszarze **Przekierowanie identyfikatora URI**użyj listy rozwijanej, aby wybrać **opcję Klient publiczny/natywny (mobilny & pulpit)**.
1. Wprowadź identyfikator URI przekierowania z unikatowym schematem. Na przykład `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Istnieją dwie ważne kwestie do rozważenia podczas wybierania identyfikatora URI przekierowania:
    * **Unikatowy**: Schemat identyfikatora URI przekierowania musi być unikatowy dla każdej aplikacji. W przykładzie `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` jest to schemat `com.onmicrosoft.contosob2c.exampleapp`. Należy przestrzegać tego wzorca. Jeśli dwie aplikacje mają ten sam schemat, użytkownik ma możliwość wyboru aplikacji. Jeśli użytkownik wybierze niepoprawnie, logowanie kończy się niepowodzeniem.
    * **Ukończono:** Identyfikator URI przekierowania musi mieć zarówno schemat, jak i ścieżkę. Ścieżka musi zawierać co najmniej jeden ukośnik po nazwie domeny. Na przykład `//oauth/` działa, gdy `//oauth` kończy się niepowodzeniem. Nie dołączaj znaków specjalnych w identyfikatorze URI, na przykład podkreślenia.
1. W obszarze **Uprawnienia**zaznacz pole wyboru *Udzielaj zgody administratora na otwieranie i offline_access uprawnienia.*
1. Wybierz pozycję **Zarejestruj**.