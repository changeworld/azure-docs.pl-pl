---
title: Informacje o wersji StorSimple 8000 serii aktualizacji 4 | Dokumentacja firmy Microsoft
description: Opisuje nowe funkcje, problemy i rozwiązania StorSimple 8000 serii Update 4.
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
ms.openlocfilehash: f4db5a49b71b0c384da7af2b90bd5426a4f35c72
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
ms.locfileid: "34058152"
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>Informacje o wersji StorSimple 8000 serii aktualizacji 4

## <a name="overview"></a>Przegląd

Poniższe informacje o wersji opisano nowe funkcje i zidentyfikować problemy otwarte krytyczne dla StorSimple 8000 serii Update 4. Zawierają one również listę aktualizacji oprogramowania StorSimple zawarte w tej wersji. 

Aktualizacja 4 może odnosić się do każdego urządzenia StorSimple z wersji (GA) lub Update 0.1 za pomocą aktualizacji w wersji 3.1. Wersja urządzenia skojarzone z aktualizacją Update 4 jest 6.3.9600.17820.

Przejrzyj informacje zawarte w informacjach o wersji przed wdrożeniem aktualizacji w rozwiązaniu StorSimple.

> [!IMPORTANT]
> * Aktualizacja 4 ma oprogramowanie urządzenia, oprogramowania układowego należy LSI sterowników i oprogramowania układowego, oprogramowania układowego dysku, Storport i Spaceport, zabezpieczeń i inne aktualizacje systemu operacyjnego. Trwa około 4 godziny, aby zainstalować tę aktualizację. Aktualizacja oprogramowania układowego dysku jest destrukcyjne aktualizacji i powoduje przestój dla danego urządzenia. Firma Microsoft zaleca stosowanie aktualizacji w wersji 4, aby zapewnić aktualność urządzenia. 
> * Dostępność nowych wersji nie może wyświetlić aktualizacje od razu, ponieważ jak etapowego wdrażania aktualizacji. Poczekaj kilka dni, a następnie skanowanie w poszukiwaniu aktualizacji ponownie jako te staną się dostępne wkrótce.

## <a name="whats-new-in-update-4"></a>Nowości w aktualizacji w wersji 4

W pakiecie Update 4 wprowadzono następujące ulepszenia klucza i poprawki.

* **Inteligentny automatycznego algorytmów odzyskiwanie miejsca** — w ramach aktualizacji 4, odzyskiwanie miejsca zautomatyzowane, algorytmy rozszerzono o opcję Dostosuj cykle odzyskiwanie miejsca oparte na oczekiwany regeneracji miejsca w chmurze. 

* **Ulepszenia wydajności dla woluminów przypiętych lokalnie** — aktualizacji w wersji 4 ma wyższą wydajność woluminów przypiętych lokalnie w scenariuszach, w których wprowadzanie danych wysokiej (dane można porównywać pod względem rozmiaru woluminu).

