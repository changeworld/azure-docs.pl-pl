---
title: Automatyczna aktualizacja usługi mobilności w odzyskiwania po awarii z platformy Azure do platformy Azure | Dokumentacja firmy Microsoft
description: Zawiera omówienie automatycznej aktualizacji usługi mobilności, która jest używana do replikacji maszyn wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/02/2018
ms.author: rajanaki
ms.openlocfilehash: 45f2e2927f699769bb385038c04d4dd23e075a9a
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="automatic-update-of-mobility-service-extension-in-azure-to-azure-replication"></a>Automatyczna aktualizacja usługi mobilności rozszerzenia w replikacji Azure do platformy Azure

Usługa Azure Site Recovery ma miesięczne okresach zlecenia, gdzie są dodawane ulepszenia istniejących funkcji lub nowych i znanych ewentualnych problemów. Oznaczałoby to, aby utrzymać bieżącego z usługą, należy zaplanować wdrożenie tych poprawek w okresach miesięcznych. Aby uniknąć head over skojarzone z uaktualnienia, użytkowników można zamiast tego wybrać opcję odzyskiwania lokacji w celu zarządzania aktualizacjami składników. Jak wyjaśniono w [odwołania architektura](azure-to-azure-architecture.md) podczas odzyskiwania systemu Azure do platformy Azure, usługi mobilności są instalowane na wszystkich maszynach wirtualnych platformy Azure dla których replikacja jest włączona, podczas replikacji maszyn wirtualnych z jednego systemu Azure region na inny. Ten dokument zawiera szczegóły poniżej:

- Jak działa automatyczna aktualizacja?
- Włącz Aktualizacje automatyczne
- Typowe problemy i rozwiązywanie problemów
 
## <a name="how-does-automatic-update-work"></a>Jak działa automatyczna aktualizacja

Po zezwolisz odzyskiwania lokacji w celu zarządzania aktualizacjami globalny element runbook (która jest używana przez usługi Azure) jest wdrażane za pomocą konta automatyzacji, który jest tworzony w tej samej subskrypcji co magazyn. Jedno konto automatyzacji jest używany dla określonych magazynu. Element runbook sprawdza dla każdej maszyny Wirtualnej w magazynie, dla którego włączone jest automatyczne aktualizacje i inicjuje uaktualnienie rozszerzenia usługi mobilności, jeśli dostępna jest nowsza wersja. Domyślne harmonogramy recurrs runbook codziennie o 00:00:00 zgodnie ze strefą czasową geograficznie zreplikowanej maszyny wirtualnej. Harmonogram może także modyfikować za pomocą konta automatyzacji przez użytkownika, jeśli to konieczne. 

## <a name="enable-automatic-updates"></a>Włącz Aktualizacje automatyczne

Można wybrać umożliwić odzyskanie lokacji do zarządzania aktualizacjami w następujący sposób:-

- [W ramach kroku Włączanie replikacji](#as-part-of-the-enable-replication-step)
- [Przełącz rozszerzenie zaktualizować ustawienia w ramach magazynu](#toggle-the-extension-update-settings-inside-the-vault)

### <a name="as-part-of-the-enable-replication-step"></a>W ramach kroku Włączanie replikacji:

Po włączeniu replikacji dla maszyny wirtualnej albo uruchamianie [z widoku maszyny wirtualnej](azure-to-azure-quickstart.md), lub [z magazynu usług odzyskiwania](azure-to-azure-how-to-enable-replication.md), uzyskasz możliwość albo zezwolić na odzyskiwanie lokacji Zarządzanie aktualizacjami rozszerzenia usługi Site Recovery i ręcznie zarządzać takie same.

![Włącz replikacji automatycznej aktualizacji](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Przełącz rozszerzenie zaktualizować ustawienia w ramach magazynu

1. W magazynie, przejdź do **Zarządzaj**-> **infrastruktura usługi Site Recovery**
2. W obszarze **dla usługi Azure virtual Machines**-> **ustawień aktualizacji rozszerzenia**, kliknij, aby wybrać, czy chcesz zezwolić *funkcja automatycznego odzyskiwania systemu w celu zarządzania aktualizacjami* lub *ręczne zarządzanie*. Kliknij pozycję **Zapisz**.

![Magazyn — przełącznik autuo aktualizacji](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important] 
> Po wybraniu *umożliwia automatycznego zarządzania*, ustawienie jest stosowane do wszystkich maszyn wirtualnych w odpowiedni magazyn.


> [!Note] 
> Obie opcje powiadomi użytkownika konta automatyzacji, który służy do zarządzania aktualizacjami. Jeśli po raz pierwszy w magazynie jest włączenie tej funkcji, zostanie utworzone nowe konto automatyzacji. Wszystkie kolejne włączania replikacji w tym samym magazynie użyje wcześniej utworzonej.

## <a name="common-issues--troubleshooting"></a>Typowe problemy i rozwiązywanie problemów

Jeśli wystąpi problem w usłudze Aktualizacje automatyczne, będziesz otrzymywać powiadomienia o tej samej, w obszarze "Problemy z konfiguracją" na pulpicie nawigacyjnym magazynu. 

W przypadku próbowano włączyć Aktualizacje automatyczne i nie powiodło się, można znaleźć poniżej do rozwiązywania problemów.

**Błąd**: nie masz uprawnienia do tworzenia konta Uruchom jako platformy Azure (nazwy głównej usługi) i przyznania roli współautora do nazwy głównej usługi. 
- Zalecana akcja: Upewnij się, że zalogowanego konta przypisano współautora, a następnie spróbuj ponownie wykonać operację.
 
Gdy aktualizacje automatyczne są włączone, większość problemów można zabliźnione przez usługę Site Recovery i wymaga kliknij pozycję "**naprawy**" przycisku.

![przycisk naprawy](./media/azure-to-azure-autoupdate/repair.png)

W przypadku, gdy przycisk naprawy nie jest dostępny, zapoznaj się komunikat o błędzie wyświetlany w obszarze Ustawienia rozszerzenia.

 - **Błąd**: konto Uruchom jako nie ma uprawnień dostępu do zasobu usługi odzyskiwania.

    **Zalecana akcja**: Usuń, a następnie [ponownie utworzyć konto Uruchom jako](https://docs.microsoft.com/en-us/azure/automation/automation-create-runas-account) lub upewnij się, że automatyzacji Uruchom jako dla konta Azure aplikacji usługi Active Directory ma dostęp do zasobów usług odzyskiwania.

- **Błąd**: nie znaleziono konta Uruchom jako. Albo jeden z nich został usunięty lub nie został utworzony - aplikacji usługi Azure Active Directory, nazwy głównej usługi roli, zasób certyfikatu usługi Automatyzacja, zasób połączenia usługi Automatyzacja — lub odcisk palca nie jest taka sama, jak połączenia i certyfikatów. 

    **Zalecana akcja**: Usuń i [ponownie utworzyć konto Uruchom jako](https://docs.microsoft.com/en-us/azure/automation/automation-create-runas-account).
