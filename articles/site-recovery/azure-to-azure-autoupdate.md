---
title: Automatyczna aktualizacja usługi mobilności, odzyskiwanie po awarii Azure – Azure | Dokumentacja firmy Microsoft
description: Zawiera omówienie automatyczna aktualizacja usługi mobilności, podczas replikowania maszyn wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: rajanaki
ms.openlocfilehash: 9f0a299fb8221554a3b0c9a19a616d5ba7613a67
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574407"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Automatyczna aktualizacja usługi mobilności w replikacji Azure – Azure

Usługa Azure Site Recovery ma miesięcznego cyklu wersji, gdzie ulepszenia istniejących funkcji lub nowych pakietów są dodawane i znanych ewentualnych problemów. Oznacza to, może być aktualizowana z usługą, należy zaplanować wdrożenie tych poprawki, co miesiąc. Aby uniknąć head over skojarzone z uaktualnienia, użytkownicy zamiast tego można umożliwić odzyskiwanie lokacji do zarządzania aktualizacjami składników. Jak wyjaśniono w [odwołanie do architektury](azure-to-azure-architecture.md) odzyskiwania po awarii Azure – Azure, usługi mobilności są instalowane na wszystkich maszynach wirtualnych platformy Azure dla których replikacja jest włączona podczas replikowania maszyn wirtualnych z jednej platformy Azure regionu do innego. Po włączeniu automatycznej aktualizacji, rozszerzenie usługi mobilności jest aktualizowany za pomocą każdej nowej wersji. W tym dokumencie szczegóły poniżej:

- Jak działa automatyczna aktualizacja
- Włącz Aktualizacje automatyczne
- Typowe problemy i rozwiązywanie problemów
 
## <a name="how-does-automatic-update-work"></a>Jak działa automatyczna aktualizacja

Gdy zezwalasz Site Recovery w celu zarządzania aktualizacjami, globalne elementu runbook (który jest używany przez usługi platformy Azure) jest wdrażany za pomocą konta usługi automation, który jest tworzony w tej samej subskrypcji co magazyn. Jedno konto usługi automation jest używany dla określonego magazynu. Element runbook sprawdza, czy dla każdej maszyny Wirtualnej w magazynie, dla których aktualizacje automatyczne są wyłączone w i inicjuje uaktualnienie rozszerzenia usługi mobilności, jeśli dostępna jest nowsza wersja. Domyślny harmonogram recurrs runbook codziennie o godzinie 12:00 AM zgodnie ze strefą czasową maszyny wirtualnej replikowane geograficznie. Harmonogram elementu runbook można także modyfikować za pomocą konta usługi automation przez użytkownika, jeśli to konieczne. 

> [!NOTE]
> Włączenie funkcji Aktualizacje automatyczne nie wymagają ponownego uruchomienia maszyn wirtualnych platformy Azure i nie ma wpływu na replikacji w toku.

> [!NOTE]
> Rozliczenia dla zadań, które korzystają z konta usługi automation zależy od liczby zadań razem, gdy minut w miesiącu i domyślnie 500 minut są dołączone jako bezpłatny limit jednostek dla konta usługi automation. Wykonanie przydziałów dziennych zadania z **kilka sekund, aby około minuty** i będzie **omówione w ramach bezpłatnych środków**.

BEZPŁATNY limit jednostek UWZGLĘDNIONY (MIESIĘCZNIE) ** ₹0.14 500 minut czasu uruchomione zadanie cena / minutę

## <a name="enable-automatic-updates"></a>Włącz Aktualizacje automatyczne

Można wybrać umożliwić odzyskiwanie lokacji do zarządzania aktualizacjami w następujący sposób:-

