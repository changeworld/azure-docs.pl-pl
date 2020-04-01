---
title: Konfigurowanie niestandardowych list ochrony hasłem usługi Azure Active Directory
description: W tym samouczku dowiesz się, jak skonfigurować niestandardowe listy ochrony hasłem zakazane dla usługi Azure Active Directory, aby ograniczyć typowe słowa w twoim środowisku.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4abb15462689470c87e9cf5ba8d5be8af2e45bfd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78252845"
---
# <a name="tutorial-configure-custom-banned-passwords-for-azure-active-directory-password-protection"></a>Samouczek: Konfigurowanie niestandardowych zakazanych haseł do ochrony hasłem usługi Azure Active Directory

Użytkownicy często tworzą hasła, które używają typowych lokalnych słów, takich jak szkoła, drużyna sportowa lub znana osoba. Te hasła są łatwe do odgadnięcia i słabe przed atakami opartymi na słowniku. Aby wymusić silne hasła w organizacji, niestandardowa lista haseł z blokadą usługi Azure Active Directory (Azure AD) umożliwia dodawanie określonych ciągów do oceny i blokowania. Żądanie zmiany hasła kończy się niepowodzeniem, jeśli na niestandardowej liście zablokowanych haseł znajduje się dopasowanie.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Włączanie haseł zbanowanych niestandardowych
> * Dodawanie wpisów do niestandardowej listy zakazanych haseł
> * Testowanie zmian hasła przy za pomocą zablokowanego hasła

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebne są następujące zasoby i uprawnienia:

