---
title: Zarządzanie certyfikatami federacji w usłudze Azure AD | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dostosować datę wygaśnięcia certyfikatów Federacji i odnawianie certyfikatów, które wkrótce wygasną.
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: barbkess
ms.reviewer: jeedes
ms.openlocfilehash: 48f001ab0634451b1e339b346e03f5d15cdbf9c6
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54463851"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Zarządzanie certyfikatami federacyjnego logowania jednokrotnego w usłudze Azure Active Directory
W tym artykule opisano typowe pytania i informacje dotyczące certyfikatów, które tworzy usługi Azure Active Directory (Azure AD), można ustanowić federacyjnego logowania jednokrotnego (SSO) w aplikacjach SaaS. Dodawanie aplikacji z galerii aplikacji Azure AD lub przy użyciu szablonu aplikacji spoza galerii. Konfigurowanie aplikacji przy użyciu opcji federacyjnego logowania jednokrotnego.

W tym artykule dotyczy tylko aplikacji, które są skonfigurowane do używania logowania jednokrotnego usługi Azure AD przy użyciu Federacji SAML, jak pokazano w poniższym przykładzie:

![Usługa Azure AD logowanie jednokrotne](./media/manage-certificates-for-federated-single-sign-on/saml_sso.PNG)

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Automatycznie wygenerowany certyfikat dla aplikacji i innych galerii
Podczas dodawania nowej aplikacji z galerii i konfigurowania opartej na SAML logowania jednokrotnego, usługi Azure AD generuje certyfikat dla aplikacji, który jest ważny przez trzy lata. Możesz pobrać tego certyfikatu od **certyfikat podpisywania SAML** sekcji. W przypadku aplikacji z galerii w tej sekcji może wyświetlać umożliwiający pobranie certyfikatu lub metadane, w zależności od wymagań aplikacji.

