---
title: Rejestrowanie urządzeń osobistych w sieci organizacji — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarejestrować urządzenia osobistego w sieci organizacji, aby dostęp do chronionych zasobów w organizacji.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: lizross
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 041c8bb6b4de2bbe2cbeb4c1a89e452239ae57bd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60473675"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>Rejestrowanie urządzenia osobistego w sieci organizacji
Zarejestruj urządzenie osobiste (zazwyczaj telefonie lub tablecie) w sieci organizacji. Po zarejestrowaniu urządzenia będzie dostęp do zasobów z ograniczeniami w organizacji.

>[!Note]
>W tym artykule użyto urządzenia Windows dla celów demonstracyjnych, ale możesz także zarejestrować urządzenia z systemem iOS, Android lub macOS.

## <a name="what-happens-when-you-register-your-device"></a>Co się dzieje po zarejestrowaniu urządzenia
Gdy jest rejestrowanie urządzenia w sieci organizacji, będzie miało miejsce następujące akcje:

- Windows wykonywana jest rejestracja urządzenia w sieci organizacji.

- Opcjonalnie, oparte na opcje Twojej organizacji, użytkownik może zostać poproszona o skonfigurowaną weryfikację dwuetapową, przy użyciu jednej [uwierzytelnianie wieloskładnikowe](multi-factor-authentication-end-user-first-time.md) lub [zabezpieczające](user-help-security-info-overview.md).

- Opcjonalnie oparte na opcje Twojej organizacji, użytkownik może automatycznej rejestracji w zarządzania urządzeniami przenośnymi, takie jak Microsoft Intune. Aby uzyskać więcej informacji na temat rejestrowania w programie Microsoft Intune, zobacz [zarejestrowania urządzenia w usłudze Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Będzie można przejść przez proces logowania przy użyciu nazwy użytkownika i hasło dla swojego konta firmowego lub szkolnego.

## <a name="to-register-your-windows-device"></a>Aby zarejestrować urządzenie Windows

Wykonaj następujące kroki, aby zarejestrować urządzenia osobistego w sieci.

1. Otwórz **ustawienia**, a następnie wybierz pozycję **kont**.

    ![Konta na ekranie Ustawienia](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Wybierz **dostęp do zasobów służbowych**, a następnie wybierz pozycję **Connect** z **dostęp do zasobów służbowych** ekranu.

    ![Otwórz ekran służbowe z podświetloną opcją Connect](./media/user-help-register-device-on-network/register-device-access-work-school-connect.png)

3. Na **Dodaj konto służbowe lub szkolne** ekranu, wpisz adres e-mail swojego konta firmowego lub szkolnego, a następnie wybierz **dalej**. Na przykład alain@contoso.com.

4. Zaloguj się do swojego konta firmowego lub szkolnego, a następnie wybierz **Zaloguj**.

5. Wykonaj pozostałe proces rejestracji, w tym zatwierdzania żądania weryfikacji tożsamości (Jeśli włączono weryfikację dwuetapową jest używany) oraz konfigurowanie Witaj Windows (jeśli jest to konieczne).

## <a name="to-verify-that-youre-registered"></a>Aby sprawdzić, czy jesteś zarejestrowanym
Możesz upewnić się, że jesteś zarejestrowanym sprawdzając ustawienia.

1. Otwórz **ustawienia**, a następnie wybierz pozycję **kont**.

    ![Konta na ekranie Ustawienia](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Wybierz **dostęp do zasobów służbowych**i upewnij się, zostanie wyświetlony swojego konta firmowego lub szkolnego.

    ![Otwórz ekran służbowego przy użyciu konta z połączonych firmy contoso](./media/user-help-register-device-on-network/register-device-setup-verify.png)

## <a name="next-steps"></a>Kolejne kroki
Po zarejestrowaniu urządzenia osobistego do sieci w organizacji, należy uzyskać wgląd w większość zasobów.

- Jeśli Twoja organizacja chce, aby dołączyć urządzenie pracy, zobacz [Dołączanie urządzenia pracy z siecią organizacji](user-help-join-device-on-network.md).



