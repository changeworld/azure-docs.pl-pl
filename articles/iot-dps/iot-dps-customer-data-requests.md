---
title: Funkcje żądania danych klienta dla urządzeń DPS platformy Azure
description: W przypadku urządzeń zarządzanych w usłudze azure device provisioning service (DPS), które są osobiste, w tym artykule pokazano administratorów, jak eksportować lub usuwać dane osobowe.
author: dominicbetts
ms.author: dobett
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 1dcf1b9f62f94b8f75ef2fe77f3e237a387c53eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890652"
---
# <a name="summary-of-customer-data-request-features"></a>Podsumowanie funkcji żądania danych klientów

Usługa inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub to usługa chmurowa oparta na interfejsie API REST, przeznaczona dla klientów korporacyjnych, która umożliwia bezproblemowe, automatyczne udostępnianie urządzeń bez dotykowego w usłudze Azure IoT Hub z zabezpieczeniami, które rozpoczynają się na urządzeniu i kończą się chmurą.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Poszczególne urządzenia są przypisywane identyfikatorowi rejestracji i identyfikatorowi urządzenia przez administratora dzierżawy. Dane z i o tych urządzeniach są oparte na tych identyfikatorach. Firma Microsoft nie przechowuje żadnych informacji i nie ma dostępu do danych, które pozwoliłyby na korelację tych urządzeń z osobą fizyczną.

Wiele urządzeń zarządzanych w usłudze obsługi urządzeń nie jest urządzeniami osobistymi, na przykład termostatem biurowym lub robotem fabrycznym. Klienci mogą jednak uznać niektóre urządzenia za możliwe do zidentyfikowania i według własnego uznania mogą utrzymywać własne metody śledzenia zasobów lub zapasów, które wiążą urządzenia z osobami fizycznymi. Usługa inicjowania obsługi urządzeń zarządza i przechowuje wszystkie dane skojarzone z urządzeniami tak, jakby były danymi osobowymi.

Administratorzy dzierżawy mogą używać witryny Azure portal lub interfejsów API REST usługi do obsługi żądań informacji przez eksportowanie lub usuwanie danych skojarzonych z identyfikatorem urządzenia lub identyfikatorem rejestracji.

> [!NOTE]
> Urządzenia, które zostały aprowied w usłudze Azure IoT Hub za pośrednictwem usługi inicjowania obsługi administracyjnej urządzeń mają dodatkowe dane przechowywane w usłudze Azure IoT Hub. Zobacz [dokumentację referencyjną usługi Azure IoT Hub,](../iot-hub/iot-hub-customer-data-requests.md) aby wypełnić pełne żądanie dla danego urządzenia.

## <a name="deleting-customer-data"></a>Usuwanie danych klienta

Usługa inicjowania obsługi administracyjnej urządzeń przechowuje rejestracje i rekordy rejestracji. Rejestracje zawierają informacje o urządzeniach, które mogą być aprowidyzne, a rekordy rejestracji pokazują, które urządzenia przeszły już proces inicjowania obsługi administracyjnej.

Administratorzy dzierżawy mogą usuwać rejestracje z witryny Azure portal, co spowoduje usunięcie wszystkich skojarzonych rekordów rejestracji.

Aby uzyskać więcej informacji, zobacz [Jak zarządzać rejestracjami urządzeń](how-to-manage-enrollments.md).

Możliwe jest również wykonywanie operacji usuwania dla rejestracji i rekordów rejestracji przy użyciu interfejsów API REST:

* Aby usunąć informacje o rejestracji dla jednego urządzenia, można użyć [rejestracji urządzeń — usuń](/rest/api/iot-dps/deleteindividualenrollment/deleteindividualenrollment).
* Aby usunąć informacje o rejestracji dla grupy urządzeń, można użyć [grupy rejestracji urządzeń — Usuń](/rest/api/iot-dps/deleteenrollmentgroup/deleteenrollmentgroup).
* Aby usunąć informacje o urządzeniach, które zostały aprowidy, można użyć [stanu rejestracji — usuń stan rejestracji](/rest/api/iot-dps/deletedeviceregistrationstate/deletedeviceregistrationstate).

## <a name="exporting-customer-data"></a>Eksportowanie danych klientów

Usługa inicjowania obsługi administracyjnej urządzeń przechowuje rejestracje i rekordy rejestracji. Rejestracje zawierają informacje o urządzeniach, które mogą być aprowidyzne, a rekordy rejestracji pokazują, które urządzenia przeszły już proces inicjowania obsługi administracyjnej.

Administratorzy dzierżawy mogą wyświetlać rejestracje i rekordy rejestracji za pośrednictwem witryny Azure portal i eksportować je przy użyciu kopiowania i wklejać.

Aby uzyskać więcej informacji na temat zarządzania rejestracjami, zobacz [Jak zarządzać rejestracjami urządzeń](how-to-manage-enrollments.md).

Możliwe jest również wykonywanie operacji eksportowania dla rejestracji i rekordów rejestracji przy użyciu interfejsów API REST:

* Aby wyeksportować informacje o rejestracji dla jednego urządzenia, można użyć [rejestracji urządzenia — Pobierz](/rest/api/iot-dps/getindividualenrollment/getindividualenrollment).
* Aby wyeksportować informacje o rejestracji dla grupy urządzeń, można użyć [grupy rejestracji urządzeń — Pobierz](/rest/api/iot-dps/getenrollmentgroup/getenrollmentgroup).
* Aby wyeksportować informacje o urządzeniach, które zostały już zainicjowane, można użyć [stanu rejestracji — Pobierz stan rejestracji](/rest/api/iot-dps/getdeviceregistrationstate/getdeviceregistrationstate).

> [!NOTE]
> Podczas korzystania z usług firmy Microsoft dla przedsiębiorstw firma Microsoft generuje pewne informacje, znane jako dzienniki generowane przez system. Niektóre dzienniki generowane przez system obsługi obsługi administracyjnej urządzeń nie są dostępne ani eksportowane przez administratorów dzierżawy. Dzienniki te stanowią rzeczowe działania prowadzone w ramach usługi i dane diagnostyczne związane z poszczególnymi urządzeniami.

## <a name="links-to-additional-documentation"></a>Linki do dodatkowej dokumentacji

Pełna dokumentacja interfejsów API usługi inicjowania obsługi urządzeń znajduje się pod adresem [https://docs.microsoft.com/rest/api/iot-dps](https://docs.microsoft.com/rest/api/iot-dps).

[Funkcje żądania danych klienta](../iot-hub/iot-hub-customer-data-requests.md)usługi Azure IoT Hub .