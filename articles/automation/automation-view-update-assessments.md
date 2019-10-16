---
title: Wyświetlanie ocen aktualizacji Update Management platformy Azure
description: W tym artykule opisano sposób wyświetlania ocen aktualizacji dla wdrożeń aktualizacji
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e88622ede6437086b86a33081d6ec9b9ea50ef65
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377722"
---
# <a name="view-azure-update-management-update-assessments"></a>Wyświetlanie ocen aktualizacji Update Management platformy Azure

Na koncie usługi Automation wybierz pozycję **Update Management** , aby wyświetlić stan maszyn.

Ten widok zawiera informacje dotyczące maszyn, brakujących aktualizacji, wdrożeń aktualizacji i zaplanowanych wdrożeń aktualizacji. W **kolumnie zgodność**można zobaczyć czas ostatniego oceny maszyny. W kolumnie **Aktualizowanie gotowości agenta** można sprawdzić, czy kondycja agenta aktualizacji jest poprzednia. Jeśli wystąpił problem, wybierz link, aby przejść do dokumentacji dotyczącej rozwiązywania problemów, która może ułatwić zapoznanie się z krokami, które należy podjąć w celu rozwiązania problemu.

Aby uruchomić wyszukiwanie w dzienniku, które zwraca informacje o komputerze, aktualizacji lub wdrożeniu, wybierz element z listy. Zostanie otwarte okienko **przeszukiwania dzienników** z zapytaniem dotyczącym wybranego elementu:

![Update Management widoku domyślnego](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Wyświetl brakujące aktualizacje

Wybierz pozycję **brakujące aktualizacje** , aby wyświetlić listę aktualizacji, których brakuje na Twoich komputerach. Każda aktualizacja jest wyświetlana i można ją wybrać. Informacje o liczbie maszyn, które wymagają aktualizacji, systemu operacyjnego i linku, aby uzyskać więcej informacji. W okienku **przeszukiwania dzienników** są wyświetlane szczegółowe informacje o aktualizacjach.

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

### <a name="linux-2"></a>System

|Klasyfikacja  |Opis  |
|---------|---------|
|Aktualizacje krytyczne i zabezpieczeń     | Aktualizacje dotyczące konkretnego problemu lub problemu związanego z zabezpieczeniami.         |
|Inne aktualizacje     | Wszystkie inne aktualizacje, których charakter nie ma znaczenia lub nie są aktualizacjami zabezpieczeń.        |

W przypadku systemu Linux Update Management może rozróżnić aktualizacje krytyczne i zabezpieczenia w chmurze, a dane oceny są wyświetlane z powodu wzbogacania danych w chmurze. W przypadku stosowania poprawek Update Management opiera się na danych klasyfikacji dostępnych na komputerze. W przeciwieństwie do innych dystrybucji, CentOS nie ma dostępnych informacji. Jeśli masz skonfigurowane maszyny CentOS w sposób, aby zwracały dane zabezpieczeń dla poniższego polecenia, Update Management będzie można zastosować poprawki na podstawie klasyfikacji.

```bash
sudo yum -q --security check-update
```

Obecnie nie jest obsługiwana metoda umożliwiająca natywną klasyfikację — dostępność danych w CentOS. W tej chwili tylko Najlepsza pomoc techniczna jest świadczona klientom, którzy mogli ją samodzielnie włączyć.

## <a name="next-steps"></a>Następne kroki

Po wyświetleniu jakichkolwiek ocen aktualizacji można zaplanować wdrożenie aktualizacji, wykonując czynności opisane w sekcji [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure](automation-tutorial-update-management.md).