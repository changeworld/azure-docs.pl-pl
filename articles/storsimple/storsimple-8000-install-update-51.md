---
title: Zainstaluj aktualizację 5.1 na urządzeniu z serii StorSimple 8000 | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak zainstalować aktualizację 5.1 z serii StorSimple 8000 na urządzeniu z serii StorSimple 8000.
services: storsimple
documentationcenter: NA
author: priestlg
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/05/2020
ms.author: v-grpr
ms.openlocfilehash: b90f0f35f908d16b0746075e638fe66769939a64
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658042"
---
# <a name="install-update-51-on-your-storsimple-device"></a>Instalowanie aktualizacji 5.1 na urządzeniu StorSimple

## <a name="overview"></a>Omówienie

W tym samouczku wyjaśniono, jak zainstalować aktualizację 5.1 na urządzeniu StorSimple z uruchomioną wcześniejszą wersją oprogramowania za pośrednictwem portalu Azure. <!--The hotfix method is used when you are trying to install Update 5.1 on a device running pre-Update 3 versions. The hotfix method is also used when a gateway is configured on a network interface other than DATA 0 of the StorSimple device and you are trying to update from a pre-Update 1 software version.-->

Aktualizacja 5.1 zawiera aktualizacje zabezpieczeń nieunikłowe. Aktualizacje nieunikające lub regularne mogą być stosowane za pośrednictwem witryny Azure portal <!--or by the hotfix method-->.

> [!IMPORTANT]
>
> * Aktualizacja 5.1 jest obowiązkową aktualizacją i powinna zostać zainstalowana natychmiast. Aby uzyskać więcej informacji, zobacz [Informacje o wersji aktualizacji 5.1](storsimple-update51-release-notes.md).
> * Przed instalacją przeprowadza się ręczne i automatyczne kontrole wstępne w celu określenia kondycji urządzenia pod względem stanu sprzętu i łączności sieciowej. Te wstępne kontrole są wykonywane tylko wtedy, gdy zastosujesz aktualizacje z witryny Azure portal.
> * Jeśli chcesz zainstalować przy użyciu metody poprawki, skontaktuj się z [pomocą techniczną firmy Microsoft](mailto:support@microsoft.com).

<!--
> * We strongly recommend that when updating a device running versions prior to Update 3, you install the updates using hotfix method. If you encounter any issues, [log a support ticket](storsimple-8000-contact-microsoft-support.md). 


> * We recommend that you install the software and other regular updates via the Azure portal. You should only go to the Windows PowerShell interface of the device (to install updates) if the pre-update gateway check fails in the portal. Depending upon the version you are updating from, the updates may take 4 hours (or greater) to install. The maintenance mode updates must be installed through the Windows PowerShell interface of the device. As maintenance mode updates are disruptive updates, these result in a down time for your device.


> * If running the optional StorSimple Snapshot Manager, ensure that you have upgraded your Snapshot Manager version to Update 5.1 prior to updating the device.
-->

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-51-through-the-azure-portal"></a>Instalowanie aktualizacji 5.1 za pośrednictwem portalu Azure

Wykonaj następujące kroki, aby zaktualizować urządzenie do [aktualizacji 5.1](storsimple-update51-release-notes.md).

> [!NOTE]
> Firma Microsoft pobiera dodatkowe informacje diagnostyczne z urządzenia. W rezultacie, gdy nasz zespół operacyjny identyfikuje urządzenia, które mają problemy, jesteśmy lepiej przygotowani do zbierania informacji z urządzenia i diagnozowania problemów.

#### <a name="to-install-an-update-from-the-azure-portal"></a>Aby zainstalować aktualizację z witryny Azure Portal

1. Na stronie usługi StorSimple wybierz swoje urządzenie.

    ![Wybierz urządzenie](./media/storsimple-8000-install-update-51/update1.png)

2. Przejdź do **ustawień** > urządzenia**Aktualizacje urządzenia**.

    ![Kliknij pozycję Aktualizacje urządzenia](./media/storsimple-8000-install-update-51/update2.png)

3. Jeśli dostępne są nowe aktualizacje, zostanie wyświetlone powiadomienie. Alternatywnie w bloku **Aktualizacje urządzenia** kliknij pozycję **Skanuj aktualizacje**. Zostanie utworzone zadanie skanowania dostępnych aktualizacji. Otrzymasz powiadomienie, gdy zadanie zostanie pomyślnie ukończone.

    ![Kliknij pozycję Aktualizacje urządzenia](./media/storsimple-8000-install-update-51/update3.png)

4. Przed zastosowaniem aktualizacji na urządzeniu zalecamy przejrzenie informacji o wersji. Aby zastosować aktualizacje, kliknij pozycję **Zainstaluj aktualizacje**. W **bloku Potwierdź regularne aktualizacje** przejrzyj wymagania wstępne do ukończenia przed zastosowaniem aktualizacji. Zaznacz to pole wyboru, aby wskazać, że można zaktualizować urządzenie, a następnie kliknij przycisk **Zainstaluj**.

    ![Kliknij pozycję Aktualizacje urządzenia](./media/storsimple-8000-install-update-51/update4.png)

