---
title: Chmury Cruiser i rozliczeń integracji interfejsu API platformy Microsoft Azure | Dokumentacja firmy Microsoft
description: Zawiera unikatowy perspektywy od firmy Microsoft Azure Billing partnera Cruiser chmury ich doświadczeń integrowanie rozliczeń interfejsów API usługi Azure ich produktu.  Jest to szczególnie przydatne dla platformy Azure i w chmurze Cruiser użytkowników, którzy chcą przy użyciu/próby Cruiser chmury dla pakietu Microsoft Azure Pack.
services: ''
documentationcenter: ''
author: tonguyen
manager: tonguyen
editor: ''
tags: billing
ms.assetid: b65128cf-5d4d-4cbd-b81e-d3dceab44271
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/09/2017
ms.author: mobandyo
ms.openlocfilehash: 487636ffb0efc35c282e14d835c6669ed9d47315
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="cloud-cruiser-and-microsoft-azure-billing-api-integration"></a>Cruiser i rozliczeń integracji interfejsu API platformy Microsoft Azure w chmurze
W tym artykule opisano, jak można użyć informacji zebranych w nowych Microsoft Azure Billing interfejsów API w chmurze Cruiser symulacji koszt przepływu pracy i analizy.

## <a name="azure-ratecard-api"></a>Interfejs API Azure RateCard
Interfejs API RateCard informacje szybkość z platformy Azure. Po uwierzytelnieniu za pomocą odpowiednich poświadczeń, można zbadać interfejsu API do zbierania metadanych dotyczących usług dostępnych na platformie Azure, wraz z stawek przy użyciu Twojego identyfikatora oferty.

Następujące przykładowe odpowiedzi jest z interfejsu API z cenami dla A0 wystąpienia (z systemem Windows):

    {
        "MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",
        "MeterName": "Compute Hours",
        "MeterCategory": "Virtual Machines",
        "MeterSubCategory": "A0 VM (Windows)",
        "Unit": "Hours",
        "MeterRates":
        {
            "0": 0.029
        },
        "EffectiveDate": "2014-08-01T00:00:00Z",
        "IncludedQuantity": 0.0,
        "MeterStatus": "Active"
    },

### <a name="cloud-cruisers-interface-to-azure-ratecard-api"></a>Interfejs w Cruiser Azure RateCard API w chmurze
Cruiser chmury można użyć informacji RateCard interfejsu API na różne sposoby. W tym artykule zostanie przedstawiony sposób może służyć do upewnij IaaS obciążenia koszt symulacji i analizy.

Aby zademonstrować ten przypadek użycia, załóżmy obciążeń uruchomionych na Microsoft Azure Pack (map) kilka wystąpień. Celem jest do symulowania tego samego obciążenia na platformie Azure i oszacowanie kosztów wykonywania takich migracji. Aby można było symulację, istnieją dwa główne zadania do wykonania:

1. **Importowanie i przetworzyć informacje zebrane z interfejsu API RateCard usługi.** To zadanie, również odbywa się na skoroszytach, gdzie przekształcać i publikowane do nowego planu szybkość wyodrębniania z interfejsu API RateCard. Tego nowego planu szybkość jest używany na symulacje oszacowanie cen platformy Azure.
2. **Normalizuj WAP usług i usług platformy Azure dla IaaS.** Domyślnie usługi WAP są oparte na poszczególnych zasobów (procesora CPU, pamięci, rozmiar dysku itp.), podczas gdy platforma Azure usługi są oparte na rozmiar wystąpienia (A0 A1, A2, itp.). To zadanie pierwszy można wykonywany przez aparat ETL Cruiser chmury, nazywany skoroszytów, gdzie dołączone tych zasobów na rozmiary wystąpienia analogiczne do usług Azure wystąpienia.

### <a name="import-data-from-the-ratecard-api"></a>Importuj dane z interfejsu API RateCard
Skoroszyty Cruiser chmury umożliwiają automatyczne zbierania i przetwarzania informacji z interfejsu API RateCard.  ETL (extract — przekształcenia obciążenia) skoroszyty umożliwiają konfigurowanie zbierania, przekształcania i publikowania danych w bazie danych Cruiser chmury.

