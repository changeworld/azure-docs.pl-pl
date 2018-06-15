---
title: Przenieść z klasycznego portalu Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: Zawiera odpowiedzi na często zadawane pytania dotyczące przenoszenia urządzeń StorSimple z klasycznego portalu Azure.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: ac57894e4f180f42f80479d2031f4dd5ddfdb1be
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2018
ms.locfileid: "27785183"
---
# <a name="move-storsimple-device-manager-service-from-classic-to-azure-portal-frequently-asked-questions-faq"></a>Przenieść usługi Menedżer StorSimple urządzenia z klasycznego portalu Azure: często zadawane pytania (FAQ)

## <a name="overview"></a>Przegląd

Poniżej przedstawiono pytania i odpowiedzi, które mogą być po przeniesieniu usługi Menedżer StorSimple urządzenie działa w klasycznym portalu Azure w portalu Azure.

Pytania i odpowiedzi są rozmieszczone w ramach następujących kategorii:

* Przenoszenie usługi Menedżer StorSimple urządzenia
* Przenoszenie konta magazynu
* Polecenia cmdlet oparte na przy użyciu usługi Azure Resource Manager
* Przy użyciu usługi Menedżer StorSimple danych
* Różne postanowienia

## <a name="moving-storsimple-device-manager-service"></a>Przenoszenie usługi Menedżer StorSimple urządzenia

### <a name="once-i-have-moved-to-azure-portal-can-i-still-create-a-storsimple-manager-service-in-the-classic-portal"></a>Raz I zostały przeniesione do portalu Azure, można nadal utworzyć usługi Menedżer StorSimple w klasycznym portalu?

