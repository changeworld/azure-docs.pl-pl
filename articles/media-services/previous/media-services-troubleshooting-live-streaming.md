---
title: Przewodnik rozwiązywania problemów z transmisją strumieniową na żywo | Dokumentacja firmy Microsoft
description: Ten temat zawiera sugestie dotyczące rozwiązywania problemów transmisji strumieniowej na żywo.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: f502e3228274840d23b9f52512280fc0d9f0553b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60544698"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Przewodnik rozwiązywania problemów z transmisją strumieniową na żywo  

Ten artykuł zawiera sugestie dotyczące rozwiązywania problemów, transmisji strumieniowej na żywo.

## <a name="issues-related-to-on-premises-encoders"></a>Problemy związane z koderów lokalnych
W tej sekcji przedstawiono sugestie na temat rozwiązywania problemów związanych z koderów lokalnych, które są skonfigurowane do wysyłania strumienia o pojedynczej szybkości transmisji bitów w kanałach usługi AMS, obsługującymi kodowanie na żywo.

### <a name="problem-would-like-to-see-logs"></a>Problem: Chcesz zobaczyć dzienniki
* **Potencjalny problem**: Nie można odnaleźć kodera dzienników, które mogą pomóc w debugowaniu problemów.
  
  * **Telestream Wirecast**: Zwykle można znaleźć dzienników w obszarze C:\Users\{username} \AppData\Roaming\Wirecast\ 
  * **Elemental Live**: Możesz znaleźć zawiera linki do dzienników w portalu zarządzania. Kliknij pozycję **statystyki**, następnie **dzienniki**. Na **pliki dziennika** stronie zostanie wyświetlona lista dzienników dla wszystkich elementów element LiveEvent; wybierz jeden z pasujących bieżącej sesji. 
  * **Flash Media Live Encoder**: Możesz znaleźć **katalog dziennika...**  , przechodząc do **kodowanie dziennika** kartę.

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problem: Nie ma opcji dla podawania progresywnego strumienia
* **Potencjalny problem**: Koder używane automatycznie nie bez przeplotu. 
  
    **Kroki rozwiązywania problemów**: Poszukaj usuwania przeplotu opcji w interfejsie kodera. Po włączeniu cofnąć przeplotu Sprawdź ponownie ustawienia wyjściowej progresywnego. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problem: Nastąpiła kilka ustawień danych wyjściowych encoder i nadal nie można się połączyć.
* **Potencjalny problem**: Azure kodowania kanału nie zostało prawidłowo zresetowane. 
  
    **Kroki rozwiązywania problemów**: Upewnij się, że koder jest już Wypychanie do usługi AMS, Zatrzymaj i zresetować kanał. Gdy uruchomiona ponownie, spróbuj łączenie koder z nowymi ustawieniami. Jeśli to nadal nie rozwiąże problemu, spróbuj utworzyć nowy kanał w całości, czasami kanały uszkodzeniu po kilku nieudanych próbach.  
* **Potencjalny problem**: Rozmiar GOP lub ramki kluczowe ustawienia nie są optymalne. 
  
    **Kroki rozwiązywania problemów**: Zalecane GOP rozmiar lub ramka kluczowa odbywa się dwóch sekund. Niektóre koderów obliczyć tego ustawienia w liczbę ramek, a inni korzystają w ciągu kilku sekund. Na przykład: Dla 30 kl. / s, rozmiar GOP byłoby 60 klatek, który jest odpowiednikiem 2 sekundy.  
* **Potencjalny problem**: Porty zamknięte blokują strumienia. 
  
    **Kroki rozwiązywania problemów**: Podczas przesyłania strumieniowego za pośrednictwem protokołu RTMP, sprawdź ustawienia zapory i/lub serwer proxy, aby upewnić się, że porty wychodzące 1935 i 1936 są otwarte. 

> [!NOTE]
> Jeśli po wykonaniu czynności dotyczące rozwiązywania problemów, które nadal nie można pomyślnie przesyłania strumieniowego, Prześlij zgłoszenie do pomocy technicznej w witrynie Azure portal.
> 
> 

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

