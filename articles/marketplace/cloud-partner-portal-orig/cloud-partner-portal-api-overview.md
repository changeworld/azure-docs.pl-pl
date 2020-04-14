---
title: Dokumentacja interfejsu API portalu partnerów w chmurze | Azure Marketplace
description: Opis, wymagania wstępne do użycia i lista operacji interfejsu API w portalu marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: b9f698ea81830aaa8761c05012cf6843d07ad5a4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256420"
---
# <a name="cloud-partner-portal-api-reference"></a>Dokumentacja interfejsu API portalu Cloud Partner

> [!NOTE]
> Interfejsy API portalu partnerów w chmurze są zintegrowane z centrum partnerów i będą nadal działać po migracji ofert do Centrum partnerów. Integracja wprowadza niewielkie zmiany. Przejrzyj [zmiany w interfejsach API CPP](#changes-to-cpp-apis-after-the-migration-to-partner-center) wymienione w tym dokumencie, aby upewnić się, że kod będzie nadal działać po migracji do Centrum partnerów.

Interfejsy API REST portalu partnerów w chmurze umożliwiają programowe pobieranie i manipulowanie obciążeniami, ofertami i profilami wydawców. Interfejsy API używają kontroli dostępu opartej na rolach (RBAC) do wymuszania poprawnych uprawnień w czasie przetwarzania.

To odwołanie zawiera szczegóły techniczne interfejsów API REST portalu programu Cloud Partner Portal. Próbki ładunku w tym dokumencie są tylko w celach informacyjnych i mogą ulec zmianie w miarę dodawania nowych funkcji.

## <a name="prerequisites-and-considerations"></a>Wymagania wstępne i zagadnienia

Przed użyciem interfejsów API należy przejrzeć:

- Artykuł [wymagań wstępnych,](./cloud-partner-portal-api-prerequisites.md) aby dowiedzieć się, jak dodać jednostkę usługi do konta i uzyskać token dostępu usługi Azure Active Directory (Azure AD) do uwierzytelniania.
- Dwie strategie [kontroli współbieżności](./cloud-partner-portal-api-concurrency-control.md) dostępne do wywoływania tych interfejsów API.
- Dodatkowe [zagadnienia dotyczące](./cloud-partner-portal-api-considerations.md)interfejsu API, takie jak przechowywanie wersji i obsługa błędów.

## <a name="changes-to-cpp-apis-after-the-migration-to-partner-center"></a>Zmiany w interfejsach API CPP po migracji do centrum partnerskiego

| **interfejs API** | **Zmień opis** | **Wpływ** |
| ------- | ---------------------- | ---------- |
| PUBLIKUJ, GoLive, Anuluj | W przypadku zmigrowanych ofert nagłówek odpowiedzi będzie miał inny format, ale będzie nadal działać w ten sam sposób, oznaczając ścieżkę względną w celu pobrania stanu operacji. | Podczas wysyłania dowolnego z odpowiednich żądań POST dla oferty, nagłówek Lokalizacja będzie miał jeden z dwóch formatów w zależności od stanu migracji oferty:<ul><li>Oferty niezmiagrowane<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Zmigrowane oferty<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| Operacja GET | W przypadku typów ofert, które wcześniej obsługiwały pole "notification-email" w odpowiedzi, to pole zostanie przestarzałe i nie będzie już zwracane dla zmigrowanych ofert. | W przypadku zmigrowanych ofert nie będziemy już wysyłać powiadomień do listy wiadomości e-mail określonych w żądaniach. Zamiast tego usługa interfejsu API będzie zgodna z procesem wiadomości e-mail powiadomień w Centrum partnerów, aby wysyłać wiadomości e-mail. W szczególności powiadomienia będą wysyłane na adres e-mail ustawiony w sekcji Informacje kontaktowe sprzedającego w ustawieniach konta w Centrum partnerów, aby powiadomić Cię o postępie operacji.<br><br>Zapoznaj się z adresem e-mail ustawionym w sekcji Informacje kontaktowe sprzedającego w [ustawieniach konta](https://partner.microsoft.com/dashboard/account/management) w Centrum partnerów, aby upewnić się, że w przypadku powiadomień jest dostępna poprawna wiadomość e-mail.  |

## <a name="common-tasks"></a>Typowe zadania

To odwołanie szczegóły interfejsów API do wykonywania następujących typowych zadań.

### <a name="offers"></a>Oferty

- [Pobierz wszystkie oferty](./cloud-partner-portal-api-retrieve-offers.md)
- [Pobieranie określonej oferty](./cloud-partner-portal-api-retrieve-specific-offer.md)
- [Pobieranie stanu oferty](./cloud-partner-portal-api-retrieve-offer-status.md)
- [Tworzenie oferty](./cloud-partner-portal-api-creating-offer.md)
- [Publikowanie oferty](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Operacje

- [Pobieranie operacji](./cloud-partner-portal-api-retrieve-operations.md)
- [Anulowanie operacji](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Publikowanie aplikacji

- [Udostępnianie](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Inne zadania

- [Ustawianie cen ofert dla maszyn wirtualnych](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Rozwiązywanie problemów

- [Rozwiązywanie problemów z błędami uwierzytelniania](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
