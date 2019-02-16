---
title: Usługi Azure Data Factory danych przepływ przenoszenia węzłów
description: Jak przenieść węzły w usłudze Azure Data Factory Diagram przepływu danych
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 84a949d0e8fc915441231e1d0b07c3492776cd41
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2019
ms.locfileid: "56327140"
---
# <a name="mapping-data-flow-move-nodes"></a>Mapowanie węzłów przenoszenia przepływu danych

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Agreguj opcje przekształcania](media/data-flow/agghead.png "agregatora nagłówka")

Powierzchni projektowej przepływ danych fabryki danych Azure jest powierzchni "konstrukcji", które służy do tworzenia danych przepływów góra dół od lewej do prawej. Brak przybornika, dołączone do każdego przekształcenia ze znakiem plus (+) symbol. Koncentrować się na logice biznesowej zamiast łączenie z węzłami za pośrednictwem krawędzie w środowisku grupy DAG dowolnej postaci.

Tak bez paradygmatu przeciągnij i upuść sposób "Przenieś" węzłem transformacji jest zmiana przychodzącego strumienia. Zamiast tego możesz będą się poruszały przekształceń, zmieniając "przychodzącego strumienia".

W przepływie danych fabryki danych Azure strumienie reprezentują przepływ danych. W okienku ustawienia przekształcania zobaczysz pola "Przychodzący strumień". Oznacza to, które przychodzący strumień danych jest wprowadzając przekształcenia. Klikając nazwę Stream przychodzące i wybierając inny strumień danych, można zmienić lokalizacji fizycznej przekształcenie węzła na wykresie. Przekształcenie bieżącego wraz z wszystkich kolejnych przekształceń w strumieniu następnie zostanie przeniesione do nowej lokalizacji.

Jeśli przenosisz przekształcania przekształceń co najmniej jeden po nim nową lokalizację przepływu danych zostaną dołączone za pomocą nowej gałęzi.

Jeśli masz nie kolejnych przekształceń po węźle, który został wybrany tylko tej transformacji zostanie przeniesione do nowej lokalizacji.
