---
title: Zainstaluj aktualizację 1.0 na macierz wirtualną StorSimple | Dokumentacja firmy Microsoft
description: Opisuje sposób używania interfejsu użytkownika sieci web rozwiązania StorSimple Virtual Array do stosowania aktualizacji za pomocą usługi Azure portal i poprawki — metoda
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: fa53213e577028628d48db91704578e23888f2a8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61411573"
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>Zainstaluj aktualizację 1.0 na rozwiązania StorSimple Virtual Array

## <a name="overview"></a>Omówienie

W tym artykule opisano kroki wymagane do zainstalowania aktualizacji 1.0 na rozwiązania StorSimple Virtual Array przy użyciu witryny Azure portal i za pomocą lokalnego Interfejsu w przeglądarce.

Możesz zastosować aktualizacje oprogramowania lub poprawek w celu zapewnienia aktualności rozwiązania StorSimple Virtual Array. Przed zastosowaniem aktualizacji zaleca się wykonanie woluminy lub udziały w tryb offline na hoście pierwszy i następnie urządzenia. Minimalizuje to ryzyko uszkodzenia danych. Po woluminy lub udziały są w trybie offline, należy również wziąć ręcznego tworzenia kopii zapasowych urządzenia.

> [!IMPORTANT]
> - Aktualizacja 1.0 odnosi się do **10.0.10296.0** wersję oprogramowania na urządzeniu. Aby uzyskać informacji na temat nowości w tej aktualizacji, przejdź do [informacje o wersji dotyczące aktualizacji 1.0](storsimple-virtual-array-update-1-release-notes.md).
>
> - Należy pamiętać, który, instalowania aktualizacji lub poprawki powoduje ponowne uruchomienie urządzenia. Biorąc pod uwagę, że macierz wirtualna StorSimple jest urządzenie o jednym węźle, jest przerwane wszelkie operacje We/Wy w toku i urządzenie, wystąpi Przestój.
>
> - Aktualizacja 1 jest dostępna w witrynie Azure portal, tylko wtedy, gdy macierz wirtualna jest uruchomiona w aktualizacji Update 0.6. Dla macierzy wirtualnych wersjami 0,6 przed aktualizacją należy najpierw zainstalować aktualizacji Update 0.6, a następnie zainstaluj Update 1.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Jeśli z aktualizacją Update 0,2 lub nowszy, zaleca się zainstalowanie aktualizacji w witrynie Azure portal. Procedury portalu wymaga od użytkownika skanowania, pobieranie i instalowanie aktualizacji. W zależności od wersji oprogramowania, którego macierz wirtualna jest uruchomiona, stosowania aktualizacji za pośrednictwem witryny Azure portal jest inny.

 - Jeśli macierz wirtualna jest uruchomiona w aktualizacji Update 0.6, witryny Azure portal bezpośrednio instaluje Update 1 (10.0.10296.0) na urządzeniu. Ta procedura trwa około 7 minut.
 - Jeśli macierz wirtualna jest uruchomiona w wersji wcześniejszej niż aktualizacji Update 0.6, aktualizacja jest wykonywane w dwuetapowy. Witryna Azure portal najpierw instaluje aktualizacji Update 0.6 (10.0.10293.0) na urządzeniu. Ponowne uruchomienie macierzy wirtualnej, a następnie instaluje Update 1 (10.0.10296.0) na urządzeniu z systemem w portalu. Ta procedura trwa około 15 minut.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

Po zakończeniu instalacji przejdź do usługi Menedżer urządzeń StorSimple. Wybierz **urządzeń** a następnie wybierz i kliknij urządzenie właśnie został zaktualizowany. Przejdź do **Ustawienia > Zarządzaj > aktualizacje urządzeń**. Wersja oprogramowania wyświetlane powinna być **10.0.10296.0**.

