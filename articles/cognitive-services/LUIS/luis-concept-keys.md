---
title: Jak używać kluczy tworzenia i środowiska uruchomieniowego z usługi LUIS
titleSuffix: Azure Cognitive Services
description: Usługa LUIS używa dwóch kluczy, klucza tworzenia do tworzenia modelu i klucza środowiska wykonawczego do wykonywania zapytań punktu końcowego przewidywania z wypowiedziami użytkownika.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9d213c8fa03ad2ca5e5fd7e620e52aa502749be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220964"
---
# <a name="authoring-and-runtime-keys"></a>Tworzenie i klucze środowiska uruchomieniowego

Zrozumienie języka (LUIS) ma dwie usługi i zestawy interfejsu API: 

* Tworzenie (wcześniej znane jako _programowe)_
* Przewidywanie środowiska uruchomieniowego

Istnieje kilka typów kluczy, w zależności od tego, z jaką usługą chcesz pracować i jak chcesz z nią pracować.

## <a name="non-azure-resources-for-luis"></a>Zasoby spoza platformy Azure dla usługi LUIS

### <a name="starter-key"></a>Klucz startowy

Po pierwszym uruchomieniu usługi LUIS tworzony jest klucz **startowy.** Ten zasób zapewnia:

* bezpłatne żądania usługi tworzenia za pośrednictwem portalu usługi LUIS lub interfejsów API (w tym sdków)
* bezpłatne 1000 żądań punktu końcowego prognozowania miesięcznie za pośrednictwem przeglądarki, interfejsu API lub SDK

## <a name="azure-resources-for-luis"></a>Zasoby platformy Azure dla usługi LUIS

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

Usługa LUIS umożliwia trzy typy zasobów platformy Azure: 
 
