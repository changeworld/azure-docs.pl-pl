---
title: Zarządzanie logowania jednokrotnego dla serwera Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Poznaj podstawowe informacje dotyczące logowania jednokrotnego przy użyciu serwera Proxy aplikacji
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: dbdbe8b83af20f66ad2cc99d2a5665262479b4a9
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364152"
---
# <a name="how-does-azure-ad-application-proxy-provide-single-sign-on"></a>W jaki sposób serwer Proxy aplikacji usługi Azure AD zapewnia logowanie jednokrotne

Logowanie jednokrotne jest kluczowym elementem serwera Proxy aplikacji usługi Azure AD.  Ponieważ użytkownicy muszą tylko zalogować się do usługi Azure Active Directory w chmurze, zapewnia najlepsze środowisko użytkownika. Łącznik serwera Proxy aplikacji obsługuje uwierzytelnianie do aplikacji w środowisku lokalnym, gdy uwierzytelniają się do usługi Azure Active Directory. Aplikacja wewnętrznej bazy danych nie można rozróżnić między użytkownika zdalnego logowania za pośrednictwem serwera Proxy aplikacji i normalnie korzystać na urządzeniu przyłączonym do domeny. 

Aby użyć usługi Azure Active Directory logowania jednokrotnego do aplikacji, musisz wybrać **usługi Azure Active Directory** jako metoda uwierzytelniania wstępnego. Jeśli wybierzesz **przekazywanie** , a następnie użytkownicy nie uwierzytelniania usługi Azure Active Directory w ogóle, ale są kierowane bezpośrednio do aplikacji. To ustawienie można skonfigurować, gdy najpierw opublikować aplikację, lub przejdź do aplikacji w witrynie Azure portal i edytować ustawienia serwera Proxy aplikacji. 

Aby wyświetlić dostępne opcje pojedynczego logowania jednokrotnego, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do **usługi Azure Active Directory** > **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.
3. Wybierz aplikację, dla których pojedynczego logowania jednokrotnego opcje chcesz zarządzać.
4. Wybierz **logowanie jednokrotne**.

   ![Menu rozwijane logowania jednokrotnego](./media/application-proxy-single-sign-on/single-sign-on-mode.png)

Menu rozwijane zawiera pięć opcji logowania jednokrotnego do aplikacji:

* Logowanie jednokrotne usługi Azure AD jest wyłączone
* Logowanie na podstawie hasła
* Połączone logowanie
* Zintegrowane uwierzytelnianie systemu Windows
* Logowanie na podstawie nagłówka

## <a name="azure-ad-single-sign-on-disabled"></a>Logowanie jednokrotne usługi Azure AD jest wyłączone

Jeśli nie chcesz korzystać z integracji usługi Azure Active Directory dla logowania jednokrotnego do aplikacji, wybierz opcję **usługi Azure AD logowanie jednokrotne wyłączone**. Ta opcja jest zaznaczona użytkownicy mogą uwierzytelniać dwa razy. Najpierw uwierzytelniania w usłudze Azure Active Directory i następnie zaloguj się do samej aplikacji. 

Ta opcja jest dobrym rozwiązaniem, jeśli Twoja aplikacja lokalna nie wymaga uwierzytelniania użytkowników na, ale chcesz dodać usługę Azure Active Directory jako warstwa zabezpieczeń dla dostępu zdalnego. 

## <a name="password-based-sign-on"></a>Logowanie na podstawie hasła

Jeśli chcesz użyć usługi Azure Active Directory jako magazynu hasła dla aplikacji w środowisku lokalnym, wybierz opcję **opartego na hasłach logowania jednokrotnego**. Ta opcja jest dobrym rozwiązaniem, jeśli aplikacja uwierzytelnia się za pomocą nazwy użytkownika/hasła kombi zamiast tokenów dostępu lub nagłówków. Za pomocą opartego na hasłach logowania jednokrotnego użytkownicy potrzebują logować się do czasu aplikacji pierwszy, mogą uzyskać do niego dostęp. Po tym usługi Azure Active Directory dostarcza nazwy użytkownika i hasła w imieniu użytkownika. 

Aby uzyskać informacji o ustawieniach na podstawie hasła logowania jednokrotnego, zobacz [hasło vaulting dla logowania jednokrotnego przy użyciu serwera Proxy aplikacji](application-proxy-configure-single-sign-on-password-vaulting.md).

## <a name="linked-sign-on"></a>Połączone logowanie

Jeśli masz już jednego rozwiązania logowania jednokrotnego dla Twojej tożsamości użytkownika lokalnego, wybierz **połączonego logowania jednokrotnego**. Ta opcja umożliwia usługi Azure Active Directory, aby korzystać z istniejących rozwiązań logowania jednokrotnego, ale nadal umożliwia użytkownikom dostęp zdalny do aplikacji. 

Aby uzyskać informacji na temat połączonego logowania jednokrotnego (wcześniej znany jako istniejące logowanie jednokrotne), zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="integrated-windows-authentication"></a>Zintegrowane uwierzytelnianie systemu Windows

Jeśli aplikacje w środowisku lokalnym, Użyj zintegrowanego Authentication(IWA) Windows lub na potrzeby delegowania ograniczonego protokołu Kerberos (KCD) logowanie jednokrotne, wybierz opcję **zintegrowane uwierzytelnianie Windows**. Po wybraniu tej opcji użytkownicy potrzebują tylko do uwierzytelniania w usłudze Azure Active Directory, a następnie personifikuje użytkownika do pobrania tokenu protokołu Kerberos i zaloguj się do aplikacji przez łącznik serwera Proxy aplikacji. 

Aby uzyskać informacje o konfigurowaniu zintegrowane uwierzytelnianie Windows, zobacz [ograniczonego delegowania protokołu Kerberos do logowania jednokrotnego przy użyciu serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="header-based-sign-on"></a>Logowanie na podstawie nagłówka 

Jeśli aplikacje używające nagłówków na potrzeby uwierzytelniania, wybierz opcję **logowanie na podstawie nagłówka**. Po wybraniu tej opcji użytkownicy potrzebują tylko do uwierzytelniania usługi Azure Active Directory. Partnerzy firmy Microsoft z usługą uwierzytelniania innej firmy o nazwie PingAccess, który przetłumaczyć token dostępu usługi Azure Active Directory format nagłówka w aplikacji. 

Aby uzyskać informacji na temat konfigurowania uwierzytelniania opartego na nagłówek, zobacz [opartej na nagłówkach uwierzytelniania dla logowania jednokrotnego przy użyciu serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-ping-access.md).

## <a name="next-steps"></a>Kolejne kroki

- [Hasło vaulting dla logowania jednokrotnego przy użyciu serwera Proxy aplikacji](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Ograniczone delegowanie protokołu Kerberos do logowania jednokrotnego przy użyciu serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)
- [Na podstawie nagłówka uwierzytelniania dla logowania jednokrotnego przy użyciu serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-ping-access.md) 
