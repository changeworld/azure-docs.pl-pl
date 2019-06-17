---
title: Instalowanie aktualizacji na platformie Microsoft Azure StorSimple Virtual Array | Dokumentacja firmy Microsoft
description: Opisuje sposób używania interfejsu użytkownika sieci web rozwiązania StorSimple Virtual Array do stosowania aktualizacji za pomocą metody portalu i poprawki
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 9997a97b-9382-43ed-b56e-61369335c987
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7bf064ff01693f7a65c756a99c435d7f1a39840e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61409419"
---
# <a name="install-updates-on-your-storsimple-virtual-array---azure-portal"></a>Instalowania aktualizacji w ramach rozwiązania StorSimple Virtual Array — witryna Azure portal

## <a name="overview"></a>Omówienie

W tym artykule opisano kroki wymagane do instalowania aktualizacji w ramach rozwiązania StorSimple Virtual Array przy użyciu witryny Azure portal i za pomocą lokalnego Interfejsu w przeglądarce. Należy zastosować aktualizacje oprogramowania lub poprawek, aby zapewnić aktualność rozwiązania StorSimple Virtual Array. 

Należy pamiętać, który, instalowania aktualizacji lub poprawki powoduje ponowne uruchomienie urządzenia. Biorąc pod uwagę, że macierz wirtualna StorSimple jest urządzenie o jednym węźle, jest przerwane wszelkie operacje We/Wy w toku i urządzenie, wystąpi Przestój. 

Przed zastosowaniem aktualizacji zaleca się wykonanie woluminy lub udziały w tryb offline na hoście pierwszy i następnie urządzenia. Minimalizuje to ryzyko uszkodzenia danych.

> [!IMPORTANT]
> Jeśli korzystasz z aktualizacji 0.1 lub wersji oprogramowania, musi być instalacja aktualizacji 0.3 metodę poprawkę, za pomocą lokalnego Interfejsu w przeglądarce. Jeśli korzystasz z aktualizacją Update 0.2, firma Microsoft zaleca instalowanie aktualizacji za pośrednictwem klasycznego portalu Azure.
 

## <a name="use-the-local-web-ui"></a>Użyj lokalnego internetowego interfejsu użytkownika

Istnieją dwa kroki, korzystając z lokalnego Interfejsu w przeglądarce:

* Pobieranie aktualizacji lub poprawki
* Zainstalowanie aktualizacji lub poprawki

### <a name="download-the-update-or-the-hotfix"></a>Pobieranie aktualizacji lub poprawki

Wykonaj następujące kroki, aby pobrać aktualizację oprogramowania z Wykazu usługi Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Aby pobrać aktualizacji lub poprawki

