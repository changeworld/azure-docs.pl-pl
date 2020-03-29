---
title: Przewodnik rozwiązywania problemów z transmisją na żywo | Dokumenty firmy Microsoft
description: W tym artykule przedstawiono sugestie dotyczące rozwiązywania problemów z przesyłaniem strumieniowym usługi Azure Media Services na żywo.
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
ms.openlocfilehash: b00df5e8176aaad86be5cf3ae4e04c736f36cf5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74885606"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Przewodnik rozwiązywania problemów z transmisją strumieniową na żywo  

W tym artykule przedstawiono sugestie dotyczące rozwiązywania problemów z transmisją strumieniową na żywo.

## <a name="issues-related-to-on-premises-encoders"></a>Zagadnienia związane z koderami lokalnymi
W tej sekcji przedstawiono sugestie dotyczące rozwiązywania problemów związanych z koderami lokalnymi, które są skonfigurowane do wysyłania pojedynczego strumienia szybkości transmisji bitów do kanałów usługi AMS, które są włączone do kodowania na żywo.

### <a name="problem-would-like-to-see-logs"></a>Problem: Chciałbym zobaczyć dzienniki
* **Potencjalny problem:** Nie można znaleźć dzienników kodera, które mogą pomóc w debugowaniu problemów.
  
  * **Telestream Wirecast**: Dzienniki można zwykle znaleźć\{w obszarze C:\Users username}\AppData\Roaming\Wirecast\ 
  * **Elemental Live**: Można znaleźć linki do dzienników w portalu zarządzania. Kliknij **statystyki**, a następnie **dzienniki**. Na stronie **Pliki dziennika** zostanie wyświetlona lista dzienników dla wszystkich elementów LiveEvent; wybierz tę odpowiadającą bieżącej sesji. 
  * **Program Flash Media Live Encoder**: Katalog dzienników można **znaleźć,** przechodząc do karty **Dziennik kodowania.**

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problem: Nie ma opcji wyprowadzania strumienia progresywnego
* **Potencjalny problem:** Używany koder nie powoduje automatycznego usuwania przeplotu. 
  
    **Kroki rozwiązywania problemów:** Poszukaj opcji usuwania przeplotu w interfejsie kodera. Po włączeniu usuwania przeplotu sprawdź ponownie, czy nie ma progresywnych ustawień wyjściowych. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problem: Wypróbowano kilka ustawień wyjściowych kodera i nadal nie można się połączyć.
* **Potencjalny problem:** Kanał kodowania platformy Azure nie został poprawnie zresetowany. 
  
    **Kroki rozwiązywania problemów:** Upewnij się, że koder nie jest już wypychaniem do ams, zatrzymać i zresetować kanał. Po ponownym uruchomieniu spróbuj połączyć koder z nowymi ustawieniami. Jeśli to nadal nie rozwiąże problemu, spróbuj całkowicie utworzyć nowy kanał, czasami kanały mogą ulec uszkodzeniu po kilku nieudanych próbach.  
* **Potencjalny problem:** Ustawienia rozmiaru GOP lub klatki kluczowej nie są optymalne. 
  
    **Kroki rozwiązywania problemów:** Zalecany rozmiar GOP lub interwał klatki kluczowej wynosi dwie sekundy. Niektóre kodery obliczają to ustawienie w liczbie klatek, podczas gdy inne używają sekund. Na przykład: Podczas wyprowadzania 30 klatek na sekundę rozmiar GOP wynosi 60 klatek, co odpowiada 2 sekundom.  
* **Potencjalny problem:** Zamknięte porty blokują strumień. 
  
    **Kroki rozwiązywania problemów:** Podczas przesyłania strumieniowego za pośrednictwem rtmp, sprawdź ustawienia zapory i / lub serwera proxy, aby potwierdzić, że porty wychodzące 1935 i 1936 są otwarte. 

> [!NOTE]
> Jeśli po wykonać kroki rozwiązywania problemów nadal nie można pomyślnie przesyłać strumieniowo, prześlij zgłoszenie pomocy technicznej za pomocą witryny Azure portal.
> 
> 

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

