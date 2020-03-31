---
title: Dodawanie konta służbowego do aplikacji Microsoft Authenticator — Azure AD
description: Dodaj swoje konto służbowe do aplikacji Microsoft Authenticator, aby zweryfikować swoją tożsamość podczas korzystania z weryfikacji dwuskładnikowej.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: f0cc14a53f7ead7f0a496728d477d7d30857a0fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063921"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Dodawanie konta służbowego do aplikacji Microsoft Authenticator

Jeśli twoja organizacja korzysta z weryfikacji dwuskładnikowej, możesz skonfigurować konto służbowe, aby używać aplikacji Microsoft Authenticator jako jednej z metod weryfikacji.

>[!Important]
>Aby można było dodać konto, należy pobrać i zainstalować aplikację Microsoft Authenticator. Jeśli jeszcze tego nie zrobiono, wykonaj czynności opisane w artykule [Pobierz i zainstaluj aplikację.](user-help-auth-app-download-install.md)

## <a name="add-your-work-or-school-account"></a>Dodawanie konta służbowego

1. Na komputerze przejdź do strony [Dodatkowa weryfikacja zabezpieczeń.](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)

    >[!Note]
    >Jeśli nie widzisz strony **Dodatkowa weryfikacja zabezpieczeń,** możliwe, że administrator włączył środowisko informacji zabezpieczających (w wersji zapoznawczej). W takim przypadku należy postępować zgodnie z instrukcjami w sekcji [Konfigurowanie informacji zabezpieczających, aby używać aplikacji uwierzytelniającego.](security-info-setup-auth-app.md) Jeśli tak nie jest, należy skontaktować się z działem pomocy technicznej organizacji w celu uzyskania pomocy. Aby uzyskać więcej informacji o informacjach zabezpieczających, zobacz [Omówienie informacji o zabezpieczeniach (wersja zapoznawcza).](user-help-security-info-overview.md)

2. Zaznacz pole wyboru obok **aplikacji Authenticator**, a następnie wybierz pozycję **Konfiguruj**.

    Zostanie wyświetlona strona **Konfigurowanie aplikacji mobilnej.**

    ![Ekran, który zapewnia kod QR](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. Otwórz aplikację Microsoft Authenticator, wybierz pozycję **Dodaj konto** z **ikony Dostosuj i kontroluj** w prawym górnym rogu, a następnie wybierz **pozycję Konto służbowe.**

    >[!Note]
    >Jeśli po raz pierwszy konfigurujesz aplikację Microsoft Authenticator, może pojawić się monit z pytaniem, czy zezwolić aplikacji na dostęp do aparatu (iOS), czy zezwolić aplikacji na robienia zdjęć i nagrywania wideo (Android). Musisz wybrać **opcję Zezwalaj,** aby aplikacja uwierzytelniającego mogła uzyskać dostęp do aparatu, aby zrobić zdjęcie kodu QR w następnym kroku. Jeśli nie zezwolisz na aparat, nadal możesz skonfigurować aplikację uwierzytelniacza, ale musisz ręcznie dodać informacje o kodzie. Aby uzyskać informacje dotyczące ręcznego dodawania kodu, zobacz [Ręczne dodawanie konta do aplikacji](user-help-auth-app-add-account-manual.md).

4. Użyj kamery urządzenia, aby zeskanować kod QR z ekranu **Konfigurowanie aplikacji mobilnej** na komputerze, a następnie wybierz pozycję **Gotowe**.

    >[!Note]
    >Jeśli aparat nie może przechwycić kodu QR, możesz ręcznie dodać informacje o koncie do aplikacji Microsoft Authenticator w celu weryfikacji dwuskładnikowej. Aby uzyskać więcej informacji i jak to zrobić, zobacz [Ręczne dodawanie konta](user-help-auth-app-add-account-manual.md).

5. Przejrzyj ekran **Konta** aplikacji na urządzeniu, aby upewnić się, że twoje konto ma rację i że istnieje skojarzony sześciocyfrowy kod weryfikacyjny. Aby uzyskać dodatkowe bezpieczeństwo, kod weryfikacyjny zmienia się co 30 sekund, uniemożliwiając innej osobie wielokrotne używanie kodu.

    ![Ekran Konta](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>Następne kroki

- Po dodaniu kont do aplikacji możesz zalogować się za pomocą aplikacji Authenticator na urządzeniu. Aby uzyskać więcej informacji, zobacz [Logowanie się przy użyciu aplikacji](user-help-auth-app-sign-in.md).

- W przypadku urządzeń z systemem iOS można również w chmurze uzyskać zapas y poświadczeń konta i powiązanych ustawień aplikacji, takich jak kolejność kont. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych i odzyskiwanie za pomocą aplikacji Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
