---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: tanmaygore
ms.service: virtual-machines
ms.topic: include
ms.date: 02/06/2020
ms.author: tagore
ms.custom: include file
ms.openlocfilehash: 57469bef7014010164234638f3d059ac96b125cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78383823"
---
## <a name="what-is-the-time-required-for-migration"></a>Jaki jest czas potrzebny na migrację?

Planowanie i realizacja migracji w dużym stopniu zależy od złożoności architektury i może potrwać kilka miesięcy.  

## <a name="what-is-the-definition-of-a-new-customer-on-iaas-vms-classic"></a>Jaka jest definicja nowego klienta na maszynach wirtualnych IaaS (klasyczny)?

Klienci, którzy nie mieli maszyn wirtualnych IaaS (klasycznych) w swoich subskrypcjach w miesiącu Febrauary 2020 (miesiąc przed rozpoczęciem deprecation) są uważani za nowych klientów. 

## <a name="what-is-the-definition-of-an-existing-customer-on-iaas-virtual-machines-classic"></a>Jaka jest definicja istniejącego klienta na maszynach wirtualnych IaaS (klasyczny)?

Klient, który w lutym 2020 r. aktywował lub zatrzymał, ale przydzielił maszyny wirtualne IaaS (Classic) w swoich subskrypcjach, jest uważany za istniejącego klienta. Tylko ci klienci mają czas do 1 marca 2023 r. na migrację maszyn wirtualnych z usługi Azure Service Manager do usługi Azure Resource Manager. 

## <a name="why-am-i-getting-an-error-stating-newclassicvmcreationnotallowedforsubscription"></a>Dlaczego pojawia się komunikat o błędzie "NewClassicVMCreationNotAllowedForSubscription"?

W ramach procesu wycofywania maszyna wirtualna IaaS (klasyczna) nie jest już dostępna dla nowych klientów. Zidentyfikowaliśmy Cię jako nowych klientów i dlatego twoja operacja nie została autoryzowana. Zdecydowanie zalecamy używanie [maszyn wirtualnych platformy Azure przy użyciu usługi ARM](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell). Jeśli nie można używać maszyn wirtualnych platformy Azure przy użyciu usługi ARM, skontaktuj się z pomocą techniczną w celu umieszczenia na białej liście subskrypcji.

## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>Czy ten plan migracji wpływa na moje istniejące usługi lub aplikacje uruchomione na maszynach wirtualnych platformy Azure? 

Dopiero 1 marca 2023 r. dla maszyn wirtualnych IaaS (klasyczny). Maszyny wirtualne IaaS (klasyczne) są w pełni obsługiwane usługi w ogólnej dostępności. Możesz nadal używać tych zasobów do zwiększenia Twojej obecności na platformie Microsoft Azure. 1 marca 2023 r. te maszyny wirtualne zostaną całkowicie wycofane, a wszystkie aktywne lub przydzielone maszyny wirtualne zostaną zatrzymane & cofnięte. Nie będzie miało wpływu na inne klasyczne zasoby, takie jak usługi w chmurze (klasyczne), konta magazynu (klasyczne) itp.   

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>Co stanie się z moimi maszynami wirtualnymi, jeśli nie planuję migracji w najbliższej przyszłości? 

1 marca 2023 r. maszyny wirtualne IaaS (Classic) zostaną całkowicie wycofane, a wszystkie aktywne lub przydzielone maszyny wirtualne zostaną zatrzymane & cofnięte. Aby zapobiec wpływowi na działalność, zalecamy rozpoczęcie planowania migracji już dziś i ukończenie jej przed 1 marca 2023 r. Nie przestarzałe istniejących klasycznych interfejsów API, usług w chmurze i modelu zasobów. Chcemy ułatwić migrację, biorąc pod uwagę zaawansowane funkcje, które są dostępne w modelu wdrażania usługi Resource Manager. Zaleca się rozpoczęcie planowania migracji tych zasobów do usługi Azure Resource Manager. 

## <a name="what-does-this-migration-plan-mean-for-my-existing-tooling"></a>Co oznacza ten plan migracji dla moich istniejących narzędzi? 

Aktualizacja narzędzi do modelu wdrażania usługi Resource Manager to jedna z najważniejszych zmian, które należy uwzględnić w planach migracji.

