---
title: StorSimple 8000 Series Update 4 — informacje o wersji | Dokumentacja firmy Microsoft
description: W tym artykule opisano nowe funkcje, problemy i rozwiązania dla usługi StorSimple 8000 Series Update 4.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60530993"
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>StorSimple 8000 Series Update 4 — informacje o wersji

## <a name="overview"></a>Omówienie

Poniższe informacje o wersji opisano nowe funkcje i zidentyfikować krytyczne nierozwiązane problemy dla usługi StorSimple 8000 Series Update 4. Zawierają one również listę aktualizacji oprogramowania StorSimple, które są zawarte w tej wersji. 

Update 4 można zastosować na dowolne urządzenie StorSimple z wersji (GA) lub aktualizacji 0.1 za pośrednictwem Update 3.1. Wersja urządzenia skojarzone z aktualizacją Update 4 jest 6.3.9600.17820.

Przejrzyj informacje zawarte w informacjach o wersji przed wdrożeniem aktualizacji w rozwiązaniu StorSimple.

> [!IMPORTANT]
> * Aktualizacja 4 ma oprogramowania urządzenia, oprogramowania układowego USM, LSI sterowników i oprogramowania układowego, oprogramowania układowego dysku, Storport i Spaceport, zabezpieczeń i inne aktualizacje systemu operacyjnego. Trwa około 4 godziny, aby zainstalować tę aktualizację. Aktualizacja oprogramowania układowego dysku jest szkodliwe aktualizacja, która powoduje przestój dla Twojego urządzenia. Firma Microsoft zaleca się zastosowanie aktualizacji Update 4 w celu zapewnienia aktualności urządzenia. 
> * Do nowych wersji, mogą nie być widoczne aktualizacje od razu, ponieważ robimy etapowego wdrażania aktualizacji. Poczekaj kilka dni, a następnie skanowanie w poszukiwaniu aktualizacji ponownie jako te staną się dostępne wkrótce.

## <a name="whats-new-in-update-4"></a>Co nowego w aktualizacji Update 4

W aktualizacji Update 4 wprowadzono następujące ulepszenia klucza i poprawek błędów oprogramowania.

* **Inteligentniejsze zautomatyzowane algorytmy odzyskiwanie miejsca** — w ramach aktualizacji 4, odzyskiwanie miejsca automatycznych, algorytmy są rozszerzone, aby dopasować cykle odzyskiwanie miejsca oparte na oczekiwany odzyskiwanego miejsca dostępnego w chmurze. 

* **Ulepszenia wydajności dla woluminów przypiętych lokalnie** — Update 4 została zwiększona wydajność woluminy przypięte lokalnie w scenariuszach, które mają pozyskiwanie dużej ilości danych (dane do rozmiaru woluminu).

