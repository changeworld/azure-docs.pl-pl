---
title: Włączanie zapisywania haseł usługi Azure Active Directory
description: W tym samouczku dowiesz się, jak włączyć samoobsługowe resetowanie hasła usługi Azure AD przy użyciu usługi Azure AD Connect w celu synchronizowania zmian z powrotem do lokalnego środowiska usług domenowych Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/18/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3578cb1326ebd701c3f00618c19a501a1476372
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80332138"
---
# <a name="tutorial-enable-azure-active-directory-self-service-password-reset-writeback-to-an-on-premises-environment"></a>Samouczek: Włączanie samoobsługowego resetowania hasła usługi Azure Active Directory do środowiska lokalnego

Dzięki samoobsługowego resetowania haseł usługi Azure Active Directory (Azure AD) użytkownicy mogą zaktualizować swoje hasło lub odblokować swoje konto za pomocą przeglądarki sieci Web. W środowisku hybrydowym, w którym usługa Azure AD jest połączona z lokalnym środowiskiem usług domenowych Active Directory (AD DS), ten scenariusz może spowodować, że hasła będą się różnić między dwoma katalogami.

Ponowne zapisywanie hasła może służyć do synchronizowania zmian haseł w usłudze Azure AD z powrotem do lokalnego środowiska usług AD DS. Usługa Azure AD Connect udostępnia bezpieczny mechanizm wysyłania tych zmian haseł z powrotem do istniejącego katalogu lokalnego z usługi Azure AD.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie wymaganych uprawnień do zapisywania haseł
> * Włączanie opcji zapisywania zwrotnego hasła w usłudze Azure AD Connect
> * Włączanie zapisywania zwrotnego hasła w łączeniu SSPR usługi Azure AD

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebne są następujące zasoby i uprawnienia:

