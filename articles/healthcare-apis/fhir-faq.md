---
title: Często zadawane pytania (FAQ) dotyczących FHIR usług na platformie Azure — dla FHIR interfejsu API usługi Azure
description: W tym artykule — często zadawane pytania odpowiedzi na często zadawane pytania dotyczące interfejsu API platformy Azure dla FHIR
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 7403a23e236c14d77672d5b80d953b1e11088f8a
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824138"
---
# <a name="frequently-asked-questions-about-azure-api-for-fhir"></a>Często zadawane pytania dotyczące usługi Azure API dla FHIR

## <a name="storage-location"></a>Lokalizacja magazynu

**Czy dane źródłowe FHIR&reg; interfejsy API są przechowywane na platformie Azure?** Tak, dane są przechowywane w zarządzanych baz danych na platformie Azure. Interfejs API Azure FHIR nie zapewnia bezpośredni dostęp do magazynu danych.

## <a name="identity-providers"></a>Dostawcy tożsamości

Obecnie obsługujemy Microsoft Azure Active Directory jako dostawcy tożsamości.

## <a name="supported-fhir-version"></a>Obsługiwana wersja FHIR

Obecnie obsługujemy wersji 3.0.1. Zobacz [obsługiwane funkcje](fhir-features-supported.md) Aby uzyskać szczegółowe informacje.

## <a name="oss-and-azure-api-for-fhir"></a>API OSS i na platformie Azure dla FHIR

Jaka jest różnica między serwerem Otwórz FHIR Microsoft źródła dla platformy Azure i interfejsu API usługi Azure dla FHIR? Interfejs API Azure FHIR to wersja hostowanych i zarządzanych OSS serwera FHIR firmy Microsoft dla platformy Azure. W usłudze zarządzanej firma Microsoft udostępnia wszystkie konserwacji, aktualizacje itp. Podczas uruchamiania serwera FHIR OSS dla platformy Azure, masz bezpośredni dostęp do podstawowych usług, ale ponosisz odpowiedzialność za utrzymanie, Aktualizacja serwera i cała Praca wymagana zgodność w przypadku przechowywania danych chronionych informacji o zdrowiu.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule znasz niektóre często zadawane pytania dotyczące interfejsu API platformy Azure dla FHIR. Przeczytaj o obsługiwane funkcje interfejsu API na serwerze FHIR firmy Microsoft dla platformy Azure.
 
>[!div class="nextstepaction"]
>[Obsługiwane funkcje FHIR](fhir-features-supported.md)