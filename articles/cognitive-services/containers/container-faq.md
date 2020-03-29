---
title: Kontenery usług Cognitive Services często zadawane pytania (CZĘSTO ZADAWANE PYTANIA)
titleSuffix: Azure Cognitive Services
description: Często zadawane pytania i odpowiedzi.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: dapine
ms.openlocfilehash: 33b99d50db4384c7de818a7dd0bb8492c86bef97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73961890"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Kontenery usługi Azure Cognitive Services często zadawane pytania (często zadawane pytania)

## <a name="general-questions"></a>Pytania ogólne

**P: Co jest dostępne?**

**Odp.:** [Obsługa kontenerów w usłudze Azure Cognitive Services](../cognitive-services-container-support.md) umożliwia deweloperom korzystanie z tych samych inteligentnych interfejsów API, które są dostępne na platformie Azure, ale z [korzyściami](../cognitive-services-container-support.md#features-and-benefits) z konteneryzacji. Obsługa kontenerów jest obecnie dostępna w wersji zapoznawczej dla podzbioru usług Azure Cognitive Services, w tym części:

> [!div class="checklist"]
> * [Narzędzie do wykrywania anomalii][ad-containers]
> * [Wizja komputerowa][cv-containers]
> * [Rozpoznawanie twarzy][fa-containers]
> * [Rozpoznawanie formularzy][fr-containers]
> * [Rozumienie języka (LUIS)][lu-containers]
> * [Interfejs API usługi rozpoznawania mowy][sp-containers]
> * [Analiza tekstu][ta-containers]

**Pyt.: Czy istnieje jakaś różnica między chmurą usług Cognitive Services a kontenerami?**

**Odp.:** Kontenery usług Cognitive Services są alternatywą dla chmury usług Cognitive Services. Kontenery oferują takie same możliwości jak odpowiednie usługi w chmurze. Klienci mogą wdrażać kontenery lokalnie lub na platformie Azure. Podstawowa technologia AI, warstwy cenowe, klucze interfejsu API i podpis interfejsu API są takie same między kontenerem a odpowiednimi usługami w chmurze. Oto [funkcje i korzyści](../cognitive-services-container-support.md#features-and-benefits) związane z wybieraniem kontenerów w stosunku do ich odpowiednika usługi w chmurze.

**Pyt.: Czy kontenery będą dostępne dla wszystkich usług Cognitive Services i jakiego nowego zestawu kontenerów powinniśmy się spodziewać?**

**Odp.:** Chcielibyśmy udostępnić więcej usług Cognitive Services jako oferty kontenerów. Skontaktuj się z lokalnym menedżerem konta Microsoft, aby uzyskać aktualizacje dotyczące nowych wersji kontenerów i innych ogłoszeń usług Cognitive Services.

**Pyt.: Co będzie umowa na poziomie usług (SLA) dla kontenerów usług Cognitive Services?**

**Odp.:** Kontenery usług Cognitive Services nie mają umowy SLA.

Konfiguracje kontenerów usług Cognitive Services są kontrolowane przez klientów, więc firma Microsoft nie będzie oferować umowy SLA dla ogólnej dostępności (GA). Klienci mogą swobodnie wdrażać kontenery lokalnie, w związku z czym definiują środowiska hosta.

> [!IMPORTANT]
> Aby dowiedzieć się więcej o umowach dotyczących poziomu usług usług Cognitive Services, [odwiedź naszą stronę umowy SLA.](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/)

**Pyt.: Czy te kontenery są dostępne w suwerennych chmurach?**

**Odp.:** Nie każdy zna termin "suwerenna chmura", więc zacznijmy od definicji:

> "Suwerenna chmura" składa się z [chmur Azure Government](../../azure-government/documentation-government-welcome.md), Azure [Germany](../../germany/germany-welcome.md)i Azure [China 21Vianet.](https://docs.microsoft.com/azure/china/overview-operations)

Niestety kontenery usług Cognitive Services *nie* są natywnie obsługiwane w suwerennych chmurach. Kontenery mogą być uruchamiane w tych chmurach, ale zostaną one pobrane z chmury publicznej i muszą wysyłać dane użycia do publicznego punktu końcowego.

### <a name="versioning"></a>Przechowywanie wersji

**Pyt.: Jak są kontenery aktualizowane do najnowszej wersji?**

**Odp.:** Klienci mogą wybrać, kiedy zaktualizować kontenery, które wdrożyli. Kontenery będą oznaczone standardowymi [tagami platformy Docker,](https://docs.docker.com/engine/reference/commandline/tag/) takimi jak `latest` wskazanie najnowszej wersji. Zachęcamy klientów do ściągania najnowszej wersji kontenerów po ich wydaniu, wyewidencjonowywać [elementy webhook usługi Azure Container Registry,](../../container-registry/container-registry-webhook.md) aby uzyskać szczegółowe informacje na temat powiadamiania o aktualizacji obrazu.
 
**P: Jakie wersje będą obsługiwane?**

**Odp.:** Obsługiwana będzie bieżąca i ostatnia wersja główna kontenera. Zachęcamy jednak klientów do utrzymania aktualności w celu uzyskania najnowszych technologii.
 
**P: Jak są wersjonetowane aktualizacje?**

**Odp.:** Główne zmiany wersji wskazują, że istnieje zmiana podziału do podpisu interfejsu API. Przewidujemy, że będzie to zazwyczaj pokrywać się z głównymi zmianami wersji do odpowiedniej oferty chmury usługi Cognitive Service. Drobne zmiany wersji wskazują poprawki błędów, aktualizacje modelu lub nowe funkcje, które nie wprowadzać przełomowej zmiany w podpisie interfejsu API.

## <a name="technical-questions"></a>Pytania techniczne

**Pyt: Jak należy uruchomić kontenery usług Cognitive Services na urządzeniach IoT?**

Niezależnie od tego, czy nie masz niezawodnego połączenia z Internetem, czy chcesz zaoszczędzić na kosztach przepustowości. Lub jeśli mają wymagania o małym opóźnieniu lub mają do czynienia z poufnych danych, które muszą być analizowane na miejscu, [usługa Azure IoT Edge z kontenerów usług Cognitive Services](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/) zapewnia spójność z chmury.

**P: Jak przekazać opinie o produktach i rekomendacje funkcji?**

**Odp.:** Zachęcamy klientów do publicznego [wyrażania swoich obaw,](https://cognitive.uservoice.com/) a inni głosują w górę, którzy zrobili to samo tam, gdzie pojawiają się potencjalne problemy. Narzędzie głosowe użytkownika może być używane zarówno do opinii o produkcie, jak i rekomendacji funkcji.

**P: Z kim mam się skontaktować w celu uzyskania pomocy technicznej?**

**Odp.:** Kanały obsługi klienta są takie same jak oferty chmury usług Cognitive Services. Wszystkie kontenery usług Cognitive Services obejmują funkcje rejestrowania, które pomogą nam i klientom pomocy technicznej społeczności. Aby uzyskać dodatkową pomoc techniczną, zobacz następujące opcje.

### <a name="customer-support-plan"></a>Plan obsługi klienta

Klienci powinni zapoznać się z [planem pomocy technicznej platformy Azure,](https://azure.microsoft.com/support/plans/) aby zobaczyć, z kim należy się skontaktować w celu uzyskania pomocy technicznej.

### <a name="azure-knowledge-center"></a>Centrum wiedzy platformy Azure

Klienci mogą bezpłatnie zapoznać się z [centrum wiedzy platformy Azure,](https://azure.microsoft.com/resources/knowledge-center/) aby odpowiedzieć na pytania i problemy z pomocą techniczną.

### <a name="stack-overflow"></a>Stack Overflow

> [Stack Overflow](https://en.wikipedia.org/wiki/Stack_Overflow) to strona z pytaniami i odpowiedziami dla profesjonalnych i entuzjastów programistów.

Zapoznaj się z poniższymi tagami, aby uzyskać potencjalne pytania i odpowiedzi, które są zgodne z Twoimi potrzebami.

* [Azure Cognitive Services](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [Microsoft Cognitive](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**P: Jak działa rozliczenie?**

**Odp.:** Klienci są obciążani na podstawie zużycia, podobnie jak w chmurze usług Cognitive Services. Kontenery muszą być skonfigurowane do wysyłania danych pomiarowych na platformę Azure, a transakcje będą odpowiednio rozliczane. Zasoby używane w usługach hostowanych i lokalnych dodadzą do pojedynczego przydziału z warstwowymi cenami, licząc względem obu użycia. Aby uzyskać więcej informacji, zapoznaj się ze stroną z cenami odpowiedniej oferty.

* [Narzędzie do wykrywania anomalii][ad-containers-billing]
* [Wizja komputerowa][cv-containers-billing]
* [Rozpoznawanie twarzy][fa-containers-billing]
* [Rozpoznawanie formularzy][fr-containers-billing]
* [Rozumienie języka (LUIS)][lu-containers-billing]
* [Interfejs API usługi rozpoznawania mowy][sp-containers-billing]
* [Analiza tekstu][ta-containers-billing]

> [!IMPORTANT]
> Kontenery usług Cognitive Services nie są licencjonowane do uruchamiania bez połączenia z platformą Azure w celu pomiaru. Klienci muszą włączyć kontenery do przekazywania informacji rozliczeniowych z usługą pomiaru przez cały czas. Kontenery usług Cognitive Services nie wysyłają danych klientów do firmy Microsoft.
 
**P: Jaka jest aktualna gwarancja pomocy technicznej dla kontenerów?**

**Odp.:** Nie ma gwarancji na podglądy. Standardowa gwarancja firmy Microsoft na oprogramowanie dla przedsiębiorstw będzie obowiązywać, gdy kontenery zostaną formalnie ogłoszone jako ogólna dostępność (GA).
 
**Pyt.: Co się dzieje z kontenerami usług Cognitive Services po utracie łączności z Internetem?**

**Odp.:** Kontenery usług Cognitive Services nie są *licencjonowane* do uruchamiania bez połączenia z platformą Azure w celu pomiaru. Klienci muszą włączyć kontenery do komunikowania się z usługą pomiaru przez cały czas.

**Pyt.: Jak długo kontener może działać bez połączenia z platformą Azure?**

**Odp.:** Kontenery usług Cognitive Services nie są *licencjonowane* do uruchamiania bez połączenia z platformą Azure w celu pomiaru. Klienci muszą włączyć kontenery do komunikowania się z usługą pomiaru przez cały czas.
 
**Pyt.: Co to jest bieżący sprzęt wymagany do uruchomienia tych kontenerów?**

**Odp.:** Kontenery usług Cognitive Services to kontenery oparte na x64, które mogą uruchamiać dowolny zgodny węzeł systemu Linux, maszynę wirtualną i urządzenie brzegowe obsługujące kontenery platformy Docker systemu Linux x64. Wszystkie one wymagają procesorów CPU. Minimalne i zalecane konfiguracje dla każdej oferty kontenerów są dostępne poniżej:

* [Narzędzie do wykrywania anomalii][ad-containers-recommendations]
* [Wizja komputerowa][cv-containers-recommendations]
* [Rozpoznawanie twarzy][fa-containers-recommendations]
* [Rozpoznawanie formularzy][fr-containers-recommendations]
* [Rozumienie języka (LUIS)][lu-containers-recommendations]
* [Interfejs API usługi rozpoznawania mowy][sp-containers-recommendations]
* [Analiza tekstu][ta-containers-recommendations]
 
**Pyt.: Czy te kontenery są obecnie obsługiwane w systemie Windows?**

**Odp.:** Kontenery usług Cognitive Services są kontenerami systemu Linux, jednak istnieje pewna obsługa kontenerów systemu Linux w systemie Windows. Aby uzyskać więcej informacji na temat kontenerów systemu Linux w systemie Windows, zobacz [dokumentację platformy Docker](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/).
 
**Pyt.: Jak odnajdyć kontenery?**

**Odp.:** Kontenery usług Cognitive Services są dostępne w różnych lokalizacjach, takich jak portal Azure, Centrum Platformy Docker i rejestry kontenerów platformy Azure. Najnowsze lokalizacje kontenerów można znaleźć w [repozytoriach kontenerów i obrazach](../cognitive-services-container-support.md#container-repositories-and-images).

**P: Jak kontenery usług Cognitive Services wypada w porównaniu z ofertami AWS i Google?**

**Odp.:** Firma Microsoft jest pierwszym dostawcą chmury, który przenosi swoje wstępnie przeszkolone modele AI w kontenerach z prostymi rozliczeniami na transakcję, tak jakby klienci korzystali z usługi w chmurze. Firma Microsoft uważa, że chmura hybrydowa daje klientom większy wybór.

**Pyt.: Jakie certyfikaty zgodności mają kontenery?**

**Odp.:** Kontenery usług cognitive services nie mają żadnych certyfikatów zgodności

**Pyt.: W jakich regionach są dostępne kontenery usług Cognitive Services?**

**Odp.:** Kontenery można uruchomić w dowolnym miejscu w dowolnym regionie, jednak potrzebują klucza i wywołać z powrotem do platformy Azure do pomiaru. Wszystkie obsługiwane regiony usługi w chmurze są obsługiwane dla wywołania pomiaru kontenerów.

[!INCLUDE [Containers next steps](includes/containers-next-steps.md)]

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-containers]: ../speech-service/speech-container-howto.md
[ta-containers]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md

[ad-containers-billing]: ../anomaly-Detector/anomaly-detector-container-howto.md#billing
[cv-containers-billing]: ../computer-vision/computer-vision-how-to-install-containers.md#billing
[fa-containers-billing]: ../face/face-how-to-install-containers.md#billing
[fr-containers-billing]: ../form-recognizer/form-recognizer-container-howto.md#billing
[lu-containers-billing]: ../luis/luis-container-howto.md#billing
[sp-containers-billing]: ../speech-service/speech-container-howto.md#billing
[ta-containers-billing]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#billing

[ad-containers-recommendations]: ../anomaly-Detector/anomaly-detector-container-howto.md#container-requirements-and-recommendations
[cv-containers-recommendations]: ../computer-vision/computer-vision-how-to-install-containers.md#container-requirements-and-recommendations
[fa-containers-recommendations]: ../face/face-how-to-install-containers.md#container-requirements-and-recommendations
[fr-containers-recommendations]: ../form-recognizer/form-recognizer-container-howto.md#container-requirements-and-recommendations
[lu-containers-recommendations]: ../luis/luis-container-howto.md#container-requirements-and-recommendations
[sp-containers-recommendations]: ../speech-service/speech-container-howto.md#container-requirements-and-recommendations
[ta-containers-recommendations]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#container-requirements-and-recommendations
