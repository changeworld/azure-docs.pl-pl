---
title: Zarządzanie certyfikatami federacyjnymi w usłudze Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak dostosować datę wygaśnięcia certyfikatów federacyjnych i jak odnowić certyfikaty, które wkrótce wygasną.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mimart
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de60dc5095ce4ab4d0219a388c445b08f544e1f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159033"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Zarządzanie certyfikatami dla federacyjnego logowania jednokrotnego w usłudze Azure Active Directory

W tym artykule omówimy typowe pytania i informacje związane z certyfikatami, które usługa Azure Active Directory (Azure AD) tworzy w celu ustanowienia federacyjnego logowania jednokrotnego (SSO) do aplikacji oprogramowania jako usługi (SaaS). Dodawanie aplikacji z galerii aplikacji usługi Azure AD lub przy użyciu szablonu aplikacji spoza galerii. Skonfiguruj aplikację przy użyciu opcji SSO federacyjnej.

Ten artykuł dotyczy tylko aplikacji, które są skonfigurowane do używania usługi Azure AD SSO za pośrednictwem federacji języka SAML [(Security Assertion Markup Language).](https://wikipedia.org/wiki/Security_Assertion_Markup_Language)

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Automatycznie wygenerowany certyfikat dla aplikacji galerii i aplikacji innych niż galeria

Po dodaniu nowej aplikacji z galerii i skonfigurowaniu logowania opartego na SAML (przez wybranie **jednokrotnego** > logowania**SAML** ze strony przeglądu aplikacji), usługa Azure AD generuje certyfikat dla aplikacji, która jest prawidłowa przez trzy lata. Aby pobrać aktywny certyfikat jako plik certyfikatu zabezpieczeń (**cer**), wróć do tej strony (**logowanie oparte na SAML**) i wybierz łącze pobierania w nagłówku **Saml Podpisywanie certyfikatu.** Można wybrać między certyfikatem nieprzetworzonym (binarnym) lub certyfikatem Base64 (podstawowy tekst 64 zakodowany). W przypadku aplikacji galerii w tej sekcji może również zostać wyświetlone łącze do pobrania certyfikatu jako metadanych federacji XML (pliku **xml),** w zależności od wymagań aplikacji.

![Opcje pobierania certyfikatu aktywnego podpisywania SAML](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

Aktywny lub nieaktywny certyfikat można również pobrać, wybierając ikonę **Edytuj** nagłówek **saml podpisywania certyfikatu** (ołówek), która wyświetla stronę **Certyfikat podpisywania SAML.** Wybierz wielokropek (**...**) obok certyfikatu, który chcesz pobrać, a następnie wybierz odpowiedni format certyfikatu. Masz dodatkową opcję pobrania certyfikatu w formacie pem (privacy-enhanced mail). Ten format jest identyczny z Base64, ale z rozszerzeniem nazwy pliku **pem,** który nie jest rozpoznawany w systemie Windows jako format certyfikatu.

![Opcje pobierania certyfikatu podpisywania SAML (aktywne i nieaktywne)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Dostosowywanie daty wygaśnięcia certyfikatu federacyjnego i przerzucenie go na nowy certyfikat

Domyślnie platforma Azure konfiguruje certyfikat do wygaśnięcia po trzech latach, gdy jest tworzony automatycznie podczas konfiguracji logowania jednokrotnego SAML. Ponieważ nie można zmienić daty certyfikatu po jego zapisaniu, musisz:

1. Utwórz nowy certyfikat z żądaną datą.
1. Zapisz nowy certyfikat.
1. Pobierz nowy certyfikat w poprawnym formacie.
1. Przekaż nowy certyfikat do aplikacji.
1. Uatywn nowy certyfikat w portalu usługi Azure Active Directory.

Poniższe dwie sekcje ułatwiają wykonanie tych kroków.

### <a name="create-a-new-certificate"></a>Tworzenie nowego certyfikatu

Najpierw utwórz i zapisz nowy certyfikat z inną datą wygaśnięcia:

1. Zaloguj się do [portalu usługi Azure Active Directory](https://aad.portal.azure.com/). Zostanie wyświetlona strona **centrum administracyjnego usługi Azure Active Directory.**
1. W lewym okienku wybierz pozycję **Aplikacje dla przedsiębiorstw**. Pojawi się lista aplikacji dla przedsiębiorstw na twoim koncie.
1. Wybierz aplikację, na która dotyczy problem. Zostanie wyświetlona strona przeglądu aplikacji.
1. W lewym okienku strony przeglądu aplikacji wybierz pozycję **Logowanie jednokrotne**.
1. Jeśli zostanie wyświetlona strona **Wybierz metodę logowania jednokrotnego,** wybierz opcję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml — podgląd** znajdź nagłówek **Certyfikat podpisywania SAML** i wybierz ikonę **Edytuj** (ołówek). Zostanie wyświetlona strona **Certyfikat podpisywania SAML,** na której wyświetlany jest stan **(Aktywny** lub **Nieaktywny),** data wygaśnięcia i odcisk palca (ciąg mieszania) każdego certyfikatu.
1. Wybierz **nowy certyfikat**. Pod listą certyfikatów pojawi się nowy wiersz, w którym data wygaśnięcia jest domyślnie wyświetlana dokładnie trzy lata po bieżącej dacie. (Zmiany nie zostały jeszcze zapisane, więc nadal można zmodyfikować datę wygaśnięcia).
1. W nowym wierszu certyfikatu umieść wskaźnik myszy na kolumnie daty wygaśnięcia i wybierz ikonę **Wybierz datę** (kalendarz). Pojawi się formant kalendarza, wyświetlający dni miesiąca bieżącej daty wygaśnięcia nowego wiersza.
1. Użyj kontrolki kalendarza, aby ustawić nową datę. Można ustawić dowolną datę między datą bieżącą a trzema latami po bieżącej dacie.
1. Wybierz **pozycję Zapisz**. Nowy certyfikat jest teraz wyświetlany ze stanem **Nieaktywny**, wybraną datą wygaśnięcia i odciskiem palca.
1. Wybierz **znak X,** aby powrócić do strony **Konfigurowanie logowania jednokrotnego za pomocą strony SAML — Podgląd.**

### <a name="upload-and-activate-a-certificate"></a>Przekazywanie i aktywowanie certyfikatu

Następnie pobierz nowy certyfikat w poprawnym formacie, przekaż go do aplikacji i uaktywnij go w usłudze Azure Active Directory:

1. Wyświetl dodatkowe instrukcje konfiguracji logowania SAML aplikacji przez:

   - Wybieranie łącza **prowadnicy konfiguracji** do wyświetlenia w osobnym oknie lub karcie przeglądarki lub
   - Przechodzenie do nagłówka **konfiguracji** i wybieranie **opcji Wyświetl instrukcje krok po kroku** do wyświetlenia na pasku bocznym.

1. W instrukcjach zanotuj format kodowania wymagany do przekazania certyfikatu.
1. Postępuj zgodnie z instrukcjami w [automatycznie wygenerowanym certyfikacie dla galerii i aplikacji innych](#auto-generated-certificate-for-gallery-and-non-gallery-applications) niż galeria sekcji wcześniej. Ten krok pobiera certyfikat w formacie kodowania wymaganym do przekazania przez aplikację.
1. Aby przerzucić go na nowy certyfikat, wróć do strony **Saml Podpisywanie certyfikatu,** a w nowo zapisanym wierszu certyfikatu wybierz wielokropek (**...**) i wybierz pozycję **Uatraktuuj certyfikat**. Stan nowego certyfikatu zmienia się na **Aktywny,** a wcześniej aktywny certyfikat zmienia się na stan **Nieaktywny**.
1. Kontynuuj przestrzeganie instrukcji konfiguracji logowania SAML aplikacji, które zostały wyświetlone wcześniej, dzięki czemu można przekazać certyfikat podpisywania SAML w prawidłowym formacie kodowania.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>Dodawanie adresów powiadomień e-mail w celu wygaśnięcia certyfikatu

Usługa Azure AD wyśle powiadomienie e-mail 60, 30 i 7 dni przed wygaśnięciem certyfikatu SAML. Możesz dodać więcej niż jeden adres e-mail, aby otrzymywać powiadomienia. Aby określić adres(y) e-mail, powiadomienia mają być wysyłane na adres:

1. Na stronie **Certyfikat podpisywania SAML** przejdź do nagłówka **adresów e-mail powiadomień.** Domyślnie ten nagłówek używa tylko adresu e-mail administratora, który dodał aplikację.
1. Poniżej ostatecznego adresu e-mail wpisz adres e-mail, który powinien otrzymać powiadomienie o wygaśnięciu certyfikatu, a następnie naciśnij klawisz Enter.
1. Powtórz poprzedni krok dla każdego adresu e-mail, który chcesz dodać.
1. Dla każdego adresu e-mail, który chcesz usunąć, wybierz ikonę **Usuń** (pojemnik na śmieci) obok adresu e-mail.
1. Wybierz **pozycję Zapisz**.

Otrzymasz wiadomość e-mail z powiadomieniem od aadnotification@microsoft.com. Aby uniknąć sytuacji, w których wiadomość e-mail trafia do twojej lokalizacji spamu, dodaj tę wiadomość e-mail do kontaktów.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Odnawianie certyfikatu, który wkrótce wygaśnie

Jeśli certyfikat wkrótce wygaśnie, można go odnowić przy użyciu procedury, która powoduje brak znaczących przestojów dla użytkowników. Aby odnowić wygasający certyfikat:

1. Postępuj zgodnie z instrukcjami w sekcji [Utwórz nowy certyfikat](#create-a-new-certificate) wcześniej, używając daty, która pokrywa się z istniejącym certyfikatem. Ta data ogranicza liczbę przestojów spowodowanych wygaśnięciem certyfikatu.
1. Jeśli aplikacja może automatycznie przerzucić certyfikat, ustaw nowy certyfikat na aktywny, wykonując następujące kroki:
   1. Wróć do strony **Certyfikat podpisywania SAML.**
   1. W nowo zapisanym wierszu certyfikatu wybierz wielokropek (**...**), a następnie wybierz pozycję **Uatywn certyfikat**.
   1. Pomiń kolejne dwa kroki.

1. Jeśli aplikacja może obsługiwać tylko jeden certyfikat naraz, wybierz interwał przestoju, aby wykonać następny krok. (W przeciwnym razie jeśli aplikacja nie automatycznie odebrać nowy certyfikat, ale może obsłużyć więcej niż jeden certyfikat podpisywania, można wykonać następny krok w dowolnym momencie.)
1. Przed wygaśnięciem starego certyfikatu postępuj zgodnie z instrukcjami w sekcji [Przekaż i aktywuj certyfikat](#upload-and-activate-a-certificate) wcześniej.
1. Zaloguj się do aplikacji, aby upewnić się, że certyfikat działa poprawnie.

## <a name="related-articles"></a>Pokrewne artykuły:

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](../saas-apps/tutorial-list.md)
- [Zarządzanie aplikacjami przy użyciu usługi Azure Active Directory](what-is-application-management.md)
- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](what-is-single-sign-on.md)
- [Debugowanie aplikacji logowania jednokrotnego opartego na technologii SAML w usłudze Azure Active Directory](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
