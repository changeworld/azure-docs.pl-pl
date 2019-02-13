---
title: Usługi Azure Data Factory danych przepływ przenoszenia węzłów
description: Jak przenieść węzły w usłudze Azure Data Factory Diagram przepływu danych
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 4328a090478747e9acbfb25cca45b330cd4a300b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213405"
---
#<a name="data-factory-data-flow-move-nodes"></a>Fabryka danych przepływ przenoszenia węzłami

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Agreguj opcje przekształcania](media/data-flow/agghead.png "agregatora nagłówka")

Powierzchni projektowej przepływ danych fabryki danych Azure jest powierzchni "konstrukcji", które służy do tworzenia danych przepływów góra dół od lewej do prawej. Brak przybornika, dołączone do każdego przekształcenia ze znakiem plus (+) symbol. Koncentrować się na logice biznesowej zamiast łączenie z węzłami za pośrednictwem krawędzie w środowisku grupy DAG dowolnej postaci.

Tak, bez modelu przeciągania i upuszczania ich sposób "Przenieś" węzłem transformacji jest zmiana strumienia przychodzącego. Zamiast tego możesz będą się poruszały przekształceń, zmieniając "przychodzącego strumienia".

W przepływie danych fabryki danych Azure strumienie reprezentują przepływ danych. W okienku ustawienia przekształcania zobaczysz pola "Przychodzący strumień". Oznacza to, które przychodzący strumień danych jest wprowadzając przekształcenia. Klikając nazwę Stream przychodzące i wybierając inny strumień danych, można zmienić lokalizacji fizycznej przekształcenie węzła na wykresie. Przekształcenie bieżącego wraz z wszystkich kolejnych przekształceń w strumieniu następnie zostanie przeniesione do nowej lokalizacji.

Jeśli przenosisz przekształcania przekształceń co najmniej jeden po nim nową lokalizację przepływu danych zostaną dołączone za pomocą nowej gałęzi.

Jeśli masz nie kolejnych przekształceń po węźle, który został wybrany tylko tej transformacji zostanie przeniesione do nowej lokalizacji.