Każdego skoroszytu mogą mieć jeden lub wiele kolekcji, umożliwiając służące do skorelowania danych z różnych źródeł do uzupełnienia lub rozszerzyć dane użycia. Dwa poniższe zrzuty ekranu pokazują, jak utworzyć nową *kolekcji* w istniejącym skoroszycie i importowania informacji do *kolekcji* z interfejsu API RateCard:

![Rysunek 1 — Tworzenie nowej kolekcji][1]

![Rysunek 2 — importowanie danych z nowej kolekcji][2]

Po zaimportowaniu danych do skoroszytu, jest możliwość utworzenia wielu kroków i procesy przetwarzania do modyfikacji i modelu danych. W tym przykładzie ponieważ tylko Dbamy o infrastruktury jako — usługa (IaaS), możemy wykonaj kroki przekształcania, aby usunąć niepotrzebne wierszy lub rekordy, związane z usługami innych niż IaaS.

Poniższy zrzut ekranu przedstawia kroki przekształcania używane do przetwarzania danych zbieranych z RateCard interfejsu API:

![Rysunek 3 – przekształcania kroki, aby przetworzyć dane zebrane z interfejsu API RateCard][3]

### <a name="defining-new-services-and-rate-plans"></a>Definiowanie nowych usług i szybkość plany
Istnieją różne sposoby definiowania usługi w chmurze Cruiser. Jedną z opcji jest importowanego usług danych użycia. Ta metoda jest często używana podczas pracy z chmur publicznych, gdy usługi są już zdefiniowane przez dostawcę.

Planie taryfowym to zestaw stawki lub cen, które mogą być stosowane do różnych usług, na podstawie daty obowiązywania lub grupy odbiorców, między innymi opcjami. Szybkość planów można również na chmurze Cruiser utworzyć symulacji lub scenariuszy "Co jeśli", aby zrozumieć wpływ całkowity koszt obciążenia zmian w usługach.

W tym przykładzie używamy informacje o usłudze z interfejsu API RateCard do definiowania nowych usług w chmurze Cruiser. W taki sam sposób możemy użyć stawek do usług do utworzenia nowego planie taryfowym na chmurze Cruiser.

Na końcu procesu transformacji jest możliwość tworzenia nowy krok i opublikować dane z interfejsu API RateCard jako nowe usługi i szybkości.

![Rysunek 4 - publikowania danych z interfejsu API RateCard nowych usług i szybkości][4]

### <a name="verify-azure-services-and-rates"></a>Sprawdź usług platformy Azure i szybkości
Po opublikowaniu usługi i stawki, możesz sprawdzić listę importowanych usług w chmurze Cruiser *usług* karty:

![Rysunek 5. Sprawdzanie nowych usług][5]

Na *plany szybkość* kartę, można sprawdzić nowy plan szybkość o nazwie "AzureSimulation" szybkości przetwarzania zaimportowane z interfejsu API RateCard.

![Rysunek 6 - weryfikowanie nowego planie taryfowym i skojarzone stawki][6]

### <a name="normalize-wap-and-azure-services"></a>Normalizuj WAP i usług Azure
Domyślnie WAP zawiera informacje o użyciu, oparta na użyciu obliczeniowych, pamięci i zasobów sieciowych. W chmurze Cruiser, można zdefiniować usługi na podstawie bezpośrednio na alokacji lub naliczane użycia tych zasobów. Na przykład można ustawić podstawowe częstotliwość dla każdej godziny użycia procesora CPU lub obciążania GB pamięci przydzielonej do wystąpienia.

Na przykład w celu porównania kosztów między WAP i platformą Azure, musimy łącznego użycia zasobów na WAP do pakietów, które następnie mogą być mapowane do usług platformy Azure. Można łatwo zaimplementować tej transformacji w skoroszytach:

![Rysunek 7 - przekształcania danych WAP do normalizacji usług][7]

Ostatnim krokiem w skoroszycie jest do publikowania danych w bazie danych Cruiser chmury. W tym kroku dane użycia jest teraz połączone w usługach (mapowane do usług Azure) i powiązane stawki domyślne można utworzyć opłat.

Po zakończeniu skoroszyt, przetwarzania danych, można zautomatyzować Dodawanie zadania w ramach harmonogramu zadań i określając częstotliwość i godzinę dla skoroszytu uruchomić.

![Rysunek 8 - planowania skoroszytu][8]

