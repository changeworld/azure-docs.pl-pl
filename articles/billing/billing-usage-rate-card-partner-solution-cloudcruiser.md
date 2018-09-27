---
title: Cloud Cruiser i platformy Microsoft Azure Billing integracji interfejsu API | Dokumentacja firmy Microsoft
description: Zapewnia unikatową perspektywę, Microsoft Azure Billing partnera Cloud Cruiser w ich środowiskach integrowanie interfejsów API rozliczeń platformy Azure do swojego produktu.  Jest to szczególnie przydatne w przypadku platformy Azure i Cloud Cruiser klientów, którzy są zainteresowani przy użyciu/podjęcie próby Cloud Cruiser dla pakietu Microsoft Azure Pack.
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
ms.author: erikre
ms.openlocfilehash: 95d90e898ddc8766cf96a5a72c315407cd596393
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393863"
---
# <a name="cloud-cruiser-and-microsoft-azure-billing-api-integration"></a>Cloud Cruiser i platformy Microsoft Azure Billing Integracja z interfejsem API
W tym artykule opisano, jak informacje zebrane z nowego Microsoft rozliczeń interfejsów API usługi Azure może służyć w Cloud Cruiser do przepływu pracy koszt symulowania i analiz.

## <a name="azure-ratecard-api"></a>Interfejs API karty oceny platformy Azure
Interfejsu API RateCard informacje stawki na platformie Azure. Po uwierzytelnieniu się przy użyciu odpowiednich poświadczeń, można tworzyć zapytania interfejs API, aby zebrać metadanych dotyczących usług dostępnych na platformie Azure, wraz z stawek za pomocą identyfikatora oferty

Następujące przykładowa odpowiedź pochodzi z interfejsu API z cenami dla opcji A0 wystąpienia (Windows):

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

### <a name="cloud-cruisers-interface-to-azure-ratecard-api"></a>Cloud Cruiser przez interfejs Azure RateCard interfejsu API
Cloud Cruiser znajdziesz informacje interfejsu API RateCard na różne sposoby. W tym artykule pokazujemy, jak może służyć się IaaS obciążenia kosztów, symulowania i analiz.

Aby zademonstrować ten przypadek użycia, Wyobraź sobie obciążenie kilka wystąpień na Microsoft Azure Pack (WAP). Celem jest w celu symulowania tego samego obciążenia na platformie Azure i oszacowanie kosztów działania takich migracji. Aby można było utworzyć tej symulacji, istnieją dwa główne zadania do wykonania:

1. **Importowanie i przetwarzania informacji usługi zebrane z interfejsu API RateCard.** To zadanie, również odbywa się w tych skoroszytach, w której wyodrębnione z interfejsu API RateCard są przekształcane i publikowane do nowego planu taryfowego. Tego nowego planu taryfowego jest używany na symulacje oszacowanie ceny platformy Azure.
2. **Normalizuj usługi WAP i usług Azure IaaS.** Domyślnie usługi proxy aplikacji sieci Web są oparte na poszczególnych zasobów (procesor CPU, pamięci o rozmiarze, rozmiaru dysku itp.), podczas Azure services opierają się na rozmiar wystąpienia (A0, A1, A2, itp.). To pierwsze zadanie może być wykonywany przez aparat ETL Cloud Cruiser, o nazwie skoroszyty, gdzie dołączone te zasoby na rozmiarów wystąpień, analogiczne do usług wystąpienia platformy Azure.

### <a name="import-data-from-the-ratecard-api"></a>Importuj dane z interfejsu API RateCard
Cloud Cruiser skoroszytach zautomatyzowany sposób do zbierania i przetwarzania informacji z interfejsu API RateCard.  ETL (extract-transform-load) skoroszyty umożliwiają skonfigurowanie kolekcji, przekształcania i publikowania danych w bazie danych Cloud Cruiser.

Każdy skoroszyt może mieć jednego lub wielu kolekcji, co umożliwia korelowanie informacji z różnych źródeł w celu uzupełnienia lub rozszerzyć danych użycia. Następujące dwa zrzuty ekranu pokazują, jak utworzyć nową *kolekcji* istniejący skoroszyt i importowania informacji do *kolekcji* z interfejsu API RateCard:

