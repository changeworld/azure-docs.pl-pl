---
title: Opis usługi Azure CDN rozliczeń | Dokumentacja firmy Microsoft
description: Często zadawane pytania opisano, jak działa usługa Azure CDN rozliczeń.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: v-deasim
ms.openlocfilehash: 218c493c772dc8fd212efaf60a0599fa2e896411
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="understanding-azure-cdn-billing"></a>Opis usługi Azure CDN rozliczeń

Często zadawane pytania opisuje strukturę rozliczeń dla zawartości hostowanej przez usługi Azure Content Delivery Network (CDN).

## <a name="what-is-a-billing-region"></a>Co to jest obszarem rozliczeniowym?
Rozliczeń region jest obszar geograficzny używany do określenia, jakie szybkość rozliczany w celu dostarczania obiektów z usługi Azure CDN. Bieżącej strefy rozliczeń i ich regiony są następujące:

- Strefa 1: Ameryka Północna, Europy, Bliskiego Wschodu i Afryki

- Strefa 2: Azja i Pacyfik (włącznie z Japonią)

- Strefa 3: Ameryka Południowa

- Strefa 4: Australia i Nowa Zelandia

- Strefa 5: Indie

Uzyskać informacji o regionach punktu z obecności (POP), zobacz [lokalizacje POP w sieci CDN Azure według regionu](https://docs.microsoft.com/azure/cdn/cdn-pop-locations). Na przykład punkt obecności znajdujący się w Meksyk znajduje się w regionie Ameryki Północnej i w związku z tym znajduje się w strefie 1. 

Aby uzyskać informacje o cenach usługi Azure CDN, zobacz [cennik Content Delivery Network](https://azure.microsoft.com/is-is/pricing/details/cdn/).

## <a name="how-are-delivery-charges-calculated-by-region"></a>Jak dostarczania opłaty są obliczane według regionu
Region rozliczeń usługi Azure CDN jest oparta na lokalizacji serwera źródłowego, dostarczanie zawartości dla użytkownika końcowego. Miejsce docelowe (fizyczną lokalizację) klienta nie jest uważana za rozliczeń regionu.

Na przykład jeśli użytkownik znajduje się w Meksyk generuje żądanie i to żądanie jest obsługiwany przez serwer znajduje się w obecności Stanów Zjednoczonych, ze względu na warunki równorzędna lub ruchu, rozliczeń region będzie Stanów Zjednoczonych.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>Co to jest rozliczana transakcji usługi Azure CDN?
Każde żądanie HTTP (S), która kończy się w sieci CDN jest rozliczana zdarzenie, które zawiera wszystkie typy odpowiedzi: Sukces, Niepowodzenie lub innych. Jednak różnych odpowiedzi może generują innego ruchu sieciowego. Na przykład *304 niezmodyfikowane* i innych odpowiedzi tylko nagłówki generować małego ruch, ponieważ są one małą nagłówka odpowiedzi; podobnie odpowiedzi na błędy (na przykład *404 — Nie znaleziono*) są rozliczeń ale pociągnąć za sobą małych koszt z powodu ładunku niewielki rozmiar odpowiedzi.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Jakie platformy Azure koszty są skojarzone z użyciem usługi Azure CDN?
Za pomocą usługi Azure CDN ponosi niektórych obciążeń użycia usługi używane jako punkt początkowy dla obiektów. Koszty te są zwykle ułamku całkowity koszt użytkowania CDN.

Jeśli używasz magazynu obiektów Blob platformy Azure jako punkt początkowy dla zawartości, naliczane są również następujące opłaty za magazyn dla wypełnienia pamięci podręcznej:

- Rzeczywiste GB użycia: przechowywania obiektów źródła.

- Transfery w GB: ilość danych przetransferowanych do wypełnienia pamięci podręcznych CDN.

- Transakcje: w razie potrzeby w celu wypełnienia pamięci podręcznej.

Aby uzyskać więcej informacji dotyczących rozliczeń usługi Azure Storage, zobacz [opis Azure magazynu rozliczeń — przepustowość, transakcje i pojemności](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

Jeśli używasz *dostawy usług hostowanych*, spowoduje naliczenie opłat w następujący sposób:

- Godziny obliczeń platformy Azure: wystąpienia obliczeniowe, które działają jako punkt początkowy.

- Transfer obliczeń platformy Azure: transferu danych z wystąpienia obliczeniowe celu wypełnienia pamięci podręcznej Azure CDN.

Jeśli klient używa żądania zakresu bajtów (niezależnie od źródła usługa), zastosuj następujące kwestie:

- A *żądania zakresu bajtów* jest rozliczana transakcji w sieci CDN. Gdy klient wysyła żądanie zakresu bajtów, to żądanie jest dla obiekt podzbioru (zakres). Usługa CDN odpowiada z częściowa część zawartości, która jest wymagane. Ta częściowa odpowiedź jest rozliczana transakcji i wielkość transferu jest ograniczony do rozmiaru zakresu odpowiedzi (łącznie z nagłówkami).

- Gdy żądanie dociera tylko część obiektu (przez określenie nagłówka zakresu bajtów), sieci CDN może pobrać całego obiektu w pamięci podręcznej. W związku z tym mimo że jest rozliczana transakcji z sieci CDN dla częściowej odpowiedzi, rozliczeniowy transakcji ze źródła może obejmować pełnego rozmiaru obiektu.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>Ile działanie transferu występuje do obsługi pamięci podręcznej?
Zawsze POP w sieci CDN konieczne wypełnienie pamięci podręcznej go wysyła żądanie do źródła dla obiektu pamięci podręcznej. W związku z tym źródła wiąże rozliczeniowy transakcji na każdym Chybienie pamięci podręcznej. Liczba chybień pamięci podręcznej zależy od wielu czynników:

- Jak buforowalnej zawartości jest: Jeśli zawartość ma wysoką czas wygaśnięcia (time-to-live) / wygaśnięcia wartości i jest modyfikowane i często używane, pozostaje on objęty popularnych w pamięci podręcznej, a następnie większość obciążenia jest obsługiwane przez usługę CDN. Typowy dobrej Stosunek trafień w pamięci podręcznej jest dobrze ponad 90%, co oznacza, że mniej niż 10% żądań klientów powrócić do źródła, Odśwież albo Chybienie pamięci podręcznej lub obiektu.

- Liczbę węzłów należy załadować obiekt: każdym węzłem ładuje obiekt ze źródła, generuje rozliczeniowy transakcji. W związku z tym globalnych zawartości (dostępnych z większą liczbę węzłów) powoduje więcej rozliczeniowy transakcji.

- Wpływ TTL: oznacza wyższy TTL dla obiekt musi zostać pobrane ze źródła rzadziej. Oznacza to również, że klientów, takich jak przeglądarki, można buforować obiektu dłużej, co może zmniejszyć transakcji do sieci CDN.

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Jak najbardziej efektywne zarządzanie Moje kosztów?
Ustaw najdłuższym TTL możliwe od treści. 
