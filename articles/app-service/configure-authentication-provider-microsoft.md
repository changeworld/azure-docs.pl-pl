---
title: Konfigurowanie uwierzytelniania firmy Microsoft
description: Dowiedz się, jak skonfigurować uwierzytelnianie konta Microsoft jako dostawcę tożsamości dla usługi aplikacji lub aplikacji Usługi Azure Functions.
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: ea745aa00f8990a2d1232a19780fdc70e2f78996
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437961"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-microsoft-account-login"></a>Konfigurowanie usługi app service lub aplikacji Usługi Azure do korzystania z logowania konta Microsoft

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym temacie pokazano, jak skonfigurować usługę Azure App Service lub usługi Azure Functions do używania usługi AAD do obsługi osobistych identyfikatorów logowania do konta Microsoft.

> [!NOTE]
> Zarówno osobiste konta Microsoft, jak i konta organizacyjne korzystają z dostawcy tożsamości usługi AAD. W tej chwili nie jest możliwe skonfigurowanie tego dostawcy tożsamości do obsługi obu typów logowań.

## <a name="register-your-app-with-microsoft-account"></a><a name="register-microsoft-account"> </a>Rejestrowanie aplikacji za pomocą konta Microsoft

1. Przejdź do [**rejestracji aplikacji**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) w witrynie Azure portal. W razie potrzeby zaloguj się za pomocą swojego konta Microsoft.
1. Wybierz **pozycję Nowa rejestracja**, a następnie wprowadź nazwę aplikacji.
1. W obszarze **Obsługiwane typy kont**wybierz **konta w dowolnym katalogu organizacyjnym (dowolny katalog usługi Azure AD — wielodostępne) i osobiste konta Microsoft (np.**
1. W **obszarze Przekierowanie identyfikatorów URI**wybierz pozycję **Sieć Web**, a następnie wprowadź polecenie `https://<app-domain-name>/.auth/login/aad/callback`. * \<Zastąp>nazwę domeny aplikacji* nazwą domeny aplikacji.  Na przykład `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Pamiętaj, aby użyć schematu HTTPS w adresie URL.

1. Wybierz pozycję **Zarejestruj**.
1. Skopiuj **identyfikator aplikacji (klienta).** Będzie on potrzebny później.
1. W lewym okienku wybierz pozycję **Certyfikaty & wpisy tajne** > **Nowy klucz tajny klienta**. Wprowadź opis, wybierz czas ważności i wybierz pozycję **Dodaj**.
1. Skopiuj wartość wyświetlaną na stronie **Certyfikaty & wpisy tajne.** Po opuszczeniu strony nie będzie ona wyświetlana ponownie.

    > [!IMPORTANT]
    > Wartość tajnego klienta (hasło) jest ważnym poświadczeniem zabezpieczeń. Nie udostępniaj nikomu hasła ani nie rozpowszechniaj go w aplikacji klienckiej.

## <a name="add-microsoft-account-information-to-your-app-service-application"></a><a name="secrets"> </a>Dodawanie informacji o koncie Microsoft do aplikacji usługi App Service

1. Przejdź do aplikacji w [witrynie Azure portal].
1. Wybierz **pozycję Uwierzytelnianie ustawienia** > **/ autoryzacja**i upewnij się, że **uwierzytelnianie usługi aplikacji** jest **włączone**.
1. W obszarze **Dostawcy uwierzytelniania**wybierz pozycję **Azure Active Directory**. Wybierz **opcję Zaawansowane** w **trybie zarządzania**. Wklej w identyfikatorze aplikacji (klienta) i klucz tajny klienta, który został uzyskany wcześniej. Użyj **https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0** pola **Adres url wystawcy.**
1. Kliknij przycisk **OK**.

   Usługa App Service zapewnia uwierzytelnianie, ale nie ogranicza autoryzowanego dostępu do zawartości witryny i interfejsów API. Musisz autoryzować użytkowników w kodzie aplikacji.

1. (Opcjonalnie) Aby ograniczyć dostęp do użytkowników kont Microsoft, ustaw **akcję do podjęcia, gdy żądanie nie jest uwierzytelnione,** aby **zalogować się za pomocą usługi Azure Active Directory**. Po ustawieniu tej funkcji aplikacja wymaga uwierzytelnienia wszystkich żądań. Przekierowuje również wszystkie nieuwierzyte nieuwierzyte żądania do używania aad do uwierzytelniania. Należy zauważyć, że ponieważ adres **URL wystawcy** został skonfigurowany do używania dzierżawy konta Microsoft, tylko osobiste konta acccounts zostaną pomyślnie uwierzytelnione.

   > [!CAUTION]
   > Ograniczenie dostępu w ten sposób ma zastosowanie do wszystkich wywołań aplikacji, co może nie być pożądane w przypadku aplikacji, które mają publicznie dostępną stronę główną, jak w wielu aplikacjach jednostronicowych. W przypadku takich aplikacji **można zezwolić na żądania anonimowe (brak akcji),** aby aplikacja ręcznie uruchamiała uwierzytelnianie. Aby uzyskać więcej informacji, zobacz [Przepływ uwierzytelniania](overview-authentication-authorization.md#authentication-flow).

1. Wybierz **pozycję Zapisz**.

Teraz możesz używać konta Microsoft do uwierzytelniania w aplikacji.

## <a name="next-steps"></a><a name="related-content"> </a>Następne kroki

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure Portal]: https://portal.azure.com/
