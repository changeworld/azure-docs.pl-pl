---
title: Poprawki błędu "Nie możesz się tam dostać stąd" — usługa Azure AD
description: Znajdź potencjalne poprawki, dlaczego otrzymujesz komunikat o błędzie "Nie możesz się tam dostać stąd".
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 2484de4e554d16ba049d206981a44654ede28a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190029"
---
# <a name="possible-fixes-for-the-you-cant-get-there-from-here-error-message"></a>Możliwe poprawki komunikatu o błędzie "Nie możesz się tam dostać stąd"

Podczas uzyskiwania dostępu do wewnętrznych aplikacji internetowych lub usług internetowych organizacji może zostać wyświetlony komunikat o błędzie: **Nie możesz się tam dostać.** Ten komunikat oznacza, że organizacja wprowadziła zasady uniemożliwiające urządzeniu dostęp do zasobów organizacji. Chociaż może się okazać, że musisz skontaktować się z działem pomocy technicznej, aby rozwiązać ten problem, oto kilka rzeczy, które możesz wypróbować najpierw.

## <a name="make-sure-youre-using-a-supported-browser"></a>Upewnij się, że korzystasz z obsługiwanej przeglądarki
Jeśli pojawi się komunikat Nie możesz się tam dostać z tego **komunikatu,** że próbujesz uzyskać dostęp do witryn organizacji z nieobsługiconej przeglądarki, sprawdź, która przeglądarka jest uruchomiona.

![Komunikat o błędzie związany z obsługą przeglądarki](media/user-help-device-remediation/browser-version.png)

Aby rozwiązać ten problem, należy zainstalować i uruchomić obsługiwaną przeglądarkę na podstawie systemu operacyjnego. Jeśli używasz systemu Windows 10, obsługiwane przeglądarki obejmują przeglądarki Microsoft Edge, Internet Explorer i Google Chrome. Jeśli używasz innego systemu operacyjnego, możesz sprawdzić pełną listę [obsługiwanych przeglądarek](../conditional-access/concept-conditional-access-conditions.md#supported-browsers).

## <a name="make-sure-youre-using-a-supported-operating-system"></a>Upewnij się, że korzystasz z obsługiwanego systemu operacyjnego
Upewnij się, że jest uruchomiona obsługiwana wersja systemu operacyjnego, w tym:

- **Klient systemu Windows.** systemu Windows 7 lub nowszego.

- **Windows Server.** Windows Server 2008 R2 lub nowszy.

- **Macos.** macOS X lub nowszy

- **Android i iOS.** Najnowsza wersja mobilnych systemów operacyjnych Android i iOS

Aby rozwiązać ten problem, należy zainstalować i uruchomić obsługiwany system operacyjny.

## <a name="make-sure-your-device-is-joined-to-your-network"></a>Upewnij się, że urządzenie jest przyłączone do sieci
Jeśli pojawi się komunikat **Nie możesz się tam dostać z** informacją, że urządzenie jest niezgodne z zasadami dostępu organizacji, upewnij się, że urządzenie zostało połączone z siecią organizacji.

![Komunikat o błędzie związany z tym, czy korzystasz z sieci](media/user-help-device-remediation/network-version.png)

### <a name="to-check-whether-your-device-is-joined-to-your-network"></a>Aby sprawdzić, czy urządzenie jest przyłączone do sieci
1. Zaloguj się do systemu Windows przy użyciu konta służbowego. Na przykład alain@contoso.com.

2. Połącz się z siecią organizacji za pośrednictwem wirtualnej sieci prywatnej (VPN) lub funkcji DirectAccess.

3. Po nawiązaniu połączenia naciśnij **klawisze logo systemu Windows+L,** aby zablokować urządzenie.

4. Odblokuj urządzenie za pomocą konta służbowego, a następnie spróbuj ponownie uzyskać dostęp do problematycznej aplikacji lub usługi.

    Jeśli ponownie zobaczysz komunikat o błędzie **Nie możesz się tam dostać,** wybierz link Więcej **szczegółów,** a następnie skontaktuj się z pomocą techniczną ze szczegółami.

### <a name="to-join-your-device-to-your-network"></a>Aby dołączyć urządzenie do sieci
Jeśli urządzenie nie jest połączone z siecią organizacji, możesz wykonać jedną z dwóch czynności:

- **Dołącz do urządzenia służbowego.** Dołącz do należącego do pracy urządzenia z systemem Windows 10 do sieci organizacji, aby uzyskać dostęp do potencjalnie ograniczonych zasobów. Aby uzyskać więcej informacji i instrukcje krok po kroku, zobacz [Dołączanie urządzenia służbowego do sieci organizacji](user-help-join-device-on-network.md).

- **Zarejestruj swoje urządzenie osobiste do pracy.** Zarejestruj swoje urządzenie osobiste, zazwyczaj telefon lub tablet, w sieci organizacji. Po zarejestrowaniu urządzenia może uzyskać dostęp do ograniczonych zasobów organizacji. Aby uzyskać więcej informacji i instrukcje krok po kroku, zobacz [Rejestrowanie urządzenia osobistego w sieci organizacji](user-help-register-device-on-network.md).

## <a name="next-steps"></a>Następne kroki
- [Co to jest portal MyApps?](active-directory-saas-access-panel-introduction.md)

- [Logowanie się za pomocą telefonu zamiast hasła](user-help-auth-app-sign-in.md)
