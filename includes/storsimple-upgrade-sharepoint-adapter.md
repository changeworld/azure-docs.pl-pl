---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: b2dec95e0258933b50d4437f1cb317639b62883d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183274"
---
### <a name="upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-storsomple-adapter-for-sharepoint"></a>Uaktualnianie programu SharePoint 2010 do programu SharePoint 2013, a następnie instalowanie karty StorSomple dla programu SharePoint
> [!IMPORTANT]
> Wszystkie pliki, które zostały wcześniej przeniesione do magazynu zewnętrznego za pośrednictwem SPZ, nie będą dostępne do czasu zakończenia uaktualnienia i ponownego włączenia funkcji SPZ. Aby ograniczyć wpływ na użytkownika, wykonaj wszelkie uaktualnienia lub ponownej instalacji podczas okna planowanej konserwacji.
> 
> 

#### <a name="to-upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-adapter"></a>Aby uaktualnić program SharePoint 2010 do programu SharePoint 2013, a następnie zainstalować kartę
1. W farmie programu SharePoint 2010 zanotuj ścieżkę magazynu obiektów BLOB dla zewnętrznych bloków BLOB i bazy danych zawartości, dla których włączono spz. 
2. Zainstaluj i skonfiguruj nową farmę programu SharePoint 2013. 
3. Przenoszenie baz danych, aplikacji i zbiorów witryn z farmy programu SharePoint 2010 do nowej farmy programu SharePoint 2013. Aby uzyskać instrukcje, przejdź do [przeglądu procesu uaktualniania do programu SharePoint 2013](https://technet.microsoft.com/library/cc262483.aspx).
4. Zainstaluj kartę StorSimple dla programu SharePoint w nowej farmie. Przejdź do [instalowania karty StorSimple dla programu SharePoint](#install-the-storsimple-adapter-for-sharepoint) dla procedur.
5. Korzystając z informacji, które zostały odnotowane w kroku 1, włącz SPZ dla tego samego zestawu baz danych zawartości i podaj tę samą ścieżkę magazynu obiektów BLOB, która została użyta w instalacji programu SharePoint 2010. Przejdź do [konfigurowania SPZ](#configure-rbs) dla procedur. Po wykonaniu tego kroku wcześniej zewnętrzną plików powinny być dostępne z nowej farmy. 

### <a name="upgrade-the-storsimple-adapter-for-sharepoint"></a>Uaktualnianie karty StorSimple dla programu SharePoint
> [!IMPORTANT]
> Należy zaplanować to uaktualnienie, aby wystąpiło podczas okna planowanej konserwacji z następujących powodów:
> 
> * Wcześniej zewnętrzna zawartość nie będzie dostępna, dopóki karta nie zostanie ponownie zainstalowana.
> * Każda zawartość przekazana do witryny po odinstalowaniu poprzedniej wersji karty StorSimple dla programu SharePoint, ale przed zainstalowaniem nowej wersji, będzie przechowywana w bazie danych zawartości. Po zainstalowaniu nowej karty należy przenieść tę zawartość do urządzenia StorSimple. Do migracji zawartości `RBS Migrate()` można użyć polecenia cmdlet programu Microsoft PowerShell dołączonego do programu SharePoint. Aby uzyskać więcej informacji, zobacz [Migrowanie zawartości do lub z SPZ](https://technet.microsoft.com/library/ff628255.aspx). 
> 
> 

#### <a name="to-upgrade-the-storsimple-adapter-for-sharepoint"></a>Aby uaktualnić kartę StorSimple dla programu SharePoint
1. Odinstaluj poprzednią wersję karty StorSimple dla programu SharePoint.
   
   > [!NOTE]
   > Spowoduje to automatyczne wyłączenie SPZ w bazach danych zawartości. Jednak istniejące obiekty BLOB pozostaną na urządzeniu StorSimple. Ponieważ SPZ jest wyłączona i bloków BLOB nie zostały zmigrowane z powrotem do baz danych zawartości, wszelkie żądania dla tych bloków BLOB zakończy się niepowodzeniem. 
   > 
   > 
2. Zainstaluj nową kartę StorSimple dla programu SharePoint. Nowa karta automatycznie rozpozna bazy danych zawartości, które zostały wcześniej włączone lub wyłączone dla SPZ i użyje poprzednich ustawień.

