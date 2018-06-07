---
title: Funkcje żądania danych klienta w usłudze Azure czas serii Insights
description: Podsumowanie funkcji żądania danych klienta.
author: dominicbetts
ms.author: dobett
manager: timlt
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.openlocfilehash: fbbb203d4ad0568a061043317e0444d2551d1aef
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652138"
---
# <a name="summary-of-customer-data-request-features"></a>Podsumowanie funkcji żądania danych klienta

Azure czas serii Insights to usługa w chmurze zarządzanego z magazynu, analizy i wizualizacji składnikami, które ułatwiają pozyskiwania, przechowywać Eksplorowanie i analizowanie miliardów zdarzeń jednocześnie.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Aby wyświetlić, eksportować i usunąć dane osobowe, które mogą być regulowane żądanie podmiotu danych, administrator dzierżawy Azure czas serii Insights można użyć portalu Azure lub interfejsów API REST. Przy użyciu portalu Azure do obsługi żądań podmiotu danych, udostępnia metodę mniej złożona do wykonania tych operacji, które większość użytkowników woli.

## <a name="identifying-customer-data"></a>Identyfikowanie danych klienta

Azure Insights serii czasu uwzględnia dane osobowe były dane skojarzone z administratorów i użytkowników Insights serii czasu. Czas serii Insights przechowuje identyfikator obiektu usługi Azure Active Directory użytkownikom dostępu do środowiska. Portalu Azure Wyświetla adresu e-mail użytkownika, ale te adresy e-mail nie są przechowywane w czasie serii wgląd, ich są dynamicznie wyszukiwane w usłudze Azure Active Directory przy użyciu Identyfikatora obiektu w usłudze Azure Active Directory.

## <a name="deleting-customer-data"></a>Usuwanie danych klienta

Administrator dzierżawy można usunąć dane klienta przy użyciu portalu Azure.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Jednak przed usunięciem danych klienta za pośrednictwem portalu, należy usunąć zasady dostępu użytkownika z środowisko czasu serii wgląd w portalu Azure. Aby uzyskać więcej informacji, zobacz [zezwolić na dostęp do środowiska Insights serii czasu za pomocą portalu Azure](time-series-insights-data-access.md).

Można również wykonać operacji usuwania zasad dostępu przy użyciu interfejsu API REST. Aby uzyskać więcej informacji, zobacz [usunąć zasady dostępu -](https://docs.microsoft.com/rest/api/time-series-insights-management/accesspolicies/delete).

Czas serii Insights jest zintegrowany z bloku zasad w portalu Azure. Zarówno Insights serie czasu i bloku zasady umożliwiają wyświetlanie, eksportowanie i usunąć dane użytkownika przechowywane w ramach usługi. Usuń wszelkie działania podejmowane w bloku zasad w portalu Azure powoduje usunięcie danych użytkownika w czasie serii Insights. Na przykład jeśli użytkownik ma zapisane zapytanie osobistych, zapytania zostanie trwale usunięty ze Eksplorator Insights serii czasu. Jeśli użytkownik ma udostępniony zapisane zapytanie, zapytanie będzie się powtarzać, ale informacje o użytkowniku są trwale usuwane. Poniżej znajdują się instrukcje dotyczące sposobu wykonywania tych zadań.

## <a name="exporting-customer-data"></a>Eksportowanie danych klienta

Podobnie do usuwania danych, administrator dzierżawy wyświetlać i eksportowanie danych przechowywanych w czasie serii wgląd w bloku zasad w portalu Azure.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Jeśli jesteś administratorem dzierżawy, zasady dostępu do danych czasu serii wgląd w środowisku można wyświetlić w portalu Azure. Aby uzyskać więcej informacji, zobacz [zezwolić na dostęp do środowiska Insights serii czasu za pomocą portalu Azure](time-series-insights-data-access.md).

Istnieje również możliwość wykonywania operacji eksportowania na zasady dostępu przy użyciu operacji "Lista przez środowisko" w podanych interfejsu API REST. Aby uzyskać więcej informacji, zobacz [zasad dostęp — przez środowisko listy](https://docs.microsoft.com/rest/api/time-series-insights-management/accesspolicies/listbyenvironment).

## <a name="to-delete-data-stored-within-time-series-insights"></a>Aby usunąć dane przechowywane w czasie serii Insights

Dane osobowe użytkownika może uniemożliwić drodze do magazynu czasu serii wgląd, innego scenariusza użytkownika i danych administrator. Jeśli należy wziąć pod uwagę danych przechowywanych w czasie serii Insights jako dane osobowe, można wyeksportować, a następnie usunąć te dane, wykonując następujące czynności:

**Wyświetlanie i eksportowanie danych**

Aby wyświetlić i wyeksportować dane przechowywane w czasie serii wgląd, należy do wyszukiwania danych. Eksplorator Insights serii czas lub Insights serii czasu kwerendy interfejsów API umożliwia wyświetlanie i eksportowanie danych. Aby wyświetlić i wyeksportować dane za pomocą Eksploratora czasu serii wgląd, najpierw Szukaj w celu znalezienia danych użytkownika w. Po zakończeniu wyszukiwania, kliknij prawym przyciskiem myszy wykres i wybierz **Eksploruj zdarzenia**. Siatka zdarzeń pojawi się i przedstawia opcje eksportowania danych jako woluminu CSV i JSON.

Aby uzyskać więcej informacji, zobacz [Eksplorator Azure czas serii Insights](time-series-insights-explorer.md).

**Usuwanie danych**

Obecnie Insights serii czasu nie obsługuje usuwania szczegółowych danych. Jednak Insights serii czasu pozwala, aby usunąć dane klienta przechowywane w ramach Insights serii czas przez skonfigurowanie zasad przechowywania. Okres przechowywania dla całego środowiska czasu serii wgląd do dowolnej liczby dni do potrzeb usunięcia można dostosować.

Aby uzyskać więcej informacji, zobacz [konfigurowania przechowywania w czasie serii Insights](time-series-insights-how-to-configure-retention.md).