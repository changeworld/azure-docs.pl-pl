---
title: Problem z instalowaniem łącznika agenta serwera proxy aplikacji | Microsoft Docs
description: Jak rozwiązywać problemy, które można napotkać podczas instalowania łącznika agenta serwera proxy aplikacji
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
ms.openlocfilehash: a35558b81d064680981bcf403a3584e3a3d00e4f
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311741"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Problem z instalacją łącznika agenta serwera proxy aplikacji

Łącznik serwera proxy aplikacji usługi Microsoft AAD to wewnętrzny składnik domeny, który używa połączeń wychodzących do nawiązywania łączności z punktu końcowego dostępnego w chmurze do domeny wewnętrznej.

## <a name="general-problem-areas-with-connector-installation"></a>Ogólne obszary problemów z instalacją łącznika

Gdy instalacja łącznika nie powiedzie się, główną przyczyną jest zazwyczaj jeden z następujących obszarów:

1.  **Łączność** — aby ukończyć pomyślną instalację, nowy łącznik musi zarejestrować i ustanowić przyszłe właściwości zaufania. W tym celu należy nawiązać połączenie z usługą serwera proxy aplikacji usługi AAD.

2.  **Ustanowienie zaufania** — nowy łącznik tworzy certyfikat z podpisem własnym i rejestruje usługę w chmurze.

3.  **Uwierzytelnianie administratora** — w trakcie instalacji użytkownik musi podać poświadczenia administratora, aby zakończyć instalację łącznika.

> [!NOTE]
> Dzienniki instalacji łącznika znajdują się w folderze% TEMP% i mogą pomóc w określeniu dodatkowych informacji na temat tego, co powoduje błąd instalacji.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Weryfikowanie łączności z usługą serwera proxy aplikacji w chmurze i stroną logowania firmy Microsoft

**Cel:** Sprawdź, czy komputer łącznika może nawiązać połączenie z punktem końcowym rejestracji serwera proxy aplikacji usługi AAD, a także stroną logowania firmy Microsoft.

1.  Na serwerze łącznika Uruchom test portu przy użyciu programu [Telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) lub innego narzędzia do testowania portów, aby sprawdzić, czy porty 443 i 80 są otwarte.

2.  Jeśli którykolwiek z tych portów nie powiedzie się, sprawdź, czy zapora lub serwer proxy zaplecza ma dostęp do wymaganych domen i portów, zobacz [Przygotowywanie środowiska lokalnego](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

3.  Otwórz przeglądarkę (osobna karta) i przejdź do następującej strony sieci Web: <https://login.microsoftonline.com>, upewnij się, że możesz zalogować się na tej stronie.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>Sprawdź, czy maszyny i składniki zaplecza obsługują certyfikat zaufania serwera proxy aplikacji

**Cel:** Sprawdź, czy maszyna łącznika, serwer proxy zaplecza i Zapora mogą obsługiwać certyfikat utworzony przez łącznik na potrzeby przyszłego zaufania.

>[!NOTE]
>Łącznik próbuje utworzyć certyfikat SHA512, który jest obsługiwany przez protokół TLS 1.2. Jeśli maszyna lub Zapora zaplecza i serwer proxy nie obsługują protokołu TLS 1.2, instalacja nie powiedzie się.
>
>

**Aby rozwiązać ten problem:**

1.  Sprawdź, czy maszyna obsługuje protokół TLS 1.2 — wszystkie wersje systemu Windows po 2012 R2 powinny obsługiwać protokół TLS 1,2. Jeśli komputer łącznika pochodzi z wersji 2012 R2 lub starszej, upewnij się, że na maszynie jest zainstalowany następujący artykułów bazy wiedzy: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Skontaktuj się z administratorem sieci i poproś o zweryfikowanie, czy serwer proxy zaplecza i Zapora nie blokują SHA512 dla ruchu wychodzącego.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Sprawdź, czy administrator służy do instalowania łącznika

**Cel:** Sprawdź, czy użytkownik, który próbuje zainstalować łącznik, jest administratorem z prawidłowymi poświadczeniami. Obecnie aby instalacja się powiodła, użytkownik musi być co najmniej administratorem aplikacji.

**Aby sprawdzić, czy poświadczenia są poprawne:**

Połącz się z <https://login.microsoftonline.com> i Użyj tych samych poświadczeń. Upewnij się, że logowanie zakończyło się pomyślnie. Rolę użytkownika można sprawdzić, przechodząc do **Azure Active Directory** -&gt; **użytkowników i grup** -&gt; **wszystkich użytkowników**. 

Wybierz konto użytkownika, a następnie "rola katalogu" w menu wyników. Sprawdź, czy wybrana rola to "Administrator aplikacji". Jeśli nie możesz uzyskać dostępu do żadnych stron w ramach tych kroków, nie masz wymaganej roli.

## <a name="next-steps"></a>Następne kroki
[Omówienie łączników serwera Proxy aplikacji usługi Azure AD](application-proxy-connectors.md)
