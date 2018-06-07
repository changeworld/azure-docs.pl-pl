---
title: Funkcje żądania danych klienta
author: dominicbetts
ms.author: dobett
manager: timlt
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: d6355926c8fac62b01c36d28265842b1233ce213
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629324"
---
# <a name="summary-of-customer-data-request-features"></a>Podsumowanie funkcji żądania danych klienta

Usługa inicjowania obsługi urządzeń Centrum IoT Azure jest oparte na interfejsu API REST usługi w chmurze celem przedsiębiorstwa umożliwiający bezproblemowe, automatycznej bezobsługową inicjowania obsługi urządzeń Centrum IoT Azure z zabezpieczeniami, który zaczyna się od urządzenia i kończy się wraz z chmury.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Poszczególne urządzenia są przypisywane rejestracji identyfikator i identyfikator urządzenia przez administratora dzierżawy. Dane z i o tych urządzeń są oparte na tych identyfikatorów. Firma Microsoft zachowuje żadnych informacji i nie ma dostępu do danych, która umożliwiałaby korelacji te urządzenia do osoby.

Wiele urządzeń zarządzanych w usłudze udostępniania urządzenia nie są urządzeń osobistych, na przykład office robota Termostat lub do ustawień fabrycznych. Klienci mogą jednak wziąć pod uwagę niektórych urządzeń osobistych i według własnego uznania może utrzymać własnych zasobów lub metody, które powiązanie urządzeń do osób śledzenia spisu. Usługa inicjowania obsługi urządzeń zarządza i przechowuje wszystkie dane skojarzone z urządzenia, tak jakby był on dane osobowe.

Administratorzy dzierżawy może używać portalu Azure lub interfejsów API REST usługi do spełnienia żądania informacji przez eksportowanie lub usuwanie danych skojarzonych z Identyfikatorem urządzenia lub identyfikator rejestracji.

> [!NOTE]
> Urządzenia, które zostały udostępnione w Centrum IoT Azure za pośrednictwem usługi inicjowania obsługi urządzeń ma dodatkowe dane przechowywane w usłudze Azure IoT Hub. Zobacz [dokumentacji Centrum IoT Azure](../iot-hub/iot-hub-customer-data-requests.md) w celu wykonania pełnej żądania dla danego urządzenia.

## <a name="deleting-customer-data"></a>Usuwanie danych klienta

Usługi inicjowania obsługi urządzeń są przechowywane rekordy rejestracji i rejestracji. Rejestracji zawierają informacje dotyczące urządzeń, które mogą być udostępniane i pokazać rekordy rejestracji, które urządzenia już przeszły procesu inicjowania obsługi administracyjnej.

Administratorzy dzierżawy mogą usunąć rejestracji z portalu Azure i spowoduje to usunięcie wszystkich rekordów skojarzone rejestracji.

Aby uzyskać więcej informacji, zobacz [jak zarządzać rejestracji urządzeń](how-to-manage-enrollments.md).

Istnieje również możliwość wykonywania operacji usuwania dla rejestracji i rejestracji rekordów przy użyciu interfejsów API REST:

* Aby usunąć informacje o rejestracji na jednym urządzeniu, można użyć [rejestracji urządzeń — Usuń](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollment/delete).
* Aby usunąć informacje dotyczące rejestracji dla grupy urządzeń, można użyć [grupy rejestracji urządzeń — Usuń](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollmentgroup/delete).
* Aby usunąć informacje o urządzeniach, które zostały udostępnione, można użyć [stan rejestracji — Usuń stan rejestracji](https://docs.microsoft.com/rest/api/iot-dps/registrationstate/deleteregistrationstate).

## <a name="exporting-customer-data"></a>Eksportowanie danych klienta

Usługi inicjowania obsługi urządzeń są przechowywane rekordy rejestracji i rejestracji. Rejestracji zawierają informacje dotyczące urządzeń, które mogą być udostępniane i pokazać rekordy rejestracji, które urządzenia już przeszły procesu inicjowania obsługi administracyjnej.

Administratorzy dzierżawy można wyświetlać rekordy rejestracji za pomocą portalu Azure i rejestracji i wyeksportować je przy użyciu kopiowania i wklejania.

Aby uzyskać więcej informacji na temat zarządzania rejestracji, zobacz [jak zarządzać rejestracji urządzeń](how-to-manage-enrollments.md).

Istnieje także możliwość wykonywania operacji eksportowania do rejestracji i rejestracji rekordów przy użyciu interfejsów API REST:

* Aby wyeksportować informacje o rejestracji na jednym urządzeniu, można użyć [rejestracji urządzeń - Get](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollment/get).
* Aby wyeksportować informacje o rejestracji dla grupy urządzeń, można użyć [grupy rejestracji urządzeń - Get](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollmentgroup/get).
* Aby wyeksportować informacje o urządzeniach, które już zostały udostępnione, można użyć [stan rejestracji — stan rejestracji Get](https://docs.microsoft.com/rest/api/iot-dps/registrationstate/getregistrationstate).

> [!NOTE]
> Korzystając z usług dla przedsiębiorstw firmy Microsoft, Microsoft generuje niektórych informacji, znane jako dzienniki generowane przez system. Niektóre usługi inicjowania obsługi urządzeń dzienniki generowanych przez system nie są dostępne lub można eksportować przez administratorów dzierżawy. Te dzienniki stanowią faktyczne akcje dokonywanych w ramach usługi i danych diagnostycznych dotyczących poszczególnych urządzeń.

## <a name="links-to-additional-documentation"></a>Linki do dodatkowej dokumentacji

Pełną dokumentację dla interfejsów API usługi inicjowania obsługi urządzeń znajduje się pod adresem [ https://docs.microsoft.com/rest/api/iot-dps ](https://docs.microsoft.com/rest/api/iot-dps).

Centrum IoT Azure [danych klienta żądania funkcji](../iot-hub/iot-hub-customer-data-requests.md).