---
title: Aplikacje obsługujące oświadczenia — serwer proxy aplikacji usługi Azure AD | Dokumenty firmy Microsoft
description: Jak publikować lokalnie ASP.NET aplikacje, które akceptują oświadczenia usługi ADFS dotyczące bezpiecznego dostępu zdalnego przez użytkowników.
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
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbc5c356ea5a542fdc12b11aff236c56b146b3d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68477245"
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Praca z aplikacjami obsługującymi oświadczenia w serwerze proxy aplikacji
[Aplikacje obsługujące oświadczenia](https://msdn.microsoft.com/library/windows/desktop/bb736227.aspx) wykonują przekierowanie do usługi tokenu zabezpieczającego (STS). STS żąda poświadczeń od użytkownika w zamian za token, a następnie przekierowuje użytkownika do aplikacji. Istnieje kilka sposobów, aby włączyć serwer proxy aplikacji do pracy z tych przekierowań. Ten artykuł służy do konfigurowania wdrożenia dla aplikacji obsługujących oświadczenia. 

## <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że usługa STS przekierowywania aplikacji obsługujących oświadczenia jest dostępna poza siecią lokalną. Można udostępnić sts, ujawniając go za pośrednictwem serwera proxy lub zezwalając na połączenia zewnętrzne. 

## <a name="publish-your-application"></a>Publikowanie aplikacji

1. Opublikuj aplikację zgodnie z instrukcjami opisanymi w [publikacji aplikacji za pomocą serwera proxy aplikacji](application-proxy-add-on-premises-application.md).
2. Przejdź do strony aplikacji w portalu i wybierz **pozycję Logowanie jednokrotne**.
3. Jeśli jako **metodę preauthentication** **wybrano usługę Azure Active Directory,** wybierz **opcję Rejestracji jednokrotnej usługi Azure AD jako** metodę **uwierzytelniania wewnętrznego.** Jeśli jako **metodę preauthentication**wybrano **opcję Passthrough,** nie musisz niczego zmieniać.

## <a name="configure-adfs"></a>Konfigurowanie usługi ADFS

Usługi ADFS można skonfigurować dla aplikacji obsługujących oświadczenia na jeden z dwóch sposobów. Pierwszym z nich jest przy użyciu domen niestandardowych. Drugi jest z WS-Federation. 

### <a name="option-1-custom-domains"></a>Opcja 1: Domeny niestandardowe

Jeśli wszystkie wewnętrzne adresy URL aplikacji są w pełni kwalifikowanymi nazwami domen (FQDN), możesz skonfigurować [domeny niestandardowe](application-proxy-configure-custom-domain.md) dla aplikacji. Użyj domen niestandardowych, aby utworzyć zewnętrzne adresy URL, które są takie same jak wewnętrzne adresy URL. Gdy zewnętrzne adresy URL są zgodne z wewnętrznymi adresami URL, przekierowania sts działają niezależnie od tego, czy użytkownicy są lokalni, czy zdalnie. 

### <a name="option-2-ws-federation"></a>Opcja 2: Federacja WS

1. Otwórz zarządzanie usługą ADFS.
2. Przejdź do **relacji zaufania jednostki uzależnionej,** kliknij prawym przyciskiem myszy aplikację, którą publikujesz za pomocą serwera proxy aplikacji, a następnie wybierz polecenie **Właściwości**.  

   ![Zaufanie jednostki uzależniającej kliknij prawym przyciskiem myszy nazwę aplikacji - zrzut ekranu](./media/application-proxy-configure-for-claims-aware-applications/appproxyrelyingpartytrust.png)  

3. Na karcie **Punkty końcowe** w obszarze **Typ punktu końcowego**wybierz pozycję **Federacja WS**.
4. W obszarze **Zaufany adres URL**wprowadź adres URL wprowadzony w serwerze proxy aplikacji w obszarze **Zewnętrzny adres URL** i kliknij przycisk **OK**.  

   ![Dodawanie punktu końcowego — ustawianie wartości zaufanego adresu URL — zrzut ekranu](./media/application-proxy-configure-for-claims-aware-applications/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Następne kroki
* [Włączanie logowania jednokrotnego](configure-single-sign-on-non-gallery-applications.md) dla aplikacji, które nie są obsługujące oświadczenia
* [Włączanie natywnych aplikacji klienckich do interakcji z aplikacjami proxy](application-proxy-configure-native-client-application.md)


