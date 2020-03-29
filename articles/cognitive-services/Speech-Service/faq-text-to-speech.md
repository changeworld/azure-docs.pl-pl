---
title: Często zadawane pytania dotyczące zamiany tekstu na mowę
titleSuffix: Azure Cognitive Services
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące usługi Zamiana tekstu na mowę.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 19b8be83a3678164197ec0650b07091e941a04d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110494"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Często zadawane pytania dotyczące zamiany tekstu na mowę

Jeśli nie możesz znaleźć odpowiedzi na swoje pytania w tym faq, zapoznaj się z [innymi opcjami pomocy technicznej](support.md).

## <a name="general"></a>Ogólne

**P: Jaka jest różnica między standardowym modelem głosu a niestandardowym modelem głosu?**

**A:** Standardowy model głosu (nazywany również _czcionką głosową)_ został przeszkolony przy użyciu danych należących do firmy Microsoft i jest już wdrożony w chmurze. Można użyć niestandardowego modelu głosu, aby dostosować średni model i przenieść barwę i ekspresję stylu głosu głośnika lub wyszkolić pełny, nowy model na podstawie danych szkoleniowych przygotowanych przez użytkownika. Dziś coraz więcej klientów chce jedynego w swoim rodzaju, markowego głosu dla swoich botów. Niestandardowa platforma do budowania głosu jest właściwym wyborem dla tej opcji.

**P: Od czego zacząć, jeśli chcę używać standardowego modelu głosu?**

**A:** Ponad 80 standardowych modeli głosowych w ponad 45 językach jest dostępnych za pośrednictwem żądań HTTP. Najpierw uzyskaj [klucz subskrypcji](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started). Aby nawiązać połączenia REST do wstępnie wypalonych modeli głosowych, zobacz [interfejs API REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

**Pyt.: Jeśli chcę używać dostosowanego modelu głosu, czy interfejs API jest taki sam jak ten, który jest używany dla standardowych głosów?**

**A:** Po utworzeniu i wdrożeniu niestandardowego modelu głosu otrzymasz unikatowy punkt końcowy dla modelu. Aby używać głosu do mówienia w aplikacjach, należy określić punkt końcowy w żądaniach HTTP. Ta sama funkcja, która jest dostępna w interfejsie API REST dla usługi zamiany tekstu na mowę jest dostępna dla niestandardowego punktu końcowego. Dowiedz się, jak [utworzyć niestandardowy punkt końcowy i używać go.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-voice-endpoint)

**Pyt.: Czy muszę przygotować dane szkoleniowe, aby samodzielnie tworzyć niestandardowe modele głosowe?**

**Odp**.: Tak, musisz samodzielnie przygotować dane szkoleniowe dla niestandardowego modelu głosu.

Kolekcja danych mowy jest wymagana do utworzenia dostosowanego modelu głosu. Ta kolekcja składa się z zestawu plików audio nagrań mowy i pliku tekstowego transkrypcji każdego pliku audio. Wynik cyfrowego głosu zależy w dużej mierze od jakości danych treningowych. Aby uzyskać dobry głos zamiany tekstu na mowę, ważne jest, aby nagrania były wykonane w cichym pomieszczeniu z wysokiej jakości mikrofonem stojącym. Spójna głośność, szybkość mówienia i ton mówienia, a nawet spójność w ekspresyjnych manierach mowy są niezbędne do budowania świetnego głosu cyfrowego. Gorąco polecamy nagrywanie głosów w studiu nagraniowym.

Obecnie nie zapewniamy wsparcia nagrywania online ani nie mamy żadnych rekomendacji studia nagraniowego. Aby uzyskać wymagania dotyczące formatu, [zobacz, jak przygotować nagrania i transkrypcje](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice-create-voice).

**Pyt.: Jakich skryptów należy używać do nagrywania danych mowy do niestandardowego szkolenia głosowego?**

**Odp**.: Nie ograniczamy skryptów do nagrywania głosu. Do nagrywania mowy można używać własnych skryptów. Po prostu upewnij się, że masz wystarczający zasięg fonetyczny w danych mowy. Aby wyszkolić niestandardowy głos, można rozpocząć od niewielkiej ilości danych mowy, które mogą być 50 różnych zdań (około 3-5 minut mowy). Im więcej danych podasz, tym bardziej naturalny będzie Twój głos. Możesz rozpocząć szkolenie pełnej czcionki głosowej, gdy podasz nagrania ponad 2000 zdań (około 3-4 godzin mowy). Aby uzyskać wysokiej jakości, pełny głos, musisz przygotować nagrania ponad 6000 zdań (około 8-10 godzin mowy).

Zapewniamy dodatkowe usługi, które pomogą Ci przygotować skrypty do nagrywania. Aby uzyskać informacje na ten cel, skontaktuj się z [działem obsługi klienta Custom Voice.](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation)

**Pyt.: Co zrobić, jeśli potrzebuję wyższej współbieżności niż wartość domyślna lub co jest oferowane w portalu?**

**A:** Model można skalować w górę w przyrostach 20 równoczesnych żądań. Skontaktuj się z [działem obsługi klienta Custom Voice,](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) aby uzyskać informacje na temat wyższego skalowania.

**P: Czy mogę pobrać mój model i uruchomić go lokalnie?**

**A:** Modeli nie można pobrać i wykonać lokalnie.

**P: Czy moje żądania są ograniczone?**

**A:** Interfejs API REST ogranicza żądania do 25 na 5 sekund. Szczegóły można znaleźć na naszych stronach [tekst do mowy](text-to-speech.md).

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów](troubleshooting.md)
- [Informacje o wersji](releasenotes.md)
