---
title: StorSimple 8000 Series Update 5 — informacje o wersji | Dokumentacja firmy Microsoft
description: W tym artykule opisano nowe funkcje, problemy i rozwiązania dla usługi StorSimple 8000 Series Update 5.
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
ms.openlocfilehash: 462cbd6261723aa91bbfd23292611e758a800ed2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60844095"
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>StorSimple 8000 Series Update 5 — informacje o wersji

## <a name="overview"></a>Omówienie

Poniższe informacje o wersji opisano nowe funkcje i zidentyfikować krytyczne nierozwiązane problemy dla usługi StorSimple 8000 Series Update 5. Zawierają one również listę aktualizacji oprogramowania StorSimple, które są zawarte w tej wersji.

Aktualizację 5 można zastosować do dowolnego urządzenia StorSimple z aktualizacją Update 0.1, za pomocą aktualizacji w wersji 4. Wersja urządzenia skojarzone z aktualizacją Update 5 jest 6.3.9600.17845.

Przejrzyj informacje zawarte w informacjach o wersji przed wdrożeniem aktualizacji w rozwiązaniu StorSimple.

> [!IMPORTANT]
> * Update 5 jest obowiązkowa aktualizacja i musi być zainstalowany bezpośrednio. Aby uzyskać więcej informacji, zobacz instrukcje [zastosowania aktualizacji 5](storsimple-8000-install-update-5.md).
> * Aktualizacja 5 ma oprogramowania urządzenia, oprogramowania układowego dysku, zabezpieczenia systemu operacyjnego i inne aktualizacje systemu operacyjnego. Trwa około 4 godziny, aby zainstalować tę aktualizację. Aktualizacja oprogramowania układowego dysku jest szkodliwe aktualizacja, która powoduje przestój dla Twojego urządzenia. Firma Microsoft zaleca się zastosowanie aktualizacji Update 5 w celu zapewnienia aktualności urządzenia.
> * Do nowych wersji, mogą nie być widoczne aktualizacje od razu, ponieważ robimy etapowego wdrażania aktualizacji. Poczekaj kilka dni, a następnie skanowanie w poszukiwaniu aktualizacji ponownie jako aktualizacje te staną się dostępne wkrótce.

## <a name="whats-new-in-update-5"></a>Co nowego w aktualizacji Update 5

W aktualizacji Update 5 wprowadzono następujące ulepszenia klucza i poprawek błędów oprogramowania.

* **Korzystanie z usługi Azure Active Directory (AAD) do uwierzytelniania za pomocą usługi Menedżer urządzeń StorSimple** — używany do uwierzytelniania w usłudze Menedżer urządzeń StorSimple z aktualizacją 5 lub nowszy, Azure Active Directory. Stary mechanizm uwierzytelniania zostanie uznana za przestarzałą grudnia 2017 r. Wszyscy użytkownicy mogą zawierać nowe adresy URL uwierzytelniania w ich reguł zapory. Aby uzyskać więcej informacji, przejdź do [uwierzytelnianie adresów URL na liście wymagań sieciowych dotyczących urządzenia StorSimple](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal).

    Jeśli adres URL uwierzytelniania nie jest uwzględniony w regułach zapory, użytkownicy będą widzieć alert krytyczny, że ich urządzenia StorSimple nie mogło przeprowadzić uwierzytelniania w usłudze. Użytkownicy widzą ten alert, muszą zawierać nowy adres URL uwierzytelniania. Aby uzyskać więcej informacji, przejdź do [StorSimple sieć alerty](storsimple-8000-manage-alerts.md#networking-alerts).

* **Nowa wersja programu StorSimple Snapshot Manager** — nowa wersja programu StorSimple Snapshot Manager jest zwolniony z aktualizacją Update 5 i jest zgodne ze wszystkimi urządzeniami StorSimple, które są uruchomione Update 4 lub nowszym. Firma Microsoft zaleca aktualizację do tej wersji. Poprzednia wersja usługi StorSimple Snapshot Manager jest używane dla urządzeń StorSimple z uruchomioną aktualizacją Update 3 lub starszym. [Pobierz odpowiednią wersję programu StorSimple Snapshot Manager](https://www.microsoft.com/en-us/download/details.aspx?id=44220) i odnoszą się do [wdrażanie programu StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).


## <a name="issues-fixed-in-update-5"></a>Problemy rozwiązane w aktualizacji Update 5

Poniższa tabela zawiera podsumowanie problemów, które zostały rozwiązane w aktualizacji Update 5.

| Nie | Cecha | Problem | Stosuje się do urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- |
| 1 |Komunikacji zdalnej programu Windows PowerShell |W poprzedniej wersji użytkownik otrzyma błąd podczas próby ustanowienia połączenia zdalnego na urządzenie StorSimple w chmurze za pomocą programu Windows PowerShell. Ten problem został spowodowany przez główny i rozwiązane w tej wersji. |Nie |Tak |
| 2 |Szablony przepustowości |W starszej wersji wystąpił problem z szablonami przepustowości, które wpłynęły na mniejszej przepustowości niż co urządzenie zostało skonfigurowane dla. Ten problem został rozwiązany w tej wersji. |Yes |Tak |
| 3 |Tryb failover |W poprzedniej wersji urządzenia z dużą liczbą woluminów przełączoną w tryb failover do innego urządzenia, uruchomić aktualizację Update 4, proces może zakończyć się niepowodzeniem podczas próby zastosowania rekordy kontroli dostępu. Ten problem jest rozwiązany w tej wersji. |Yes |Tak |



## <a name="known-issues-in-update-5-from-previous-releases"></a>Znane problemy w aktualizacji Update 5 z poprzednich wersji

Nie istnieją żadne nowe znane problemy w aktualizacji Update 5. Aby uzyskać listę problemów przeniesiony na 5 aktualizacji z poprzednich wersji, przejdź do [informacje o wersji Update 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Magistrali Serial attached SCSI (SAS) kontroler aktualizacje oprogramowania układowego i w aktualizacji Update 5

Ta wersja ma kontrolera SAS i LSI aktualizacje oprogramowania układowego i sterowników. Aby uzyskać więcej informacji na temat sposobu instalowania tych aktualizacji, zobacz [instalowanie aktualizacji Update 5](storsimple-8000-install-update-5.md) na urządzeniu StorSimple.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>Aktualizacje urządzenia StorSimple w chmurze w aktualizacji Update 5

Ta aktualizacja nie można zastosować do urządzenia StorSimple w chmurze (znany także jako urządzenie wirtualne). Nowe urządzenia w chmurze muszą ma zostać utworzony przy użyciu obrazu aktualizacja Update 5. Aby uzyskać informacje na temat tworzenia urządzenia StorSimple w chmurze, przejdź do [wdrażanie i zarządzanie nimi urządzenie StorSimple w chmurze](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Następny krok

Dowiedz się, jak [instalowanie aktualizacji Update 5](storsimple-8000-install-update-5.md) na urządzeniu StorSimple.

