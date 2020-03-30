---
title: Tworzenie & usuwanie kont użytkowników usług Azure AD B2C w witrynie Azure portal
description: Dowiedz się, jak używać witryny Azure Portal do tworzenia i usuwania użytkowników konsumenckich w katalogu usługi Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c8fad8b5bd2dccba36c0c6888712fa806f3cbfc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187223"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>Tworzenie i usuwanie użytkowników w usłudze Azure AD B2C za pomocą witryny Azure Portal

Mogą istnieć scenariusze, w których chcesz ręcznie utworzyć konta konsumentów w katalogu B2C usługi Azure Active Directory (Azure AD B2C). Mimo że konta konsumentów w katalogu usługi Azure AD B2C są najczęściej tworzone, gdy użytkownicy zarejestrują się, aby użyć jednej z aplikacji, można je utworzyć programowo i za pomocą witryny Azure portal. W tym artykule koncentruje się na metodzie azure portal tworzenia i usuwania użytkownika.

Aby dodać lub usunąć użytkowników, do konta należy przypisać *rolę Administratora użytkownika* lub *administratora globalnego.*

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="types-of-user-accounts"></a>Typy kont użytkowników

Zgodnie z [opisem w przeglądzie kont użytkowników w usłudze Azure AD B2C](user-overview.md)istnieją trzy typy kont użytkowników, które można utworzyć w katalogu usługi Azure AD B2C:

* Praca
* Gość
* Konsumentów

Ten artykuł koncentruje się na pracy z **kontami konsumentów** w witrynie Azure portal. Aby uzyskać informacje dotyczące tworzenia i usuwania kont pracy i gościa, zobacz [Dodawanie lub usuwanie użytkowników przy użyciu usługi Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="create-a-consumer-user"></a>Tworzenie użytkownika konsumenckiego

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz filtr **subskrypcja katalog +** w górnym menu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Możesz też wybrać **wszystkie usługi** i wyszukać i wybrać pozycję Azure **AD B2C**.
1. W obszarze **Zarządzaj** wybierz pozycję **Użytkownicy**.
1. Wybierz przycisk **Nowy użytkownik**.
1. Wybierz **pozycję Utwórz użytkownika usługi Azure AD B2C**.
1. Wybierz **metodę logowania** i wprowadź adres **e-mail** lub **nazwę użytkownika** dla nowego użytkownika. Wybrana w tym miejscu metoda logowania musi być zgodna z ustawieniem określonym dla lokalnego dostawcy tożsamości *konta* dzierżawy usługi Azure AD B2C (zobacz **Zarządzanie dostawcami** > **tożsamości** w dzierżawie usługi Azure AD B2C).
1. Wprowadź **nazwę** użytkownika. Zazwyczaj jest to imię i nazwisko użytkownika.
1. (Opcjonalnie) Możesz **zablokować logowanie,** jeśli chcesz opóźnić możliwość logowania się przez użytkownika. Możesz włączyć logowanie później, edytując **profil** użytkownika w witrynie Azure portal.
1. Wybierz **opcję Automatyczne generowanie hasła** lub pozwól mi utworzyć **hasło**.
1. Określ **imię** i **nazwisko**użytkownika .
1. Wybierz **pozycję Utwórz**.

Jeśli nie wybrano opcji **Zablokuj zaloguj się,** użytkownik może teraz zalogować się przy użyciu określonej metody logowania (wiadomości e-mail lub nazwy użytkownika).

## <a name="delete-a-consumer-user"></a>Usuwanie użytkownika konsumenta

1. W katalogu usługi Azure AD B2C wybierz pozycję **Użytkownicy**, a następnie wybierz użytkownika, którego chcesz usunąć.
1. Wybierz **pozycję Usuń**, a następnie pozycję **Tak,** aby potwierdzić usunięcie.

Aby uzyskać szczegółowe informacje na temat przywracania użytkownika w ciągu pierwszych 30 dni po usunięciu lub trwałego usuwania użytkownika, zobacz [Przywracanie lub usuwanie ostatnio usuniętego użytkownika za pomocą usługi Azure Active Directory](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="next-steps"></a>Następne kroki

W przypadku scenariuszy automatycznego zarządzania użytkownikami, na przykład migracji użytkowników z innego dostawcy tożsamości do katalogu usługi Azure AD B2C, zobacz [Azure AD B2C: Migracja użytkowników](user-migration.md).
