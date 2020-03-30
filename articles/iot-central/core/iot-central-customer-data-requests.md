---
title: Funkcje żądania danych klienta w usłudze Azure IoT Central | Dokumenty firmy Microsoft
description: W tym artykule opisano identyfikowanie, usuwanie i eksportowanie danych klientów w aplikacji Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 3cf88b4d3c4c74493235d2997db4d464bb055b81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023756"
---
# <a name="summary-of-customer-data-request-features"></a>Podsumowanie funkcji żądania danych klientów

Usługa Azure IoT Central to w pełni zarządzane rozwiązanie do obsługi internetu rzeczy (IoT), które ułatwia łączenie, monitorowanie i zarządzanie zasobami IoT na dużą skalę, tworzenie szczegółowych informacji na podstawie danych IoT i podejmowanie świadomych działań.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identyfikowanie danych klientów

Identyfikatory obiektów usługi Azure Active Directory służą do identyfikowania użytkowników i przypisywania ról. Portal Azure IoT Central wyświetla adresy e-mail użytkowników dla przypisań ról, ale tylko identyfikator obiektu usługi Azure Active Directory jest przechowywany, adres e-mail jest dynamicznie wyszukiwany z usługi Azure Active Directory. Administratorzy usługi Azure IoT Central mogą wyświetlać, eksportować i usuwać użytkowników aplikacji w sekcji administrowania użytkownikami aplikacji Azure IoT Central.

W aplikacji adresy e-mail można skonfigurować do odbierania alertów. W takim przypadku adresy e-mail są przechowywane w centrum IoT i muszą być zarządzane ze strony administracji kontem w aplikacji.

W odniesieniu do urządzeń firma Microsoft nie przechowuje żadnych informacji i nie ma dostępu do danych, które umożliwiają korelację urządzenia z użytkownikami. Wiele urządzeń zarządzanych w usłudze Azure IoT Central nie jest urządzeniami osobistymi, na przykład automatem lub ekspresem do kawy. Klienci mogą jednak uznać niektóre urządzenia za możliwe do zidentyfikowania i według własnego uznania mogą utrzymywać własne systemy śledzenia zasobów lub zapasów, które wiążą urządzenia z osobami fizycznymi. Usługa Azure IoT Central zarządza i przechowuje wszystkie dane skojarzone z urządzeniami tak, jakby były danymi osobistymi.

Podczas korzystania z usług firmy Microsoft dla przedsiębiorstw firma Microsoft generuje pewne informacje, znane jako dzienniki generowane przez system. Dzienniki te stanowią rzeczowe działania prowadzone w ramach usługi i dane diagnostyczne związane z poszczególnymi urządzeniami i nie są związane z aktywnością użytkownika. Dzienniki generowane przez system Usługi Azure IoT Central nie są dostępne ani eksportowane przez administratorów aplikacji.

## <a name="deleting-customer-data"></a>Usuwanie danych klienta

Możliwość usuwania danych użytkownika jest dostępna tylko za pośrednictwem strony administracyjnej IoT Central. Administratorzy aplikacji mogą wybrać użytkownika do usunięcia i wybrać **pozycję Usuń** w prawym górnym rogu aplikacji, aby usunąć rekord. Administratorzy aplikacji mogą również usuwać poszczególne konta, które nie są już skojarzone z daną aplikacją.

Po usunięciu użytkownika nie są do nich wysyłane żadne dalsze alerty. Jednak ich adres e-mail musi być indywidualnie usunięty z każdego skonfigurowany alert.

## <a name="exporting-customer-data"></a>Eksportowanie danych klientów

Możliwość eksportowania danych jest dostępna tylko za pośrednictwem strony administracyjnej IoT Central. Dane klienta, w tym przypisane role, mogą być wybierane, kopiowane i wklejane przez administratora aplikacji.

## <a name="links-to-additional-documentation"></a>Linki do dodatkowej dokumentacji

Aby uzyskać więcej informacji na temat administrowania kontami, w tym definicje ról, zobacz [Jak administrować aplikacją](howto-administer.md).
