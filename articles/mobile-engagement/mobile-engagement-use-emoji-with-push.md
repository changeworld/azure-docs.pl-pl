---
title: Używanie emotikonów Emoji w ramach usługi Azure Mobile Engagement
description: Jak używać emotikony Emoji w powiadomień wypychanych
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 663317d7-3c93-4e8f-b13d-c6fb342124ee
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: b5b0e7bfe07054d093dc164cb5f72bde4ba28170
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="use-emoji-emoticon-within-push-notifications"></a>Użyj emotikon Emoji w powiadomień wypychanych
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Można uwzględnić Emoji emotikony w przypadku powiadomienie wypychane w kilku prostych krokach: 

1. Przede wszystkim należy znaleźć Emoji chcesz wysłać w wiadomości. Upewnij się, że Emoji wybierasz będą obsługiwane przez urządzenie docelowe jako urządzenia produkującej zająć trochę czasu, można dodać nowo zatwierdzonych Emojis do platformy urządzeń. 
2. Na **Windows** — można przejść do tego [łącze](http://apps.timwhitlock.info/emoji/tables/unicode) i skopiuj ikonę "Native".
   
    ![][7] 
3. Na **Mac** — można znaleźć Emojis w słowniku aplikacji w obszarze Edycja -> Emoji & symboli.
   
    ![][6] 
4. Teraz, przejdź do **osiągnąć** karcie w portalu usługi Azure Mobile Engagement. Wybierz typ powiadomienia wypychane (anons sonduje itp). W tym przykładzie wybrany powiadomienia wypychane.
5. Określ innego pola powiadomienia, aż zostanie wyświetlony tekst powiadomienia. Jest to, którym można dodać emotikonu Emoji. Można go umieścić w tytułu i/lub wiadomości. Konieczne będzie przeciągnij i upuść lub skopiuj Emoji, który można znaleźć w powyższych lokalizacjach. 
   
    ![][1]
6. Wypełnij pola inne powiadomienia i zapisz go. 
7. Przy próbie uruchomienia testu lub aktywować anons zobaczysz powiadomienie z emotikon określone.   
   
    ![][3] ![][4] ![][5]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/iOS_Emoji.png
[4]: ./media/mobile-engagement-use-emoji-with-push/Android_Emoji.png
[5]: ./media/mobile-engagement-use-emoji-with-push/WindowsPhone_Emoji.png
[6]: ./media/mobile-engagement-use-emoji-with-push/Mac_SelectEmoji.png
[7]: ./media/mobile-engagement-use-emoji-with-push/Windows_SelectEmoji.png

