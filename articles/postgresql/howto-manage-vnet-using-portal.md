---
title: Tworzenie i zarządzanie nimi — Azure Database dla punktów końcowych usługi postgresql w warstwie sieci wirtualnej i reguł za pomocą witryny Azure portal | Dokumentacja firmy Microsoft
description: Tworzenie i zarządzanie nimi — Azure Database dla punktów końcowych usługi postgresql w warstwie sieci wirtualnej i reguł za pomocą witryny Azure portal
services: postgresql
author: mbolz
ms.author: mbolz
ms.reviewer: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/15/2018
ms.openlocfilehash: 3884b594c903c234ce83c0752f92ddc34b24cd5a
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48239323"
---
# <a name="create-and-manage-azure-database-for-postgresql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Tworzenie i zarządzanie nimi — Azure Database dla punktów końcowych usługi postgresql w warstwie sieci wirtualnej i reguł sieci wirtualnej przy użyciu witryny Azure portal
Punkty końcowe usługi sieci wirtualnej i reguł rozszerzyć prywatnej przestrzeni adresowej sieci wirtualnej do usługi Azure Database for postgresql w warstwie serwera. Omówienie usługi Azure Database dla punktów końcowych usługi postgresql w warstwie sieci wirtualnej, w tym informacje o ograniczeniach, zobacz [— Azure Database for punkty końcowe usługi sieci wirtualnej serwera PostgreSQL](concepts-data-access-and-security-vnet.md). Punkty końcowe usługi sieci wirtualnej są dostępne we wszystkich obsługiwanych regionów dla usługi Azure Database for PostgreSQL.

> [!NOTE]
> Obsługa punktów końcowych usługi sieci wirtualnej jest tylko w przypadku serwerów ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci.

## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Utwórz regułę sieci wirtualnej i włączyć punkty końcowe usługi w witrynie Azure portal

1. Na stronie serwer postgresql — w obszarze Ustawienia kliknij pozycję **zabezpieczenia połączeń** aby otworzyć okienko zabezpieczenia połączeń dla usługi Azure Database for PostgreSQL. Przejdź do menu **+ Dodawanie istniejącej sieci wirtualnej**. Jeśli nie masz istniejącej sieci wirtualnej możesz kliknąć **+ Utwórz nową sieć wirtualną** ją utworzyć. Zobacz [Szybki Start: tworzenie sieci wirtualnej przy użyciu witryny Azure portal](../virtual-network/quick-create-portal.md)

   ![Portal Azure — zabezpieczenia połączeń kliknij](./media/howto-manage-vnet-using-portal/1-connection-security.png)

2. Wprowadź nazwę reguły sieci wirtualnej, wybierz subskrypcję, sieć wirtualna i Nazwa podsieci, a następnie kliknij przycisk **Włącz**. Dzięki temu punktów końcowych usługi sieci wirtualnej w podsieci przy użyciu automatycznie **Microsoft.SQL** tag usługi.

   ![Portal Azure — Konfigurowanie sieci wirtualnej](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

   > [!IMPORTANT]
   > Zdecydowanie zaleca się przeczytaj ten artykuł, konfiguracje punktu końcowego usługi i zagadnienia, przed rozpoczęciem konfigurowania punktów końcowych usługi. **Punkt końcowy usługi sieci wirtualnej:** A [punkt końcowy usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md) jest podsiecią, w których wartości właściwości zawierają jedną lub więcej nazw typu formalnego usługi platformy Azure. Punkty końcowe usługi sieci wirtualnej, użyj nazwy typu usługi **Microsoft.Sql**, która odnosi się do usługi platformy Azure o nazwie bazy danych SQL. Ten tag usługi dotyczy także usługi Azure SQL Database, Azure Database for PostgreSQL i MySQL. Ważne jest, aby pamiętać podczas stosowania **Microsoft.Sql** tag usługi punktu końcowego usługi sieci wirtualnej umożliwia skonfigurowanie ruch w ramach punktu końcowego usługi dla wszystkich usług bazy danych Azure, w tym usługi Azure SQL Database, Azure Database for PostgreSQL i Usługa Azure Database for MySQL serwerów w podsieci. 
   > 

3. Po włączeniu kliknij **OK** i zobaczysz, że punkty końcowe usługi sieci wirtualnej są włączone wraz z reguły sieci wirtualnej.

   ![Włączone punkty końcowe usługi sieci wirtualnej i regułę sieci wirtualnej, utworzyć](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Kolejne kroki
- Podobnie, można utworzyć skrypty do [Włącz sieć wirtualną punkty końcowe usługi i Utwórz regułę sieci Wirtualnej dla usługi Azure Database for PostgreSQL przy użyciu wiersza polecenia platformy Azure](howto-manage-vnet-using-cli.md).
- Aby uzyskać pomoc podczas nawiązywania połączenia z usługi Azure Database for postgresql w warstwie serwera, zobacz [biblioteki połączeń dla usługi Azure Database for PostgreSQL](./concepts-connection-libraries.md)