### <a name="create-reports-for-workload-cost-simulation-analysis"></a>Tworzenie raportów analizy kosztów symulacji obciążenia
Po użycia są zbierane i opłaty są ładowane do bazy danych Cruiser chmury, możemy użyć modułu chmury Cruiser wgląd do utworzenia obciążenia koszt symulacji, który mamy konieczna.

W celu zaprezentowania tego scenariusza, utworzyliśmy następującego raportu:

![Porównanie kosztów][9]

Górny wykres przedstawia porównania kosztów przez usługi porównania ceny pracą dla każdej określonej usługi między WAP (ciemny niebieski) i Azure (niebieski światła).

Wykres dolnej zawiera te same dane, ale według działów. Koszty dla każdego działu do uruchamiania ich obciążenie na WAP i na platformie Azure, wraz z różnica między nimi jest wyświetlany w pasku oszczędności (zielony).

## <a name="azure-usage-api"></a>Interfejs API użycia platformy Azure
### <a name="introduction"></a>Wprowadzenie
Firma Microsoft wprowadziła ostatnio interfejsu API użycia usługi Azure, umożliwiając subskrybentów programowo ściągnąć danych użycia, aby uzyskać wgląd w ich użycia. Klienci Cruiser chmury można korzystać z bardziej zaawansowane funkcje dostępne za pośrednictwem tego interfejsu API zestawu danych.

Cruiser chmury za pomocą integracji interfejsu API użycia na kilka sposobów. Poziom szczegółowości (co godzinę informacje o użyciu) oferujące informacji metadanych zasobów dostępnych za pośrednictwem interfejsu API zestawu danych niezbędne do obsługi elastyczne ogólnej analizy kosztów lub obciążenia zwrotnego modeli. 

W tym samouczku firma Microsoft przedstawia przykład metody Cruiser chmury mogą korzystać z informacji użycia interfejsu API. W szczególności firma Microsoft będzie Utwórz grupę zasobów na platformie Azure, skojarzyć tagi dla struktury konta, a następnie opisano proces ściąganie i przetwarzania informacji znacznika w chmurze Cruiser.

Końcowe celem jest możliwość tworzenia raportów podobny do następującego i można analizować kosztów i zużycia na podstawie struktury konta wypełnienia znaczników.

![Na rysunku nr 10 - raport o awarii przy użyciu tagów][10]

### <a name="microsoft-azure-tags"></a>Tagi platformy Microsoft Azure
Dostępne za pośrednictwem interfejsu API Azure użycia danych obejmuje nie tylko informacje o, ale także w tym wszystkie znaczniki skojarzone z nim metadane zasobu. Znaczniki umożliwiają łatwe do organizowania zasobów, ale w celu zapewnienia efektywności, należy upewnić się, że:

* Tagi poprawnie są stosowane do zasobów w czasie udostępniania
* Tagi prawidłowo są używane w procesie ogólnej analizy kosztów/opłaty zwrotne powiązać użycia do struktury konta organizacji.

Oba te wymagania może być trudne, szczególnie w przypadku ręcznego procesu na dostarczenie lub ładowania strony. Błędnie, nieprawidłowe lub niekompletne nawet tagi są typowych utrudnień od klientów po przy użyciu tagów i te błędy może utrudnić życia na stronie pobierania.

Przy użyciu nowego Azure użycia interfejsu API Cruiser chmury można pobierać informacje o zasobie znakowania i za pomocą zaawansowanej ETL narzędzie skoroszytów, usunąć te typowe błędy znakowania. Za pomocą przekształcania za pomocą wyrażeń regularnych i danych korelacji Cruiser chmury można zidentyfikować niepoprawnie oznakowanych zasobów i zastosować poprawne tagów, zapewnienie poprawne skojarzenie zasobów z klienta.

Na stronie pobierania Cruiser chmury automatyzuje proces ogólnej analizy kosztów/opłaty zwrotne i skorzystaj z informacji tag powiązać użycia odpowiedniego użytkownika (działu dzielenia, Project, itp.). Ta Automatyzacja zapewnia ogromny poprawy jakości i zapewnia spójne i podlegającą inspekcji procesu ładowania.

### <a name="creating-a-resource-group-with-tags-on-microsoft-azure"></a>Tworzenie grupy zasobów z tagami w systemie Microsoft Azure
Pierwszym krokiem w tym samouczku jest utworzenie grupy zasobów w portalu Azure, następnie utwórz nowe tagi do powiązania z zasobów. Na przykład możemy utworzyć następujących tagów: działu, środowisko, właściciel projektu.

