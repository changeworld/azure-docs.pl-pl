---
title: Przywracanie aplikacji na platformie Azure
description: Dowiedz się, jak przywracanie z migawki aplikacji.
services: app-service
documentationcenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.topic: article
ms.date: 04/04/2018
ms.author: aelnably;nicking
ms.openlocfilehash: e1ae8fcc30323c865aa96937f43054515f293394
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Przywracanie z migawki aplikacji na platformie Azure
W tym artykule przedstawiono sposób przywracania aplikacji w [usłudze Azure App Service](../app-service/app-service-web-overview.md) z migawki. Aplikację można przywrócić do poprzedniego stanu, na podstawie jednej z aplikacji migawki. Nie trzeba włączać migawki kopii zapasowej, platforma automatycznie zapisuje migawkę wszystkich aplikacji w celu odzyskiwania danych.

Migawki są kopie przyrostowe w tle i oferują wiele zalet w porównaniu regular [kopii zapasowych](web-sites-backup.md):
- Nie błędów kopiowania pliku z powodu blokady pliku.
- Nie limitu rozmiaru magazynu.
- Nie jest wymagana żadna konfiguracja.

Przywracanie z migawki jest dostępna dla aplikacji działających **Premium** warstwy lub nowszej. Aby uzyskać informacji na temat skalowania aplikacji w górę, zobacz [skalowanie w górę aplikacji na platformie Azure](web-sites-scale.md).

## <a name="limitations"></a>Ograniczenia

- Funkcja jest obecnie w wersji zapoznawczej.
- Można przywrócić tylko do tej samej aplikacji lub gnieździe należących do danej aplikacji.
- Usługa aplikacji zatrzymuje aplikacji docelowej lub miejsca docelowego podczas przywracania.
- Usługi aplikacji przechowuje trzy miesiące warto migawek na potrzeby odzyskiwania danych platformy.
- Migawki można przywrócić tylko w ciągu ostatnich 30 dni.
 

## <a name="restore-an-app-from-a-snapshot"></a>Przywracanie z migawki aplikacji

1. Na **ustawienia** strony aplikacji w [portalu Azure](https://portal.azure.com), kliknij przycisk **kopii zapasowych** do wyświetlenia **kopii zapasowych** strony. Następnie kliknij przycisk **przywrócić** w obszarze **Snapshot(Preview)** sekcji.
   
    ![](./media/app-service-web-restore-snapshots/1.png)

2. W **przywrócić** wybierz migawek do przywrócenia.
   
    ![](./media/app-service-web-restore-snapshots/2.png)
   
3. Określ miejsce docelowe przywracania aplikacji w **docelową lokalizację przywracania**.
   
    ![](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > Jeśli wybierzesz **Zastąp**, wszystkie istniejące dane w bieżącym systemie plików aplikacji jest usunięte i zastąpione. Przed kliknięciem przycisku **OK**, upewnij się, że jest to, co chcesz zrobić.
   > 
   > 
      
   > [!Note]
   > Ze względu na bieżące ograniczenia techniczne można przywracać tylko do aplikacji w tej samej jednostce skali. To ograniczenie zostanie usunięta w przyszłej wersji.
   > 
   > 
   
    Możesz wybrać **istniejącej aplikacji** do przywrócenia do gniazda. Przed użyciem tej opcji należy utworzono już miejsca w aplikacji.

4. Można przywrócić konfigurację lokacji.
   
    ![](./media/app-service-web-restore-snapshots/4.png)

5. Kliknij przycisk **OK**.
