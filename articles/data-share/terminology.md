---
title: Terminologia dotycząca udziału danych platformy Azure
description: Terminologia dotycząca udziału danych platformy Azure
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: b78a1b250f46c2ddd2b36e19590c4a94ca3ab3fb
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844704"
---
# <a name="azure-data-share-preview-concepts"></a>Pojęcia dotyczące usługi Azure Data Share w wersji zapoznawczej 

W wersji zapoznawczej usługi Azure Data Share wprowadzono nową terminologię związaną z udostępnianiem danych. W tym artykule opisano niektóre często używane terminy, które mogą być używane w całej usłudze. 

## <a name="data-provider"></a>Dostawca danych

Dostawca danych jest organizacją, która udostępnia dane klientom. Zazwyczaj dostawca danych może być właścicielem lub Curator danych. Dostawcy danych chcą udostępniać dane różnych typów. Niektóre przykłady danych, które mogą być udostępniane dostawcy danych, obejmują dane pierwotne, takie jak punkt sprzedaży lub dane szeregów czasowych. Dostawca danych może również chcieć udostępnić wstępnie przetworzone, nadzorowane dane, które już zawierają analizę i szczegółowe informacje. 

## <a name="data-consumer"></a>Użytkownik danych 

Odbiorca danych to organizacja, która otrzymuje dane od dostawcy danych. Odbiorca danych może chcieć przyłączyć dane udostępnione do swoich danych w celu uzyskania szczegółowych informacji. W niektórych przypadkach odbiorca danych może odbierać dane, które zostały już przetworzone. 

## <a name="data-share"></a>Udział danych

Udział danych to grupa zestawów DataSet, które są współużytkowane jako pojedyncza jednostka. Zestawy danych mogą pochodzić z wielu źródeł danych platformy Azure, które są obsługiwane przez usługę Azure Data Share. Obecnie udział danych platformy Azure obsługuje platformę Azure Blob Storage i Azure Data Lake Store. 

## <a name="share-subscription"></a>Udostępnianie subskrypcji 

Subskrypcja udziału jest tworzona, gdy odbiorca danych akceptuje zaproszenie do udziału danych od dostawcy danych. Dostawcy danych mogą przeglądać aktywne subskrypcje udziałów, przechodząc do **wysłanych udziałów** na koncie usługi Azure Data Share i wybierając pozycję **Udostępnij subskrypcje**.

Odbiorca danych może sprawdzić, czy ma aktywną subskrypcję udziałów, przechodząc do **odebranych udziałów** i wyświetlając stan odebranych udziałów. 

## <a name="snapshot"></a>Snapshot

Migawkę można utworzyć przez odbiorcę danych po zaakceptowaniu zaproszenia udziału danych. Po zaakceptowaniu zaproszenia mogą wyzwalać pełną migawkę danych, które są do nich udostępniane. Migawka jest kopią danych w punkcie w czasie, w którym odbiorca danych wygenerował migawkę. 

Istnieją dwa typy migawek — pełne i przyrostowe. Pełna migawka zawiera wszystkie dane w udziale danych. Migawka przyrostowa zawiera wszystkie dane, które zostały zaktualizowane/dodane od momentu wyzwolenia ostatniej migawki. 

## <a name="snapshot-settings-in-azure-data-share"></a>Ustawienia migawki w udziale danych platformy Azure
 
Dostawca danych może włączyć ustawienie migawki dla udziału danych. To ustawienie umożliwia konsumentom danych otrzymywanie aktualizacji przyrostowych w miarę ich występowania. To ustawienie powinno być włączone, jeśli dostawca danych ma otrzymywać aktualizacje dla danych, które zostały udostępnione. 

Jeśli dostawca danych włączy to ustawienie, można wybrać interwał cyklu. Interwał cyklu może być godzinny lub codzienny. 

Odbiorca danych ma możliwość wyboru tego harmonogramu migawki w celu otrzymywania aktualizacji przyrostowych, które obejmują wszystkie dane, które uległy zmianie od czasu pierwszej generacji nowej migawki. 

## <a name="invitation"></a>Zaproszenie

Dostawca danych może zaprosić wielu odbiorców do swojego udziału danych. Można to zrobić, dodając odbiorców do udziału danych. Zaproszenia można także dodawać po utworzeniu udziału danych. 

Dostawca danych może usunąć zaproszenie po jego wysłaniu, jeśli nie zostało ono zaakceptowane. Jeśli dostawca danych usunie zaproszenie i nie zostało jeszcze zaakceptowane, konsument danych nie będzie mógł go zaakceptować. 

Zaproszenia można wysyłać do 5 razy dziennie. 

## <a name="recipient"></a>Adresat

Odbiorca to osoba, która otrzymuje zaproszenie do udziału danych. Zazwyczaj dostawca danych doda odbiorców do udziału danych, który tworzy. Gdy odbiorca zaproszenia zaakceptuje zaproszenie, staje się konsumentem danych.  

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zacząć udostępniać dane, przejdź do samouczka [udostępnianie danych](share-your-data.md) .