Poniższy zrzut ekranu przedstawia przykład grupy zasobów o znacznikach.

![Rysunek 11 — grupa zasobów o znacznikach w portalu Azure][11]

Następnym krokiem jest, aby pobrać informacje z użycia interfejsu API w chmurze Cruiser. Interfejs API użycia zawiera obecnie odpowiedzi w formacie JSON. Oto przykładowe dane pobrane:

    {
      "id": "/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXX/providers/Microsoft.Commerce/UsageAggregates/Daily_BRSDT_20150623_0000",
      "name": "Daily_BRSDT_20150623_0000",
      "type": "Microsoft.Commerce/UsageAggregate",
      "properties":
      {
        "subscriptionId": "bb678b04-0e48-4b44-XXXX-XXXXXXXXX",
        "usageStartTime": "2015-06-22T00:00:00+00:00",
        "usageEndTime": "2015-06-23T00:00:00+00:00",
        "meterName": "Compute Hours",
        "meterRegion": "",
        "meterCategory": "Virtual Machines",
        "meterSubCategory": "Standard_D1 VM (Non-Windows)",
        "unit": "Hours",
        "instanceData": "{\"Microsoft.Resources\":{\"resourceUri\":\"/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXXX/resourceGroups/DEMOUSAGEAPI/providers/Microsoft.Compute/virtualMachines/MyDockerVM\",\"location\":\"eastus\",\"tags\":{\"Department\":\"Sales\",\"Project\":\"Demo Usage API\",\"Environment\":\"Test\",\"Owner\":\"RSE\"},\"additionalInfo\":{\"ImageType\":\"Canonical\",\"ServiceType\":\"Standard_D1\"}}}",
        "meterId": "e60caf26-9ba0-413d-a422-6141f58081d6",
        "infoFields": {},
        "quantity": 8

      },
    },


### <a name="import-data-from-the-usage-api-into-cloud-cruiser"></a>Importuj dane z użycia interfejsu API w chmurze Cruiser
Skoroszyty Cruiser chmury umożliwiają automatyczne zbierania i przetwarzania informacji z użycia interfejsu API. Skoroszyt ETL (extract — przekształcenia obciążenia) umożliwia skonfigurowanie kolekcji, przekształcania i publikowania danych w bazie danych Cruiser chmury.

Każdego skoroszytu mogą mieć jeden lub wiele kolekcji. Dzięki temu można służące do skorelowania danych z różnych źródeł do uzupełnienia lub rozszerzyć dane użycia. W tym przykładzie tworzymy nowego arkusza w skoroszycie szablonu platformy Azure (*UsageAPI)* i ustaw nowy *kolekcji* Aby zaimportować informacje z użycia interfejsu API.

![Rysunek 3 – zaimportowane do arkusza UsageAPI danych użycia interfejsu API][12]

Powiadomienie, że ten skoroszyt już inne arkusze importowanego usług Azure (*ImportServices*) i przetwarzania informacji o użyciu z rozliczeniami interfejsu API (*PublishData*).

Następnie używamy użycia interfejsu API do wypełnienia *UsageAPI* arkusza i skorelowania informacji przy użyciu danych użycia z interfejsu API rozliczeń na *PublishData* arkusza.

### <a name="processing-the-tag-information-from-the-usage-api"></a>Przetwarzanie informacji dotyczących tagu z interfejsu API użycia
Po zaimportowaniu danych do skoroszytu, utworzymy kroki przekształcania *UsageAPI* arkusz, aby przetworzyć informacji z interfejsu API. Pierwszym krokiem jest na potrzeby procesor "Podział JSON" Wyodrębnij tagi z jednego pola, a następnie utwórz pola dla każdego z nich (działu, projektu właściciela i środowiska).

![Rysunek 4. Utwórz nowe pola informacji tag][13]

Zwróć uwagę, usługę "Sieć" brakuje informacji tag (żółty pole), ale można sprawdzić, jest częścią tej samej grupie zasobów, analizując *ResourceGroupName* pola. Ponieważ mamy znaczników do innych zasobów z tej grupy zasobów, możemy użyć tych informacji do zastosowania do tego zasobu w dalszej części procesu Brak tagów.