1. Uruchom program Internet Explorer i przejdź do [ https://catalog.update.microsoft.com ](https://catalog.update.microsoft.com).

2. Jeśli po raz pierwszy używasz Wykazu usługi Microsoft Update na danym komputerze, po wyświetleniu monitu o zainstalowanie dodatku Wykazu usługi Microsoft Update kliknij pozycję **Zainstaluj**.

3. W polu wyszukiwania wykazu usługi Microsoft Update wprowadź numer bazy wiedzy Knowledge Base (KB) poprawki, którą chcesz pobrać. Wprowadź **3182061** for Update 0.3, a następnie kliknij przycisk **wyszukiwania**.
   
    Lista poprawek zawierająca pojawia się na przykład **StorSimple Virtual Array Update 0.3**.
   
    ![Przeszukiwanie wykazu](./media/storsimple-virtual-array-install-update/download1.png)

4. Kliknij pozycję **Add** (Dodaj). Aktualizacja zostanie dodana do koszyka.

5. Kliknij pozycję **Wyświetl koszyk**.

6. Kliknij pozycję **Pobierz**. Określ lokalizację lokalną, do której mają trafiać pobrane pliki, albo **przejdź** do takiej lokalizacji. Aktualizacje zostaną pobrane do wskazanej lokalizacji i umieszczone w podfolderze o nazwie takiej samej jak aktualizacja. Folder można też skopiować do udziału sieciowego osiągalnego z urządzenia.

7. Otwórz folder skopiowany, powinien zostać wyświetlony plik pakietu autonomicznego usługi Microsoft Update `WindowsTH-KB3011067-x64`. Ten plik jest używany do zainstalowania aktualizacji lub poprawki.

### <a name="install-the-update-or-the-hotfix"></a>Zainstalowanie aktualizacji lub poprawki

Przed instalacją aktualizacji lub poprawki upewnij się, że aktualizacja lub poprawka pobierane lokalnie na hoście ani dostępne za pośrednictwem udziału sieciowego. 

Ta metoda umożliwia instalowanie aktualizacji na urządzeniu z systemem GA lub zaktualizuj 0,1 wersje oprogramowania. Ta procedura ma mniej niż 2 minut. Wykonaj następujące czynności w celu zainstalowania aktualizacji lub poprawki.

#### <a name="to-install-the-update-or-the-hotfix"></a>Do zainstalowania aktualizacji lub poprawki

1. W lokalnym internetowym interfejsie użytkownika, przejdź do **konserwacji** > **aktualizacji oprogramowania**.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update/update1m.png)

2. W **ścieżka pliku aktualizacji**, wprowadź nazwę pliku aktualizacji lub poprawki. Możesz także przejść do lokalizacji pliku instalacyjnego aktualizacja lub poprawka umieszczony w udziale sieciowym. Kliknij przycisk **Zastosuj**.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update/update2m.png)

3. Zostanie wyświetlone ostrzeżenie. Po zastosowaniu aktualizacji i ponownym uruchomieniu urządzenia wystąpi Przestój, biorąc pod uwagę te jest urządzenie o jednym węźle. Kliknij ikonę znacznika wyboru.
   
   ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update/update3m.png)

4. Aktualizacja zostanie uruchomiony. Po pomyślnym zaktualizowaniu urządzenia, jego uruchomieniu. Lokalny interfejs użytkownika jest niedostępny w tym czasie.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update/update5m.png)

5. Po ponownym uruchomieniu, nastąpi przekierowanie do **Zaloguj** strony. Aby zweryfikować, że oprogramowanie urządzenia został zaktualizowany w lokalnym internetowym interfejsie użytkownika, przejdź do **konserwacji** > **aktualizacji oprogramowania**. Wersja oprogramowania wyświetlane powinna być **10.0.0.0.0.10288.0** for Update 0.3.
   
   > [!NOTE]
   > Firma Microsoft raport wersje oprogramowania w nieco inny sposób w lokalnym internetowym interfejsie użytkownika, jak i portalu Azure. Na przykład raporty lokalnego Interfejsu w przeglądarce **10.0.0.0.0.10288** i Azure portal raporty **10.0.10288.0** dla tej samej wersji.
   
    ![aktualizowanie urządzenia](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Jeśli z aktualizacją Update 0.2, zaleca się zainstalowanie aktualizacji w witrynie Azure portal. Procedury portalu wymaga od użytkownika skanowania, pobieranie i instalowanie aktualizacji. Ta procedura trwa około 7 minut. Wykonaj następujące czynności w celu zainstalowania aktualizacji lub poprawki.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal.md)]

Po zakończeniu instalacji (co wskazuje stan zadania na poziomie 100%), przejdź do usługi Menedżer urządzeń StorSimple. Wybierz **urządzeń** a następnie wybierz i kliknij urządzenie, aby zaktualizować z listy urządzeń podłączonych do tej usługi. W **ustawienia** przejdź do bloku **Zarządzaj** i wybierz pozycję **aktualizacji urządzenia**. Wersja oprogramowania wyświetlane powinna być **10.0.10288.0**.


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [administrowanie rozwiązania StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

