---
title: Narzędzie do modelowania zagrożeń zwalnia — narzędzie do modelowania zagrożeń firmy Microsoft — Azure | Dokumentacja firmy Microsoft
description: Dokumentowanie informacje o wersji przed zagrożeniami, narzędzie do modelowania
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2019
ms.author: jegeib
ms.openlocfilehash: bdf8b701567aaa3a0d9006333557bcec4f312723
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586464"
---
# <a name="threat-modeling-tool-ga-release-71509112---9122018"></a>Narzędzie do modelowania zagrożeń wersji ogólnie dostępnej wersji 7.1.50911.2 - 12/9/2018

Jesteśmy radością informujemy, że narzędzie do modelowania zagrożeń firmy Microsoft jest teraz dostępna do pobrania jako obsługiwanych wersji jest ogólnie dostępna (GA). Ta wersja zawiera ważne prywatności i aktualizacje zabezpieczeń, a także poprawki, aktualizacje funkcji i stabilność. Istniejących użytkowników wersji zapoznawczej 2017 wyświetli monit o aktualizację do najnowszej wersji za pomocą technologii ClickOnce, po otwarciu klienta. Nowi użytkownicy narzędzia [kliknij tutaj, aby pobrać klienta](https://aka.ms/threatmodelingtool).

W tej wersji firma Microsoft zakończą się obsługa wersji zapoznawczej 2017 i zaleca, aby wszyscy użytkownicy w wersji zapoznawczej aktualizacji do wersji ogólnie dostępnej. Na lub po 2018 15 października firma Microsoft ustawi w minimalnej wymaganej wersji ClickOnce dla narzędzia do modelowania zagrożeń, a wszyscy klienci w wersji zapoznawczej będzie wymagane do uaktualnienia.

Microsoft Threat modelowania narzędzie 2016, który jest dostępny z [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=49168), będą nadal obsługiwane do 2019 1 października, dla krytycznych poprawek tylko.

## <a name="feature-changes"></a>Funkcja zmiany

### <a name="azure-stencil-updates"></a>Aktualizacje platformy Azure wzornika

Wzorników dodatkowe platformy Azure i ich skojarzone zagrożeń i ograniczenia zostały dodane do wzornika Ustaw wysyłania w tej wersji. Wprowadzono znaczące zmiany w obszarach zainteresowań "Azure App Services", "Oferty innych bazy danych platformy Azure" i "W usłudze Azure Storage."

![Aktualizacje platformy Azure wzornika](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_azure_stencil_update-300x70.png)

### <a name="onedrive-integration-feature-removed"></a>Funkcja integracji usługi OneDrive usunięte

Funkcji "Zapisz do usługi OneDrive", "Otwórz w usłudze OneDrive" i "Udostępnij Link" (wersja zapoznawcza) zostały usunięte. Użytkownicy usługi onedrive są zachęcani do użycia przez firmę Microsoft [OneDrive for Windows](https://onedrive.live.com/about/en-us/download/) klienta, aby uzyskać dostęp do swoich plików przechowywanych w usłudze OneDrive za pośrednictwem standardowego pliku Zapisz i otwórz okien dialogowych.

## <a name="notable-fixed-bugs-reported-by-customers"></a>Warto poprawki błędów zgłaszanych przez klientów

### <a name="in-tmt-preview-the-tool-crashes-when-using-the-standard-template"></a>W wersji zapoznawczej TMT narzędzie ulega awarii podczas przy użyciu szablonu

- Gdy wzornika ogólnego (na przykład "ogólny przepływ danych") jest dodawany do powierzchni do rysowania i generuje zagrożenia, narzędzie może ulec awarii. Ten problem został rozwiązany.

### <a name="in-tmt-preview-when-i-save-a-report-or-copy-the-threats-the-risk-levels-are-incorrect"></a>W wersji zapoznawczej TMT podczas zapisywania raportu lub skopiuj zagrożeń poziomy ryzyka są niepoprawne

- Jeśli użytkownik modyfikuje poziom ryzyka dotyczący konkretnych zagrożeń i następnie zapisuje raport lub kopiuje ryzyka, poziom ryzyka może przywrócić "High". Ten problem został rozwiązany.

## <a name="known-issues-and-faq"></a>Znane problemy i często zadawane pytania

### <a name="users-of-high-resolution-screens-may-experience-small-text-in-the-threat-properties"></a>Użytkownicy z ekranami o wysokiej rozdzielczości mogą mieć mały tekst we właściwościach zagrożeń

#### <a name="issue"></a>Problem

W widoku analizy narzędzia Jeśli użytkownik ma o wysokiej rozdzielczości ekranu, który jest ustawiana domyślnie, aby powiększyć, aby zwiększyć czytelność w Windows, w sekcji "Możliwe Mitigation(s)" związany z zagrożeniem mogą być wyświetlane z małego tekstu.

![Znany problem z ekranami o wysokiej rozdzielczości](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_screen_resolution-300x153.png)

#### <a name="workaround"></a>Obejście

Użytkownik może kliknąć pozycję w tekście ograniczania ryzyka i powiększenie tę sekcję przy użyciu standardowych kontrolki powiększenia Windows (myszy Crtl kółka w górę).

### <a name="files-in-the-recently-opened-models-section-of-the-main-window-may-fail-to-open"></a>Pliki w sekcji "Ostatnio otwarte modele" okna głównego może zakończyć się niepowodzeniem do otwarcia

#### <a name="issue"></a>Problem

Funkcja "Otwórz w usłudze OneDrive" w wersji zapoznawczej została usunięta. Użytkownicy z "Ostatnio otwarte modele", które zostały zapisane w usłudze OneDrive zostanie wyświetlony następujący błąd.

![Funkcja OneDrive usunięte](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_save_error-300x131.png)

#### <a name="workaround"></a>Obejście

Użytkownicy usługi onedrive są zachęcani do użycia przez firmę Microsoft [OneDrive for Windows](https://onedrive.live.com/about/en-us/download/) klienta, aby uzyskać dostęp do swoich plików przechowywanych w usłudze OneDrive za pośrednictwem standard, jak i dialogowe "Otwieranie modelu".

![Funkcja OneDrive usunięte](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_save_onedrive-300x149.png)

### <a name="my-organization-uses-the-2016-version-of-the-tool-can-i-use-the-azure-stencil-set"></a>Moja organizacja korzysta z narzędzia w wersji 2016, można użyć zestawu wzornika platformy Azure?

Tak, można! [Zestaw wzornika platformy Azure jest dostępny w witrynie github](https://github.com/Microsoft/threat-modeling-templates/)i może być załadowany w narzędziu w wersji 2016. Aby utworzyć nowy model przy użyciu zestawu Azure wzornika, należy użyć okna dialogowego "Szablon dla nowych modeli" na ekranie głównym menu. TMT 2016 nie można renderować łącza można odnaleźć pola "Możliwe środki zaradcze" zestawu wzornika platformy Azure, dlatego może zostać wyświetlony łącza wyświetlane jako tagi HTML.

![Aktualizacje platformy Azure wzornika w kliencie 2016](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_azure_stencils-300x212.png)

## <a name="system-requirements"></a>Wymagania systemowe

- Obsługiwane systemy operacyjne
  - Microsoft Windows 10
- Wymagana wersja platformy .NET
  - .NET 3.5.2
- Wymagania dodatkowe
  - Aby otrzymywać aktualizacje do narzędzia, jak również szablony, wymagane jest połączenie internetowe.

## <a name="documentation-and-feedback"></a>Dokumentacja i opinie

- Dokumentacja dla narzędzia do modelowania zagrożeń znajduje się na [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)i zawiera informacje o [dotyczące korzystania z narzędzia](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Kolejne kroki

Pobierz najnowszą wersję [narzędzie do modelowania zagrożeń firmy Microsoft](https://aka.ms/threatmodelingtool).
