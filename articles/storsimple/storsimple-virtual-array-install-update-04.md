---
title: Instalowanie aktualizacji na tablicy wirtualnej StorSimple | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak używać interfejsu użytkownika sieci Web tablicy wirtualnej StorSimple do stosowania aktualizacji przy użyciu portalu Azure i metody poprawki
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
ms.date: 02/07/2017
ms.author: alkohli
ms.openlocfilehash: b67fcb82bdcc94d7faeceedb7420a869e6578cad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61436465"
---
# <a name="install-update-04-on-your-storsimple-virtual-array"></a>Instalowanie aktualizacji 0.4 na tablicy wirtualnej StorSimple

## <a name="overview"></a>Omówienie

W tym artykule opisano kroki wymagane do zainstalowania aktualizacji 0.4 w tablicy wirtualnej StorSimple za pośrednictwem lokalnego interfejsu użytkownika sieci web i za pośrednictwem witryny Azure portal. Aby mieć aktualną aktualizację wirtualną tablicy StorSimple, należy zastosować aktualizacje oprogramowania lub poprawki. 

Należy pamiętać, że zainstalowanie aktualizacji lub poprawki uruchamia ponownie urządzenie. Biorąc pod uwagę, że StorSimple Virtual Array jest urządzeniem z jednym węzłem, wszelkie we/wy w toku jest zakłócony i urządzenie doświadcza przestojów. 

Przed zastosowaniem aktualizacji zaleca się najpierw przetraktować woluminy lub udziały w trybie offline na hoście, a następnie na urządzeniu. Minimalizuje to ryzyko uszkodzenia danych.

> [!IMPORTANT]
> Jeśli używasz wersji oprogramowania Aktualizacji 0.1 lub GA, musisz użyć metody poprawki za pośrednictwem lokalnego interfejsu użytkownika sieci web, aby zainstalować aktualizację 0.3. Jeśli korzystasz z aktualizacji 0.2 lub nowszej, zaleca się zainstalowanie aktualizacji za pośrednictwem witryny Azure portal.
 

## <a name="use-the-local-web-ui"></a>Korzystanie z lokalnego interfejsu użytkownika sieci Web

Istnieją dwa kroki podczas korzystania z lokalnego interfejsu użytkownika sieci Web:

* Pobierz aktualizację lub poprawkę
* Instalowanie aktualizacji lub poprawki

### <a name="download-the-update-or-the-hotfix"></a>Pobierz aktualizację lub poprawkę

Wykonaj następujące kroki, aby pobrać aktualizację oprogramowania z Wykazu usługi Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Aby pobrać aktualizację lub poprawkę

1. Uruchom program Internet [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)Explorer i przejdź do pliku .

2. Jeśli po raz pierwszy używasz Wykazu usługi Microsoft Update na danym komputerze, po wyświetleniu monitu o zainstalowanie dodatku Wykazu usługi Microsoft Update kliknij pozycję **Zainstaluj**.

3. W polu wyszukiwania katalogu Microsoft Update należy wprowadzić numer bazy wiedzy (KB) poprawki, którą chcesz pobrać. Wprowadź **3216577** dla aktualizacji 0.4, a następnie kliknij przycisk **Wyszukaj**.
   
    Zostanie wyświetlona lista poprawek, na przykład **StorSimple Virtual Array Update 0.4**.
   
    ![Przeszukiwanie wykazu](./media/storsimple-virtual-array-install-update-04/download1.png)

4. Kliknij przycisk **Dodaj**. Aktualizacja zostanie dodana do koszyka.

5. Kliknij pozycję **Wyświetl koszyk**.

6. Kliknij **pozycję Pobierz**. Określ lokalizację lokalną, do której mają trafiać pobrane pliki, albo **przejdź** do takiej lokalizacji. Aktualizacje zostaną pobrane do wskazanej lokalizacji i umieszczone w podfolderze o nazwie takiej samej jak aktualizacja. Folder można też skopiować do udziału sieciowego osiągalnego z urządzenia.

