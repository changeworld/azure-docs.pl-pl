---
title: Dołączanie nowego urządzenia z systemem Windows 10 przy użyciu usługi Azure AD podczas pierwszego uruchomienia | Microsoft Docs
description: Temat, który objaśnia, jak użytkownicy mogą konfigurować dołączanie do usługi Azure AD podczas pierwszego uruchomienia.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/03/2019
ms.author: joflore
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: a79c5f89b14d15ffe4f3c582ac7e1e4cabbdc611
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60296660"
---
# <a name="tutorial-join-a-new-windows-10-device-with-azure-ad-during-a-first-run"></a>Samouczek: dołączanie nowego urządzenia z systemem Windows 10 przy użyciu usługi Azure AD podczas pierwszego uruchomienia

Zarządzanie urządzeniami w usłudze Azure Active Directory (Azure AD) pozwala zapewnić, że użytkownicy uzyskują dostęp do zasobów z urządzeń, które spełniają Twoje standardy dotyczące zabezpieczeń i zgodności. Aby uzyskać więcej informacji, zobacz [wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](overview.md).

W systemie Windows 10 możesz dołączyć nowe urządzenie do usługi Azure AD podczas pierwszego uruchomienia.  
Dzięki temu możesz dostarczać zapakowane w folię urządzenia do swoich pracowników lub uczniów.

Jeśli masz system Windows 10 Professional lub Windows 10 Enterprise zainstalowany na urządzeniu, środowisko użytkownika domyślnie rozpocznie proces konfiguracji urządzeń firmowych.

W przypadku *trybu OOBE* w systemie Windows dołączanie do lokalnej domeny usługi Active Directory (AD) nie jest obsługiwane. Jeśli planujesz dołączyć komputer do domeny usługi AD, podczas konfiguracji należy wybrać link **Skonfiguruj system Windows za pomocą konta lokalnego**. Następnie możesz dołączyć domenę z poziomu ustawień na komputerze.
 
W tym samouczku dowiesz się, jak dołączyć urządzenie do usługi Azure AD podczas pierwszego uruchomienia:
 > [!div class="checklist"]
> * Wymagania wstępne
> * Dołączanie urządzenia
> * Weryfikacja

## <a name="prerequisites"></a>Wymagania wstępne

Aby dołączyć urządzenie z systemem Windows 10, należy skonfigurować usługę rejestracji urządzeń, aby umożliwić rejestrowanie urządzeń. Oprócz posiadania uprawnień do dołączania urządzeń w dzierżawie usługi Azure AD musisz mieć mniej zarejestrowanych urządzeń niż skonfigurowane maksimum. Aby uzyskać więcej informacji, zobacz [konfigurowanie ustawień urządzenia](device-management-azure-portal.md#configure-device-settings).

Ponadto, jeśli dzierżawa jest federacyjna, dostawca tożsamości MUSI obsługiwać punkt końcowy nazwy użytkownika/hasła WS-Fed i WS-Trust. Może to być wersja 1.3 lub 2005. Obsługa tego protokołu jest wymagana do dołączenia urządzenia do usługi Azure AD oraz zalogowania się do urządzenia przy użyciu hasła.

## <a name="joining-a-device"></a>Dołączanie urządzenia

**Aby dołączyć urządzenie z systemem Windows 10 do usługi Azure AD podczas pierwszego uruchomienia:**


1. Po włączeniu nowego urządzenia i uruchomieniu procesu konfiguracji powinien zostać wyświetlony komunikat **Przygotowanie**. Postępuj zgodnie z monitami, aby skonfigurować urządzenie.

2. Rozpocznij od dostosowania regionu i języka. Następnie zaakceptuj postanowienia licencyjne oprogramowania firmy Microsoft.
 
    ![Dostosowywanie regionu](./media/azuread-joined-devices-frx/01.png)

3. Wybierz sieć, której chcesz użyć, aby połączyć się z Internetem.

4. Kliknij opcję **To urządzenie należy do mojej organizacji**. 

    ![Ekran Kto jest właścicielem tego komputera](./media/azuread-joined-devices-frx/02.png)

5. Wprowadź poświadczenia podane przez organizację, a następnie kliknij opcję **Zaloguj**.

    ![Ekran logowania](./media/azuread-joined-devices-frx/03.png)

6. Urządzenie lokalizuje pasującą dzierżawę w usłudze Azure AD. Jeśli znajdujesz się w domenie federacyjnej, system przekieruje Cię do lokalnego serwera usługi Secure Token Service (STS), np. usługi Active Directory Federation Services (AD FS).

7. Jeśli jesteś użytkownikiem, który nie korzysta z domeny federacyjnej, wprowadź poświadczenia bezpośrednio na stronie hostowanej w usłudze Azure AD. 

8. Zostanie wyświetlony monit o zastosowanie uwierzytelniania wieloskładnikowego. 
 
9. Usługa Azure AD sprawdzi, czy rejestracja w usłudze zarządzania urządzeniami przenośnymi jest wymagana.

10. System Windows rejestruje urządzenie w katalogu organizacji w usłudze Azure AD oraz rejestruje je w usłudze zarządzania urządzeniami przenośnymi, jeśli ma to zastosowanie.

11. Podjęta akcja zależy od typu użytkownika:
    - Użytkownik zarządzany — system Windows przeniesie Cię do pulpitu za pośrednictwem automatycznego procesu logowania.

    - Użytkownik federacyjny — nastąpi przejście do ekranu logowania systemu Windows w celu wprowadzenia poświadczeń.

## <a name="verification"></a>Weryfikacja

Aby sprawdzić, czy urządzenie dołączyło do usługi Azure AD, zapoznaj się z oknem dialogowym **Dostęp do zasobów służbowych** na urządzeniu z systemem Windows. Okno dialogowe powinno wskazywać, że masz połączenie z katalogiem usługi Azure AD.

![Dostęp do zasobów służbowych](./media/azuread-joined-devices-frx/13.png)


## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji, zobacz [wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](overview.md).

- Aby uzyskać więcej informacji o zarządzaniu urządzeniami w portalu usługi Azure AD, zobacz [zarządzanie urządzeniami przy użyciu witryny Azure Portal](device-management-azure-portal.md).
