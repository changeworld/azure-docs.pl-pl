---
title: Dokumentacja interfejsu API w portalu partnerów w chmurze | Portal Azure Marketplace
description: Opis, wymagania wstępne dotyczące korzystania i listę operacji interfejsu API w portalu marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 9626fdb5dbdf720a65217421565fc559a0cee684
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935477"
---
<a name="cloud-partner-portal-api-reference"></a>Dokumentacja interfejsu API w portalu partnera chmury
==================================

Interfejsy API REST programu Cloud Partner Portal umożliwiają programowe pobieranie i manipulowania nimi profile wydawcy, oferty i obciążeń. Interfejsy API użyć kontroli dostępu opartej na rolach (RBAC), aby wymusić odpowiednie uprawnienia w czasie przetwarzania.

Ta dokumentacja zawiera szczegółowe informacje techniczne dla interfejsów API REST programu Cloud Partner portalu. Przykłady ładunku w tym dokumencie są tylko do celów referencyjnych i może ulec zmianie po dodaniu nowych funkcji.


<a name="prerequisites-and-considerations"></a>Wymagania wstępne i zagadnienia
-------------------------------

Przed rozpoczęciem korzystania z interfejsów API, należy zapoznać się:

- [Wymagania wstępne](./cloud-partner-portal-api-prerequisites.md) artykuł, aby dowiedzieć się, jak dodać nazwy głównej usługi do swojego konta i uzyskiwanie tokenów dostępu usługi Azure Active Directory (Azure AD) do uwierzytelniania. 
- Dwa [kontroli współbieżności](./cloud-partner-portal-api-concurrency-control.md).
Strategie dostępne do wywoływania tych interfejsów API.
- Dodatkowy interfejs API [zagadnienia](./cloud-partner-portal-api-considerations.md), takich jak przechowywanie wersji i obsługi błędów.


<a name="common-tasks"></a>Typowe zadania
------------
Ta dokumentacja szczegółowo opisuje interfejsy API, aby wykonać następujące typowe zadania.


### <a name="offers"></a>Oferty

-   [Pobieranie wszystkich ofert](./cloud-partner-portal-api-retrieve-offers.md)
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

-   [Ustaw cennik oferty maszyny wirtualnej](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Rozwiązywanie problemów

-   [Rozwiązywanie problemów z błędami uwierzytelniania](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