## <a name="how-long-will-the-management-plane-downtime-be"></a>Jak długo będzie trwał przestój płaszczyzny zarządzania? 

To zależy od liczby migrowanych zasobów. W przypadku mniejszych wdrożeń (kilkadziesiąt maszyn wirtualnych) cała migracja powinna trwać krócej niż godzinę. W przypadku wdrożeń na dużą skalę (setki maszyn wirtualnych) migracja może zająć kilka godzin.

## <a name="can-i-roll-back-after-my-migrating-resources-are-committed-in-resource-manager"></a>Czy mogę wykonać wycofanie po zatwierdzeniu migrowanych zasobów w usłudze Resource Manager? 

Możesz przerwać migrację, o ile zasoby są w stanie przygotowania. Wycofanie nie jest obsługiwane po pomyślnym zmigrowaniu zasobów za pomocą operacji zatwierdzenia.

## <a name="can-i-roll-back-my-migration-if-the-commit-operation-fails"></a>Czy mogę wycofać migrację w przypadku niepowodzenia operacji zatwierdzenia? 

Nie można przerwać migracji, jeśli operacja zatwierdzenia nie powiedzie się. Wszystkie operacje migracji, w tym operacja zatwierdzenia, są idempotentne. Dlatego zalecamy podjęcie ponownej próby wykonania operacji po chwili. Jeśli nadal napotkasz błąd, utwórz bilet pomocy technicznej.

## <a name="do-i-have-to-buy-another-express-route-circuit-if-i-have-to-use-iaas-under-resource-manager"></a>Czy muszę kupić kolejny obwód usługi ExpressRoute, jeśli muszę używać infrastruktury IaaS w ramach usługi Resource Manager? 

Nie. Ostatnio umożliwiliśmy [przenoszenie obwodów usługi ExpressRoute z klasycznego modelu wdrażania do modelu wdrażania usługi Resource Manager](../articles/expressroute/expressroute-move.md). Nie musisz kupować nowego obwodu usługi ExpressRoute, jeśli masz już taki.

## <a name="what-if-i-had-configured-role-based-access-control-policies-for-my-classic-iaas-resources"></a>Co stanie się, jeśli mam skonfigurowane zasady kontroli dostępu opartej na rolach dla moich klasycznych zasobów IaaS? 

Podczas migracji zasoby są przekształcane z klasycznych na zasoby usługi Resource Manager. Dlatego zalecamy zaplanowanie aktualizacji zasad RBAC, które należy wykonać po migracji.

## <a name="i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>Kopię zapasową moich klasycznych maszyn wirtualnych w przechowalni. Czy mogę migrować maszyny wirtualne z trybu klasycznego do trybu usługi Resource Manager i chronić je w magazynie usługi Recovery Services?

Po przeniesieniu maszyny Wirtualnej z trybu klasycznego do Resource Manager kopie zapasowe wykonane przed migracją nie zostaną przeniesione do nowo zmigrowanych maszyn wirtualnych Menedżera zasobów. Jeśli jednak chcesz zachować kopie zapasowe klasycznych maszyn wirtualnych, wykonaj następujące kroki przed migracją. 

1. W magazynie usług odzyskiwania przejdź do karty **Elementy chronione** i wybierz maszynę wirtualną. 
2. Kliknij pozycję Zatrzymaj ochronę. Pozostaw opcję *Usuń powiązane dane kopii zapasowych***niezaznaczoną**.

> [!NOTE]
> Opłata za wystąpienie kopii zapasowej zostanie naliczona do momentu przechowywania danych. Kopie zapasowe zostaną przycięte zgodnie z zakresem przechowywania. Jednak ostatnia kopia zapasowa jest zawsze przechowywana, dopóki nie usuniesz jawnie danych kopii zapasowej. Zaleca się, aby sprawdzić zakres przechowywania maszyny wirtualnej i wyzwolić "Usuń dane kopii zapasowej" na chronionym elemencie w magazynie po zakończeniu zakresu przechowywania. 
>
>

Aby przeprowadzić migrację maszyny wirtualnej do trybu Menedżera zasobów, 

