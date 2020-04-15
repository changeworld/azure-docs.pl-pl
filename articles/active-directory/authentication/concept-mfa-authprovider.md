---
title: Dostawcy auth azure wielu czynników — usługa Azure Active Directory
description: Kiedy należy używać dostawcy Auth z usługi Azure MFA?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf50a8f58978a010fe3d8228ace8579fcf52eb38
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309900"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Kiedy używać dostawcy uwierzytelniania wieloskładnikowego platformy Azure

> [!IMPORTANT]
> Od 1 września 2018 r. nowi dostawcy er mogą przestać być tworzoni. Istniejących dostawców eru może być nadal używany i aktualizowany, ale migracja nie jest już możliwe. Uwierzytelnianie wieloskładnikowe będzie nadal dostępne jako funkcja w licencjach usługi Azure AD Premium.

Weryfikacja dwuetapowa jest domyślnie dostępna dla administratorów globalnych, którzy zarządzają użytkownikami usług Azure Active Directory i Office 365. Jeśli jednak chcesz korzystać z [funkcji zaawansowanych](howto-mfa-mfasettings.md), musisz kupić pełną wersję usługi Azure Multi-Factor Authentication (MFA).

Dostawca usługi Azure Multi-Factor Auth jest używany do korzystania z funkcji oferowanych przez uwierzytelnianie wieloskładnikowe platformy Azure dla użytkowników, którzy **nie mają licencji.**

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Zastrzeżenia związane z zestawem SDK usługi Azure MFA

Należy zauważyć, że SDK został przestarzały i będzie działać tylko do 14 listopada 2018. Po tym czasie wywołania do zestawu SDK będą kończyć się niepowodzeniem.

## <a name="what-is-an-mfa-provider"></a>Co to jest dostawca usługi MFA?

Istnieją dwa typy dostawców Auth, a rozróżnienie dotyczy sposobu naliczania opłat za subskrypcję platformy Azure. W modelu opartym na uwierzytelnianiu jest uwzględniana liczba uwierzytelnień w dzierżawie w ciągu miesiąca. Ta opcja jest przydatna, jeśli masz pewną liczbę użytkowników, którzy uwierzytelniają się tylko czasami. W modelu opartym na użytkownikach jest uwzględniana liczba osób w dzierżawie, które w danym miesiącu korzystają z weryfikacji dwuetapowej. Ta opcja jest przydatna, jeśli masz użytkowników z licencjami, ale musisz objąć uwierzytelnianiem wieloskładnikowym dodatkowych użytkowników, którzy nie mają licencji.

## <a name="manage-your-mfa-provider"></a>Zarządzanie dostawcą usługi MFA

Po utworzeniu dostawcy usługi MFA nie możesz zmienić modelu użycia (na włączonego użytkownika lub na uwierzytelnienie).

Jeśli zakupiono wystarczającą liczbę licencji, aby objąć wszystkich użytkowników, którzy są włączeni dla usługi MFA, można całkowicie usunąć dostawcę usługi MFA.

Jeśli dostawca usługi MFA nie jest połączony z dzierżawą usługi Azure AD lub łączysz nowego dostawcę usługi MFA z inną dzierżawą usługi Azure AD, ustawienia użytkownika i opcje konfiguracji nie są przenoszone. Ponadto istniejące serwery usługi Azure MFA muszą zostać ponownie aktywowane przy użyciu poświadczeń aktywacji generowanych za pośrednictwem dostawcy usługi MFA.

### <a name="removing-an-authentication-provider"></a>Usuwanie dostawcy uwierzytelniania

> [!CAUTION]
> Nie ma potwierdzenia podczas usuwania dostawcy uwierzytelniania. Wybranie **opcji Usuń** jest procesem trwałym.

Dostawców uwierzytelniania można znaleźć w **witrynie Azure portal** > Dostawców**Providers**zabezpieczeń usługi Azure Active >  > **Security** > **MFA****Directory.** Kliknij wymienionych dostawców, aby wyświetlić szczegóły i konfiguracje skojarzone z tym dostawcą.

Przed usunięciem dostawcy uwierzytelniania należy zapoznać się z niestandardowymi ustawieniami skonfigurowanym w dostawcy. Zdecyduj, jakie ustawienia należy przeprowadzić do ogólnych ustawień usługi MFA od dostawcy i zakończ migrację tych ustawień. 

Serwery usługi Azure MFA połączone z dostawcami będą musiały zostać ponownie aktywowane przy użyciu poświadczeń wygenerowanych w**ustawieniach serwera****zabezpieczeń usługi** >  **Azure** > **Active Directory** > **usługi Azure Security MFA** > . Przed ponowną aktywacją następujące pliki muszą `\Program Files\Multi-Factor Authentication Server\Data\` zostać usunięte z katalogu na serwerach usługi Azure MFA w twoim środowisku:

- caCert ( caCert )
- cert
- grupaCACert
- klawisz groupKey
- Groupname
- licenseKey (Klucz)
- Pkey

![Usuwanie dostawcy eru z witryny Azure portal](./media/concept-mfa-authprovider/authentication-provider-removal.png)

Po potwierdzeniu, że wszystkie ustawienia zostały zmigrowane, możesz przejść do **witryny Azure portal** > **Azure Active Directory** > **Security MFA** > **MFA** > **Providers** i wybrać elipsy **...** i wybrać pozycję **Usuń**. .

> [!WARNING]
> Usunięcie dostawcy uwierzytelniania spowoduje usunięcie wszelkich informacji raportowania skojarzonych z tym dostawcą. Przed usunięciem dostawcy można zapisać raporty aktywności.

> [!NOTE]
> Użytkownicy ze starszymi wersjami aplikacji Microsoft Authenticator i usługi Azure MFA Server mogą wymagać ponownej rejestracji aplikacji.

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie ustawień usługi Multi-Factor Authentication](howto-mfa-mfasettings.md)
