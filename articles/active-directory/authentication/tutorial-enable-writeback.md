---
title: Włączanie zapisywania zwrotnego haseł w usłudze Azure AD
description: W tym samouczku włączysz zapisywanie zwrotne haseł, aby pobierać zmiany haseł zainicjowane w chmurze z powrotem do środowiska lokalnego usługi AD w ramach programu Azure AD Connect.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: dabe0ad1a556ee43f3e6cae0e1cd421db5cde0fd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60413964"
---
# <a name="tutorial-enabling-password-writeback"></a>Samouczek: włączanie zapisywania zwrotnego haseł

W tym samouczku włączysz zapisywanie zwrotne haseł dla swojego środowiska hybrydowego. Zapisywanie zwrotne haseł jest używane do synchronizowania zmian haseł w usłudze Azure Active Directory (Azure AD) z lokalnym środowiskiem usług Active Directory Domain Services (AD DS). Zapisywanie zwrotne haseł jest włączane w ramach programu Azure AD Connect w celu zapewnienia bezpiecznego mechanizmu przesyłania zmian haseł do istniejącego katalogu lokalnego z usługi Azure AD. Więcej szczegółów na temat wewnętrznego działania funkcji zapisywania zwrotnego haseł można znaleźć w artykule [Co to jest zapisywanie zwrotne haseł](concept-sspr-writeback.md).

> [!div class="checklist"]
> * Włączanie opcji zapisywania zwrotnego haseł w programie Azure AD Connect
> * Włączanie opcji zapisywania zwrotnego haseł w usłudze samoobsługowego resetowania hasła (SSPR)

## <a name="prerequisites"></a>Wymagania wstępne

* Dostęp do działającej dzierżawy usługi Azure AD z przypisaną co najmniej próbną wersją licencji.
* Konto z uprawnieniami administratora globalnego w dzierżawie usługi Azure AD.
* Istniejący serwer ze skonfigurowaną, uruchomioną aktualną wersją programu [Azure AD Connect](../hybrid/how-to-connect-install-express.md).
* Ukończenie poprzednich samouczków dotyczących samoobsługowego resetowania hasła.

## <a name="enable-password-writeback-option-in-azure-ad-connect"></a>Włączanie opcji zapisywania zwrotnego haseł w programie Azure AD Connect

Aby włączyć zapisywanie zwrotne haseł, najpierw należy włączyć tę funkcję z poziomu serwera, na którym zainstalowano program Azure AD Connect.

1. Aby skonfigurować i włączyć zapisywanie zwrotne haseł, zaloguj się na serwerze programu Azure AD Connect i uruchom kreatora konfiguracji programu **Azure AD Connect**.
2. Na **stronie powitalnej** wybierz pozycję **Konfiguruj**.
3. Na stronie **Zadania dodatkowe** wybierz pozycję **Dostosuj opcje synchronizacji**, a następnie wybierz pozycję **Dalej**.
4. Na stronie **Łączenie z usługą Azure AD** wprowadź poświadczenia administratora globalnego, a następnie wybierz pozycję **Dalej**.
5. Na stronach filtrowania **Łączenie katalogów** i **Domena/jednostka OU** wybierz pozycję **Dalej**.
6. Na stronie **Funkcje opcjonalne** zaznacz pole obok pozycji **Zapisywanie zwrotne haseł** i wybierz pozycję **Dalej**.
7. Na stronie **Wszystko gotowe do skonfigurowania** wybierz pozycję **Konfiguruj** i poczekaj na zakończenie procesu.
8. Po ukończeniu konfiguracji wybierz pozycję **Zakończ**.

## <a name="enable-password-writeback-option-in-sspr"></a>Włączanie opcji zapisywania zwrotnego haseł w usłudze SSPR

Włączenie funkcji zapisywania zwrotnego haseł w programie Azure AD Connect to tylko połowa sukcesu. Zezwolenie usłudze SSPR na używanie funkcji zapisywania zwrotnego haseł zamyka pętlę, umożliwiając w ten sposób użytkownikom, którzy zmienią lub zresetują swoje hasło, ustawienie tego hasła także w środowisku lokalnym.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego.
2. Przejdź do usługi **Azure Active Directory**, kliknij pozycję **Resetowanie hasła**, następnie wybierz pozycję **Integracja lokalna**.
3. Dla opcji **Zapisywać zwrotnie hasła do katalogu lokalnego?** ustaw wartość **Tak**.
4. Dla opcji **Zezwolić użytkownikom na odblokowywanie kont bez resetowania hasła?** ustaw wartość **Tak**.
5. Kliknij pozycję **Zapisz**

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku włączono funkcję zapisywania zwrotnego haseł dla samoobsługowego resetowania hasła. Pozostaw otwarte okno witryny Azure Portal i przejdź do następnego samouczka, aby skonfigurować dodatkowe ustawienia związane z samoobsługowym resetowaniem hasła przed wdrożeniem rozwiązania w pilotażu.

> [!div class="nextstepaction"]
> [Włączanie funkcji samoobsługowego resetowania hasła na ekranie logowania systemu Windows](tutorial-sspr-windows.md)
