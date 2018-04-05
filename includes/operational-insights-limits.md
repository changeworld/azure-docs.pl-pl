---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 03/29/2018
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: 29256b3bcfedb7fe5045ff4c6c3842eb25e00a28
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
Następujące limity dotyczą zasobów usługi Log Analytics na subskrypcję:

| Zasób | Limit domyślny | Komentarze
| --- | --- | --- |
| Liczba wolnych obszarów roboczych na subskrypcję | 10 | Tego limitu nie można zwiększyć. |
| Liczba płatnych obszarów roboczych na subskrypcję | ND | Ograniczeniem jest liczba zasobów w grupie zasobów i liczba grup zasobów na subskrypcję | 

>[!NOTE]
>Począwszy od 2 kwietnia 2018 nowych obszarów roboczych w nowej subskrypcji będą automatycznie używać *GB na* cenową planu.  Istniejące subskrypcje utworzone przed 2 kwietnia lub subskrypcji, która została powiązana z istniejących rejestracji EA można nadal wybór między trzy warstwy cenowej dla nowych obszarów roboczych. 
>

Następujące limity dotyczą każdego obszaru roboczego usługi Log Analytics:

|  | Bezpłatna | Standardowa (Standard) | Premium | Autonomiczna | OMS | Na GB |
| --- | --- | --- | --- | --- | --- |--- |
| Ilość danych zebranych na dzień |500 MB<sup>1</sup> |Brak |None | Brak | Brak | Brak
| Okres przechowywania danych |7 dni |1 miesiąc |12 miesięcy | 1 miesiąc<sup>2</sup> | 1 miesiąc <sup>2</sup>| 1 miesiąc <sup>2</sup>|

<sup>1</sup> Gdy klienci osiągną limit dziennego transferu danych wynoszący 500 MB, analiza danych zostanie zatrzymana, a następnie wznowiona na początku następnego dnia. Dzień jest oparty na czasie UTC.

<sup>2</sup> okres przechowywania danych dla autonomicznej, OMS i planów cenowych GB na może wzrosnąć do 730 dni.

| Kategoria | Limity | Komentarze
| --- | --- | --- |
| Interfejs API modułu zbierającego dane | Maksymalny rozmiar pojedynczego wpisu to 30 MB<br>Maksymalny rozmiar wartości pól to 32 KB | Dziel większe woluminy na wiele wpisów<br>Pola dłuższe niż 32 KB są obcinane. |
| Interfejs API wyszukiwania | 5000 rekordów zwracanych dla danych niezagregowanych<br>500 000 rekordów dla danych zagregowanych | Zagregowane dane to wyszukiwanie obejmujące polecenie `measure`
 
