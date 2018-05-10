---
title: Zarządzanie danymi użytkownika w usłudze Azure AD B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak usunąć lub wyeksportować dane użytkownika w usłudze Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 05/06/2018
ms.author: davidmu
ms.openlocfilehash: 414221c3e4942801b5792835d520ec936c8c4bbb
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="manage-user-data-in-azure-ad-b2c"></a>Zarządzanie danymi użytkownika w usłudze Azure AD B2C

 Ten artykuł zawiera informacje dotyczące sposobu zarządzania danych użytkownika w usłudze Azure Active Directory (AD) B2C przy użyciu operacji dostarczonych przez [interfejsu API usługi Azure Active Directory Graph](https://msdn.microsoft.com/en-us/library/azure/ad/graph/api/api-catalog). Zarządzanie danymi użytkownika obejmuje możliwość usunięcia danych lub wyeksportować dane z dzienników inspekcji.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Usuń dane użytkownika

Dane użytkownika są przechowywane w katalogu usługi Azure AD B2C i w dziennikach inspekcji. Wszystkie dane inspekcji użytkownika jest przechowywane przez 30 dni przechowywania danych w usłudze Azure AD B2C. Jeśli chcesz usunąć dane użytkownika w tym ciągu 30 dni, możesz użyć [usunąć użytkownika](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#DeleteUser) operacji. Operacja usuwania jest wymagana dla każdego dzierżawcy usługi Azure AD B2C których dane mogą znajdować się. 

Każdy użytkownik w usłudze Azure AD B2C jest przypisany identyfikator obiektu. Identyfikator obiektu zawiera jednoznaczne identyfikatora można użyć do usuwania danych użytkownika w usłudze Azure AD B2C.  W zależności od architektury, identyfikator obiektu może być identyfikator korelacji przydatne przez innych usług, takich jak finansowych, marketing i baz danych zarządzania relacji klienta.  

Najbardziej dokładna sposobem uzyskania Identyfikatora obiektu dla użytkownika jest uzyskanie go jako część przebieg uwierzytelniania w usłudze Azure AD B2C.  Odebranie prawidłowe żądanie dotyczące danych od użytkownika za pomocą innych metod, proces w trybie offline, takie jak wyszukiwanie przez dział obsługi klienta agenta, może być konieczne Znajdź użytkownika i Zanotuj identyfikator skojarzonego obiektu. 

W poniższym przykładzie przedstawiono przepływ usuwania danych:

1. Użytkownik loguje się i wybiera **usunąć dane**.
2. Oferuje możliwość usuwania danych w sekcji Administracja aplikacji.
3. Aplikacja wymusza uwierzytelniania usługi Azure AD B2C. Usługa Azure AD B2C zapewnia token o identyfikatorze obiektu użytkownika do aplikacji. 
4. Token jest odbierany przez aplikację i obiektu, do którego identyfikator służy do usuwania danych użytkownika za pośrednictwem wywołania interfejsu API usługi Azure AD Graph. Azure AD Graph API powoduje usunięcie danych użytkownika i zwraca kod stanu 200 OK.
5. Aplikacja organizuje usunięcie danych użytkownika w innych systemach organizacji, zgodnie z potrzebami, przy użyciu Identyfikatora obiektu lub innych identyfikatorów.
6. Aplikacja potwierdza usunięcie danych i dalej instrukcje dla użytkownika.

## <a name="export-customer-data"></a>Eksportuj dane klienta

Eksportowanie danych klienta z usługi Azure AD B2C proces jest podobny do procesu usuwania.

Dane użytkownika w usłudze Azure AD B2C jest ograniczona do:

- **Dane przechowywane w usłudze Azure Active Directory** — może pobrać danych w podróży użytkownika uwierzytelniania usługi Azure AD B2C przy użyciu Identyfikatora obiektu lub dowolną nazwę logowania, takie jak wiadomości e-mail lub nazwy użytkownika.  
- **Raport dotyczący zdarzenia inspekcji specyficzne dla użytkownika** -danych jest indeksowana przy użyciu identyfikatora obiektu.

W poniższym przykładzie przepływ danych eksportu opisane jako wykonywane przez aplikację można również przeprowadzić przez proces zaplecza lub przez użytkownika z rolą administratora w katalogu:

1. Użytkownik loguje się do aplikacji. Azure AD B2C wymusza uwierzytelnianie wieloskładnikowe autoryzacji, jeśli to konieczne.
2. Aplikacja używa poświadczeń użytkownika do wywołania operacji usługi Azure AD Graph API można pobrać atrybutów użytkownika. Interfejs API Azure AD Graph udostępnia dane atrybutu w formacie JSON. W zależności od schematu można ustawić identyfikator zawartości tokenu obejmują wszystkie dane osobowe o użytkowniku.
3. Aplikacja pobiera działania inspekcji przez użytkownika końcowego. Azure AD Graph API zapewnia dane zdarzeń do aplikacji.
4. Aplikacja agreguje dane i udostępnia użytkownikowi.

## <a name="next-steps"></a>Kolejne kroki

- Informacje o sposobie zarządzania, jak użytkownicy mogą uzyskiwać dostęp do aplikacji w [zarządzanie dostępem użytkowników](manage-user-access.md)




