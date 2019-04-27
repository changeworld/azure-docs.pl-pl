---
title: Ręczne przechodzenie w tryb failover w centrum Azure IoT Hub | Microsoft Docs
description: Przedstawienie sposobu ręcznego przejścia w tryb failover w centrum Azure IoT Hub
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 40a7bba99068ebc2368e413199cf966bd2e4f25c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60650543"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub-public-preview"></a>Samouczek: Ręczne przechodzenie w tryb failover dla centrum IoT (publiczna wersja zapoznawcza)

Ręczne przechodzenie w tryb failover to funkcja usługi IoT Hub, która umożliwia klientom [przełączanie w tryb failover](https://en.wikipedia.org/wiki/Failover) operacji centrum z regionu podstawowego do odpowiedniego sparowanego geograficznie regionu świadczenia usługi Azure. Ręczne przejście w tryb failover można wykonać w przypadku awarii regionalnej lub dłuższej awarii usługi. Można też wykonać planowane przejście w tryb failover, aby przetestować możliwości odzyskiwania po awarii, mimo że zalecamy użycie testowego centrum IoT Hub zamiast centrum w środowisku produkcyjnym. Funkcja ręcznego przechodzenia w tryb failover jest oferowana klientom bez dodatkowych kosztów.

Ten samouczek obejmuje wykonanie następujących zadań:

> [!div class="checklist"]
> * Utwórz centrum IoT Hub przy użyciu witryny Azure Portal. 
> * Wykonaj przejście w tryb failover. 
> * Centrum zostanie uruchomione w lokalizacji dodatkowej.
> * Wykonaj powrót po awarii, aby przywrócić operacje centrum IoT Hub w lokalizacji podstawowej. 
> * Upewnij się, że centrum działa poprawnie we właściwej lokalizacji.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com). 

2. Kliknij pozycję **+ Utwórz zasób**, wybierz pozycję **Internet rzeczy**, a następnie pozycję **IoT Hub**.

   ![Zrzut ekranu przedstawiający tworzenie centrum IoT Hub](./media/tutorial-manual-failover/create-hub-01.png)

3. Wybierz kartę **Podstawowe**. Wypełnij poniższe pola.

    **Subskrypcja**: wybierz subskrypcję platformy Azure, której chcesz użyć.

    **Grupa zasobów**: kliknij pozycję **Utwórz nową** i wprowadź **ManlFailRG** jako nazwę grupy zasobów.

    **Region**: wybierz region znajdujący się w pobliżu, który należy do wersji zapoznawczej. W tym samouczku użyto regionu `westus2`. Przejście w tryb failover można wykonać tylko między sparowanymi geograficznie regionami świadczenia usługi Azure. Regionem sparowanym geograficznie z regionem westus2 jest region WestCentralUS.
    
   **Nazwa centrum IoT Hub**:podaj nazwę centrum IoT Hub. Nazwa centrum musi być unikatowa w skali globalnej. 

   ![Zrzut ekranu przedstawiający okienko Podstawowe umożliwiające utworzenie centrum IoT Hub](./media/tutorial-manual-failover/create-hub-02-basics.png)

   Kliknij pozycję **Przegląd + utwórz**. Zostaną użyte domyślne ustawienia rozmiaru i skali. 

4. Przejrzyj informacje, a następnie kliknij pozycję **Utwórz**, aby utworzyć centrum IoT Hub. 

   ![Zrzut ekranu przedstawiający ostatni etap tworzenia centrum IoT Hub](./media/tutorial-manual-failover/create-hub-03-create.png)

## <a name="perform-a-manual-failover"></a>Ręczne przechodzenie w tryb failover

Istnieje limit dwóch przejść w tryb failover i dwóch powrotów po awarii dziennie w przypadku centrum IoT Hub.

1. Kliknij pozycję **Grupy zasobów**, a następnie wybierz grupę zasobów **ManlFailRG**. Kliknij centrum na liście zasobów. 

2. W obszarze **Odporność** w okienku usługi IoT Hub kliknij pozycję **Ręczne przejście do trybu failover (wersja zapoznawcza)**. Jeśli centrum nie jest skonfigurowane w prawidłowym regionie, opcja ręcznego przejścia w tryb failover jest wyłączona.

   ![Zrzut ekranu przedstawiający okienko właściwości centrum IoT Hub](./media/tutorial-manual-failover/trigger-failover-01.png)

3. W okienku ręcznego przejścia w tryb failover zostaną wyświetlone pozycje **Lokalizacja podstawowa centrum IoT Hub** i **Lokalizacja dodatkowa centrum IoT Hub**. Najpierw jako lokalizacja podstawowa jest ustawiana lokalizacja określona podczas tworzenia centrum IoT Hub. Lokalizacja podstawowa zawsze wskazuje lokalizację, w której centrum jest obecnie aktywne. Lokalizacja dodatkowa to standardowy [region świadczenia usługi Azure sparowany geograficznie](../best-practices-availability-paired-regions.md) z lokalizacją podstawową. Nie można zmienić wartości lokalizacji. W tym samouczku lokalizacja podstawowa to `westus2`, a lokalizacja dodatkowa to `WestCentralUS`.

   ![Zrzut ekranu przedstawiający okienko ręcznego przejścia w tryb failover](./media/tutorial-manual-failover/trigger-failover-02.png)

