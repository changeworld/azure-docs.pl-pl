---
title: Problem z konfiguracją sfederowanego logowania jednokrotnego dla aplikacji niezwiązanych z galerią | Dokumenty firmy Microsoft
description: Rozwiązywanie niektórych typowych problemów, które mogą wystąpić podczas konfigurowania federacyjnego logowania jednokrotnego do niestandardowej aplikacji SAML, która nie jest wymieniona w Galerii aplikacji usługi Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7894bfada4d363e89f526280e2925b4f4c6180a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76711879"
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>Problem z konfigurowaniem federacyjnego logowania jednokrotnego dla aplikacji spoza galerii

Jeśli wystąpi problem podczas konfigurowania aplikacji. Sprawdź, czy postępujesz do wszystkich kroków w [artykule Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji usługi Azure Active Directory.](configure-federated-single-sign-on-non-gallery-applications.md)

## <a name="cant-add-another-instance-of-the-application"></a>Nie można dodać innego wystąpienia aplikacji

Aby dodać drugie wystąpienie aplikacji, musisz mieć możliwość:

-   Skonfiguruj unikatowy identyfikator dla drugiego wystąpienia. Nie można skonfigurować tego samego identyfikatora, który jest używany dla pierwszego wystąpienia.

-   Skonfiguruj inny certyfikat niż używany w pierwszym wystąpieniu.

Jeśli aplikacja nie obsługuje żadnego z powyższych, nie można skonfigurować drugie wystąpienie.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Gdzie ustawić format Identyfikator jednostki (identyfikator użytkownika)

Nie można wybrać formatu Identyfikator użytkownika (Identyfikator użytkownika), który usługa Azure AD wysyła do aplikacji w odpowiedzi po uwierzytelnieniu użytkownika.

Usługa Azure AD wybiera format atrybutu NameID (identyfikator użytkownika) na podstawie wybranej wartości lub formatu żądanego przez aplikację w saml AuthRequest. Aby uzyskać więcej informacji, odwiedź artykuł [Protokół SAML logowania jednokrotnego](../develop/single-sign-on-saml-protocol.md#authnrequest) w sekcji NameIDPolicy,

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Gdzie mogę uzyskać metadane aplikacji lub certyfikat z usługi Azure AD

Aby pobrać metadane aplikacji lub certyfikat z usługi Azure AD, wykonaj następujące kroki:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6. Wybierz aplikację skonfigurowaną logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8. Przejdź do sekcji **Saml Podpisywanie certyfikatów,** a następnie kliknij pozycję **Pobierz** wartość kolumny. W zależności od tego, co aplikacja wymaga konfigurowania logowania jednokrotnego, zostanie wyświetlone możliwe pobranie metadanych XML lub certyfikatu.

Usługa Azure AD nie udostępnia adresu URL, aby uzyskać metadane. Metadane można pobrać tylko jako plik XML.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Nie wiem, jak dostosować oświadczenia SAML wysyłane do aplikacji

Aby dowiedzieć się, jak dostosować oświadczenia atrybutów SAML wysyłane do aplikacji, zobacz [Mapowanie oświadczeń w usłudze Azure Active Directory,](../develop/active-directory-claims-mapping.md) aby uzyskać więcej informacji.

## <a name="next-steps"></a>Następne kroki
[Zarządzanie aplikacjami za pomocą usługi Azure Active Directory](what-is-application-management.md)