![Rysunek 1. Tworzenie nowej kolekcji][1]

![Rysunek 2. importowanie danych z nowej kolekcji][2]

Po zaimportowaniu danych do skoroszytu, jest możliwość tworzenia wielu kroki i procesy przetwarzania, modyfikowania i modelu danych. W tym przykładzie ponieważ firma Microsoft jest zainteresowany wyłącznie infrastruktury as-a-Service (IaaS), możemy użyć kroki przekształcania, aby usunąć zbędne wiersze lub rekordy, związane z usługami innymi niż IaaS.

Poniższy zrzut ekranu przedstawia kroki przekształcania, używane do przetwarzania danych zebranych z interfejsu API RateCard:

![Rysunek 3. kroki przekształcania, aby przetwarzać dane zebrane z interfejsu API RateCard][3]

### <a name="defining-new-services-and-rate-plans"></a>Definiowanie nowych usług i szybkość plany
Istnieją różne sposoby, aby zdefiniować usługi Cloud Cruiser. Jedną z opcji jest zaimportowanie usług z danych użycia. Ta metoda jest często używana podczas pracy z chmurami publicznymi, w której usługi zostały już zdefiniowane przez dostawcę.

Planie taryfowym to zbiór stawek lub cen, które mogą być stosowane do różnych usług, na podstawie daty obowiązywania lub grupy odbiorców, między innymi opcjami. W przypadku planów taryfowych można również na Cloud Cruiser do utworzenia symulacji lub scenariusze "Co jeśli", aby zrozumieć, jak zmiany w usługach wpływa na całkowity koszt obciążenia.

W tym przykładzie korzystamy z interfejsu API RateCard informacji usług do definiowania nowych usług w Cloud Cruiser. W taki sam sposób aby utworzyć nowy Plan stawki na Cloud Cruiser możemy użyć stawek do usług.

Po zakończeniu procesu transformacji jest możliwość utworzenia nowego kroku i opublikować dane z interfejsu API RateCard jako nowych usług i kursów.

![Rysunek 4. publikowanie danych z interfejsu API RateCard jako nowe usługi i stawki][4]

### <a name="verify-azure-services-and-rates"></a>Sprawdź i stawki za usługi platformy Azure
Po opublikowaniu usługi i kursów, możesz sprawdzić listę importowanych usługi Cloud Cruiser *usług* karty:

![Rysunek 5. Sprawdzanie nowych usług][5]

Na *plany współczynnik* karcie, można sprawdzić o nazwie "AzureSimulation" przy użyciu stawek zaimportowane z interfejsu API RateCard nowego planu taryfowego.

![Rysunek 6 — weryfikowanie nowego planie taryfowym i skojarzone stawki][6]

### <a name="normalize-wap-and-azure-services"></a>Normalizuj WAP i usług platformy Azure
Domyślnie proxy aplikacji sieci Web udostępnia informacje o użyciu, oparta na wykorzystaniu wystąpień obliczeniowych, pamięci i zasobów sieciowych. Cloud Cruiser można zdefiniować na podstawie usług bezpośrednio w alokacji lub mierzonego użytkowania tych zasobów. Na przykład możesz ustawić podstawowa stawka za każdą godzinę użycia procesora CPU lub opłaty za GB pamięci przydzielonej do wystąpienia.

Na przykład w celu porównania kosztów między proxy aplikacji sieci Web i na platformie Azure, musimy agregacji WAP zużycie zasobów, do pakietów, które następnie mogą zostać zmapowane do usług platformy Azure. Tej transformacji można łatwo zaimplementować w skoroszytach:

![Rysunek 7 - przekształcania danych WAP do normalizacji usług][7]

Ostatnim krokiem w skoroszycie jest do publikowania danych w bazie danych Cloud Cruiser. W tym kroku dane użycia jest teraz dołączone do usług, (mapowane do usług platformy Azure) i powiązane z kursów domyślnej do utworzenia opłaty.

Po zakończeniu skoroszytu, można zautomatyzować przetwarzania danych, przez dodanie zadania w ramach harmonogramu zadań i określając częstotliwość i godzinę dla skoroszytu do uruchomienia.

