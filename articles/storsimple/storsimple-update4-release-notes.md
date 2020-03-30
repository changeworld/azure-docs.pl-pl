---
title: StorSimple 8000 Series Update 4 informacje o wersji | Dokumenty firmy Microsoft
description: W tym artykule opisano nowe funkcje, problemy i obejścia aktualizacji 4 storsimple 8000 Series Update 4.
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
ms.workload: TBD
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: ef95ca7b9f94690b607e37fbf5d9378c2f2bcfda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254588"
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>StorSimple 8000 Series Update 4 informacje o wersji

## <a name="overview"></a>Omówienie

W poniższych informacjach o wersji opisano nowe funkcje i identyfikować krytyczne otwarte problemy dla StorSimple 8000 Series Update 4. Zawierają one również listę aktualizacji oprogramowania StorSimple zawartych w tej wersji. 

Aktualizacja 4 może być stosowana do dowolnego urządzenia StorSimple z systemem Release (GA) lub Update 0.1 do aktualizacji 3.1. Wersja urządzenia skojarzona z aktualizacją 4 to 6.3.9600.17820.

Zapoznaj się z informacjami zawartymi w informacjach o wersji przed wdrożeniem aktualizacji w rozwiązaniu StorSimple.

> [!IMPORTANT]
> * Aktualizacja 4 ma oprogramowanie urządzenia, oprogramowanie układowe USM, sterownik LSI i oprogramowanie układowe, oprogramowanie układowe dysku, Storport i Spaceport, zabezpieczenia i inne aktualizacje systemu operacyjnego. Instalacja tej aktualizacji trwa około 4 godzin. Aktualizacja oprogramowania układowego dysku jest przełomową aktualizacją, która powoduje przestoje urządzenia. Zalecamy zastosowanie aktualizacji 4, aby urządzenie było aktualne. 
> * W przypadku nowych wersji aktualizacje mogą nie być widoczne natychmiast, ponieważ wprowadzamy aktualizacje stopniowo. Poczekaj kilka dni, a następnie przeskanuj w poszukiwaniu aktualizacji ponownie, ponieważ wkrótce staną się one dostępne.

## <a name="whats-new-in-update-4"></a>Co nowego w aktualizacji 4

W aktualizacji 4 wprowadzono następujące ulepszenia i poprawki błędów.

* **Inteligentniejsze zautomatyzowane algorytmy rekultywacji miejsca** — w aktualizacji 4 algorytmy automatycznej regeneracji przestrzeni są udoskonalone w celu dostosowania cykli regeneracji przestrzeni w oparciu o oczekiwaną odzyskaną przestrzeń dostępną w chmurze. 

* **Ulepszenia wydajności dla woluminów przypiętych lokalnie** — aktualizacja 4 poprawiła wydajność woluminów przypiętych lokalnie w scenariuszach, które mają wysokie pozyskiwania danych (dane porównywalne z rozmiarem woluminu).

