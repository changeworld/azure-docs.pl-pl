---
title: Terminologia związana z usługą Azure Data Share
description: Dowiedz się więcej o typowych terminach używanych do opisywania zasobów używanych w usłudze Azure Data Share (dostawca danych, konsument danych, udostępnianie danych, subskrypcja udostępniania, migawka, zaproszenie, odbiorca).
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 33532380d8f98df44029eeea998130d1da5fdafd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73468560"
---
# <a name="azure-data-share-concepts"></a>Pojęcia dotyczące udostępniania danych platformy Azure 

Usługa Azure Data Share przedstawia nową terminologię związaną z udostępnianiem danych. W tym artykule wyjaśniono niektóre często używane terminy, które mogą być widoczne używane w całej usłudze. 

## <a name="data-provider"></a>Dostawca danych

Dostawca danych to organizacja, która udostępnia dane swoim konsumentom. Zazwyczaj dostawcą danych może być właściciel lub kurator danych. Dostawcy danych chcą udostępniać dane różnych typów. Niektóre przykłady danych, które dostawca danych może chcieć udostępnić, obejmują nieprzetworzone dane, takie jak dane punktu sprzedaży lub szeregów czasowych. Dostawca danych może również chcieć udostępnić wstępnie przetworzone, wyselekcjonowane dane, które już zawierają analizy i szczegółowe informacje. 

## <a name="data-consumer"></a>Konsument danych 

Konsument danych to organizacja odbierająca dane od dostawcy danych. Konsument danych może chcieć dołączyć do udostępnionych danych z własnymi danymi, aby uzyskać szczegółowe informacje. W niektórych przypadkach konsument danych może odbierać dane, które zostały już przetworzone. 

## <a name="data-share"></a>Data Share

Udział danych to grupa zestawów danych, które są współużytkowane jako pojedyncza jednostka. Zestawy danych mogą pochodzić z wielu źródeł danych platformy Azure, które są obsługiwane przez usługę Azure Data Share. Obecnie usługa Azure Data Share obsługuje usługę Azure Blob Storage i magazyn usługi Azure Data Lake Store. 

## <a name="share-subscription"></a>Udostępnij subskrypcję 

Subskrypcja udostępniania jest tworzona, gdy konsument danych akceptuje zaproszenie do udostępniania danych od dostawcy danych. Dostawcy danych mogą wyświetlać subskrypcje aktywnych udziałów, przechodząc do **udziałów wysłanych** na swoim koncie udostępniania danych platformy Azure i wybierając pozycję **Udostępnij subskrypcje**.

Konsument danych może sprawdzić, czy ma aktywną subskrypcję udziałów, przechodząc do **sekcji Odebrane udziały** i wyświetlając stan otrzymanych udziałów. 

## <a name="snapshot"></a>Snapshot

Migawka może być utworzona przez konsumenta danych, gdy akceptują zaproszenie do udostępniania danych. Gdy zaakceptują zaproszenie, mogą wyzwolić pełną migawkę danych udostępnionych im. Migawka jest kopią danych w momencie, w tym miejscu, że konsument danych wygenerował migawkę. 

Istnieją dwa typy migawek — pełne i przyrostowe. Pełna migawka zawiera wszystkie dane w ramach udziału danych. Migawka przyrostowa zawiera wszystkie dane, które zostały zaktualizowane/dodane od czasu wyzwolenia ostatniej migawki. 

## <a name="snapshot-settings-in-azure-data-share"></a>Ustawienia migawki w udziale danych platformy Azure
 
Dostawca danych może włączyć ustawienie migawki dla udziału danych. To ustawienie umożliwia odbiorcom danych odbieranie aktualizacji przyrostowych w miarę ich występowania. To ustawienie powinno być włączone, jeśli dostawca danych chciałby, aby ich konsumenci danych otrzymywali aktualizacje danych, które zostały udostępnione. 

Jeśli dostawca danych włączy to ustawienie, można wybrać interwał cyklu. Interwał cyklu może być co godzinę lub codziennie. 

Konsument danych ma możliwość wyrażenia zgody na ten harmonogram migawek w celu otrzymywania aktualizacji przyrostowych, które zawierają wszystkie dane, które uległy zmianie od czasu wygenerowania nowej migawki. 

## <a name="invitation"></a>Zaproszenie

Dostawca danych może zaprosić wielu adresatów do udostępniania danych. Mogą to zrobić, dodając adresatów do udziału danych. Zaproszenia można również dodawać po utworzeniu udziału danych. 

Dostawca danych może usunąć zaproszenie po jego wysłaniu, jeśli nie zostało zaakceptowane. Jeśli dostawca danych usunie zaproszenie i nie zostało ono jeszcze zaakceptowane, konsument danych nie będzie mógł go zaakceptować. 

Zaproszenia mogą być resentem do pięciu razy dziennie. 

## <a name="recipient"></a>Adresat

Odbiorcą jest osoba, która otrzymuje zaproszenie do udziału danych. Zazwyczaj dostawca danych doda adresatów do utworzonego przez nie udziału danych. Gdy odbiorca zaproszenia zaakceptuje zaproszenie, staje się konsumentem danych.  

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak rozpocząć udostępnianie danych, przejdź do [samouczka udostępniania danych.](share-your-data.md)
