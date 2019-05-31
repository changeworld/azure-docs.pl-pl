---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 05/16/2018
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: 34f2ab8f7ccafb8b30e298cd71e09171ad8c87cb
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238261"
---
Następujące limity dotyczą zasobów usługi Azure Log Analytics na subskrypcję.

| Resource | Limit domyślny | Komentarze
| --- | --- | --- |
| Liczba wolnych obszarów roboczych na subskrypcję | 10 | Nie można zwiększyć ten limit. |
| Liczba płatnych obszarów roboczych na subskrypcję | ND | Ograniczeniem liczby zasobów w grupie zasobów i liczba grup zasobów na subskrypcję. | 

>[!NOTE]
>Od 2 kwietnia 2018 r. automatycznie używać nowych obszarów roboczych w nowej subskrypcji *na GB* plan cenowy. W przypadku istniejących subskrypcji utworzony przed 2 kwietnia lub subskrypcji powiązanej z istniejącą rejestracją umowy Enterprise Agreement można nadal z trzech warstw cenowych dla nowych obszarów roboczych. 
>

Następujące limity dotyczą każdego obszaru roboczego usługi Log Analytics.

|  | Wolne | Standardowa (Standard) | Premium | Autonomiczna | OMS | Na GB |
| --- | --- | --- | --- | --- | --- |--- |
| Ilość danych zebranych na dzień |500 MB<sup>1</sup> |Brak |Brak | Brak | Brak | Brak
| Okres przechowywania danych |7 dni |1 miesiąc |12 miesięcy | 1 miesiąc<sup>2</sup> | 1 miesiąc<sup>2</sup>| 1 miesiąc<sup>2</sup>|

<sup>1</sup>gdy klienci osiągną ich 500 MB limit dziennego transferu danych, analiza danych zatrzymuje się i zostanie wznowiona na początku następnego dnia. Dzień jest oparty na czasie UTC.

<sup>2</sup>okres przechowywania danych dla autonomicznej, OMS i planów cen na GB, można zwiększyć do 730 dni.

| Category | Limits | Komentarze
| --- | --- | --- |
| Interfejs API modułu zbierającego dane | Maksymalny rozmiar pojedynczego wpisu to 30 MB.<br>Maksymalny rozmiar wartości pól to 32 KB. | Dziel większe woluminy na wiele wpisów.<br>Pola dłuższe niż 32 KB są obcinane. |
| Interfejs API wyszukiwania | 5000 rekordów zwracanych dla danych niezagregowanych.<br>500 000 rekordów dla danych zagregowanych. | Zagregowane dane to wyszukiwanie obejmujące `summarize` polecenia.
 