![Rysunek 8 — planowanie skoroszytu][8]

### <a name="create-reports-for-workload-cost-simulation-analysis"></a>Tworzenie raportów analizy kosztów symulacji obciążenia
Po zebraniu użycie i opłaty są ładowane do bazy danych Cloud Cruiser, możemy użyć modułu Cloud Cruiser wgląd do utworzenia obciążenia koszt symulacji, która zawierała.

W celu przedstawienia w tym scenariuszu, utworzyliśmy następującego raportu:

![Porównanie kosztów][9]

Górny wykres przedstawia porównania kosztów przez usługi, porównywanie cena obciążenie dla każdej określonej usługi między proxy aplikacji sieci Web (ciemnoniebieski) i na platformie Azure (jasnoniebieski).

Wykres dolnej przedstawia te same dane, ale według działów. Koszty dla każdego działu do uruchomienia obciążenia na WAP i platformą Azure oraz różnicę między nimi jest wyświetlany na pasku oszczędności (zielony).

## <a name="azure-usage-api"></a>Interfejs API użycia platformy Azure
### <a name="introduction"></a>Wprowadzenie
Firma Microsoft wprowadziła niedawno interfejs API użycia platformy Azure, dzięki czemu subskrybenci programowo ściągać dane użycia, aby uzyskać szczegółowe informacje dotyczące ich użycia. Klienci usługi cloud Cruiser korzystać z zalet bogatszy zestaw danych dostępne za pośrednictwem tego interfejsu API.

Cloud Cruiser za pomocą integracji interfejsu API użycia na kilka sposobów. Poziom szczegółowości (co godzinę informacje o użyciu) i zasobów informacji o metadanych dostępne przez interfejs API udostępnia niezbędne zestawu danych, aby obsługiwać elastyczne modele raportów przewidywanych kosztów i obciążeń zwrotnych. 

W tym samouczku firma Microsoft przedstawia przykład zyska Cloud Cruiser informacji interfejs API użycia. W szczególności firma Microsoft będzie Utwórz grupę zasobów na platformie Azure, skojarzyć tagi dla struktury konta, a następnie opisują proces ściąganie i przetwarzania informacji dotyczących tagu w Cloud Cruiser.

Końcowym celem jest aby można było tworzyć raporty, podobny do następującego i mieć możliwość analizowania kosztów i użycia na podstawie struktury konta wypełniony przez tagi.

![Na rysunku nr 10 - raport o awarii, za pomocą tagów][10]

### <a name="microsoft-azure-tags"></a>Tagi platformy Microsoft Azure
Dane, które są dostępne za pośrednictwem interfejsu API użycia platformy Azure obejmują nie tylko informacji o użyciu, ale również metadane zasobów, takie jak wszystkie znaczniki skojarzone z nią. Tagi umożliwiają łatwe organizowanie zasobów, ale aby obowiązywać, należy upewnić się, że:

* Tagi są prawidłowo stosowane do zasobów na inicjowania obsługi administracyjnej
* Znaczniki są używane prawidłowo na temat procesu przewidywanych kosztów i obciążeń zwrotnych, aby powiązać użycia struktury kont organizacji.

Obu tych wymagań może być trudne, szczególnie w przypadku, gdy jest procesem wykonywanym ręcznie na dostarczenie lub ładowania strony. Błędnie wpisane, nieprawidłowe lub nawet Brak tagów są typowych utrudnień od klientów, gdy przy użyciu tagów oraz tych błędów może utrudnić życia ładowania strony.

Nowy sposób użycia interfejsu API usługi Azure Cloud Cruiser można ściągnąć znakowania informacje o zasobach i za pomocą zaawansowanego narzędzia ETL, o nazwie skoroszyty, należy usunąć te typowe błędy znakowania. Za pomocą przekształcenia przy użyciu wyrażeń regularnych i korelacji danych Cloud Cruiser można zidentyfikować niepoprawnie oznakowane zasoby i zastosować poprawne tagów, zapewniające poprawne skojarzenie zasoby z konsumenta.

