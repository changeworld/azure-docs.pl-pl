---
title: Zaawansowane opcje w tokenie SAML wstępnie zintegrowanych aplikacji w usłudze Azure Active Directory podpisywanie certyfikatów | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać zaawansowanych certyfikatów podpisywania opcje w tokenie SAML wstępnie zintegrowanych aplikacji w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: barbkess
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 245e3d32bc546ae6f725ac6f7eb549d77119c956
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55191520"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Zaawansowane opcje w tokenie SAML dla aplikacji z galerii usługi Azure Active Directory podpisywania certyfikatu
Już dzisiaj usługi Azure Active Directory (Azure AD) obsługuje tysiące wstępnie zintegrowanych aplikacji w galerii aplikacji usługi Azure Active Directory. Liczba ta obejmuje więcej niż 500 aplikacje, które obsługuje logowanie jednokrotne przy użyciu protokołu SAML 2.0. Jeśli użytkownik uwierzytelnia się do aplikacji za pomocą usługi Azure AD przy użyciu protokołu SAML, usługi Azure AD wysyła token do aplikacji (za pośrednictwem metody POST protokołu HTTP). Następnie aplikacja sprawdza poprawność i używa tokenu logowania użytkownika zamiast monitowania o nazwę użytkownika i hasło. Te tokeny SAML są podpisane za pomocą unikatowy certyfikat, który jest generowany w usłudze Azure AD i określonych standardowych algorytmów.

Usługa Azure AD używa niektóre z domyślnych ustawień dla aplikacji w galerii. Wartości domyślne są konfigurowane w zależności od wymagań aplikacji.

Usługa Azure AD obsługuje zaawansowane ustawienia podpisywania certyfikatów. Aby wybrać te opcje, najpierw wybierz **Pokaż zaawansowane ustawienia podpisywania certyfikatów** pole wyboru:

![Pokaż zaawansowane ustawienia podpisywania certyfikatów ](./media/certificate-signing-options/saml-advance-certificate.png)

Po zaznaczeniu tego pola wyboru, możesz skonfigurować opcje podpisywania certyfikatu i certyfikatu algorytm podpisywania.

## <a name="certificate-signing-options"></a>Opcje podpisywania certyfikatów

Usługa Azure AD obsługuje trzy opcje certyfikatów do podpisywania:

* **Zaloguj się potwierdzenie SAML**. Ta opcja domyślna jest ustawiona dla większości aplikacji galerii. Jeśli ta opcja jest zaznaczona, usługi Azure AD jako dostawcy tożsamości podpisuje potwierdzenie SAML i certyfikat X509 certyfikatu aplikacji. Ponadto, używa algorytmu podpisywania, który wybrano w **algorytmu podpisywania** listy rozwijanej.

* **Zaloguj się odpowiedzi SAML**. Jeśli ta opcja jest zaznaczona, usługi Azure AD jako dostawcy tożsamości podpisuje odpowiedzi SAML X509 certyfikatu aplikacji. Ponadto, używa algorytmu podpisywania, który wybrano w **algorytmu podpisywania** listy rozwijanej.

* **Zaloguj się odpowiedź i potwierdzenie SAML**. Jeśli ta opcja jest zaznaczona, usługi Azure AD jako dostawcy tożsamości loguje całą tokenu SAML X509 certyfikatu aplikacji. Ponadto, używa algorytmu podpisywania, który wybrano w **algorytmu podpisywania** listy rozwijanej.

    ![Opcje podpisywania certyfikatów](./media/certificate-signing-options/saml-signing-options.png)

## <a name="certificate-signing-algorithms"></a>Algorytmy podpisywania certyfikatu

Usługa Azure AD obsługuje dwa algorytmy podpisywania do podpisywania SAML odpowiedzi:

* **SHA-256**. Usługa Azure AD używa to domyślny algorytm używany do podpisywania odpowiedzi protokołu SAML. Algorytm najnowsze i jest traktowany jako więcej bezpieczne niż SHA-1. Większość aplikacji obsługuje algorytm SHA-256. Jeśli aplikacja obsługuje tylko algorytm SHA-1 jako algorytm podpisywania, możesz go zmienić. W przeciwnym razie firma Microsoft zaleca używanie algorytmu SHA-256 do podpisywania odpowiedzi protokołu SAML.

    ![Certyfikatu SHA-256, algorytm podpisywania](./media/certificate-signing-options/saml-signing-algo-sha256.png)

* **SHA-1**. Jest to algorytm starszych i jest ona traktowana jako mniej bezpieczna niż SH-256. Jeśli aplikacja obsługuje tylko ten algorytm podpisywania, możesz wybrać tę opcję w **algorytmu podpisywania** listy rozwijanej. Następnie usługa Azure AD podpisuje odpowiedzi SAML przy użyciu algorytmu SHA-1.

    ![SHA-1 certyfikatu algorytm podpisywania](./media/certificate-signing-options/saml-signing-algo-sha1.png)

## <a name="next-steps"></a>Kolejne kroki
* [Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w galerii aplikacji usługi Azure Active Directory](configure-federated-single-sign-on-non-gallery-applications.md)
* [Rozwiązywanie problemów z opartej na SAML logowania jednokrotnego](../develop/howto-v1-debug-saml-sso-issues.md)


