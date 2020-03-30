---
title: Konfigurowanie aplikacji SaaS do współpracy b2b — usługa Azure AD
description: Przykłady kodu i programu PowerShell na potrzeby współpracy B2B w usłudze Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c73a14c2a8cd063672bd0998368ca660f52cd5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272948"
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>Konfigurowanie aplikacji SaaS na potrzeby współpracy B2B

Współpraca b2b usługi Azure Active Directory (Azure AD) współpracuje z większością aplikacji integruj się z usługą Azure AD. W tej sekcji omówimy instrukcje konfigurowania niektórych popularnych aplikacji SaaS do użytku z usługą Azure AD B2B.

Zanim spojrzysz na instrukcje dotyczące aplikacji, oto kilka reguł:

* W przypadku większości aplikacji konfiguracja użytkownika musi odbywać się ręcznie. Oznacza to, że użytkownicy muszą być tworzone ręcznie w aplikacji, jak również.

* W przypadku aplikacji obsługujących automatyczną konfigurację, takich jak Dropbox, z aplikacji tworzone są oddzielne zaproszenia. Użytkownicy muszą mieć pewność, że zaakceptują każde zaproszenie.

* W atrybutach użytkownika, aby ograniczyć wszelkie problemy z zniekształconym dyskiem profilu użytkownika (UPD) w użytkownikach-gościach, należy zawsze ustawiać **identyfikator użytkownika** na **user.mail**.


## <a name="dropbox-business"></a>Dropbox Business

Aby umożliwić użytkownikom logowanie się przy użyciu konta organizacji, należy ręcznie skonfigurować dropbox business tak, aby używał usługi Azure AD jako dostawcy tożsamości języka saml (NyL). Jeśli Dropbox Business nie został skonfigurowany do tego celu, nie może monitować ani w inny sposób zezwalać użytkownikom na logowanie się przy użyciu usługi Azure AD.

1. Aby dodać aplikację Dropbox Business do usługi Azure AD, wybierz **pozycję Aplikacje przedsiębiorstwa** w lewym okienku, a następnie kliknij przycisk **Dodaj**.

   ![Przycisk "Dodaj" na stronie Aplikacje przedsiębiorstwa](media/configure-saas-apps/add-dropbox.png)

2. W oknie **Dodaj aplikację** wprowadź **dropbox** w polu wyszukiwania, a następnie wybierz pozycję Dropbox **dla firm** na liście wyników.

   ![Wyszukaj "dropbox" na stronie Dodawanie aplikacji](media/configure-saas-apps/add-app-dialog.png)

3. Na stronie **Logowanie jednokrotne** wybierz pozycję **Logowanie jednokrotne** w lewym okienku, a następnie wprowadź **adres user.mail** w polu **Identyfikator użytkownika.** (Domyślnie jest skonfigurowana jako niania o wartości 100 000.)

   ![Konfigurowanie logowania jednokrotnego dla aplikacji](media/configure-saas-apps/configure-app-sso.png)

4. Aby pobrać certyfikat używany do konfiguracji Dropbox, wybierz pozycję **Konfiguruj dropbox**, a następnie wybierz **pozycję SAML Single Sign On Service URL na** liście.

   ![Pobieranie certyfikatu dla konfiguracji Dropbox](media/configure-saas-apps/download-certificate.png)

5. Zaloguj się do Dropbox za pomocą adresu URL logowania na stronie **jednokrotnego logowania.**

   ![Zrzut ekranu przedstawiający stronę logowania dropbox](media/configure-saas-apps/sign-in-to-dropbox.png)

6. W menu wybierz pozycję **Konsola administracyjna**.

   ![Link "Konsola administracyjna" w menu Dropbox](media/configure-saas-apps/dropbox-menu.png)

7. W oknie dialogowym **Uwierzytelnianie** wybierz pozycję **Więcej**, przekaż certyfikat, a następnie w polu **Zaloguj adres URL** wprowadź adres URL logowania JEDNOkrotnego SAML.

   ![Łącze "Więcej" w zwiniętym oknie dialogowym Uwierzytelnianie](media/configure-saas-apps/dropbox-auth-01.png)

   !["Adres URL logowania" w rozwiniętym oknie dialogowym Uwierzytelnianie](media/configure-saas-apps/paste-single-sign-on-URL.png)

8. Aby skonfigurować automatyczną konfigurację użytkownika w witrynie Azure portal, wybierz pozycję **Inicjowanie obsługi administracyjnej** w lewym okienku, wybierz pozycję **Automatycznie** w polu **Tryb inicjowania obsługi administracyjnej,** a następnie wybierz pozycję **Autoryzuj**.

   ![Konfigurowanie automatycznego inicjowania obsługi administracyjnej użytkowników w witrynie Azure portal](media/configure-saas-apps/set-up-automatic-provisioning.png)

Po skonfigurowaniu użytkowników gości lub członków w aplikacji Dropbox otrzymują oni osobne zaproszenie od Dropbox. Aby korzystać z logowania jednokrotnego w Dropbox, osoby zaproszone muszą zaakceptować zaproszenie, klikając w nim link.

## <a name="box"></a>Box
Można umożliwić użytkownikom uwierzytelnienie użytkowników-gości box z ich konta usługi Azure AD przy użyciu federacji, która jest oparta na protokole SAML. W tej procedurze należy przekazać metadane do Box.com.

1. Dodaj aplikację Box z aplikacji dla przedsiębiorstw.

2. Skonfiguruj logowanie jednokrotne w następującej kolejności:

   ![Zrzut ekranu przedstawiający ustawienia konfiguracji logowania jednokrotnego](media/configure-saas-apps/configure-box-sso.png)

   a. W polu **Zaloguj się na adres URL** upewnij się, że adres URL logowania jest ustawiony odpowiednio dla Box w witrynie Azure portal. Ten adres URL to adres URL dzierżawy Box.com. Należy postępować zgodnie z *https://.box.com*konwencją nazewnictwa .  
   **Identyfikator** nie ma zastosowania do tej aplikacji, ale nadal jest wyświetlany jako pole obowiązkowe.

   b. W polu **Identyfikator użytkownika** wprowadź **user.mail** (dla aplikacji SSO dla kont gości).

   d. W obszarze **Certyfikat podpisywania SAML**kliknij pozycję **Utwórz nowy certyfikat**.

   d. Aby rozpocząć konfigurowanie dzierżawy Box.com do używania usługi Azure AD jako dostawcy tożsamości, pobierz plik metadanych, a następnie zapisz go na dysku lokalnym.

   e. Przekaż plik metadanych do zespołu pomocy technicznej Box, który konfiguruje logowanie jednokrotne.

3. W przypadku automatycznej konfiguracji użytkownika usługi Azure AD w lewym okienku wybierz pozycję **Inicjowanie obsługi administracyjnej,** a następnie wybierz pozycję **Autoryzuj**.

   ![Autoryzowanie usługi Azure AD do łączenia się z polem Box](media/configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Podobnie jak osoby zaproszone do Dropbox, osoby zaproszone do boxu muszą zrealizować zaproszenie z aplikacji Box.

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły dotyczące współpracy usługi Azure AD B2B:

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Grupy dynamiczne i współpraca B2B](use-dynamic-groups.md)
- [Mapowanie oświadczeń użytkowników współpracy B2B](claims-mapping.md)
- [Udostępnianie zewnętrzne w usłudze Office 365](o365-external-user.md)