* Działająca dzierżawa usługi Azure AD z włączoną co najmniej próbną wersją licencji.
    * W razie potrzeby [utwórz go za darmo](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
    * Aby uzyskać więcej informacji, zobacz [Wymagania licencyjne dotyczące sspr usługi Azure AD](concept-sspr-licensing.md).
* Konto z uprawnieniami *administratora globalnego.*
* Usługa Azure AD skonfigurowana do samodzielnego resetowania hasła.
    * W razie potrzeby [wykonaj poprzedni samouczek, aby włączyć wiele samoużyć usługi Azure AD.](tutorial-enable-sspr.md)
* Istniejące lokalne środowisko usług AD DS skonfigurowane przy obliczu bieżącej wersji usługi Azure AD Connect.
    * W razie potrzeby skonfiguruj usługę Azure AD Connect przy użyciu ustawień [ekspresowych](../hybrid/how-to-connect-install-express.md) lub [niestandardowych.](../hybrid/how-to-connect-install-custom.md)
    * Aby używać funkcji Writeback hasła, kontrolery domeny muszą być systemami Windows Server 2008 R2 lub nowszymi.

## <a name="configure-account-permissions-for-azure-ad-connect"></a>Konfigurowanie uprawnień kont dla usługi Azure AD Connect

Usługa Azure AD Connect umożliwia synchronizowanie użytkowników, grup i poświadczeń między lokalnym środowiskiem usług AD DS a usługą Azure AD. Zazwyczaj instalujesz usługę Azure AD Connect na komputerze z systemem Windows Server 2012 lub nowszym, który jest przyłączony do lokalnej domeny usług AD DS.

Aby poprawnie pracować z zapisem zwrotnym spr, konto określone w usłudze Azure AD Connect musi mieć odpowiednie uprawnienia i opcje ustawione. Jeśli nie masz pewności, które konto jest obecnie używane, otwórz usługę Azure AD Connect i wybierz opcję **Wyświetl bieżącą konfigurację.** Konto, do którego należy dodać uprawnienia, znajduje się na liście w obszarze **Katalogi zsynchronizowane**. Na koncie należy ustawić następujące uprawnienia i opcje:

* **Resetowanie hasła**
* **Uprawnienia do zapisu** na`lockoutTime`
* **Uprawnienia do zapisu** na`pwdLastSet`
* **Rozszerzone prawa** do "Niewygasłego hasła" w przypadku:
   * Główny obiekt *każdej domeny* w tym lesie
   * Jednostki organizacyjne użytkownika, które mają być w zakresie SSPR

Jeśli nie przypisują tych uprawnień, writeback wydaje się być poprawnie skonfigurowany, ale użytkownicy napotykają błędy podczas zarządzania hasłami lokalnymi z chmury.

Aby skonfigurować odpowiednie uprawnienia do zapisywania zwrotnego hasła, wykonaj następujące kroki:

1. W lokalnym środowisku usług AD DS otwórz **pozycję Użytkownicy i komputery usługi Active Directory** z kontem z odpowiednimi uprawnieniami *administratora domeny.*
1. W menu **Widok** upewnij się, że **funkcje zaawansowane** są włączone.
1. W lewym panelu wybierz prawym przyciskiem wyboru obiekt reprezentujący katalog główny domeny i wybierz polecenie **Właściwości** > **Zaawansowane****zabezpieczenia** > .
1. Na karcie **Uprawnienia** wybierz pozycję **Dodaj**.
1. W przypadku **podmiotu zabezpieczeń**wybierz konto, do którego mają być stosowane uprawnienia (konto używane przez usługę Azure AD Connect).
1. Z listy rozwijanej **Dotyczy** wybierz pozycję **Użytkownik podrzędny**.
1. W obszarze *Uprawnienia*zaznacz pola dla następujących opcji:
    * **Resetowanie hasła**
1. W obszarze *Właściwości*zaznacz pola dla następujących opcji. Aby znaleźć te opcje, które mogą być już ustawione domyślnie, należy przewinąć listę:
    * **Czas blokady zapisu**
    * **Napisz pwdLastSet**

    [![](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions-cropped.png "Set the appropriate permissions in Active Users and Computers for the account that is used by Azure AD Connect")](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions.png#lightbox)

1. Gdy wszystko będzie gotowe, wybierz opcję **Zastosuj / OK,** aby zastosować zmiany i zamknij wszystkie otwarte okna dialogowe.

Podczas aktualizowania uprawnień replikacja tych uprawnień do wszystkich obiektów w katalogu może potrwać do godziny lub dłużej.

Zasady haseł w lokalnym środowisku usług AD DS mogą uniemożliwiać poprawne przetwarzanie resetowania haseł. Aby hasło dokonywać zapisu zwrotnego działało najwydajniej, zasady grupy dla *minimalnego wieku hasła* muszą być ustawione na 0. To ustawienie można znaleźć w obszarze **Zasady konfiguracji komputera > > Ustawienia systemu Windows > ustawienia zabezpieczeń > zasady konta** w obszarze `gpedit.msc`. 

Jeśli zaktualizujesz zasady grupy, poczekaj na replikację `gpupdate /force` zaktualizowanych zasad lub użyj polecenia.

> [!Note]
> Aby hasła zostały natychmiast zmienione, należy ustawić stornadztwo hasła na 0. Jeśli jednak użytkownicy przestrzegają zasad lokalnych, a *minimalny wiek hasła* jest ustawiony na wartość większą niż zero, zapisywanie zwrotne hasła będzie nadal działać po ocenie zasad lokalnych. 

## <a name="enable-password-writeback-in-azure-ad-connect"></a>Włączanie zapisywania zwrotnego hasła w usłudze Azure AD Connect

Jedną z opcji konfiguracji w usłudze Azure AD Connect jest zapisywanie zwrotne hasła. Gdy ta opcja jest włączona, zdarzenia zmiany hasła powodują, że usługa Azure AD Connect synchronizuje zaktualizowane poświadczenia z powrotem do lokalnego środowiska usług AD DS.

Aby włączyć samoobsługowe resetowanie hasła, należy najpierw włączyć opcję stornia zwrotnego w usłudze Azure AD Connect. Na serwerze usługi Azure AD Connect wykonaj następujące kroki:

1. Zaloguj się do serwera usługi Azure AD Connect i uruchom kreatora konfiguracji **usługi Azure AD Connect.**
1. Na **stronie powitalnej** wybierz pozycję **Konfiguruj**.
1. Na stronie **Zadania dodatkowe** wybierz pozycję **Dostosuj opcje synchronizacji**, a następnie wybierz pozycję **Dalej**.
1. Na stronie **Połącz z usługą Azure AD** wprowadź poświadczenia administratora globalnego dla dzierżawy platformy Azure, a następnie wybierz pozycję **Dalej**.
1. Na stronach filtrowania **Łączenie katalogów** i **Domena/jednostka OU** wybierz pozycję **Dalej**.
1. Na stronie **Funkcje opcjonalne** zaznacz pole obok pozycji **Zapisywanie zwrotne haseł** i wybierz pozycję **Dalej**.

    ![Konfigurowanie usługi Azure AD Connect do zapisywania haseł](media/tutorial-enable-sspr-writeback/enable-password-writeback.png)

1. Na stronie **Wszystko gotowe do skonfigurowania** wybierz pozycję **Konfiguruj** i poczekaj na zakończenie procesu.
1. Po ukończeniu konfiguracji wybierz pozycję **Zakończ**.

## <a name="enable-password-writeback-for-sspr"></a>Włącz zapisywanie haseł dla sspr

Po włączeniu zapisywania zwrotnego haseł w usłudze Azure AD Connect należy skonfigurować wiele SSPR usługi Azure AD dla funkcji writeback. Po włączeniu samowzrostu samoużycie samookamentu do korzystania z hasła wstecz, użytkownicy, którzy zmienią lub zresetować swoje hasło mają zaktualizowane hasło zsynchronizowane z powrotem do lokalnego środowiska usług AD DS, jak również.

Aby włączyć zapisywanie haseł w łączeniu zwrotnym w sypr, wykonaj następujące kroki:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta administratora globalnego.
1. Wyszukaj i wybierz **pozycję Azure Active Directory**, wybierz pozycję **Resetowanie hasła**, a następnie wybierz pozycję **Integracja lokalna**.
1. Czy ustawić opcję **zapisywania haseł do katalogu lokalnego?** *Yes*
1. Czy opcja **Zezwalaj użytkownikom na odblokowywanie kont bez resetowania hasła?** *Yes*

    ![Włączanie samoobsługowego resetowania hasła usługi Azure AD w celu zapisania hasła](media/tutorial-enable-sspr-writeback/enable-sspr-writeback.png)

1. Gdy będzie gotowy, wybierz pozycję **Zapisz**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz już używać funkcji zapisu zwrotnego samouś, które zostały skonfigurowane w ramach tego samouczka, wykonaj następujące kroki:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wyszukaj i wybierz **pozycję Azure Active Directory**, wybierz pozycję **Resetowanie hasła**, a następnie wybierz pozycję **Integracja lokalna**.
1. Czy ustawić opcję **zapisywania haseł do katalogu lokalnego?** *No*
1. Czy ustawiono opcję **Zezwalaj użytkownikom na odblokowywanie kont bez resetowania hasła?** *No*

Jeśli nie chcesz już używać żadnych funkcji hasła, wykonaj następujące kroki z serwera usługi Azure AD Connect:

1. Zaloguj się do serwera usługi Azure AD Connect i uruchom kreatora konfiguracji **usługi Azure AD Connect.**
1. Na **stronie powitalnej** wybierz pozycję **Konfiguruj**.
1. Na stronie **Zadania dodatkowe** wybierz pozycję **Dostosuj opcje synchronizacji**, a następnie wybierz pozycję **Dalej**.
1. Na stronie **Połącz z usługą Azure AD** wprowadź poświadczenia administratora globalnego dla dzierżawy platformy Azure, a następnie wybierz pozycję **Dalej**.
1. Na stronach filtrowania **Łączenie katalogów** i **Domena/jednostka OU** wybierz pozycję **Dalej**.
1. Na stronie **Funkcje opcjonalne** usuń zaznaczenie pola obok pozycji **Zapisywanie zwrotne hasła** i wybierz pozycję **Dalej**.
1. Na stronie **Wszystko gotowe do skonfigurowania** wybierz pozycję **Konfiguruj** i poczekaj na zakończenie procesu.
1. Po ukończeniu konfiguracji wybierz pozycję **Zakończ**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku włączono ponowne zapisywanie zwrotne przynap. W tym samouczku omówiono:

> [!div class="checklist"]
> * Konfigurowanie wymaganych uprawnień do zapisywania haseł
> * Włączanie opcji zapisywania zwrotnego hasła w usłudze Azure AD Connect
> * Włączanie zapisywania zwrotnego hasła w łączeniu SSPR usługi Azure AD

> [!div class="nextstepaction"]
> [Ocena ryzyka podczas logowania](tutorial-risk-based-sspr-mfa.md)
