---
title: Ustawianie limitu czasu braku aktywności na poziomie katalogu dla użytkowników witryny Azure Portal | Dokumenty firmy Microsoft
description: Administratorzy mogą wymusić maksymalny czas bezczynność przed wylogowywane. Zasady limitu czasu braku aktywności jest ustawiona na poziomie katalogu.
services: azure-portal
keywords: ustawienia, limit czasu
author: mgblythe
ms.author: mblythe
ms.date: 02/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: e27135d09da7060f2a948e37f6026fe66fbef5b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096611"
---
# <a name="set-directory-level-inactivity-timeout"></a>Ustawianie limitu czasu braku aktywności na poziomie katalogu

Ustawienie limitu czasu braku aktywności pomaga chronić zasoby przed nieautoryzowanym dostępem, jeśli użytkownicy zapomną zabezpieczyć swoją stację roboczą. Gdy użytkownik był bezczynny przez pewien czas, ich sesji witryny azure portal jest automatycznie wylogowywane. Administratorzy w [roli Administrator globalny](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) mogą wymusić maksymalny czas bezczynny przed wylogowywane. Ustawienie limitu czasu braku aktywności ma zastosowanie na poziomie katalogu. Aby uzyskać więcej informacji o katalogach, zobacz [Omówienie Usług domenowych Active Directory](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

## <a name="configure-the-inactive-timeout-setting"></a>Konfigurowanie nieaktywnego limitu czasu

Jeśli jesteś administratorem globalnym i chcesz wymusić ustawienie limitu czasu bezczynności dla wszystkich użytkowników witryny Azure portal, wykonaj następujące kroki:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz **pozycję Ustawienia** z globalnego nagłówka strony.
3. Zaznacz tekst łącza **Skonfiguruj limit czasu poziomu katalogu**.

    ![Zrzut ekranu przedstawiający ustawienia portalu z wyróżnionym tekstem łącza](./media/admin-timeout/settings.png)

4. Zostanie otwarta nowa strona. Na stronie **Konfigurowanie limitu czasu braku aktywności na poziomie katalogu** wybierz pozycję Włącz limit czasu **bezczynności poziomu katalogu dla portalu Azure,** aby włączyć to ustawienie.
5. Następnie wprowadź **godziny** i **minuty,** aby uzyskać maksymalny czas, przez jaki użytkownik może być bezczynny przed automatycznym wylogowywane.
6. Wybierz przycisk **Zastosuj**.

    ![Zrzut ekranu przedstawiający stronę w celu ustawienia limitu czasu braku aktywności na poziomie katalogu](./media/admin-timeout/configure.png)

Aby potwierdzić, że zasady limitu czasu braku aktywności są ustawione poprawnie, wybierz **pozycję Powiadomienia** z nagłówka strony globalnej. Sprawdź, czy na liście znajduje się powiadomienie o powodzeniu.

  ![Zrzut ekranu przedstawiający pomyślny komunikat z powiadomieniem o przesunie limitu czasu braku aktywności na poziomie katalogu](./media/admin-timeout/confirmation.png)

Ustawienie staje się skuteczne dla nowych sesji. Nie będzie ona miała zastosowania od razu do wszystkich użytkowników, którzy są już zalogowane.

> [!NOTE]
> Jeśli administrator globalny skonfigurował ustawienie limitu czasu na poziomie katalogu, użytkownicy mogą zastąpić zasady i ustawić własny nieaktywny czas wylogowania. Jednak użytkownik musi wybrać przedział czasu, który jest mniejszy niż to, co jest ustawione na poziomie katalogu przez administratora globalnego.
>

## <a name="next-steps"></a>Następne kroki

* [Ustawianie preferencji witryny Azure Portal](set-preferences.md)
* [Eksportowanie i usuwanie ustawień użytkownika](azure-portal-export-delete-settings.md)
* [Włączanie dużego kontrastu lub zmienianie motywu](azure-portal-change-theme-high-contrast.md)
