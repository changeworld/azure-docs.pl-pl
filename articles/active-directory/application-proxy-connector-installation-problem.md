---
title: Problem podczas instalowania łącznika agenta serwera Proxy aplikacji | Dokumentacja firmy Microsoft
description: Jak rozwiązywać problemy, które może występować podczas instalowania łącznika agenta serwera Proxy aplikacji
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 6d0eb2e816e39a92bf895842d570587ec5385f1a
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366148"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Problem podczas instalowania łącznika agenta serwera Proxy aplikacji

Łącznik serwera Proxy aplikacji usługi AAD firmy Microsoft jest składnikiem domeny wewnętrznej, który używa połączeń wychodzących w celu ustanowienia łączności z końcowego dostępne w chmurze do domeny wewnętrznej.

## <a name="general-problem-areas-with-connector-installation"></a>Ogólne obszarów problemów z instalacją łącznika

Podczas instalacji łącznika nie powiedzie się, przyczyny to zwykle spowodowane jednym z następujących obszarów:

1.  **Łączność** — w celu ukończenia pomyślnej instalacji nowych potrzeb łącznika do rejestrowania i ustanowienia zaufania przyszłych właściwości. Odbywa się przez połączenie z usługą w chmurze serwer Proxy aplikacji usługi AAD.

2.  **Tworzenie relacji zaufania** — nowy łącznik tworzy certyfikatu z podpisem własnym i rejestruje do usługi w chmurze.

3.  **Uwierzytelnianie administrator** — podczas instalacji, użytkownik musi podać poświadczenia administratora, aby ukończyć instalację łącznika.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Sprawdź łączność z usługi Serwer Proxy aplikacji w chmurze i strony Login firmy Microsoft

**Cel:** upewnij się, że na maszynie łącznika można połączyć się punkt końcowy rejestracji serwera Proxy aplikacji usługi AAD, a także strony logowania firmy Microsoft.

1.  Otwórz przeglądarkę i przejdź do następującej stronie sieci web: <https://aadap-portcheck.connectorporttest.msappproxy.net> i sprawdź, czy działa łączność środkowe stany USA i wschodnie stany USA centrach danych z portów 80 i 443.

2.  Jeśli dowolny z tych portów nie powiedzie się (nie ma zielony znacznik wyboru), sprawdź, czy serwer proxy zapory lub wewnętrznej bazy danych ma \*. msappproxy.net z portów 80 i 443 poprawnie zdefiniowany.

3.  Otwórz w przeglądarce (osobnej karcie) i przejdź do następującej stronie sieci web: <https://login.microsoftonline.com>, upewnij się, że możesz zalogować się do tej strony.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>Upewnij się, że składniki maszyny i wewnętrznej bazy danych, obsługa certyfikatu relacji zaufania serwera Proxy aplikacji

**Cel:** Sprawdź, czy certyfikat utworzony przez łącznik dla przyszłych zaufania można obsługiwać maszynie łącznika, wewnętrznej bazy danych serwera proxy i zapory.

>[!NOTE]
>Łącznik próbuje utworzyć certyfikat SHA512, która jest obsługiwana przez TLS1.2. Jeśli komputer lub zaporę wewnętrznej bazy danych i serwera proxy nie obsługuje TLS1.2, instalacja się nie powieść.
>
>

**Aby rozwiązać ten problem:**

1.  Sprawdź komputer obsługuje TLS1.2 — Windows wszystkie wersje po 2012 R2 powinny obsługiwać protokół TLS 1.2. Jeśli maszyna łącznik jest w wersji 2012 R2 lub starszy, upewnij się, że następujących artykułów bazy wiedzy są zainstalowane na komputerze: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Skontaktuj się z administratorem sieci i poproś sprawdzić, czy wewnętrznej bazy danych serwera proxy i zapory nie blokują SHA512 dla ruchu wychodzącego.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Sprawdź, czy administrator służy do instalowania łącznika

**Cel:** Sprawdź, czy administrator z prawidłowymi poświadczeniami użytkownika, który próbuje zainstalować łącznik. Obecnie użytkownik musi być administratorem globalnym dla instalacji zakończyło się sukcesem.

**Aby sprawdzić, czy poświadczenia są prawidłowe:**

Połączyć się z <https://login.microsoftonline.com> i używać tych samych poświadczeń. Upewnij się, że użytkownik zaloguje się ponownie. Rolę użytkownika można sprawdzić, przechodząc do **usługi Azure Active Directory**  - &gt; **użytkowników i grup**  - &gt; **wszyscy użytkownicy**. 

Wybierz konto użytkownika, następnie "Rola katalogu" w menu wynikowe. Sprawdź, czy wybraną rolę "Administrator globalny". Jeśli nie możesz uzyskać dostępu do żadnych stron wzdłuż tych kroków nie jesteś administratorem globalnym.

## <a name="next-steps"></a>Kolejne kroki
[Omówienie łączników serwera Proxy aplikacji usługi Azure AD](manage-apps/application-proxy-connectors.md)