![Usługa Azure AD logowania jednokrotnego](./media/manage-certificates-for-federated-single-sign-on/saml_certificate_download.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Dostosowywanie datę wygaśnięcia certyfikatu usługi federacyjnej i przechodzą do nowego certyfikatu
Domyślnie certyfikaty są ustawione na wygaśnięcie po trzy lata. Możesz wybrać inny wygaśnięcia dla certyfikatu, wykonując następujące kroki.
Zrzuty ekranów korzystają usługi Salesforce dla przykładu, ale te kroki można zastosować do wszystkich federacyjnych aplikacji SaaS.

1. W [witryny Azure portal](https://aad.portal.azure.com), kliknij przycisk **aplikacja dla przedsiębiorstw** w okienku po lewej stronie, a następnie kliknij przycisk **nową aplikację** na **Przegląd** strony:

   ![Otwórz kreatora Konfiguracja logowania jednokrotnego](./media/manage-certificates-for-federated-single-sign-on/enterprise_application_new_application.png)

2. Wyszukiwanie aplikacji z galerii, a następnie wybierz aplikację, którą chcesz dodać. Jeśli nie można odnaleźć wymaganej aplikacji, należy dodać aplikację przy użyciu **aplikacji spoza galerii** opcji. Ta funkcja jest dostępna tylko w ramach jednostki SKU usługi Azure AD Premium (P1 i P2).

    ![Usługa Azure AD logowania jednokrotnego](./media/manage-certificates-for-federated-single-sign-on/add_gallery_application.png)

3. Kliknij przycisk **logowanie jednokrotne** łącze w okienku po lewej stronie i zmień **tryb rejestracji jednokrotnej** do **opartej na SAML logowania jednokrotnego**. W tym kroku wygeneruje certyfikat z trzech lat dla swojej aplikacji.

4. Aby utworzyć nowy certyfikat, kliknij przycisk **Utwórz nowy certyfikat** łącze w **certyfikat podpisywania SAML** sekcji.

    ![Wygeneruj nowy certyfikat](./media/manage-certificates-for-federated-single-sign-on/create_new_certficate.png)

5. **Utwórz nowy certyfikat** łącze otwiera kontrolki kalendarza. Można ustawić dowolną datę i godzinę do trzech lat od bieżącej daty. Wybrana data i godzina to nowa data wygaśnięcia i godzina nowy certyfikat. Kliknij pozycję **Zapisz**.

    ![Pobierz, a następnie przekaż certyfikat](./media/manage-certificates-for-federated-single-sign-on/certifcate_date_selection.PNG)

6. Dostępne do pobrania jest teraz nowy certyfikat. Kliknij przycisk **certyfikatu** link, aby go pobrać. W tym momencie certyfikat nie jest aktywny. Przeskocz do tego certyfikatu, należy wybrać **Ustaw nowy certyfikat jako aktywny** pole wyboru i kliknij przycisk **Zapisz**. Z tego punktu Usługi Azure AD jest uruchamiany przy użyciu nowego certyfikatu podpisywania odpowiedzi.

7.  Aby dowiedzieć się, jak przekazać certyfikat do określonej aplikacji SaaS, kliknij przycisk **samouczek konfigurowania aplikacji widoku** łącza.

## <a name="certificate-expiration-notification-email"></a>Wiadomość e-mail z powiadomieniem wygaśnięcia certyfikatu

Usługa Azure AD będzie wysyłać wiadomość e-mail powiadomienie 60, 30 i 7 dni przed wygaśnięciem certyfikatu SAML. Aby określić adres e-mail, w których można wysłać powiadomienia:

- W witrynie usługi Azure Active Directory aplikacji pojedynczego logowania przejdź do pola wiadomość E-mail z powiadomieniem.
- Wprowadź adres e-mail, który powinien otrzymywać powiadomienia e-mail wygaśnięcia certyfikatu. Domyślnie to pole używa adresu e-mail administratora, który dodaje aplikację.

Otrzymasz powiadomienie e-mail z aadnotification@microsoft.com. Aby uniknąć poczty e-mail, przechodząc do lokalizacji wiadomości-śmieci, należy dodać tę wiadomość e-mail do Twoich kontaktów. 

## <a name="renew-a-certificate-that-will-soon-expire"></a>Odnów certyfikat, który wkrótce wygaśnie
Poniższe kroki odnawiania powinno spowodować nie znaczących przestojów dla użytkowników. Zrzuty ekranu w tej sekcji funkcji Salesforce jako przykład, ale te kroki można stosować do dowolnej federacyjnych aplikacji SaaS.

1. Na **usługi Azure Active Directory** aplikacji **logowanie jednokrotne** strony, generuje nowy certyfikat dla aplikacji. Można to zrobić, klikając **Utwórz nowy certyfikat** łącze w **certyfikat podpisywania SAML** sekcji.

    ![Wygeneruj nowy certyfikat](./media/manage-certificates-for-federated-single-sign-on/create_new_certficate.png)

2. Wybierz odpowiednią datę i godzinę wygaśnięcia dla nowego certyfikatu, a następnie kliknij przycisk **Zapisz**. Wybranie daty, która pokrywa się z istniejącym certyfikatem zapewni wynosi żadnych przestojów z powodu wygaśnięcia certyfikatu. 

3. Jeśli aplikacja może automatycznego przerzucania certyfikatu, należy ustawić nowy certyfikat na aktywny.  Zaloguj się do aplikacji, aby sprawdzić, czy działa.

4. Jeżeli aplikacja nie jest automatycznie pobrania nowego certyfikatu, ale można dojście do więcej niż jeden certyfikat podpisywania, przed upływem starego, Przekaż nowy katalog w celu aplikacji, a następnie wróć do portalu i przypisz ją aktywnego certyfikatu. 

5. Jeśli aplikacja może obsługiwać tylko jeden certyfikat naraz, wybierz okno Przestój Pobierz nowy certyfikat, przekaż go do aplikacji, wróć do witryny Azure Portal i ustaw nowy certyfikat jako aktywny. 
   
6. Aby aktywować nowy certyfikat w usłudze Azure AD, wybierz pozycję **Ustaw nowy certyfikat jako aktywny** pole wyboru i kliknij przycisk **Zapisz** znajdujący się u góry strony. Przedstawia to za pośrednictwem nowego certyfikatu po stronie usługi Azure AD. Zmiany stanu certyfikatu z **New** do **Active**. Z tego punktu Usługi Azure AD jest uruchamiany przy użyciu nowego certyfikatu podpisywania odpowiedzi. 
   
    ![Wygeneruj nowy certyfikat](./media/manage-certificates-for-federated-single-sign-on/new_certificate_download.png)

## <a name="related-articles"></a>Pokrewne artykuły:
* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](../saas-apps/tutorial-list.md)
* [Zarządzanie aplikacjami w usłudze Azure Active Directory](what-is-application-management.md)
* [Dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](what-is-single-sign-on.md)
* [Rozwiązywanie problemów z opartej na SAML logowania jednokrotnego](../develop/howto-v1-debug-saml-sso-issues.md)