Następnym krokiem jest utworzenie tabeli odnośników kojarzenie informacji z znaczniki, aby *ResourceGroupName*. Tej tabeli odnośników jest używana w następnym kroku wzbogacić dane dotyczące zużycia informacje znaczników.

### <a name="adding-the-tag-information-to-the-consumption-data"></a>Dodawanie informacji znacznika do danych o zużyciu energii
Obecnie firma Microsoft może wykonać skok do *PublishData* arkusza, który przetwarza informacji o użyciu z interfejsu API rozliczeń i Dodaj pola wyodrębnione ze znacznikami. Ten proces odbywa się przez wyszukiwanie w tabeli odnośników utworzony w poprzednim kroku przy użyciu *ResourceGroupName* jako klucz wyszukiwania.

![Rysunek 5 - wypełnianie struktury konta o informacje z wyszukiwaniach][14]

Należy zauważyć, że pola struktury odpowiednie konto usługi "Sieć" zostały zastosowane, rozwiązywania problemu z Brak tagów. Możemy również wypełnić pola struktury konta dla zasobów innych niż naszych docelowej grupy zasobów "Inne" w celu odróżnienia ich w raportach.

Teraz musimy dodać krok do publikowania danych użytkowania. W tym kroku odpowiednie szybkości dla każdej usługi zdefiniowane w naszym planie taryfowym można zastosować do informacji o użyciu z wynikowy opłata ładowane do bazy danych.

Najlepsze w to, że wystarczy tylko raz przejść przez ten proces. Po zakończeniu skoroszyt, wystarczy dodać go do harmonogramu i uruchamiane co godzinę lub codziennie o zaplanowanej godzinie. Jest to jedynie tworzenie nowych raportów lub dostosowywanie istniejących, aby można było analizować dane, aby uzyskać wgląd w istotne dane z użycie w chmurze.

### <a name="next-steps"></a>Następne kroki
* Szczegółowe instrukcje dotyczące tworzenia chmury Cruiser skoroszyty i raportów można znaleźć w chmurze Cruiser online [dokumentacji](http://docs.cloudcruiser.com/) (nieprawidłowa jest wymagana nazwa logowania).  Aby uzyskać więcej informacji na temat Cruiser chmury, skontaktuj się z [ info@cloudcruiser.com ](mailto:info@cloudcruiser.com).
* Zobacz [uzyskać wgląd w Microsoft Azure użycia zasobów](billing-usage-rate-card-overview.md) omówienie API RateCard i użycia zasobów Azure.
* Zapoznaj się z [dokumentacja interfejsu API REST rozliczenia Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) uzyskać więcej informacji na obu interfejsów API, które są częścią zestawu interfejsów API dostarczonych przez Menedżera zasobów Azure.
* Jeśli chcesz przejść bezpośrednio do przykładowy kod, zobacz nasze Microsoft Azure Billing interfejsu API przykłady kodu na [przykłady kodu Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

### <a name="learn-more"></a>Dowiedz się więcej
* Aby dowiedzieć się więcej na temat usługi Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) artykułu.

<!--Image references-->

[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "Rysunek 1 — Tworzenie nowej kolekcji"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "Rysunek 2 — importowanie danych z nowej kolekcji"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "Rysunek 3 – przekształcania kroki, aby przetworzyć dane zebrane z interfejsu API RateCard"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "Rysunek 4 - publikowania danych z interfejsu API RateCard nowych usług i szybkości"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "Rysunek 5. Sprawdzanie nowych usług"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "Rysunek 6 - weryfikowanie nowego planie taryfowym i skojarzone stawki"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "Rysunek 7 - przekształcania danych WAP do normalizacji usług"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "Rysunek 8 - planowania skoroszytu"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "Rysunek 9 — przykładowy raport dla scenariusza porównania kosztów obciążenia"
[10]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/1_ReportWithTags.png "Na rysunku nr 10 - raport o awarii przy użyciu tagów"
[11]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/2_ResourceGroupsWithTags.png "Rysunek 11 — grupa zasobów o znacznikach w portalu Azure"
[12]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/3_ImportIntoUsageAPISheet.png "Rysunek 12 — zaimportowane do arkusza UsageAPI danych użycia interfejsu API"
[13]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/4_NewTagField.png "Rysunek 13 — Utwórz nowe pola informacji tag"
[14]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/5_PopulateAccountStructure.png "Rysunek 14 - wypełnianie struktury konta o informacje z wyszukiwaniach"