- [W ramach kroku Włączanie replikacji](#as-part-of-the-enable-replication-step)
- [Przełącz rozszerzenie aktualizowanie ustawień w magazynie](#toggle-the-extension-update-settings-inside-the-vault)

### <a name="as-part-of-the-enable-replication-step"></a>W ramach kroku Włączanie replikacji:

Po włączeniu replikacji dla maszyny wirtualnej, albo uruchamianie [z widoku maszyny wirtualnej](azure-to-azure-quickstart.md), lub [z magazynu usługi recovery services](azure-to-azure-how-to-enable-replication.md), otrzymasz możliwość albo zezwolić na odzyskiwanie lokacji Zarządzanie aktualizacjami dla rozszerzenie usługi Site Recovery lub ręcznie zarządzać takie same.

![Automatyczna aktualizacja, Włącz replikację w-](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Przełącz rozszerzenie aktualizowanie ustawień w magazynie

1. W magazynie, przejdź do **Zarządzaj**-> **infrastruktura usługi Site Recovery**
2. W obszarze **dla usługi Azure virtual Machines**-> **ustawień aktualizacji rozszerzenia**, kliknij aby wybrać, czy zezwolić na *ASR do zarządzania aktualizacjami* lub *ręcznie zarządzać*. Kliknij pozycję **Zapisz**.

![Vault Przełącz — autuo aktualizacji](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important] 
> Po wybraniu *ASR umożliwia zarządzanie*, ustawienie ma zastosowanie do wszystkich maszyn wirtualnych w odpowiedni magazyn.


> [!Note] 
> Obie opcje powiadomi konta usługi automation, który służy do zarządzania aktualizacjami. Jeśli po raz pierwszy w magazynie są włączenie tej funkcji, zostaną utworzone nowe konto usługi automation. Wszystkie replikacje kolejnych Włącz w tym samym magazynie, użyje ono utworzone wcześniej.

## <a name="common-issues--troubleshooting"></a>Typowe problemy i rozwiązywanie problemów

Jeśli występuje problem z aktualizacji automatycznych, zostanie wyświetlone powiadomienie o tym samym, w obszarze "Problemy z konfiguracją" na pulpicie nawigacyjnym magazynu. 

W przypadku próby włączenia automatycznej aktualizacji i nie powiodło się, można znaleźć poniżej do rozwiązywania problemów.

**Błąd**: nie masz uprawnienia do tworzenia konta Uruchom jako platformy Azure (nazwy głównej usługi) i nadania roli Współautor dla nazwy głównej usługi. 
- Zalecana akcja: Upewnij się, że zalogowanym koncie jest przypisany "Współautor", a następnie spróbuj ponownie wykonać operację. Zapoznaj się [to](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) dokumentu, aby uzyskać więcej informacji na temat przypisywania odpowiednich uprawnień.
 
Gdy aktualizacje automatyczne są włączone, większość problemów naprawiona w usłudze Site Recovery i wymaga kliknij pozycję "**naprawy**" przycisk.

![przycisk naprawy](./media/azure-to-azure-autoupdate/repair.png)

W przypadku, gdy przycisk naprawy nie jest dostępny, zapoznaj się z komunikatem o błędzie wyświetlany w obszarze okienka ustawień rozszerzenia.

 - **Błąd**: konto Uruchom jako nie ma uprawnień do dostępu do zasobu usługi recovery services.

    **Zalecana akcja**: Usuń i następnie [ponownie utwórz konto Uruchom jako](https://docs.microsoft.com/azure/automation/automation-create-runas-account) lub upewnij się, że aplikacja usługi Automation Uruchom jako dla konta usługi Azure Active Directory ma dostęp do zasobu usługi recovery services.

- **Błąd**: nie znaleziono konta Uruchom jako. Albo jeden z nich został usunięty lub nie został utworzony — aplikacja usługi Azure Active Directory, nazwa główna usługi, rola, zasób certyfikatu usługi Automation, zasób połączenia usługi Automation — lub odcisk palca nie jest taki sam dla certyfikatu i połączenia. 

    **Zalecana akcja**: Usuń i [ponownie utwórz konto Uruchom jako](https://docs.microsoft.com/azure/automation/automation-create-runas-account).