Nie. Po migracji usługi Menedżer StorSimple do portalu Azure w klasycznym portalu nie można utworzyć nowej usługi. Ponadto [klasycznym portalu nie będzie dostępny z 8 stycznia 2018](https://azure.microsoft.com/updates/azure-portal-updates-for-classic-portal-users). 

### <a name="i-have-multiple-storsimple-managers-running-in-the-classic-portal-can-i-choose-which-ones-to-move-to-the-azure-portal"></a>Mam wiele menedżerów StorSimple działa w klasycznym portalu. Można wybrać te, które można przenieść do portalu Azure?

Nie. Nie można wybrać, które menedżerów StorSimple, aby przejść do portalu Azure. Wszyscy menedżerowie StorSimple w ramach Twojej subskrypcji są przenoszone.


### <a name="i-initiated-the-migration-of-my-service-to-the-new-azure-portal-should-i-delete-the-storsimple-manager-from-the-classic-portal"></a>Zainicjowane I migracji Moje usługi do nowego portalu Azure. Należy usunąć menedżera StorSimple z klasycznego portalu? 

Nie. Nie należy próbować usunąć dowolnej usługi, które zostały przeniesione z klasycznego portalu. Poczekaj, aż do ukończenia migracji, gdy wszyscy menedżerowie StorSimple przeniesione do nowego portalu, nie należy usunąć wszystkie usługi z klasycznego portalu. Po pewnym czasie klasycznego portalu jest przestarzały.

### <a name="can-i-rename-my-storsimple-device-manager-service-in-the-azure-portal"></a>Czy można zmienić moje usługi Menedżer StorSimple urządzenia w portalu Azure?

Nie. Nie można zmienić nazwy usługi, po utworzeniu usługi w portalu Azure. Takie samo zachowanie to również dla innych jednostek, takich jak urządzenia, kontenery woluminów, woluminów i zasad tworzenia kopii zapasowych.

### <a name="can-i-create-the-80108020-storsimple-cloud-appliances-with-azure-resource-manager-deployment-model"></a>Z modelu wdrażania usługi Azure Resource Manager może utworzyć urządzenia 8010/8020 StorSimple chmury?

Tak. Możesz utworzyć nowe urządzenia chmury StorSimple 8010/8020 w modelu wdrażania usługi Azure Resource Manager. Podstawowy maszyn wirtualnych dla tych urządzeń chmury są również modelu wdrażania usługi Resource Manager.

### <a name="when-we-migrate-subscriptions-to-the-azure-portal-will-the-underlying-vms-for-the-storsimple-cloud-appliances-also-migrate-to-azure-resource-management-deployment-model"></a>Możemy migracji subskrypcji w portalu Azure, będzie podstawowym maszyn wirtualnych dla urządzenia StorSimple w chmurze również przeprowadzić migrację do modelu wdrażania zarządzania zasobami Azure?

Przenieś usługi StorSimple jest niezależna od Zarządzanie maszyn wirtualnych dla urządzenia StorSimple w chmurze. Możesz mogą kompleksowo zarządzać maszyn wirtualnych w przypadku urządzeń chmury StorSimple w klasycznym i portalu Azure nawet dzisiaj.

### <a name="can-i-manage-existing-classic-80108020-storsimple-cloud-appliance-from-the-azure-portal"></a>Czy mogę zarządzać istniejących klasycznego urządzenia chmury StorSimple 8010/8020 z portalu Azure

Tak. Skojarzone z istniejące urządzenia 8010/8020 chmury maszyn wirtualnych można zarządzać za pomocą portalu Azure.

Jeśli utworzono urządzenia StorSimple chmury modelu 8010/8020 systemem 3.0 aktualizacji i powyżej, możesz nie dotyczy usługi przenoszenia do nowego portalu Azure. Można będzie w pełni zarządzać chmury urządzeń bez żadnych problemów. 

Jeśli masz urządzenia z wersjami przed 3.0 aktualizacji w klasycznym portalu, następnie należy tylko mają ograniczoną funkcjonalność dostępna chmury. Aby uzyskać więcej informacji, przejdź do [listy nieobsługiwanej operacji dla urządzenia z wersjami przed Update 3](storsimple-8000-manage-service.md##supported-operations-on-devices-running-versions-prior-to-update-50).

Urządzenia w chmurze nie można aktualizować. Użyj najnowszej wersji oprogramowania do tworzenia nowego urządzenia chmury i następnie przełączyć istniejącą kontenery woluminów do nowego urządzenia chmury utworzony. Aby uzyskać więcej informacji, przejdź do [funkcjonować w trybie awaryjnym urządzenia chmury](storsimple-8000-cloud-appliance-u2.md#fail-over-to-the-cloud-appliance)


### <a name="my-storsimple-8000-series-device-is-running-update-20-i-migrated-my-service-to-new-azure-portal-my-device-connected-successfully-but-it-seems-that-i-am-not-able-to-fully-manage-my-device-how-do-i-resolve-this-behavior"></a>Moje urządzenia z serii StorSimple 8000 działa 2.0 aktualizacji. Moje usługi I migracji do nowego portalu Azure. Moje urządzenie podłączone pomyślnie, ale wydaje się, że nie mam w pełni zarządzać urządzeniem. Jak rozwiązać ten problem?

Nowy portal Azure jest obsługiwana jedynie w przypadku urządzeń StorSimple z aktualizacji 3.0 lub nowszej. Jeśli urządzenie zostało uruchomione 2.0 aktualizacji, należy tylko mają ograniczoną funkcjonalność dostępna dla tego urządzenia. Aby uzyskać więcej informacji, przejdź do [listy nieobsługiwanej operacji dla urządzenia z wersjami przed Update 3](storsimple-8000-manage-service.md##supported-operations-on-devices-running-versions-prior-to-update-50).

Aby w pełni zarządzać urządzenie, zainstaluj najnowszą aktualizację na urządzeniu. Aby uzyskać więcej informacji, przejdź do [zainstalować aktualizacji 5](storsimple-8000-install-update-5.md).

### <a name="i-just-moved-my-storsimple-manager-service-to-the-azure-portal-i-am-seeing-some-alerts-related-to-my-device-is-this-behavior-related-to-the-move"></a>Tylko Mój usługi Menedżer StorSimple została przeniesiona do portalu Azure. Widzę niektórych alerty powiązane z urządzenia. To zachowanie jest powiązane z przeniesienie?

Nie. Przeniesienie sam nie powinno spowodować błędy lub alerty. Postępować zgodnie z zaleceniami alertu, aby rozpoznać alerty.

### <a name="i-am-using-a-storsimple-50007000-series-device-are-these-also-supported-in-the-azure-portal"></a>Korzystam z urządzenia z serii StorSimple 5000/7000. Są one również obsługiwane w portalu Azure?

Nie. Urządzeń z serii StorSimple 5000/7000 nie są obsługiwane w portalu Azure.

### <a name="i-am-planning-to-migrate-data-from-storsimple-50007000-series-device-to-a-storsimple-8000-series-device-how-does-moving-a-service-to-azure-portal-impact-my-data-migration"></a>Jestem I planowania migracji danych z urządzenia z serii StorSimple 5000/7000 do urządzenia z serii StorSimple 8000. Jak ma przechodzenia do portalu Azure usługi wpływu Moje migracji danych? 

Dane można migrować z serii StorSimple 5000/7000 urządzenia na urządzeniu z serii StorSimple 8000 systemem w portalu Azure. Umożliwienie migracji danych z serii 5000/7000 8000 serii, należy najpierw [logowania biletu pomocy technicznej firmy Microsoft Support](storsimple-8000-contact-microsoft-support.md).


## <a name="moving-subscriptions-storage-accounts-resource-groups"></a>Przenoszenie subskrypcji, konta magazynu, grupy zasobów

### <a name="can-i-move-storsimple-device-manager-from-one-subscription-to-another-subscription-in-the-azure-portal"></a>Czy mogę przenieść Menedżera urządzeń StorSimple z jedną subskrypcję do innej subskrypcji w portalu Azure

Nie. Przenoszenie usługi Menedżer StorSimple urządzenia z jedną subskrypcją do innego nie jest obsługiwane. Można wykonywać ręczny proces składający się z następujących czynności:

* Przeprowadź migrację danych poza urządzenia StorSimple.
* Wykonaj resetowania do ustawień fabrycznych urządzenia, to usuwa wszystkie dane lokalne na urządzeniu.
* Rejestrowanie urządzenia z nową subskrypcję usługi Menedżer StorSimple urządzenia.
* Migrację danych do urządzenia.

### <a name="do-i-have-to-migrate-the-storage-account-to-azure-resource-manager-deployment-model"></a>Należy ponownie przeprowadzić migrację konta magazynu do modelu wdrażania usługi Azure Resource Manager?

Nie. Kont magazynu classic może w pełni zarządzać w portalu Azure. Po przeniesieniu usługi StorSimple do portalu Azure, konta magazynu w dalszym ciągu działać jak wcześniej.

### <a name="what-happens-to-the-storage-account-after-the-service-is-migrated-to-the-azure-portal"></a>Co się dzieje na koncie magazynu po przeprowadzeniu migracji usługi do portalu Azure?

Przeniesienie usługi nie jest powiązana z zarządzania konta magazynu. Zarówno classic i kont magazynu usługi Azure Resource Manager może w pełni zarządzać w portalu Azure. Po przeniesieniu usługi do portalu Azure, urządzenie powinno być kontynuowane do uruchomienia z tego konta magazynu i nie powinna istnieć bez wpływu na dane.

### <a name="can-i-migrate-storsimple-device-manager-from-one-resource-group-to-another"></a>Można przeprowadzić migrację Menedżera urządzeń StorSimple z jednej grupy zasobów do innego

Nie. Nie można przenieść tworzone StorSimple Menedżera urządzeń z jednej grupy zasobów w innej grupie zasobów.

## <a name="using-azure-resource-manager-based-cmdlets"></a>Polecenia cmdlet oparte na przy użyciu usługi Azure Resource Manager

### <a name="i-moved-to-the-new-azure-portal-now-my-scripts-based-on-azure-classic-deployment-model-powershell-cmdlets-are-not-working-what-do-i-need-to-do"></a>Została przeniesiona do nowego portalu Azure. Teraz nie działają moje skrypty oparte na poleceń cmdlet programu PowerShell modelu wdrażania klasycznego Azure? Co należy zrobić?

Istniejące wdrożenia usługi Azure klasycznego modelu poleceń cmdlet programu PowerShell nie są obsługiwane w portalu Azure. Zaktualizuj skryptów do zarządzania urządzeniami za pomocą Menedżera zasobów Azure. Aby uzyskać więcej informacji na temat aktualizowania skrypty, przejdź do [próbek do nowego portalu Azure](https://github.com/anoobbacker/storsimpledevicemgmttools).

### <a name="i-just-moved-to-the-azure-portal-and-needed-to-roll-over-the-service-data-encryption-key-where-is-this-option-in-the-azure-portal"></a>Właśnie przeniesiona do portalu Azure i potrzebne do Przerzuć klucz szyfrowania danych usługi. W przypadku tej opcji w portalu Azure?

Opcja Przerzuć klucz szyfrowania danych usługi nie jest w portalu Azure. Aby uzyskać więcej informacji na temat tego przerzucania w portalu Azure, przejdź do [zmienić klucza szyfrowania danych usługi](storsimple-8000-manage-service.md#change-the-service-data-encryption-key).

### <a name="i-am-using-windows-powershell-for-storsimple-cmdlets-on-the-storsimple-device-to-perform-operations-such-extract-a-support-package-are-these-cmdlets-affected-when-i-move-to-the-new-azure-portal"></a>Używam programu Windows PowerShell dla poleceń cmdlet StorSimple na urządzeniu StorSimple na wykonywanie operacji takich wyodrębniania pakietu obsługi. Te polecenia cmdlet wpływ przenieść do nowego portalu Azure?

Nie. Z usługą przenoszenia do nowego portalu Azure powinien istnieć bez wpływu na środowisko Windows PowerShell dla StorSimple polecenia cmdlet związane z lokalnego urządzenia StorSimple (które nie ma wpływu na przeniesienie). Można nadal używać tych poleceń cmdlet, aby utworzyć pakiet obsługi bez żadnych problemów, nawet w portalu Azure. To przeniesienie ma wpływ tylko Azure wdrażania klasycznego modelu poleceń cmdlet programu PowerShell.

## <a name="moving-storsimple-data-manager-service"></a>Przenoszenie usługi Menedżer StorSimple danych

### <a name="i-am-using-storsimple-data-manager-service-in-classic-azure-portal-how-should-i-proceed-with-this-move"></a>Używam usługi Menedżer StorSimple danych w klasycznym portalu Azure. Jak należy kontynuować to przeniesienie?

Jeśli używasz usługi Menedżer StorSimple danych możesz zostały automatycznie przeniesione do portalu Azure.

## <a name="miscellaneous"></a>Różne postanowienia

### <a name="i-am-running-storsimple-snapshot-manager-for-my-8000-series-device-is-there-any-impact-on-storsimple-snapshot-manager-when-i-move-to-azure-portal"></a>Używam programu StorSimple Snapshot Manager urządzenia serii 8000. Istnieje już wpływu na StorSimple Snapshot Manager przy przechodzeniu do portalu Azure?

Nie. Nie ma to wpływu na StorSimple Snapshot Manager po przeniesieniu usługi do portalu Azure. Urządzenie i StorSimple Snapshot Manager nadal działać jak wcześniej.

### <a name="can-i-rename-my-storsimple-device-volume-containers-or-volumes"></a>Czy mogę zmienić nazwę Moje urządzenia StorSimple, kontenery woluminów lub woluminy?

Nie. Nie można zmienić nazwy urządzenia, woluminy, kontenery woluminów lub zasad tworzenia kopii zapasowych w portalu Azure.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [obsługiwane operacje na urządzenia z wersjami przed 5.0 aktualizacji](storsimple-8000-manage-service.md#supported-operations-on-devices-running-versions-prior-to-update-50).



