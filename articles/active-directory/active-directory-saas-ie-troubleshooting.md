---
title: Rozwiązywanie problemów z rozszerzenia Panelu dostępu platformy Azure dla programu Internet Explorer | Dokumentacja firmy Microsoft
description: Jak wdrożyć dodatek programu Internet Explorer dla portalu Moje aplikacje przy użyciu zasad grupy.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: f56b3230-26fd-42ec-9e3d-2c12daf15479
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/31/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a4f1538cf598da8b5b9aa19def2d5f86ceaca0a0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="troubleshooting-the-access-panel-extension-for-internet-explorer"></a>Rozwiązywanie problemów z rozszerzeniem Panel dostępu dla programu Internet Explorer
Ten artykuł pomoże Ci rozwiązać następujące problemy:

* Nie możesz uzyskiwać dostęp do aplikacji za pośrednictwem portalu Moje aplikacje przy użyciu programu Internet Explorer.
* Zostanie wyświetlony komunikat "Zainstaluj oprogramowanie" nawet po zainstalowaniu oprogramowania.

Jeśli jesteś administratorem, zobacz też: [wdrażanie rozszerzenia Panel dostępu dla programu Internet Explorer przy użyciu zasad grupy](active-directory-saas-ie-group-policy.md)

## <a name="run-the-diagnostic-tool"></a>Uruchom narzędzie diagnostyczne
Można diagnozować problemy instalacji z rozszerzeniem panelu dostępu przez pobranie i uruchomienie narzędzia diagnostyczne panelu dostępu:

1. [Kliknij tutaj, aby pobrać narzędzie diagnostyczne.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
2. Otwórz plik i naciśnij klawisz **Wyodrębnij wszystkie** przycisku.
   
    ![Kliknij przycisk Wyodrębnij wszystkie](./media/active-directory-saas-ie-troubleshooting/extract1.png)
3. Następnie naciśnij klawisz **wyodrębnić** przycisk, aby kontynuować.
   
    ![Naciśnij klawisz wyodrębniania](./media/active-directory-saas-ie-troubleshooting/extract2.png)
4. Aby uruchomić narzędzie, kliknij prawym przyciskiem myszy plik o nazwie **AccessPanelExtensionDiagnosticTool**, a następnie wybierz pozycję **Otwórz za pomocą > na podstawie Host skryptów systemu Windows**.
   
    ![Otwórz za pomocą > hosta skryptów na podstawie systemu Microsoft Windows](./media/active-directory-saas-ie-troubleshooting/open_tool.png)
5. Zostanie wtedy wyświetlone następujące okno diagnostyczne w tym artykule opisano, co może być problem z instalacją.
   
    ![Przykładowe okno diagnostycznych](./media/active-directory-saas-ie-troubleshooting/tool_preview.png)
6. Kliknij przycisk "**tak**" Aby pozwolić, aby rozwiązać problemy, które zostały znalezione program.
7. Aby zapisać te zmiany, Zamknij co okno programu Internet Explorer, a następnie otwórz go ponownie.<br />Jeśli nadal nie można uzyskać dostępu do aplikacji, spróbuj wykonać poniższe czynności.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Sprawdź, czy włączono rozszerzenie panelu dostępu
Aby sprawdzić, czy rozszerzenie panelu dostępu jest włączona w programie Internet Explorer:

1. W programie Internet Explorer, kliknij przycisk **koło zębate ikonę** w prawym górnym rogu okna. Następnie wybierz **Opcje internetowe**.<br />(W starszych wersjach programu Internet Explorer można go znaleźć, w obszarze **Narzędzia > Opcje internetowe**.
   
    ![Przejdź do pozycji Narzędzia > Opcje internetowe](./media/active-directory-saas-ie-troubleshooting/internetoptions.png)
2. Kliknij przycisk **programy** , a następnie kliknij **Zarządzanie dodatkami** przycisku.
   
    ![Kliknij pozycję Zarządzanie dodatkami](./media/active-directory-saas-ie-troubleshooting/internetoptions_programs.png)
3. W tym oknie dialogowym wybierz **rozszerzenia Panel dostępu** , a następnie kliknij przycisk **włączyć** przycisku.
   
    ![Kliknij pozycję Włącz](./media/active-directory-saas-ie-troubleshooting/enableaddon.png)
4. Aby zapisać te zmiany, zamknij okno programu Internet Explorer, co, a następnie otwórz go ponownie.

## <a name="enable-extensions-for-inprivate-browsing"></a>Włącz rozszerzenia do przeglądania InPrivate
Jeśli używasz trybu przeglądania InPrivate:

1. W programie Internet Explorer, kliknij przycisk **koło zębate ikonę** w prawym górnym rogu okna. Następnie wybierz **Opcje internetowe**.<br />(W starszych wersjach programu Internet Explorer można go znaleźć, w obszarze **Narzędzia > Opcje internetowe**.
   
    ![Przykładowe okno diagnostycznych](./media/active-directory-saas-ie-troubleshooting/inprivateoptions.png)
2. Przejdź do **prywatności** karcie następnie **Usuń zaznaczenie pola wyboru** pola wyboru **wyłącz paski narzędzi i rozszerzeń po uruchomieniu przeglądania InPrivate**</p>
   
    ![Usuń zaznaczenie pola wyboru Wyłącz paski narzędzi i rozszerzeń po uruchomieniu przeglądania InPrivate](./media/active-directory-saas-ie-troubleshooting/enabletoolbars.png)
3. Aby zapisać te zmiany, zamknij okno programu Internet Explorer, co, a następnie otwórz go ponownie.

## <a name="uninstall-the-access-panel-extension"></a>Odinstaluj rozszerzenie panelu dostępu
Aby odinstalować rozszerzenia Panelu dostępu z tego komputera:

1. Na klawiaturze naciśnij klawisz **klawisz systemu Windows** aby otworzyć Start menu. Po otwarciu menu można wpisać niczego do wyszukiwania. Wpisz "Panel sterowania", a następnie otwórz **Panelu sterowania** po wyświetleniu w wynikach wyszukiwania.
   
    ![Wyszukaj Panelu sterowania](./media/active-directory-saas-ie-troubleshooting/search_sm.png)
2. W prawym górnym rogu panelu sterowania Zmień **wyświetlić** opcji w celu **duże ikony**. Następnie znajdź i kliknij **programy i funkcje** przycisku.
   
    ![Zmian widok do pokazania duże ikony](./media/active-directory-saas-ie-troubleshooting/control_panel.png)
3. Wybierz z listy, **rozszerzenia Panel dostępu**, a następnie kliknij **Odinstaluj** przycisku.
   
    ![Kliknij przycisk Odinstaluj](./media/active-directory-saas-ie-troubleshooting/uninstall.png)
4. Następnie spróbujesz zainstalować rozszerzenie ponownie, aby sprawdzić, czy problem został rozwiązany.

Jeśli wystąpią problemy dotyczące odinstalowywania rozszerzenia, można również usunąć go za pomocą [Microsoft Napraw go](https://go.microsoft.com/?linkid=9779673) narzędzia.

## <a name="related-articles"></a>Pokrewne artykuły
* [Indeks artykułów dotyczących zarządzania aplikacjami w usłudze Azure Active Directory](active-directory-apps-index.md)
* [Dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md)
* [Wdrażanie rozszerzenia Panel dostępu dla programu Internet Explorer przy użyciu zasad grupy](active-directory-saas-ie-group-policy.md)

