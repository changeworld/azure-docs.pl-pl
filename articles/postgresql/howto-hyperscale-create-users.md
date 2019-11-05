---
title: Tworzenie użytkowników w Azure Database for PostgreSQL-Citus
description: W tym artykule opisano, jak można tworzyć nowe konta użytkowników w celu współdziałania z Azure Database for PostgreSQL-Citus.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7187135b29f0a9a790c032330c73bcb1ae27229b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515943"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Tworzenie użytkowników w Azure Database for PostgreSQL-Citus

W tym artykule opisano, jak można tworzyć użytkowników w grupie serwerów Citus. Aby dowiedzieć się więcej o użytkownikach subskrypcji platformy Azure i ich uprawnieniach, odwiedź [artykuł kontroli dostępu opartej na rolach (RBAC) platformy Azure](../role-based-access-control/built-in-roles.md) lub zapoznaj [się z tematem Dostosowywanie ról](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>Konto administratora serwera

Nowo utworzona grupa serwerów ze skalą (Citus) zawiera kilka wstępnie zdefiniowanych ról:

* [Domyślne role PostgreSQL](https://www.postgresql.org/docs/current/default-roles.html)
* *azure_pg_admin*
* *postgres*
* *citus*

Administrator serwera *Citus*, jest członkiem roli *azure_pg_admin* .
Nie jest to jednak częścią roli *Postgres* (Administrator).  Ze względu na to, że jest to zarządzana usługa PaaS, tylko firma Microsoft jest częścią roli administratora.

Aparat PostgreSQL używa uprawnień do kontrolowania dostępu do obiektów bazy danych, jak to opisano w [dokumentacji produktu PostgreSQL](https://www.postgresql.org/docs/current/static/sql-createrole.html).
W Azure Database for PostgreSQL administrator serwera otrzymuje następujące uprawnienia: LOGIN, NOSUPERUSER, INHERIT, CREATEDB, DbRole, noreplication

## <a name="how-to-create-additional-users"></a>Jak utworzyć dodatkowych użytkowników

Konto administratora *Citus* nie ma uprawnień do tworzenia dodatkowych użytkowników. Aby dodać użytkownika, Użyj zamiast niego Azure Portal.

1. Przejdź do strony **role** dla swojej grupy serwerów w ramach skalowania i kliknij pozycję **+ Dodaj**:

   ![Strona role](media/howto-hyperscale-create-users/1-role-page.png)

2. Wprowadź nazwę i hasło roli. Kliknij pozycję **Zapisz**.

   ![Dodaj rolę](media/howto-hyperscale-create-users/2-add-user-fields.png)

Użytkownik zostanie utworzony w węźle koordynatora grupy serwerów i rozpropagowany do wszystkich węzłów procesu roboczego.

## <a name="how-to-delete-a-user-or-change-their-password"></a>Jak usunąć użytkownika lub zmienić jego hasło

Przejdź do strony **role** dla swojej grupy serwerów, a następnie kliknij przycisk wielokropka **...** obok użytkownika. Wielokropek spowoduje otwarcie menu w celu usunięcia użytkownika lub zresetowania hasła.

   ![Edytowanie roli](media/howto-hyperscale-create-users/edit-role.png)

Rola *Citus* jest uprzywilejowana i nie można jej usunąć.

## <a name="how-to-modify-privileges-for-role"></a>Jak zmodyfikować uprawnienia dla roli

Nowe role są często używane do zapewniania dostępu do bazy danych z ograniczonymi uprawnieniami. Aby zmodyfikować uprawnienia użytkownika, użyj standardowych poleceń PostgreSQL przy użyciu narzędzia, takiego jak PgAdmin lub PSQL. (Zobacz [nawiązywanie połączenia z usługą PSQL](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) na stronie Szybki Start skalowania (Citus).

Na przykład, aby zezwolić *db_user* na odczytywanie *MyTable*, Udziel uprawnienia:

```sql
GRANT SELECT ON mytable TO db_user;
```

Funkcja wieloskal (Citus) propaguje instrukcje GRANTu jednej tabeli w całym klastrze, stosując je we wszystkich węzłach procesu roboczego. Takie dotacje są jednak dostępne dla całego systemu (np. dla wszystkich tabel w schemacie), które muszą być uruchamiane w każdym węźle daty.  Użyj funkcji pomocnika *run_command_on_workers ()* :

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="next-steps"></a>Następne kroki

Otwórz Zaporę dla adresów IP nowych maszyn użytkowników, aby umożliwić im łączenie: [Utwórz reguły zapory Citus (i zarządzaj nimi) przy użyciu Azure Portal](howto-hyperscale-manage-firewall-using-portal.md).

Aby uzyskać więcej informacji na temat zarządzania kontami użytkowników bazy danych, zobacz dokumentację produktu PostgreSQL:

* [Role i uprawnienia bazy danych](https://www.postgresql.org/docs/current/static/user-manag.html)
* [Składnia GRANTu](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Uprawnienia](https://www.postgresql.org/docs/current/static/ddl-priv.html)