1. Usuń rozszerzenie kopii zapasowej/migawki z maszyny wirtualnej.
2. Przeprowadź migrację maszyny wirtualnej z trybu klasycznego do trybu usługi Resource Manager. Upewnij się, że informacje o magazynie i sieci odpowiadające maszynie wirtualnej również zostały zmigrowane do trybu usługi Resource Manager.

Ponadto jeśli chcesz wykonać kopię zapasową zmigrowanych maszyn wirtualnych, przejdź do bloku zarządzania maszyną wirtualną, aby [włączyć kopię zapasową](../articles/backup/quick-backup-vm-portal.md#enable-backup-on-a-vm).

## <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-migration"></a>Czy mogę zwalidować moją subskrypcję lub moje zasoby, aby sprawdzić, czy można je zmigrować? 

Tak. W przypadku opcji migracji obsługiwanej przez platformę pierwszym krokiem przygotowania do migracji jest zwalidowanie zasobów pod kątem możliwości ich migracji. Jeśli operacja walidacji nie powiedzie się, zostaną zgłoszone komunikaty dla wszystkich przyczyn uniemożliwiających wykonanie migracji.

## <a name="what-happens-if-i-run-into-a-quota-error-while-preparing-the-iaas-resources-for-migration"></a>Co stanie się, jeśli wystąpi błąd limitu przydziału podczas przygotowywania zasobów IaaS do migracji? 

Zalecamy przerwanie migracji, a następnie zgłoszenie żądania pomocy technicznej w celu zwiększenia limitów przydziału w regionie, w którym są migrowane maszyny wirtualne. Po zatwierdzeniu żądania dotyczącego limitu przydziału możesz ponownie rozpocząć wykonywanie kroków migracji.

## <a name="how-do-i-report-an-issue"></a>Jak mogę zgłosić problem? 

Problemy i pytania dotyczące migracji można publikować na naszym [forum maszyn wirtualnych](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows), używając słowa kluczowego ClassicIaaSMigration. Zalecamy publikowanie wszystkich pytań na tym forum. Jeśli masz umowę pomocy technicznej, możesz także zgłosić bilet pomocy technicznej.

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>Co zrobić, jeśli nie podobają mi się nazwy zasobów wybrane przez platformę podczas migracji? 

Wszystkie zasoby z nazwami jawnie podanymi w klasycznym modelu wdrażania zostaną zachowane podczas migracji. W niektórych przypadkach zostaną utworzone nowe zasoby. Na przykład dla każdej maszyny wirtualnej zostanie utworzony interfejs sieciowy. Obecnie nie obsługujemy możliwości kontrolowania nazw nowych zasobów tworzonych podczas migracji. Zagłosuj na tę funkcję na [forum opinii platformy Azure](https://feedback.azure.com).

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>Czy mogę zmigrować obwody usługi ExpressRoute używane w subskrypcjach wraz z łączami autoryzacji? 

Obwodów usługi ExpressRoute używających łącz autoryzacji między subskrypcjami nie można zmigrować automatycznie bez przestoju. Udostępniamy przewodnik z opisem wykonywania takiej migracji ręcznie. Kroki i więcej informacji zawiera temat [Migrate ExpressRoute circuits and associated virtual networks from the classic to the Resource Manager deployment model](../articles/expressroute/expressroute-migration-classic-resource-manager.md) (Migrowanie obwodów usługi ExpressRoute i skojarzonych sieci wirtualnych z modelu wdrażania klasycznego do modelu usługi Resource Manager).

## <a name="i-got-the-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated"></a>Dostałem komunikat *"Maszyna wirtualna zgłasza ogólny stan agenta jako Nie gotowy. W związku z tym maszyny Wirtualnej nie można migrować. Upewnij się, że agent maszyny Wirtualnej zgłasza ogólny stan agenta jako Gotowy"* lub *"Maszyna wirtualna zawiera rozszerzenie, którego stan nie jest zgłaszany z maszyny Wirtualnej. W związku z tym tej maszyny Wirtualnej nie można migrować."*

Ten komunikat jest zgłaszany, jeśli maszyna wirtualna nie ma połączenia wychodzącego do Internetu. Agent maszyny wirtualnej używa połączenia wychodzącego, aby uzyskać dostęp do konta usługi Azure Storage na potrzeby aktualizacji stanu agenta co pięć minut.
