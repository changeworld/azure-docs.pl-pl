---
title: Zarządzanie rejestracji Jednokrotnej dla serwera Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Poznaj podstawy rejestracji jednokrotnej z serwerem Proxy aplikacji
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: barbkess
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: d1c9f4dd8504c2409663cb87b0c0d56164229ed2
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="how-does-azure-ad-application-proxy-provide-single-sign-on"></a>W jaki sposób serwera Proxy aplikacji usługi Azure AD zapewnia rejestrację jednokrotną

Logowanie jednokrotne jest kluczowym elementem serwera Proxy aplikacji usługi Azure AD.  Ponieważ użytkownicy muszą tylko zalogować się do usługi Azure Active Directory w chmurze zapewnia najlepsze środowisko użytkownika. Gdy do usługi Azure Active Directory, uwierzytelniają łącznika serwera Proxy aplikacji obsługuje uwierzytelniania do aplikacji lokalnych. Aplikacja wewnętrznej bazy danych nie można rozróżnić użytkownika zdalnego logowania za pośrednictwem serwera Proxy aplikacji i normalnie korzystać na urządzeniu przyłączonym do domeny. 

Aby używać usługi Azure Active Directory dla rejestracji jednokrotnej do aplikacji, musisz wybrać **usługi Azure Active Directory** jako metody uwierzytelniania wstępnego. W przypadku wybrania **Passthrough** , a następnie użytkownicy nie uwierzytelniania usługi Azure Active Directory na wszystkie, ale są kierowane bezpośrednio do aplikacji. To ustawienie można skonfigurować przy najpierw opublikować aplikację, lub przejdź do aplikacji w portalu Azure i edytować ustawienia serwera Proxy aplikacji. 

Aby wyświetlić jednej opcji logowania jednokrotnego, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do **usługi Azure Active Directory** > **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.
3. Wybierz aplikację, dla których pojedynczego logowania jednokrotnego opcje chcesz zarządzać.
4. Wybierz **logowanie jednokrotne**.

   ![Menu rozwijane logowania jednokrotnego](./media/application-proxy-single-sign-on/single-sign-on-mode.png)

Menu rozwijane zawiera pięć opcji rejestracji jednokrotnej do aplikacji:

* Logowanie jednokrotne usługi Azure AD jest wyłączone
* Na podstawie hasła logowania jednokrotnego
* Połączonego logowania jednokrotnego
* Zintegrowane uwierzytelnianie systemu Windows
* Na podstawie nagłówka logowania jednokrotnego

## <a name="azure-ad-single-sign-on-disabled"></a>Logowanie jednokrotne usługi Azure AD jest wyłączone

Jeśli nie chcesz korzystać z integracji usługi Azure Active Directory dla rejestracji jednokrotnej do aplikacji, wybierz **usługi Azure AD rejestracji jednokrotnej wyłączone**. W przypadku wybrania tej opcji użytkownicy mogą uwierzytelniać dwa razy. Po pierwsze uwierzytelnianie w usłudze Azure Active Directory i następnie zaloguj się do samej aplikacji. 

Ta opcja jest dobrym rozwiązaniem, jeśli aplikacji lokalnej nie wymaga uwierzytelniania użytkowników, ale chcesz dodać usługę Azure Active Directory jako warstwę zabezpieczeń dla dostępu zdalnego. 

## <a name="password-based-sign-on"></a>Na podstawie hasła logowania jednokrotnego

Jeśli chcesz używać usługi Azure Active Directory jako magazynu haseł dla lokalnych aplikacji, wybierz **opartego na hasłach logowania jednokrotnego**. Ta opcja jest dobrym rozwiązaniem, jeśli aplikacja jest uwierzytelniany w usłudze kombi nazwy użytkownika i hasła zamiast tokenów dostępu lub nagłówków. Z opartego na hasłach logowania jednokrotnego użytkownicy potrzebują logować się do czasu aplikacji pierwszy uzyskiwania dostępu do. Po wykonaniu tej usługi Azure Active Directory udostępnia nazwy użytkownika i hasła w imieniu użytkownika. 

Aby uzyskać informacji o ustawieniach opartego na hasłach logowania, zobacz [hasło vaulting dla rejestracji jednokrotnej z serwerem Proxy aplikacji](application-proxy-configure-single-sign-on-password-vaulting.md).

## <a name="linked-sign-on"></a>Połączonego logowania jednokrotnego

Jeśli masz już jedno rozwiązanie logowania jednokrotnego dla tożsamości użytkownika lokalnego, wybierz **połączonego logowania jednokrotnego**. Ta opcja umożliwia usłudze Azure Active Directory korzystać z istniejących rozwiązań logowania jednokrotnego, ale nadal daje użytkownikom zdalny dostęp do aplikacji. 

Informacje o połączonego logowania jednokrotnego (wcześniej znany jako istniejących logowania jednokrotnego), zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="integrated-windows-authentication"></a>Zintegrowane uwierzytelnianie systemu Windows

Jeśli aplikacji lokalnych użyć zintegrowanego Authentication(IWA) systemu Windows lub jeśli chcesz użyć delegowanie ograniczone protokołu Kerberos (KCD) dla logowania jednokrotnego, wybierz **zintegrowane uwierzytelnianie systemu Windows**. Po wybraniu tej opcji użytkownicy potrzebują tylko do uwierzytelniania usługi Azure Active Directory, a następnie łącznika serwera Proxy aplikacji personifikuje użytkownika, aby uzyskać token protokołu Kerberos i zaloguj się do aplikacji. 

Aby uzyskać informacje o konfigurowaniu zintegrowane uwierzytelnianie systemu Windows, zobacz [ograniczone delegowanie protokołu Kerberos do logowania jednokrotnego przy użyciu serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="header-based-sign-on"></a>Na podstawie nagłówka logowania jednokrotnego 

Użycie aplikacji nagłówki uwierzytelniania, wybierz **na podstawie nagłówka logowania jednokrotnego**. Po wybraniu tej opcji użytkownicy potrzebują tylko do uwierzytelniania usługi Azure Active Directory. Partnerzy firmy Microsoft z usługą uwierzytelniania innej firmy o nazwie PingAccess, który translacji na format nagłówka dla aplikacji token dostępu usługi Azure Active Directory. 

Informacje dotyczące konfigurowania uwierzytelniania na podstawie nagłówka, zobacz [nagłówka uwierzytelniania dla logowania jednokrotnego przy użyciu serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-ping-access.md).

## <a name="next-steps"></a>Kolejne kroki

- [Hasło vaulting dla logowania jednokrotnego przy użyciu serwera Proxy aplikacji](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Ograniczone delegowanie protokołu Kerberos do logowania jednokrotnego przy użyciu serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)
- [Nagłówek uwierzytelniania dla logowania jednokrotnego przy użyciu serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-ping-access.md) 