5. Zostanie rozpoczęty zestaw testów wymagań wstępnych. Testy te obejmują:
   
   * **Testy kondycji kontrolera** w celu sprawdzenia, czy oba kontrolery urządzeń są w dobrej kondycji i w trybie online.
   * **Testy kondycji składnika sprzętowego** w celu sprawdzenia, czy wszystkie składniki sprzętowe Twojego urządzenia StorSimple są w dobrej kondycji.
   * **Testy interfejsu DATA 0** w celu sprawdzenia, czy interfejs DATA 0 jest włączony na Twoim urządzeniu. Jeśli ten interfejs nie jest włączony, należy go włączyć i ponowić próbę.

     Aktualizacja jest pobierana i instalowana tylko wtedy, gdy wszystkie kontrole zostały pomyślnie zakończone. Zobaczysz powiadomienie o trwających testach. Jeśli prechecks nie powiedzie się, a następnie zostaną dostarczone z przyczynami niepowodzenia. Rozwiąż te problemy, a następnie ponów próbę wykonania operacji. Jeśli nie będziesz w stanie samodzielnie rozwiązać tych problemów, konieczne może być skontaktowanie się z pomocą techniczną firmy Microsoft.

7. Po pomyślnym zakończeniu kontroli wstępnej tworzone jest zadanie aktualizacji. Otrzymasz powiadomienie o pomyślnym utworzeniu zadania aktualizacji.
   
    ![Tworzenie zadania aktualizacji](./media/storsimple-8000-install-update-51/update6.png)
   
    Następnie aktualizacja jest stosowania do Twojego urządzenia.

9. Aktualizacja zajmuje kilka godzin. Aby w dowolnym momencie wyświetlić szczegóły zadania, wybierz zadanie aktualizacji i kliknij pozycję **Szczegóły**.

    ![Tworzenie zadania aktualizacji](./media/storsimple-8000-install-update-51/update8.png)

     Postęp zadania aktualizacji można również monitorować w **ustawieniach urządzenia > zadaniach**. Na **zadania** bloku, można zobaczyć postęp aktualizacji.

     ![Tworzenie zadania aktualizacji](./media/storsimple-8000-install-update-51/update7.png)

10. Po zakończeniu zadania przejdź do **pozycji Ustawienia urządzenia > aktualizacje urządzenia**. Wersja oprogramowania powinna zostać zaktualizowana.


Sprawdź, czy na urządzeniu jest uruchomiona **aktualizacja 5.1 z serii StorSimple 8000 (6.3.9600.17885)**. Data **ostatniej aktualizacji** powinna zostać zmodyfikowana.
<!-- 5.1 - KB 4542887-->

<!--You will now see that the Maintenance mode updates are available (this message might continue to be displayed for up to 24 hours after you install the updates). The steps to install maintenance mode update are detailed in the next section.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## Install Update 5.1 as a hotfix

The software versions that can be upgraded using the hotfix method are:

* Update 0.1, 0.2, 0.3
* Update 1, 1.1, 1.2
* Update 2, 2.1, 2.2
* Update 3, 3.1
* Update 4
* Update 5

> [!NOTE]
> The recommended method to install Update 5.1 is through the Azure portal when trying to update from Update 3 and later version. When updating a device running versions prior to Update 3, use this procedure. You can also use this procedure if you fail the gateway check when trying to install the updates through the Azure portal. The check fails when you have a gateway assigned to a non-DATA 0 network interface and your device is running a software version earlier than Update 1.

The hotfix method involves the following three steps:

1. Download the hotfixes from the Microsoft Update Catalog.
2. Install and verify the regular mode hotfixes.
3. Install and verify the maintenance mode hotfix.

#### Download updates for your device

You must download and install the following hotfixes in the prescribed order and the suggested folders:

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Software update<br> Download both _HcsSoftwareUpdate.exe_ and _CisMSDAgent.exe_ |Regular <br></br>Non-disruptive |~ 25 mins |FirstOrderUpdate|

If updating from a device running Update 4, you only need to install the OS cumulative updates as second order updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |OS cumulative updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|

If installing from a device running Update 3 or earlier, install the following in addition to the cumulative updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |LSI driver and firmware updates <br> USM firmware update (version 3.38) |Regular <br></br>Non-disruptive |~ 3 hrs <br> (includes 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |OS security updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |OS updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|



You may also need to install disk firmware updates on top of all the updates shown in the preceding tables. You can verify whether you need the disk firmware updates by running the `Get-HcsFirmwareVersion` cmdlet. If you are running these firmware versions: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107`, then you do not need to install these updates.

| Order | KB | Description | Update type | Install time | Install in folder|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Disk firmware |Maintenance <br></br>Disruptive |~ 30 mins | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * If updating from Update 4, the total install time is close to 4 hours.
> * Before using this procedure to apply the update, make sure that both the device controllers are online and all the hardware components are healthy.

Perform the following steps to download and install the hotfixes.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]
-->
<!--
[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]
-->

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [wersji aktualizacji 5.1](storsimple-update51-release-notes.md).
