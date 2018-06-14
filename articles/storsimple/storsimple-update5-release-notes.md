---
title: Informacje o wersji StorSimple 8000 serii aktualizacji 5 | Dokumentacja firmy Microsoft
description: Opisuje nowe funkcje, problemy i rozwiązania StorSimple 8000 serii aktualizacji 5.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: 672757e82bcf645b705f46a9975e09c9dc5eef92
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2017
ms.locfileid: "24518923"
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>Informacje o wersji StorSimple 8000 serii aktualizacji 5

## <a name="overview"></a>Omówienie

Poniższe informacje o wersji opisano nowe funkcje i zidentyfikować problemy otwarte krytyczne dla StorSimple 8000 serii aktualizacji 5. Zawierają one również listę aktualizacji oprogramowania StorSimple zawarte w tej wersji.

Można zastosować aktualizacji 5, do każdego urządzenia StorSimple systemem Update 0.1 za pomocą aktualizacji w wersji 4. Wersja urządzenia skojarzone z aktualizacją 5 jest 6.3.9600.17845.

Przejrzyj informacje zawarte w informacjach o wersji przed wdrożeniem aktualizacji w rozwiązaniu StorSimple.

> [!IMPORTANT]
> * Aktualizacja 5 jest to aktualizacja wymagana i musi być zainstalowana natychmiast. Aby uzyskać więcej informacji, zobacz temat jak [zastosowania aktualizacji 5](storsimple-8000-install-update-5.md).
> * Aktualizacja 5 ma oprogramowanie urządzenia, oprogramowania układowego dysku zabezpieczeń systemu operacyjnego i inne aktualizacje systemu operacyjnego. Trwa około 4 godziny, aby zainstalować tę aktualizację. Aktualizacja oprogramowania układowego dysku jest destrukcyjne aktualizacji i powoduje przestój dla danego urządzenia. Firma Microsoft zaleca się zastosowanie aktualizacji 5 w celu zapewnienia aktualności urządzenia.
> * Dostępność nowych wersji nie może wyświetlić aktualizacje od razu, ponieważ jak etapowego wdrażania aktualizacji. Poczekaj kilka dni, a następnie skanowanie w poszukiwaniu aktualizacji ponownie jako tych aktualizacji zostanie wkrótce udostępnione.

## <a name="whats-new-in-update-5"></a>Nowości w aktualizacji 5

Następujące ulepszenia klucza i poprawki zostały wprowadzone w aktualizacji 5.

* **Użycie programu Azure Active Directory (AAD) do uwierzytelniania w usłudze Menedżer StorSimple urządzenia** — używane do uwierzytelniania w usłudze Menedżer urządzeń StorSimple z aktualizacją 5 lub nowszej, Azure Active Directory. Stary mechanizmu uwierzytelniania zostaną wycofane przez grudnia 2017 r. Wszyscy użytkownicy muszą zawrzeć nowe adresy URL uwierzytelniania w ich reguły zapory. Aby uzyskać więcej informacji, przejdź do [uwierzytelnianie adresów URL na liście wymagań sieciowych dla urządzenia StorSimple](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal).

    Jeśli adres URL uwierzytelniania nie jest uwzględniony w zasadach zapory, użytkownicy będą widzieć alert krytyczny, który ich urządzenia StorSimple nie można uwierzytelnić w usłudze. Użytkownicy widzą ten alert, muszą zawierać nowy adres URL uwierzytelniania. Aby uzyskać więcej informacji, przejdź do [StorSimple sieci alerty](storsimple-8000-manage-alerts.md#networking-alerts).

* **Nowa wersja programu StorSimple Snapshot Manager** — nowa wersja programu StorSimple Snapshot Manager jest publikowana z aktualizacją 5 i jest zgodny ze wszystkimi urządzeniami StorSimple uruchomionych aktualizacji 4 lub nowszej. Zaleca się zaktualizowanie do tej wersji. Poprzednia wersja programu StorSimple Snapshot Manager jest używany dla urządzenia StorSimple, które działają aktualizacji 3 lub wcześniej. [Pobierz odpowiednią wersję programu StorSimple Snapshot Manager](https://www.microsoft.com/en-us/download/details.aspx?id=44220) i zapoznaj się z [wdrażanie StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).


## <a name="issues-fixed-in-update-5"></a>Problemy rozwiązane w ramach aktualizacji 5

Poniższa tabela zawiera podsumowanie problemów, które zostały usunięte w aktualizacji 5.

| Nie | Funkcja | Problem | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- |
| 1 |Komunikacji zdalnej programu Windows PowerShell |W poprzednich wersjach użytkownik otrzyma błąd podczas próby ustanowienia połączenia zdalnego do urządzenia StorSimple chmury za pomocą środowiska Windows PowerShell. Ten problem został spowodowany głównego i stałym w tej wersji. |Nie |Tak |
| 2 |Szablony przepustowości |Wystąpił problem z szablonami przepustowości, które spowodowało mniejszej przepustowości od urządzenia skonfigurowano dla, w starszej wersji. Ten problem zostanie rozwiązany w tej wersji. |Tak |Tak |
| 3 |Tryb failover |W poprzedniej wersji urządzenia z dużą liczbą woluminów nie powiodło się za pośrednictwem innego urządzenie z systemem aktualizacji 4 procesu może zakończyć się niepowodzeniem podczas próby zastosowania rekordy kontroli dostępu. Tego problemu w tej wersji. |Tak |Tak |



## <a name="known-issues-in-update-5-from-previous-releases"></a>Znane problemy w aktualizacji 5 z poprzednich wersji

Nie występują nowe znane problemy w aktualizacji 5. Aby uzyskać listę problemów przenoszone do aktualizacji 5 z poprzednich wersji, przejdź do [wersji Update 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Magistrali Serial attached SCSI (SAS) kontrolera aktualizacje w aktualizacji 5

Ta wersja zawiera kontroler SAS i LSI aktualizacje sterowników i oprogramowania układowego. Aby uzyskać więcej informacji na temat sposobu instalowania tych aktualizacji, zobacz [zainstalować aktualizacji 5](storsimple-8000-install-update-5.md) na urządzeniu StorSimple.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>Aktualizacje urządzenia chmury StorSimple w aktualizacji 5

Ta aktualizacja nie można zastosować do urządzenia chmury StorSimple (znanej także jako urządzenia wirtualnego). Nowe urządzenia w chmurze muszą zostać utworzone przy użyciu obrazu aktualizacji 5. Aby uzyskać informacje na temat tworzenia urządzenia chmury StorSimple, przejdź do [wdrażanie i zarządzanie nimi urządzenia chmury StorSimple](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Następny krok

Dowiedz się, jak [zainstalować aktualizacji 5](storsimple-8000-install-update-5.md) na urządzeniu StorSimple.

