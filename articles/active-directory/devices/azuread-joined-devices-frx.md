---
title: Dołączanie urządzenia z systemem Windows 10 nowe za pomocą usługi Azure AD podczas pierwszego uruchomienia | Dokumentacja firmy Microsoft
description: Temat, który objaśnia, jak użytkownicy mogą skonfigurować usługi Azure AD Join podczas pierwszego uruchomienia komputera.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 1376f011d056aac33333f6ac31ee2eaadaf3ef4a
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39415001"
---
# <a name="join-a-new-windows-10-device-with-azure-ad-during-a-first-run"></a>Dołączanie urządzenia z systemem Windows 10 nowe za pomocą usługi Azure AD podczas pierwszego uruchomienia

Za pomocą zarządzania urządzeniami w usłudze Azure Active Directory (Azure AD) można zagwarantować, że użytkownicy uzyskują dostęp do zasobów z urządzeń, które spełniają Twoje standardy dotyczące bezpieczeństwa i zgodności. Aby uzyskać więcej informacji, zobacz [wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](overview.md).

W systemie Windows 10 możesz dołączyć nowe urządzenie do usługi Azure AD podczas pierwszego uruchomienia komputera (FRX).  
Dzięki temu można rozpowszechniać urządzeń porządnie zapakowane do pracowników lub studentów.

Jeśli masz system Windows 10 Professional lub Windows 10 Enterprise zainstalowana na urządzeniu, środowisko wartość domyślna to proces instalacji w przypadku urządzeń należących do firmy.

W Windows *środowiska out-of-box*, przyłączania do domeny usługi Active Directory (AD) w środowisku lokalnym nie jest obsługiwane. Jeśli planujesz przyłączyć komputer do domeny usługi AD podczas instalacji należy wybrać łącze **skonfigurować Windows za pomocą konta lokalnego**. Następnie możesz dołączyć domeny przy użyciu ustawień na komputerze.
 


## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby przyłączyć urządzenie z systemem Windows 10, usługi rejestracji urządzeń, musi być skonfigurowany umożliwiające rejestrowanie urządzeń. Oprócz mając uprawnienia do dołączania urządzeń w Twojej dzierżawie usługi Azure AD, musi mieć mniej urządzeń zarejestrowanych niż skonfigurowane maksimum. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ustawień urządzenia](device-management-azure-portal.md#configure-device-settings).

Ponadto w Twojej dzierżawy jest Sfederowane, dostawcy tożsamości musi obsługiwać WS-Fed i WS-Trust punkt końcowy nazwy użytkownika i hasła. Może to być w wersji 1.3 lub 2005. Ta obsługa protokołu jest wymagany do przyłączenia urządzenia do usługi Azure AD i zaloguj się do urządzenia przy użyciu hasła.

## <a name="joining-a-device"></a>Łączenie urządzenia

**Dołączanie urządzenia z systemem Windows 10 do usługi Azure AD podczas FRX:**


1. Po włączeniu nowe urządzenie i rozpocznij proces instalacji, powinien zostać wyświetlony **wprowadzenie gotowe** wiadomości. Postępuj zgodnie z monitami, aby skonfigurować urządzenie.

2. Rozpocznij od dostosowanie region i język. Następnie zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft.
 
    ![Dostosowywanie w Twoim regionie](./media/azuread-joined-devices-frx/01.png)

3. Wybierz sieć, z którego chcesz użyć do nawiązywania połączenia z Internetem.

4. Kliknij przycisk **to urządzenie należy do mojej organizacji**. 

    ![Kto jest właścicielem tego ekranu komputera](./media/azuread-joined-devices-frx/02.png)

5. Wprowadź poświadczenia, które zostały przekazane użytkownikowi przez Twoją organizację, a następnie kliknij przycisk **Zaloguj**.

    ![Ekran logowania](./media/azuread-joined-devices-frx/03.png)

6. Użytkownik urządzenia lokalizuje pasującego dzierżawy w usłudze Azure AD. Jeśli jesteś w domeny federacyjnej, nastąpi przekierowanie do lokalnego serwera Secure Token Service (STS), na przykład, Active Directory Federation Services (AD FS).

7. Jeśli jesteś użytkownikiem domeny inne niż federacyjne, wprowadź swoje poświadczenia bezpośrednio na stronie hostowanych przez usługi AD systemu Azure. 

8. Zostanie wyświetlony monit o wezwanie do uwierzytelnienia Multi-Factor Authentication. 
 
9. Usługi Azure AD umożliwia sprawdzenie, czy wymagana jest rejestracja w usłudze zarządzania urządzeniami przenośnymi.

10. Windows to zarejestrowanie urządzenia w katalogu organizacji w usłudze Azure AD i rejestrowane w usłudze zarządzania urządzeniami przenośnymi, jeśli ma to zastosowanie.

11. Jeśli chcesz:
    - Zarządzanego użytkownika Windows spowoduje przejście do pulpitu za pośrednictwem procesu automatycznego logowania.

    - Użytkownik federacyjny są kierowane do Windows ekran logowania, aby wprowadzić swoje poświadczenia.

## <a name="verification"></a>Weryfikacja

Aby sprawdzić, czy urządzenie jest przyłączone do usługi Azure AD, zapoznaj się z **dostęp do zasobów służbowych** okna dialogowego na urządzeniu z systemem Windows. Okno dialogowe powinno wskazywać, czy masz połączenie z katalogiem usługi Azure AD.

![Dostęp do zasobów służbowych](./media/azuread-joined-devices-frx/13.png)


## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji, zobacz [wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](overview.md).

- Aby uzyskać więcej informacji na temat zarządzania urządzeniami w portalu usługi Azure AD, zobacz [zarządzania urządzeniami przy użyciu witryny Azure portal](device-management-azure-portal.md).
