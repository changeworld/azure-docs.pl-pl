---
title: Zarządzanie danymi użytkownika w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak usunąć lub eksportowanie danych użytkownika w usłudze Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: eb99cd94dca2a463c275dd5d4fab540b4fc55318
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66510441"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Zarządzanie danymi użytkownika w usłudze Azure Active Directory B2C

 W tym artykule opisano, jak możesz zarządzać danymi użytkownika w usłudze Azure Active Directory (Azure AD) B2C przy użyciu operacji, które są dostarczane przez [interfejsu API usługi Azure Active Directory Graph](/previous-versions/azure/ad/graph/api/api-catalog). Zarządzanie danymi użytkowników obejmuje usunięcie lub eksportowanie danych z dzienników inspekcji.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Usuń dane użytkownika

Dane użytkownika są przechowywane w katalogu usługi Azure AD B2C i dzienników inspekcji. Wszystkie dane inspekcji użytkownika są przechowywane przez 30 dni w usłudze Azure AD B2C. Jeśli chcesz usunąć dane użytkownika w ciągu tego 30-dniowego okresu, możesz użyć [usunąć użytkownika](/previous-versions/azure/ad/graph/api/users-operations#DeleteUser) operacji. Operacja usuwania jest wymagana dla wszystkich dzierżaw usługi Azure AD B2C lokalizację danych. 

Każdy użytkownik w usłudze Azure AD B2C jest przypisany identyfikator obiektu. Identyfikator obiektu oferuje jednoznaczną identyfikator można ich używać do usuwania danych użytkownika w usłudze Azure AD B2C. W zależności od architektury, identyfikator obiektu może być identyfikator korelacji przydatne dla innych usług, takich jak finansowych, marketingu i bazy danych zarządzania relacji klientów. 

Najdokładniejszych sposób, aby uzyskać identyfikator obiektu użytkownika jest uzyskać go jako część podróż uwierzytelniania w usłudze Azure AD B2C. Jeśli zostanie wyświetlony prawidłowy żądanie dotyczące danych przez użytkownika przy użyciu innych metod, proces w trybie offline, takich jak wyszukiwanie przez dział obsługi klienta agenta, może być konieczne Znajdź użytkownika, a następnie zanotuj identyfikator skojarzonego obiektu. 

Poniższy przykład przedstawia przepływ możliwości usunięcia danych:

1. Użytkownik loguje się i wybiera **Usuń moje dane**.
2. Aplikacja udostępnia opcję usuwania danych w administracyjnej części aplikacji.
3. Aplikacja wymusza uwierzytelniania usługi Azure AD B2C. Usługa Azure AD B2C zapewnia token identyfikator obiektu użytkownika do aplikacji. 
4. Token jest odbierany przez aplikację, a obiekt, który identyfikator służy do usuwania danych użytkownika za pomocą wywołania interfejsu API programu Graph usługi Azure AD. Interfejs API programu Graph usługi Azure AD usuwa danych użytkownika i zwraca kod stanu 200 OK.
5. Aplikacja organizuje usuwania danych użytkownika w innych systemach organizacji, zgodnie z potrzebami przy użyciu Identyfikatora obiektu lub innych identyfikatorów.
6. Aplikacja potwierdzi usunięcie danych i zapewnia kolejne kroki do użytkownika.

## <a name="export-customer-data"></a>Eksportowanie danych klienta

Proces eksportowania danych klientów z usługi Azure AD B2C jest podobny do procesu usuwania.

Dane użytkownika w usłudze Azure AD B2C jest ograniczone do:

- **Dane przechowywane w usłudze Azure Active Directory**: Można pobierać dane w podróży użytkownika uwierzytelniania usługi Azure AD B2C, przy użyciu Identyfikatora obiektu lub dowolnej nazwy logowania, takie jak adres e-mail lub nazwy użytkownika. 
- **Raport dotyczący zdarzeń inspekcji specyficzne dla użytkownika**: Umożliwia indeksowanie danych przy użyciu identyfikatora obiektu.

W poniższym przykładzie przepływ danych eksportu kroki, które są opisane jako wykonywanych przez aplikację można również przeprowadzić przez proces wewnętrznej bazy danych lub użytkownik mający rolę administratora w katalogu:

1. Użytkownik loguje się do aplikacji. Usługa Azure AD B2C wymusza uwierzytelniania za pomocą usługi Azure Multi-Factor Authentication, jeśli to konieczne.
2. Aplikacja używa poświadczeń użytkownika do wywołania operacji interfejsu API programu Graph usługi Azure AD, można pobrać atrybutów użytkownika. Interfejs API programu Graph usługi Azure AD udostępnia dane atrybutu w formacie JSON. W zależności od schematu można ustawić identyfikator zawartości tokenu do uwzględnienia wszystkich danych osobowych o użytkowniku.
3. Aplikacja pobiera działania inspekcji użytkownika. Interfejs API programu Graph usługi Azure AD zapewnia dane zdarzeń, do aplikacji.
4. Aplikacja agreguje dane i udostępnia go do użytkownika.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak zarządzać jak użytkownikom dostęp do Twoich aplikacji, zobacz [zarządzanie dostępem użytkowników](manage-user-access.md).




