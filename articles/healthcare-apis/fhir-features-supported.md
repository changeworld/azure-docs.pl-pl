---
title: Obsługiwane funkcje FHIR na platformie Azure — interfejs API platformy Azure, dla FHIR
description: W tym artykule wyjaśniono, które funkcje specyfikacji FHIR, które są implementowane w usłudze Azure API dla FHIR
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: a2851272d15ed2ce2c7f940cc3e429ef9f8b4446
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824146"
---
# <a name="features"></a>Funkcje

Usługa Azure interfejs API dla FHIR zapewnia w pełni zarządzane wdrożenia programu Microsoft Server FHIR dla platformy Azure. Serwer jest implementacją [FHIR](https://hl7.org/fhir) standardowych. Ten dokument zawiera listę najważniejszych funkcji serwera FHIR.

## <a name="fhir-version"></a>Wersja FHIR

Bieżąca wersja: `3.0.1`

## <a name="rest-api"></a>Interfejs API REST

| Interfejs API                            | Obsługiwane | Komentarz |
|--------------------------------|-----------|---------|
| odczyt                           | Yes       |         |
| vread                          | Yes       |         |
| update                         | Yes       |         |
| aktualizowanie za pomocą optymistyczne blokowanie | Yes       |         |
| Aktualizowanie (warunkowego)           | Nie        |         |
| Poprawki                          | Nie        |         |
| delete                         | Yes       |         |
| Usuń (warunkowego)           | Nie        |         |
| create                         | Yes       | Obsługuje zarówno PUT/POST |
| Tworzenie (warunkowego)           | Nie        |         |
| szukaj                         | Częściowe   | Zobacz poniżej |
| Możliwości                   | Yes       |         |
| partia                          | Nie        |         |
| Transakcji                    | Nie        |         |
| Historia                        | Yes       |         |
| Stronicowania                         | Częściowe   | `self` i `next` są obsługiwane |
| Elementy pośredniczące                 | Nie        |         |

## <a name="search"></a>Wyszukiwanie

Obsługiwane są wszystkie typy parametrów wyszukiwania. Tworzenie łańcucha parametrów i zwrotny czy łańcucha *nie* obsługiwane.

| Typ parametru wyszukiwania | Obsługiwane | Komentarz |
|-----------------------|-----------|---------|
| Liczba                | Yes       |         |
| Data/Data i godzina         | Yes       |         |
| String                | Yes       |         |
| Token                 | Yes       |         |
| Informacje ogólne             | Yes       |         |
| Złożone             | Yes       |         |
| Ilość              | Yes       | Problem [#103](https://github.com/Microsoft/fhir-server/issues/103) |
| URI                   | Yes       |         |


| Modyfikatory             | Obsługiwane | Komentarz |
|-----------------------|-----------|---------|
|`:missing`             | Yes       |         |
|`:exact`               | Yes       |         |
|`:contains`            | Yes       |         |
|`:text`                | Yes       |         |
|`:in` (token)          | Nie        |         |
|`:below` (token)       | Nie        |         |
|`:above` (token)       | Nie        |         |
|`:not-in` (token)      | Nie        |         |
|`:[type]` (odwołanie w)  | Nie        |         |
|`:below` Identyfikator (uri)         | Yes       |         |
|`:above` Identyfikator (uri)         | Nie        | Problem [#158](https://github.com/Microsoft/fhir-server/issues/158) |

| Typowy parametr wyszukiwania | Obsługiwane | Komentarz |
|-------------------------| ----------|---------|
| `_id`                   | Yes       |         |
| `_lastUpdated`          | Yes       |         |
| `_tag`                  | Yes       |         |
| `_profile`              | Yes       |         |
| `_security`             | Yes       |         |
| `_text`                 | Nie        |         |
| `_content`              | Nie        |         |
| `_list`                 | Nie        |         |
| `_has`                  | Nie        |         |
| `_type`                 | Yes       |         |
| `_query`                | Nie        |         |

| Wyszukaj operacje       | Obsługiwane | Komentarz |
|-------------------------|-----------|---------|
| `_filter`               | Nie        |         |
| `_sort`                 | Nie        |         |
| `_score`                | Nie        |         |
| `_count`                | Yes       |         |
| `_summary`              | Częściowe   | `_summary=count` jest obsługiwany |
| `_include`              | Nie        |         |
| `_revinclude`           | Nie        |         |
| `_contained`            | Nie        |         |
| `_elements`             | Nie        |         |

## <a name="persistence"></a>Trwałość

Microsoft FHIR Server ma moduł podłączanych trwałości (zobacz [ `Microsoft.Health.Fhir.Core.Features.Persistence` ](https://github.com/Microsoft/fhir-server/src/Microsoft.Health.Fhir.Core/Features/Persistence)).

Obecnie dotyczy to implementacja na potrzeby kodu typu open source serwera FHIR [usługi Azure Cosmos DB](../cosmos-db/index-overview.md).

Usługa cosmos DB to globalnie dystrybuowana wiele modeli (interfejs API SQL, interfejsu API usługi MongoDB itp.) baza danych. Obsługuje ona różne [poziomów spójności](../cosmos-db/consistency-levels.md). Domyślny szablon wdrożenia konfiguruje serwer FHIR przy użyciu `Strong` spójności, ale zasady zgodności mogą być modyfikowane (zazwyczaj swobodniejszych) na żądanie przez żądania przy użyciu `x-ms-consistency-level` nagłówek żądania.

## <a name="role-based-access-control"></a>Kontrola dostępu na podstawie ról

Serwer FHIR używa [usługi Azure Active Directory](https://azure.microsoft.com/services/active-directory/) kontroli dostępu. W szczególności kontroli dostępu na podstawie ról (RBAC) jest wymuszana, jeśli `FhirServer:Security:Enabled` parametr konfiguracji jest ustawiony na `true`i wszystkich żądań (z wyjątkiem `/metadata`) do serwera FHIR musi mieć `Authorization` nagłówek żądania równa `Bearer <TOKEN>`. Token musi zawierać co najmniej jedną rolę, zgodnie z definicją w `roles` oświadczenia. Żądania będą miały, jeśli token zawiera rolę umożliwiająca określonej akcji na określony zasób.

Obecnie są stosowane akcje dozwolone dla danej roli *globalnie* w interfejsie API.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule zostały przeczytane dotyczące obsługiwanych funkcji FHIR w interfejsie API Azure FHIR. Następnie Wdróż FHIR interfejsu API na platformie Azure.
 
>[!div class="nextstepaction"]
>[Wdrażanie serwera Otwórz FHIR źródła](fhir-oss-powershell-quickstart.md)