---
title: Rozwiązywanie problemów z rozszerzeniem panelu dostępu platformy Azure dla programu Internet Explorer | Dokumentacja firmy Microsoft
description: Jak używać zasad grupy do wdrożenia dodatku programu Internet Explorer w portalu Moje aplikacje.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: celested
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63dfece713657df4450f18b8a7ce212ce2c41687
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60291557"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Rozwiązywanie problemów z rozszerzenia Panelu dostępu do programu Internet Explorer

Ten artykuł pomoże Ci rozwiązać następujące problemy:

* Nie możesz uzyskiwać dostęp do aplikacji za pośrednictwem portalu Moje aplikacje przy użyciu programu Internet Explorer.
* Pojawi się komunikat "Zainstaluj oprogramowanie", nawet jeśli po zainstalowaniu oprogramowania.

Jeśli jesteś administratorem, zobacz [sposób wdrażania rozszerzenia Panelu dostępu do programu Internet Explorer przy użyciu zasad grupy](deploy-access-panel-browser-extension.md).

## <a name="run-the-diagnostic-tool"></a>Uruchom narzędzia diagnostyczne

Można diagnozować problemy z instalacją przy użyciu rozszerzenia Panelu dostępu do, pobierając i uruchamiając narzędzie diagnostyczne panelu dostępu. 

Aby pobrać i zainstalować narzędzia diagnostycznego:

1. [Wybierz ten link, aby pobrać narzędzie diagnostyczne.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)

2. Otwórz plik i Wyodrębnij zawartość do komputera.
   
3. Aby uruchomić narzędzie, kliknij prawym przyciskiem myszy plik o nazwie *AccessPanelExtensionDiagnosticTool.js* i wybierz **Otwórz za pomocą** > **hosta na podstawie skryptów programu Microsoft Windows** .
   
    ![Otwórz za pomocą > Script Host oparty na systemie Microsoft Windows](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

4. Przejrzyj wyniki diagnostyki, które są wyświetlane, a następnie wybierz **tak** do rozwiązywania problemów. **Sprawdź wyniki** pojawi się okno dialogowe z informacjami o tym, co należy zrobić, jeśli rozszerzenie nie działa.  

5. Przeczytaj komunikat, a następnie wybierz **OK**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Sprawdź, czy włączono rozszerzenie panelu dostępu

Aby sprawdzić, czy włączono rozszerzenie panelu dostępu w programie Internet Explorer:

1. W programie Internet Explorer wybierz **ikonę koła zębatego** w prawym górnym rogu okna, a następnie wybierz pozycję **Opcje internetowe**.
   
2. Przejdź do **programy** kartę, a następnie wybierz pozycję **zarządzać dodatkami**.
   
3. Wybierz **rozszerzenia Panelu dostępu do** w **firmy Microsoft Corporation** i wybierz pozycję **Włącz**.
   
4. Aby zapisać zmiany, zamknij wszystkie okna przeglądarki Internet Explorer otwartych. Zmiana zostanie uwzględniona przy następnym otwarciu programu Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Włącz rozszerzenia do przeglądania InPrivate

Aby włączyć rozszerzenia na przeglądanie InPrivate:

1. W programie Internet Explorer wybierz **ikonę koła zębatego** w prawym górnym rogu okna, a następnie wybierz pozycję **Opcje internetowe**.
   
2. Przejdź do **zachowania** kartę i sprawdź, czy **wyłącz paski narzędzi i rozszerzeń, gdy przeglądanie InPrivate uruchamia** pole wyboru jest wyczyszczone.
   
3.  Aby zapisać zmiany, zamknij wszystkie okna przeglądarki Internet Explorer otwartych. Zmiana zostanie uwzględniona przy następnym otwarciu programu Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Odinstaluj rozszerzenie panelu dostępu

Aby odinstalować rozszerzenie panelu dostępu z tego komputera:

1. W Panelu sterowania, wyszukaj *odinstalować*. 

2. W wynikach wyszukiwania wybierz **Odinstaluj program**.
   
    ![Wyszukaj program dezinstalacyjny.](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

3. Wybierz z listy, **rozszerzenia Panelu dostępu do** i wybierz **Odinstaluj**.

    ![Odinstaluj rozszerzenie panelu dostępu.](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)
   
4. Może następnie spróbować zainstalować rozszerzenie ponownie, aby zobaczyć, czy problem został rozwiązany.

Jeśli napotkasz problemy, odinstalować rozszerzenie można również usunąć go za pomocą [Microsoft rozwiązać go](https://go.microsoft.com/?linkid=9779673) narzędzia.

## <a name="related-articles"></a>Pokrewne artykuły:
* [Dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](what-is-single-sign-on.md)
* [Jak wdrożyć rozszerzenia Panelu dostępu do programu Internet Explorer przy użyciu zasad grupy](deploy-access-panel-browser-extension.md)

