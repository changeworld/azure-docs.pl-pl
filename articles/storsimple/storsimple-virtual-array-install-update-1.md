---
title: Zainstaluj aktualizację 1.0 na tablicy wirtualnej StorSimple | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak używać interfejsu użytkownika sieci Web tablicy wirtualnej StorSimple do stosowania aktualizacji przy użyciu portalu Azure i metody poprawki
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254510"
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>Instalowanie aktualizacji 1.0 na tablicy wirtualnej StorSimple

## <a name="overview"></a>Omówienie

W tym artykule opisano kroki wymagane do zainstalowania aktualizacji 1.0 w tablicy wirtualnej StorSimple za pośrednictwem lokalnego interfejsu użytkownika sieci web i za pośrednictwem witryny Azure portal.

Należy zastosować aktualizacje oprogramowania lub poprawki, aby zachować storsimple virtual array aktualne. Przed zastosowaniem aktualizacji zaleca się najpierw przetraktować woluminy lub udziały w trybie offline na hoście, a następnie na urządzeniu. Minimalizuje to ryzyko uszkodzenia danych. Po woluminy lub udziały są w trybie offline, należy również wykonać ręczną kopię zapasową urządzenia.

> [!IMPORTANT]
> - Aktualizacja 1.0 odpowiada wersji oprogramowania **10.0.10296.0** na urządzeniu. Aby uzyskać informacje o nowościach w tej aktualizacji, przejdź do [informacji o wersji aktualizacji 1.0](storsimple-virtual-array-update-1-release-notes.md).
>
> - Należy pamiętać, że zainstalowanie aktualizacji lub poprawki uruchamia ponownie urządzenie. Biorąc pod uwagę, że StorSimple Virtual Array jest urządzeniem z jednym węzłem, wszelkie we/wy w toku jest zakłócony i urządzenie doświadcza przestojów.
>
> - Aktualizacja 1 jest dostępna w witrynie Azure portal tylko wtedy, gdy macierz wirtualna jest uruchomiona aktualizacja 0.6. W przypadku macierzy wirtualnych z uruchomień w wersjach wstępnej aktualizacji 0.6 należy najpierw zainstalować aktualizację 0.6, a następnie zainstalować aktualizację 1.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Jeśli jest uruchomiona aktualizacja 0.2 lub nowsza, zaleca się zainstalowanie aktualizacji za pośrednictwem witryny Azure portal. Procedura portalu wymaga od użytkownika skanowania, pobierania, a następnie instalowania aktualizacji. W zależności od wersji oprogramowania jest uruchomiona macierz wirtualna, stosowanie aktualizacji za pośrednictwem witryny Azure portal jest inny.

 - Jeśli tablica wirtualna jest uruchomiona aktualizacja 0.6, portal Azure bezpośrednio instaluje aktualizację 1 (10.0.10296.0) na urządzeniu. Ta procedura trwa około 7 minut.
 - Jeśli tablica wirtualna jest uruchomiona wersja przed aktualizacją 0.6, aktualizacja jest wykonywana w dwóch etapach. Portal Azure po raz pierwszy instaluje aktualizację 0.6 (10.0.10293.0) na urządzeniu. Tablica wirtualna uruchamia się ponownie, a portal instaluje aktualizację 1 (10.0.10296.0) na urządzeniu. Ta procedura trwa około 15 minut.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

Po zakończeniu instalacji przejdź do usługi StorSimple Device Manager. Wybierz **pozycję Urządzenia,** a następnie wybierz pozycję i kliknij właśnie zaktualizowane urządzenie. Przejdź do **ustawień > zarządzanie aktualizacjami urządzeń >**. Wyświetlana wersja oprogramowania powinna mieć **10.0.10296.0**.

