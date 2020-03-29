---
title: Konfigurowanie rejestracji i logowanie się za pomocą konta QQ przy użyciu usługi Azure Active Directory B2C
description: Zapewnij klientom rejestrację i logowanie się za pomocą kont QQ w aplikacjach przy użyciu usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b0f15124c64e5cca54112987d486ddadaca79452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187991"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowanie się za pomocą konta QQ przy użyciu usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-qq-application"></a>Tworzenie aplikacji QQ

Aby użyć konta QQ jako dostawcy tożsamości w usłudze Azure Active Directory B2C (Azure AD B2C), należy utworzyć aplikację w dzierżawie, która go reprezentuje. Jeśli nie masz jeszcze konta QQ, możesz zarejestrować się w [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Zarejestruj się w programie deweloperskim QQ

1. Zaloguj się do [portalu dewelopera QQ](http://open.qq.com) za pomocą poświadczeń konta QQ.
1. Po zalogowaniu się [https://open.qq.com/reg](https://open.qq.com/reg) przejdź do rejestracji jako deweloper.
1. Wybierz opcję 中**田 (indywidualny** deweloper).
1. Wprowadź wymagane informacje i wybierz pozycję 中**田**田 (następny krok).
1. Ukończ proces weryfikacji wiadomości e-mail. Musisz poczekać kilka dni, aby zostać zatwierdzonym po zarejestrowaniu się jako deweloper.

### <a name="register-a-qq-application"></a>Zarejestruj aplikację QQ

1. Przejdź [https://connect.qq.com/index.html](https://connect.qq.com/index.html)do .
1. Wybierz **应用管理** opcję 中田田田 (zarządzanie aplikacjami).
1. Wybierz **创建应用** opcję 中田田田 (tworzenie aplikacji) i wprowadź wymagane informacje.
1. Wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` **adres** URL wywołania zwrotnego. Na przykład, `tenant_name` jeśli jest contoso, ustaw `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`adres URL na .
1. Wybierz **创建应用** opcję 中田田田 (tworzenie aplikacji).
1. Na stronie potwierdzenia **wybierz** pozycję Zarządzanie aplikacjami , aby powrócić do strony zarządzania aplikacjami.
1. Wybierz **查看** pozycję widok obok utworzonej aplikacji.
1. Wybierz **修改** opcję 中田 (edytuj).
1. Skopiuj **identyfikator aplikacji** i **klucz aplikacji**. Potrzebujesz obu tych wartości, aby dodać dostawcę tożsamości do dzierżawy.

## <a name="configure-qq-as-an-identity-provider"></a>Konfigurowanie qq jako dostawcy tożsamości

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
1. Wybierz ikonę **Katalog + Subskrypcja** na pasku narzędzi portalu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. W witrynie Azure portal wyszukaj i wybierz **pozycję Azure AD B2C**.
1. Wybierz **dostawców tożsamości,** a następnie wybierz **QQ (Preview)**.
1. Wprowadź **nazwę**. Na przykład *QQ*.
1. W przypadku identyfikatora **klienta**wprowadź identyfikator aplikacji QQ, który został utworzony wcześniej.
1. W przypadku **klucza tajnego klienta**wprowadź klucz aplikacji, który został nagrany.
1. Wybierz **pozycję Zapisz**.
