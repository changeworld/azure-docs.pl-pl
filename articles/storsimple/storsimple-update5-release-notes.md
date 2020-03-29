---
title: StorSimple 8000 Series Update 5 informacje o wersji
description: W tym artykule opisano nowe funkcje, problemy i obejścia aktualizacji 5 storsimple 8000 Series.
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: 4fed65e12a166c046a0c63d7c4849f86805bbe9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275167"
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>StorSimple 8000 Series Update 5 informacje o wersji

## <a name="overview"></a>Omówienie

W poniższych informacjach o wersji opisano nowe funkcje i identyfikować krytyczne otwarte problemy dla StorSimple 8000 Series Update 5. Zawierają one również listę aktualizacji oprogramowania StorSimple zawartych w tej wersji.

Aktualizacja 5 może być stosowana do dowolnego urządzenia StorSimple z aktualizacją 0.1 do aktualizacji 4. Wersja urządzenia skojarzona z aktualizacją 5 to 6.3.9600.17845.

Przejrzyj informacje zawarte w informacjach o wersji przed wdrożeniem aktualizacji w rozwiązaniu StorSimple.

> [!IMPORTANT]
> * Aktualizacja 5 jest obowiązkową aktualizacją i musi zostać zainstalowana natychmiast. Aby uzyskać więcej informacji, zobacz jak [zastosować aktualizację 5](storsimple-8000-install-update-5.md).
> * Aktualizacja 5 ma oprogramowanie urządzenia, oprogramowanie układowe dysku, zabezpieczenia systemu operacyjnego i inne aktualizacje systemu operacyjnego. Instalacja tej aktualizacji trwa około 4 godzin. Aktualizacja oprogramowania układowego dysku jest przełomową aktualizacją, która powoduje przestoje urządzenia. Zalecamy zastosowanie aktualizacji 5, aby urządzenie było aktualne.
> * W przypadku nowych wersji aktualizacje mogą nie być widoczne natychmiast, ponieważ wprowadzamy aktualizacje stopniowo. Poczekaj kilka dni, a następnie ponownie przeskanuj w poszukiwaniu aktualizacji, ponieważ te aktualizacje będą wkrótce dostępne.

## <a name="whats-new-in-update-5"></a>Co nowego w aktualizacji 5

W aktualizacji 5 wprowadzono następujące ulepszenia i poprawki błędów.

* **Korzystanie z usługi Azure Active Directory (AAD) do uwierzytelniania za pomocą usługi StorSimple Device Manager** — od aktualizacji 5 usługa Azure Active Directory jest używana do uwierzytelniania za pomocą usługi StorSimple Device Manager. Stary mechanizm uwierzytelniania zostanie przestarzały do grudnia 2017 r. Wszyscy użytkownicy muszą dołączyć nowe adresy URL uwierzytelniania do reguł zapory. Aby uzyskać więcej informacji, przejdź do [adresów URL uwierzytelniania wymienionych w wymaganiach sieciowych dla urządzenia StorSimple](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal).

    Jeśli adres URL uwierzytelniania nie jest uwzględniony w regułach zapory, użytkownicy zobaczą alert krytyczny, że ich urządzenie StorSimple nie może uwierzytelnić się za pomocą usługi. Jeśli użytkownicy zobaczą ten alert, muszą dołączyć nowy adres URL uwierzytelniania. Aby uzyskać więcej informacji, przejdź do [storsimple alerty sieci .](storsimple-8000-manage-alerts.md#networking-alerts)

* **Nowa wersja StorSimple Snapshot Manager** — nowa wersja StorSimple Snapshot Manager jest wydana z aktualizacją 5 i jest zgodna ze wszystkimi urządzeniami StorSimple, na których jest uruchomiona aktualizacja 4 lub nowsza. Zaleca się aktualizację do tej wersji. Poprzednia wersja StorSimple Snapshot Manager jest używana dla urządzeń StorSimple, na których jest uruchomiona aktualizacja 3 lub wcześniejsza. [Pobierz odpowiednią wersję StorSimple Snapshot Manager](https://www.microsoft.com/en-us/download/details.aspx?id=44220) i zapoznaj się z [poleceniem wdrożenia Menedżera migawek StorSimple](storsimple-snapshot-manager-deployment.md).


## <a name="issues-fixed-in-update-5"></a>Problemy rozwiązane w aktualizacji 5

Poniższa tabela zawiera podsumowanie problemów, które zostały rozwiązane w aktualizacji 5.

| Nie | Funkcja | Problem | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- |
| 1 |Ponowne formatowanie programu Windows PowerShell |W poprzedniej wersji użytkownik otrzyma błąd podczas próby nawiązania zdalnego połączenia z urządzeniem StorSimple Cloud Appliance za pośrednictwem programu Windows PowerShell. Ten problem został spowodowany przez roota i naprawiony w tej wersji. |Nie |Tak |
| 2 |Szablony przepustowości |We wcześniejszej wersji wystąpił problem z szablonami przepustowości, który powodował mniejszą przepustowość niż to, dla którego urządzenie zostało skonfigurowane. Ten problem został rozwiązany w tej wersji. |Tak |Tak |
| 3 |Tryb failover |W poprzedniej wersji, gdy urządzenie z dużą liczbą woluminów zostało przejęte awaryjnie na innym urządzeniu z aktualizacją 4, proces zakończy się niepowodzeniem podczas próby zastosowania rekordów kontroli dostępu. Ten problem został rozwiązany w tej wersji. |Tak |Tak |



## <a name="known-issues-in-update-5-from-previous-releases"></a>Znane problemy w aktualizacji 5 z poprzednich wersji

W aktualizacji 5 nie ma żadnych nowych znanych problemów. Aby uzyskać listę problemów przeniesionych do aktualizacji 5 z poprzednich wersji, przejdź do [aktualizacji 3 informacje o wersji](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Dołączone szeregowo aktualizacje kontrolera SCSI (SAS) i oprogramowania układowego w aktualizacji 5

W tej wersji dostępne są aktualizacje sterowników i oprogramowania układowego typu SAS oraz sterowników LSI i oprogramowania układowego. Aby uzyskać więcej informacji na temat instalowania tych aktualizacji, zobacz [instalowanie aktualizacji 5](storsimple-8000-install-update-5.md) na urządzeniu StorSimple.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>Aktualizacje urządzenia w chmurze StorSimple w aktualizacji 5

Tej aktualizacji nie można zastosować do urządzenia StorSimple Cloud Appliance (znanego również jako urządzenie wirtualne). Nowe urządzenia w chmurze muszą być tworzone przy użyciu obrazu aktualizacji 5. Aby uzyskać informacje na temat tworzenia urządzenia StorSimple Cloud Appliance, przejdź do [wdrażania urządzenia StorSimple Cloud Appliance i zarządzania nim.](storsimple-8000-cloud-appliance-u2.md)

## <a name="next-step"></a>Następny krok

Dowiedz się, jak [zainstalować aktualizację 5](storsimple-8000-install-update-5.md) na urządzeniu StorSimple.