* **Przywracanie oparte na heatmap** — we wcześniejszych wersjach po odzyskiwaniu po awarii (DR) dane zostały przywrócone z chmury na podstawie wzorców dostępu, co powoduje powolną wydajność. 

    Nowa funkcja jest zaimplementowana w aktualizacji 4, która śledzi często używane dane w celu utworzenia mapy cieplnej, gdy urządzenie jest używane przed dr (najczęściej używane fragmenty danych mają wysokie ciepło, podczas gdy mniej używane fragmenty mają niskie ciepło). Po dr StorSimple używa mapy cieplnej, aby automatycznie przywrócić i nawodnienia danych z chmury. 

    Wszystkie przywracane są teraz przywracane na mapie cieplnej. Aby uzyskać więcej informacji na temat wykonywania zapytań i anulowania zadań przywracania i nawadniania opartych na mapach cieplnych, przejdź do [programu Windows PowerShell dla polecenia cmdlet StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

* **Narzędzie Diagnostyka StorSimple** — w aktualizacji 4 jest wydawane narzędzie Diagnostyka StorSimple, aby umożliwić łatwe diagnozowanie i rozwiązywanie problemów związanych ze zdrowiem systemu, sieci, wydajności i komponentu sprzętowego. To narzędzie jest uruchamiane za pośrednictwem programu Windows PowerShell for StorSimple. Aby uzyskać więcej informacji, przejdź do [rozwiązywania problemów za pomocą narzędzia Diagnostyka StorSimple](storsimple-8000-diagnostics.md).

* **Narzędzie do migracji storsimple oparte na interfejsie użytkownika** — przed wydaniem tej wersji migracja danych z serii 5000–7000 wymagała od użytkowników wykonania części przepływu pracy migracji przy użyciu interfejsu programu Azure PowerShell. W tej wersji łatwe w użyciu narzędzie do migracji StorSimple oparte na interfejsie użytkownika jest dostępne dla obsługi technicznej w celu ułatwienia tego samego przepływu pracy migracji. To narzędzie pozwoliłoby również na konsolidację zasobników odzyskiwania. 

* **Zmiany związane z FIPS** — ta wersja jest domyślnie włączona na wszystkich urządzeniach z serii StorSimple 8000 dla kont w chmurze publicznej platformy Microsoft Azure i azure.

* **Zmiany aktualizacji** — w tej wersji usunęły się błędy związane z błędami aktualizacji.

* **Alert o awariach dysku** — w tej wersji dodano nowy alert ostrzegający użytkownika o zbliżających się awariach dysku. Jeśli napotkasz ten alert, skontaktuj się z pomocą techniczną firmy Microsoft, aby wysłać dysk zastępczy. Aby uzyskać więcej informacji, przejdź do [alertów sprzętowych na urządzeniu StorSimple](storsimple-8000-manage-alerts.md#hardware-alerts).

* **Zmiany zastępowania kontrolera** — polecenie cmdlet, które umożliwia użytkownikowi zapytanie o stan procesu zastępowania kontrolera jest dodawane w tej wersji. Aby uzyskać więcej informacji, przejdź do polecenia [cmdlet, aby zbadać stan zastąpienia kontrolera](https://technet.microsoft.com/library/dn688168.aspx).


## <a name="issues-fixed-in-update-4"></a>Problemy rozwiązane w aktualizacji 4

Poniższa tabela zawiera podsumowanie problemów, które zostały rozwiązane w aktualizacji 4.    

| Nie | Funkcja | Problem | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- |
| 1 |Tryb failover |We wcześniejszej wersji, po pracy awaryjnej, wystąpił problem związany z oczyszczania obserwowane w lokacji klienta. Ten problem został rozwiązany w tej wersji. |Tak |Tak |
| 2 |Lokalnie przypięte woluminy |W poprzedniej wersji wystąpił problem z tworzeniem powiązanych woluminów dla woluminów przypiętych lokalnie, co spowodowałoby błędy tworzenia woluminów. Ten problem został spowodowany przez roota i naprawiony w tej wersji. |Tak |Nie |
| 3 |Pakiet pomocy technicznej |W poprzedniej wersji wystąpiły problemy związane z pakietem pomocy technicznej, które spowodowałoby wyjątek System.OutMemory lub inne błędy powodujące niepowodzenie tworzenia pakietu pomocy technicznej. Te błędy zostały naprawione w tej wersji. |Tak |Tak |
| 4 |Monitorowanie |W poprzedniej wersji wystąpił problem związany z monitorowaniem wykresów dla woluminów przypiętych lokalnie, gdzie zużycie było wyświetlane w EB. Ten błąd został rozwiązany w tej wersji. |Tak |Tak |
| 5 |Migracja |W poprzedniej wersji było kilka problemów związanych z niezawodnością migracji z serii 5000-7000 do urządzeń z serii 8000. Te problemy zostały rozwiązane w tej wersji. |Tak |Tak |
| 6 |Aktualizacja |W poprzednich wersjach, jeśli wystąpił błąd aktualizacji, kontrolery przejdzie w tryb odzyskiwania, a zatem użytkownik nie może kontynuować aktualizacji i będzie musiał skontaktować się z pomocą techniczną firmy Microsoft. <br> To zachowanie zostało zmienione w tej wersji. Jeśli użytkownik ma błąd aktualizacji po obu kontrolerów są uruchomione tej samej wersji (Aktualizacja 4), kontrolery nie przechodzą w tryb odzyskiwania. Jeśli użytkownik napotka ten błąd, zaleca się, aby poczekać na trochę, a następnie ponów próbę aktualizacji. Ponowna próby może zakończyć się pomyślnie. Jeśli ponowna ponawianie nie powiedzie się, należy skontaktować się z pomocą techniczną firmy Microsoft. |Tak |Tak |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Znane problemy w aktualizacji 4 z poprzednich wersji

W aktualizacji 4 nie ma żadnych nowych znanych problemów. Aby uzyskać listę problemów przeniesionych do aktualizacji 4 z poprzednich wersji, przejdź do [aktualizacji 3 informacje o wersji](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>Dołączone szeregowo aktualizacje kontrolera SCSI (SAS) i oprogramowania układowego w aktualizacji 4

W tej wersji dostępne są aktualizacje sterowników i oprogramowania układowego typu SAS oraz sterowników LSI i oprogramowania układowego. Aby uzyskać więcej informacji na temat instalowania tych aktualizacji, zobacz [instalowanie aktualizacji 4](storsimple-install-update-4.md) na urządzeniu StorSimple.

## <a name="virtual-device-updates-in-update-4"></a>Aktualizacje urządzeń wirtualnych w aktualizacji 4

Tej aktualizacji nie można zastosować do urządzenia StorSimple Cloud Appliance (znanego również jako urządzenie wirtualne). Konieczne będzie utworzenie nowych urządzeń wirtualnych. 

## <a name="next-step"></a>Następny krok

Dowiedz się, jak [zainstalować aktualizację 4](storsimple-install-update-4.md) na urządzeniu StorSimple.

