---
title: Problem z konfiguracją federacyjnego logowania jednokrotnego dla aplikacji spoza galerii | Microsoft Docs
description: Rozwiązywanie niektórych typowych problemów, które mogą wystąpić podczas konfigurowania federacyjnego logowania jednokrotnego do niestandardowej aplikacji SAML, która nie jest wymieniona w galerii aplikacji usługi Azure AD.
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
ROBOTS: NOINDEX
ms.openlocfilehash: 99c5e4d99f45e2a642a46f7dc070fb7512ff4d73
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422558"
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>Wystąpił problem podczas konfigurowania federacyjnego logowania jednokrotnego dla aplikacji spoza galerii

Jeśli wystąpi problem podczas konfigurowania aplikacji. Upewnij się, że wykonano wszystkie kroki opisane w artykule [Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)

## <a name="cant-add-another-instance-of-the-application"></a>Nie można dodać innego wystąpienia aplikacji

Aby dodać drugie wystąpienie aplikacji, musisz mieć możliwość:

-   Skonfiguruj unikatowy identyfikator drugiego wystąpienia. Nie można skonfigurować tego samego identyfikatora, który jest używany dla pierwszego wystąpienia.

-   Skonfiguruj inny certyfikat niż użyty dla pierwszego wystąpienia.

Jeśli aplikacja nie obsługuje żadnego z powyższych, nie można skonfigurować drugiego wystąpienia.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Gdzie ustawić format EntityID (identyfikator użytkownika)

Nie można wybrać formatu EntityID (identyfikator użytkownika), który usługa Azure AD wysyła do aplikacji w odpowiedzi po uwierzytelnieniu użytkownika.

Usługa Azure AD wybiera format atrybutu NameID (identyfikator użytkownika) w oparciu o wybraną wartość lub format żądany przez aplikację w elemencie SAML AuthRequest. Aby uzyskać więcej informacji, zapoznaj się z artykułem Logowanie jednokrotne logowania jednokrotnego [protokołu SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) w sekcji NameIDPolicy.

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Gdzie można uzyskać metadane aplikacji lub certyfikat z usługi Azure AD

Aby pobrać metadane aplikacji lub certyfikat z usługi Azure AD, wykonaj następujące kroki:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub współadministrator **.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, dla której skonfigurowano Logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

8. Przejdź do sekcji **certyfikat podpisywania SAML** , a następnie kliknij pozycję **Pobierz** wartość kolumny. W zależności od tego, co aplikacja wymaga skonfigurowania logowania jednokrotnego, zobaczysz opcję pobrania XML metadanych lub certyfikatu.

Usługa Azure AD nie udostępnia adresu URL do pobrania metadanych. Metadane można pobrać tylko jako plik XML.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Nie wiadomo, jak dostosować oświadczenia SAML wysyłane do aplikacji

Aby dowiedzieć się, jak dostosować oświadczenia atrybutu SAML wysyłane do aplikacji, zobacz [Mapowanie oświadczeń w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) , aby uzyskać więcej informacji.

## <a name="next-steps"></a>Następne kroki
[Managing Applications with Azure Active Directory (Zarządzanie aplikacjami za pomocą usługi Azure Active Directory)](what-is-application-management.md)
