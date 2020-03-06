---
title: Azure Government sieci Web, urządzeń przenośnych i interfejsu API | Microsoft Docs
description: Zapewnia to porównanie funkcji i wskazówek dotyczących tworzenia aplikacji dla Azure Government
services: azure-government
cloud: gov
documentationcenter: ''
author: gsacavdm
manager: pathuff
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/22/2019
ms.author: gsacavdm
ms.openlocfilehash: 15c6936b7a8c319c4ddef86eddc47546966782b4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396331"
---
# <a name="azure-government-web--mobile"></a>Azure Government Sieć Web + aplikacje mobilne

W tym artykule opisano usługi sieci Web i Mobile dla środowiska Azure Government, opisujące różnice funkcji, które różnią się od wersji publicznej, a także wszelkie uwagi dotyczące środowiska.

## <a name="azure-cognitive-search"></a>Azure Cognitive Search

[Usługa Azure wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/) jest ogólnie dostępna w Azure Government. Aby skorzystać z samodzielnej eksploracji funkcji wyszukiwania przy użyciu publicznych danych rządowych, odwiedź witrynę sieci Web [wyszukiwania zawartości i analizy](https://documentsearch.azurewebsites.net/home/) , wybierz zestaw danych "sąd USA z odwołaniami do regionu 1", a następnie wybierz jedną z opcji demonstracyjnych.

Funkcje wyszukiwania, które zostały powszechnie przyjęte w aplikacjach do wyszukiwania dla instytucji rządowych, obejmują [umiejętności poznawcze](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro), przydatne w przypadku wyodrębniania struktury i informacji z dużych nierozróżnianych dokumentów tekstowych.

Podstawowa składnia zapytań, która opracowuje zapytania do wyszukiwania dużej ilości zawartości, dotyczy również deweloperów aplikacji. Usługa Azure Wyszukiwanie poznawcze obsługuje dwie składnie: [proste](https://docs.microsoft.com/azure/search/query-simple-syntax) i [pełne](https://docs.microsoft.com/azure/search/query-lucene-syntax). [Przykłady wyrażeń zapytania](https://docs.microsoft.com/azure/search/search-query-simple-examples) można przejrzeć dla orientacji.

### <a name="variations"></a>Obsługi
Punkt końcowy usług wyszukiwania utworzonych w Azure Government jest następujący:

| Typ usługi | Azure Public | Azure Government |
| ------------ | ------------ | ---------------- |
| Usługa Wyszukiwanie poznawcze platformy Azure |\*. search.windows.net |\*. search.windows.us|

Wszystkie funkcje ogólnie dostępne i w wersji zapoznawczej w chmurze publicznej są również dostępne w Azure Government.

### <a name="considerations"></a>Zagadnienia do rozważenia

Poniższe informacje identyfikują granicę Azure Government dla Wyszukiwanie poznawcze platformy Azure:

| Dozwolone dane regulowane/kontrolowane | Dane regulowane/kontrolowane nie są dozwolone |
| ----------------------------------- | --------------------------------------- |
| Wszystkie dane przechowywane i przetwarzane na platformie Azure Wyszukiwanie poznawcze mogą zawierać dane regulowane Azure Government. | Metadane usługi Azure Wyszukiwanie poznawcze nie mogą zawierać danych kontrolowanych eksportem. Te metadane obejmują wszystkie dane konfiguracyjne wprowadzone podczas tworzenia i konserwowania usługi. Nie wprowadzaj danych z regulowanej/kontrolowanej do następujących pól: Nazwa subskrypcji, grupy zasobów, nazwa usługi, nazwy zasobów, Tagi zasobów lub dowolna nazwa obiektu, który można opisać w Wyszukiwanie poznawcze (indeksy, indeksatory, źródła danych, mapy synonimów i umiejętności). Nie uwzględniaj poufnych danych w nagłówkach HTTP wysyłanych do żądania ściągnięcia interfejsu API REST w ciągach wyszukiwania/zapytań wysyłanych w ramach interfejsu API.|

## <a name="app-services"></a>App Services
### <a name="variations"></a>Obsługi
Usługa Azure App Services jest ogólnie dostępna w Azure Government.

Adres Azure App Service aplikacji tworzonych w Azure Government różni się od aplikacji utworzonych w chmurze publicznej:

| Typ usługi | Azure Public | Azure Government |
| --- | --- | --- |
| App Service |\*. azurewebsites.net |\*. azurewebsites.us|
| Identyfikator jednostki usługi| abfa0a7c-a6b6-4736-8310-5855508787cd | 6a02c803-dafd-4136-b4c3-5a6f318b4714 |

Niektóre funkcje App Service dostępne w chmurze publicznej nie są jeszcze dostępne w programie Azure Government:

- Diagnozowanie i rozwiązywanie problemów
- Wdrożenie
    - Opcje wdrażania: dostępne są tylko lokalne repozytorium git i zewnętrzne repozytorium.
    - Centrum wdrażania
- Ustawienia
    - Application Insights
- Narzędzia programistyczne
    - Test wydajnościowy
    - Eksplorator zasobów
    - Debugowanie PHP
- Obsługa & Rozwiązywanie problemów
    - App Service Advisor
- Certyfikaty usługi App Service


### <a name="considerations"></a>Zagadnienia do rozważenia
Poniższe informacje identyfikują Azure Government granicy App Service:

| Dozwolone dane regulowane/kontrolowane | Dane regulowane/kontrolowane nie są dozwolone |
| --- | --- |
| Dane wprowadzone, przechowywane i przetwarzane w ramach Azure App Service mogą zawierać dane kontrolowane eksportem. Pliki binarne działające w Azure App Service. Statyczne uwierzytelnienia, takie jak hasła i numery PIN kart inteligentnych, w celu uzyskania dostępu do składników platformy platformy Azure. Klucze prywatne certyfikatów używane do zarządzania składnikami platformy Azure. Parametry połączenia SQL. Inne informacje dotyczące zabezpieczeń/wpisy tajne, takie jak certyfikaty, klucze szyfrowania, klucze główne i klucze magazynu przechowywane w usługach platformy Azure. |Metadane nie mogą zawierać danych kontrolowanych eksportem. Te metadane obejmują wszystkie dane konfiguracyjne wprowadzone podczas tworzenia i konserwowania Azure App Service. Nie wprowadzaj danych z regulowane/kontrolowane do następujących pól: grupy zasobów, nazwy zasobów, Tagi zasobów|

## <a name="api-management"></a>API Management
Aby uzyskać szczegółowe informacje dotyczące tej usługi i sposobu korzystania z niej, zobacz [dokumentację usługi Azure API Management](../api-management/index.yml).

### <a name="variations"></a>Obsługi

Usługa Azure API Management Service jest ogólnie dostępna w Azure Government. Funkcje, które nie są obecnie dostępne w usłudze API Management Service for Azure Government, to:

- Integracja Azure AD B2C 
    - Integracja z usługą Azure AD B2C nie jest dostępna w Azure Government 

Adresy URL do uzyskiwania dostępu do usługi Azure API Management w Azure Government są różne:

| Typ usługi | Azure Public | Azure Government |
| --- | --- | --- |
|API Management Gateway| \*. azure-api.net| \*. azure-api.us|
|Portal API Management | \*. portal.azure-api.net |\*. portal.azure-api.us| 
|Zarządzanie API Management| \*. management.azure-api.net |\*. management.azure-api.us|

### <a name="considerations"></a>Zagadnienia do rozważenia
Poniższe informacje identyfikują granicę Azure Government usługi Azure API Management:

| Dozwolone dane regulowane/kontrolowane | Dane regulowane/kontrolowane nie są dozwolone |
| --- | --- |
|Wszystkie dane przechowywane i przetwarzane w usłudze Azure API Management Service mogą zawierać dane objęte Azure Governmentmi.|Metadane usługi Azure API Management nie mogą zawierać danych kontrolowanych eksportem. Nie wprowadzaj danych z regulowane/kontrolowane do następujących pól: API Management nazwa usługi, nazwa subskrypcji, grupy zasobów, Tagi zasobów.|

## <a name="next-steps"></a>Następne kroki
Aby uzyskać dodatkowe informacje i aktualizacje, zasubskrybuj [Blog Microsoft Azure Government.](https://blogs.msdn.microsoft.com/azuregov/)

