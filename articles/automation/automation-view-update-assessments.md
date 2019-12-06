---
title: Wyświetlanie ocen aktualizacji Update Management platformy Azure
description: W tym artykule opisano sposób wyświetlania ocen aktualizacji dla wdrożeń aktualizacji.
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d27df57e9371f16a15d3a18b7722598062377d88
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850520"
---
# <a name="view-azure-update-management-update-assessments"></a>Wyświetlanie ocen aktualizacji Update Management platformy Azure

Na koncie Azure Automation wybierz pozycję **Update Management** , aby wyświetlić stan maszyn.

Ten widok zawiera informacje dotyczące maszyn, brakujących aktualizacji, wdrożeń aktualizacji i zaplanowanych wdrożeń aktualizacji. W kolumnie **zgodność** można zobaczyć czas ostatniego oceny maszyny. W kolumnie **Aktualizowanie gotowości agenta** można sprawdzić kondycję agenta aktualizacji. Jeśli wystąpił problem, wybierz link, aby przejść do dokumentacji dotyczącej rozwiązywania problemów, która może pomóc w rozwiązaniu problemu.

Aby uruchomić wyszukiwanie w dzienniku, które zwraca informacje o komputerze, aktualizacji lub wdrożeniu, wybierz odpowiedni element na liście. Zostanie otwarte okienko **przeszukiwania dzienników** z zapytaniem dotyczącym wybranego elementu:

![Update Management widoku domyślnego](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Wyświetl brakujące aktualizacje

Wybierz pozycję **brakujące aktualizacje** , aby wyświetlić listę aktualizacji, których brakuje na Twoich komputerach. Każda aktualizacja jest wyświetlana i można ją wybrać. Wyświetlane są informacje o liczbie maszyn, które wymagają aktualizacji, szczegółów systemu operacyjnego i linku do większej ilości informacji. W okienku **przeszukiwania dzienników** są również wyświetlane szczegółowe informacje o aktualizacjach.

![Brakujące aktualizacje](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Klasyfikacje aktualizacji

W poniższej tabeli wymieniono klasyfikacje aktualizacji w Update Management z definicją dla każdej klasyfikacji.

### <a name="windows"></a>Windows

|Klasyfikacja  |Opis  |
|---------|---------|
|Aktualizacje krytyczne     | Aktualizacja dla konkretnego problemu, która stanowi odpowiedź na krytyczną usterkę niepowiązaną z zabezpieczeniami.        |
|Aktualizacje zabezpieczeń     | Aktualizacja problemu dotyczącego określonego produktu, związanego z zabezpieczeniami.        |
|Pakiety zbiorcze aktualizacji     | Zbiorczy zestaw poprawek, które są pakowane razem w celu łatwiejszego wdrażania.        |
|Pakiety funkcji     | Nowe funkcje produktu dystrybuowane poza wydaniem produktu.        |
|Dodatki Service Pack     | Zbiorczy zestaw poprawek, które są stosowane do aplikacji.        |
|Aktualizacje definicji     | Aktualizacja dla wirusów lub innych plików definicji.        |
|Narzędzia     | Narzędzie lub funkcja, która pomaga wykonać jedno lub więcej zadań.        |
|Aktualizacje     | Aktualizacja aplikacji lub pliku, który jest aktualnie zainstalowany.        |

### <a name="linux-2"></a>Linux

|Klasyfikacja  |Opis  |
|---------|---------|
|Aktualizacje krytyczne i zabezpieczeń     | Aktualizacje dotyczące konkretnego problemu lub problemu związanego z zabezpieczeniami.         |
|Inne aktualizacje     | Wszystkie inne aktualizacje, których charakter nie ma znaczenia ani aktualizacje zabezpieczeń.        |

W przypadku systemu Linux Update Management może rozróżnić aktualizacje krytyczne i aktualizacje zabezpieczeń w chmurze, a dane oceny są wyświetlane. (Stopień szczegółowości jest możliwy ze względu na Wzbogacanie danych w chmurze). W przypadku stosowania poprawek Update Management opiera się na danych klasyfikacji dostępnych na komputerze. W przeciwieństwie do innych dystrybucji, CentOS nie ma informacji dostępnych w wersji RTM produktu. Jeśli masz maszyny CentOS skonfigurowane do zwracania danych zabezpieczeń dla poniższego polecenia, Update Management może zostać poprawione na podstawie klasyfikacji:

```bash
sudo yum -q --security check-update
```

Obecnie nie jest obsługiwana metoda umożliwiająca natywną klasyfikację — dostępność danych w systemie CentOS. W tej chwili tylko Najlepsza pomoc techniczna jest świadczona klientom, którzy włączyli tę funkcję samodzielnie.

## <a name="next-steps"></a>Następne kroki

Po wyświetleniu jakichkolwiek ocen aktualizacji można zaplanować wdrożenie aktualizacji, wykonując czynności opisane w sekcji [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure](automation-tutorial-update-management.md).