* **Przywracanie oparte na mapę cieplną** — we wcześniejszych wersjach, odzyskiwania po awarii (DR), następujące dane zostały przywrócone z chmury na podstawie wzorców dostępu, co spowoduje niską wydajność. 

    Nowa funkcja jest zaimplementowana w aktualizacji Update 4 ścieżki będzie często używanych danych, aby utworzyć mapę cieplną, gdy urządzenie jest w użyciu przed odzyskiwania po awarii (najczęściej używanych fragmentów danych mają ciepło używane mniejsze fragmenty ma niski cieplna). Po odzyskiwania po awarii usługa StorSimple używa cieplnej, aby automatycznie przywrócić i przywrócenia z magazynu trwałego danych z chmury. 

    Wszystkie operacje przywracania są teraz Przywracanie na podstawie mapy cieplnej. Aby uzyskać więcej informacji na temat sposobu wykonywania zapytań i anulować zadania ponownego wypełniania i przywracanie na podstawie mapy cieplnej, przejdź do [programu Windows PowerShell dla StorSimple Dokumentacja poleceń cmdlet](https://technet.microsoft.com/library/dn688168.aspx).

* **Narzędzia do diagnostyki usługi StorSimple** — w aktualizacji Update 4 narzędzia do diagnostyki usługi StorSimple został wydany w sposób umożliwiający łatwe diagnozowanie i rozwiązywanie problemów związanych z kondycja składników systemu, sieci, wydajność i sprzętu. To narzędzie jest uruchamiane za pomocą programu Windows PowerShell dla usługi StorSimple. Aby uzyskać więcej informacji, przejdź do [Rozwiązywanie problemów przy użyciu narzędzia do diagnostyki usługi StorSimple](storsimple-8000-diagnostics.md).

* **Oparty na interfejsie użytkownika narzędzia do migracji usługi StorSimple** — przed tą wersją migracji danych z urządzeń serii 5000 – 7000 wymaganych użytkowników do uruchomienia w ramach przepływu pracy migracji za pomocą interfejsu programu Azure PowerShell. W tej wersji narzędzie migracji usługi StorSimple opartego na interfejsie użytkownika łatwy w użyciu jest udostępniana do pomocy technicznej w celu ułatwienia ten sam przepływ pracy migracji. To narzędzie umożliwia również konsolidacji zasobników odzyskiwania. 

* **Zmiany dotyczące FIPS** — w tej wersji lub nowszy, Standard FIPS jest włączony domyślnie na wszystkich urządzeniach serii StorSimple 8000, dla kont w chmurze publicznej platformy Microsoft Azure Government i platformy Azure.

* **Aktualizowanie zmian** — w tej wersji Naprawiono usterki związane z aktualizacji błędów.

* **Alert dotyczący błędów dysku** — w tym wydaniu dodano jest nowy alert, które ostrzega użytkownika o zbliżającym się awarii dysku. Jeśli napotkasz ten alert, skontaktuj się z Microsoft Support do wysłania dysku zastępczego. Aby uzyskać więcej informacji, przejdź do [alerty sprzętu na urządzeniu StorSimple](storsimple-8000-manage-alerts.md#hardware-alerts).

* **Zmiany zastępczego kontrolera** — polecenia cmdlet, które umożliwia użytkownikowi wykonać zapytanie o stan procesu wymiany kontrolera zostanie dodany do tej wersji. Aby uzyskać więcej informacji, przejdź do [polecenia cmdlet, aby stan zastępczego kontrolera zapytania](https://technet.microsoft.com/library/dn688168.aspx).


## <a name="issues-fixed-in-update-4"></a>Problemy rozwiązane w aktualizacji Update 4

Poniższa tabela zawiera podsumowanie problemów, które zostały rozwiązane w aktualizacji Update 4.    

| Nie | Cecha | Problem | Stosuje się do urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- |
| 1 |Tryb failover |W poprzedniej wersji po włączeniu trybu failover, wystąpił problem związany z oczyszczaniem zaobserwowane w lokacji klienta. Ten problem jest rozwiązany w tej wersji. |Yes |Yes |
| 2 |Lokalnie przypięte woluminy |W poprzedniej wersji wystąpił problem do tworzenia powiązanych woluminu dla woluminów przypiętych lokalnie, które mogłyby spowodować błędy tworzenia woluminu. Ten problem został spowodowany przez główny i rozwiązane w tej wersji. |Yes |Nie |
| 3 |Pakiet dla pomocy technicznej |W poprzedniej wersji wystąpiły problemy związane z pakietu dla pomocy technicznej, które mogłyby spowodować wyjątek System.OutOfMemory lub inne błędy uniemożliwiające tworzenie pakietu pomocy technicznej. Te błędy są rozwiązane w tej wersji. |Yes |Yes |
| 4 |Monitorowanie |W poprzedniej wersji istnieje problem związany z monitorowaniem wykresy lokalnie przypięte woluminy gdzie zużycie został wyświetlony w EB. Ten problem zostanie rozwiązany w tej wersji. |Yes |Yes |
| 5 |Migracja |W poprzedniej wersji wystąpiły problemy kilka niezawodności migracji z serii 5000 – 7000 do urządzenia z serii 8000. W tej wersji rozwiązano tych problemów. |Yes |Yes |
| 6 |Aktualizacja |W poprzednich wersjach, jeśli wystąpił błąd aktualizacji, kontrolery przejdzie do trybu odzyskiwania i dlatego nie można kontynuować aktualizacji użytkownika, a musi skontaktować się z Microsoft Support. <br> To zachowanie zostało zmienione w tej wersji. Jeśli użytkownik ma Niepowodzenie aktualizacji po oba kontrolery są uruchomiona ta sama wersja (aktualizacji Update 4), kontrolery nie przejdą w tryb odzyskiwania. Jeśli użytkownik napotka tego błędu, firma Microsoft zaleca, poczekaj, aż bitu, a następnie ponów próbę aktualizacji. Ponowienie próby może zakończyć się pomyślnie. Jeśli ponowienie próby nie powiedzie się, następnie mogą kontaktować się Microsoft Support. |Yes |Yes |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Znane problemy w aktualizacji Update 4 z poprzednich wersji

Nie istnieją żadne nowe znane problemy w aktualizacji Update 4. Aby uzyskać listę problemów przenoszone do wersji Update 4 z poprzednich wersji, przejdź do [informacje o wersji Update 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>Magistrali Serial attached SCSI (SAS) kontroler aktualizacje oprogramowania układowego i w aktualizacji Update 4

Ta wersja ma kontrolera SAS i LSI aktualizacje oprogramowania układowego i sterowników. Aby uzyskać więcej informacji na temat sposobu instalowania tych aktualizacji, zobacz [instalowanie aktualizacji Update 4](storsimple-install-update-4.md) na urządzeniu StorSimple.

## <a name="virtual-device-updates-in-update-4"></a>Aktualizacje urządzenia wirtualnego w aktualizacji Update 4

Ta aktualizacja nie można zastosować do urządzenia StorSimple w chmurze (znany także jako urządzenie wirtualne). Nowe urządzenia wirtualnego należy ma zostać utworzony. 

## <a name="next-step"></a>Następny krok

Dowiedz się, jak [instalowanie aktualizacji Update 4](storsimple-install-update-4.md) na urządzeniu StorSimple.