Na stronie pobierania Cloud Cruiser automatyzuje proces przewidywanych kosztów i obciążeń zwrotnych i użyć informacji dotyczących tagu powiązać użycie odpowiednich użytkownika (dział, dzielenie, projekt itp.). Ta Automatyzacja zapewnia olbrzymią poprawy jakości obsługi i zagwarantować spójne i inspekcji procesu pobierania.

### <a name="creating-a-resource-group-with-tags-on-microsoft-azure"></a>Tworzenie grupy zasobów za pomocą tagów w systemie Microsoft Azure
Pierwszym krokiem w ramach tego samouczka jest utworzenie grupy zasobów w witrynie Azure portal, następnie utwórz nowe tagi do skojarzenia z zasobami. W tym przykładzie możemy utworzyć następujące tagi: dział, środowisko, właściciel projektu.

Poniższy zrzut ekranu przedstawia przykład grupy zasobów z skojarzonych tagów.

![Rysunek 11 — grupę zasobów za pomocą skojarzonych tagów w witrynie Azure portal][11]

Następnym krokiem jest, aby pobrać informacje z użycia interfejsu API do Cloud Cruiser. Interfejs API użycia udostępnia obecnie odpowiedzi w formacie JSON. Poniżej przedstawiono przykładowe dane pobrane:

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


### <a name="import-data-from-the-usage-api-into-cloud-cruiser"></a>Importowanie danych z interfejsu API użycia do Cloud Cruiser
Cloud Cruiser skoroszytach zautomatyzowany sposób do zbierania i przetwarzania informacji z użycia interfejsu API. Skoroszyt programu ETL (extract-transform-load) umożliwia skonfigurowanie kolekcji, przekształcania i publikowania danych w bazie danych Cloud Cruiser.

Każdy skoroszyt może mieć jednego lub wielu kolekcji. Dzięki temu można skorelować informacje z różnych źródeł w celu uzupełnienia lub rozszerzyć danych użycia. W tym przykładzie tworzymy nowy arkusz w skoroszycie szablonu platformy Azure (*UsageAPI)* i ustawić nowe *kolekcji* do importowania informacji o z użycia interfejsu API.

![Rysunek 3. zaimportowane do arkusza UsageAPI dane użycia interfejsu API][12]

Zwróć uwagę, że ten skoroszyt zawiera już inne arkusze, aby zaimportować usług platformy Azure (*ImportServices*) i przetwarzania informacji o użyciu z interfejsu API rozliczeń (*PublishData*).

Następnie używamy interfejs API użycia do wypełniania *UsageAPI* arkusza, a korelowanie informacji z danych użycia z interfejsu API rozliczeń na *PublishData* arkusza.

### <a name="processing-the-tag-information-from-the-usage-api"></a>Przetwarzanie informacji dotyczących tagu z użycia interfejsu API
Po zaimportowaniu danych do skoroszytu, możemy utworzyć kroki przekształcania w *UsageAPI* arkusza w celu przetwarzania informacji z interfejsu API. Pierwszym krokiem jest procesor "JSON split" umożliwia prowadzenie tagi jedno pole, a następnie utwórz pola dla każdego z nich (dział, projektu, właścicielem i środowiska).

![Rysunek 4. Utwórz nowe pola, aby uzyskać informacje dotyczące tagu][13]

Zwróć uwagę, usłudze "Sieć" Brak informacji dotyczących tagu (żółte pole), ale możemy zweryfikować, że jest częścią tej samej grupie zasobów, analizując *ResourceGroupName* pola. Ponieważ mamy tagi dla innych zasobów w tej grupie zasobów, możemy użyć tych informacji do zastosowania Brak tagów do tego zasobu w dalszej części procesu.

Następnym krokiem jest utworzenie tabeli odnośników kojarzenia informacji z tagów, aby *ResourceGroupName*. Ta tabela odnośników jest używany w następnym kroku wzbogacić dane o zużyciu za pomocą informacji dotyczących tagu.

