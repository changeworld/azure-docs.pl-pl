---
title: Aplikacje obsługujące oświadczenia — serwera Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Sposób publikowania lokalnych aplikacji programu ASP.NET, które akceptują oświadczeń ADFS, aby uzyskać bezpieczny dostęp zdalny przez użytkowników.
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
ms.openlocfilehash: c97729cf7d88ebdeefb44c83eb571bb6d7ebd0ed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65825600"
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Praca z aplikacji obsługujących oświadczenia na serwerze Proxy aplikacji
[Aplikacje obsługujące oświadczenia](https://msdn.microsoft.com/library/windows/desktop/bb736227.aspx) wykonania przekierowania do Usługa tokenu zabezpieczającego (STS). Usługa STS żąda poświadczeń użytkownika w zamian za token, a następnie przekierowuje użytkownika do aplikacji. Istnieje kilka sposobów, aby włączyć serwer Proxy aplikacji do pracy z tymi przekierowania. Aby skonfigurować wdrożenie aplikacji z obsługą oświadczeń, należy użyć w tym artykule. 

## <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że usługą STS, która przekierowuje obsługującej oświadczenia aplikacji jest dostępna spoza sieci lokalnej. Możesz udostępnić usługi STS przez ujawnienie go za pośrednictwem serwera proxy lub zezwala na połączenia zewnętrzne. 

## <a name="publish-your-application"></a>Publikowanie aplikacji

1. Opublikowanie aplikacji zgodnie z instrukcji przedstawionych w temacie [publikowania aplikacji za pomocą serwera Proxy aplikacji](application-proxy-add-on-premises-application.md).
2. Przejdź do strony aplikacji w portalu i wybierz **logowanie jednokrotne**.
3. W przypadku wybrania **usługi Azure Active Directory** jako swojej **metoda uwierzytelniania wstępnego**, wybierz opcję **usługi Azure AD logowanie jednokrotne wyłączone** jako swoje **wewnętrzne Metoda uwierzytelniania**. Jeśli została wybrana opcja **przekazywanie** jako swojej **metoda uwierzytelniania wstępnego**, nie trzeba wprowadzić zmiany.

## <a name="configure-adfs"></a>Konfigurowanie usług AD FS

Usługi AD FS można skonfigurować dla aplikacji obsługujących oświadczenia w jeden z dwóch sposobów. Pierwsza to za pomocą domen niestandardowych. Drugim jest przy użyciu protokołu WS-Federation. 

### <a name="option-1-custom-domains"></a>Opcja 1: Niestandardowe domeny

Jeśli wszystkie wewnętrzne adresy URL dla aplikacji są w pełni kwalifikowanej nazwy domeny (FQDN), a następnie można skonfigurować [domen niestandardowych](application-proxy-configure-custom-domain.md) dla aplikacji. Użyj domen niestandardowych, aby utworzyć zewnętrzne adresy URL, które są takie same jak wewnętrzne adresy URL. W przypadku usługi zewnętrzne adresy URL zgodne z wewnętrznych adresów URL, przekierowań STS działać, czy użytkownicy znajdują się w środowisku lokalnym lub zdalnym. 

### <a name="option-2-ws-federation"></a>Opcja 2: WS-Federation

1. Otwórz przystawkę Zarządzanie usług AD FS.
2. Przejdź do **zaufania jednostek uzależnionych**, kliknij prawym przyciskiem myszy w aplikacji, w przypadku publikowania za pomocą serwera Proxy aplikacji, a wybierz **właściwości**.  

   ![Jednostki uzależnionej zaufania kliknij prawym przyciskiem myszy nazwę aplikacji — zrzut ekranu](./media/application-proxy-configure-for-claims-aware-applications/appproxyrelyingpartytrust.png)  

3. Na **punktów końcowych** , w obszarze **typ punktu końcowego**, wybierz opcję **WS-Federation**.
4. W obszarze **zaufany adres URL**, wprowadź adres URL wprowadzony na serwerze Proxy aplikacji w ramach **zewnętrzny adres URL** i kliknij przycisk **OK**.  

   ![Dodawanie punktu końcowego — ustaw wartość zaufanego adresu URL — zrzut ekranu](./media/application-proxy-configure-for-claims-aware-applications/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Kolejne kroki
* [Włączanie logowania jednokrotnego na](configure-single-sign-on-portal.md) dla aplikacji, które nie obsługują oświadczeń
* [Włącz aplikacje klienta natywnego do interakcji z serwera proxy aplikacji](application-proxy-configure-native-client-application.md)


