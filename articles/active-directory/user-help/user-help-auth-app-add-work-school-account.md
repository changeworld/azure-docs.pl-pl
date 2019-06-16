---
title: Dodaj swoje konto służbowe lub szkolne do aplikacji Microsoft Authenticator — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak dodać swoje konto służbowe do aplikacji Microsoft Authenticator podczas weryfikacji two-Factor Authentication.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: 231f3c44344f4119bab6e1efb5546e6fded0c784
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60473875"
---
# <a name="add-your-work-or-school-account"></a>Dodaj swoje konto służbowe
Jeśli Twoja organizacja używa weryfikacji two-Factor Authentication, można ustawić konto służbowe, korzystać z aplikacji Microsoft Authenticator jako jednej z metod weryfikacji.

>[!Important]
>Przed dodaniem konta należy pobrać i zainstalować aplikację Microsoft Authenticator. Jeśli użytkownik jeszcze nie, wykonaj kroki opisane w [pobrać i zainstalować aplikację](user-help-auth-app-download-install.md) artykułu.

## <a name="add-your-work-or-school-account"></a>Dodaj swoje konto służbowe

1. Na komputerze, przejdź do [dodatkowej weryfikacji zabezpieczeń](https://aka.ms/mfasetup) strony.

    >[!Note]
    >Jeśli nie widzisz **dodatkowej weryfikacji zabezpieczeń** strony, jest możliwe, że administrator wyłączył zabezpieczeń środowiska informacje (wersja zapoznawcza). Jeśli tak jest rzeczywiście powinien postępuj zgodnie z instrukcjami w [ustawić informacje zabezpieczające, aby użyć aplikacji authenticator](security-info-setup-auth-app.md) sekcji. Jeśli nie jest to przypadek, konieczne będzie skontaktuj się z działem pomocy technicznej w Twojej organizacji, aby uzyskać pomoc. Aby uzyskać więcej informacji na temat informacji zabezpieczających, zobacz [zabezpieczeniami informacji (wersja zapoznawcza) — omówienie](user-help-security-info-overview.md).

2. Zaznacz pole wyboru obok pozycji **aplikacji Authenticator**, a następnie wybierz pozycję **Konfiguruj**.

    **Konfiguracja aplikacji mobilnej** zostanie wyświetlona strona.
    
    ![Ekran, który zawiera kod QR](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. Otwórz aplikację Microsoft Authenticator, wybierz opcję **Dodaj konto** z **dostosowywanie i sterowania** ikonę w prawym górnym rogu, a następnie wybierz **pracy konta służbowego**.

4. Umożliwia skanowanie kodu QR za pomocą aparatu urządzenia **Konfiguracja aplikacji mobilnej** ekranu na komputerze, a następnie wybierz **gotowe**.

    >[!Note]
    >Jeśli aparat fotograficzny nie może przechwytywać kod QR, można ręcznie dodawać informacje o Twoim koncie do aplikacji Microsoft Authenticator podczas weryfikacji two-Factor Authentication. Aby uzyskać więcej informacji i jak to zrobić, zobacz [ręcznie dodać konto](user-help-auth-app-add-account-manual.md).

5. Przegląd **kont** ekranu do aplikacji na urządzeniu, aby upewnić się, że Twoje konto jest po prawej stronie i że jest skojarzony 6 cyfrowy kod weryfikacyjny. Dla dodatkowego bezpieczeństwa kod weryfikacyjny zmienia co 30 sekund, uniemożliwiając ktoś przy użyciu kodu wiele razy.

    ![ekran kont](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>Kolejne kroki

- Po dodaniu konta do aplikacji, można zalogować się przy użyciu aplikacji Authenticator na urządzeniu. Aby uzyskać więcej informacji, zobacz [Zaloguj się przy użyciu aplikacji](user-help-auth-app-sign-in.md).

- Dla urządzeń z systemem iOS można również wykonać kopię zapasową poświadczeń konta usługi i powiązane ustawienia aplikacji, takie jak kolejność Twoje konta z chmurą. Aby uzyskać więcej informacji, zobacz [tworzenia kopii zapasowych i odzyskiwanie przy użyciu aplikacji Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