7. Otwórz skopiowany folder, powinien zostać wyświetlony plik `WindowsTH-KB3011067-x64`pakietu autonomicznego Microsoft Update . Ten plik jest używany do instalowania aktualizacji lub poprawki.

### <a name="install-the-update-or-the-hotfix"></a>Instalowanie aktualizacji lub poprawki

Przed instalacją aktualizacji lub poprawki upewnij się, że aktualizacja lub poprawka zostały pobrane lokalnie na hoście lub dostępne za pośrednictwem udziału sieciowego. 

Ta metoda służy do instalowania aktualizacji na urządzeniu z systemem GA lub Aktualizacja 0.1 wersje oprogramowania. Ta procedura trwa mniej niż 2 minuty. Wykonaj następujące kroki, aby zainstalować aktualizację lub poprawkę.

#### <a name="to-install-the-update-or-the-hotfix"></a>Aby zainstalować aktualizację lub poprawkę

1. W lokalnym interfejsie użytkownika sieci Web przejdź do aktualizacji**oprogramowania** **konserwacji** > .
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update/update1m.png)

2. W **polu Aktualizuj ścieżkę pliku**wprowadź nazwę pliku aktualizacji lub poprawki. Można również przejść do pliku instalacji aktualizacji lub poprawki, jeśli są umieszczane w udziale sieciowym. Kliknij przycisk **Zastosuj**.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update/update2m.png)

3. Zostanie wyświetlone ostrzeżenie. Biorąc pod uwagę, że jest to urządzenie z jednym węzłem, po zastosowaniu aktualizacji urządzenie zostanie ponownie uruchomione i nastąpi przestoje. Kliknij ikonę wyboru.
   
   ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update/update3m.png)

4. Rozpocznie się aktualizacja. Po pomyślnym zaktualizowaniu urządzenia zostanie ponownie uruchomione. Lokalny interfejs użytkownika nie jest dostępny w tym czasie.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update/update5m.png)

5. Po zakończeniu ponownego uruchomienia zostaniesz przesuń do strony **Zaloguj się.** Aby sprawdzić, czy oprogramowanie urządzenia zostało zaktualizowane, w lokalnym interfejsie użytkownika sieci Web przejdź do **witryny Konserwacja** > **oprogramowania Update**. Wyświetlana wersja oprogramowania powinna być **10.0.0.0.0.10289.0** dla aktualizacji 0.4.
   
   > [!NOTE]
   > Firma Microsoft raportuje wersje oprogramowania w nieco inny sposób w lokalnym interfejsie użytkownika sieci web i witrynie Azure portal. Na przykład lokalny interfejs użytkownika sieci web raportuje **10.0.0.0.0.10289** i witryny Azure portal raporty **10.0.10289.0** dla tej samej wersji.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Jeśli jest uruchomiona aktualizacja 0.2 lub nowsza, zaleca się zainstalowanie aktualizacji za pośrednictwem witryny Azure portal. Procedura portalu wymaga od użytkownika skanowania, pobierania, a następnie instalowania aktualizacji. Ta procedura trwa około 7 minut. Wykonaj następujące kroki, aby zainstalować aktualizację lub poprawkę.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Po zakończeniu instalacji (zgodnie ze stanem zadania na poziomie 100 %), przejdź do usługi StorSimple Device Manager. Wybierz **pozycję Urządzenia,** a następnie wybierz pozycję i kliknij urządzenie, które chcesz zaktualizować z listy urządzeń połączonych z tą usługą. W bloku **Ustawienia** przejdź do sekcji **Zarządzanie** i wybierz pozycję **Aktualizacje urządzenia**. Wyświetlana wersja oprogramowania powinna mieć **10.0.10289.0**.


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [administrowaniu tablicą wirtualną StorSimple](storsimple-ova-web-ui-admin.md).

