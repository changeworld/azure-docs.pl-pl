---
title: Dane klienta żądania funkcji w programie Azure IoT centralnej | Dokumentacja firmy Microsoft
description: Funkcje żądania danych klienta w centralnej IoT Azure
author: dominicbetts
ms.author: dobett
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: bb5e263b0332f957b4e7f4a928ccd53f639bcd9c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629409"
---
# <a name="summary-of-customer-data-request-features"></a>Podsumowanie funkcji żądania danych klienta

Środkowe IoT Azure to rozwiązanie oprogramowanie jako usługa pełni zarządzana Internetu rzeczy (IoT), który można łatwo połączyć, monitorowanie, zarządzać IoT na dużą skalę, utworzyć szczegółowych informacji na podstawie danych IoT i podejmowania świadomych działań.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identyfikowanie danych klienta

Azure Active Directory — identyfikatory obiektów są używane do identyfikowania użytkowników i przypisywania ról. Azure IoT centralnej adresu e-mail użytkownika wyświetla portalu do przypisania ról, ale tylko usługi Azure Active Directory — identyfikator obiektu są przechowywane, adres e-mail jest poddawany kwerendzie dynamicznie z usługi Azure Active Directory. Administratorzy centralnych IoT Azure można wyświetlać, eksportować i usuwania użytkowników aplikacji, w sekcji Administracja użytkownika aplikacji Azure IoT centralnej.

W aplikacji aby otrzymywać alerty można skonfigurować adresy e-mail. W takim przypadku adresy e-mail są przechowywane w centralnych IoT i musi być zarządzane ze strony Administracja konta w aplikacji.

Dotyczące urządzeń firma Microsoft zachowuje żadnych informacji i nie ma dostępu do danych, które umożliwia urządzeniu użytkownika korelacji. Wiele urządzeń zarządzanych w usłudze Azure IoT Central nie są urządzeń osobistych, na przykład Automat lub maker kawy. Klienci mogą jednak wziąć pod uwagę niektórych urządzeń osobistych i według własnego uznania może utrzymać własnych zasobów lub spisu, systemy, które powiązanie urządzeń do osób, śledzenia. Azure IoT centralnego zarządza i przechowuje wszystkie dane skojarzone z urządzenia, tak jakby był on dane osobowe.

Korzystając z usług dla przedsiębiorstw firmy Microsoft, Microsoft generuje niektórych informacji, znane jako dzienniki generowane przez system. Te dzienniki stanowią faktyczne akcje dokonywanych w ramach usługi i danych diagnostycznych dotyczących poszczególnych urządzeń, a nie dotyczą działań użytkownika. Azure IoT centralnego generowanych przez system dzienniki nie są dostępne lub można eksportować przez administratorów aplikacji.

## <a name="deleting-customer-data"></a>Usuwanie danych klienta

Możliwość usunięcia danych użytkownika jest dostępna wyłącznie za pośrednictwem strony Administracja centralna IoT. Administratorzy aplikacji można wybrać użytkownika, które chcesz usunąć, a następnie kliknij przycisk **usunąć** w prawym górnym rogu aplikacji do usunięcia w rekordzie. Administratorzy aplikacji można również usunąć indywidualnych kont, które nie są już skojarzone z aplikacją, która jest zagrożona.

Po usunięciu użytkownika żadne dalsze alerty pocztą e-mail do nich. Jednak swój adres e-mail musi indywidualnie usunięte z każdego skonfigurowanego alertu.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie reguł i akcji dla urządzenia](tutorial-configure-rules.md).

## <a name="exporting-customer-data"></a>Eksportowanie danych klienta

Możliwość eksportowania danych jest dostępna wyłącznie za pośrednictwem strony Administracja centralna IoT. Dane klienta, w tym przypisane role można wybrane, skopiować i wkleić przez administratora aplikacji.

## <a name="links-to-additional-documentation"></a>Linki do dodatkowej dokumentacji

Aby uzyskać więcej informacji na temat konta administrowaniem, takich jak definicje ról, zobacz [administrowanie aplikacji](howto-administer.md).
