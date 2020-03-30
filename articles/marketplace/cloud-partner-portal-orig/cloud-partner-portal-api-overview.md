---
title: Dokumentacja interfejsu API portalu partnerów w chmurze | Azure Marketplace
description: Opis, wymagania wstępne do użycia i lista operacji interfejsu API w portalu marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 296c667876807bdd05a6281de461df76207b5490
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288550"
---
<a name="cloud-partner-portal-api-reference"></a>Odwołanie do interfejsu API portalu partnerów w chmurze
==================================

Interfejsy API REST portalu partnerów w chmurze umożliwiają programowe pobieranie i manipulowanie obciążeniami, ofertami i profilami wydawców. Interfejsy API używają kontroli dostępu opartej na rolach (RBAC) do wymuszania poprawnych uprawnień w czasie przetwarzania.

To odwołanie zawiera szczegóły techniczne interfejsów API REST portalu programu Cloud Partner Portal. Próbki ładunku w tym dokumencie są tylko w celach informacyjnych i mogą ulec zmianie w miarę dodawania nowych funkcji.


<a name="prerequisites-and-considerations"></a>Wymagania wstępne i zagadnienia
-------------------------------

Przed użyciem interfejsów API należy przejrzeć:

- Artykuł [wymagań wstępnych,](./cloud-partner-portal-api-prerequisites.md) aby dowiedzieć się, jak dodać jednostkę usługi do konta i uzyskać token dostępu usługi Azure Active Directory (Azure AD) do uwierzytelniania. 
- Kontrola dwóch [współbieżności](./cloud-partner-portal-api-concurrency-control.md).
dostępnych strategii wywoływania tych interfejsów API.
- Dodatkowe [zagadnienia dotyczące](./cloud-partner-portal-api-considerations.md)interfejsu API, takie jak przechowywanie wersji i obsługa błędów.


<a name="common-tasks"></a>Typowe zadania
------------
To odwołanie szczegóły interfejsów API do wykonywania następujących typowych zadań.


### <a name="offers"></a>Oferty

-   [Pobierz wszystkie oferty](./cloud-partner-portal-api-retrieve-offers.md)
-   [Pobieranie określonej oferty](./cloud-partner-portal-api-retrieve-specific-offer.md)
-   [Pobieranie stanu oferty](./cloud-partner-portal-api-retrieve-offer-status.md)
-   [Tworzenie oferty](./cloud-partner-portal-api-creating-offer.md)
-   [Publikowanie oferty](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Operacje

-   [Pobieranie operacji](./cloud-partner-portal-api-retrieve-operations.md)
-   [Anulowanie operacji](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Publikowanie aplikacji

-   [Udostępnianie](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Inne zadania

-   [Ustawianie cen ofert dla maszyn wirtualnych](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Rozwiązywanie problemów

-   [Rozwiązywanie problemów z błędami uwierzytelniania](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