![Wersja oprogramowania po aktualizacji](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>Korzystanie z lokalnego interfejsu użytkownika sieci Web

Istnieją dwa kroki podczas korzystania z lokalnego interfejsu użytkownika sieci Web:

* Pobierz aktualizację lub poprawkę
* Instalowanie aktualizacji lub poprawki

> [!IMPORTANT] 
> **Tę aktualizację należy kontynuować tylko w przypadku uruchomienia aktualizacji 0.6 (10.0.10293.0). Jeśli korzystasz z wcześniejszej wersji, [najpierw zainstaluj aktualizację 0.6](storsimple-virtual-array-install-update-06.md) na urządzeniu, a następnie zastosuj aktualizację 1.**

### <a name="download-the-update-or-the-hotfix"></a>Pobierz aktualizację lub poprawkę

Jeśli w macierzy wirtualnej jest uruchomiona aktualizacja 0.6, wykonaj następujące kroki, aby pobrać aktualizację 1 z wykazu Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Aby pobrać aktualizację lub poprawkę

1. Uruchom program Internet [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)Explorer i przejdź do pliku .

2. Jeśli katalog microsoft update jest używany po raz pierwszy na tym komputerze, kliknij przycisk **Zainstaluj** po wyświetleniu monitu o zainstalowanie dodatku wykazu microsoft update.

3. W polu wyszukiwania katalogu Microsoft Update należy wprowadzić numer bazy wiedzy (KB) poprawki, którą chcesz pobrać. Wprowadź **4047203** dla aktualizacji 1.0, a następnie kliknij przycisk **Wyszukaj**.
   
    Zostanie wyświetlona lista poprawek, na przykład **StorSimple Virtual Array Update 1.0**.
   
    ![Przeszukiwanie wykazu](./media/storsimple-virtual-array-install-update-1/download1.png)

4. Kliknij **pozycję Pobierz**.

5. Pobierz dwa pliki do folderu. Folder można również skopiować do udziału sieciowego, do którego można dotrzeć z urządzenia.

6. Otwórz folder, w którym znajdują się pliki.

    ![Pliki w pakiecie](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    Widzisz dwa pliki:
    -  Plik `WindowsTH-KB3011067-x64`pakietu autonomicznego usługi Microsoft Update . Ten plik służy do aktualizacji oprogramowania urządzenia.
    - Plik zawierający aktualizacje `windows8.1-kb4034681-x64`zbiorcze na sierpień . Aby uzyskać więcej informacji na temat tego, co jest zawarte w tym zestawieniu, przejdź do [miesięcznego zestawienia zabezpieczeń sierpnia](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810).

### <a name="install-the-update-or-the-hotfix"></a>Instalowanie aktualizacji lub poprawki

Przed instalacją aktualizacji lub poprawki upewnij się, że:

 - Masz aktualizację lub poprawkę pobraną lokalnie na hoście lub dostępną za pośrednictwem udziału sieciowego.
 - Twoja tablica wirtualna jest uruchomiona aktualizacja 0.6 (10.0.10293.0). Jeśli wersja jest uruchomiona przed aktualizacją 0.6, najpierw [zainstaluj aktualizację 0.6,](storsimple-virtual-array-install-update-06.md) a następnie zainstaluj aktualizację 1.

Ta procedura trwa około 4 minut. Wykonaj następujące kroki, aby zainstalować aktualizację lub poprawkę.

#### <a name="to-install-the-update-or-the-hotfix"></a>Aby zainstalować aktualizację lub poprawkę

1. W lokalnym interfejsie użytkownika sieci Web przejdź do aktualizacji**oprogramowania** **konserwacji** > . Zanotuj wersję oprogramowania, którą uruchamiasz. **Tę aktualizację należy kontynuować tylko w przypadku uruchomienia aktualizacji 0.6 (10.0.10293.0). Jeśli korzystasz z wcześniejszej wersji, [najpierw zainstaluj aktualizację 0.6](storsimple-virtual-array-install-update-06.md) na urządzeniu, a następnie zastosuj aktualizację 1.**
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. W **polu Aktualizuj ścieżkę pliku**wprowadź nazwę pliku aktualizacji lub poprawki. Można również przejść do pliku instalacji aktualizacji lub poprawki, jeśli są umieszczane w udziale sieciowym. Kliknij przycisk **Zastosuj**.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. Zostanie wyświetlone ostrzeżenie. Biorąc pod uwagę, że tablica wirtualna jest urządzeniem z jednym węzłem, po zastosowaniu aktualizacji urządzenie zostanie ponownie uruchomione i nastąpi przestoje. Kliknij ikonę wyboru.
   
   ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. Rozpocznie się aktualizacja. Po pomyślnym zaktualizowaniu urządzenia zostanie ponownie uruchomione. Lokalny interfejs użytkownika nie jest dostępny w tym czasie.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. Po zakończeniu ponownego uruchomienia zostaniesz przesuń do strony **Zaloguj się.** Aby sprawdzić, czy oprogramowanie urządzenia zostało zaktualizowane, w lokalnym interfejsie użytkownika sieci Web przejdź do **witryny Konserwacja** > **oprogramowania Update**. Wyświetlana wersja oprogramowania powinna być **10.0.0.0.0.10296** dla aktualizacji 1.0.
   
   > [!NOTE]
   > Firma Microsoft raportuje wersje oprogramowania w nieco inny sposób w lokalnym interfejsie użytkownika sieci web i witrynie Azure portal. Na przykład lokalny interfejs użytkownika sieci web raportuje **10.0.0.0.0.10296** i portal Azure raporty **10.0.10296.0** dla tej samej wersji.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. Powtórz kroki 2-4, aby zainstalować `windows8.1-kb4012213-x64`poprawkę zabezpieczeń systemu Windows przy użyciu pliku . Tablica wirtualna uruchamia się ponownie po zainstalowaniu i musisz zalogować się do lokalnego interfejsu użytkownika sieci web.

> [!NOTE]
> Jeśli aktualizacja 1 została bezpośrednio zastosowana na urządzeniu z uruchomieniem wersji przed aktualizacją 0.6, brakuje niektórych aktualizacji. Aby uzyskać następujące kroki, skontaktuj się z pomocą techniczną firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [administrowaniu tablicą wirtualną StorSimple](storsimple-ova-web-ui-admin.md).
