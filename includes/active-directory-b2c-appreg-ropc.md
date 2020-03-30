---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 03329252c0ed4231585d1717d9361a2aef35b36f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187008"
---
Aby zarejestrować aplikację w dzierżawie usługi Azure AD B2C, można użyć **bieżącego środowiska aplikacji** lub naszego nowego środowiska rejestracji aplikacji ujednoliconej **(w wersji zapoznawczej).** [Dowiedz się więcej na temat nowego środowiska](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikacje](#tab/applications/)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz filtr **subskrypcja katalog +** w górnym menu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Możesz też wybrać **wszystkie usługi** i wyszukać i wybrać pozycję Azure **AD B2C**.
1. Wybierz **pozycję Aplikacje**, a następnie wybierz pozycję **Dodaj**.
1. Wprowadź nazwę aplikacji. Na przykład *ROPC_Auth_app*.
1. W przypadku **klienta macierzystego**wybierz pozycję **Tak**.
1. Pozostaw inne wartości w stanie, w jakim są, a następnie wybierz pozycję **Utwórz**.
1. Zapisz **identyfikator aplikacji** do użycia w późniejszym kroku.

#### <a name="app-registrations-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz filtr **subskrypcja katalog +** w górnym menu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Możesz też wybrać **wszystkie usługi** i wyszukać i wybrać pozycję Azure **AD B2C**.
1. Wybierz **pozycję Rejestracje aplikacji (Wersja zapoznawcza),** a następnie wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *ROPC_Auth_app*.
1. Pozostaw inne wartości w ich stanie, a następnie wybierz pozycję **Zarejestruj**.
1. Zarejestruj **identyfikator aplikacji (klienta)** do użycia w późniejszym kroku.
1. W obszarze **Zarządzanie**wybierz pozycję **Uwierzytelnianie**.
1. Wybierz **wypróbuj nowe środowisko** (jeśli jest to pokazane).
1. W obszarze **Domyślny typ klienta**wybierz opcję **Tak,** aby traktować aplikację jako klienta publicznego. To ustawienie jest wymagane dla przepływu ROPC.
1. Wybierz **pozycję Zapisz**.