---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: b2dec95e0258933b50d4437f1cb317639b62883d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59804800"
---
### <a name="upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-storsomple-adapter-for-sharepoint"></a>Uaktualnienie programu SharePoint 2010 do programu SharePoint 2013, a następnie zainstaluj StorSomple Adapter dla programu SharePoint
> [!IMPORTANT]
> Wszystkie pliki, które wcześniej zostały przeniesione do magazynu zewnętrznego za pośrednictwem SPZ nie będą dostępne do momentu zakończenia uaktualnienia i SPZ jest aktywna, ponownie. Aby ograniczyć wpływu na użytkowników, wykonaj uaktualnienie ani ponowna instalacja podczas okna zaplanowanej konserwacji.
> 
> 

#### <a name="to-upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-adapter"></a>Aby uaktualnić program SharePoint 2010 do programu SharePoint 2013, a następnie zainstaluj adapter
1. W farmie programu SharePoint 2010 należy pamiętać, ścieżka magazynu obiektów BLOB dla zawartości baz danych, dla których włączono SPZ i zewnętrznych obiektów blob. 
2. Zainstaluj i skonfiguruj nową farmę programu SharePoint 2013. 
3. Przenoszenie baz danych, aplikacji i kolekcji witryn z farmy programu SharePoint 2010 nową farmę programu SharePoint 2013. Aby uzyskać instrukcje, przejdź do [Przegląd procesu uaktualniania do programu SharePoint 2013](https://technet.microsoft.com/library/cc262483.aspx).
4. Instalowanie adaptera StorSimple dla programu SharePoint w farmie nowy. Przejdź do [zainstalować Adapter usługi StorSimple dla programu SharePoint](#install-the-storsimple-adapter-for-sharepoint) procedury.
5. Korzystając z informacji zanotowaną w kroku 1, Włącz SPZ dla tego samego zestawu baz danych zawartości i zapewniają taką samą ścieżkę magazynu obiektów BLOB, używanym w instalacji programu SharePoint 2010. Przejdź do [skonfigurować SPZ](#configure-rbs) procedury. Po wykonaniu tego kroku, wcześniej zewnętrznych plików powinny być dostępne z nowej farmy. 

### <a name="upgrade-the-storsimple-adapter-for-sharepoint"></a>Uaktualnianie adaptera StorSimple dla programu SharePoint
> [!IMPORTANT]
> Należy zaplanować to uaktualnienie wystąpić podczas okna zaplanowanej konserwacji z następujących powodów:
> 
> * Wcześniej zewnętrznych zawartość nie będzie dostępna do momentu karta zostanie ponownie zainstalowany.
> * Żadnej zawartości, przekazywane do witryny po odinstalowaniu wcześniejszej wersji adaptera StorSimple dla programu SharePoint, ale przed zainstalowaniem nowej wersji będą przechowywane w bazie danych zawartości. Musisz przenieść tę zawartość z urządzeniem StorSimple, po zainstalowaniu nowej karty. Można użyć programu Microsoft `RBS Migrate()` dołączone do programu SharePoint, aby przenieść zawartość polecenia cmdlet programu PowerShell. Aby uzyskać więcej informacji, zobacz [migracji zawartości do lub z SPZ](https://technet.microsoft.com/library/ff628255.aspx). 
> 
> 

#### <a name="to-upgrade-the-storsimple-adapter-for-sharepoint"></a>Aby uaktualnić adaptera StorSimple dla programu SharePoint
1. Odinstaluj poprzednią wersję adaptera StorSimple dla programu SharePoint.
   
   > [!NOTE]
   > Spowoduje to wyłączenie automatycznego SPZ w bazach danych zawartości. Jednak istniejących obiektów blob pozostaną na urządzeniu StorSimple. Ponieważ SPZ jest wyłączona i nie zostały poddane migracji obiektów blob do bazy danych zawartości, wszystkie żądania dla tych obiektów blob nie powiedzie się. 
   > 
   > 
2. Zainstaluj nowy adaptera StorSimple dla programu SharePoint. Nowa karta będzie automatycznie rozpoznawać baz danych zawartości, które zostały wcześniej włączona lub wyłączona dla SPZ i użyje poprzednich ustawień.

