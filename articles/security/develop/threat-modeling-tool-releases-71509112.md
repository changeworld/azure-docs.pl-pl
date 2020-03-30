---
title: Microsoft Threat Modeling Tool wydanie 9/12/2018
titleSuffix: Azure
description: Dokumentowanie informacji o wersji narzędzia do modelowania zagrożeń
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/15/2019
ms.openlocfilehash: a921310f14f0d48922165a46d750366e170ee374
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269915"
---
# <a name="threat-modeling-tool-ga-release-71509112---9122018"></a>Narzędzie do modelowania zagrożeń GA release 7.1.50911.2 - 9/12/2018

Z przyjemnością informujemy, że narzędzie Microsoft Threat Modeling Tool jest teraz dostępne do pobrania jako obsługiwana wersja ogólnie dostępna (GA). Ta wersja zawiera ważne aktualizacje prywatności i zabezpieczeń, a także poprawki błędów, aktualizacje funkcji i ulepszenia stabilności. Obecni użytkownicy wersji zapoznawczej 2017 zostaną poproszeni o aktualizację do najnowszej wersji za pomocą technologii ClickOnce po otwarciu klienta. Dla nowych użytkowników narzędzia, można [pobrać klienta](https://aka.ms/threatmodelingtool).

W tej wersji kończymy obsługę wersji zapoznawczej 2017 i zalecamy wszystkim użytkownikom aktualizacji w wersji zapoznawczej do wersji GA. W dniu lub po 15 października 2018 r. ustawimy minimalną wymaganą wersję ClickOnce dla narzędzia do modelowania zagrożeń, a wszyscy klienci wersji zapoznawczej będą musieli dokonać uaktualnienia.

Narzędzie Microsoft Threat Modeling Tool 2016, które jest dostępne w [Centrum pobierania Microsoft,](https://www.microsoft.com/en-us/download/details.aspx?id=49168)jest obsługiwane do 1 października 2019 r. tylko dla krytycznych poprawek zabezpieczeń.

## <a name="feature-changes"></a>Zmiany funkcji

### <a name="azure-stencil-updates"></a>Aktualizacje wzornika platformy Azure

Dodatkowe wzorniki platformy Azure i skojarzone z nimi zagrożenia i środki zaradcze zostały dodane do usługi wysyłkowej zestawu wzornika w tej wersji. Wprowadzono istotne zmiany w obszarach fokusowych "Usługi aplikacji Azure", "Oferty bazy danych platformy Azure" i "Usługa Azure Storage".

![Aktualizacje wzornika platformy Azure](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencil_update-300x70.png)

### <a name="onedrive-integration-feature-removed"></a>Usunięto funkcję integracji usługi OneDrive

Usunięto funkcje "Zapisz w usłudze OneDrive", "Otwórz z usługi OneDrive" i "Udostępnij link" w wersji zapoznawczej. Użytkownicy usługi OneDrive są zachęcani do korzystania z klienta [usługi OneDrive dla systemu Windows](https://onedrive.live.com/about/en-us/download/) firmy Microsoft, aby uzyskać dostęp do swoich plików przechowywanych w usłudze OneDrive za pośrednictwem standardowych okien dialogowych zapisywania i otwierania plików.

## <a name="notable-fixed-bugs-reported-by-customers"></a>Znaczące naprawione błędy zgłoszone przez klientów

### <a name="in-tmt-preview-the-tool-crashes-when-using-the-standard-template"></a>W urzędzie podglądu TMT narzędzie ulega awarii podczas korzystania ze standardowego szablonu

- Gdy wzornik rodzajowy (na przykład "Ogólny przepływ danych") jest dodawany do powierzchni rysunku i generuje zagrożenia, narzędzie może ulec awarii. Ten problem został rozwiązany.

### <a name="in-tmt-preview-when-i-save-a-report-or-copy-the-threats-the-risk-levels-are-incorrect"></a>W ucho.

- Jeśli użytkownik zmodyfikuje poziom ryzyka określonych zagrożeń, a następnie zapisuje raport lub kopiuje ryzyko, poziom ryzyka może powrócić do "Wysoki". Ten problem został rozwiązany.

## <a name="known-issues-and-faq"></a>Znane problemy i często zadawane pytania

### <a name="users-of-high-resolution-screens-may-experience-small-text-in-the-threat-properties"></a>Użytkownicy ekranów o wysokiej rozdzielczości mogą doświadczać małego tekstu we właściwościach zagrożenia

#### <a name="issue"></a>Problem

W widoku analizy narzędzia, jeśli użytkownik ma ekran o wysokiej rozdzielczości, który jest domyślnie ustawiony na powiększanie czytelności w systemie Windows, sekcja "Możliwe środki zaradcze" zagrożenia może pojawić się z małym tekstem.

![Znany problem z ekranami o wysokiej rozdzielczości](./media/threat-modeling-tool-releases-71509112/tmt_screen_resolution-300x153.png)

#### <a name="workaround"></a>Obejście

Użytkownik może kliknąć tekst ograniczający zagrożenie i użyć standardowego formantu powiększenia systemu Windows (Kółko myszy Crtl w górę), aby zwiększyć powiększenie tej sekcji.

### <a name="files-in-the-recently-opened-models-section-of-the-main-window-may-fail-to-open"></a>Pliki w sekcji "Ostatnio otwarte modele" w oknie głównym mogą nie zostać otwarte

#### <a name="issue"></a>Problem

Usunięto funkcję "Otwórz z usługi OneDrive" wersji zapoznawczej. Użytkownicy z "Ostatnio otwartymi modelami", które zostały zapisane w usłudze OneDrive, otrzymają następujący błąd.

![Usunięto funkcję usługi OneDrive](./media/threat-modeling-tool-releases-71509112/tmt_save_error-300x131.png)

#### <a name="workaround"></a>Obejście

Użytkownicy usługi OneDrive są zachęcani do korzystania z klienta [usługi OneDrive dla systemu Windows](https://onedrive.live.com/about/en-us/download/) firmy Microsoft, aby uzyskać dostęp do swoich plików przechowywanych w usłudze OneDrive za pośrednictwem standardowego i okna dialogowego "Otwórz model".

![Usunięto funkcję usługi OneDrive](./media/threat-modeling-tool-releases-71509112/tmt_save_onedrive-300x149.png)

### <a name="my-organization-uses-the-2016-version-of-the-tool-can-i-use-the-azure-stencil-set"></a>Moja organizacja używa wersji narzędzia na rok 2016, czy mogę użyć zestawu wzorników platformy Azure?

Tak, możesz! [Zestaw wzorników platformy Azure jest dostępny w usłudze GitHub](https://github.com/Microsoft/threat-modeling-templates/)i można go załadować w wersji narzędzia na rok 2016. Aby utworzyć nowy model z zestawem wzorników platformy Azure, użyj okna dialogowego "Szablon dla nowych modeli" na ekranie menu głównego. TMT 2016 nie może renderować łącza znalezione w polach "Możliwe środki zaradcze" zestawu wzorników platformy Azure, w związku z czym mogą być wyświetlane łącza wyświetlane jako znaczniki HTML.

![Aktualizacje wzornika platformy Azure w kliencie 2016](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencils-300x212.png)

## <a name="system-requirements"></a>Wymagania systemowe

- Obsługiwane systemy operacyjne
  - Microsoft Windows 10
- Wymagana wersja .NET
  - .NET 3.5.2
- Wymagania dodatkowe
  - Do otrzymywania aktualizacji narzędzia oraz szablonów wymagane jest połączenie z Internetem.

## <a name="documentation-and-feedback"></a>Dokumentacja i informacje zwrotne

- Dokumentacja narzędzia do modelowania zagrożeń znajduje się na [docs.microsoft.com](threat-modeling-tool.md)i zawiera informacje [o użyciu narzędzia.](threat-modeling-tool-getting-started.md)

## <a name="next-steps"></a>Następne kroki

Pobierz najnowszą wersję [narzędzia Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
