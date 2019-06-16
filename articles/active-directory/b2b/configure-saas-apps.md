---
title: Konfigurowanie aplikacji SaaS dla B2B współpracy — usługi Azure Active Directory | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 35dad420aa004e27ec974c494dc66e9b8e13c733
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65811943"
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>Konfigurowanie aplikacji SaaS na potrzeby współpracy B2B

Współpraca z usługą Azure Active Directory (Azure AD) B2B współpracuje z większość aplikacji, które integrują się z usługą Azure AD. W tej sekcji, w jaki sposób za pomocą instrukcji dotyczących konfigurowania niektóre popularne aplikacje SaaS do użycia przy użyciu usługi Azure AD B2B.

Zanim przyjrzymy się instrukcje specyficzne dla aplikacji, poniżej przedstawiono niektóre reguły akceptacji:

* Dla większości aplikacji ustawień użytkownika musi się zdarzyć, ręcznie. Oznacza to użytkownicy muszą ręcznie utworzone w aplikacji, jak również.

* W przypadku aplikacji, które obsługują konfigurację automatyczną, takiego jak Dropbox oddzielne zaproszenia są tworzone na podstawie aplikacji. Użytkownicy muszą być koniecznie zaakceptować zaproszenie każdego.

* Atrybuty użytkownika, aby uniknąć jakichkolwiek problemów z zniekształcone funkcja dysku profilu użytkownika (UPD) w użytkowników-gości, zawsze wartość **identyfikator użytkownika** do **user.mail**.


## <a name="dropbox-business"></a>Firmy Dropbox

Aby umożliwić użytkownikom na logowanie przy użyciu swojego konta organizacji, należy ręcznie skonfigurować usługi Dropbox firm, aby używać usługi Azure AD jako dostawcy tożsamości zabezpieczeń Assertion Markup Language (SAML). Jeśli firmy Dropbox nie została skonfigurowana w tym celu, go nie Monituj lub w przeciwnym razie użytkownicy mogą logować się za pomocą usługi Azure AD.

1. Aby dodać aplikację biznesową usługi Dropbox do usługi Azure AD, wybierz **aplikacje dla przedsiębiorstw** w okienku po lewej stronie, a następnie kliknij **Dodaj**.

   ![Przycisk "Dodaj" na stronie aplikacje przedsiębiorstwa](media/configure-saas-apps/add-dropbox.png)

2. W **dodać aplikację** oknie wprowadź **dropbox** w polu wyszukiwania, a następnie wybierz pozycję **usługa Dropbox dla firm** na liście wyników.

   ![Wyszukaj "dropbox" na stronie Dodawanie strony aplikacji](media/configure-saas-apps/add-app-dialog.png)

3. Na **logowanie jednokrotne** wybierz opcję **logowanie jednokrotne** w okienku po lewej stronie, a następnie wprowadź **user.mail** w **identyfikator użytkownika** pole. (Jego jest ustawione jako nazwy UPN domyślnie).

   ![Konfigurowanie logowania jednokrotnego dla aplikacji](media/configure-saas-apps/configure-app-sso.png)

4. Aby pobrać certyfikat do użycia podczas konfiguracji usługi Dropbox, zaznacz **skonfigurować DropBox**, a następnie wybierz pozycję **SAML pojedynczy znak na adres URL usługi** na liście.

   ![Pobieranie certyfikatu dla konfiguracji usługi Dropbox](media/configure-saas-apps/download-certificate.png)

5. Zaloguj się do usługi Dropbox na adres URL logowania jednokrotnego z **logowanie jednokrotne** strony.

   ![Zrzut ekranu przedstawiający stronę logowania w usłudze Dropbox](media/configure-saas-apps/sign-in-to-dropbox.png)

6. W menu, wybierz **konsoli administracyjnej**.

   ![Link "Konsola administratora" w menu usługi Dropbox](media/configure-saas-apps/dropbox-menu.png)

7. W **uwierzytelniania** okno dialogowe, wybierz opcję **więcej**, Przekaż certyfikat i następnie **Zaloguj się w adresie URL** wprowadź SAML pojedynczy adres URL logowania.

   ![Link "Więcej" w oknie dialogowym uwierzytelniania zwinięty](media/configure-saas-apps/dropbox-auth-01.png)

   !["Zaloguj się w adresie URL" rozwinięty w oknie dialogowym uwierzytelniania](media/configure-saas-apps/paste-single-sign-on-URL.png)

8. Aby skonfigurować ustawienia automatycznego użytkownika w witrynie Azure portal, wybierz **aprowizacji** w okienku po lewej stronie wybierz **automatyczne** w **inicjowania obsługi trybu** , a następnie wybierz  **Autoryzuj**.

   ![Konfigurowanie automatycznej aprowizacji użytkowników w witrynie Azure portal](media/configure-saas-apps/set-up-automatic-provisioning.png)

Po użytkowników z gościa lub elementu członkowskiego zdefiniowano w aplikacji Dropbox, otrzyma zaproszenie oddzielne z usługi Dropbox. Aby korzystać z usługi Dropbox logowanie jednokrotne, zapraszane osoby muszą musi zaakceptować zaproszenie, klikając łącze w nim.

## <a name="box"></a>Box
Możesz umożliwić użytkownikom uwierzytelnianie użytkowników-gości pole za pomocą konta usługi Azure AD przy użyciu Federacji, który jest oparty na protokole SAML. W ramach tej procedury możesz przekazać do usługi Box.com metadanych.

1. Dodawanie aplikacji Box z aplikacjami w przedsiębiorstwie.

2. Skonfiguruj logowanie jednokrotne w następującej kolejności:

   ![Zrzut ekranu przedstawiający ustawienia konfiguracji rejestracji jednokrotnej](media/configure-saas-apps/configure-box-sso.png)

   a. W **adres URL logowania** Sprawdź, czy adres URL logowania jest odpowiednio ustawiony dla pola w witrynie Azure portal. Ten adres URL jest adres URL dzierżawy usługi Box.com. Powinien on być zgodny konwencji nazewnictwa *https://.box.com* .  
   **Identyfikator** nie ma zastosowania do tej aplikacji, ale nadal jest wyświetlany jako wymaganym polem.

   b. W **identyfikator użytkownika** wprowadź **user.mail** (na potrzeby logowania jednokrotnego dla konta gościa).

   c. W obszarze **certyfikat podpisywania SAML**, kliknij przycisk **Utwórz nowy certyfikat**.

   d. Aby rozpocząć konfigurowanie Twojej dzierżawy usługi Box.com, używać usługi Azure AD jako dostawcy tożsamości, pobrać pliku metadanych, a następnie zapisz go na dysk lokalny.

   e. Przekazuj plik metadanych do pola obsługuje zespół, który umożliwia skonfigurowanie logowania jednokrotnego dla Ciebie.

3. Ustawienia automatycznego użytkownika usługi Azure AD, w okienku po lewej stronie wybierz **aprowizacji**, a następnie wybierz pozycję **Autoryzuj**.

   ![Autoryzacja usługi Azure AD connect do pola](media/configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Np. usługi Dropbox zapraszane osoby muszą zapraszane osoby muszą pole musi zrealizowanie zaproszenia z aplikacji Box.

## <a name="next-steps"></a>Kolejne kroki

Na współpracy B2B usługi Azure AD, zobacz następujące artykuły:

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Grupy dynamiczne i współpracy B2B](use-dynamic-groups.md)
- [Mapowanie oświadczeń użytkowników współpracy B2B](claims-mapping.md)
- [Udostępnianie zewnętrzne w usłudze Office 365](o365-external-user.md)

