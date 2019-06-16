---
title: Dane klienta żądania funkcji w usłudze Azure Time Series Insights | Dokumentacja firmy Microsoft
description: Podsumowanie funkcji żądania danych klienta usługi Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.date: 05/08/2019
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.custom: seodec18
ms.openlocfilehash: f99a54f5623100e057e0c02249f5886f46656a21
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66355930"
---
# <a name="summary-of-customer-data-request-features"></a>Podsumowanie funkcji żądania danych klienta

Usługa Azure Time Series Insights to usługa zarządzana usługa w chmurze zawierającą składniki do magazynu, analiz i wizualizacji, które ułatwiają do pozyskiwania, przechowywać, eksplorować i analizować miliardy zdarzeń jednocześnie.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Aby wyświetlić, eksportowania i usuwania danych osobowych, które mogą być przedmiotem żądania podmiotu danych, administrator dzierżawy usługi Azure Time Series Insights można użyć witryny Azure portal lub interfejsów API REST. W witrynie Azure portal na żądania podmiotów danych usługi, udostępnia metodę mniej skomplikowany wykonywać te operacje, które wolą większość użytkowników.

## <a name="identifying-customer-data"></a>Identyfikowanie danych klienta

Usługa Azure Time Series Insights uwzględnia danych osobowych dane skojarzone z Administratorzy i użytkownicy usługi Time Series Insights. Usługa Time Series Insights przechowuje identyfikator obiektu usługi Azure Active Directory użytkownikom dostępu do środowiska. Witryny Azure portal Wyświetla adresy e-mail użytkowników, ale te adresy e-mail nie są przechowywane w ramach usługi Time Series Insights, ich są dynamicznie odszukać za pomocą usługi Azure Active Directory — identyfikator obiektu w usłudze Azure Active Directory.

## <a name="deleting-customer-data"></a>Usuwanie danych klienta

Administrator dzierżawy można usunąć danych klientów przy użyciu witryny Azure portal.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Jednak przed usunięciem danych klientów za pośrednictwem portalu użytkownika zasad dostępu należy usunąć ze środowiska usługi Time Series Insights w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [zezwolić na dostęp do środowiska usługi Time Series Insights przy użyciu witryny Azure portal](time-series-insights-data-access.md).

Można również wykonywać operacje usuwania zasad dostępu przy użyciu interfejsu API REST. Aby uzyskać więcej informacji, zobacz [usuwania zasad dostępu —](/rest/api/time-series-insights/management/accesspolicies/delete).

Usługa Time Series Insights jest zintegrowana z bloku zasady w witrynie Azure portal. Zarówno usługa Time Series Insights, jak i w bloku zasad umożliwiają wyświetlanie, eksportować i usuwać dane użytkownika przechowywane w ramach usługi. Usuń dowolny działania podejmowane w bloku zasad w portalu platformy Azure powoduje usunięcie danych użytkownika w ramach usługi Time Series Insights. Na przykład jeśli użytkownik ma zapisane zapytanie osobistego, to zapytanie zostaną trwale usunięte z Eksploratora usługi Time Series Insights. Jeśli użytkownik ma zapisanego zapytania udostępnione, zapytanie będzie się powtarzać, ale informacje o użytkowniku są trwale usuwane. Następująca uwaga zawiera instrukcje dotyczące sposobu wykonywania tych zadań.

## <a name="exporting-customer-data"></a>Eksportowanie danych klienta

Podobnie do usuwania danych, administrator dzierżawy wyświetlić i wyeksportować dane przechowywane w usłudze Time Series Insights z poziomu bloku zasady w witrynie Azure portal.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Jeśli jesteś administratorem dzierżawy, możesz wyświetlić zasady dostępu do danych w środowisku usługi Time Series Insights w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [zezwolić na dostęp do środowiska usługi Time Series Insights przy użyciu witryny Azure portal](time-series-insights-data-access.md).

Istnieje również możliwość wykonywania operacji eksportowania zasadami dostępu przy użyciu operacji "list przez środowisko" w podanych interfejsu API REST. Aby uzyskać więcej informacji, zobacz [zasady dostępu — środowisko przez listy](/rest/api/time-series-insights/management/accesspolicies/listbyenvironment).

## <a name="to-delete-data-stored-within-time-series-insights"></a>Aby usunąć dane przechowywane w usłudze Time Series Insights

Dane osobowe może uniemożliwić drodze do magazynu usługi Time Series Insights, inny scenariusz z danych administratora i użytkowników. Należy wziąć pod uwagę dane przechowywane w usługi Time Series Insights, jak dane osobowe, można eksportować i usuwać te dane wykonując następujące czynności:

**Wyświetlanie i eksportowanie danych**

Aby wyświetlić i wyeksportować dane przechowywane w usłudze Time Series Insights, należy wyszukać te dane. Aby wyświetlić i wyeksportować dane, można użyć Eksploratora usługi Time Series Insights lub interfejsami API zapytań usługi Time Series Insights. Aby wyświetlić i wyeksportować dane za pomocą Eksploratora usługi Time Series Insights, najpierw wyszukać dane użytkownika w danym. Po zakończeniu wyszukiwania, kliknij prawym przyciskiem myszy wykres i wybierz pozycję **Eksploruj zdarzenia**. Siatka zdarzeń pojawi się i przedstawia opcje eksportowania danych jako CSV i JSON.

Aby uzyskać więcej informacji, zobacz [Eksploratora usługi Azure Time Series Insights](time-series-insights-explorer.md).

**Usuwanie danych**

Obecnie usługa Time Series Insights nie obsługuje usuwania szczegółowych danych. Jednak usługa Time Series Insights zapewnia możliwość usunięcia danych klienta przechowywanych w ramach usługi Time Series Insights, konfigurując zasady przechowywania. Możesz dostosować okres przechowywania całego środowiska usługi Time Series Insights do dowolnej liczby dni do obsługi wymagań dotyczących usuwania.

Aby uzyskać więcej informacji, zobacz [konfigurowania przechowywania w usłudze Time Series Insights](time-series-insights-how-to-configure-retention.md).

## <a name="next-steps"></a>Kolejne kroki

* Przeczytaj więcej na temat [udzielanie dostępu do danych do środowiska usługi TSI](./time-series-insights-data-access.md).

* Widok [Eksploratora usługi Azure Time Series Insights](time-series-insights-explorer.md).

* Dowiedz się więcej o [konfigurowania przechowywania w usłudze Time Series Insights](time-series-insights-how-to-configure-retention.md).