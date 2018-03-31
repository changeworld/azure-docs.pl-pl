---
title: Zawartość zestawu SDK aplikacji uniwersalnych systemu Windows
description: Dowiedz się więcej o zawartość zestawu SDK systemu Windows uniwersalnej aplikacji dla usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 8fa1b701-1c2b-4aec-940c-06c974ef5405
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 7b520adcc6af24f7b092580ea82a787a120668bf
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-apps-sdk-content"></a>Zawartość zestawu SDK aplikacji uniwersalnych systemu Windows
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Ten dokument wymieniono i opisano zawartości wdrożone przez zestaw SDK aplikacji.

## <a name="the-resources-folder"></a>`/Resources` Folderu
Ten folder zawiera wszystkie zasoby, które wymaga usługi Mobile Engagement. Można również dostosować je do aplikacji.

* `EngagementConfiguration.xml` Plik konfiguracji Mobile Engagement, jest to, gdzie można dostosować ustawienia usługi Mobile Engagement (parametry połączenia usługi Mobile Engagement, awarii raportu...).

### <a name="html-folder"></a>folder polecenia
* `EngagementNotification.html` : `Notification` Widok html projektu dla transparentach w aplikacji w sieci web.
* `EngagementAnnouncement.html` : `Announcement` Widoku projektowanie html w aplikacji międzysegmentowe widoków witryn sieci web.

### <a name="images-folder"></a>/images folder
* `EngagementIconNotification.png` : Brand ikona wyświetlana po lewej stronie powiadomienia, Zastąp to jedno z ikoną marki.
* `EngagementIconOk.png` : `Ok` Ikona zasięgu zawartości stron dla przycisku akcji lub sprawdzania poprawności.
* `EngagementIconNOK.png` : `NOK` Ikona przycisk weryfikacji zawartości stron w zasięgu jest wyłączony.
* `EngagementIconClose.png` : `Close` Ikony powiadomień reach i zawartość dla przycisku odrzucenia.

### <a name="overlay-folder"></a>/overlay folder
* `EngagementPageOverlay.cs` Strona nakładki odpowiedzialny za dodawanie stopnia zaangażowania osiągnąć w aplikacji interfejsu użytkownika Służącego do jej podrzędnych.

