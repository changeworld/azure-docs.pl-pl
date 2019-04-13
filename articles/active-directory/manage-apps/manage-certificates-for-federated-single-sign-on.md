---
title: Zarządzanie certyfikatami federacji w usłudze Azure AD | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dostosować datę wygaśnięcia certyfikatów Federacji i odnawianie certyfikatów, które wkrótce wygasną.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: celested
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64c2d14a2aa6fc6b53260912b5bead2bd7c01e8d
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547848"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Zarządzanie certyfikatami federacyjnego logowania jednokrotnego w usłudze Azure Active Directory

W tym artykule omówione typowe pytania i informacje związane z certyfikatami, że usługi Azure Active Directory (Azure AD) tworzy nawiązać federacyjnego logowania jednokrotnego (SSO) z oprogramowaniem jako aplikacji usługi (SaaS). Dodawanie aplikacji z galerii aplikacji Azure AD lub przy użyciu szablonu aplikacji spoza galerii. Konfigurowanie aplikacji przy użyciu opcji federacyjnego logowania jednokrotnego.

Ten artykuł ma zastosowanie tylko do aplikacji, które są skonfigurowane do używania logowania jednokrotnego usługi Azure AD za pomocą [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) federacyjnych (SAML).

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Automatycznie wygenerowany certyfikat dla aplikacji i innych galerii

Podczas dodawania nowej aplikacji z galerii i skonfigurować opartej na SAML logowania jednokrotnego (wybierając **logowanie jednokrotne** > **SAML** ze strony Przegląd aplikacji), Azure AD generuje certyfikat dla aplikacji, który jest ważny przez trzy lata. Aby pobrać aktywnego certyfikatu jako certyfikatu zabezpieczeń (**cer**) plik, wróć do tej strony (**opartej na SAML logowania jednokrotnego**) i wybierz link pobierania w **certyfikat podpisywania SAML** nagłówka. Możesz wybrać między pierwotne certyfikatu (plik binarny) lub certyfikat (Base64 tekstu podstawowego) Base64. W przypadku aplikacji z galerii w tej sekcji również są pokazywane link umożliwiający pobranie certyfikatu jako kod XML metadanych Federacji ( **.xml** pliku), w zależności od wymagań aplikacji.

