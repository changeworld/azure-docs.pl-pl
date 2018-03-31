---
title: Dane wyjściowe usługi Stream Analytics Data Lake Store | Dokumentacja firmy Microsoft
description: Konfiguracja uwierzytelniania i autoryzacji usługi Azure Data Lake Store w zadaniu Stream Analytics
keywords: ''
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: ea5baafa-0054-4c70-973a-6a3a8c6eaffc
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: 802193b9f15cbc35bec32191ccc63df3329b96f9
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="stream-analytics-data-lake-store-output"></a>Dane wyjściowe Stream Analytics Data Lake Store
Zadania usługi analiza strumienia obsługuje kilka metod danych wyjściowych, co jest [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/). Azure Data Lake Store to repozytorium w hiperskali obsługujące całe przedsiębiorstwo na potrzeby obciążeń analizy dużych ilości danych (big data). Data Lake Store można przechowywać dane dowolnego rozmiar, typ i wprowadzanie szybkość analiz operacyjnych i poznawczych.

## <a name="authorize-a-data-lake-store-account"></a>Autoryzuj konto usługi Data Lake Store
1. Po wybraniu jako dane wyjściowe w portalu Azure Data Lake Store pojawi się monit dopuszczają użycie usługi istniejących Data Lake Store lub zażądać dostępu do usługi Data Lake Store.
   
   ![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-authorization.png)  
   
2. Jeśli masz już dostępu do usługi Data Lake Store, kliknij przycisk "Autoryzuj" i krótki czas strony będzie wyskakujące wskazujący "Przekierowanie do autoryzacji". Strona zostanie automatycznie zamknięte i zostanie wyświetlone na stronie, która pozwala na skonfigurowanie danych wyjściowych usługi Data Lake Store.

Jeśli użytkownik nie jest zarejestrowany w usłudze Data Lake Store, można wykonać "Uaktywnij teraz" łącze do zainicjowania żądania lub wykonaj [uzyskać uruchomiono instrukcje](../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="configure-the-data-lake-store-output-properties"></a>Skonfiguruj właściwości danych wyjściowych usługi Data Lake Store
Po utworzeniu konta usługi Data Lake Store uwierzytelnionego, można skonfigurować właściwości dla danych wyjściowych usługi Data Lake Store. W poniższej tabeli jest listą nazw właściwości i ich opis, aby skonfigurować dane wyjściowe usługi Data Lake Store.

<table>
<tbody>
<tr>
<td><B>NAZWA WŁAŚCIWOŚCI</B></td>
<td><B>OPIS ELEMENTU</B></td>
</tr>
<tr>
<td>Alias wyjściowy</td>
<td>Jest to przyjazna nazwa używana w zapytaniach do kierowania wyników zapytania do tej usługi Data Lake Store.</td>
</tr>
<tr>
<td>Konto usługi Data Lake Store</td>
<td>Nazwa konta magazynu, w którym wysyłania danych wyjściowych. Zostanie wyświetlona lista kont usługi Data Lake Store, do których zalogowany użytkownik ma dostęp do.</td>
</tr>
<tr>
<td>Wzorzec prefiksu ścieżki [<I>opcjonalne</I>]</td>
<td>Ścieżka do pliku używany do zapisywania plików w ramach określonego konta magazynu usługi Data Lake. <BR>{date} {time}<BR>Przykład 1: folder1/dzienniki / {date} / {time}<BR>Przykład 2: folder1/dzienniki / {date}</td>
</tr>
<tr>
<td>Data w formacie [<I>opcjonalne</I>]</td>
<td>Jeśli token daty jest używany w ścieżce prefiks, można wybrać format daty, w którym pliki są organizowane. Przykład: RRRR/MM/dd.</td>
</tr>
<tr>
<td>Format czasu [<I>opcjonalne</I>]</td>
<td>Jeśli czas nie jest używany w ścieżce prefiks, określ format czasu, w którym pliki są organizowane. Obecnie jedyna obsługiwana wartość to HH.</td>
</tr>
<tr>
<td>Format serializacji zdarzeń</td>
<td>Format serializacji w danych wyjściowych. JSON, CSV i Avro są obsługiwane.</td>
</tr>
<tr>
<td>Encoding</td>
<td>Jeśli formatu CSV lub JSON, należy określić kodowania. UTF-8 to jedyny obsługiwany obecnie format kodowania.</td>
</tr>
<tr>
<td>Ogranicznik</td>
<td>Dotyczy tylko serializacji woluminów CSV. Analiza strumienia obsługuje różne ograniczniki dla serializacji danych CSV. Obsługiwane wartości to przecinek, średnik, miejsca, karta i pionowy pasek.</td>
</tr>
<tr>
<td>Format</td>
<td>Dotyczy tylko serializacji JSON. Rozdzielone Określa, czy dane wyjściowe będą formatowane przez poszczególne obiekty JSON rozdzielone znakiem nowego wiersza. Tablica Określa, czy dane wyjściowe będą formatowane jako tablica obiektów JSON.</td>
</tr>
</tbody>
</table>

## <a name="renew-data-lake-store-authorization"></a>Odnów autoryzacji usługi Data Lake Store
Obecnie jest to ograniczenie gdzie token uwierzytelniania musi być ręcznie odświeżyć co 90 dni, dla wszystkich zadań usługi Data Lake Store w danych wyjściowych. Należy również ponownego uwierzytelnienia konta usługi Data Lake Store, jeśli hasło zostało zmienione od czasu utworzenia lub ostatniej uwierzytelniony zadania. Objawem tego problemu jest Brak danych wyjściowych zadania i wystąpił błąd w dziennikach operacji wskazujący potrzebę ponownej autoryzacji.

Aby rozwiązać ten problem, Zatrzymaj uruchomione zadania i przejdź do usługi Data Lake Store dane wyjściowe. Kliknij łącze "Odnowić autoryzacji", a przez krótki czas strony będzie wyskakujące wskazujący "Przekierowanie do autoryzacji...". Strona zostanie automatycznie zamknięte i w razie powodzenia wskaże "Autoryzacji został pomyślnie odnowiony". Można następnie trzeba w dolnej części strony, kliknij przycisk "Zapisz" i przejść przez ponowne uruchomienie zadania od ostatniego czasu zatrzymana w celu uniknięcia utraty danych.

![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-renew-authorization.png)

