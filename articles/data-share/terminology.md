---
title: Terminologia (wersja zapoznawcza) udziału danych platformy Azure
description: Terminologia (wersja zapoznawcza) udziału danych platformy Azure
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 4e7db84666b9d3786c3fc25e3653d24d0b95f2e4
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789230"
---
# <a name="azure-data-share-preview-concepts"></a>Pojęcia związane z danymi platformy Azure udziału (wersja zapoznawcza) 

Azure udostępniania danych w wersji zapoznawczej wprowadzono pewne nową terminologią związanych z udostępnianiem danych. W tym artykule wyjaśniono, że kilka często używanych terminów, które może zostać wyświetlony używanych w całej usłudze. 

## <a name="data-provider"></a>Dostawca danych

Dostawca danych jest organizacja, która udostępnia dane z niego korzystają. Zazwyczaj dostawcy danych może być właścicielem lub oglądających danych. Dostawcy danych mają być udostępnianie danych różnych typów. Przykłady danych, które warto udostępnić dostawcę danych: dane pierwotne, takie jak punkt sprzedaży lub dane szeregów czasowych. Dostawca danych może być również udostępniać dane wstępnie przetworzone, wyselekcjonowanych, która już zawiera analiz i szczegółowych informacji. 

## <a name="data-consumer"></a>Odbiorca danych 

Odbiorca danych jest organizacja, która otrzymuje dane od dostawcy danych. Odbiorca danych może chcą Dołącz do udostępnionych danych z własnymi danymi do uzyskiwania szczegółowych informacji. W niektórych przypadkach konsument danych może odbierać dane, które zostały już przetworzone. 

## <a name="data-share"></a>Udostępnij dane

Udostępnij dane to grupa zestawów danych, które są udostępniane jako pojedynczy element. Zestawy danych można się z wielu źródeł danych platformy Azure, które są obsługiwane przez udziału danych platformy Azure. Obecnie usługa udziału danych platformy Azure obsługuje usługi Azure Blob Storage i Azure Data Lake Store. 

## <a name="share-subscription"></a>Udostępnianie subskrypcji 

Udostępnianie subskrypcji jest tworzone, gdy konsument danych akceptuje zaproszenie udostępniania danych, od dostawcy danych. Dostawcy danych można wyświetlić udział aktywne subskrypcje, przechodząc do **wysyłane udziałów** swoje dane platformy Azure umożliwia udostępnianie kont, a następnie wybierając **udostępnianie subskrypcje**.

Odbiorca danych można sprawdzić, jeśli mają subskrypcję aktywnego udziału, przechodząc do **Odebrano udziałów** i wyświetlanie stanu ich odebranych udziałów. 

## <a name="snapshot"></a>Snapshot

Migawki mogą być tworzone przez użytkownika danych, gdy zaakceptują zaproszenie udostępniania danych. Gdy zaakceptują zaproszenie, mogą wyzwalać pełna migawka danych mu udostępnione. Migawka jest kopii danych w momencie wygenerowania migawki odbiorcy danych. 

Istnieją dwa typy migawek — pełne i przyrostowe. Pełna migawka zawiera wszystkie dane w obrębie udziału danych. Jest przyrostową migawką zawiera wszystkie dane, które zostały zaktualizowane/dodał ponieważ ostatnia migawka została wyzwolona. 

## <a name="snapshot-settings-in-azure-data-share"></a>Ustawienia migawki udziału danych platformy Azure
 
Dostawca danych można włączyć ustawienia migawki dla udziału danych. To ustawienie włącza konsumenci danych otrzymywać aktualizacje przyrostowe w miarę ich występowania. To ustawienie powinno być włączone, jeżeli ich konsumenci danych, aby otrzymywać aktualizacje danych, który został udostępniony przez dostawcę danych. 

Jeśli dostawca danych włączenie tego ustawienia, można wybrać interwał cyklu. Interwał cyklu można godzinowo lub dziennie. 

Odbiorca danych ma możliwość wyrazić zgodę na ten harmonogram migawki mają otrzymywać aktualizacje przyrostowe, w tym wszelkie dane, które zmieniły się od czasu najpierw generowane w nową migawkę. 

## <a name="invitation"></a>Zaproszenie

Dostawca danych mogą zapraszać wielu adresatów, do ich udziału danych. Mogą to zrobić, dodając odbiorców do udziału danych. Po utworzeniu udziału danych, mogą być również dodawane zaproszeń. 

Dostawca danych można usunąć zaproszenia, po został wysłany. Należy pamiętać, że dostawca danych usuwa zaproszenie po zostało zaakceptowane, odbiorcy danych można nadal mieć subskrypcję aktywnego udziału. Jeśli dostawca danych usuwa zaproszenia, nie ma jeszcze zaakceptowane odbiorcy danych nie będzie go zaakceptować. 

## <a name="recipient"></a>Odbiorcy

Odbiorcy to osoba, która otrzymuje zaproszenie do udostępniania danych. Zazwyczaj dostawca danych będzie dodawać odbiorców do udziału danych, które tworzą. Gdy adresat zaproszenia zaakceptuje zaproszenia, stają się one konsumenta danych.  

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak uruchomić, udostępnianie danych, w dalszym ciągu [udostępnianie danych](share-your-data.md) samouczka.

