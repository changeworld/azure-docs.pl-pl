---
title: Zarządzanie danymi użytkownika w usłudze Azure Active Directory B2C | Dokumenty firmy Microsoft
description: Dowiedz się, jak usunąć lub wyeksportować dane użytkownika w usłudze Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2018
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: e245b58449ab773914fc60be056082b82f05035a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184489"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Zarządzanie danymi użytkownika w usłudze Azure Active Directory B2C

 W tym artykule omówiono sposób zarządzania danymi użytkownika w usłudze Azure Active Directory B2C (Azure AD B2C) przy użyciu operacji dostarczanych przez [interfejs API programu Microsoft Graph](https://docs.microsoft.com/graph/use-the-api). Zarządzanie danymi użytkownika obejmuje usuwanie lub eksportowanie danych z dzienników inspekcji.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Usuwanie danych użytkownika

Dane użytkownika są przechowywane w katalogu usługi Azure AD B2C i w dziennikach inspekcji. Wszystkie dane inspekcji użytkownika są przechowywane przez 7 dni w usłudze Azure AD B2C. Jeśli chcesz usunąć dane użytkownika w tym 7-dniowym okresie, możesz użyć operacji [Usuń użytkownika.](https://docs.microsoft.com/graph/api/user-delete) Operacja DELETE jest wymagana dla każdej dzierżawy usługi Azure AD B2C, w której mogą znajdować się dane.

Każdemu użytkownikowi usługi Azure AD B2C jest przypisywany identyfikator obiektu. Identyfikator obiektu zapewnia jednoznaczny identyfikator do użycia do usuwania danych użytkownika w usłudze Azure AD B2C. W zależności od architektury identyfikator obiektu może być użytecznym identyfikatorem korelacji w innych usługach, takich jak bazy danych zarządzania relacjami finansowymi, marketingu i relacji z klientami.

Najdokładniejszym sposobem uzyskania identyfikatora obiektu dla użytkownika jest uzyskanie go w ramach podróży uwierzytelniania za pomocą usługi Azure AD B2C. Jeśli otrzymasz prawidłowe żądanie danych od użytkownika przy użyciu innych metod, proces w trybie offline, takich jak wyszukiwanie przez agenta obsługi klienta, może być konieczne, aby znaleźć użytkownika i zanotować skojarzony identyfikator obiektu.

W poniższym przykładzie pokazano możliwy przepływ usuwania danych:

1. Użytkownik loguje się i wybiera **pozycję Usuń moje dane**.
2. Aplikacja oferuje opcję usunięcia danych w sekcji administracyjnej aplikacji.
3. Aplikacja wymusza uwierzytelnianie do usługi Azure AD B2C. Usługa Azure AD B2C udostępnia token z identyfikatorem obiektu użytkownika z powrotem do aplikacji.
4. Token jest odbierany przez aplikację, a identyfikator obiektu jest używany do usuwania danych użytkownika za pośrednictwem wywołania interfejsu API programu Microsoft Graph. Interfejs API programu Microsoft Graph usuwa dane użytkownika i zwraca kod stanu 200 OK.
5. Aplikacja organizuje usuwanie danych użytkownika w innych systemach organizacyjnych w razie potrzeby przy użyciu identyfikatora obiektu lub innych identyfikatorów.
6. Aplikacja potwierdza usunięcie danych i zapewnia kolejne kroki dla użytkownika.

## <a name="export-customer-data"></a>Eksportowanie danych klientów

Proces eksportowania danych klientów z usługi Azure AD B2C jest podobny do procesu usuwania.

Dane użytkownika usługi Azure AD B2C są ograniczone do:

- **Dane przechowywane w usłudze Azure Active Directory:** Można pobrać dane w podróży użytkownika uwierzytelniania usługi Azure AD B2C przy użyciu identyfikatora obiektu lub dowolnej nazwy logowania, takiej jak adres e-mail lub nazwa użytkownika.
- **Raport zdarzeń inspekcji specyficznych dla użytkownika:** Dane można indeksować przy użyciu identyfikatora obiektu.

W poniższym przykładzie przepływu danych eksportu kroki opisane jako wykonywane przez aplikację mogą być również wykonywane przez proces wewnętrznej bazy danych lub przez użytkownika z rolą administratora w katalogu:

1. Użytkownik loguje się do aplikacji. Usługa Azure AD B2C wymusza uwierzytelnianie za pomocą uwierzytelniania wieloskładnikowego platformy Azure, jeśli to konieczne.
2. Aplikacja używa poświadczeń użytkownika do wywołania operacji interfejsu API programu Microsoft Graph w celu pobrania atrybutów użytkownika. Interfejs API programu Microsoft Graph udostępnia dane atrybutów w formacie JSON. W zależności od schematu można ustawić zawartość tokenu identyfikatora, aby uwzględnić wszystkie dane osobowe użytkownika.
3. Aplikacja pobiera działanie inspekcji użytkownika. Interfejs API programu Microsoft Graph udostępnia dane zdarzeń do aplikacji.
4. Aplikacja agreguje dane i udostępnia go użytkownikowi.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zarządzać dostępem użytkowników do aplikacji, zobacz [Zarządzanie dostępem użytkowników](manage-user-access.md).
