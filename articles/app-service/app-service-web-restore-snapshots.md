---
title: Przywracanie aplikacji z migawki
description: Dowiedz się, jak przywrócić aplikację z migawki. Odzyskiwanie po nieoczekiwanej utracie danych w warstwie Premium dzięki automatycznym kopiowaniom w tle.
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.topic: article
ms.date: 04/04/2018
ms.reviewer: nicking
ms.custom: seodec18
ms.openlocfilehash: b17a49535541c8f75f65cdbe9986a895427f3877
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255152"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Przywracanie aplikacji na platformie Azure z migawki
W tym artykule pokazano, jak przywrócić aplikację w [usłudze Azure App Service](../app-service/overview.md) z migawki. Możesz przywrócić aplikację do poprzedniego stanu na podstawie jednej z migawek aplikacji. Nie trzeba włączać kopii zapasowej migawek, platforma automatycznie zapisuje migawkę wszystkich aplikacji do celów odzyskiwania danych.

Migawki są przyrostowymi kopiami w tle i oferują kilka zalet w stosunku do [zwykłych kopii zapasowych:](manage-backup.md)
- Brak błędów kopiowania plików z powodu blokad plików.
- Brak ograniczeń rozmiaru magazynu.
- Nie jest wymagana konfiguracja.

Przywracanie z migawek jest dostępne dla aplikacji działających w warstwie **Premium** lub wyższej. Aby uzyskać informacje dotyczące skalowania aplikacji, zobacz [Skalowanie w górę aplikacji na platformie Azure](manage-scale-up.md).

## <a name="limitations"></a>Ograniczenia

- Funkcja jest obecnie w wersji zapoznawczej.
- Możesz przywrócić tylko tę samą aplikację lub gniazdo należące do tej aplikacji.
- Usługa App Service zatrzymuje docelową aplikację lub gniazdo docelowe podczas przywracania.
- Usługa App Service przechowuje migawki na trzy miesiące do celów odzyskiwania danych platformy.
- Migawki można przywrócić tylko dla ostatnich 30 dni.
- Usługi app services uruchomione w środowisku usługi app service nie obsługują migawek.
 

## <a name="restore-an-app-from-a-snapshot"></a>Przywracanie aplikacji z migawki

1. Na stronie **Ustawienia** aplikacji w [witrynie Azure portal](https://portal.azure.com)kliknij pozycję Kopie **zapasowe,** aby wyświetlić stronę **Kopie zapasowe.** Następnie kliknij pozycję **Przywróć** w sekcji **Migawka (Podgląd).**
   
    ![](./media/app-service-web-restore-snapshots/1.png)

2. Na stronie **Przywracanie** wybierz migawkę do przywrócenia.
   
    ![](./media/app-service-web-restore-snapshots/2.png)
   
3. Określ miejsce docelowe przywracania aplikacji w **obszarze Przywróć miejsce docelowe**.
   
    ![](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > Jeśli wybierzesz **opcję Zastąp,** wszystkie istniejące dane w bieżącym systemie plików aplikacji zostaną wymazane i zastąpione. Przed kliknięciem **przycisku OK**upewnij się, że jest to, co chcesz zrobić.
   > 
   > 
      
   > [!Note]
   > Ze względu na bieżące ograniczenia techniczne można przywrócić tylko do aplikacji w tej samej jednostce skalowania. To ograniczenie zostanie usunięte w przyszłej wersji.
   > 
   > 
   
    Można wybrać **istniejącą aplikację,** aby przywrócić do gniazda. Przed użyciem tej opcji należy już utworzyć gniazdo w aplikacji.

4. Można przywrócić konfigurację witryny.
   
    ![](./media/app-service-web-restore-snapshots/4.png)

5. Kliknij przycisk **OK**.