### <a name="adding-the-tag-information-to-the-consumption-data"></a>Dodawanie informacji dotyczących tagu do danych użycia
Teraz możemy przejść do tematu *PublishData* arkusz, który przetwarza dane użycia z interfejsu API rozliczeń oraz umożliwia dodawanie pól wyodrębnione z tagami. Ten proces odbywa się przez wyszukiwanie w tabeli odnośników, utworzone w poprzednim kroku przy użyciu *ResourceGroupName* jako klucz dla wyszukiwań.

![Rysunek 5 - wypełnianie strukturę z informacjami w wyszukiwaniach][14]

Zwróć uwagę, że pola struktury odpowiednie konto w usłudze "Sieć" zostały zastosowane, rozwiązywania problemu z Brak tagów. Firma Microsoft również wypełnione pola struktury konta dla zasobów innych niż docelową grupę zasobów za pomocą "Other" w celu odróżnienia ich w raportach.

Teraz musimy dodać krok do publikowania danych użycia. W tym kroku odpowiednie kursy dla każdej usługi zdefiniowane w naszej planie taryfowym można zastosować do informacje o użyciu, wynikowy opłat, ładowane do bazy danych.

Najlepsze jest to, wystarczy przejść przez ten proces raz. Po ukończeniu skoroszytu, wystarczy dodać go do harmonogramu i działa co godzinę lub codziennie o zaplanowanej godzinie. Jest to kwestia tworzenia nowych raportów lub dostosowywania istniejących, aby analizować dane w celu uzyskania istotnych informacji korzystania z usługi chmury.

### <a name="next-steps"></a>Następne kroki
* Aby uzyskać szczegółowe instrukcje dotyczące tworzenia Cloud Cruiser skoroszyty i raporty dotyczą Cloud Cruiser online [dokumentacji](http://docs.cloudcruiser.com/) (wymagane zalogowanie prawidłowy).  Aby uzyskać więcej informacji na temat Cloud Cruiser, skontaktuj się z pomocą [ info@cloudcruiser.com ](mailto:info@cloudcruiser.com).
* Zobacz [wgląd w użycie zasobów usługi Microsoft Azure](billing-usage-rate-card-overview.md) omówienie API RateCard i użycie zasobów platformy Azure.
* Zapoznaj się z [dokumentacja interfejsu API REST rozliczeń platformy Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) Aby uzyskać więcej informacji na temat obu interfejsów API, które są częścią zestawu interfejsów API dostarczonych przez usługę Azure Resource Manager.
* Jeśli chcesz od razu rozpocząć korzystanie w prawo do przykładowego kodu, odwiedź nasze Microsoft Azure Billing przykłady kodu interfejsu API w witrynie [przykłady kodu platformy Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

### <a name="learn-more"></a>Dowiedz się więcej
* Aby dowiedzieć się więcej na temat usługi Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) artykułu.

<!--Image references-->

[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "Rysunek 1. Tworzenie nowej kolekcji"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "Rysunek 2. importowanie danych z nowej kolekcji"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "Rysunek 3. kroki przekształcania, aby przetwarzać dane zebrane z interfejsu API RateCard"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "Rysunek 4. publikowanie danych z interfejsu API RateCard jako nowe usługi i stawki"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "Rysunek 5. Sprawdzanie nowych usług"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "Rysunek 6 — weryfikowanie nowego planie taryfowym i skojarzone stawki"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "Rysunek 7 - przekształcania danych WAP do normalizacji usług"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "Rysunek 8 — planowanie skoroszytu"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "Rysunek 9 — przykładowy raport do scenariusza porównania kosztów obciążenia"
[10]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/1_ReportWithTags.png "Na rysunku nr 10 - raport o awarii, za pomocą tagów"
[11]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/2_ResourceGroupsWithTags.png "Rysunek 11 — grupę zasobów za pomocą skojarzonych tagów w witrynie Azure portal"
[12]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/3_ImportIntoUsageAPISheet.png "Rysunek 12 — interfejs API użycia dane zaimportowane do arkusza UsageAPI"
[13]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/4_NewTagField.png "Rysunek 13 — Utwórz nowe pola, aby uzyskać informacje dotyczące tagu"
[14]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/5_PopulateAccountStructure.png "Rysunek 14 - wypełnianie strukturę z informacjami w wyszukiwaniach"
