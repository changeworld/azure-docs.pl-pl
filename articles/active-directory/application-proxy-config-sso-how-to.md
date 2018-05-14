---
title: Jak skonfigurować logowanie jednokrotne do aplikacji serwera Proxy aplikacji | Dokumentacja firmy Microsoft
description: Jak można skonfigurować logowanie jednokrotne do aplikacji serwera proxy aplikacji szybko
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 09d17f9f70ef352fe8d44a16c0f78323153a8a1a
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Jak skonfigurować logowanie jednokrotne do aplikacji serwera Proxy aplikacji

Logowanie jednokrotne (SSO) umożliwia użytkownikom dostęp do aplikacji bez uwierzytelniania wiele razy. Umożliwia pojedynczego uwierzytelniania w chmurze, do usługi Azure Active Directory oraz zezwala łącznika do personifikacji użytkownika, aby zakończyć wszystkie problemy dodatkowego uwierzytelniania z aplikacji lub usługi.

## <a name="how-to-configure-single-sign-on"></a>Jak skonfigurować jednokrotnego na
Aby skonfigurować logowanie Jednokrotne, należy najpierw upewnić się, że aplikacja jest skonfigurowana do uwierzytelniania wstępnego za pomocą usługi Azure Active Directory. Aby wykonać tę konfigurację, przejdź do **usługi Azure Active Directory**  - &gt; **aplikacje dla przedsiębiorstw**  - &gt; **wszystkie aplikacje**   - &gt; Aplikacji  **- &gt; serwera Proxy aplikacji**. Na tej stronie można znaleźć w polu "Wstępne uwierzytelnianie" i upewnij się, że ma ustawioną wartość "Azure Active Directory. 

Aby uzyskać więcej informacji na temat metod uwierzytelniania wstępnego, zobacz krok 4 [dokumentu publikowania aplikacji](manage-apps/application-proxy-publish-azure-portal.md).

   ![Metoda uwierzytelniania wstępnego w portalu Azure](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Konfigurowanie trybów rejestracji jednokrotnej dla aplikacji serwera Proxy aplikacji
Skonfiguruj typ logowania jednokrotnego. Metody logowania są klasyfikowane, oparte na korzysta z jakiego typu uwierzytelniania aplikacji zaplecza. Aplikacje serwera Proxy aplikacji obsługuje trzy rodzaje rejestracji:

-   **Na podstawie hasła logowania jednokrotnego**: opartego na hasłach logowania może służyć do dowolnej aplikacji, która korzysta z pola Nazwa użytkownika i hasło do logowania. Czynności konfiguracyjne znajdują się w [dokumentacji konfiguracji logowania jednokrotnego hasła](active-directory-enterprise-apps-whats-new-azure-portal.md#bring-your-own-password-sso-applications).

-   **Zintegrowane uwierzytelnianie systemu Windows**: w przypadku aplikacji przy użyciu zintegrowanego uwierzytelniania systemu Windows (IWA) rejestracji jednokrotnej jest włączona za pośrednictwem protokołu Kerberos ograniczonego delegowania (KCD). Ta metoda zapewnia uprawnienia łączniki serwera Proxy aplikacji w usłudze Active Directory umożliwiające personifikowanie użytkowników i do wysyłania i odbierania tokenów w ich imieniu. Szczegółowe informacje na temat konfigurowania KCD znajdują się w [rejestracji jednokrotnej z dokumentacją KCD](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md).

-   **Na podstawie nagłówka logowania jednokrotnego**: na podstawie nagłówka logowania jest włączona za pośrednictwem powiązania i wymagać dodatkowej konfiguracji. Aby uzyskać szczegółowe informacje o współpracy i instrukcje krok po kroku dotyczące konfigurowania rejestracji jednokrotnej do aplikacji, która używa nagłówki uwierzytelniania, zobacz [PingAccess dokumentacji usługi Azure AD](application-proxy-ping-access.md).

Każdą z tych opcji można znaleźć, przechodząc do aplikacji w "Aplikacje przedsiębiorstwa" i otwieranie **rejestracji jednokrotnej** strony w menu po lewej stronie. należy pamiętać, że jeśli aplikacja została utworzona w starego portalu, mogą nie być widoczne wszystkie te opcje.

Na tej stronie zostanie również wyświetlony jedną dodatkową opcję logowania jednokrotnego: połączonego logowania jednokrotnego. Ta opcja jest również obsługiwana przez serwer Proxy aplikacji. Jednak ta opcja nie dodaje logowania jednokrotnego do aplikacji. Inaczej mówiąc, że aplikacja może już istnieć rejestracji jednokrotnej implementowane za pomocą innej usługi, takie jak Active Directory Federation Services. 

Ta opcja umożliwia utworzenie tego ziemi pierwszy użytkowników łącza do aplikacji na podczas uzyskiwania dostępu do aplikacji administrator. Na przykład w przypadku aplikacji, która jest skonfigurowana do uwierzytelniania użytkowników za pomocą Active Directory Federation Services 2.0, administrator może użyć opcji "połączonego logowania jednokrotnego" Aby utworzyć łącze do niego w panelu dostępu.

## <a name="next-steps"></a>Kolejne kroki
[Podaj logowanie jednokrotne do aplikacji przy użyciu serwera Proxy aplikacji](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
