---
title: Problem z instalacją łącznika agenta proxy aplikacji | Dokumenty firmy Microsoft
description: Jak rozwiązywać problemy, które mogą wystąpić podczas instalowania łącznika agenta agenta proxy aplikacji
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
ms.openlocfilehash: 466e1ce0efbdec3f5475634f3857d02554d93d98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049132"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Problem z instalacją łącznika agenta serwera proxy aplikacji

Łącznik serwera proxy aplikacji AAD firmy Microsoft to wewnętrzny składnik domeny, który używa połączeń wychodzących do ustanawiania łączności z dostępnego punktu końcowego w chmurze z domeną wewnętrzną.

## <a name="general-problem-areas-with-connector-installation"></a>Ogólne obszary problemowe z instalacją łącznika

Gdy instalacja łącznika nie powiedzie się, główną przyczyną jest zwykle jeden z następujących obszarów:

1.  **Łączność** — aby zakończyć pomyślną instalację, nowy łącznik musi zarejestrować i ustanowić przyszłe właściwości zaufania. Odbywa się to przez połączenie z usługą serwera proxy aplikacji AAD w chmurze.

2.  **Trust Establishment** — nowy łącznik tworzy certyfikat z podpisem własnym i rejestruje się w usłudze w chmurze.

3.  **Uwierzytelnianie administratora** — podczas instalacji użytkownik musi podać poświadczenia administratora, aby ukończyć instalację łącznika.

> [!NOTE]
> Dzienniki instalacji łącznika można znaleźć w folderze %TEMP% i mogą pomóc w dostarczeniu dodatkowych informacji na temat przyczyn awarii instalacji.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Weryfikowanie łączności z usługą serwera proxy aplikacji w chmurze i stroną Microsoft Login

**Cel:** Sprawdź, czy komputer łącznika może łączyć się z punktem końcowym rejestracji serwera proxy aplikacji AAD, a także ze stroną logowania firmy Microsoft.

1.  Na serwerze łączników uruchom test portu za pomocą [usługi telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) lub innego narzędzia do testowania portów w celu sprawdzenia, czy porty 443 i 80 są otwarte.

2.  Jeśli którykolwiek z tych portów nie powiedzie się, sprawdź, czy serwer proxy zapory lub wewnętrznej bazy danych ma dostęp do wymaganych domen i portów zobacz, [Przygotuj środowisko lokalne](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

3.  Otwórz przeglądarkę (oddzielną kartę) i przejdź `https://login.microsoftonline.com`do następującej strony internetowej: , upewnij się, że możesz zalogować się do tej strony.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>Weryfikowanie obsługi składników komputera i zaplecza dla certyfikatu zaufania serwera proxy aplikacji

**Cel:** Sprawdź, czy komputer łącznika, serwer proxy wewnętrznej bazy danych i zapora mogą obsługiwać certyfikat utworzony przez łącznik dla przyszłego zaufania.

>[!NOTE]
>Łącznik próbuje utworzyć certyfikat SHA512, który jest obsługiwany przez TLS1.2. Jeśli komputer lub zapora wewnętrznej bazy danych i serwer proxy nie obsługuje protokołu TLS1.2, instalacja nie powiedzie się.
>
>

**Aby rozwiązać ten problem:**

1.  Sprawdź, czy urządzenie obsługuje protokół TLS1.2 — wszystkie wersje systemu Windows po 2012 R2 powinny obsługiwać protokół TLS 1.2. Jeśli urządzenie złącza pochodzi z wersji 2012 R2 lub wcześniejszej, upewnij się, że na urządzeniu są zainstalowane następujące kb:<https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Skontaktuj się z administratorem sieci i poproś o sprawdzenie, czy serwer proxy i zapora wewnętrznej bazy danych nie blokują sha512 dla ruchu wychodzącego.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Sprawdź, czy administrator jest używany do zainstalowania łącznika

**Cel:** Sprawdź, czy użytkownik, który próbuje zainstalować łącznik, jest administratorem z poprawnymi poświadczeniami. Obecnie użytkownik musi być co najmniej administratorem aplikacji, aby instalacja powiodła się.

**Aby sprawdzić, czy poświadczenia są poprawne:**

Połącz `https://login.microsoftonline.com` się z tymi samymi poświadczeniami i użyj ich. Upewnij się, że logowanie zakończyło się pomyślnie. Możesz sprawdzić rolę użytkownika, przechodząc do **usługi Azure Active Directory Użytkownicy**  - &gt; **i grupy**  - &gt; **Wszyscy użytkownicy**. 

Wybierz konto użytkownika, a następnie "Rola katalogu" w menu wynikowym. Sprawdź, czy wybrana rola to "Administrator aplikacji". Jeśli nie możesz uzyskać dostępu do żadnej ze stron w tych krokach, nie masz wymaganej roli.

## <a name="next-steps"></a>Następne kroki
[Opis łączników serwera proxy aplikacji usługi Azure AD](application-proxy-connectors.md)