* Działająca dzierżawa usługi Azure AD z włączoną co najmniej próbną wersją licencji.
    * W razie potrzeby [utwórz go za darmo](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Konto z uprawnieniami *administratora globalnego.*
* Użytkownik niebędący administratorem z hasłem, który znasz, na przykład *testuser*. Przetestować zdarzenie zmiany hasła przy użyciu tego konta w tym samouczku.
    * Jeśli chcesz utworzyć użytkownika, zobacz [Szybki start: Dodawanie nowych użytkowników do usługi Azure Active Directory](../add-users-azure-active-directory.md).
    * Aby przetestować operację zmiany hasła przy użyciu zablokowanego hasła, dzierżawa usługi Azure AD musi być [skonfigurowana do samoobsługowego resetowania hasła.](tutorial-enable-sspr.md)

## <a name="what-are-banned-password-lists"></a>Co to są listy zablokowanych haseł?

Usługa Azure AD zawiera globalną listę zakazanych haseł. Zawartość globalnej listy zakazanych haseł nie jest oparta na żadnym zewnętrznym źródle danych. Zamiast tego globalna lista zakazanych haseł jest oparta na bieżących wynikach telemetrii i analizy zabezpieczeń usługi Azure AD. Gdy użytkownik lub administrator próbuje zmienić lub zresetować swoje poświadczenia, żądane hasło jest sprawdzane względem listy zablokowanych haseł. Żądanie zmiany hasła kończy się niepowodzeniem, jeśli na globalnej liście zablokowanych haseł znajduje się dopasowanie.

Aby zapewnić elastyczność w zakresie haseł, można również zdefiniować niestandardową listę zakazanych haseł. Niestandardowa lista zakazanych haseł działa wraz z globalną listą zakazanych haseł, aby wymusić silne hasła w organizacji. Terminy specyficzne dla organizacji można dodać do niestandardowej listy zakazanych haseł, takich jak następujące przykłady:

* Marki
* Nazwy produktów
* Lokalizacje, takie jak siedziba firmy
* Warunki wewnętrzne specyficzne dla firmy
* Skróty, które mają określone znaczenie firmy

Gdy użytkownik próbuje zresetować hasło do czegoś, co znajduje się na globalnej lub niestandardowej liście zablokowanych haseł, zobaczy jeden z następujących komunikatów o błędach:

* *Niestety hasło zawiera słowo, frazę lub wzór, który ułatwia odgadnięcie hasła. Spróbuj ponownie przy innym haśle.*
* *Niestety nie można użyć tego hasła, ponieważ zawiera ono słowa lub znaki, które zostały zablokowane przez administratora. Spróbuj ponownie przy innym haśle.*

Niestandardowa lista zakazanych haseł jest ograniczona do maksymalnie 1000 terminów. Nie jest przeznaczony do blokowania dużych list haseł. Aby zmaksymalizować korzyści płynące z niestandardowej listy zakazanych haseł, zapoznaj się z [niestandardowymi pojęciami o zakazanych hasłach](concept-password-ban-bad.md#custom-banned-password-list) i [omówieniem algorytmu oceny haseł.](concept-password-ban-bad.md#how-are-passwords-evaluated)

## <a name="configure-custom-banned-passwords"></a>Konfigurowanie niestandardowych zakazanych haseł

Włączmy niestandardową listę zakazanych haseł i dodajmy kilka wpisów. W każdej chwili możesz dodać dodatkowe wpisy do niestandardowej listy zakazanych haseł.

Aby włączyć niestandardową listę zakazanych haseł i dodać do niej wpisy, wykonaj następujące czynności:

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu konta z uprawnieniami *administratora globalnego.*
1. Wyszukaj i wybierz **pozycję Azure Active Directory**, a następnie wybierz pozycję **Zabezpieczenia** z menu po lewej stronie.
1. W nagłówku menu **Zarządzanie** wybierz pozycję **Metody uwierzytelniania**, a następnie **ochrona hasłem**.
1. Ustaw opcję **Wymuszanie listy niestandardowej** na *Tak*.
1. Dodaj ciągi do **listy Niestandardowe zbanowane hasło**, jeden ciąg na wiersz. Do niestandardowej listy haseł zbanowanych haseł obowiązują następujące zagadnienia i ograniczenia:

    * Niestandardowa lista zakazanych haseł może zawierać maksymalnie 1000 terminów.
    * Niestandardowa lista zakazanych haseł jest niewrażliwa na argumenty.
    * Niestandardowa lista zakazanych haseł uwzględnia substytucję wspólnych znaków, takich jak "o" i "0" lub "a" i "@".
    * Minimalna długość ciągu to cztery znaki, a maksymalna 16 znaków.

    Określ własne hasła niestandardowe do zablokowania, jak pokazano w poniższym przykładzie

    [![](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png "Modify the custom banned password list under Authentication Methods in the Azure portal")](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords.png#lightbox)

1. Pozostaw opcję **Włącz ochronę hasłem w usłudze Active Directory systemu Windows Server** na *Nie*.
1. Aby włączyć niestandardowe hasła zbanowane i wpisy, wybierz pozycję **Zapisz**.

Zastosowanie aktualizacji listy haseł zbanowanymi niestandardowymi hasłami może potrwać kilka godzin.

W środowisku hybrydowym można również [wdrożyć ochronę hasłem usługi Azure AD w środowisku lokalnym.](howto-password-ban-bad-on-premises-deploy.md) Te same globalne i niestandardowe listy zakazanych haseł są używane zarówno dla żądań zmiany hasła w chmurze, jak i na prem.

## <a name="test-custom-banned-password-list"></a>Testowanie niestandardowej listy zakazanych haseł

Aby wyświetlić niestandardową listę zablokowanych haseł w akcji, spróbuj zmienić hasło na odmianę dodaną w poprzedniej sekcji. Gdy usługa Azure AD próbuje przetworzyć zmianę hasła, hasło jest dopasowywać do wpisu na niestandardowej liście zakazanych haseł. Następnie użytkownik jest wyświetlany błąd.

> [!NOTE]
> Aby użytkownik mógł zresetować swoje hasło w portalu internetowym, dzierżawa usługi Azure AD musi być [skonfigurowana do samoobsługowego resetowania hasła.](tutorial-enable-sspr.md)

1. Przejdź do strony Moje [https://myapps.microsoft.com](https://myapps.microsoft.com) **aplikacje** pod adresem .
1. W prawym górnym rogu wybierz swoje imię i nazwisko, a następnie wybierz **pozycję Profil** z menu rozwijanego.

    ![Wybieranie profilu](media/tutorial-configure-custom-password-protection/myapps-profile.png)

1. Na stronie **Profil** wybierz pozycję **Zmień hasło**.
1. Na stronie **Zmienianie hasła** wprowadź istniejące (stare) hasło. Wprowadź i potwierdź nowe hasło, które znajduje się na niestandardowej liście zablokowanych haseł zdefiniowanych w poprzedniej sekcji, a następnie wybierz pozycję **Prześlij**.
1. Zwracany jest komunikat o błędzie informujący, że hasło zostało zablokowane przez administratora, jak pokazano w poniższym przykładzie:

    ![Komunikat o błędzie wyświetlany podczas próby użycia hasła należącego do niestandardowej listy zakazanych haseł](media/tutorial-configure-custom-password-protection/password-change-error.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz już używać niestandardowej listy zakazanych haseł skonfigurowanych w ramach tego samouczka, wykonaj następujące kroki:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wyszukaj i wybierz **pozycję Azure Active Directory**, a następnie wybierz pozycję **Zabezpieczenia** z menu po lewej stronie.
1. W nagłówku menu **Zarządzanie** wybierz pozycję **Metody uwierzytelniania**, a następnie **ochrona hasłem**.
1. Ustaw opcję **Wymuszanie listy niestandardowej** na *Nie*.
1. Aby zaktualizować niestandardową konfigurację hasła zbanowane, wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku włączono i skonfigurowano niestandardowe listy ochrony hasłem dla usługi Azure AD. W tym samouczku omówiono:

> [!div class="checklist"]
> * Włączanie haseł zbanowanych niestandardowych
> * Dodawanie wpisów do niestandardowej listy zakazanych haseł
> * Testowanie zmian hasła przy za pomocą zablokowanego hasła

> [!div class="nextstepaction"]
> [Włączanie usługi Azure Multi-Factor Authentication bazującej na ryzyku](tutorial-mfa-applications.md)