![SAML active podpisywania certyfikatu opcje pobierania](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

Możesz również pobrać certyfikat aktywną lub nieaktywną, wybierając **certyfikat podpisywania SAML** nagłówka **Edytuj** ikonę (ołówka), która wyświetla **certyfikatpodpisywaniaSAML** strony. Wybierz przycisk wielokropka (**...** ) obok certyfikatu, aby pobrać, a następnie wybierz format certyfikatu, który chcesz. Masz dodatkową opcję Pobierz certyfikat w formacie poczty z rozszerzoną ochroną prywatności (PEM). Ten format jest taka sama jak Base64, ale z **PEM** rozszerzenie, które nie są rozpoznawane w Windows w formacie certyfikatu.

![Opcje pobierania certyfikatu (aktywnych i nieaktywnych) podpisywania protokołu SAML](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Dostosowywanie datę wygaśnięcia certyfikatu usługi federacyjnej i przechodzą do nowego certyfikatu

Domyślnie platforma Azure konfiguruje certyfikat wygaśnie po upływie trzech lat, gdy jest tworzony automatycznie podczas SAML jednej konfiguracji logowania jednokrotnego. Nie można zmienić datę certyfikatu po jego zapisaniu, należy:

1. Utwórz nowy certyfikat z odpowiednią datą.
2. Zapisz nowy certyfikat.
3. Pobierz nowy certyfikat w poprawnym formacie.
4. Przekaż nowy certyfikat do aplikacji.
5. Ustaw nowy certyfikat jako aktywny w portalu Azure Active Directory.

Poniższych sekcjach ułatwiają wykonaj następujące kroki.

### <a name="create-a-new-certificate"></a>Utwórz nowy certyfikat

Najpierw utwórz i Zapisz nowy certyfikat za pomocą różnych wygaśnięcia:

1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com/). **Centrum administracyjne usługi Azure Active Directory** zostanie wyświetlona strona.

2. W lewym okienku wybierz pozycję **Aplikacje dla przedsiębiorstw**. Zostanie wyświetlona lista aplikacji przeznaczonych dla przedsiębiorstw na Twoim koncie.

3. Wybierz aplikację, których to dotyczy. Zostanie wyświetlona strona Omówienie aplikacji.

4. W okienku po lewej stronie strony przeglądu aplikacji wybierz **logowanie jednokrotne**.

5. Jeśli **wybierz jedną metodę logowania jednokrotnego** zostanie wyświetlona strona wybierz **SAML**.

6. W **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML - Preview** strony, Znajdź **certyfikat podpisywania SAML** nagłówek i wybierz **Edytuj** ikonę (ołówka). **Certyfikat podpisywania SAML** zostanie wyświetlona strona, która wyświetla stan (**Active** lub **nieaktywny**), datę wygaśnięcia i odcisk palca (ciąg wyznaczania wartości skrótu) każdego certyfikatu.

7. Wybierz **nowy certyfikat**. Pojawi się nowy wiersz poniżej listy certyfikatów, których data wygaśnięcia wartość domyślna to dokładnie trzech lat po dacie bieżącej. (Zmiany nie zostały jeszcze zapisane, dzięki czemu mogą modyfikować datę wygaśnięcia.)

8. W nowym wierszu certyfikatu, umieść kursor nad kolumny daty wygaśnięcia, a następnie wybierz pozycję **wybierz datę** ikonę (Kalendarz). Formant kalendarza zostanie wyświetlone, dni miesiąca do bieżącej daty wygaśnięcia nowego wiersza.

9. Aby ustawić nową datę przy użyciu kontrolki kalendarza. Możesz ustawić dowolna data od bieżącej daty i trzech lat po dacie bieżącej.

10. Wybierz pozycję **Zapisz**. Pojawi się nowy certyfikat ze stanem **nieaktywny**, po upływie daty, wybierz opcję i odcisku palca.

11. Wybierz **X** aby powrócić do **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML - Preview** strony.

### <a name="upload-and-activate-a-certificate"></a>Przekazywanie i aktywować certyfikatu

Następnie Pobierz nowy certyfikat w prawidłowym formacie, przekaż go do aplikacji i uaktywnić w usłudze Azure Active Directory:

1. Aby wyświetlić dodatkowe instrukcje konfiguracji logowania jednokrotnego SAML aplikacji albo:
   - Wybieranie **Przewodnik po konfiguracji** link, aby wyświetlić w osobnym oknie przeglądarki lub na karcie, lub
   - przechodząc do **Konfigurowanie** nagłówka i wybierając polecenie **wyświetlić instrukcje krok po kroku** do wyświetlania na pasku bocznym.

2. W instrukcjach należy pamiętać, formacie kodowania, wymagane w celu przekazania certyfikatu.

3. Postępuj zgodnie z instrukcjami w [automatycznie wygenerowany certyfikat dla aplikacji i innych galerii](#auto-generated-certificate-for-gallery-and-non-gallery-applications) wcześniejszej sekcji. Ten krok spowoduje pobranie certyfikatu w formacie kodowania, wymagane do przekazania przez aplikację.

4. Gdy użytkownik chce przechodzą do nowego certyfikatu, wróć do **certyfikat podpisywania SAML** strony, a w wierszu nowo zapisany certyfikat, wybierz przycisk wielokropka (**...** ) i wybierz **uaktywnić certyfikat**. Stan nowego certyfikatu zmieni się na **Active**, a wcześniej aktywnego certyfikatu zmienia stan **nieaktywny**.

5. Kontynuuj poniższe instrukcje konfiguracji logowania jednokrotnego SAML aplikacji, które wcześniej wyświetlane tak, aby przekazać podpisywania SAML certyfikatów poprawny format kodowania.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>Dodaj adresy powiadomień e-mail do wygaśnięcia certyfikatu

Usługa Azure AD będzie wysyłać wiadomość e-mail powiadomienie 60, 30 i 7 dni przed wygaśnięciem certyfikatu SAML. Możesz dodać więcej niż jeden adres e-mail, aby otrzymywać powiadomienia. Aby określić adresy e-mail, chcesz, aby powiadomienia mają być wysyłane do:

1. W **certyfikat podpisywania SAML** strony, przejdź do **adresy e-mail powiadomień** nagłówka. Domyślnie ten nagłówek używa tylko adres e-mail administratora, który dodaje aplikację.

2. Poniżej adres e-mail końcowego wpisz adres e-mail, który powinien otrzymywać powiadomienie o wygaśnięciu certyfikatu, a następnie naciśnij klawisz Enter.

3. Powtórz poprzedni krok dla każdego z adresów e-mail, który chcesz dodać.

4. Dla każdego z adresów e-mail do usunięcia, wybierz **Usuń** ikonę (Kosza) obok adres e-mail.

5. Wybierz pozycję **Zapisz**.

Otrzymasz powiadomienie e-mail z aadnotification@microsoft.com. Aby uniknąć poczty e-mail, przechodząc do lokalizacji spamu, Dodaj tę wiadomość e-mail do Twoich kontaktów.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Odnów certyfikat, który wkrótce wygaśnie

Jeśli certyfikat wygaśnie, można odnowić go za pomocą procedury, która powoduje nie znaczących przestojów dla użytkowników. Aby odnowić Wygasający certyfikat:

1. Postępuj zgodnie z instrukcjami w [Utwórz nowy certyfikat](#create-a-new-certificate) sekcji wcześniej, za pomocą daty, która pokrywa się z istniejącym certyfikatem. Tą datą ogranicza przestoje spowodowane wygaśnięcia certyfikatu.

2. Jeśli aplikacji można automatycznego przerzucania certyfikatu, ustawić nowy certyfikat na aktywny, wykonaj następujące czynności:
   1. Wróć do **certyfikat podpisywania SAML** strony.
   2. W wierszu nowo zapisany certyfikat, wybierz przycisk wielokropka (**...** ), a następnie wybierz **uaktywnić certyfikat**.
   3. Pomiń dwa następne kroki.

3. Jeśli aplikacja może obsługiwać tylko jeden certyfikat naraz, wybierz przedział czasu przestojów do wykonania kolejnego kroku. (W przeciwnym razie, jeśli aplikacja nie automatyczne pobranie nowego certyfikatu, ale może obsługiwać więcej niż jeden certyfikat podpisywania, można wykonać następny krok dowolnym.)

4. Przed upływem starego certyfikatu, postępuj zgodnie z instrukcjami [przekazywania i aktywowania certyfikatu](#upload-and-activate-a-certificate) wcześniejszej sekcji.

5. Zaloguj się do aplikacji, aby upewnić się, że certyfikat działa prawidłowo.

## <a name="related-articles"></a>Pokrewne artykuły:

* [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](../saas-apps/tutorial-list.md)
* [Zarządzanie aplikacjami w usłudze Azure Active Directory](what-is-application-management.md)
* [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](what-is-single-sign-on.md)
* [Debugowanie opartej na SAML logowania jednokrotnego do aplikacji w usłudze Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md)
