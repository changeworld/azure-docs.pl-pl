---
title: Informacje o interfejsie API portal Cloud Partner | Portal Azure Marketplace
description: Opis, wymagania wstępne do użycia oraz lista operacji interfejsu API portalu Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: b6591e1780d03cbfaff70fbd19ec3dfd274fae79
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819617"
---
<a name="cloud-partner-portal-api-reference"></a>Dokumentacja interfejsu API portal Cloud Partner
==================================

Interfejsy API REST portal Cloud Partner umożliwiają pobieranie programistyczne i manipulowanie obciążeniami, ofertami i profilami wydawcy. Interfejsy API używają kontroli dostępu opartej na rolach (RBAC) do wymuszania prawidłowych uprawnień w czasie przetwarzania.

Ta dokumentacja zawiera szczegółowe informacje techniczne dotyczące portal Cloud Partner interfejsów API REST. Próbki ładunku w tym dokumencie są przeznaczone tylko do celów informacyjnych i mogą ulec zmianie po dodaniu nowych funkcji.


<a name="prerequisites-and-considerations"></a>Wymagania wstępne i zagadnienia
-------------------------------

Przed użyciem interfejsów API należy zapoznać się z tematem:

- W artykule dotyczącym [wymagań wstępnych](./cloud-partner-portal-api-prerequisites.md) dowiesz się, jak dodać jednostkę usługi do konta i uzyskać token dostępu Azure Active Directory (Azure AD) na potrzeby uwierzytelniania. 
- Dwie [kontrolki współbieżności](./cloud-partner-portal-api-concurrency-control.md).
strategie dostępne do wywoływania tych interfejsów API.
- Dodatkowe [zagadnienia dotyczące](./cloud-partner-portal-api-considerations.md)interfejsów API, takie jak przechowywanie wersji i obsługa błędów.


<a name="common-tasks"></a>Typowe zadania
------------
Te szczegółowe informacje dotyczące interfejsów API do wykonywania następujących typowych zadań.


### <a name="offers"></a>Oferta

-   [Pobierz wszystkie oferty](./cloud-partner-portal-api-retrieve-offers.md)
-   [Pobierz konkretną ofertę](./cloud-partner-portal-api-retrieve-specific-offer.md)
-   [Pobieranie stanu oferty](./cloud-partner-portal-api-retrieve-offer-status.md)
-   [Tworzenie oferty](./cloud-partner-portal-api-creating-offer.md)
-   [Publikowanie oferty](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Operacje

-   [Pobieranie operacji](./cloud-partner-portal-api-retrieve-operations.md)
-   [Anulowanie operacji](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Publikowanie aplikacji

-   [Udostępnianie](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Inne zadania

-   [Ustawianie cen dla ofert maszyn wirtualnych](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Rozwiązywanie problemów

-   [Rozwiązywanie problemów z błędami uwierzytelniania](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
