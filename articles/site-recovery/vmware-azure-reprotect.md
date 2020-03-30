---
title: Ponowne przekładzanie maszyn wirtualnych maszyn wirtualnych do lokacji lokalnej za pomocą usługi Azure Site Recovery
description: Dowiedz się, jak ponownie przeceniać maszyny wirtualne VMware po przejściu w stan failover na platformie Azure za pomocą usługi Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mayg
ms.openlocfilehash: 976888f57269cc9fe6107a38e30d78c73eb5c124
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257175"
---
# <a name="reprotect-from-azure-to-on-premises"></a>Ponowne włączanie ochrony po przejściu z platformy Azure do środowiska lokalnego

Po [przeczesywi](site-recovery-failover.md) lokalnych maszyn wirtualnych VMware lub serwerów fizycznych na platformie Azure, pierwszym krokiem w przypadku niepowodzenia z powrotem do lokacji lokalnej jest ponowne przeróbki maszyn wirtualnych platformy Azure, które zostały utworzone podczas pracy awaryjnej. W tym artykule opisano, jak to zrobić. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

1. Wykonaj kroki opisane w [tym artykule,](vmware-azure-prepare-failback.md) aby przygotować się do ponownego tworzenia i powrotu po awarii, w tym konfigurowania serwera przetwarzania na platformie Azure i lokalnego głównego serwera docelowego oraz konfigurowania sieci VPN między lokacjami lub prywatnej komunikacji równorzędnej usługi ExpressRoute w celu powrotu po awarii.
2. Upewnij się, że lokalny serwer konfiguracji jest uruchomiony i połączony z platformą Azure. Podczas pracy awaryjnej na platformie Azure lokacja lokalna może nie być dostępna, a serwer konfiguracji może być niedostępny lub zamknięty. Podczas powrotu po awarii maszyna wirtualna musi istnieć w bazie danych serwera konfiguracji. W przeciwnym razie powrót po awarii nie powiedzie się.
3. Usuń wszystkie migawki na lokalnym głównym serwerze docelowym. Reprotection nie będzie działać, jeśli istnieją migawki.  Migawki na maszynie Wirtualnej są automatycznie scalane podczas zadania ponownego ceł.
4. Jeśli ponownie wdrażasz maszyny wirtualne zebrane w grupie replikacji w celu zapewnienia spójności wielu maszyn wirtualnych, upewnij się, że wszystkie mają ten sam system operacyjny (Windows lub Linux) i upewnij się, że wdrażany serwer docelowy ma ten sam typ systemu operacyjnego. Wszystkie maszyny wirtualne w grupie replikacji muszą używać tego samego głównego serwera docelowego.
5. Otwórz [wymagane porty](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) do powrotu po awarii.
6. Upewnij się, że serwer vCenter jest połączony przed powrotem po awarii. W przeciwnym razie odłączanie dysków i dołączanie ich z powrotem do maszyny wirtualnej kończy się niepowodzeniem.
7. Jeśli serwer vCenter zarządza maszynami wirtualnymi, do których powrócisz po awarii, upewnij się, że masz wymagane uprawnienia. Jeśli użytkownik tylko do odczytu odnajduje i chroni maszyny wirtualne, ochrona powiedzie się i pracy awaryjnej. Jednak podczas ponownego przekazywania, pracy awaryjnej kończy się niepowodzeniem, ponieważ nie można odnajdyć magazynów danych i nie są wymienione podczas ponownego przekazywania. Aby rozwiązać ten problem, można zaktualizować poświadczenia vCenter za pomocą [odpowiedniego konta/uprawnień,](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)a następnie ponowić próbę wykonania zadania. 
8. Jeśli użyto szablonu do utworzenia maszyn wirtualnych, upewnij się, że każda maszyna wirtualna ma swój własny identyfikator UUID dla dysków. Jeśli lokalny identyfikator UUID maszyny Wirtualnej ma konflikt z identyfikatorem UUID głównego serwera docelowego, ponieważ oba zostały utworzone na podstawie tego samego szablonu, ponowne odrzucenie nie powiedzie się. Wdrażanie z innego szablonu.
9. Jeśli powrót do alternatywnego serwera vCenter nie powiódł się, upewnij się, że nowy serwer vCenter i serwer docelowy wzorcowy zostaną odnalezione. Zazwyczaj, jeśli nie są one magazyny danych nie są dostępne lub nie są widoczne w **Reprotect**.
10. Sprawdź następujące scenariusze, w których nie można przywrócić po awarii:
    - Jeśli korzystasz z bezpłatnej edycji ESXi 5.5 lub bezpłatnej edycji vSphere 6 Hypervisor. Uaktualnienie do innej wersji.
    - Jeśli masz serwer fizyczny z dodatku SP1 dla systemu Windows Server 2008 R2.
    - Maszyny wirtualne VMware nie mogą zakończyć się niepowodzeniem z powrotem do funkcji Hyper-V.
    - Maszyny wirtualne, które [zostały zmigrowane](migrate-overview.md#what-do-we-mean-by-migration).
    - Maszyna wirtualna, która została przeniesiona do innej grupy zasobów.
    - Replika maszyny Wirtualnej platformy Azure, która została usunięta.
    - Replika maszyny Wirtualnej platformy Azure, która nie jest chroniona (replikowanie do lokacji lokalnej).
10. [Przejrzyj typy powrotu po awarii,](concepts-types-of-failback.md) których można użyć — odzyskiwanie oryginalnej lokalizacji i odzyskiwanie lokalizacji alternatywnej.


## <a name="enable-reprotection"></a>Włącz ponowne odwrót

Włącz replikację. Można ponownie wyekslić określone maszyny wirtualne lub plan odzyskiwania:

- Jeśli ponownie wytężone planu odzyskiwania, należy podać wartości dla każdej chronionej maszyny.
- Jeśli maszyny wirtualne należą do grupy replikacji dla spójności wielu maszyn wirtualnych, można je ponownie ponownie wyczerpywać tylko przy użyciu planu odzyskiwania. Maszyny wirtualne w grupie replikacji muszą używać tego samego głównego serwera docelowego

### <a name="before-you-start"></a>Przed rozpoczęciem

- Po uruchomieniu maszyny Wirtualnej na platformie Azure po przejściu w tryb failover agenta, aby zarejestrować się z powrotem na serwerze konfiguracji (do 15 minut). W tym czasie nie będzie można ponownie przejść do ponownego ceł, a komunikat o błędzie wskazuje, że agent nie jest zainstalowany. Jeśli tak się stanie, poczekaj kilka minut, a następnie ponownie przecenić.
- Jeśli chcesz przywrócić maszynę wirtualną platformy Azure po awarii do istniejącej lokalnej maszyny Wirtualnej, zainstaluj lokalne magazyny danych maszyn wirtualnych z dostępem do odczytu/zapisu na hoście ESXi serwera docelowego.
- Jeśli chcesz wrócić po awarii do lokalizacji alternatywnej, na przykład jeśli lokalna maszyna wirtualna nie istnieje, wybierz dysk przechowywania i magazyn danych, które są skonfigurowane dla głównego serwera docelowego. Po powrocie do lokacji lokalnej maszyny wirtualne VMware w planie ochrony po awarii używają tego samego magazynu danych co główny serwer docelowy. Nowa maszyna wirtualna jest następnie tworzona w vCenter.

Włącz ponowneodwrotzenie w następujący sposób:

1. Wybierz opcję **Replikowane** > **elementy przechowalni**. Kliknij prawym przyciskiem myszy maszynę wirtualną, która uległa awarii, a następnie wybierz polecenie **Re-Protect**. Lub z przycisków poleceń wybierz urządzenie, a następnie wybierz **pozycję Re-Protect**.
2. Sprawdź, czy wybrano kierunek ochrony **lokalnej platformy Azure.**
3. W **głównym serwerze docelowym** i **serwerze przetwarzania**wybierz lokalny główny serwer docelowy i serwer przetwarzania.  
4. W przypadku **magazynu danych**wybierz magazyn danych, do którego chcesz odzyskać dyski lokalnie. Ta opcja jest używana, gdy lokalna maszyna wirtualna jest usuwana i należy utworzyć nowe dyski. Ta opcja jest ignorowana, jeśli dyski już istnieją. Nadal trzeba określić wartość.
5. Wybierz dysk retencji.
6. Zasady powrotu po awarii są wybierane automatycznie.
7. Wybierz **przycisk OK,** aby rozpocząć ponowne odwrót.

    ![Okno dialogowe Ponowne wybieranie ceł](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. Zadanie rozpoczyna replikowanie maszyny Wirtualnej platformy Azure do lokacji lokalnej. Na karcie **Zadania** można śledzić postęp.
    - Po pomyślnym odłączeniu maszyna wirtualna wchodzi w stan chroniony.
    - Lokalna maszyna wirtualna jest wyłączana na czas ponownego włączania ochrony. Pomaga to zapewnić spójność danych podczas replikacji.
    - Nie włączaj lokalnej maszyny Wirtualnej po zakończeniu ponownego ceł.
   

## <a name="next-steps"></a>Następne kroki

- Jeśli wystąpią jakiekolwiek problemy, zapoznaj się z [artykułem dotyczącym rozwiązywania problemów](vmware-azure-troubleshoot-failback-reprotect.md).
- Po chronionych maszynach wirtualnych platformy Azure można [uruchomić powrót po awarii.](vmware-azure-failback.md) Powrót po awarii zamyka maszynę wirtualną platformy Azure i uruchamia lokalną maszynę wirtualną. Spodziewaj się pewnego przestoju dla aplikacji i odpowiednio wybierz czas powrotu po awarii.