* **Przywracanie na podstawie Heatmap** -we wcześniejszych wersjach, po odzyskiwania awaryjnego (DR), dane został przywrócony z chmury, w oparciu o wzorce dostępu, co spowoduje niską wydajność. 

    Nowa funkcja jest zaimplementowana w aktualizacji 4 ścieżki będzie często używanych danych do utworzenia heatmap, gdy urządzenie jest w użyciu przed DR (najczęściej używanych fragmentów danych mają wysokie cieplna używane mniejsze fragmenty ma cieplna niski). Po odzyskiwania po awarii StorSimple używa heatmap, aby automatycznie przywrócić i rehydrate danych z chmury. 

    Wszystkie przywraca są teraz heatmap na podstawie operacji przywracania. Aby uzyskać więcej informacji na temat sposobu wykonywania zapytań i anulować zadania przywracania i preparaty nawadniające heatmap na podstawie, przejdź do [programu Windows PowerShell dla StorSimple dokumentacji poleceń cmdlet](https://technet.microsoft.com/library/dn688168.aspx).

* **Narzędzie diagnostyczne StorSimple** — aktualizacja 4, narzędzia diagnostyki StorSimple został wydany aby umożliwić łatwe diagnozowanie i rozwiązywanie problemów związanych z kondycja składnika systemu, wydajności, sieci i sprzętu. To narzędzie jest uruchamiane za pomocą programu Windows PowerShell dla urządzenia StorSimple. Aby uzyskać więcej informacji, przejdź do [Rozwiązywanie problemów przy użyciu narzędzia diagnostycznego StorSimple](storsimple-8000-diagnostics.md).

* **Oparty na interfejsie użytkownika narzędzia migracji StorSimple** — przed tą wersją migracji danych z serii 5000 7000 wymagane użytkownikom wykonywanie część przepływu pracy migracja za pomocą interfejsu programu Azure PowerShell. W tej wersji łatwego w użyciu narzędzia migracji StorSimple interfejsu użytkownika jest udostępniana do pomocy technicznej w celu ułatwienia tego samego przepływu pracy migracji. To narzędzie umożliwia również konsolidacji zasobników odzyskiwania. 

* **Zmiany dotyczące FIPS** — to zlecenia i jego nowszych wersjach, FIPS jest włączone domyślnie na wszystkich urządzeniach serii StorSimple 8000 dla kont w chmurze Microsoft Azure dla instytucji rządowych i Azure publicznego.

* **Aktualizuj zmiany** — w tej wersji Naprawiono błędy dotyczące niepowodzenia aktualizacji.

* **Alert w przypadku awarii dysku** -dodaniu nowego alertu, które ostrzega użytkownika o zbliżającym się awarii dysku w tej wersji. Jeśli wystąpi ten alert, skontaktuj się z Microsoft Support na potrzeby wysłania dysk zastępczy. Aby uzyskać więcej informacji, przejdź do [alerty sprzętu na urządzeniu StorSimple](storsimple-8000-manage-alerts.md#hardware-alerts).

* **Zmiany zastępczego kontrolera** -polecenia cmdlet, które umożliwia użytkownikowi zapytać o stan procesu wymiany kontrolera jest dodawany w tej wersji. Aby uzyskać więcej informacji, przejdź do [polecenia cmdlet zbadać kontrolera zastąpienie stanu](https://technet.microsoft.com/library/dn688168.aspx).


## <a name="issues-fixed-in-update-4"></a>Problemy rozwiązane w ramach aktualizacji w wersji 4

Poniższa tabela zawiera podsumowanie problemów, które zostały usunięte w pakiecie Update 4.    

| Nie | Cecha | Problem | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- |
| 1 |Tryb failover |W starszych wersji po pracy awaryjnej, wystąpił problem związany z oczyszczaniem odczytaną po stronie klienta. Tego problemu w tej wersji. |Yes |Yes |
| 2 |Woluminów przypiętych lokalnie |W poprzednich wersjach wystąpił problem podczas tworzenia woluminu pokrewne dla woluminów przypiętych lokalnie, które umożliwiałyby błędy tworzenia woluminu. Ten problem został spowodowany głównego i stałym w tej wersji. |Yes |Nie |
| 3 |Pakiet dla pomocy technicznej |W poprzedniej wersji wystąpiły problemy związane z pakietu pomocy technicznej, który spowoduje powstanie wyjątku System.OutOfMemory lub inne błędy uniemożliwiające tworzenia pakietu obsługi. Te błędy są ustalone w tej wersji. |Yes |Yes |
| 4 |Monitorowanie |W poprzedniej wersji istnieje problem związany z monitorowania wykresów dla lokalnie przypięty woluminów gdzie zużycie był wyświetlany w EB. Ten problem zostanie rozwiązany w tej wersji. |Yes |Yes |
| 5 |Migracja |W poprzedniej wersji wystąpiły pewne problemy związane z serii 5000 7000 do urządzeń z serii 8000 niezawodność migracji. Te problemy rozwiązano w tej wersji. |Yes |Yes |
| 6 |Aktualizacja |W poprzednich wersjach, jeśli wystąpił błąd aktualizacji, kontrolery przejdzie do trybu odzyskiwania i dlatego nie można kontynuować aktualizacji i należy skontaktować się z Microsoft Support użytkownika. <br> To zachowanie zostało zmienione w tej wersji. Jeśli użytkownik ma Niepowodzenie aktualizacji po obu kontrolerów korzystają z tej samej wersji (aktualizacja 4), kontrolery nie przechodzą w trybie odzyskiwania. Jeśli użytkownik napotka tego błędu, firma Microsoft zaleca, poczekaj, aż bit i spróbuj ponownie wykonać aktualizację. Ponów próbę, może się powieść. Jeśli próba nie powiedzie się, powinny one kontaktu Support firmy Microsoft. |Yes |Yes |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Znane problemy w pakiecie Update 4 z poprzednich wersji

Nie występują nowe znane problemy w pakiecie Update 4. Aby uzyskać listę problemów przenoszone do aktualizacji w wersji 4 z poprzednich wersji, przejdź do [wersji Update 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>Magistrali Serial attached SCSI (SAS) kontrolera aktualizacje w pakiecie Update 4

Ta wersja zawiera kontroler SAS i LSI aktualizacje sterowników i oprogramowania układowego. Aby uzyskać więcej informacji na temat sposobu instalowania tych aktualizacji, zobacz [zainstalować aktualizacji w wersji 4](storsimple-install-update-4.md) na urządzeniu StorSimple.

## <a name="virtual-device-updates-in-update-4"></a>Urządzenie wirtualne aktualizacji w pakiecie Update 4

Ta aktualizacja nie można zastosować do urządzenia chmury StorSimple (znanej także jako urządzenia wirtualnego). Nowe urządzenia wirtualnego będzie muszą zostać utworzone. 

## <a name="next-step"></a>Następny krok

Dowiedz się, jak [zainstalować aktualizacji w wersji 4](storsimple-install-update-4.md) na urządzeniu StorSimple.

