---
title: Wyświetlanie ocen aktualizacji usługi Azure Update Management
description: W tym artykule opisano sposób wyświetlania ocen aktualizacji dla wdrożeń aktualizacji.
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: 58d3cf6261456c09195ad6dafaeb781b55d9e5ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278417"
---
# <a name="view-azure-update-management-update-assessments"></a>Wyświetlanie ocen aktualizacji usługi Azure Update Management

Na koncie usługi Azure Automation wybierz pozycję **Zarządzanie aktualizacjami,** aby wyświetlić stan maszyn.

Ten widok zawiera informacje o komputerach, brakujących aktualizacjach, wdrożeniach aktualizacji i zaplanowanych wdrożeniach aktualizacji. W kolumnie **ZGODNOŚĆ** można zobaczyć, kiedy maszyna została oceniona po raz ostatni. W **update agent gotowości** kolumny, można zobaczyć kondycję agenta aktualizacji. Jeśli występuje problem, wybierz łącze, aby przejść do dokumentacji rozwiązywania problemów, która może pomóc w rozwiązaniu problemu.

Aby uruchomić wyszukiwanie w dzienniku, które zwraca informacje o komputerze, aktualizacji lub wdrożenia, wybierz odpowiedni element na liście. Zostanie otwarte okienko **Wyszukiwanie dzienników** z zapytaniem dla wybranego elementu:

![Widok domyślny zarządzania aktualizacjami](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Wyświetlanie brakujących aktualizacji

Wybierz **pozycję Brakujące aktualizacje,** aby wyświetlić listę aktualizacji, których brakuje na komputerach. Każda aktualizacja jest wyświetlana i można ją wybrać. Wyświetlane są informacje o liczbie komputerów wymagających aktualizacji, szczegółach systemu operacyjnego i łączu, aby uzyskać więcej informacji. W **okienku wyszukiwania dziennika** jest również więcej szczegółów dotyczących aktualizacji.

![Brakujące aktualizacje](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Klasyfikacje aktualizacji

W poniższych tabelach przedstawiono obsługiwane klasyfikacje aktualizacji w zarządzania aktualizacjami z definicją dla każdej klasyfikacji.

### <a name="windows"></a>Windows

|Klasyfikacja  |Opis  |
|---------|---------|
|Aktualizacje krytyczne     | Aktualizacja określonego problemu, który rozwiązuje krytyczny błąd niezwiązany z zabezpieczeniami.        |
|Aktualizacje zabezpieczeń     | Aktualizacja problemu związanego z zabezpieczeniami specyficznym dla produktu.        |
|Pakiety zbiorcze aktualizacji     | Skumulowany zestaw poprawek, które są pakowane razem w celu łatwego wdrożenia.        |
|Pakiety funkcji     | Nowe funkcje produktu, które są dystrybuowane poza wydaniem produktu.        |
|Dodatki Service Pack     | Skumulowany zestaw poprawek, które są stosowane do aplikacji.        |
|Aktualizacje definicji     | Aktualizacja do wirusów lub innych plików definicji.        |
|Narzędzia     | Narzędzie lub funkcja, która pomaga wykonać jedno lub więcej zadań.        |
|Aktualizacje     | Aktualizacja aplikacji lub pliku, który jest aktualnie zainstalowany.        |

### <a name="linux"></a><a name="linux-2"></a>Linux

|Klasyfikacja  |Opis  |
|---------|---------|
|Aktualizacje krytyczne i zabezpieczeń     | Aktualizacje dotyczące określonego problemu lub problemu związanego z zabezpieczeniami specyficznego dla produktu.         |
|Inne aktualizacje     | Wszystkie inne aktualizacje, które nie mają charakteru krytycznego lub nie są aktualizacjami zabezpieczeń.        |

W systemie Linux zarządzanie aktualizacjami może rozróżniać aktualizacje krytyczne i aktualizacje zabezpieczeń w chmurze podczas wyświetlania danych oceny. (Ta szczegółowość jest możliwa ze względu na wzbogacenie danych w chmurze). W przypadku poprawek zarządzanie aktualizacjami opiera się na danych klasyfikacji dostępnych na komputerze. W przeciwieństwie do innych dystrybucji, CentOS nie ma tych informacji dostępnych w wersjach RTM produktu. Jeśli masz maszyny CentOS skonfigurowane do zwracania danych zabezpieczeń dla następującego polecenia, zarządzanie aktualizacjami może łatać się na podstawie klasyfikacji:

```bash
sudo yum -q --security check-update
```

Obecnie nie ma żadnej obsługiwanej metody, aby włączyć dostępność danych natywnych klasyfikacji w CentOS. W tej chwili tylko najlepsze wsparcie jest dostępne dla klientów, którzy włączyli tę funkcję na własną rękę.

Aby sklasyfikować aktualizacje w red hat enterprise w wersji 6, musisz zainstalować wtyczkę yum-security. Na Red Hat Enterprise Linux 7 wtyczka jest już częścią samego mniam, nie ma potrzeby instalowania czegokolwiek. Aby uzyskać więcej informacji, zobacz następujący [artykuł merytoryczny](https://access.redhat.com/solutions/10021)Red Hat .

## <a name="next-steps"></a>Następne kroki

Po wyświetleniu ocen aktualizacji można zaplanować wdrożenie aktualizacji, wykonując kroki opisane w [witrynie Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure.](automation-tutorial-update-management.md)