![Wersja oprogramowania po aktualizacji](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>Użyj lokalnego internetowego interfejsu użytkownika

Istnieją dwa kroki, korzystając z lokalnego Interfejsu w przeglądarce:

* Pobieranie aktualizacji lub poprawki
* Zainstalowanie aktualizacji lub poprawki

> [!IMPORTANT] 
> **Kontynuuj tę aktualizację tylko wtedy, gdy są uruchomione aktualizacji Update 0.6 (10.0.10293.0). Jeśli używasz starszej wersji, [instalowanie aktualizacji Update 0.6](storsimple-virtual-array-install-update-06.md) na urządzeniu z systemem pierwsze, a następnie zastosować Update 1.**

### <a name="download-the-update-or-the-hotfix"></a>Pobieranie aktualizacji lub poprawki

Jeśli macierz wirtualna jest uruchomiona w aktualizacji Update 0.6, wykonaj następujące kroki, aby pobrać Update 1 z wykazu usługi Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Aby pobrać aktualizacji lub poprawki

1. Uruchom program Internet Explorer i przejdź do [ https://catalog.update.microsoft.com ](https://catalog.update.microsoft.com).

2. Jeśli korzystasz wykazu usługi Microsoft Update po raz pierwszy na tym komputerze, kliknij przycisk **zainstalować** monit o zainstalowanie dodatku wykazu usługi Microsoft Update.

3. W polu wyszukiwania wykazu usługi Microsoft Update wprowadź numer bazy wiedzy Knowledge Base (KB) poprawki, którą chcesz pobrać. Wprowadź **4047203** aktualizacji 1.0, a następnie kliknij polecenie **wyszukiwania**.
   
    Lista poprawek zawierająca pojawia się na przykład **StorSimple wirtualnego tablicy aktualizację 1.0**.
   
    ![Przeszukiwanie wykazu](./media/storsimple-virtual-array-install-update-1/download1.png)

4. Kliknij przycisk **Pobierz**.

5. Pobierz oba pliki do folderu. Można również skopiować folder do udziału sieciowego, który jest dostępny z urządzenia.

6. Otwórz folder, w którym znajdują się pliki.

    ![Pliki w pakiecie](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    Zostaną wyświetlone dwa pliki:
    -  Plik pakietu autonomicznego usługi Microsoft Update `WindowsTH-KB3011067-x64`. Ten plik jest używany do aktualizacji oprogramowania urządzenia.
    - Plik, który zawiera aktualizacje zbiorcze programu sierpnia `windows8.1-kb4034681-x64`. Aby uzyskać więcej informacji o to, co jest uwzględnione w tym pakiecie zbiorczym, przejdź do [sierpnia miesięczne zabezpieczeń zbiorczy](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810).

### <a name="install-the-update-or-the-hotfix"></a>Zainstalowanie aktualizacji lub poprawki

Przed instalacją aktualizacji lub poprawki upewnij się, że:

 - Masz aktualizacji lub poprawek, pobierane lokalnie na hoście albo dostępne za pośrednictwem udziału sieciowego.
 - Macierz wirtualna jest uruchomiona w aktualizacji Update 0.6 (10.0.10293.0). Jeśli używasz wersji wcześniejszej niż aktualizacji Update 0.6, [instalowanie aktualizacji Update 0.6](storsimple-virtual-array-install-update-06.md) pierwszy, a następnie zainstaluj aktualizację 1.

Ta procedura trwa około 4 minut, aby zakończyć. Wykonaj następujące czynności w celu zainstalowania aktualizacji lub poprawki.

#### <a name="to-install-the-update-or-the-hotfix"></a>Do zainstalowania aktualizacji lub poprawki

1. W lokalnym internetowym interfejsie użytkownika, przejdź do **konserwacji** > **aktualizacji oprogramowania**. Zanotuj wersji oprogramowania, które są uruchomione. **Kontynuuj tę aktualizację tylko wtedy, gdy są uruchomione aktualizacji Update 0.6 (10.0.10293.0). Jeśli używasz starszej wersji, [instalowanie aktualizacji Update 0.6](storsimple-virtual-array-install-update-06.md) na urządzeniu z systemem pierwsze, a następnie zastosować Update 1.**
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. W **ścieżka pliku aktualizacji**, wprowadź nazwę pliku aktualizacji lub poprawki. Możesz także przejść do lokalizacji pliku instalacyjnego aktualizacja lub poprawka umieszczony w udziale sieciowym. Kliknij przycisk **Zastosuj**.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. Zostanie wyświetlone ostrzeżenie. Biorąc pod uwagę macierz wirtualna jest urządzenie o jednym węźle, po zastosowaniu aktualizacji i ponownym uruchomieniu urządzenia wystąpi Przestój. Kliknij ikonę znacznika wyboru.
   
   ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. Aktualizacja zostanie uruchomiony. Po pomyślnym zaktualizowaniu urządzenia, jego uruchomieniu. Lokalny interfejs użytkownika jest niedostępny w tym czasie.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. Po ponownym uruchomieniu, nastąpi przekierowanie do **Zaloguj** strony. Aby zweryfikować, że oprogramowanie urządzenia został zaktualizowany w lokalnym internetowym interfejsie użytkownika, przejdź do **konserwacji** > **aktualizacji oprogramowania**. Wersja oprogramowania wyświetlane powinna być **10.0.0.0.0.10296** aktualizacji 1.0.
   
   > [!NOTE]
   > Firma Microsoft raport wersje oprogramowania w nieco inny sposób w lokalnym internetowym interfejsie użytkownika, jak i portalu Azure. Na przykład raporty lokalnego Interfejsu w przeglądarce **10.0.0.0.0.10296** i Azure portal raporty **10.0.10296.0** dla tej samej wersji.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. Powtórz kroki 2 – 4, aby zainstalować poprawkę zabezpieczeń Windows przy użyciu pliku `windows8.1-kb4012213-x64`. Macierz wirtualna ponownego uruchomienia po zakończeniu instalacji i musisz zalogować się do lokalnego Interfejsu w przeglądarce.

> [!NOTE]
> Jeśli aktualizacja Update 1 stosowane bezpośrednio na urządzeniu z systemem w wersji wcześniejszej niż aktualizacji Update 0.6, brakuje niektórych aktualizacji. Dalsze czynności, skontaktuj się z Microsoft Support.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [administrowanie rozwiązania StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).
