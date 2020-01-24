---
title: Lokalizowanie & korzystanie z aplikacji w portalu Moje aplikacje — Azure AD
description: Dowiedz się, jak znaleźć Portal moje aplikacje, a następnie jak uzyskać dostęp do aplikacji opartych na chmurze w organizacji.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: c15580481c153a58122db593f678998be485b57d
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705007"
---
# <a name="locate-and-use-your-organizations-cloud-based-apps-on-the-my-apps-portal"></a>Lokalizowanie aplikacji opartych na chmurze w organizacji i korzystanie z nich w portalu My Apps

Możesz użyć swojego konta służbowego z portalem **Moje aplikacje** oparte na sieci Web, aby wyświetlać i uruchamiać wiele aplikacji opartych na chmurze w organizacji, aktualizować niektóre informacje o profilu i koncie, wyświetlać informacje o **grupach** oraz przeprowadzać **przeglądy dostępu** do aplikacji i grup. Jeśli nie masz dostępu do portalu **Moje aplikacje** , musisz skontaktować się z pomocą techniczną, aby uzyskać odpowiednie uprawnienia.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Ta zawartość jest przeznaczona dla użytkowników. Jeśli jesteś administratorem, możesz znaleźć więcej informacji na temat sposobu konfigurowania i zarządzania aplikacjami opartymi na chmurze w [dokumentacji zarządzania aplikacjami](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="supported-browsers"></a>Obsługiwane przeglądarki

Możesz przejść do portalu **My Apps** z dowolnej z następujących przeglądarek sieci Web:

- Google Chrome

- Mozilla Firefox, wersja 26,0 lub nowsza

- Microsoft Edge

- Internet Explorer, wersja 11 (ograniczona obsługa)

## <a name="download-and-install-the-my-apps-secure-sign-in-extension"></a>Pobierz i zainstaluj rozszerzenie moje aplikacje bezpieczne logowanie

Po wyświetleniu monitu Pobierz i zainstaluj rozszerzenie moje aplikacje bezpieczne logowanie. To rozszerzenie ułatwia rozpoczęcie pracy z aplikacjami w chmurze w organizacji, które wymagają użycia procesu logowania jednokrotnego. Jeśli Twoja organizacja ma już skonfigurowane Logowanie jednokrotne, rozszerzenie jest instalowane automatycznie i można pominąć tę sekcję.

To rozszerzenie ułatwia:

- Zaloguj się bezpośrednio do aplikacji ze strony logowania.

- Uruchom dowolne aplikacje przy użyciu funkcji **szybkiego wyszukiwania** .

- Zobacz ostatnie aplikacje używane w **ostatnio używanych** sekcjach.

- Użyj wewnętrznych adresów URL firmy podczas zdalnego korzystania z [serwera proxy aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

### <a name="to-download-and-install-the-extension"></a>Aby pobrać i zainstalować rozszerzenie

Pobierz i zainstaluj rozszerzenie na podstawie używanej przeglądarki:

- **Google Chrome.** W sklepie internetowym dla programu Chrome przejdź do funkcji [Moje aplikacje bezpieczne logowanie](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) , a następnie wybierz pozycję **Dodaj do przeglądarki Chrome**.

- **Mozilla Firefox** Na stronie **Dodatki** dla programu Firefox przejdź do funkcji [Moje aplikacje bezpieczne logowanie](https://addons.mozilla.org/firefox/addon/access-panel-extension/) , a następnie wybierz pozycję **Dodaj do przeglądarki Firefox**.

- **Microsoft Edge** W Microsoft Store przejdź do funkcji [Moje aplikacje bezpieczne logowanie](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) , a następnie wybierz pozycję **Pobierz**.

Ikona jest dodawana po prawej stronie paska **adresu** , co pozwala na logowanie i dostosowanie rozszerzenia.

#### <a name="to-change-your-my-apps-portal-using-the-extension"></a>Aby zmienić portal My Apps przy użyciu rozszerzenia
Możesz wybrać liczbę aplikacji, które mają być wyświetlane w **ostatnio używanej** sekcji, i zdecydować, czy zezwolić wewnętrznym adresom URL organizacji na przekierowywanie.

1. Wybierz ikonę nowe **Moje aplikacje bezpieczne logowanie** ![ikona rozszerzenia](media/my-apps-portal/my-apps-portal-extension-icon.png) z prawej strony paska **adresu** , a następnie wybierz pozycję **Zaloguj się, aby rozpocząć**.

2. Kliknij prawym przyciskiem myszy ikonę **ustawienia** ![ikona Ustawienia](media/my-apps-portal/my-apps-portal-extension-settings-icon.png), a następnie wybierz pozycję **Ustawienia**.

3. W polu **Ustawienia** wybierz liczbę najnowszych aplikacji, które mają być widoczne w portalu, i zdecyduj, czy wewnętrzne adresy URL organizacji mają być przekierowywane, aby można było z nich korzystać zdalnie.

    ![Strona ustawień rozszerzenia, pokazując dostępne dostosowania](media/my-apps-portal/my-apps-portal-extension-settings-page.png)

## <a name="access-and-use-the-my-apps-portal-by-device"></a>Dostęp do portalu Moje aplikacje i korzystanie z niego według urządzenia
Możesz uzyskiwać dostęp do portalu Moje aplikacje i korzystać z niego na swoim komputerze, z poziomu przeglądarki zarządzanej przez usługę Intune lub z urządzenia przenośnego z systemem iOS lub Android.

![Strona aplikacje w portalu Moje aplikacje](media/my-apps-portal/my-apps-portal-apps-page.png)

### <a name="access-and-use-the-my-apps-portal-on-your-computer"></a>Dostęp do portalu Moje aplikacje i korzystanie z niego na komputerze
Jeśli masz uprawnienia do uzyskiwania dostępu do aplikacji opartych na chmurze w organizacji i korzystania z nich, możesz uzyskać do nich dostęp za pośrednictwem portalu **My Apps** .

1. Zaloguj się do konta służbowego.

2. Otwórz przeglądarkę internetową i przejdź do https://myapps.microsoft.com lub Użyj linku dostarczonego przez organizację. Na przykład może nastąpić przekierowanie do dostosowanej strony organizacji, takiej jak https://myapps.microsoft.com/contoso.com.

    Zostanie wyświetlona strona **aplikacje** przedstawiająca wszystkie aplikacje oparte na chmurze należące do organizacji i dostępne do użycia.

3. Na stronie **aplikacje** wybierz aplikację, której chcesz używać.

    Zostanie otwarta nowa strona dla aplikacji, w której możesz zalogować się (w razie potrzeby) lub zacząć korzystać z aplikacji.

### <a name="access-and-use-the-my-apps-portal-on-an-intune-managed-browser"></a>Dostęp do portalu My Apps i korzystanie z niego w programie Intune Managed Browser

Wyświetlaj aplikacje organizacji i używaj ich z Intune Managed Browser na urządzeniach z systemem iOS lub Android.

1. Na urządzeniu przenośnym Pobierz i zainstaluj aplikację Intune Managed Browser ze [sklepu Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) i [sklep Google Play](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser).

2. Otwórz aplikację Intune Managed Browser, przejdź do https://myapps.microsoft.com lub Użyj linku dostarczonego przez organizację. Na przykład może nastąpić przekierowanie do dostosowanej strony organizacji, takiej jak https://myapps.microsoft.com/contoso.com.

    Zostanie wyświetlona strona **aplikacje** przedstawiająca wszystkie aplikacje oparte na chmurze należące do organizacji i dostępne do użycia.

3. Na stronie **aplikacje** wybierz aplikację, której chcesz używać.

    Zostanie otwarta nowa strona dla aplikacji, w której możesz zalogować się (w razie potrzeby) lub zacząć korzystać z aplikacji.

### <a name="access-and-use-the-my-apps-portal-on-an-ios-device"></a>Dostęp do portalu Moje aplikacje i korzystanie z niego na urządzeniu z systemem iOS

Wyświetl Portal **Moje aplikacje** i korzystaj z niego z urządzenia iPhone lub iPad z systemem iOS w wersji 7 lub nowszej. Możesz także zainstalować [aplikację mobilną moje aplikacje](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8) , aby uzyskiwać dostęp do aplikacji organizacji na urządzeniach z systemem iOS. 

1. Na urządzeniu przenośnym Uruchom aplikację przeglądarki sieci Web, taką jak Safari.

2. Przejdź do https://myapps.microsoft.com lub Użyj linku dostarczonego przez organizację. Na przykład może nastąpić przekierowanie do dostosowanej strony organizacji, takiej jak https://myapps.microsoft.com/contoso.com.

    Zostanie wyświetlona strona **aplikacje** przedstawiająca wszystkie aplikacje oparte na chmurze należące do organizacji i dostępne do użycia.

3. Na stronie **aplikacje** wybierz aplikację, której chcesz używać.

    Zostanie otwarta nowa strona dla aplikacji, w której możesz zalogować się (w razie potrzeby) lub zacząć korzystać z aplikacji.

### <a name="access-and-use-the-my-apps-portal-on-an-android-device"></a>Dostęp do portalu Moje aplikacje i korzystanie z niego na urządzeniu z systemem Android

Wyświetl Portal **Moje aplikacje** i korzystaj z niego na urządzeniu z systemem Android.

1. Na urządzeniu przenośnym Uruchom aplikację przeglądarki sieci Web, np. Google Chrome.

2. Przejdź do https://myapps.microsoft.com lub Użyj linku dostarczonego przez organizację. Na przykład może nastąpić przekierowanie do dostosowanej strony organizacji, takiej jak https://myapps.microsoft.com/contoso.com.

    Zostanie wyświetlona strona **aplikacje** przedstawiająca wszystkie aplikacje oparte na chmurze należące do organizacji i dostępne do użycia.

3. Na stronie **aplikacje** wybierz aplikację, której chcesz używać.

    Zostanie otwarta nowa strona dla aplikacji, w której możesz zalogować się (w razie potrzeby) lub zacząć korzystać z aplikacji.

## <a name="add-a-new-app-to-the-my-apps-portal"></a>Dodawanie nowej aplikacji do portalu My Apps

Jeśli administrator udzielił Ci uprawnień, możesz dodać nową aplikację do strony **aplikacje** .

1. Na stronie **aplikacje** wybierz pozycję **Dodaj aplikację**.

    Zostanie wyświetlona strona **Dodawanie aplikacji** .

    ![Strona Dodawanie aplikacji w portalu Moje aplikacje](media/my-apps-portal/my-apps-portal-add-apps-page.png)

2. Wybierz aplikację, którą chcesz dodać z podanej listy, a następnie wybierz pozycję **Dodaj**.

3. Aplikacja zostanie dodana do listy na stronie **aplikacje** .

    Niektóre aplikacje mogą wymagać zatwierdzenia przez administratora przed dodaniem. W takiej sytuacji aplikacja nie została dodana do strony **aplikacje** do momentu zatwierdzenia jej przez administratora.

## <a name="next-steps"></a>Następne kroki

Po przejściu do strony **aplikacje** możesz:

- [Zmienianie informacji o profilu](my-apps-portal-end-user-update-profile.md)

- [Wyświetlanie i aktualizowanie informacji związanych z grupami](my-apps-portal-end-user-groups.md)

- [Wykonywanie własnych przeglądów dostępu](my-apps-portal-end-user-access-reviews.md)
