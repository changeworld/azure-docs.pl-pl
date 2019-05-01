---
title: Dane klienta żądania funkcji w usłudze Azure IoT Central | Dokumentacja firmy Microsoft
description: Funkcji żądania danych klienta w usłudze Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 4c63ac8d6b16b2a0476600f67c66b2ccc8dc2aab
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64685554"
---
# <a name="summary-of-customer-data-request-features"></a>Podsumowanie funkcji żądania danych klienta

Usługa Azure IoT Central to w pełni zarządzane rozwiązanie oprogramowania jako usługi Internetu rzeczy (IoT), które ułatwia łączenie, monitorowanie i zarządzać elementami zawartości IoT na dużą skalę, utworzyć szczegółowe informacje na podstawie danych IoT i podejmowania świadomych decyzji.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identyfikowanie danych klienta

Usługa Azure Active Directory — identyfikatory obiektów są używane do identyfikowania użytkowników i przypisywać role. Usługa Azure IoT Central adresy e-mail użytkowników w portalu jest wyświetlany w do przypisania roli, ale tylko usługi Azure Active Directory — identyfikator obiektu są przechowywane, adres e-mail jest wysyłane zapytanie dynamiczne z usługi Azure Active Directory. Administratorzy usługi Azure IoT Central można wyświetlić, eksportowanie i usuwanie użytkowników aplikacji, w sekcji Administracja użytkownika aplikacji usługi Azure IoT Central.

W aplikacji aby otrzymywać alerty można skonfigurować adresy e-mail. W takim przypadku adresy e-mail są przechowywane w ramach IoT Central i musi być zarządzany z poziomu strony administrowania konta w aplikacji.

Dotyczące urządzenia firma Microsoft zachowuje żadnych informacji i nie ma dostępu do danych, które umożliwia urządzenia do użytkownika korelacji. Wiele urządzeń zarządzanych w usłudze Azure IoT Central nie są urządzeń osobistych, na przykład Automat lub twórca kawy. Klienci mogą jednak wziąć pod uwagę niektóre urządzenia jako osobiste i według własnego uznania mogą utrzymać ich własnych zasobów magazynu śledzenia systemami powiązanie urządzeń fizycznych. Usługa Azure IoT Central zarządza i przechowuje wszystkie dane skojarzone z urządzeń, jak w przypadku danych osobowych.

Korzystając z usług firmy Microsoft dla przedsiębiorstw firmy Microsoft generuje pewnych informacji, znane jako dzienniki generowane przez system. Te dzienniki stanowią odzwierciedlenie faktycznych działań przeprowadzanych w ramach usługi i dane diagnostyczne, które dotyczą poszczególnych urządzeń, a nie są związane z aktywności użytkownika. Usługa Azure IoT Central dzienniki generowane przez system nie są dostępne lub który można eksportować przez administratorów aplikacji.

## <a name="deleting-customer-data"></a>Usuwanie danych klienta

Możliwość usunięcia danych użytkownika jest dostępna wyłącznie przez stronę administracyjną IoT Central. Administratorzy aplikacji można wybrać użytkownika do usunięcia, a następnie wybierz **Usuń** w prawym górnym rogu aplikacji, aby usunąć rekord. Administratorzy aplikacji można również usunąć indywidualne konta, które nie są już skojarzone z aplikacją, która jest zagrożona.

Po użytkownik zostanie usunięty, żadne dalsze alerty wysyłane są do nich. Jednak swój adres e-mail musi indywidualnie usunięte z każdego skonfigurowanego alertu.

Aby uzyskać więcej informacji, zobacz [Konfiguruj reguły i akcje dla Twojego urządzenia](tutorial-configure-rules.md).

## <a name="exporting-customer-data"></a>Eksportowanie danych klienta

Możliwość eksportowania danych jest dostępna wyłącznie przez stronę administracyjną IoT Central. Dane klienta, w tym przypisane role można wybrane, skopiować i wkleić, administrator aplikacji.

## <a name="links-to-additional-documentation"></a>Linki do dodatkowej dokumentacji

Aby uzyskać więcej informacji na temat administrowania kontem, w tym definicje ról, zobacz [jak zarządzać aplikacją](howto-administer.md).
