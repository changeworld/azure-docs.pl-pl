---
title: Opis rozliczeń usługi Azure cdn | Dokumenty firmy Microsoft
description: W tym często zadawanych pytaniach opisano, jak działa rozliczenia usługi Azure CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2019
ms.author: allensu
ms.openlocfilehash: d3a2dfba98f83d34c3e83ec865e3b692f7dbacd2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81254227"
---
# <a name="understanding-azure-cdn-billing"></a>Omówienie rozliczania za usługę Azure CDN

W tym często zadawanym faq opisano strukturę rozliczeń zawartości hostowanych przez usługę Azure Content Delivery Network (CDN).

## <a name="what-is-a-billing-region"></a>Co to jest region rozliczeń?
Region rozliczeń to obszar geograficzny używany do określenia, jaka stawka jest naliczana za dostarczanie obiektów z usługi Azure CDN. Bieżące strefy rozliczeniowe i ich regiony są następujące:

- Strefa 1: Ameryka Północna, Europa, Bliski Wschód i Afryka

- Strefa 2: Azja i Pacyfik (w tym Japonia)

- Strefa 3: Ameryka Południowa

- Strefa 4: Australia i Nowa Zelandia

- Strefa 5: Indie

Aby uzyskać informacje o regionach punktu obecności (POP), zobacz [Lokalizacje pop usługi Azure CDN według regionów](https://docs.microsoft.com/azure/cdn/cdn-pop-locations). Na przykład pop znajduje się w Meksyku jest w regionie Ameryki Północnej i dlatego jest uwzględniony w strefie 1. 

Aby uzyskać informacje na temat cen usługi Azure CDN, zobacz [Cennik sieci dostarczania zawartości](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="how-are-delivery-charges-calculated-by-region"></a>W jaki sposób opłaty za dostawę są obliczane według regionu?
Region rozliczeń usługi Azure CDN jest oparty na lokalizacji serwera źródłowego dostarczającego zawartość do użytkownika końcowego. Miejsce docelowe (lokalizacja fizyczna) klienta nie jest uważane za region rozliczeń.

Jeśli na przykład użytkownik znajdujący się w Meksyku wystawia żądanie, a to żądanie jest obsługiwane przez serwer znajdujący się w amerykańskim połączeniu adresów POP z powodu komunikacji równorzędnej lub warunków drogowych, regionem rozliczeniowym będą Stany Zjednoczone.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>Co to jest transakcja usługi Azure CDN podlegania rozliczane?
Każde żądanie HTTP(S), które kończy się w sieci CDN, jest zdarzeniem podlegacym rozliczaniu, które obejmuje wszystkie typy odpowiedzi: sukces, niepowodzenie lub inne. Jednak różne odpowiedzi mogą generować różne kwoty ruchu. Na przykład *304 Nie zmodyfikowane* i inne odpowiedzi tylko do nagłówka generują niewielki ruch, ponieważ są one odpowiedzią małego nagłówka; podobnie, odpowiedzi na błędy (na przykład *404 Nie znaleziono)* są rozliczane, ale ponoszą niewielki koszt ze względu na niewielki ładunek odpowiedzi.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Jakie inne koszty platformy Azure są skojarzone z korzystaniem z usługi Azure CDN?
Korzystanie z usługi Azure CDN wiąże się również z pewnymi opłatami za użycie usług używanych jako źródło dla obiektów. Koszty te stanowią zazwyczaj niewielki ułamek całkowitego kosztu użycia sieci CDN.

Jeśli używasz magazynu obiektów Blob platformy Azure jako źródła zawartości, poniesiesz również następujące opłaty za magazyn dla wypełnień pamięci podręcznej:

- Rzeczywiste GB używane: rzeczywiste przechowywanie obiektów źródłowych.

- Transakcje: W razie potrzeby, aby wypełnić pamięć podręczną.

- Transfery w GB: ilość danych przesłanych do wypełnienia pamięci podręcznych sieci CDN.

> [!NOTE]
> Od października 2019 r. jeśli korzystasz z usługi Azure CDN firmy Microsoft, koszt transferu danych z aplikacji Origins hostowanych na platformie Azure do punktu wyjścia usługi CDN jest bezpłatny. Usługa Azure CDN firmy Verizon i azure CDN firmy Akamai podlega stawkom opisanym poniżej.

Aby uzyskać więcej informacji na temat rozliczeń usługi Azure Storage, zobacz [Opis rozliczeń usługi Azure Storage — przepustowość, transakcje i pojemność.](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/)

Jeśli korzystasz z *usługi hostowanego,* opłaty zostaną naliczone w następujący sposób:

- Czas obliczeń platformy Azure: wystąpienia obliczeń, które działają jako źródło.

- Transfer obliczeń platformy Azure: transfery danych z wystąpień obliczeniowych w celu wypełnienia pamięci podręcznych usługi Azure CDN.

Jeśli klient używa żądań zakresu bajtów (niezależnie od usługi pochodzenia), zastosowanie mają następujące zagadnienia:

- *Żądanie zakresu bajtów* jest transakcją podlegania rozliczaniu w sieci CDN. Gdy klient wystawia żądanie zakresu bajtów, to żądanie dotyczy podzbioru (zakresu) obiektu. Sieć CDN odpowiada tylko częścią żądanej zawartości. Ta częściowa odpowiedź jest transakcją podlegalną do rozliczenia, a kwota transferu jest ograniczona do rozmiaru odpowiedzi zakresu (plus nagłówki).

- Gdy żądanie dociera tylko do części obiektu (przez określenie nagłówka zakresu bajtów), sieć CDN może pobrać cały obiekt do swojej pamięci podręcznej. W rezultacie, mimo że transakcja podlegające rozliczaniu z sieci CDN jest dla częściowej odpowiedzi, transakcji rozliczane od źródła może obejmować pełny rozmiar obiektu.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>Ile działania transferu występuje do obsługi pamięci podręcznej?
Za każdym razem, gdy punkt obecności cdn musi wypełnić swoją pamięć podręczną, żąda do źródła dla obiektu buforowanego. W rezultacie pochodzenie ponosi rozliczalną transakcję na każdej miss pamięci podręcznej. Liczba pominiętych pamięci podręcznej zależy od wielu czynników:

- Jak buforowalna zawartość jest: Jeśli zawartość ma wysoki czas wygaśnięcia (czas wygaśnięcia)/wygaśnięcia wartości i jest często dostępny, więc pozostaje popularny w pamięci podręcznej, a następnie zdecydowana większość obciążenia jest obsługiwana przez sieć CDN. Typowy dobry współczynnik trafień pamięci podręcznej wynosi znacznie ponad 90%, co oznacza, że mniej niż 10% żądań klientów musi powrócić do źródła, aby uzyskać pominięcie pamięci podręcznej lub odświeżenie obiektu.

- Ile węzłów trzeba załadować obiekt: Za każdym razem, gdy węzeł ładuje obiekt od początku układu współrzędnych, nalicza transakcję podlegającego rozliczaniu. W rezultacie więcej globalnej zawartości (dostępnej z większej liczby węzłów) powoduje więcej transakcji podlegania rozliczaniu.

- Wpływ TTL: Wyższy czas wygaśnięcia obiektu oznacza, że musi być pobierany ze źródła rzadziej. Oznacza to również, że klienci, takich jak przeglądarki, można buforować obiekt dłużej, co może zmniejszyć transakcje do sieci CDN.

## <a name="which-origin-services-are-eligible-for-free-data-transfer-with-azure-cdn-from-microsoft"></a>Które usługi pochodzenia kwalifikują się do bezpłatnego transferu danych za pomocą usługi Azure CDN od firmy Microsoft? 
Jeśli używasz jednej z następujących usług platformy Azure jako źródła usługi CDN, nie zostanie naliczona opłata z transferu danych z pochodzenia do punktami pocp usługi CDN. 

- Azure Storage
- Usługi multimediów platformy Azure
- Azure Virtual Machines
- Virtual Network
- Moduł równoważenia obciążenia
- Application Gateway
- System DNS platformy Azure
- ExpressRoute
- VPN Gateway
- Traffic Manager
- Network Watcher
- Azure Firewall
- Azure Front Door Service
- Azure Bastion
- Usługa aplikacji platformy Azure
- Azure Functions
- Azure Data Factory
- Usługa Azure API Management
- Azure Batch 
- Azure Data Explorer
- HDInsight
- Azure Cosmos DB
- Azure Data Lake Store
- Azure Machine Learning 
- Baza danych Azure SQL Database
- Azure Cache for Redis

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Jak zarządzać kosztami najskuteczniej?
Ustaw najdłuższy możliwy czas wygaśnięcia zawartości. 
