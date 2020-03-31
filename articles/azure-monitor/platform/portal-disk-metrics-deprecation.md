---
title: Wycofanie danych dysku w witrynie Azure portal | Dokumenty firmy Microsoft
description: Dowiedz się, które metryki dysku zostały przestarzałe i jak zaktualizować alerty metryki, aby użyć nowych metryk.
services: azure-monitor
ms.subservice: metrics
ms.topic: conceptual
author: albecker1
ms.author: albecker
ms.date: 03/12/2020
ms.openlocfilehash: f2b960c2198800e04da77ad6b5be78d7b4762354
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299805"
---
# <a name="disk-metrics-deprecation-in-the-azure-portal"></a>Wycofanie danych dysku w witrynie Azure portal

Przestarzałe metryki związane z dyskiem zostaną wkrótce usunięte z witryny Azure portal. Nowa wersja każdej przestarzałej metryki jest dostępna do użycia. Z tego artykułu dowiesz się, które metryki są nowe i jak zaktualizować alerty metryki, aby ich używać.

## <a name="list-of-new-metrics"></a>Lista nowych wskaźników

W tej tabeli mapuje każdy przestarzały metryki do jego odpowiedniego nowego metryki. 

|Przestarzała metryka|Nowa (zastępcza) metryka|
|----|----|
|QD dysku danych (przestarzałe)|Głębokość kolejki dysków danych (wersja zapoznawcza)|
|Bajty odczytu dysku danych/s (przestarzałe)|Bajty odczytu dysku danych/s (wersja zapoznawcza)|
|Operacje odczytu dysku danych/s (przestarzałe)|Operacje odczytu dysku danych/s (wersja zapoznawcza)|
|Bajty zapisu dysku danych/s (przestarzałe)|Bajty zapisu dysku danych/s (wersja zapoznawcza)|
|Operacje zapisu na dysku danych/s (przestarzałe)|Operacje zapisu na dysku danych/s (wersja zapoznawcza)|
|OS QD (przestarzałe)|Głębokość kolejki systemu operacyjnego (wersja zapoznawcza)|
|Bajty odczytu systemu operacyjnego/s (przestarzałe)|Bajty odczytu systemu operacyjnego/s (wersja zapoznawcza)|
|Operacje odczytu systemu operacyjnego/s (przestarzałe)|Operacje odczytu systemu operacyjnego/s (wersja zapoznawcza)|
|Bajty zapisu systemu operacyjnego/s (przestarzałe)|Bajty zapisu systemu operacyjnego/s (wersja zapoznawcza)|
|Operacje zapisu systemu operacyjnego/s (przestarzałe)|Operacje zapisu systemu operacyjnego/s (wersja zapoznawcza)|

<a id="update-metrics" />

## <a name="migrate-metrics-in-your-metric-alerts"></a>Migrowanie danych w alertach metryk

Zaktualizuj alerty metryki, aby używać nowych danych.

1. W witrynie Azure portal wyszukaj **alerty**. Następnie w sekcji **Usługi** wybierz pozycję **Alerty**.

   > [!div class="mx-imgBorder"]
   > ![Opis obrazu](./media/portal-disk-metrics-deprecation/alert-service-azure-portal.png)

2. Na stronie **Alerty** wybierz przycisk **Zarządzaj regułami alertów.** 

   > [!div class="mx-imgBorder"]
   > ![Opis obrazu](./media/portal-disk-metrics-deprecation/manage-alert-rules-button.png)

3. Na liście rozwijanej **Grupa zasobów** zaznacz pole wyboru **Maszyny wirtualne,** a następnie na liście rozwijanej **Typ sygnału** zaznacz pole wyboru **Metryki.** 

   > [!div class="mx-imgBorder"]
   > ![Opis obrazu](./media/portal-disk-metrics-deprecation/filter-alerts.png)

4. Na liście metryk należy zidentyfikować warunki, które odnoszą się do dysków. Kliknij nazwę reguły. 

   Nazwa jest wyświetlana jako hiperłącze w kolumnie **Nazwa** tabeli.

   > [!div class="mx-imgBorder"]
   > ![Opis obrazu](./media/portal-disk-metrics-deprecation/find-disk-conditions.png)

5. W sekcji **Warunki** na stronie **Zarządzanie regułami** kliknij warunek alertu. 

   Warunek jest wyświetlany jako hiperłącze.  

   > [!div class="mx-imgBorder"]
   > ![Opis obrazu](./media/portal-disk-metrics-deprecation/adjust-condition.png)

   Zostanie wyświetlona strona **Konfiguruj logikę sygnału,** a ustawienia warunku pojawią się w sekcji **Logika alertu** na tej stronie.

6. Po usunięciu przestarzałej metryki należy zarejestrować te ustawienia.

   > [!div class="mx-imgBorder"]
   > ![Opis obrazu](./media/portal-disk-metrics-deprecation/condition-rules.png)

   > [!TIP] 
   > Należy rozważyć przechwycenie tych ustawień na zrzucie ekranu lub w pliku tekstowym. 

7. Kliknij **łącze Wyboru Wstecz, aby zasygnalizować.**

   > [!div class="mx-imgBorder"]
   > ![Opis obrazu](./media/portal-disk-metrics-deprecation/back-to-signal-selection.png)

8. Na stronie **Konfigurowanie logiki sygnału** wybierz odpowiednią metrykę zastępczą (nową metrykę). Użyj [tabeli,](#update-metrics) która pojawia się wcześniej w tym artykule, aby oznaczyć nazwę nowej metryki.

   > [!TIP] 
   > Zacznij wpisywać tekst na pasku wyszukiwania, aby zawęzić listę nazw metryk. 

   > [!div class="mx-imgBorder"]
   > ![Opis obrazu](./media/portal-disk-metrics-deprecation/choose-new-metric.png)

9. Wybierz przycisk **Gotowe.** 

   > [!div class="mx-imgBorder"]
   > ![Opis obrazu](./media/portal-disk-metrics-deprecation/set-new-metric.png)

10. Zaobe go to zatwierdź, wybierając przycisk **Zapisz.** 

    > [!div class="mx-imgBorder"]
    > ![Opis obrazu](./media/portal-disk-metrics-deprecation/save-new-metric.png)






