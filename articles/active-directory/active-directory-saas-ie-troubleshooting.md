---
title: Rozwiązywanie problemów z rozszerzeniem panelu dostępu platformy Azure dla programu Internet Explorer | Dokumentacja firmy Microsoft
description: Jak używać zasad grupy do wdrożenia dodatku programu Internet Explorer w portalu Moje aplikacje.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/30/2018
ms.author: barbkess
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 682973e6781a1de2c8d9628e39347650a3852b81
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364781"
---
# <a name="troubleshooting-the-access-panel-extension-for-internet-explorer"></a>Rozwiązywanie problemów z rozszerzenia Panelu dostępu do programu Internet Explorer
Ten artykuł pomoże Ci rozwiązać następujące problemy:

* Nie możesz uzyskiwać dostęp do aplikacji za pośrednictwem portalu Moje aplikacje przy użyciu programu Internet Explorer.
* Pojawi się komunikat "Zainstaluj oprogramowanie", nawet jeśli po zainstalowaniu oprogramowania.

Jeśli jesteś administratorem, zobacz też: [sposób wdrażania rozszerzenia Panelu dostępu do programu Internet Explorer przy użyciu zasad grupy](active-directory-saas-ie-group-policy.md)

## <a name="run-the-diagnostic-tool"></a>Uruchom narzędzia diagnostyczne
Pobierając i uruchamiając narzędzie diagnostyczne panelu dostępu, można zdiagnozować problemy z instalacją przy użyciu rozszerzenia Panelu dostępu do:

1. [Kliknij tutaj, aby pobrać narzędzie diagnostyczne.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
2. Otwórz plik i naciśnij klawisz **wyodrębnić wszystkie** przycisku.
   
    ![Naciśnij przycisk Wyodrębnij wszystko](./media/active-directory-saas-ie-troubleshooting/extract1.png)
3. Następnie naciśnij klawisz **wyodrębnić** przycisk, aby kontynuować.
   
    ![Naciśnij klawisz Extract](./media/active-directory-saas-ie-troubleshooting/extract2.png)
4. Aby uruchomić narzędzie, kliknij prawym przyciskiem myszy plik o nazwie **AccessPanelExtensionDiagnosticTool**, a następnie wybierz **Otwórz za pomocą > hosta na podstawie skryptów programu Microsoft Windows**.
   
    ![Otwórz za pomocą > Script Host oparty na systemie Microsoft Windows](./media/active-directory-saas-ie-troubleshooting/open_tool.png)
5. Zostanie wtedy wyświetlone następujące okno diagnostycznych w tym artykule opisano, co może być problem z instalacją.
   
    ![Przykład diagnostycznego okno](./media/active-directory-saas-ie-troubleshooting/tool_preview.png)
6. Kliknij przycisk "**tak**" umożliwiające program Rozwiązywanie problemów, które zostały znalezione.
7. Aby zapisać te zmiany, zamknij okno programu Internet Explorer, co, a następnie otwórz go ponownie.<br />Jeśli nadal nie można uzyskać dostępu do aplikacji, spróbuj wykonać poniższe kroki.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Sprawdź, czy włączono rozszerzenie panelu dostępu
Aby sprawdzić, czy rozszerzenie panelu dostępu jest włączona w programie Internet Explorer:

1. W programie Internet Explorer kliknij **ikonę koła zębatego** w prawym górnym rogu okna. Następnie wybierz pozycję **Opcje internetowe**.<br />(W starszych wersjach programu Internet Explorer można znaleźć w obszarze **Narzędzia > Opcje internetowe**.
   
    ![Przejdź do pozycji Narzędzia > Opcje internetowe](./media/active-directory-saas-ie-troubleshooting/internetoptions.png)
2. Kliknij przycisk **programy** , a następnie kliknij **zarządzać dodatkami** przycisku.
   
    ![Kliknij pozycję Zarządzanie dodatkami](./media/active-directory-saas-ie-troubleshooting/internetoptions_programs.png)
3. W tym oknie dialogowym wybierz **rozszerzenia Panelu dostępu do** a następnie kliknij przycisk **Włącz** przycisku.
   
    ![Kliknij pozycję Włącz](./media/active-directory-saas-ie-troubleshooting/enableaddon.png)
4. Aby zapisać te zmiany, zamknij okno programu Internet Explorer, co, a następnie otwórz go ponownie.

## <a name="enable-extensions-for-inprivate-browsing"></a>Włącz rozszerzenia do przeglądania InPrivate
Jeśli używasz trybu przeglądania InPrivate:

1. W programie Internet Explorer kliknij **ikonę koła zębatego** w prawym górnym rogu okna. Następnie wybierz pozycję **Opcje internetowe**.<br />(W starszych wersjach programu Internet Explorer można znaleźć w obszarze **Narzędzia > Opcje internetowe**.
   
    ![Przykład diagnostycznego okno](./media/active-directory-saas-ie-troubleshooting/inprivateoptions.png)
2. Przejdź do **zachowania** karcie następnie **Usuń zaznaczenie pola wyboru** pola wyboru **wyłącz paski narzędzi i rozszerzeń, gdy przeglądanie InPrivate rozpoczyna się**</p>
   
    ![Usuń zaznaczenie pola wyboru Wyłącz paski narzędzi i rozszerzeń po uruchomieniu przeglądania InPrivate](./media/active-directory-saas-ie-troubleshooting/enabletoolbars.png)
3. Aby zapisać te zmiany, zamknij okno programu Internet Explorer, co, a następnie otwórz go ponownie.

## <a name="uninstall-the-access-panel-extension"></a>Odinstaluj rozszerzenie panelu dostępu
Aby odinstalować rozszerzenie panelu dostępu z danego komputera:

1. Na klawiaturze naciśnij klawisz **klucz Windows** można otworzyć Start menu. Po otwarciu menu można wpisać niczego do wyszukiwania. Wpisz "Panel sterowania", a następnie otwórz **Panelu sterowania** , gdy pojawia się w wynikach wyszukiwania.
   
    ![Wyszukaj Panelu sterowania](./media/active-directory-saas-ie-troubleshooting/search_sm.png)
2. W prawym górnym rogu panelu sterowania Zmień **wyświetlić** opcję **duże ikony**. Następnie znajdź i kliknij pozycję **programy i funkcje** przycisku.
   
    ![Zmian centralnych widok, aby pokazać duże ikony](./media/active-directory-saas-ie-troubleshooting/control_panel.png)
3. Wybierz z listy, **rozszerzenia Panelu dostępu do**, a następnie kliknij **Odinstaluj** przycisku.
   
    ![Kliknij polecenie Odinstaluj](./media/active-directory-saas-ie-troubleshooting/uninstall.png)
4. Może następnie spróbować zainstalować rozszerzenie ponownie, aby zobaczyć, czy problem został rozwiązany.

Jeśli wystąpią problemy z odinstalować rozszerzenie, można również usunąć go za pomocą [Microsoft rozwiązać go](https://go.microsoft.com/?linkid=9779673) narzędzia.

## <a name="related-articles"></a>Powiązane artykuły
* [Indeks artykułów dotyczących zarządzania aplikacjami w usłudze Azure Active Directory](active-directory-apps-index.md)
* [Dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md)
* [Jak wdrożyć rozszerzenia Panelu dostępu do programu Internet Explorer przy użyciu zasad grupy](active-directory-saas-ie-group-policy.md)

