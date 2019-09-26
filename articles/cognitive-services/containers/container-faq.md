---
title: Kontenery Cognitive Services często zadawane pytania
titleSuffix: Azure Cognitive Services
description: Często zadawane pytania i odpowiedzi.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: dapine
ms.openlocfilehash: 6e218f33bdc33708cef0c94eb85298abf2b8927c
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316628"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Kontenery Cognitive Services platformy Azure — często zadawane pytania

## <a name="general-questions"></a>Pytania ogólne

**Pyt.: Co jest dostępne?**

**Odp.:** [Obsługa kontenerów w usłudze azure Cognitive Services](../cognitive-services-container-support.md) pozwala deweloperom korzystać z tych samych inteligentnych interfejsów API, które są dostępne na platformie Azure, ale z [korzyściami](../cognitive-services-container-support.md#features-and-benefits) z kontenerach. Obsługa kontenerów jest obecnie dostępna w wersji zapoznawczej dla podzestawu Cognitive Services platformy Azure, w tym części:

> [!div class="checklist"]
> * [Wykrywacz anomalii][ad-containers]
> * [Przetwarzanie obrazów][cv-containers]
> * [Twarzy][fa-containers]
> * [Aparat rozpoznawania formularzy][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [Interfejs API usługi rozpoznawania mowy][sp-containers]
> * [Analiza tekstu][ta-containers]
<!-- > * [Translator Text][tt-containers] -->

**Pyt.: Czy istnieje różnica między chmurą Cognitive Services i kontenerami?**

**Odp.:** Kontenery Cognitive Services są alternatywą dla chmury Cognitive Services. Kontenery oferują te same możliwości co odpowiednie usługi w chmurze. Klienci mogą wdrażać kontenery lokalnie lub na platformie Azure. Podstawowa technologia AI, warstwy cenowe, klucze interfejsu API i podpis interfejsu API są takie same, jak w przypadku kontenera i odpowiednich usług w chmurze. Poniżej przedstawiono [funkcje i korzyści wynikające](../cognitive-services-container-support.md#features-and-benefits) z wyboru kontenerów w ramach usługi w chmurze.

**Pyt.: Czy kontenery będą dostępne dla wszystkich Cognitive Services i jakie są następne zestawy kontenerów, których oczekujemy?**

**Odp.:** Chcemy, aby więcej Cognitive Services dostępnych jako oferty kontenerów. Skontaktuj się z lokalnym menedżerem konto Microsoft, aby uzyskać aktualizacje dotyczące nowych wydań kontenerów i innych anonsów Cognitive Services.

**Pyt.: Jakie będzie umowa dotycząca poziomu usług (SLA) dla kontenerów Cognitive Services?**

**Odp.:** Kontenery Cognitive Services nie mają umowy SLA.

Cognitive Services konfiguracjami kontenerów zasobów są kontrolowane przez klientów, dlatego firma Microsoft nie oferuje umowy SLA na potrzeby ogólnej dostępności. Klienci mogą bezpłatnie wdrażać kontenery w środowisku lokalnym, a tym samym definiować środowiska hosta.

> [!IMPORTANT]
> Aby dowiedzieć się więcej na temat Cognitive Services umów dotyczących poziomu usług, [odwiedź stronę umów SLA](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/).

**Pyt.: Czy te kontenery są dostępne w ramach suwerennych chmur?**

**Odp.:** Nie wszyscy znają termin "chmura suwerenna", więc zacznijmy od definicji:

> "Suwerenna chmura" składa się z [Azure Government](../../azure-government/documentation-government-welcome.md), [platformy Azure (Niemcy](../../germany/germany-welcome.md)) i [platformy Azure z Chin](https://docs.microsoft.com/azure/china/overview-operations) .

Niestety kontenery Cognitive Services *nie* są natywnie obsługiwane w przypadku suwerennych chmur. Kontenery można uruchamiać w tych chmurach, ale zostaną one pobrane z chmury publicznej i muszą wysyłać dane użycia do publicznego punktu końcowego.

### <a name="versioning"></a>Obsługa wersji

**Pyt.: W jaki sposób kontenery są aktualizowane do najnowszej wersji?**

**Odp.:** Klienci mogą określić, kiedy mają być aktualizowane wdrożone kontenery. Kontenery zostaną oznaczone `latest` standardowymi [tagami platformy Docker](https://docs.docker.com/engine/reference/commandline/tag/) , na przykład w celu wskazania najnowszej wersji. Zachęcamy klientów do ściągania najnowszej wersji kontenerów po ich wydaniu, wyewidencjonowywania [Azure Container Registry elementów webhook](../../container-registry/container-registry-webhook.md) w celu uzyskania szczegółowych informacji o tym, jak uzyskać powiadamianie o zaktualizowaniu obrazu.
 
**Pyt.: Jakie wersje będą obsługiwane?**

**Odp.:** Bieżąca i Ostatnia wersja główna kontenera będą obsługiwane. Zachęcamy jednak, aby klienci mogli zachować aktualność w celu uzyskania najnowszej technologii.
 
**Pyt.: Jak są aktualizowane aktualizacje?**

**Odp.:** Główne zmiany wersji wskazują na nieprzerwaną zmianę sygnatury interfejsu API. Przewidujemy, że zwykle będzie to zbieżne ze zmianami wersji głównych do odpowiedniej oferty w chmurze usługi poznawczej. Zmiany wersji pomocniczej wskazują poprawki błędów, aktualizacje modelu lub nowe funkcje, które nie wprowadzają istotnej zmiany w sygnaturze interfejsu API.

## <a name="technical-questions"></a>Pytania techniczne

**Pyt.: Jak uruchamiać kontenery Cognitive Services na urządzeniach IoT?**

Bez względu na to, czy nie masz niezawodnego połączenia z Internetem, czy chcesz zaoszczędzić koszt przepustowości. Lub jeśli mają wymagania dotyczące małych opóźnień lub zawierają dane poufne, które należy analizować w witrynie, [Azure IoT Edge z kontenerami Cognitive Services](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/) zapewnia spójność z chmurą.

**Pyt.: Jak mogę udostępnić opinie o produkcie i zalecenia dotyczące funkcji?**

**Odp.:** Zachęcamy klientów do [zagłosowania](https://cognitive.uservoice.com/) publicznie i popełnienia innych osób, które zrobiły to samo, gdzie potencjalne problemy nakładają się na siebie. Narzędzia głosowego użytkownika można używać zarówno w celu uzyskania opinii o produkcie, jak i zaleceń dotyczących funkcji.

**Pyt.: Z kim mam się kontaktować, aby uzyskać pomoc techniczną?**

**Odp.:** Kanały obsługi klienta są takie same, jak oferta Cognitive Services w chmurze. Wszystkie kontenery Cognitive Services obejmują funkcje rejestrowania, które ułatwią nam i klientom pomoc techniczną. Aby uzyskać dodatkową pomoc techniczną, zobacz poniższe opcje.

### <a name="customer-support-plan"></a>Plan pomocy technicznej klienta

Klienci powinni zapoznać się z [planem pomocy technicznej platformy Azure](https://azure.microsoft.com/support/plans/) , aby dowiedzieć się, komu skontaktować się z pomocą techniczną.

### <a name="azure-knowledge-center"></a>Centrum wiedzy Azure

Klient jest bezpłatny, aby poznać [usługę Azure Knowledge Center](https://azure.microsoft.com/resources/knowledge-center/) w celu uzyskania odpowiedzi na pytania i pomoc techniczną.

### <a name="stack-overflow"></a>Stack Overflow

> [Stack Overflow](https://en.wikipedia.org/wiki/Stack_Overflow) jest witryną pytań i odpowiedzi dla programistów profesjonalnych i entuzjastą.

Zapoznaj się z następującymi tagami, aby poznać potencjalne pytania i odpowiedzi, które są dostosowane do Twoich potrzeb.

* [Azure Cognitive Services](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [Poznawcze firmy Microsoft](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**Pyt.: Jak działają rozliczenia?**

**Odp.:** Klienci są obciążani opłatami na podstawie zużycia, podobnie jak w przypadku chmury Cognitive Services. Kontenery muszą być skonfigurowane do wysyłania danych zliczania do platformy Azure, a transakcje będą rozliczane odpowiednio. Zasoby używane w ramach usług hostowanych i lokalnych zostaną dodane do pojedynczego przydziału przy użyciu cen warstwowych, licząc względem obu użycia. Aby uzyskać więcej informacji, zobacz stronę cennika odpowiedniej oferty.

* [Wykrywacz anomalii][ad-containers-billing]
* [Przetwarzanie obrazów][cv-containers-billing]
* [Twarzy][fa-containers-billing]
* [Aparat rozpoznawania formularzy][fr-containers-billing]
* [Language Understanding (LUIS)][lu-containers-billing]
* [Interfejs API usługi rozpoznawania mowy][sp-containers-billing]
* [Analiza tekstu][ta-containers-billing]
<!-- * [Translator Text][tt-containers-billing] -->

> [!IMPORTANT]
> Kontenery usługi cognitive Services nie są licencjonowane do uruchomienia bez połączenia z platformy Azure do zbierania danych. Klienci muszą włączyć kontener, aby komunikować informacje rozliczeniowe usłudze zliczania przez cały czas. Kontenery Cognitive Services nie wysyłają danych klienta do firmy Microsoft.
 
**Pyt.: Jaka jest bieżąca gwarancja pomocy technicznej dla kontenerów?**

**Odp.:** Nie ma gwarancji dla wersji zapoznawczych. Standardowa gwarancja firmy Microsoft dla oprogramowania dla przedsiębiorstw zostanie zastosowana, gdy kontenery są ogłoszone jako ogólnie dostępne.
 
**Pyt.: Co się stanie Cognitive Services kontenery po utracie połączenia z Internetem?**

**Odp.:** Kontenery Cognitive Services *nie są licencjonowane* do uruchamiania bez połączenia z platformą Azure w celu pomiaru. Klienci muszą włączyć kontenery do komunikowania się z usługą pomiaru przez cały czas.

**Pyt.: Jak długo może działać kontener bez połączenia z platformą Azure?**

**Odp.:** Kontenery Cognitive Services *nie są licencjonowane* do uruchamiania bez połączenia z platformą Azure w celu pomiaru. Klienci muszą włączyć kontenery do komunikowania się z usługą pomiaru przez cały czas.
 
**Pyt.: Co to jest bieżący sprzęt wymagany do uruchomienia tych kontenerów?**

**Odp.:** Kontenery Cognitive Services są kontenerami opartymi na architekturze x64, które mogą uruchamiać dowolne zgodne maszyny wirtualne z systemem Linux, maszynę wirtualną i Urządzenie brzegowe obsługujące kontenery platformy Docker x64 Wszystkie wymagają procesorów CPU. Minimalne i zalecane konfiguracje dla każdej oferty kontenera są dostępne poniżej:

* [Wykrywacz anomalii][ad-containers-recommendations]
* [Przetwarzanie obrazów][cv-containers-recommendations]
* [Twarzy][fa-containers-recommendations]
* [Aparat rozpoznawania formularzy][fr-containers-recommendations]
* [Language Understanding (LUIS)][lu-containers-recommendations]
* [Interfejs API usługi rozpoznawania mowy][sp-containers-recommendations]
* [Analiza tekstu][ta-containers-recommendations]
<!-- * [Translator Text][tt-containers-recommendations] -->
 
**Pyt.: Czy te kontenery są obecnie obsługiwane w systemie Windows?**

**Odp.:** Kontenery Cognitive Services to kontenery systemu Linux, ale w systemie Windows istnieje pewna obsługa kontenerów systemu Linux. Aby uzyskać więcej informacji na temat kontenerów systemu Linux w systemie Windows, zobacz [dokumentację platformy Docker](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/).
 
**Pyt.: Jak mogę odnaleźć kontenery?**

**Odp.:** Kontenery Cognitive Services są dostępne w różnych lokalizacjach, takich jak Azure Portal, Hub Docker i rejestry kontenerów platformy Azure. W przypadku najnowszych lokalizacji kontenera zapoznaj się z [repozytoriami i obrazami kontenerów](../cognitive-services-container-support.md#container-repositories-and-images).

**Pyt.: Jak Cognitive Services kontenery są porównywane z ofertami usługi AWS i Google?**

**Odp.:** Firma Microsoft jest pierwszym dostawcą usług w chmurze, który umożliwia przenoszenie wstępnie przeszkolonych modeli AI do kontenerów z prostymi rozliczeniami na transakcję, tak jakby klienci korzystali z usługi w chmurze. Firma Microsoft uważa, że chmura hybrydowa zapewnia klientom większy wybór.

**Pyt.: Jakie certyfikaty zgodności są kontenerami?**

**Odp.:** Kontenery usług poznawczych nie mają żadnych certyfikatów zgodności

**Pyt.: W jakich regionach są dostępne Cognitive Services kontenery?**

**Odp.:** Kontenery mogą być uruchamiane w dowolnym miejscu w dowolnym regionie, jednak potrzebują klucza i wywołania zwrotnego do platformy Azure w celu pomiaru. Wszystkie obsługiwane regiony dla usługi w chmurze są obsługiwane dla wywołania pomiaru kontenerów.

[!INCLUDE [Containers next steps](includes/containers-next-steps.md)]

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-containers]: ../speech-service/speech-container-howto.md
[ta-containers]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md
<!-- [tt-containers]: ../translator/how-to-install-containers.md -->

[ad-containers-billing]: ../anomaly-Detector/anomaly-detector-container-howto.md#billing
[cv-containers-billing]: ../computer-vision/computer-vision-how-to-install-containers.md#billing
[fa-containers-billing]: ../face/face-how-to-install-containers.md#billing
[fr-containers-billing]: ../form-recognizer/form-recognizer-container-howto.md#billing
[lu-containers-billing]: ../luis/luis-container-howto.md#billing
[sp-containers-billing]: ../speech-service/speech-container-howto.md#billing
[ta-containers-billing]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#billing
<!-- [tt-containers-billing]: ../translator/how-to-install-containers.md#billing -->

[ad-containers-recommendations]: ../anomaly-Detector/anomaly-detector-container-howto.md#container-requirements-and-recommendations
[cv-containers-recommendations]: ../computer-vision/computer-vision-how-to-install-containers.md#container-requirements-and-recommendations
[fa-containers-recommendations]: ../face/face-how-to-install-containers.md#container-requirements-and-recommendations
[fr-containers-recommendations]: ../form-recognizer/form-recognizer-container-howto.md#container-requirements-and-recommendations
[lu-containers-recommendations]: ../luis/luis-container-howto.md#container-requirements-and-recommendations
[sp-containers-recommendations]: ../speech-service/speech-container-howto.md#container-requirements-and-recommendations
[ta-containers-recommendations]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#container-requirements-and-recommendations
<!-- [tt-containers-recommendations]: ../translator/how-to-install-containers.md#container-requirements-and-recommendations -->
