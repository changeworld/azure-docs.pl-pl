---
title: Podejścia do migracji użytkowników
titleSuffix: Azure AD B2C
description: Migrowanie kont użytkowników z innego dostawcy tożsamości do usługi Azure AD B2C przy użyciu metod importu zbiorczego lub bezproblemowej migracji.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b3ee069985fd39288a562d3caafc50b12290c060
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332330"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>Migrowanie użytkowników do usługi Azure AD B2C

Migracja z innego dostawcy tożsamości do usługi Azure Active Directory B2C (Azure AD B2C) może również wymagać migracji istniejących kont użytkowników. W tym miejscu omówione są dwie metody migracji, *import zbiorczy* i *bezproblemowa migracja.* Z obu podejść, musisz napisać aplikację lub skrypt, który używa [interfejsu API programu Microsoft Graph](manage-user-accounts-graph-api.md) do tworzenia kont użytkowników w usłudze Azure AD B2C.

## <a name="bulk-import"></a>Import luzem

W przepływie importu zbiorczego aplikacja migracji wykonuje następujące kroki dla każdego konta użytkownika:

1. Przeczytaj konto użytkownika ze starego dostawcy tożsamości, w tym jego bieżące poświadczenia (nazwa użytkownika i hasło).
1. Utwórz odpowiednie konto w katalogu usługi Azure AD B2C z bieżącymi poświadczeniami.

Użyj przepływu importu zbiorczego w jednej z tych dwóch sytuacji:

- Masz dostęp do poświadczeń zwykłego tekstu użytkownika (jego nazwy użytkownika i hasła).
- Poświadczenia są szyfrowane, ale można je odszyfrować.

