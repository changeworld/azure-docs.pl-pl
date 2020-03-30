---
title: Tworzenie użytkowników — hiperskala (Citus) — usługa Azure Database for PostgreSQL
description: W tym artykule opisano, jak utworzyć nowe konta użytkowników do interakcji z usługą Azure Database for PostgreSQL — Hiperskala (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 1/8/2019
ms.openlocfilehash: 674fd4372bdf7c3782d18aaf04b48eb0067a9b2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484931"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Tworzenie użytkowników w usłudze Azure Database for PostgreSQL — hiperskala (Citus)

> [!NOTE]
> Termin "użytkownicy" odnosi się do użytkowników w grupie serwerów o hiperskali (Citus). Aby dowiedzieć się więcej o użytkownikach subskrypcji platformy Azure i ich uprawnieniach, odwiedź [artykuł o kontroli dostępu (RBAC) opartej na rolach platformy Azure](../role-based-access-control/built-in-roles.md) lub [sprawdź, jak dostosować role.](../role-based-access-control/custom-roles.md)

## <a name="the-server-admin-account"></a>Konto administratora serwera

Aparat PostgreSQL używa [ról](https://www.postgresql.org/docs/current/sql-createrole.html) do kontrolowania dostępu do obiektów bazy danych, a nowo utworzona grupa serwerów Hiperskali (Citus) ma kilka wstępnie zdefiniowanych ról:

* [Domyślne role PostgreSQL](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

Ponieważ hiperskala jest zarządzaną usługą PaaS, `postgres` tylko firma Microsoft może zalogować się za pomocą roli superużytnia. W przypadku ograniczonego dostępu `citus` administracyjnego funkcja Hyperscale zapewnia tę rolę.

Uprawnienia dla `citus` roli:

* Odczyt wszystkich zmiennych konfiguracyjnych, nawet zmiennych normalnie widocznych tylko dla superużytników.
* Przeczytaj wszystkie\_\_ \* widoki statystyk pg i użyj różnych rozszerzeń związanych ze statystykami - nawet widoków lub rozszerzeń normalnie widocznych tylko dla superużytników.
* Wykonywanie funkcji monitorowania, które mogą przyjmować blokady ACCESS SHARE w tabelach, potencjalnie przez długi czas.
* [Utwórz rozszerzenia PostgreSQL](concepts-hyperscale-extensions.md) (ponieważ rola `azure_pg_admin`jest członkiem ).

W szczególności `citus` rola ma pewne ograniczenia:

* Nie można utworzyć ról
* Nie można utworzyć baz danych

## <a name="how-to-create-additional-user-roles"></a>Jak utworzyć dodatkowe role użytkowników

Jak wspomniano, `citus` konto administratora nie ma uprawnień do tworzenia dodatkowych użytkowników. Aby dodać użytkownika, użyj interfejsu portalu Azure.

1. Przejdź do strony **Role** dla grupy serwerów hiperskali i kliknij **przycisk + Dodaj**:

   ![Strona Role](media/howto-hyperscale-create-users/1-role-page.png)

2. Wprowadź nazwę roli i hasło. Kliknij przycisk **Zapisz**.

   ![Dodaj rolę](media/howto-hyperscale-create-users/2-add-user-fields.png)

Użytkownik zostanie utworzony w węźle koordynatora grupy serwerów i propagowany do wszystkich węzłów procesu roboczego. Role utworzone za pośrednictwem `LOGIN` witryny Azure portal mają atrybut, co oznacza, że są prawdziwymi użytkownikami, którzy mogą logować się do bazy danych.

## <a name="how-to-modify-privileges-for-user-role"></a>Jak zmodyfikować uprawnienia dla roli użytkownika

Nowe role użytkowników są często używane do zapewnienia dostępu do bazy danych z ograniczonymi uprawnieniami. Aby zmodyfikować uprawnienia użytkownika, należy użyć standardowych poleceń PostgreSQL, używając narzędzia, takiego jak PgAdmin lub psql. (Zobacz [łączenie się z psql](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) w przewodniku Szybki start (Citus) w hiperskali).

Na przykład, `db_user` aby `mytable`zezwolić na odczyt , udzielić uprawnień:

```sql
GRANT SELECT ON mytable TO db_user;
```

Hiperskala (Citus) propaguje jednosko table instrukcje GRANT za pośrednictwem całego klastra, stosując je do wszystkich węzłów procesu roboczego. Jednak grants, które są w całym systemie (na przykład dla wszystkich tabel w schemacie) muszą być uruchamiane w każdym węźle daty.  Użyj `run_command_on_workers()` funkcji pomocnika:

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>Jak usunąć rolę użytkownika lub zmienić hasło

Aby zaktualizować użytkownika, odwiedź stronę **Role** grupy serwerów hiperskali i kliknij wielokropek **...** obok użytkownika. Wielokropek otworzy menu, aby usunąć użytkownika lub zresetować hasło.

   ![Edytowanie roli](media/howto-hyperscale-create-users/edit-role.png)

Rola `citus` jest uprzywilejowana i nie można jej usunąć.

## <a name="next-steps"></a>Następne kroki

Otwórz zaporę dla adresów IP maszyn nowych użytkowników, aby umożliwić im łączenie się: [Tworzenie reguł zapory w hiperskali (Citus) i zarządzanie nimi przy użyciu witryny Azure portal](howto-hyperscale-manage-firewall-using-portal.md).

Aby uzyskać więcej informacji na temat zarządzania kontem użytkowników bazy danych, zobacz Dokumentacja produktu PostgreSQL:

* [Role i uprawnienia bazy danych](https://www.postgresql.org/docs/current/static/user-manag.html)
* [Składnia GRANT](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Uprawnienia](https://www.postgresql.org/docs/current/static/ddl-priv.html)
