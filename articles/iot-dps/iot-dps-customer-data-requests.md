---
title: Funkcje żądania danych klienta
author: dominicbetts
ms.author: dobett
manager: timlt
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 5dd027c886e8102e77ddefe93817daee0e1ec29b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626438"
---
# <a name="summary-of-customer-data-request-features"></a>Podsumowanie funkcji żądania danych klienta

Azure IoT Hub Device Provisioning Service jest oparte na interfejsie API REST usługi w chmurze przeznaczona dla klientów korporacyjnych umożliwiającą bezproblemowe i automatyczne bezdotykową automatyczną aprowizację urządzeń w usłudze Azure IoT Hub przy użyciu zabezpieczeń, który rozpoczyna się na urządzeniu i kończąca na chmurze.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Poszczególne urządzenia są przypisane rejestracji identyfikator i Identyfikatora urządzenia przez administratora dzierżawy. Dane z i dotyczących tych urządzeń jest oparty na tych identyfikatorów. Firma Microsoft zachowuje żadnych informacji i nie ma dostępu do danych, które pozwoliłoby korelacji tych urządzeń, z określoną osobą.

Wiele urządzeń zarządzanych w usłudze Device Provisioning nie są urządzeń osobistych, na przykład robota Termostat lub fabryki pakietu office. Klienci mogą jednak wziąć pod uwagę niektóre urządzenia jako osobiste i według własnego uznania mogą utrzymać ich własnych zasobów magazynu śledzenia metody, które powiązanie urządzeń fizycznych. Usługi Device Provisioning Service zarządza i przechowuje wszystkie dane skojarzone z urządzeń, jak w przypadku danych osobowych.

Administratorzy dzierżawy za pomocą witryny Azure portal lub interfejsów API REST usługi można wykonać żądania informacji przez wyeksportowanie lub usuwania danych skojarzonych z Identyfikatorem urządzenia lub identyfikator rejestracji.

> [!NOTE]
> Urządzenia, które zostały udostępnione w usłudze Azure IoT Hub przy użyciu usługi Device Provisioning Service ma dodatkowe dane przechowywane w usłudze Azure IoT Hub. Zobacz [dokumentacja usługi Azure IoT Hub](../iot-hub/iot-hub-customer-data-requests.md) w celu wykonania pełnego żądania dla danego urządzenia.

## <a name="deleting-customer-data"></a>Usuwanie danych klienta

Usługi Device Provisioning przechowuje rejestracji i rejestracji rekordów. Rejestracje zawierają informacje dotyczące urządzeń, które są dozwolone do aprowizacji i rejestracji rekordy pokazują, które urządzenia już przeszły proces inicjowania obsługi administracyjnej.

Administratorzy dzierżawy mogą usunąć rejestracje w witrynie Azure portal, a spowoduje to usunięcie wszystkich rekordów rejestracji skojarzony.

Aby uzyskać więcej informacji, zobacz [jak zarządzanie rejestracjami urządzeń](how-to-manage-enrollments.md).

Istnieje również możliwość wykonywania operacji usuwania rejestracji i rekordów rejestracji przy użyciu interfejsów API REST:

* Aby usunąć informacje o rejestracji dla jednego urządzenia, możesz użyć [rejestrowanie urządzeń — usuwanie](/rest/api/iot-dps/deleteindividualenrollment/deleteindividualenrollment).
* Aby usunąć informacje o rejestracji dla grupy urządzeń, można użyć [grupy rejestracji urządzeń — usuwanie](/rest/api/iot-dps/deleteenrollmentgroup/deleteenrollmentgroup).
* Aby usunąć informacje o urządzeniach, które zostały udostępnione, można użyć [stanu rejestracji — usuwanie stanu rejestracji](/rest/api/iot-dps/deletedeviceregistrationstate/deletedeviceregistrationstate).

## <a name="exporting-customer-data"></a>Eksportowanie danych klienta

Usługi Device Provisioning przechowuje rejestracji i rejestracji rekordów. Rejestracje zawierają informacje dotyczące urządzeń, które są dozwolone do aprowizacji i rejestracji rekordy pokazują, które urządzenia już przeszły proces inicjowania obsługi administracyjnej.

Administratorzy dzierżawy mogą wyświetlić rejestracje i rekordy rejestracji za pośrednictwem witryny Azure portal i wyeksportować je za pomocą kopiowania i wklejania.

Aby uzyskać więcej informacji na temat zarządzania rejestracji, zobacz [jak zarządzanie rejestracjami urządzeń](how-to-manage-enrollments.md).

Istnieje również możliwość wykonywania operacji eksportowania do rejestracji i rekordów rejestracji przy użyciu interfejsów API REST:

* Aby wyeksportować informacje o rejestracji dla jednego urządzenia, możesz użyć [rejestrowanie urządzeń — Get](/rest/api/iot-dps/getindividualenrollment/getindividualenrollment).
* Aby wyeksportować informacje o rejestracji dla grupy urządzeń, można użyć [grupy rejestracji urządzeń - Get](/rest/api/iot-dps/getenrollmentgroup/getenrollmentgroup).
* Aby wyeksportować informacje o urządzeniach, które już zostały udostępnione, można użyć [stanu rejestracji — stan rejestracji Get](/rest/api/iot-dps/getdeviceregistrationstate/getdeviceregistrationstate).

> [!NOTE]
> Korzystając z usług firmy Microsoft dla przedsiębiorstw firmy Microsoft generuje pewnych informacji, znane jako dzienniki generowane przez system. Niektóre usługi Device Provisioning dzienniki generowane przez system nie są dostępne lub który można eksportować przez administratorów dzierżawy. Te dzienniki stanowią odzwierciedlenie faktycznych działań przeprowadzanych w ramach usługi i dane diagnostyczne, które dotyczą poszczególnych urządzeń.

## <a name="links-to-additional-documentation"></a>Linki do dodatkowej dokumentacji

Pełną dokumentację dotyczącą interfejsów API usługi aprowizacji urządzeń znajduje się w [ https://docs.microsoft.com/rest/api/iot-dps ](https://docs.microsoft.com/rest/api/iot-dps).

Usługa Azure IoT Hub [funkcje na żądanie danych klienta](../iot-hub/iot-hub-customer-data-requests.md).