|Klucz|Przeznaczenie|Usługi poznawcze`kind`|Usługi poznawcze`type`|
|--|--|--|--|
|[Klucz tworzenia](#programmatic-key)|Uzyskiwanie dostępu do danych aplikacji i zarządzanie nimi za pomocą tworzenia, szkolenia, publikowania i testowania. Utwórz klucz tworzenia usługi LUIS, jeśli zamierzasz programowo tworzyć aplikacje usługi LUIS.<br><br>Klucz ma `LUIS.Authoring` na celu umożliwienie:<br>* programowo zarządzać aplikacjami i modelami language understanding, w tym szkoleniami i publikowaniem<br> * uprawnienia kontroli do zasobu autora, przypisując osoby do [roli współautora](#contributions-from-other-authors).|`LUIS.Authoring`|`Cognitive Services`|
|[Klucz przewidywania](#prediction-endpoint-runtime-key)| Żądania punktu końcowego prognozowania kwerend. Utwórz klucz przewidywania usługi LUIS, zanim aplikacja kliencka zażąda prognoz poza 1000 żądań dostarczonych przez zasób startowy. |`LUIS`|`Cognitive Services`|
|[Klucz zasobów usługi Cognitive Service dla wielu usług](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Żądania punktu końcowego przewidywania kwerend udostępnione usługi LUIS i innym obsługiwanym usługom Cognitive Services.|`CognitiveServices`|`Cognitive Services`|

Po zakończeniu procesu tworzenia zasobów [przypisz klucz](luis-how-to-azure-subscription.md) do aplikacji w portalu usługi LUIS.

Ważne jest, aby tworzyć aplikacje usługi LUIS w [regionach,](luis-reference-regions.md#publishing-regions) w których chcesz opublikować i zbadać.

> [!CAUTION]
> Dla wygody wiele przykładów używa [klucza Starter,](#starter-key) ponieważ zapewnia kilka wolnych wywołań punktu końcowego przewidywania w swoim [przydziałze.](luis-boundaries.md#key-limits)  


### <a name="query-prediction-resources"></a>Zasoby przewidywania kwerend

* Klucz środowiska wykonawczego może służyć do wszystkich aplikacji usługi LUIS lub dla określonych aplikacji usługi LUIS. 
* Nie należy używać klucza środowiska wykonawczego do tworzenia aplikacji usługi LUIS. 

Punkt końcowy środowiska wykonawczego usługi LUIS akceptuje dwa style kwerendy, oba używają klucza wykonawczego punktu końcowego przewidywania, ale w różnych miejscach.

Punkt końcowy używany do uzyskiwania dostępu do środowiska wykonawczego używa poddomeny, która `{region}` jest unikatowa dla regionu zasobu, oznaczone w poniższej tabeli. 

## <a name="assignment-of-the-key"></a>Przypisanie klucza

Klucz środowiska uruchomieniowego można [przypisać](luis-how-to-azure-subscription.md) w [portalu usługi LUIS](https://www.luis.ai) lub za pośrednictwem odpowiednich interfejsów API. 

## <a name="key-limits"></a>Kluczowe limity

Można utworzyć maksymalnie 10 kluczy tworzenia na region na subskrypcję. 

Zobacz [Limity kluczy](luis-boundaries.md#key-limits) i [regiony platformy Azure](luis-reference-regions.md). 

Regiony publikowania różnią się od regionów tworzenia. Upewnij się, że tworzysz aplikację w regionie tworzenia odpowiadającym regionowi publikowania, w który ma się znajdować aplikacja kliencka.

## <a name="key-limit-errors"></a>Błędy limitu kluczy
Jeśli przekroczysz przydział transakcji na sekundę (TPS), zostanie wyświetlony błąd HTTP 429. Jeśli przekroczysz przydział transakcji na miesiąc (TPS), zostanie wyświetlony błąd HTTP 403. 

## <a name="contributions-from-other-authors"></a>Wypowiedzi innych autorów

**W przypadku tworzenia aplikacji [migrowanych zasobów:](luis-migration-authoring.md) ** _współautorzy_ są zarządzani w witrynie Azure portal dla zasobu autorskiego przy użyciu strony **Kontrola dostępu (IAM).** Dowiedz [się, jak dodać użytkownika,](luis-how-to-collaborate.md)używając adresu e-mail współpracownika i roli _współautora._ 

**W przypadku aplikacji, które nie zostały jeszcze zmigrowane:** wszyscy _współpracownicy_ są zarządzane w portalu usługi LUIS ze strony **Zarządzanie -> współpracownikami.**

## <a name="move-transfer-or-change-ownership"></a>Przenoszenie, przenoszenie lub zmienianie własności

Aplikacja jest definiowana przez jego zasobów platformy Azure, który jest określany przez subskrypcję właściciela. 

Możesz przenieść aplikację usługi LUIS. Użyj następujących zasobów dokumentacji w witrynie Azure portal lub interfejsu wiersza polecenia platformy Azure:

* [Przenoszenie aplikacji między zasobami tworzenia usługi LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Przenoszenie zasobu do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Przenoszenie zasobu w ramach tej samej subskrypcji lub subskrypcji](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

Aby przenieść [własność](../../cost-management-billing/manage/billing-subscription-transfer.md) subskrypcji: 

**Dla użytkowników, którzy migrowali — [tworzenie zasobów migrowanych](luis-migration-authoring.md) aplikacji**: Jako właściciel zasobu możesz dodać plik `contributor`.

**Dla użytkowników, którzy nie zmigrowali jeszcze:** Eksportuj aplikację jako plik JSON. Inny użytkownik usługi LUIS może zaimportować aplikację, stając się tym samym właścicielem aplikacji. Nowa aplikacja będzie miała inny identyfikator aplikacji.  

## <a name="access-for-private-and-public-apps"></a>Dostęp do aplikacji prywatnych i publicznych

W przypadku aplikacji **prywatnej** dostęp do środowiska uruchomieniowego jest dostępny dla właścicieli i współautorów. W przypadku aplikacji **publicznej** dostęp do środowiska uruchomieniowego jest dostępny dla wszystkich osób, które mają własny zasób usługi Azure [Cognitive Service](../cognitive-services-apis-create-account.md) lub [usługi LUIS](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) i mają identyfikator aplikacji publicznej. 

Obecnie nie ma katalogu aplikacji publicznych.

### <a name="authoring-access"></a>Tworzenie dostępu
Dostęp do aplikacji z portalu [usługi LUIS](luis-reference-regions.md#luis-website) lub interfejsów API tworzenia jest kontrolowany przez [zasób](https://go.microsoft.com/fwlink/?linkid=2092087) tworzenia platformy Azure. 

Właściciel i wszyscy współautorzy mają dostęp do autora aplikacji. 

|Dostęp do tworzenia obejmuje|Uwagi|
|--|--|
|Dodawanie lub usuwanie kluczy punktu końcowego||
|Eksportowanie wersji||
|Eksportowanie dzienników punktów końcowych||
|Importowanie wersji||
|Upublicznij aplikację|Gdy aplikacja jest publiczna, każda osoba z kluczem autora lub punktu końcowego może wysyłać zapytania do aplikacji.|
|Modyfikowanie modelu|
|Publikowanie|
|Przeglądanie wypowiedzi punktów końcowych dla [aktywnego uczenia się](luis-how-to-review-endpoint-utterances.md)|
|Szkolenie|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Przewidywanie dostępu do środowiska uruchomieniowego punktu końcowego

Dostęp do kwerendy punktu końcowego przewidywania jest kontrolowana przez ustawienie na stronie **Informacje o aplikacji** w sekcji **Zarządzanie.** 

|[Prywatny punkt końcowy](#runtime-security-for-private-apps)|[Publiczny punkt końcowy](#runtime-security-for-public-apps)|
|:--|:--|
|Dostępne dla właścicieli i współpracowników|Dostępne dla właściciela, współpracowników i innych osób, które znają identyfikator aplikacji|

Można kontrolować, kto widzi klucz środowiska wykonawczego usługi LUIS, wywołując go w środowisku serwer-serwer. Jeśli używasz usługi LUIS z bota, połączenie między botem a usługą LUIS jest już bezpieczne. Jeśli wywołujesz punkt końcowy usługi LUIS bezpośrednio, należy utworzyć interfejs API po stronie serwera (na przykład [funkcję](https://azure.microsoft.com/services/functions/)platformy Azure) z kontrolowanym [dostępem](https://azure.microsoft.com/services/active-directory/)(np. Gdy interfejs API po stronie serwera jest wywoływany i uwierzytelniany, a autoryzacja jest weryfikowana, należy przekazać połączenie do usługi LUIS. Chociaż ta strategia nie zapobiega atakom typu man-in-the-middle, zaciemnia adres URL klucza i punktu końcowego od użytkowników, umożliwia śledzenie dostępu i umożliwia dodawanie rejestrowania odpowiedzi punktu końcowego [(np.](https://azure.microsoft.com/services/application-insights/)

#### <a name="runtime-security-for-private-apps"></a>Zabezpieczenia środowiska uruchomieniowego dla aplikacji prywatnych

Środowisko wykonawcze aplikacji prywatnej jest dostępne tylko dla następujących osób:

|Klucz i użytkownik|Wyjaśnienie|
|--|--|
|Klucz autora właściciela| Do 1000 trafień w punkcie końcowym|
|Klucze tworzenia współpracownika/współautora| Do 1000 trafień w punkcie końcowym|
|Dowolny klucz przypisany do usługi LUIS przez autora lub współpracownika/współautora|Na podstawie warstwy użycia klucza|

#### <a name="runtime-security-for-public-apps"></a>Zabezpieczenia środowiska uruchomieniowego dla aplikacji publicznych

Gdy aplikacja jest skonfigurowana jako publiczna, _każdy_ prawidłowy klucz tworzenia usługi LUIS lub klucz punktu końcowego usługi LUIS może wysyłać zapytania do aplikacji, o ile klucz nie był używany w całym przydziałzie punktu końcowego.

Użytkownik, który nie jest właścicielem lub współautorem, może uzyskać dostęp do środowiska uruchomieniowego aplikacji publicznej tylko wtedy, gdy podano identyfikator aplikacji. Usługa LUIS nie ma _rynku_ publicznego ani innego sposobu wyszukiwania aplikacji publicznej.  

Aplikacja publiczna jest publikowana we wszystkich regionach, dzięki czemu użytkownik z kluczem zasobu usługi LUIS oparty na regionie może uzyskać dostęp do aplikacji w dowolnym regionie skojarzonym z kluczem zasobu.

## <a name="transfer-of-ownership"></a>Przeniesienie własności

Usługa LUIS nie ma pojęcia przenoszenia własności zasobu. 

## <a name="securing-the-endpoint"></a>Zabezpieczanie punktu końcowego 

Można kontrolować, kto może zobaczyć klucz punktu końcowego punktu końcowego przewidywania usługi LUIS, wywołując go w środowisku serwer-serwer. Jeśli używasz usługi LUIS z bota, połączenie między botem a usługą LUIS jest już bezpieczne. Jeśli wywołujesz punkt końcowy usługi LUIS bezpośrednio, należy utworzyć interfejs API po stronie serwera (na przykład [funkcję](https://azure.microsoft.com/services/functions/)platformy Azure) z kontrolowanym [dostępem](https://azure.microsoft.com/services/active-directory/)(np. Po wywołaniu interfejsu API po stronie serwera i weryfikacji uwierzytelniania i autoryzacji należy przekazać połączenie do usługi LUIS. Chociaż ta strategia nie zapobiega atakom typu man-in-the-middle, zaciemnia punkt końcowy od użytkowników, umożliwia śledzenie dostępu i umożliwia dodawanie rejestrowania odpowiedzi punktu końcowego [(np.](https://azure.microsoft.com/services/application-insights/)  

## <a name="next-steps"></a>Następne kroki

* Poznaj pojęcia [dotyczące przechowywania wersji.](luis-concept-version.md) 
* Dowiedz [się, jak tworzyć klucze](luis-how-to-azure-subscription.md).
