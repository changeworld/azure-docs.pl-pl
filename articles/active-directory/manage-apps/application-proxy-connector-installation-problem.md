---
title: Problem podczas instalowania łącznika agenta serwera Proxy aplikacji | Dokumentacja firmy Microsoft
description: Jak rozwiązywać problemy, które może występować podczas instalowania łącznika agenta serwera Proxy aplikacji
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
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c82bba6ccb1eaa1933176362e34b8c3e30c37f8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65783635"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Problem z instalacją łącznika agenta serwera proxy aplikacji

Łącznik serwera Proxy aplikacji usługi AAD firmy Microsoft jest składnikiem domeny wewnętrznej, który używa połączeń wychodzących w celu ustanowienia łączności z końcowego dostępne w chmurze do domeny wewnętrznej.

## <a name="general-problem-areas-with-connector-installation"></a>Ogólne obszarów problemów z instalacją łącznika

Podczas instalacji łącznika nie powiedzie się, przyczyny to zwykle spowodowane jednym z następujących obszarów:

1.  **Łączność** — w celu ukończenia pomyślnej instalacji nowych potrzeb łącznika do rejestrowania i ustanowienia zaufania przyszłych właściwości. Odbywa się przez połączenie z usługą w chmurze serwer Proxy aplikacji usługi AAD.

2.  **Tworzenie relacji zaufania** — nowy łącznik tworzy certyfikatu z podpisem własnym i rejestruje do usługi w chmurze.

3.  **Uwierzytelnianie administrator** — podczas instalacji, użytkownik musi podać poświadczenia administratora, aby ukończyć instalację łącznika.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Sprawdź łączność z usługi Serwer Proxy aplikacji w chmurze i strony Login firmy Microsoft

**Cel:** Sprawdź, czy na maszynie łącznika może połączyć się punkt końcowy rejestracji serwera Proxy aplikacji usługi AAD, a także strony logowania firmy Microsoft.

1.  Otwórz przeglądarkę i przejdź do następującej stronie sieci web: <https://aadap-portcheck.connectorporttest.msappproxy.net> i sprawdź, czy działa łączność środkowe stany USA i wschodnie stany USA centrach danych z portów 80 i 443.

2.  Jeśli dowolny z tych portów nie powiedzie się (nie ma zielony znacznik wyboru), sprawdź, czy serwer proxy zapory lub wewnętrznej bazy danych ma \*. msappproxy.net z portów 80 i 443 poprawnie zdefiniowany.

3.  Otwórz w przeglądarce (osobnej karcie) i przejdź do następującej stronie sieci web: <https://login.microsoftonline.com>, upewnij się, że możesz zalogować się do tej strony.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>Upewnij się, że składniki maszyny i wewnętrznej bazy danych, obsługa certyfikatu relacji zaufania serwera Proxy aplikacji

**Cel:** Sprawdź, czy certyfikat utworzony przez łącznik dla przyszłych zaufania może obsługiwać maszynie łącznika, wewnętrznej bazy danych serwera proxy i zapory.

>[!NOTE]
>Łącznik próbuje utworzyć certyfikat SHA512, która jest obsługiwana przez TLS1.2. Jeśli komputer lub zaporę wewnętrznej bazy danych i serwera proxy nie obsługuje TLS1.2, instalacja się nie powieść.
>
>

**Aby rozwiązać ten problem:**

1.  Sprawdź komputer obsługuje TLS1.2 — Windows wszystkie wersje po 2012 R2 powinny obsługiwać protokół TLS 1.2. Jeśli maszyna łącznik jest w wersji 2012 R2 lub starszy, upewnij się, że następujących artykułów bazy wiedzy są zainstalowane na komputerze: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Skontaktuj się z administratorem sieci i poproś sprawdzić, czy wewnętrznej bazy danych serwera proxy i zapory nie blokują SHA512 dla ruchu wychodzącego.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Sprawdź, czy administrator służy do instalowania łącznika

**Cel:** Sprawdź, czy użytkownik, który próbuje zainstalować łącznik jest administrator z prawidłowymi poświadczeniami. Obecnie użytkownik musi być co najmniej administratora aplikacji do zainstalowania zakończyło się sukcesem.

**Aby sprawdzić, czy poświadczenia są prawidłowe:**

Połączyć się z <https://login.microsoftonline.com> i używać tych samych poświadczeń. Upewnij się, że użytkownik zaloguje się ponownie. Rolę użytkownika można sprawdzić, przechodząc do **usługi Azure Active Directory**  - &gt; **użytkowników i grup**  - &gt; **wszyscy użytkownicy**. 

Wybierz konto użytkownika, następnie "Rola katalogu" w menu wynikowe. Sprawdź, czy wybraną rolę "Administrator aplikacji". Jeśli nie możesz uzyskać dostępu do żadnych stron wzdłuż tych kroków, nie masz wymaganych ról.

## <a name="next-steps"></a>Kolejne kroki
[Omówienie łączników serwera Proxy aplikacji usługi Azure AD](application-proxy-connectors.md)
