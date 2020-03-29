---
title: Rozwiązywanie problemów z rozszerzeniem panelu programu Azure Access dla programu IE | Dokumenty firmy Microsoft
description: Jak używać zasad grupy do wdrażania dodatku Internet Explorer dla portalu Moje aplikacje.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0269c87572e2a9242a54491103ae0fcc3637518
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67723908"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Rozwiązywanie problemów z rozszerzeniem panelu dostępu dla programu Internet Explorer

Ten artykuł ułatwia rozwiązywanie następujących problemów:

* Podczas korzystania z programu Internet Explorer nie można uzyskać dostępu do aplikacji za pośrednictwem portalu Moje aplikacje.
* Zostanie wyświetlony komunikat "Zainstaluj oprogramowanie", nawet jeśli oprogramowanie jest już zainstalowane.

Jeśli jesteś administratorem, zobacz [Jak wdrożyć rozszerzenie panelu dostępu dla programu Internet Explorer przy użyciu zasad grupy](deploy-access-panel-browser-extension.md).

## <a name="run-the-diagnostic-tool"></a>Uruchamianie narzędzia diagnostycznego

Problemy z instalacją można zdiagnozować za pomocą rozszerzenia panelu dostępu, pobierając i uruchamiając narzędzie diagnostyczne Panelu dostępu. 

Aby pobrać i zainstalować narzędzie diagnostyczne:

1. [Wybierz to łącze, aby pobrać narzędzie diagnostyczne.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. Otwórz plik i wyodrębnij zawartość do komputera.
1. Aby uruchomić narzędzie, kliknij prawym przyciskiem myszy plik o nazwie *AccessPanelExtensionDiagnosticTool.js* i wybierz polecenie **Otwórz za pomocą** > **hosta skryptów z systemem Microsoft Windows**.

    ![Otwórz za pomocą > hosta skryptów opartych na systemie Microsoft Windows](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. Przejrzyj wyświetlane wyniki diagnostyczne i wybierz pozycję **Tak,** aby rozwiązać problemy. Zostanie **wyświetlone** okno dialogowe Sprawdź wyniki z informacjami o tym, co zrobić, jeśli rozszerzenie nie działa.  
1. Przeczytaj wiadomość i wybierz **przycisk OK**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Sprawdzanie, czy rozszerzenie panelu dostępu jest włączone

Aby sprawdzić, czy w programie Internet Explorer włączono rozszerzenie panelu dostępu:

1. W programie Internet Explorer wybierz **ikonę Biegi** w prawym górnym rogu okna i wybierz **pozycję Opcje internetowe**.
1. Przejdź do karty **Programy** i wybierz pozycję **Zarządzaj dodatkami**.
1. Wybierz **rozszerzenie panelu programu Access** w sekcji Microsoft **Corporation** i wybierz pozycję **Włącz**.
1. Aby zapisać zmiany, zamknij wszystkie otwarte okna przeglądarki Internet Explorer. Zmiana wchodzi w życie przy następnym otwarciu programu Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Włączanie rozszerzeń do przeglądania InPrivate

Aby włączyć rozszerzenia do przeglądania InPrivate:

1. W programie Internet Explorer wybierz **ikonę Biegi** w prawym górnym rogu okna i wybierz **pozycję Opcje internetowe**.
1. Przejdź do karty **Prywatność** i sprawdź, czy pole wyboru **Wyłącz paski narzędzi i rozszerzenia podczas rozpoczynania przeglądania InPrivate** jest jasne.
1. Aby zapisać zmiany, zamknij wszystkie otwarte okna przeglądarki Internet Explorer. Zmiana wchodzi w życie przy następnym otwarciu programu Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Odinstalowywanie rozszerzenia panelu dostępu

Aby odinstalować rozszerzenie panelu dostępu z komputera:

1. W Panelu sterowania wyszukaj *hasło odinstaluj*.
1. W wynikach wyszukiwania wybierz pozycję **Odinstaluj program**.

    ![Wybierz opcję Odinstaluj program z Panelu sterowania](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. Z listy wybierz rozszerzenie **panelu programu Access** i wybierz pozycję **Odinstaluj**.

    ![Odinstalowywanie rozszerzenia panelu dostępu](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. Następnie można spróbować zainstalować rozszerzenie ponownie, aby sprawdzić, czy problem został rozwiązany.

Jeśli napotkasz problemy z odinstalowaniem rozszerzenia, możesz je również usunąć za pomocą narzędzia [Microsoft Fix It.](https://go.microsoft.com/?linkid=9779673)

## <a name="related-articles"></a>Pokrewne artykuły:

* [Dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](what-is-single-sign-on.md)
* [Jak wdrożyć rozszerzenie panelu dostępu dla programu Internet Explorer przy użyciu zasad grupy](deploy-access-panel-browser-extension.md)