3. W górnej części okienka ręcznego przejścia w tryb failover kliknij pozycję **Inicjuj pracę w trybie failover**. Zostanie wyświetlone okienko **Potwierdzanie ręcznego przejścia w tryb failover**. Podaj nazwę centrum IoT Hub, aby potwierdzić, że chcesz przełączyć je w tryb failover. Aby zainicjować pracę w trybie failover, kliknij przycisk **OK**.

   Ilość czasu potrzebnego do ręcznego przejścia w tryb failover jest proporcjonalna do liczby urządzeń zarejestrowanych w centrum. Jeśli na przykład masz 100 000 urządzeń, może to potrwać 15 minut, ale jeśli masz pięć milionów urządzeń, może to potrwać godzinę lub dłużej.

4. W okienku **Potwierdzanie ręcznego przejścia w tryb failover** podaj nazwę centrum IoT Hub, aby potwierdzić, że chcesz przełączyć je w tryb failover. Aby zainicjować pracę w trybie failover, kliknij przycisk OK. 

   ![Zrzut ekranu przedstawiający okienko ręcznego przejścia w tryb failover](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   W trakcie procesu ręcznego przechodzenia w tryb failover w okienku ręcznego przejścia w tryb failover jest wyświetlany transparent z informacją o trwającym ręcznym przechodzeniu w tryb failover. 

   ![Zrzut ekranu przedstawiający trwające ręczne przechodzenie w tryb failover](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   Jeśli zamkniesz okienko usługi IoT Hub i otworzysz je ponownie przez kliknięcie go w okienku grupy zasobów, zostanie wyświetlony transparent z informacją o tym, że centrum jest nieaktywne. 

   ![Zrzut ekranu przedstawiający nieaktywne centrum IoT Hub](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   Po zakończeniu region podstawowy i pomocniczy na stronie ręcznego przejścia w tryb failover zostaną przerzucone i centrum ponownie będzie aktywne. W tym przykładzie lokalizacja podstawowa to teraz `WestCentralUS`, a lokalizacja dodatkowa to teraz `westus2`. 

   ![Zrzut ekranu przedstawiający ukończone przejście w tryb failover](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

## <a name="perform-a-failback"></a>Wykonywanie powrotu po awarii 

Po ręcznym przejściu w tryb failover można przełączyć operacje centrum ponownie do oryginalnego regionu podstawowego — taka operacja jest nazywana powrotem po awarii. Jeśli właśnie wykonano przejście w tryb failover, zaczekaj około godziny przed zażądaniem powrotu po awarii. Jeśli spróbujesz wykonać powrót po awarii wcześniej, zostanie wyświetlony komunikat o błędzie.

Powrót po awarii przebiega podobnie do ręcznego przejścia w tryb failover. Poniżej przedstawiono procedurę: 

1. Aby wykonać powrót po awarii, wróć do centrum IoT Hub w okienku usługi IoT Hub.

2. W obszarze **Odporność** w okienku usługi IoT Hub kliknij pozycję **Ręczne przejście do trybu failover (wersja zapoznawcza)**. 

3. W górnej części okienka ręcznego przejścia w tryb failover kliknij pozycję **Inicjuj pracę w trybie failover**. Zostanie wyświetlone okienko **Potwierdzanie ręcznego przejścia w tryb failover**. 

4. W okienku **Potwierdzanie ręcznego przejścia w tryb failover** podaj nazwę centrum IoT Hub, aby potwierdzić, że chcesz wykonać dla niego powrót po awarii. Aby zainicjować powrót po awarii, kliknij przycisk OK. 

   ![Zrzut ekranu przedstawiający żądanie ręcznego powrotu po awarii](./media/tutorial-manual-failover/trigger-failback-01-regions.png)

   Zostaną wyświetlone banery opisane w sekcji przechodzenia w tryb failover. Po ukończeniu powrotu po awarii jako lokalizacja podstawowa jest ponownie wyświetlany region `westus2`, a jako lokalizacja dodatkowa jest wyświetlany region `WestCentralUS` zgodnie z oryginalnym ustawieniem.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

Aby usunąć zasoby utworzone w ramach tego samouczka, usuń grupę zasobów. Ta akcja spowoduje również usunięcie wszystkich zasobów znajdujących się w grupie. W takim przypadku następuje również usunięcie centrum IoT Hub i samej grupy zasobów. 

1. Kliknij pozycję **Grupy zasobów**. 

2. Znajdź i wybierz grupę zasobów **ManlFailRG**. Kliknij je, aby je otworzyć. 

3. Kliknij pozycję **Usuń grupę zasobów**. Po wyświetleniu monitu wprowadź nazwę grupy zasobów i kliknij przycisk **Usuń**, aby potwierdzić. 

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób konfigurowania i wykonywania ręcznego przejścia w tryb failover oraz żądania powrotu po awarii przez wykonanie następujących zadań:

> [!div class="checklist"]
> * Utwórz centrum IoT Hub przy użyciu witryny Azure Portal. 
> * Wykonaj przejście w tryb failover. 
> * Centrum zostanie uruchomione w lokalizacji dodatkowej.
> * Wykonaj powrót po awarii, aby przywrócić operacje centrum IoT Hub w lokalizacji podstawowej. 
> * Upewnij się, że centrum działa poprawnie we właściwej lokalizacji.

Przejdź do następnego samouczka, aby dowiedzieć się, jak zarządzać stanem urządzenia IoT. 

> [!div class="nextstepaction"]
> [Zarządzanie stanem urządzenia IoT](tutorial-device-twins.md)