Aby uzyskać informacje dotyczące programowego tworzenia kont użytkowników, zobacz [Zarządzanie kontami użytkowników usługi Azure AD B2C za pomocą programu Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="seamless-migration"></a>Bezproblemowa migracja

Użyj płynnego przepływu migracji, jeśli hasła w postaci zwykłego tekstu w starym dostawcy tożsamości nie są dostępne. Na przykład, gdy:

- Hasło jest przechowywane w jednokierunkowym formacie zaszyfrowanym, na przykład z funkcją mieszania.
- Hasło jest przechowywane przez starszego dostawcę tożsamości w sposób, do którego nie można uzyskać dostępu. Na przykład, gdy dostawca tożsamości sprawdza poprawność poświadczeń, wywołując usługę sieci web.

Płynny przepływ migracji nadal wymaga zbiorczej migracji kont użytkowników, ale następnie używa [zasad niestandardowych](custom-policy-get-started.md) do wykonywania zapytań o [interfejs API REST](custom-policy-rest-api-intro.md) (który tworzysz), aby ustawić hasło każdego użytkownika przy pierwszym logowaniu.

Płynny przepływ migracji ma zatem dwie fazy: *import zbiorczy* i *ustawianie poświadczeń.*

### <a name="phase-1-bulk-import"></a>Faza 1: Import luzem

1. Aplikacja migracji odczytuje konta użytkowników ze starego dostawcy tożsamości.
1. Aplikacja do migracji tworzy odpowiednie konta użytkowników w katalogu usługi Azure AD B2C, ale *nie ustawia haseł*.

### <a name="phase-2-set-credentials"></a>Faza 2: Ustawianie poświadczeń

Po zakończeniu zbiorczej migracji kont, zasady niestandardowe i interfejs API REST następnie wykonać następujące czynności, gdy użytkownik loguje się:

1. Przeczytaj konto użytkownika usługi Azure AD B2C odpowiadające wprowadzonemu adresowi e-mail.
1. Sprawdź, czy konto jest oflagowane do migracji, oceniając atrybut rozszerzenia logicznego.
    - Jeśli atrybut rozszerzenia `True`zwraca , wywołać interfejs API REST, aby sprawdzić poprawność hasła względem starszego dostawcy tożsamości.
      - Jeśli interfejs API REST stwierdzi, że hasło jest nieprawidłowe, zwróć przyjazny błąd do użytkownika.
      - Jeśli interfejs API REST określa, że hasło jest poprawne, zapisz hasło na koncie usługi `False`Azure AD B2C i zmień atrybut rozszerzenia logicznego na .
    - Jeśli atrybut rozszerzenia logicznego `False`zwraca się, kontynuuj proces logowania w normalny sposób.

Aby wyświetlić przykładowe zasady niestandardowe i interfejs API REST, zobacz [przykład bezproblemowej migracji użytkownika](https://aka.ms/b2c-account-seamless-migration) w usłudze GitHub.

![Schemat blokowy diagramu podejścia do migracji bez szwu do migracji użytkowników](./media/user-migration/diagram-01-seamless-migration.png)<br />*Diagram: Płynny przepływ migracji*

## <a name="best-practices"></a>Najlepsze rozwiązania

### <a name="security"></a>Zabezpieczenia

Podejście migracji bezproblemowej używa własnego niestandardowego interfejsu API REST do sprawdzania poprawności poświadczeń użytkownika względem starszego dostawcy tożsamości.

**Musisz chronić interfejs API REST przed atakami siłowymi.** Osoba atakująca może przesłać kilka haseł w nadziei, że ostatecznie zgadnie poświadczenia użytkownika. Aby pomóc w pokonaniu takich ataków, należy zatrzymać wyświetlanie żądań do interfejsu API REST, gdy liczba prób logowania przekroczy określony próg. Ponadto zabezpiecz komunikację między usługą Azure AD B2C a interfejsem API REST. Aby dowiedzieć się, jak zabezpieczyć interfejsy API restful do produkcji, zobacz [Bezpieczny interfejs API restful](secure-rest-api.md).

### <a name="user-attributes"></a>Atrybuty użytkownika

Nie wszystkie informacje w starszej dostawcy tożsamości powinny zostać zmigrowane do katalogu usługi Azure AD B2C. Zidentyfikuj odpowiedni zestaw atrybutów użytkownika do przechowywania w usłudze Azure AD B2C przed migracją.

- **Sklep DO** w usłudze Azure AD B2C
  - Nazwa użytkownika, hasło, adresy e-mail, numery telefonów, numery/identyfikatory członków.
  - Znaczniki zgody dla polityki prywatności i umów licencyjnych użytkownika końcowego.
- **NIE PRZECHOWUJ** W usłudze Azure AD B2C
  - Poufne dane, takie jak numery kart kredytowych, numery ubezpieczenia społecznego (SSN), dokumentacja medyczna lub inne dane regulowane przez organy rządowe lub branżowe.
  - Preferencje marketingowe lub komunikacyjne, zachowania użytkowników i szczegółowe informacje.

### <a name="directory-clean-up"></a>Oczyszczanie katalogu

Przed rozpoczęciem procesu migracji skorzystaj z okazji, aby wyczyścić katalog.

- Zidentyfikuj zestaw atrybutów użytkownika, które mają być przechowywane w usłudze Azure AD B2C, i migruj tylko to, czego potrzebujesz. W razie potrzeby można utworzyć [atrybuty niestandardowe,](custom-policy-custom-attributes.md) aby przechowywać więcej danych o użytkowniku.
- Jeśli przeprowadzasz migrację ze środowiska z wieloma źródłami uwierzytelniania (na przykład każda aplikacja ma własny katalog użytkowników), należy przeprowadzić migrację do ujednoliconego konta w usłudze Azure AD B2C.
- Jeśli wiele aplikacji ma różne nazwy użytkowników, można przechowywać wszystkie z nich na koncie użytkownika usługi Azure AD B2C przy użyciu kolekcji tożsamości. W odniesieniu do hasła, niech użytkownik wybierze jeden i ustawić go w katalogu. Na przykład w przypadku bezproblemowej migracji tylko wybrane hasło powinno być przechowywane na koncie usługi Azure AD B2C.
- Usuń nieużywane konta użytkowników przed migracją lub nie migruj starych kont.

### <a name="password-policy"></a>Zasady haseł

Jeśli kont, które przeprowadzasz migrację, mają słabszą siłę hasła niż [silna siła hasła](../active-directory/authentication/concept-sspr-policy.md) wymuszana przez usługę Azure AD B2C, można wyłączyć wymaganie dotyczące silnego hasła. Aby uzyskać więcej informacji, zobacz [Właściwość zasad haseł](manage-user-accounts-graph-api.md#password-policy-property).

## <a name="next-steps"></a>Następne kroki

Repozytorium [azure-ad-b2c/user-migration](https://github.com/azure-ad-b2c/user-migration) w usłudze GitHub zawiera przykład niestandardowej zasady migracji bezproblemowej i przykład kodu interfejsu API REST:

[Bezproblemowa migracja użytkowników & przykładowy kod interfejsu API REST](https://aka.ms/b2c-account-seamless-migration)
