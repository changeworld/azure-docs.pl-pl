---
title: Określanie opcji publikowania w portalu Azure Marketplace | Azure
description: W tym artykule opisano kryteria kwalifikacyjne i wymagania dotyczące publikowania partnerów próbujących zrozumieć sposób publikowania aplikacji w portalu Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: jm-aditi-ms
manager: pabutler
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/13/2018
ms.author: ellacroi
ms.openlocfilehash: 967d33cb7a9eb3ec922f9d85cfc2581b85bce537
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825902"
---
# <a name="determine-your-publishing-option"></a>Określanie opcji publikowania
Opcja publikowania wybrana dla oferty odnosi się bezpośrednio zarówno do wymagań dotyczących uprawnień, jak i korzyści z witryny Marketplace GTM. Co ważniejsze, wybór opcji publikowania i typu oferty definiuje sposób, w jaki użytkownicy będą korzystać z oferty portalu Marketplace.

Aby skonfigurować swoją ofertę, musisz zrozumieć następujące kluczowe koncepcje dotyczące rynku: Opcje publikowania, typy ofert i konfigurację oraz wywołania do akcji, które będą określać, jak i gdzie oferta zostanie przedstawiona w witrynie portalu Marketplace.

![](./media/marketplace-publishers-guide/storefronts_options_table.png)


W tym artykule dowiesz się, jak to zrobić...
<ul><li>    Jak określić odpowiedni sklep dla swojego rozwiązania </ul></li>
<ul><li>    Które opcje publikowania i wywołania do akcji są dostępne w każdej sklepie </ul></li>
<ul><li>    Jakie typy ofert są dostępne dla każdej opcji publikowania </ul></li>


## <a name="selecting-a-storefront-publishing-option-and-offer-type-for-your-solution"></a>Wybieranie witryny sklepu, opcji publikowania i typu oferty dla Twojego rozwiązania

Przed wybraniem opcji publikowania należy zapoznać się z wymaganiami dotyczącymi uprawnień witryny Marketplace dotyczącymi rozwiązań, aplikacji i usług w witrynie sieci Web:

Aplikacje w **portalu Azure Marketplace** są technicznymi rozwiązaniami "konstrukcyj-Block" opartymi na platformie Azure i przeznaczonymi dla odbiorców IT lub deweloperów. Usługi doradcze w witrynie Azure Marketplace to profesjonalne oferty usług, które ułatwiają klientom rozpoczęcie pracy z platformą Azure lub przyspieszenie korzystania z niej.

Aplikacje **AppSource** są rozwiązaniami biznesowymi, które mogą być wbudowane w platformę Azure lub wbudowaną dla: Dynamics 365, Office 365, Power BI lub aplikacji zaawansowanych. Usługi doradcze AppSource są ofertami usług profesjonalnych, które ułatwiają klientom rozpoczęcie pracy z systemem Dynamics Power BI 365 lub przyspieszeniem korzystania z niego.


## <a name="understand-storefront-selection"></a>Omówienie wyboru witryny sklepu

Witryna sklepu, w której zostanie przedstawiona oferta, Azure Marketplace i/lub AppSource, zostanie automatycznie określona przez szczegółowe informacje o ofercie oraz odbiorców docelowych, a także kategorie i branże wybrane przez użytkownika podczas tworzenia oferty. 

>[!Note]
>"Tworzenie krzyżowe" (tylko w przypadku aplikacji SaaS): gdy lista lub oferta oparta na wersji próbnej spełnia kryteria dla odbiorców dla użytkowników technicznych i firmowych, oferta zostanie wyświetlona w obu witrynach. Dowiedz się więcej na temat opcji publikowania poniżej.

## <a name="choose-a-publishing-option"></a>Wybierz opcję publikowania

Dostępne opcje publikowania oferują zróżnicowane zaangażowanie klientów, zapewniając dostęp do udostępniania potencjalnych klientów i [komercyjnych korzyści z witryny Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits).  Zanotuj wywołania do akcji, które odpowiadają opcji publikowania:

| **Opcja publikowania**    | **Opis**  |
| :------------------- | :-------------------|
| **Staw** | Prosta lista aplikacji lub usługi, która umożliwia użytkownikowi portalu Marketplace zażądanie połączenia z klientem za pośrednictwem wywołania do akcji **kontaktowanie się ze mną** . |
| **Wersja próbna** | Skorzystaj z portalu Marketplace, aby zwiększyć możliwości odnajdywania i automatyzowania aprowizacji wersji próbnej rozwiązania, dzięki czemu potencjalni użytkownicy mogą korzystać z usługi SaaS, IaaS lub firmy Microsoft bez opłat przez ograniczony czas przed ich zakupem. Opcja wywołania do akcji używana dla opcji publikowania wersji próbnej: **bezpłatna wersja próbna** lub **testowa**. |
|**BYOL**  |Skorzystaj z portalu Marketplace, aby zwiększyć możliwości odnajdywania i automatyzowania aprowizacji rozwiązania oraz dokończyć transakcję finansowo osobno. Typy ofert BYOL doskonale sprawdzają się w przypadku migracji do chmury. Wywołanie akcji to **teraz**.
| **Transact** | Aplikacja jest uruchamiana na platformie Azure i może być inicjowana jako zasób bezpośrednio do subskrypcji platformy Azure klienta, gdy klient wybierze wywołanie " **Pobierz teraz** do działania". Opłaty za licencję na oprogramowanie można opcjonalnie zakupić i rozliczać za pośrednictwem wybranego instrumentu płatniczego i postanowień, a także wybrać opcję oferowania ograniczonego czasu dostępu do **bezpłatnej wersji próbnej oprogramowania** (dostępnego w witrynie Azure Marketplace). |

>[!Note]
>W przypadku korzystania z opcji publikacji Transact należy poznać zagadnienia dotyczące cen, rozliczeń, fakturowania i wypłaty przed wybraniem typu oferty i utworzeniem oferty. [Aby dowiedzieć się więcej, zapoznaj się z artykułami dotyczącymi rozliczeń i zagadnień komercyjnych](./marketplace-commercial-transaction-capabilities-and-considerations.md).

## <a name="next-steps"></a>Następne kroki

*   Po wybraniu opcji publikowania możesz [wybrać typ oferty](./publisher-guide-by-offer-type.md) , który będzie używany do prezentowania oferty.
*   Zapoznaj się z wymaganiami dotyczącymi kwalifikacji w sekcji Opcje publikowania według typu oferty, aby zakończyć wybór i konfigurację oferty.
*   Zapoznaj się z wzorcem publikowania według witryny sklepu, aby zapoznać się z przykładami dotyczącymi sposobu mapowania rozwiązania na typ oferty i konfigurację.



