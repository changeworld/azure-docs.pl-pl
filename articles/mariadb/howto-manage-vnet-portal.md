---
title: Tworzenie i zarządzanie nimi — Azure Database dla punktów końcowych usługi sieci wirtualnej MariaDB oraz reguł za pomocą witryny Azure portal | Dokumentacja firmy Microsoft
description: Tworzenie i zarządzanie nimi — Azure Database dla punktów końcowych usługi sieci wirtualnej MariaDB oraz reguł za pomocą witryny Azure portal
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: b94c797bc2351706badb98075514e5324d90a249
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2018
ms.locfileid: "52277300"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Tworzenie i zarządzanie nimi — Azure Database dla punktów końcowych usługi sieci wirtualnej MariaDB oraz reguł sieci wirtualnej przy użyciu witryny Azure portal

> [!IMPORTANT]
> Funkcja punktów końcowych usługi sieci wirtualnej jest w publicznej wersji zapoznawczej.

Punkty końcowe usługi sieci wirtualnej i reguł dotyczyć usługi Azure Database dla serwera MariaDB prywatnej przestrzeni adresowej sieci wirtualnej. Omówienie usługi Azure Database dla punktów końcowych usługi sieci wirtualnej MariaDB, łącznie z ograniczeniami, zobacz [— Azure Database for punkty końcowe usługi sieci wirtualnej serwera MariaDB](concepts-data-access-security-vnet.md). Punkty końcowe usługi sieci wirtualnej są dostępne we wszystkich obsługiwanych regionów dla usługi Azure Database dla serwera MariaDB.

> [!NOTE]
> Obsługa punktów końcowych usługi sieci wirtualnej jest tylko w przypadku serwerów ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci.

## <a name="create-a-vnet-rule-and-enable-service-endpoints"></a>Utwórz regułę sieci wirtualnej i włączyć punkty końcowe usługi

1. Na stronie serwera MariaDB, w obszarze Ustawienia kliknij pozycję **zabezpieczenia połączeń** aby otworzyć okienko zabezpieczenia połączeń dla usługi Azure Database dla serwera MariaDB. Przejdź do menu **+ Dodawanie istniejącej sieci wirtualnej**. Jeśli nie masz istniejącej sieci wirtualnej możesz kliknąć **+ Utwórz nową sieć wirtualną** ją utworzyć. Zobacz [Szybki Start: tworzenie sieci wirtualnej przy użyciu witryny Azure portal](../virtual-network/quick-create-portal.md)

   ![Portal Azure — zabezpieczenia połączeń kliknij](./media/howto-manage-vnet-portal/1-connection-security.png)

2. Wprowadź nazwę reguły sieci wirtualnej, wybierz subskrypcję, sieć wirtualna i Nazwa podsieci, a następnie kliknij przycisk **Włącz**. Dzięki temu punktów końcowych usługi sieci wirtualnej w podsieci przy użyciu automatycznie **Microsoft.SQL** tag usługi.

   ![Portal Azure — Konfigurowanie sieci wirtualnej](./media/howto-manage-vnet-portal/2-configure-vnet.png)

   Konto musi mieć uprawnienia niezbędne do tworzenia sieci wirtualnej i punktu końcowego usługi.

   Punkty końcowe usługi można konfigurować w sieciach wirtualnych niezależnie od siebie, przez użytkownika z dostępem do zapisu do sieci wirtualnej.
    
   Aby zabezpieczyć zasoby usługi platformy Azure z siecią wirtualną, użytkownik musi mieć uprawnienia do "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" dla dodawanych podsieci. To uprawnienie jest domyślnie uwzględniane we wbudowanych rolach administratora usługi, domyślnie i może być modyfikowane przez tworzenie ról niestandardowych.
    
   Dowiedz się więcej na temat [wbudowanych ról](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) i przypisywaniu określonych uprawnień do [ról niestandardowych](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).
    
   Sieci wirtualne i zasoby usług platformy Azure mogą należeć do tej samej lub różnych subskrypcji. Zasoby usługi sieci wirtualnej i na platformie Azure należą do różnych subskrypcji, zasoby, powinny znajdować się w tej samej dzierżawie usługi Active Directory (AD).

   > [!IMPORTANT]
   > Zdecydowanie zaleca się przeczytaj ten artykuł, konfiguracje punktu końcowego usługi i zagadnienia, przed rozpoczęciem konfigurowania punktów końcowych usługi. **Punkt końcowy usługi sieci wirtualnej:** A [punkt końcowy usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md) jest podsiecią, w których wartości właściwości zawierają jedną lub więcej nazw typu formalnego usługi platformy Azure. Punkty końcowe usługi sieci wirtualnej, użyj nazwy typu usługi **Microsoft.Sql**, która odnosi się do usługi platformy Azure o nazwie bazy danych SQL. Ten tag usługi dotyczy także usługi Azure SQL Database, Azure Database dla usługi MariaDB, PostgreSQL i MySQL. Ważne jest, aby pamiętać podczas stosowania **Microsoft.Sql** tag usługi punktu końcowego usługi sieci wirtualnej umożliwia skonfigurowanie ruch w ramach punktu końcowego usługi dla wszystkich usług bazy danych Azure, w tym usługi Azure SQL Database, Azure Database for PostgreSQL, Azure Database for MariaDB oraz usługi Azure Database for MySQL serwerów w podsieci.
   > 

3. Po włączeniu kliknij **OK** i zobaczysz, że punkty końcowe usługi sieci wirtualnej są włączone wraz z reguły sieci wirtualnej.

   ![Włączone punkty końcowe usługi sieci wirtualnej i regułę sieci wirtualnej, utworzyć](./media/howto-manage-vnet-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [Konfigurowanie protokołu SSL w usłudze Azure Database for MySQL](howto-configure-ssl.md)

<!--
- Similarly, you can script to [Enable VNet service endpoints and create a VNET rule for Azure Database for MySQL using Azure CLI](howto-manage-vnet-using-cli.md).
- For help in connecting to an Azure Database for MySQL server, see [Connection libraries for Azure Database for MySQL](./concepts-connection-libraries.md)
-->
