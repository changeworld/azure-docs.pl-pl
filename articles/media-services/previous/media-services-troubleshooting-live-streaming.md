---
title: Transmisja strumieniowa na żywo podręczniku rozwiązywania problemów | Dokumentacja firmy Microsoft
description: Ten temat zawiera sugestie dotyczące rozwiązywania problemów transmisji strumieniowej na żywo.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 3a7f6c1d-ce57-4fa4-a7a6-edb526b3ffbf
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.openlocfilehash: 84e3e9fc18671d7199eeaf638377a6681cf09fb4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33940928"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Przewodnik rozwiązywania problemów z transmisją strumieniową na żywo
Ten artykuł zawiera sugestie dotyczące rozwiązywania problemów, transmisji strumieniowej na żywo.

## <a name="issues-related-to-on-premises-encoders"></a>Problemy związane z koderów lokalnych
W tej sekcji przedstawiono sugestie dotyczące rozwiązywania problemów związanych z koderów lokalnych, które są skonfigurowane do wysyłania do kanałów AMS, które są włączone dla kodowanie na żywo o pojedynczej szybkości transmisji.

### <a name="problem-would-like-to-see-logs"></a>Problem: Chcesz zobaczyć dzienniki
* **Potencjalny problem**: nie można Znajdź kodera dzienników, która może pomóc w debugowaniu problemów.
  
  * **Telestream Wirecast**: zwykle można znaleźć dzienniki w obszarze C:\Users\{username} \AppData\Roaming\Wirecast\ 
  * **Stanie wolnym Live**: można znaleźć zawiera łącza do dzienników w portalu zarządzania. Polecenie **Statystyka**, następnie **dzienniki**. Na **pliki dziennika** strony, zostanie wyświetlona lista dzienników dla wszystkich elementów LiveEvent; wybierz jedną dopasowania bieżącej sesji. 
  * **Flash Media Live Encoder**: można znaleźć **katalog dziennika...**  przechodząc do **kodowanie dziennika** kartę.

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problem: Nie jest dostępna opcja wyprowadzania progresywnego strumienia
* **Potencjalny problem**: koder używany nie zostać automatycznie bez przeplotu. 
  
    **Kroki rozwiązywania problemów**: Poszukaj usuwania przeplotu opcji w interfejsie kodera. Po włączeniu cofnąć przeplot Sprawdź ponownie ustawienia wyjściowej progresywnego. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problem: Nastąpiła kilka ustawień wyjściowej koder i nadal nie można nawiązać połączenia.
* **Potencjalny problem**: Azure kodowania kanału nie zostało prawidłowo zresetowane. 
  
    **Kroki rozwiązywania problemów**: Upewnij się, że koder jest już Wypychanie do AMS, Zatrzymaj i zresetować kanału. Raz uruchomić ponownie, spróbuj się połączyć kodera przy użyciu nowych ustawień. Jeśli to nadal nie rozwiąże problemu, spróbuj utworzyć nowy kanał całkowicie, czasami kanałów może zostać uszkodzony po kilku nieudanych próbach.  
* **Potencjalny problem**: rozmiar GOP lub klatki ustawienia nie są optymalne. 
  
    **Kroki rozwiązywania problemów**: GOP zalecany rozmiar lub klatki kluczowej odbywa się dwie sekundy. Niektóre kodery obliczyć tego ustawienia w liczbę ramek, podczas gdy inne sekund. Na przykład: podczas wyprowadzania 30 klatek na sekundę, rozmiar GOP byłoby 60 ramki, który jest odpowiednikiem 2 sekundy.  
* **Potencjalny problem**: blokuje porty zamkniętego strumienia. 
  
    **Kroki rozwiązywania problemów**: podczas przesyłania strumieniowego za pośrednictwem protokołu RTMP, sprawdź ustawienia zapory i/lub serwera proxy, aby upewnić się, że porty wyjściowe 1935 i 1936 są otwarte. 

> [!NOTE]
> Jeśli po wykonaniu kroków, które nadal nie można pomyślnie przesyłania strumieniowego, należy przesłać biletu pomocy technicznej przy użyciu portalu Azure.
> 
> 

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

